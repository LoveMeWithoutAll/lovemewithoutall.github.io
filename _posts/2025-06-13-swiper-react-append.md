---
layout: single
title: Swiper.js + React 에서 slide prepend 문제를 해결하는 방법
date: 2025-06-13 00:49:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/11play.png"
    image: "/assets/images/11play.png"
categories:
- IT
tags: [react, swiper.js]
---

> **TL;DR**
> 
> 가상 슬라이드(Virtual Slides)를 켠 **Swiper.js + React** 프로젝트에서
> *prepend*로 아이템을 추가하면서도 **사용자가 보고 있던 슬라이드를 유지하는** 방법은
>
> 1. **Zustand**에 “현재 슬라이드 전역 인덱스”를 저장하고,
> 2. 리스트를 앞·뒤로 확장할 때 **같은 액션 안에서** 인덱스를 갱신한 뒤,
> 3. `useEffect` 로 **Swiper 인스턴스를 직접** `slideTo()` 하라

아래는 그 과정을 거치며 겪은 삽질, 제약, 최종 코드와 함께 정리했다.

---

## 1. 문제 정의

* **요구**

  * 인피니트 스크롤(세로) **Shorts** UI
  * 과거 영상은 *prepend*, 이후 영상은 *append*
  * 스크롤 위치(사용자가 보던 슬라이드) **불변**

* **기술 스택**

  * React 18
  * Swiper 11 (`swiper/react`) — **Virtual Slides** 필수
  * 상태 관리 : Zustand

* **난제**

  * 리스트의 뒤에 요소를 추가하는 경우(append), 사용자가 현재 보고 있는 Shorts(swiper slide)의 index가 변하지 않기 때문에 문제가 없다.
  * **리스트의 앞에 요소를 추가하는 경우(prepend), 사용자가 현재 보고 있는 Shorts(swiper slide)의 index가 변한다!**

> [Swiper 공식문서](https://swiperjs.com/swiper-api#virtual-slides-methods): *“React에서 Virtual Slides를 쓰는 경우
> `swiper.virtual.prependSlide()`/`appendSlide()` 같은 Imperative API 를 직접 호출하면 안 된다.”*
> 이는 **선언형(Declarative)** 렌더링을 유지해야 최적화 & 동기화 문제가 없기 때문이다.

---

## 2. 시도했던 (그리고 실패한) 방법들

| 방법                                           | 한 줄 후기                         | 문제가 된 포인트                      |
| -------------------------------------------- | ------------------------------ | ------------------------------ |
| `key`를 바꿔 Swiper 리마운트                        | 화면은 맞지만 “전체 리렌더”               | 상태·이벤트 초기화, **FPS 저하**         |
| `virtualIndex`에 전역 오프셋                       | 논리적으로 맞아 보이지만 **Swiper 캐시 꼬임** | 중복 virtualIndex + loop 사용 시 예외 |
| `swiper.virtual.update()` 후 `slideTo(+diff)` | Imperative 쪽으로 많이 기운다          | 그럼에도 **렌더 타이밍 race** 발생        |

결국 *가장 작은 제어면만 직접 건드리되, React 선언형 흐름을 최대한 보존*하는 하이브리드 접근이 필요했다.

---

## 3. 최종 아키텍처

```
┌──────────────────────┐      set()/subscribe
│   Zustand Store      │◄────────────────────────┐
│  ├─ list             │                         │
│  └─ swiperActiveIdx  │                         │
└──────────────────────┘                         │
         ▲ prepend/append (동시에 인덱스 갱신)        │
         │                                        │
         │                useEffect               │
┌────────┴─────────────┐   (watch swiperActiveIdx)│
│   React Component    │─────────────────────┐    │
│ (ShortsSwiper & Hook)│                     │    │
└──────────────────────┘            slideTo()│    │
                                 ┌───────────▼────┴┐
                                 │ Swiper Instance │
                                 └─────────────────┘
```

### 3-1. 핵심 관점

| 요소                | 선언형 영역                             | 명령형(Imperative) 영역     |
| ----------------- | ---------------------------------- | ---------------------- |
| **슬라이드 데이터**      | `list` (React re-render)       | —                      |
| **현재 슬라이드 논리 위치** | `swiperActiveIdx` (Zustand → 컴포넌트) | —                      |
| **DOM 실제 위치 보정**  | —                                  | `swiper.slideTo()` 한 줄 |

> **“상태는 React가, 위치 보정은 Swiper가.”**
> 렌더링 결정권과 뷰어 위치 제어를 명확히 분리했다.

---

## 4. 코드

### 4-1. Zustand Store

```ts
// listStore.ts
type listState = {
  list: Shorts[];
  swiperActiveIndex: number;   // 전역 인덱스
};
type listActions = {  
  appendlist(more: Play[]): void;     // append
  prependlist(prev: Play[]): void;    // prepend
  setSwiperActiveIndex(i: number): void;
};

const store: StateCreator<listState & listActions> = (set) => ({
  list: [],
  swiperActiveIndex: 0,

  addlist: (p) =>
    set((s) => ({
      list: [...s.list, ...p],
      swiperActiveIndex: s.swiperActiveIndex,     // 뒤에 붙여도 현재 index 유지
    })),
  prependlist: (p) =>
    set((s) => ({
      list: [...p, ...s.list],
      swiperActiveIndex: s.swiperActiveIndex + p.length, // 보던 슬라이드가 밀린 만큼 +
    })),
  setSwiperActiveIndex: (i) => set({ swiperActiveIndex: i }),
});
```

### 4-2. 위치 보정 훅

```ts
// useSwiperIndexForPrepending.ts
const useSwiperIndexForPrepending = (
  swiperRef: MutableRefObject<Swiper | null>,
) => {
  const swiperActiveIndex = uselistStore((s) => s.swiperActiveIndex);

  useEffect(() => {
    const swiper = swiperRef.current;
    if (!swiper) return;

    // Swiper 가 실제 DOM 위치를 갱신하도록 요청
    swiper.slideTo(swiperActiveIndex, 0, false);  // 현재 슬라이드로 즉시 워프
  }, [swiperActiveIndex, swiperRef]);
};
```

#### swiper.slideTo 사용시 주의 사항
1. swiper.slideTo 메소드의 2번째 파라미터는 0으로 설정해야 slide가 즉시 변경된다.
2. swiper.slideTo 메소드의 3번째 파라미터인 `runCallbacks를 false로 설정`해야 한다. 이 코드는 오로지 사용자에게 보여지는 슬라이드를 유지시키는 것만이 목적이다. 따라서 다른 비즈니스 로직(analytics 등)을 실행하면 안 된다.
`runCallbacks를 false로 설정`하면 slide가 이동해도 swiper.js의 `slideChange` 등 콜백 함수가 **실행되지 않는다**. 

### 4-3. ShortsSwiper 컴포넌트

```tsx
import { Swiper, SwiperSlide } from 'swiper/react';

const ShortsSwiper = () => {
  const list = uselistStore((s) => s.list);
  const setSwiperActiveIndex = uselistStore((s) => s.setSwiperActiveIndex);

  const swiperRef = useRef<Swiper|null>(null); // Swiper.js 인스턴스를 보관할 ref
  useSwiperIndexForPrepending(swiperRef);

  return (
    <Swiper
      modules={[Virtual]}
      virtual
      onSwiper={(swiper: typeof Swiper) => (swiperRef.current = swiper)} // Swiper.js 인스턴스를 ref에 참조
      onSlideChange={(swiper) => setSwiperActiveIndex(swiper.activeIndex)} // 일반적인 slide 넘기기 동작을 할 때도 index를 변경한다
    >
      {list.map((shorts, i) => (
        <SwiperSlide key={shorts.id} virtualIndex={i}>
          {/* … */}
        </SwiperSlide>
      ))}
    </Swiper>
  );
};
```

---

## 5. 왜 이 방법이 **가장** 안전했는가?

| 요구/제약                         | 해결 포인트                                                                    |
| ----------------------------- | ------------------------------------------------------------------------- |
| **Virtual Slides 유지**         | DOM 은 최소 슬라이드 수만 그려서 성능 ↗                                                |
| **공식 가이드: prepend/append 금지** | 실제 데이터는 React가 관리                                                       |
| **선언형 철학**                    | `list`, `swiperActiveIndex` -> **global state** <br> DOM 보정은 *필요한 순간*에 “한 줄” 명령 |
| **UX**                        | 사용자는 prepend 순간에 **렌더 지연 & 점프 & 리플로우**를 체감하지 못함                                           |

---

## 6. 마치며

React + Swiper + Virtual Slides 조합에서 “데이터를 앞에 붙이면서 스크롤 위치 유지”는
문서 한 줄로는 해결되지 않는 **엣지 케이스**다.

* **완전 선언형**만 고집하면 퍼포먼스/캐시가 깨지고,
* **완전 명령형**으로 가면 React 와 상태 분기가 꼬인다.

이번 방식처럼 **“상태 = 선언형, 위치 보정 = 최소 명령형”** 으로 역할을 나누면
두 세계의 단점을 피하면서 필요한 UX 를 얻을 수 있다.

이 케이스를 프론트엔드 개발의 일종의 패턴이라 생각하고 정리하면 다음과 같다.

> 1. **전역 인덱스**를 상태로 보존하고
> 2. 데이터 길이 변화와 동시에 인덱스를 보정하며
> 3. `slideTo()`로 DOM 을 맞춰라.

## 그 외

[Swiper.js의 예제](https://codesandbox.io/p/sandbox/swiper-virtual-slides-react-28spe)는 왜 Virtual slide를 사용하면서 prepend를 하는가? 공식 문서의 가이드를 공식 예제가 따르지 않으면 개발자는 무얼 보고 개발해야 하는가? ㅠㅠ

EOD

20250612

---
layout: single
title: Transition/Translate 이벤트 핸들러 최적화
date: 2025-08-21 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://i.namu.wiki/i/791QGb45uFOV2j8KzEp08FrlW4Iu0ovdNWzDEKXCdyJmse7DQmJQLrOVKGXf_Ahyr0VYOAcCsEJS05UPHcPMPXGQCE4sYPo-kTfStXXqSUq1VnP0hrq9RvBVn4xAi9rI1RhbjwSylxJhjmOWwXnTMQ.webp"
    image: "https://i.namu.wiki/i/791QGb45uFOV2j8KzEp08FrlW4Iu0ovdNWzDEKXCdyJmse7DQmJQLrOVKGXf_Ahyr0VYOAcCsEJS05UPHcPMPXGQCE4sYPo-kTfStXXqSUq1VnP0hrq9RvBVn4xAi9rI1RhbjwSylxJhjmOWwXnTMQ.webp"
categories:
- IT
tags: [Javascript]
---

[SwiperJS](https://swiperjs.com/)를 사용하여 개발하다가 Transition/Translate 이벤트 핸들러가 성능 문제를 발생시켜 간단히 정리해 보았다.

## 1줄 요약

dom 옮기는 이벤트 콜백 함수에서는 requestAnimationFrame 를 써라.

## 3줄 요약

-	스와이프/스크롤/포인터 이동/라이브러리의 translate 콜백 같은 전환(transition) 관련 이벤트는 **프레임 동기화(requestAnimationFrame)** 보다 더 자주 발생한다. 그러면 저성능 기기(똥폰)이 아니더라도, 프레임은 끊기고 콜백 함수의 연산은 씹힌다.
- 이벤트 콜백 내부에서는 곧바로 계산/DOM 쓰기를 하지 말고, requestAnimationFrame를 써서 페인트 직전에 1번만 함수가 실행되게 하라. 쓰로틀링이다.
- requestAnimationFrame 콜백 내부에서 사용하는 연산 값이 있다면 requestAnimationFrame 콜백 내부에서 계산하라. 그렇지 않으면 함수 연산이 밀려서 엉뚱한 결과를 낸다.



## 현상: 이벤트는 "프레임"보다 시끄럽다

스와이프/포인터 이동/라이브러리의 translate 콜백(예: Swiper의 onSetTranslate)은 프레임 속도와 무관하게 더 자주 발생할 수 있다...고 하지만 2025년 현재, 웹뷰에서는 100% 이벤트 씹힘 현상이 발생한다.

- 이벤트가 씹히는 게 뭐냐? 실행되어야 할 이벤트가 발생하지 않는 것이다. 
- 왜 이벤트가 실행이 안 되나? 함수를 너무 많이(여러 번) 호출하면, 브라우저 렌더링이 완료되기 전에 호출된 모든 함수를 실행 완료할 수 없기 때문이다.
- 그러면 어떻게 하면 되느냐? 호출 횟수를 줄이면 된다.
- 얼마나 줄이면 되느냐? 화면이 이상하게 움직이지만 않을 정도로만 실행하면 된다.
- 화면이 이상하게 움직이지 않을 정도가 어느 정도인가? 화면 움직임 1프레임 당 1번이면 된다.
- 반면 이벤트가 프레임당 N회 들어오면, 콜백에서 DOM을 매번 만지면서 중복 계산과 불필요한 스타일/합성 작업을 유발한다.

또 한 가지 함정: 이벤트 직후의 상태는 종종 중간값이다. 예를 들어 translate가 순간적으로 "튀는" 값을 내놓을 때가 있고, 이 "튀는" 값을 제대로 처리하지 못 하면 화면이 이상하게 움직이거나, 움직이다 말거나, 끊기면서 움직이거나, 아예 안 움직일 수가 있다.

해결책을 논하기에 앞서 브라우저의 렌더링 파이프라인을 아주 간단하게 요약하면 이렇다. 크롬팀에서 렌더링NG를 내놓으면서 이것도 이제 슬슬 안 맞기 시작하지만...

```
자바스크립트 -> 스타일/레이아웃 -> (필요하면 composite) -> 페인트
```

## 해결책: requestAnimationFrame 안에서 계산하고, "프레임당 1회"만 실행하라

1.	스로틀링 효과: requestAnimationFrame은 스타일 재계산 직전에 호출된다. 그리고 1 렌더링 사이클에 1번만 실행된다. requestAnimationFrame가 쓰로틀링을 해주는 것이다.
2.	일관된 스냅샷: requestAnimationFrame 콜백 안에서 다시 최신 상태(예: swiper.translate, swiper.height)를 읽고 그 값으로 검사와 계산을 동시에 수행한다. 같은 프레임의 style 계산 직전이므로 중간값에 덜 휘둘리고, "검사는 최신값인데 쓰기는 옛값" 같이 더러운 클로저 발생을 예방한다

## 나쁜 예 vs 좋은 예

### 나쁜 예: 이벤트 콜백에서 직접 계산/쓰기

```javascript
function onSetTranslate(swiper: Swiper, translate: number) { // 이벤트가 프레임보다 자주 들어옴
  if (swiper.translate % swiper.height === 0) return; // 중간값에 걸려 불필요한 스킵 발생

  const y = getTranslateY(swiper, translate); // 검사/계산 시점 불일치

  videoDiv.style.transform = `translate3d(0,${y}px,0)`; // 프레임당 쓸데없이 여러 번 DOM 쓰기
}
```

### 좋은 예: 이벤트 콜백에서는 rAF만 예약, 실제 작업은 rAF 내부에서

```javascript
function onSetTranslate(swiper: Swiper) {
  requestAnimationFrame(() => {
    // rAF 안에서 "다시 읽기"
    const translate = swiper.translate ?? 0;
    const height = swiper.height || 0;

    // 검사와 계산을 같은 스냅샷 값으로
    const y = Math.max(translate, translate % height)        
    videoDiv.style.transform = `translate3d(0,${y}px,0)`;
  });
}
```

### 그 외

-	전환 취소/리셋 분기(transitionstart/transitionend)와 같은 이벤트는 1번만 일어나니 requestAnimationFrame을 사용할 필요는 없다.
- 하지만 움직임(translate) 동기화는 항상 rAF 루프에서 처리하라.
- 번외로, 권하건대 SwiperJS 같은 걸로 틱톡 UX 프론트엔드 만들지 말라. 그냥 처음부터 직접 만들어라. <b>SwiperJS는 틱톡 만들라고 있는 라이브러리가 아니다.</b> 울고 싶다.

## 결론

움직임 이벤트는 requestAnimationFrame 내부에서 읽고, 계산하고, 써라

EOD

20250821

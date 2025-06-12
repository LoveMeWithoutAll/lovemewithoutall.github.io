---
layout: single
title: React 커스텀 훅에서만 나타나는 `exhaustive-deps` 경고의 기술적 배경
date: 2025-06-12 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
categories:
- IT
tags: [react]
---

### `useRef` 와 `useEffect` 의 의존성 진단

## 커스텀 훅에서만 나타나는 `exhaustive-deps` 경고의 기술적 배경

#### 문제 제기

동일한 `ref` 객체를 사용하는 두 시나리오가 있다.

1. **컴포넌트 내부**에서 직접 `useRef()` 호출
2. **커스텀 훅**이 `useRef()` 결과를 반환

두 경우 모두 런타임 동작은 같지만, ESLint 규칙 `react-hooks/exhaustive-deps` 는 두 번째 패턴에만 “의존성 배열에 ref를 추가하라”는 경고를 띄운다. 왜 그럴까?

---

## 1. React 런타임: `useRef` 객체의 **안정성(Stable Identity)**

`useRef()` 는 최초 렌더에서 단 한 번 `{ current }` 객체를 생성하며, 이후 렌더에서도 **같은 객체 참조를 재사용**한다. React 공식 문서는 이를 명시적으로 보장한다. ([stackoverflow.com][1])

```tsx
function Comp() {
  const boxRef = useRef<HTMLDivElement>(null); // 항상 동일한 객체
  /* … */
}
```

> **결과**
>
> * 의존성 배열이 `[]`든 `[boxRef]`든, 객체 참조(식별자)가 변하지 않으므로 `useEffect`는 *추가 실행*되지 않는다.
> * 개발용 Strict Mode 에서는 의도적으로 초기 렌더가 두 번 일어나므로, 두 번 모두 마운트·언마운트 효과가 호출된다.

---

## 2. `exhaustive-deps` 규칙: **정적 분석의 한계**

`eslint-plugin-react-hooks` 는 컴파일 타임에 소스 코드를 스캔하며, 내부적으로 *“안정 반환값(Stable Return Value) 화이트리스트”* 를 유지한다.

```text
SAFE_RETURNS = { useRef, useState, useReducer, … }
```

* **컴포넌트 내부**에서 *직접* 호출된 훅이 이 목록에 포함되면, 해당 변수는 *자동으로 안정하다고 간주*되어 의존성 배열 생략이 허용된다.
* **커스텀 훅**은 함수 본문을 열어볼 수 없는 *블랙박스*다. 정적 분석 도구는 해당 훅이 매번 다른 객체를 생성할 가능성을 배제할 수 없으므로, *보수적* 전략을 취해 경고를 출력한다. 이 설계 원칙은 GitHub 이슈에서도 확인할 수 있다. ([github.com][2], [github.com][3])

즉, 문제의 근원은 **런타임 보증**과 **정적 분석 힌트** 간의 정보 비대칭에 있다.

---

## 3. 런타임 비교: `[]` vs `[ref]`

| 의존성 배열  | 재실행 조건             | 프로덕션 기준 실행 횟수\* |
| ------- | ------------------ | --------------- |
| `[]`    | 조건 없음              | 1회              |
| `[ref]` | `ref` 객체 참조가 바뀔 때만 | 객체가 불변 ⇒ 1회     |

\* React Strict Mode(개발 환경)에서는 의도적으로 +1회

`ref.current` 값은 mutable 하므로 의존성에 넣어도 변경을 **감지하지 않는다**. 값 변화를 트래킹하려면 `useState`, `useSyncExternalStore`, 콜백 ref 등 별도 패턴을 사용해야 한다.

---

## 4. 실무 대응 전략

| 방법                         | 구현 예                                                      | 장점 / 고려 사항                                |
| -------------------------- | --------------------------------------------------------- | ----------------------------------------- |
| **ref를 배열에 추가**            | `useEffect(fn, [myRef]);`                                 | ref 식별자는 불변 → 실행 1회, 경고 해소                |
| **effect를 훅 내부로 이동**       | `useFocusRef()` 내부에서 `useEffect` 실행                       | 컴포넌트 코드 간결, 경고 자체 제거                      |
| **ESLint 무시 주석**           | `// eslint-disable-next-line react-hooks/exhaustive-deps` | 최후 수단. 유지보수 리스크 존재                        |
| **커스텀 훅을 ‘안정 훅’으로 등록(포크)** | 포크된 `exhaustive-deps` 플러그인 사용                             | 공식 규칙엔 미지원; 팀 규모에 따라 검토 ([reddit.com][4]) |

---

## 5. 결론

1. **React 런타임**은 `useRef` 반환 객체의 *식별자 불변*을 보증한다.
2. **`exhaustive-deps` 규칙**은 내장 훅만 안정 값으로 취급하고, 커스텀 훅은 잠재적 변동 값으로 가정한다.
3. 결과적으로 `[ref]`를 넣든 `[]`로 두든 실행 횟수는 동일하지만, *정적 분석 경고*는 커스텀 훅에서만 노출된다.
4. 경고를 제거하려면 **ref를 배열에 추가**하거나 **effect를 훅 내부로 옮기는** 방식이 가장 안전하고 유지보수에 우호적이다.

> 런타임 보증과 정적 분석 간의 시각 차이를 이해하면 “왜 나만 경고를 보는지” 혼란스러울 이유가 사라진다. 프로젝트 규칙에 맞추어 의존성 배열을 조정하거나 훅 설계를 재구성하면 깔끔하게 해결된다. Happy Hooking! 🪝

---

[1]: https://stackoverflow.com/questions/72402329/what-is-the-exact-behaviour-of-useref-react-hook-does-the-object-get-recreated?utm_source=chatgpt.com "What is the exact behaviour of useRef react hook? Does the object ..."
[2]: https://github.com/facebook/react/issues/22539?utm_source=chatgpt.com "Allow custom hooks to return stable results · Issue #22539 - GitHub"
[3]: https://github.com/facebook/react/issues/24112?utm_source=chatgpt.com "Suggestion: [eslint-plugin-react-hooks] - exhaustive-deps ... - GitHub"
[4]: https://www.reddit.com/r/reactjs/comments/1d5rkro/can_eslint_reacthooksexhaustivedeps_safely_be/?utm_source=chatgpt.com "Can ESLint \"react-hooks/exhaustive-deps\" safely be ignored with ..."

---
layout: single
title: React Router useNavigate 훅 완전 분석 - "navigate('..') 한 번인데 왜 두 칸이 날아갈까?"
date: 2025-04-29 10:49:30.000000000 +09:00
type: post
header:
    teaser: "https://reactrouter.com/splash/hero-3d-logo.webp"
    image: "https://reactrouter.com/splash/hero-3d-logo.webp"
categories:
- IT
tags: [react-router]
---

# react-router useNavigate 훅의 동작 분석 - navigate는 어디서부터 시작되는가

## 환경
- react-router v7.5

## 1줄 요약

`navigate('.')`가 어디서부터 경로를 계산할지는, `useNavigate()`를 호출한 컴포넌트가 속한 `<Route> (=pathnameBase)`에 달려 있다.

## 현상

상세 페이지(path: `/list/:no`)에서 리스트(path: `/list`)로 돌아가기 위하여 아래 코드를 실행했다.

```ts
const navigate = useNavigate();

navigate('..'); // 예상: /list
```

하지만 실제로는 `/list` 대신 루트 경로(path: `/`)로 이동해 버렸다. 혹시나 navigate가 2번 실행되었는지 싶어서 콘솔 로그를 찍어보아도 `navigate`는 딱 1번 뿐.

아래와 같이 relative: path 옵션을 줘도 결과는 동일했다.

```ts
navigate('..', { relative: 'path' })
```

같은 코드를 쓰는 다른 컴포넌트들도 모두 같은 현상을 보인다.

## 디버깅 과정

1. navigate 호출 횟수
	- 확인 대상: 이벤트 버블링 or 더블 클릭으로 두 번 호출되는지 먼저 의심
	- 확인 방법: console.count() 로 체크
	- 결과: 1번만 호출 확인
1. 라우트 구조
	- 확인 대상: 라우트 선언이 잘못된 것 아닌가? 절대 & 상대 경로, path 없는 래퍼(Route path="") 존재 여부
	- 확인 방법: `<Route path="...">` 선언 확인
	- 결과: 문제 없음
1. 현재 URL
	- 확인 대상: 클릭 직전 URL이 확실히 `/list/:no` 인가? 나도 모르게 `/list`로 리다이렉트 하지는 않았을까?
	- 확인 방법: `location.pathname`을 log로 확인
	- 결과: 문제 없음. 클릭 직전 url은 `/list/:no` 였다.
1. 상대 경로 비교
	- 확인 대상: navigate('.') vs navigate('..') 동작 비교
	- 확인 결과: **공식 문서의 설명과는 다르게 navigate('.')는 기대한 대로 정상 동작**
	- 의문: '.' 은 기대한 대로 동작하고, '..' 는 그렇지 않았다. '.'는 현재 라우트로 이동이고, '..'는 상위 라우트로 이동 아닌가?
	- 공식 문서 확인: 공식 문서에는 기산점에 대해 애매하게만 써져 있다. 현재 위치는 `useNavigate`가 호출된 곳이라고 하는데, `relative: "path"` 옵션이 적용된 경우만 그런 것인지, 아닌지 확실치가 않다
1. useNavigate 내부 코드 확인
	- 확인 대상: `react-router hooks.tsx` 파일에서 useNavigate 내부 동작
	- 확인 방법:
		- [react-router의 matchs를 사용하여 route 구조를 배열로 만드는 코드를 확인해보니](https://github.com/remix-run/react-router/blob/main/packages/react-router/lib/hooks.tsx#L246C43-L246C62)
		- [useNavigate가 인식하는 현재 route는 현재 컴포넌트의 matches 배열에서 가장 마지막 엘리먼트](https://github.com/remix-run/react-router/blob/main/packages/react-router/lib/router/utils.ts#L1430C55-L1430C73)
	- 결과: `라우트 이동`의 **계산 기준점** 은 현재 컴포넌트에서 matches 배열의 마지막 element(Route)이다.
1. 라우트 기준점
	- 확인 대상: useMatches() 로 useNavigation을 실행하는 컴포넌트가의 `pathnameBase`을 확인
	- 확인 결과: `pathnameBase`가 `/list`(!)로 찍힌다
	- 의문: `pathnameBase`이 왜 `/list/:no`가 아닐까?

## 원인

1. useNavigate() 는 자신이 선언된 컴포넌트가 속한 `<Route>`의 `pathnameBase`을 기준점으로 삼는다.

1. `navigate` 옵션에 relative: 'path' 를 줘도, 계산 기준점은 변하지 않는다. 다만 해석 방식만 “URL 세그먼트” 방식으로 할 뿐이다.

1. Layout 같은 상위 라우트에서 `useNavigate을` 호출하여 라우팅을 하면, 상세 페이지( `/list/:no` )를 보고 있다고 하더라도 기준점은 `/list`가 된다.

```tsx
<Route path="/list" element={<Layout />}> {/* ← Layout 안에서 useNavigate() */}
	<Route index element={<List />} />
	<Route path=":no" element={<Content />} />
</Route>
```

4. 따라서 `'..'`을 1번만 적용해도 `/list` -> `/` 가 된다. `/list/:no`는 애당초 계산 대상이 아니었다. 결과적으로 **두 세그먼트가 한꺼번에 잘린 것처럼** 보인다.

## useNavigate가 인식하는 현재 path 확인 방법

### 방법1: 가장 간단

`React DevTools`에서 useNavigate를 호출하는 컴포넌트를 선택 -> hooks 패널에서 `Navigate.Route.matches[마지막].pathnameBase`를 확인

### 방법2

```ts
const Component = () => {
	// ...
	  // 매칭된 라우트 배열 (루트 → 가장 깊은 순)
  const matches = useMatches();

  // 현재 컴포넌트가 속한 라우트 = 배열의 마지막 요소
  const current = matches[matches.length - 1];

  console.log('pathnameBase : ', current.pathnameBase); // 예: "/list"
	// ...
}
```

### 방법3

```ts
// gpt가 알려준 편리한 방법
import { useResolvedPath } from 'react-router-dom';

function usePathnameBase(): string {
  // "." (route-relative) 를 해석한 결과가 곧 pathnameBase
  const { pathname } = useResolvedPath('.');

  return pathname;          // 예: "/list"
}
```

## 결론 및 best practice
- `useNavigate를` 호출하는 컴포넌트의 `pathnameBase`가 항상 라우팅 기준점이다.
- 햇갈릴 때는 애당초 절대 경로를 사용하여 라우팅 하는 것도 좋은 방법이다
	- 예: navigate('/list')
- 라우팅 디버깅 할 때는 라우팅 기준점을 디버깅 해야한다.
	- `useMatches()` / `useResolvedPath('.')` 로 `pathnameBase` 확인	

## 요약
- 문제: `navigate('..)` 를 썼는데 생각보다 많이 올라간다. 

- 원인: navigate 의 출발점은 navigate를 실행하는 컴포넌트의 `<Route>`(pathnameBase)이기 때문. `navigate` 하는 코드가 공통 헤더 & 검색바처럼 상위 라우트에 붙어 있으면, 상세 URL 세그먼트를 계산에 포함하지 못한다.

- 해결
	- 상위 라우트의 공통 컴포넌트에서는 navigate('.') 를 사용해서 상위 route로 이동하거나,
	- navigate('/notice') 처럼 절대 경로를 쓰거나,
	-`useNavigate()` 호출 위치를 하위 라우트로 옮긴 후, `navigate('..')`를 실행(상위 라우트로 이동)

- 체크포인트: 항상 pathnameBase를 로그로 확인하라

EOD

20250430

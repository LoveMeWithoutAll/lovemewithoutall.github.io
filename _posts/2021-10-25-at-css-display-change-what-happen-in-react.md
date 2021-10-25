---
layout: single
title: react에서 CSS display none을 쓰면 어떤 일이?
date: 2021-10-25 20:43:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
    image: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
categories:
- IT
tags: [react, css]
---

## display: block -> display: none이 된 component는

1. 렌더링되지 않는다
2. DOM tree에는 보인다
3. React devtools Component tab에는 보인다
4. component는 destroy 되지 않는다
	1. component는 계속 유지된다
	2. component는 하던 일을 계속 한다
	3. useEffect의 return은 실행되지 않는다

-------

## 그렇다면 dislpay: none이 초기값인 component는?

display: block -> none이 될 때와 마찬가지로

1. 렌더링되지 않는다
2. DOM tree에는 보인다
3. React devtools Component tab에는 보인다
4. component는 일을 한다
	1. useEffect가 실행된다

-------

## 결론

1. React의 functional component는 React element를 return 한다.
	1. React element는 CSS display property가 block이든 none이든 상관없이 return한다
	2. 이 단계에서 side effect도 모두 실행된다
2. 브라우저는 return 된 React element를 사용하여 DOM tree와 CSSOM tree를 만든다
	1. Rendering tree를 만들 때 display: none인 element는 빠진다
	2.  React는 이 단계에서 아무 역할도 하지 않는다

-------

## 예제 코드

https://codesandbox.io/s/react-style-display-none-jf32k?file=/src/App.tsx

20210927

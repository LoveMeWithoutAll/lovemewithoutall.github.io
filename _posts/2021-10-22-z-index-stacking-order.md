---
layout: single
title: z-index stacking order
date: 2021-10-22 18:05:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/d/d5/CSS3_logo_and_wordmark.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/d/d5/CSS3_logo_and_wordmark.svg"
categories:
- IT
tags: [css]
---

CSS property z-index는 parent를 공유하는 element끼리만 영향을 미친다. 다른 parent의 element와는 무관하다. 
그 이유는 stacking order 때문이다. stacking context는 하나의 element당 1개 생성되고, stacking order는 하나의 stacking element 내에서만 적용된다.

---

## 예제

blue의 z-index가 cat의 z-index보다 크다.  하지만 서로 다른 stacking context에 속하기 때문에 서로간의 z-index 차이는 stacking order에 영향을 미치지 않는다. 그리고 cat과 parent element를 공유하는 content의 z-index는 cat의 z-index보다 낮다. blue의 z-index는 content element 안에서만 stacking order에 영향을 미친다. 그래서 blue가 cat에게 덮힌다.

https://codesandbox.io/s/z-index-in-blocks-s7box

---

## 참고

https://www.w3.org/TR/CSS21/visuren.html#layers

https://erwinousy.medium.com/z-index%EA%B0%80-%EB%8F%99%EC%9E%91%ED%95%98%EC%A7%80%EC%95%8A%EB%8A%94-%EC%9D%B4%EC%9C%A0-4%EA%B0%80%EC%A7%80-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EA%B3%A0%EC%B9%98%EB%8A%94-%EB%B0%A9%EB%B2%95-d5097572b82f

20210915

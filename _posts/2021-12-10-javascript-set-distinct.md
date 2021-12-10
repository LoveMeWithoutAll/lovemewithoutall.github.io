---
layout: single
title: javascript Set 자료형이 object를 element로 한다면?
date: 2021-12-10 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2021-12-10-javascript-set-distinct/2021-12-10-javascript-set-distinct-1.png"
    image: "/assets/images/2021-12-10-javascript-set-distinct/2021-12-10-javascript-set-distinct-1.png"
categories:
- IT
tags: [javascript, set]
---

javascript Set 자료형에 object를 element로 넣는다면, 각 element가 unique한지 어떻게 구별하는 기준은 무엇일까? object 안의 property value까지 비교할까?

메모리 주소값만 비교한다.

예를 들어보자.

```javascript
let a = {a: 1};
let b = {a: 1};

let set = new Set([a, b]);
```

의 결과는?

![result1](/assets/images/2021-12-10-javascript-set-distinct/2021-12-10-javascript-set-distinct-1.png)

그러니 Set을 사용해 object를 distinct하게 select 하려는 시도는 하지 말자.

-----

만약 object에 unique한 값이 있다면, 그 값을 key로 하여 Map 자료형을 사용하라.

예시

```javascript
let a = {key: 'a', value: 1};
let b = {key: 'b', value: 1};
let arr = [a, b];

const map = new Map();
arr.forEach(element => map.set(element.key, element));

const uniqueArr = [...map.values()]
```

![result2](/assets/images/2021-12-10-javascript-set-distinct/2021-12-10-javascript-set-distinct-2.png)

20211203

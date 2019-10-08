---
layout: single
title: JavaScript console 객체의 신기한 메소드
date: 2019-10-08 17:33:30.000000000 +09:00
type: post
header:
    teaser: "https://developers.google.com/web/tools/chrome-devtools/console/images/playground.png"
    image: "https://developers.google.com/web/tools/chrome-devtools/console/images/playground.png"
categories:
- IT
tags: [javascript]
---

Chrome browser 기준이다.

## console.count

함수가 몇 번 호출되었는지 알려준다.

```javascript
function sayHello(name) {
  console.count()
  console.log(name)
}

sayHello("Indrek")  // default: 1 /n Indrek
sayHello("William") // default: 2 /n William
sayHello("Kelly")   // default: 3 /n Kelly
```

같은 파라미터로 몇 번 호출되었는지도 알려준다.

```javascript
function sayHello(name) {
  console.count(name)
}

sayHello("Indrek")  // Indrek: 1
sayHello("William") // William: 1
sayHello("Kelly")   // Kelly: 1
sayHello("Indrek")  // Indrek: 2
```

## console.table

배열을 읽기 편하게 보여준다.

```javascript
const fruits = ["kiwi", "banana", "strawberry"]

console.table(fruits)

| (index)       | Value          |
| ------------- | -------------  |
| 0             | "kiwi"         |
| 1             | "banana"       |
| 2             | "strawberry"   |
// Array(3)0: "kiwi"1: "banana"2: "strawberry"length: 3__proto__: Array(0)
```

object도 된다.

```javascript
const pets = {
  name: "Simon",
  type: "cat"
};

console.table(pets);

| (index)       | Value          |
| ------------- | -------------  |
| name          | "Simon"        |
| type          | "cat"          |
// Object..
```

출처: https://medium.com/better-programming/boost-your-javascript-debugging-skills-with-these-console-tricks-ab984c70298a

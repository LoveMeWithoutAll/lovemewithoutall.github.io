---
layout: single
title: Cool Methods on the JavaScript console Object
date: 2019-10-08 17:33:30.000000000 +09:00
type: post
header:
    teaser: "https://developers.google.com/web/tools/chrome-devtools/console/images/playground.png"
    image: "https://developers.google.com/web/tools/chrome-devtools/console/images/playground.png"
categories:
- IT
tags: [javascript]
---

This is based on the Chrome browser.

## console.count

It tells you how many times a function has been called.

```javascript
function sayHello(name) {
  console.count()
  console.log(name)
}

sayHello("Indrek")  // default: 1 /n Indrek
sayHello("William") // default: 2 /n William
sayHello("Kelly")   // default: 3 /n Kelly
```

It can also tell you how many times it was called with the same parameter.

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

It displays an array in an easy-to-read way.

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

It works with objects too.

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

Source: https://medium.com/better-programming/boost-your-javascript-debugging-skills-with-these-console-tricks-ab984c70298a

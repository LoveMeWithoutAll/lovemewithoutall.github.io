---
layout: single
title: JavaScript Conditional Object Properties
date: 2022-03-16 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2022-03-16-typescript-conditional-object-property/example.png"
    image: "/assets/images/2022-03-16-typescript-conditional-object-property/example.png"
categories:
- IT
tags: [javascript]
---


A technique for conditionally adding a property to an object.

```javascript
const obj = {
    ...(isTrue && {a: 'a'})
}

// if isTrue is true
obj = {a: 'a'}

// if isTrue is false
obj = {}
```

![example](/assets/images/2022-03-16-typescript-conditional-object-property/example.png)

This is a pattern I've found really useful, but during a code review a brilliant teammate discovered the following.
1. There's no problem when you combine a boolean with object destructuring (the typical case), but
2. when you use false, you get the error below. The error says you can't use the spread operator because it's not an object type.

![error](/assets/images/2022-03-16-typescript-conditional-object-property/error.png)

My guess is that internally TypeScript handles boolean and false differently. The fact that it warns you ahead of time, before compilation, about these minor (?) things that could cause bugs proves that TypeScript really is a wise (?) language.

Aside from that, I went into the TypeScript git repo for the first time to dig into this, but I came away with nothing but a sense of despair....

20220224

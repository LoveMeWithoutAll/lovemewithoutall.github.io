---
layout: single
title: "What if a JavaScript Set holds objects as elements?"
date: 2021-12-10 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2021-12-10-javascript-set-distinct/2021-12-10-javascript-set-distinct-1.png"
    image: "/assets/images/2021-12-10-javascript-set-distinct/2021-12-10-javascript-set-distinct-1.png"
categories:
- IT
tags: [javascript, set]
---

If you put objects as elements into a JavaScript Set, what is the criterion for deciding whether each element is unique? Does it compare even the property values inside the objects?

It only compares the memory address.

Let's look at an example.

```javascript
let a = {a: 1};
let b = {a: 1};

let set = new Set([a, b]);
```

What's the result?

![result1](/assets/images/2021-12-10-javascript-set-distinct/2021-12-10-javascript-set-distinct-1.png)

So don't try to use a Set to select distinct objects.

-----

If your object has a unique value, use a Map with that value as the key.

Example

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

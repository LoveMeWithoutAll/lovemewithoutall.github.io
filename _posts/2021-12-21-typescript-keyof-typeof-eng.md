---
layout: single
title: typescript keyof typeof
date: 2021-12-21 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
categories:
- IT
tags: [typescript]
---

In typescript, when you want to do something (like comparing objects) based on all of an object's keys, the type becomes a headache. The object's type is fixed, but when you use the Object.keys() API, the keys come out as the string type. If you try to look up an object's property using a key that was extracted as the string type, you get a type error.

```typescript
// error example
const initData = {
  broadcastStatus: {
    SCHEDULED: true,
    WAITING: true,
    ONAIR: true,
    PAUSED: true,
    ENDED: false,
  },
};

const otherData = {
  broadcastStatus: {
    SCHEDULED: true,
    WAITING: true,
    ONAIR: true,
    PAUSED: true,
    ENDED: false,
  },
}

const test = Object.keys(broadcastStatus).some(
       key => broadcastStatus[key] !== initData.broadcastStatus[key],
    );

// error log
TS7053: Element implicitly has an 'any' type because expression of type 'string' can't be used to index type '{ SCHEDULED: boolean; WAITING: boolean; ONAIR: boolean; PAUSED: boolean; ENDED: boolean; }'.
No index signature with a parameter of type 'string' was found on type '{ SCHEDULED: boolean; WAITING: boolean; ONAIR: boolean; PAUSED: boolean; ENDED: boolean; }'.
```

I solved this problem by typing the return value of Object.keys() as Array<keyof typeof broadcastStatus>.

```typescript
// you can just use keyof typeof
const test = (Object.keys(broadcastStatus) as Array<
        keyof typeof broadcastStatus
      >).some(key => broadcastStatus[key] !== initData.broadcastStatus[key])
```

So what kind of magic does keyof typeof pull off?
Applying typeof to an object gives you that object's type.
Applying keyof to the object's type gives you a type that joins the object's keys, as string values, with |.

```typescript
const obj = {
  SCHEDULED: true,
  WAITING: true,
  ONAIR: true,
  PAUSED: true,
  ENDED: false,
}

let type1: typeof obj;
// type1: {PAUSED: boolean, ONAIR: boolean, WAITING: boolean, ENDED: boolean, SCHEDULED: boolean}

let key: keyof typeof obj;
// let key: "PAUSED" | "ONAIR" | "WAITING" | "ENDED" | "SCHEDULED"
```

20211214

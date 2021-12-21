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

typescript에서 object의 모든 key값을 기준으로 무언가 작업(object 비교)를 할 때, type 때문에 골치아파진다. object의 type은 정해져 있는데, Object.keys() api를 사용하면 key가 string type으로 뽑혀 나온다. string type으로 뽑혀나온 key로 object의 property를 조회하려면 type 오류가 발생한다. 

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

이 문제는 Object.keys()의 리턴값에 Array<keyof typeof broadcastStatus>로 타입을 지정하여 해결했다.

```typescript
// keyof typeof 를 활용하면 된다
const test = (Object.keys(broadcastStatus) as Array<
        keyof typeof broadcastStatus
      >).some(key => broadcastStatus[key] !== initData.broadcastStatus[key])
```

keyof typeof 어떤 마법을 부리길래?
Object에 typeof를 그 객체의 타입이 나온다.
Object의 type에 keyof를 하면 그 객체의 key를 string 값으로 해서 | 로 이은 type이 나온다.

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

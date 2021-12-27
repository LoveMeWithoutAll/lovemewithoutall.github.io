---
layout: single
title: typescript에서 여러 개의 type을 붙이고 싶다면
date: 2021-12-27 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
categories:
- IT
tags: [typescript]
---

Intersection Types를 쓰면 된다.

type을 & 로 붙이면 된다.

굳이 타입을 여러 번 선언할 필요가 없다면

```typescript
type BroadcastDisplay = {
  no: number;
  title: string;
  startAt: string;
  endAt: string;
  state: BroadcastState;
  HOME: boolean;
  SCHEDULE_TABLE: boolean;
  TOP_MAIN_CARD: boolean;
};

type DisplayRowProps = BroadcastDisplay & {
  testType: ({no, type, value}: DisplayToggleValueType) => void;
};
```

이런 식으로 해도 된다.

20211223

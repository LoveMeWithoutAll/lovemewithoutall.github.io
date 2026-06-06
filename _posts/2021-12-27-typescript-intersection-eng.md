---
layout: single
title: If you want to combine multiple types in TypeScript
date: 2021-12-27 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
categories:
- IT
tags: [typescript]
---

You can use Intersection Types.

Just combine the types with `&`.

If you don't really need to declare the type multiple times,

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

you can do it like this.

20211223

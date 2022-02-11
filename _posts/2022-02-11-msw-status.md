---
layout: single
title: msw에서 status만 응답하도록 하는 코드
date: 2022-02-09 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://github.com/mswjs/msw/raw/main/media/msw-logo.svg"
    image: "https://github.com/mswjs/msw/raw/main/media/msw-logo.svg"
categories:
- IT
tags: [etc]
---

200 ok status만 응답하는 코드
```
rest.patch(
  `url`,
  (_, res, ctx) => {
    return res(ctx.status(200));
  },
),
```

20220209

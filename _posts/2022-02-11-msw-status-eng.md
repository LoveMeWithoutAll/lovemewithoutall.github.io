---
layout: single
title: Code to make msw respond with status only
date: 2022-02-09 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://github.com/mswjs/msw/raw/main/media/msw-logo.svg"
    image: "https://github.com/mswjs/msw/raw/main/media/msw-logo.svg"
categories:
- IT
tags: [etc]
---

Code that responds with a 200 OK status only

```javascript
rest.patch(
  `url`,
  (_, res, ctx) => {
    return res(ctx.status(200));
  },
),
```

20220209

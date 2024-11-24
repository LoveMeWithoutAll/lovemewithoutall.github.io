---
layout: single
title: next.js dynamic import
date: 2024-11-20 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js]
---
next.js에서 dynamic import 함부로 하지 마라. 컴포넌트 내부에서 선언하면, 그 컴포넌트를 리렌더링 할 때마다 dynamic import 한 컴포넌트를 새로 그린다.

```typescript
const component = dynamic(() => import('@/component'), { ssr: false });
```

20241120

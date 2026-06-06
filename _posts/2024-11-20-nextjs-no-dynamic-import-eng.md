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
Don't use dynamic import carelessly in next.js. If you declare it inside a component, the dynamically imported component gets re-rendered from scratch every time that component re-renders.

```typescript
const component = dynamic(() => import('@/component'), { ssr: false });
```

20241120

---
layout: single
title: A bug where error boundaries don't work in Next.js 14
date: 2024-11-08 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js]
---

This is based on Next.js v14.2.16.

Next.js provides a convenient Error boundary feature. If you create an `error.tsx` (or `error.js` in JavaScript) in a routing directory, this file acts as the error boundary for that routing directory. Errors that occur in a layout are caught by the `error.tsx` in the parent routing directory. Errors that occur at the topmost level, or errors that other error boundaries fail to catch, are caught by `global-error.tsx`. This works because Next.js internally wraps the code in a `try catch`.

But there's a bug. When you use the `generateMetadata` function in `page.tsx`, if an error occurs, neither the `error.tsx` in the same routing directory nor `global-error.tsx` catches the error. The Next.js server throws a 500 error, and the following error is printed in the web browser's console.

```typescript
The above error occurred in the <ServerRoot> component:

    at ServerRoot (webpack-internal:///(app-pages-browser)/../../.yarn/__virtual__/next-virtual-b305331578/0/cache/next-npm-14.2.16-96548c5567-819e285096.zip/node_modules/next/dist/client/app-index.js:112:27)
    at Root (webpack-internal:///(app-pages-browser)/../../.yarn/__virtual__/next-virtual-b305331578/0/cache/next-npm-14.2.16-96548c5567-819e285096.zip/node_modules/next/dist/client/app-index.js:117:11)
    at ReactDevOverlay (webpack-internal:///(app-pages-browser)/../../.yarn/__virtual__/next-virtual-b305331578/0/cache/next-npm-14.2.16-96548c5567-819e285096.zip/node_modules/next/dist/client/components/react-dev-overlay/app/ReactDevOverlay.js:87:9)

Consider adding an error boundary to your tree to customize error handling behavior.
Visit https://reactjs.org/link/error-boundaries to learn more about error boundaries.
```

This situation isn't covered in the official documentation. I searched the Next.js GitHub issues (see below). I couldn't find an issue that exactly matched the problem I ran into. But it seems there has long been an issue where `error.tsx` fails to catch errors from `generateMetadata`.

* [Errors in `generateMetadata` are not handled by `error.tsx` #49925
](https://github.com/vercel/next.js/discussions/49925)
* [[NEXT-1305] Errors in `generateMetadata` are not caught by global-`error.tsx` boundary in dev mode #50863](https://github.com/vercel/next.js/issues/50863)

After much trial and tribulation, I found a solution. If you wrap the code that might throw an error in `generateMetadata` with a `try catch`, then `error.tsx` catches the error and performs its role as an error boundary properly.

You can do it like the code below.

```typescript
import { Metadata } from 'next';

export async function `generateMetadata`({ searchParams }: pageProps): Promise<Metadata> {
  try {
    const response = await API.get();
    
    return {
      title: response.title
    };
  } catch (e) {
    return {
      title: response.title
    };
  }
}
```

Looking around reddit, there's plenty of criticism, like you should ditch Next.js, it's an over-engineered monster, and so on. I too had to briefly estimate the timeline needed to remove Next.js.

You can't develop with Next.js by relying on the official documentation alone.

20241108

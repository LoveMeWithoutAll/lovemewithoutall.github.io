---
layout: single
title: How to fix Next.js 14 parallel route errors
date: 2024-10-29 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js]
---

They say that using Next.js's [parallel routes](https://nextjs.org/docs/14/app/building-your-application/routing/parallel-routes) feature lets you easily implement a modal UI. But when I tried it on version 14.2.16, it didn't work properly. When I checked the source code on [github](https://github.com/vercel/nextgram) for an app where [parallel routes](https://nextgram.vercel.app/) work correctly, it turned out to be next.js version 15.

## Symptoms

The `@directory_name/(.)sub_directory_name/page.tsx` is not overlaid as a modal on top of the rendering of `app/page.tsx`. Let's break down the symptoms. When you navigate to `/sub_directory_name` to bring up the modal,

* Parallel routes: `@directory_name/(.)sub_directory_name/page.tsx` does not get slotted into the slot corresponding to `@directory_name`. Instead, `@directory_name/(.)sub_directory_name/page.tsx` goes into the `children` slot (the implicit slot of `app/page.tsx`). `app/page.tsx` does not get rendered.
* [Intercepting routes](https://nextjs.org/docs/14/app/building-your-application/routing/intercepting-routes): The `(.)` convention does not work. It fails to detect the same level as the path.

The following error log on the next.js server sometimes appears and sometimes doesn't.

```typescript
TypeError: Cannot read properties of undefined (reading '0') at /Users/a1101586/dev/11st/shorts-fe/.yarn/__virtual__/next-virtual-d46895ab80/0/cache/next-npm-14.2.15-21f04e6ccc-5c5ed27888.zip/node_modules/next/dist/compiled/next-server/app-page.runtime.dev.js:39:13374 at Array.map (<anonymous>) at /Users/a1101586/dev/11st/shorts-fe/.yarn/__virtual__/next-virtual-d46895ab80/0/cache/next-npm-14.2.15-21f04e6ccc-5c5ed27888.zip/node_modules/next/dist/compiled/next-server/app-page.runtime.dev.js:39:13346 at async Promise.all (index 1) at async rW (/Users/a1101586/dev/11st/shorts-fe/.yarn/__virtual__/next-virtual-d46895ab80/0/cache/next-npm-14.2.15-21f04e6ccc-5c5ed27888.zip/node_modules/next/dist/compiled/next-server/app-page.runtime.dev.js:39:13008) at async r0 (/Users/a1101586/dev/11st/shorts-fe/.yarn/__virtual__/next-virtual-d46895ab80/0/cache/next-npm-14.2.15-21f04e6ccc-5c5ed27888.zip/node_modules/next/dist/compiled/next-server/app-page.runtime.dev.js:39:16189) {page: "/product", stack: "TypeError: Cannot read properties of undefined (re…led/next-server/app-page.runtime.dev.js:39:16189)", message: "Cannot read properties of undefined (reading '0')"}
```

## Cause

I don't know. Looking at the error that occurs on the server, it seems the error probably happens during the process of slotting two slots into the `root layer`.

## Solution

### 1

Modify the convention of the [intercepting route](https://nextjs.org/docs/14/app/building-your-application/routing/intercepting-routes) syntax. If you go by the docs, you'd think that since `(.)` refers to the same routing level, you can just use it. That's what the official docs say too. But the `(.)` convention does not work properly together with parallel routes.

Instead, **the `(...)` convention (which points to the root app directory) works correctly**.

Trace back up from the app directory.

### 2

Very occasionally, deleting all dependencies and reinstalling them makes it work correctly.

20241030

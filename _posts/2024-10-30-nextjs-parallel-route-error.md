---
layout: single
title: Next.js 14 parallel route 오류 해결법
date: 2024-10-29 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js]
---

Next.js의 [패러렐 라우트](https://nextjs.org/docs/14/app/building-your-application/routing/parallel-routes) 기능을 사용하면 modal UI를 간편하게 구현할 수 있다고 한다. 하지만 14.2.16  버전에서 확인해보니 정상 동작하지 않는다. [패러렐 라우트](https://nextgram.vercel.app/)가 정상 동작하는 앱의 소스 코드를 [github](https://github.com/vercel/nextgram)에서 확인해보니 next.js 15 버전이다.

## 현상

`app/page.tsx`의 렌더링 위에 `@디렉토리명/(.)하위_디렉토리명/page.tsx`가 모달로 오버레이 되지 않는다. 현상을 쪼개보자. modal을 띄우기 위해 `/하위_디렉토리명`으로 path를 이동시키면,

* 패러렐 라우트: `@디렉토리명`에 상응하는 slot에 `@디렉토리명/(.)하위_디렉토리명/page.tsx`이 끼워지질 않는다. 대신 `children` slot(`app/page.tsx`의 암시적 slot)에 `@디렉토리명/(.)하위_디렉토리명/page.tsx`이 들어간다. `app/page.tsx`는 렌더링 되지 않는다.
* [인터셉팅 라우트](https://nextjs.org/docs/14/app/building-your-application/routing/intercepting-routes): `(.)` 컨벤션이 동작하지 않는다. path와 같은 레벨의  감지하지 못한다.

next.js 서버에서 아래 오류 로그는 뜰 때도 있고, 안 뜰 때도 있다.

```typescript
TypeError: Cannot read properties of undefined (reading '0') at /Users/a1101586/dev/11st/shorts-fe/.yarn/__virtual__/next-virtual-d46895ab80/0/cache/next-npm-14.2.15-21f04e6ccc-5c5ed27888.zip/node_modules/next/dist/compiled/next-server/app-page.runtime.dev.js:39:13374 at Array.map (<anonymous>) at /Users/a1101586/dev/11st/shorts-fe/.yarn/__virtual__/next-virtual-d46895ab80/0/cache/next-npm-14.2.15-21f04e6ccc-5c5ed27888.zip/node_modules/next/dist/compiled/next-server/app-page.runtime.dev.js:39:13346 at async Promise.all (index 1) at async rW (/Users/a1101586/dev/11st/shorts-fe/.yarn/__virtual__/next-virtual-d46895ab80/0/cache/next-npm-14.2.15-21f04e6ccc-5c5ed27888.zip/node_modules/next/dist/compiled/next-server/app-page.runtime.dev.js:39:13008) at async r0 (/Users/a1101586/dev/11st/shorts-fe/.yarn/__virtual__/next-virtual-d46895ab80/0/cache/next-npm-14.2.15-21f04e6ccc-5c5ed27888.zip/node_modules/next/dist/compiled/next-server/app-page.runtime.dev.js:39:16189) {page: "/product", stack: "TypeError: Cannot read properties of undefined (re…led/next-server/app-page.runtime.dev.js:39:16189)", message: "Cannot read properties of undefined (reading '0')"}
```

## 원인

모른다. 서버에서 발생하는 오류를 보니, 아마도 `root layer`에 2개의 slot을 끼우는 과정에서 오류가 발생하는 것 같다.

## 해결법

[intercepting route](https://nextjs.org/docs/14/app/building-your-application/routing/intercepting-routes) 구문의 컨벤션을 수정한다. 문서대로라면 `(.)`를 사용하면 같은 라우팅 레벨이니까 이걸 사용하면 될거라 생각할거다. 공식 문서에도 그렇게 되어 있다. 하지만 `(.)` 컨벤션은 parallel routed와 함께 정상 동작하지 않는다. 

대신 **`(...)`(root app directory를 가리킴) 컨벤션은 정상 동작**한다.

app 디렉토리부터 거슬러 올라가자.

20241030

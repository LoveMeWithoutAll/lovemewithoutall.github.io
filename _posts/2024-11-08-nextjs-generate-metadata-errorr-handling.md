---
layout: single
title: Next.js 14 에서 error boundary가 동작하지 않는 오류
date: 2024-11-08 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js]
---

Next.js v14.2.16 기준이다.

Next.js는 편리한 Error boundary 기능을 제공한다. 라우팅 디렉토리에 `error.tsx`(js에서는 error.js)를 작성하면 이 파일이 해당 라우팅 디렉토리의 error boundary 역할을 해준다. layout에서 발생하는 에러는 상위의 라우팅 디렉토리에 있는 `error.tsx가` 잡아준다. 최상위에서 발생하는 에러 혹은 다른 error boundary가 잡아내지 못 한 에러는 `global-error.tsx`가 잡아준다. Next.js의 내부적으로 코드를 `try catch`로 감싸고 있기 때문에 이렇게 동작하는 것이다.

하지만 버그가 있다. `page.tsx`에서 `generateMetadata` 함수를 사용할 때, 에러가 발생하면 같은 라우팅 디렉토리에 있는 `error.tsx` 뿐만 아니라 global-`error.tsx에서도` 에러를 잡아내지 못한다. Next.js 서버가 500에러를 내고, 웹브라우저의 console에서는 이런 에러가 출력된다.

```typescript
The above error occurred in the <ServerRoot> component:

    at ServerRoot (webpack-internal:///(app-pages-browser)/../../.yarn/__virtual__/next-virtual-b305331578/0/cache/next-npm-14.2.16-96548c5567-819e285096.zip/node_modules/next/dist/client/app-index.js:112:27)
    at Root (webpack-internal:///(app-pages-browser)/../../.yarn/__virtual__/next-virtual-b305331578/0/cache/next-npm-14.2.16-96548c5567-819e285096.zip/node_modules/next/dist/client/app-index.js:117:11)
    at ReactDevOverlay (webpack-internal:///(app-pages-browser)/../../.yarn/__virtual__/next-virtual-b305331578/0/cache/next-npm-14.2.16-96548c5567-819e285096.zip/node_modules/next/dist/client/components/react-dev-overlay/app/ReactDevOverlay.js:87:9)

Consider adding an error boundary to your tree to customize error handling behavior.
Visit https://reactjs.org/link/error-boundaries to learn more about error boundaries.
```

공식 문서에는 없는 상황이다. Next.js github issue(하단 참조)를 찾아 보았다. 내가 겪은 문제와 정확히 일치하는 오류는 없다. 하지만 예전부터 `generateMetadata`의 에러를 `error.tsx가` 잡아내지 못하는 문제가 있었던 것으로 보인다.

* [Errors in `generateMetadata` are not handled by `error.tsx` #49925
](https://github.com/vercel/next.js/discussions/49925)
* [[NEXT-1305] Errors in `generateMetadata` are not caught by global-`error.tsx` boundary in dev mode #50863](https://github.com/vercel/next.js/issues/50863)

고행 끝에 해결책을 찾았다. `generateMetadata`에서 에러가 발생할만한 코드를 `try catch`로 감싸주면, `error.tsx가` 에러를 잡아채어 error boundary 역할을 정상적으로 수행한다. 

아래 코드처럼 하면 된다.

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

reddit을 찾아보니 Next.js를 버려야 한다, 오버엔지니어링 된 괴물이다, 등등 비난이 많다. 나 역시 잠시나마 Next.js를 제거하는데 필요한 일정을 검토해야만 했다.

Next.js는 공식 문서만 보고 개발할 수 없다.

20241108

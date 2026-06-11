---
layout: single
title: import 한 줄이 e2e test 를 깨뜨리다
date: 2026-06-11 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://raw.githubusercontent.com/wingsuitist/ecmascript-logo/master/es-ecmascript-logo.png"
    image: "https://raw.githubusercontent.com/wingsuitist/ecmascript-logo/master/es-ecmascript-logo.png"
categories:
- IT
tags: [javascript]
---

## 기술 스택

- Amplify v6 Gen 2
- react-router v6
- react v19


## 현상

앱을 새로 실행할 때마다 amplify cognito 인증 토큰을 refresh 하더라. 인증 토큰이 만료되지 않았는데도 그랬다. 로컬 서버에서든, 운영 서버에서든 마찬가지였다. 찜찜했지만 서비스 사용에는 아무 문제도 없었기에 그냥 두었다.

문제는 e2e test 에서 발생했다. e2e test 를 실제 cognito 서버에서 인증 토큰을 받아오도록 구성했을 때는 문제가 없었다. 하지만 너무나도 느린 e2e test에 고통받은 나머지, 가짜 인증 토큰을 만들어 mocking 하는 방식으로 e2e test 구성을 변경하자 문제가 발생했다. 대부분의 e2e 테스트는 cognito 인증 토큰이 필요했다. 그래서 나는 인증 토큰을 mocking 했다. 그리고 e2e test 환경에서의 amplify cognito 가 바라보는 localStorage에 미리 인증 토큰을 박아두었다. 이러면 인증이 안 되어서 테스트가 실패하는 현상이 발생하지 않아야 하는데, 놀랍게도 인증은 실패했다. 거지 같은 상황이었다.

여태껏 흐린 눈으로 외면하고 있던 amplify cognito의 기이한 문제를 해결할 때가 온 것이다.


## 증상

우선 앱을 실행할 때마다 amplify cognito 인증 토큰을 refresh 하는 원인부터 조사했다. 확인해보니 `react-router`에 걸어둔 loader 가 문제였다. loader 에서 인증 여부를 검사하도록 해두었는데, 여기서 인증이 실패하니 인증 토큰이 만료되었는지 여부를 확인하는 코드가 실행되는 것이다. 문제는 유효한 토큰이 있어서 인증이 실패할 상황이 아닌데도 인증이 실패하는 경로를 타는 것이었다.


## 부팅 구조

그래서 앱 초기화 코드를 살펴보았다.

부팅은 단순해 보였다. `main.tsx` 가 엔트리고, `import App from '@/App'` → `App` 이 `routes.tsx` 를 import 한다. `routes.tsx` 는 **모듈 최상단**에서 `export const router = createBrowserRouter([...])`. 그리고 `main.tsx` **본문**에서 `Amplify.configure(...)` 를 호출한 뒤 render. 읽을 땐 아무 문제도 없어 보인다. amplify configure 가 코드상 render 위에 있으니까.


## 헛다리: "토큰이 없거나 만료됐나?"

당연히 토큰을 먼저 의심했다. 인증 토큰을 잘못 넣었나, 만료됐나, amplify cognito 에서 인증 토큰 파싱을 잘못했나. 토큰을 찍어 보려고 amplify cognito 인증 provider 의 `getTokens` 에 로그를 박았다. 그런데 첫 `fetchAuthSession`(인증 여부 확인 코드) 실행 시점에는 `getTokens` 함수가 **호출조차 안 됐다.** 강제 refresh 가 도는 순간에야 처음 찍혔고, 그때 보니 토큰은 멀쩡히 present + 미만료였다.

토큰이 없어서가 아니었다. 첫 호출이 그 토큰을 읽으러 인증 토큰 provider 까지 **가지도 않은** 것이다.


## 결정적 단서: 순서가 거꾸로였다

호출 순서를 로깅해보니 답이 나왔다.

```
1. react router loader 가 인증 여부 확인 함수 호출
2. [Amplify.configure done]
3. 인증 실패 -> 인증 토큰 refresh
```

react-router의 loader가 `Amplify.configure` **보다 먼저** 실행되었다. Amplify 가 아직 설정 전이니, Amplify가 바라볼 가짜 인증 토큰이 없었다. 그래서 loader 의 인증 여부 확인이 실패했고, 그 결과로 인증 토큰이 만료됐는지 확인하는 코드가 실행됐다.

Amplify.configure 가 코드 위에 적혀 있는데 loader 가 어떻게 그보다 먼저 도는거야?


## 근본 원인: import-먼저-평가 + eager loader

두 가지가 겹쳤다.

**(a) ES 모듈은 import 가 본문보다 먼저, 소스 순서대로 깊이우선 평가된다.** `main.tsx` 의 `import App` 을 만나면 `App` → `routes.tsx` 가 import 단계에서 평가되고, 그 최상단의 `createBrowserRouter([...])` 가 그때 실행된다. `main.tsx` 본문의 `Amplify.configure` 보다 무조건 앞선다.

`ESM import = 그 파일을 한 번 "실행"해서, 그 결과(export)를 가져오는 것`이기 때문에 이런 현상이 발생한다.

**(b) 데이터 라우터는 생성 시점에 현재 URL 의 초기 라우트 loader 를 당장 실행한다. ** `createBrowserRouter` 는 `RouterProvider`의 마운트조차 기다리지 않는다. 그래서 라우터가 만들어지는 그 순간 loader가 인증 여부 검사를 한다.

타임라인으로 펴면 이렇다.

```
[import 단계]
  main.tsx: import App
    └ App: import { router } from routes.tsx
        └ routes.tsx 평가: createBrowserRouter([...]) 실행
            └ eager: authLoader → 첫 [인증 여부 확인 함수 실행]  ← (1)
                   await 양보
[본문 단계]
  main.tsx 본문: Amplify.configure(...)                 ← (2) 너무 늦음
[다시]
  (1) 의 인증 결과 = 빈 세션 → 인증 토큰 refresh
```

[인증 여부 확인 함수 실행] 의 `await` 가 양보하는 사이 `main.tsx` 본문이 이어져 Amplify.configure 가 실행된다. 첫 호출 시점엔 인증 없는 빈 세션, 그래서 강제 인증 토큰 refresh 의 흐름이다.


## 해결: 라우터를 함수로, 본문에서 순서를 명시

핵심은 **configure(엔트리 본문)가 라우터 생성(= eager loader)보다 먼저** 돌게 만드는 것이다. 라우터를 모듈 최상단 const 로 두는 한, 그 순서는 import 평가 순서라는 우연에 달려 있다. 그래서 라우터 생성을 const 가 아니라 **팩토리 함수**로 바꿔 본문으로 끌어내렸다.

### before

```tsx
// routes.tsx — 모듈 평가 시점에 라우터가 만들어짐(= eager loader 가 그때 돈다)
export const router = createBrowserRouter([{ path: '/', element: ..., loader: authLoader }, ...]);

// App.tsx
import { router } from '@/route/routes';

const App = () => <RouterProvider router={router} />;

// main.tsx — import App 이 본문(configure)보다 먼저 평가된다
import App from '@/App';

Amplify.configure(amplifyOutputs, { Auth: { tokenProvider: CustomAuthTokenProvider } });
render(<App />);
```

### after

라우터를 호출 전엔 아무것도 돌지 않는 함수로 export 한다.

```tsx
// routes.tsx
export const createAppRouter = () =>
  createBrowserRouter([
    {
      path: '/',
      element: withProviders(<Home />),
      loader: authLoader,
    },
    ...loginRoutes,
    ...copilotRoutes,
    ...researchCenterRoutes,
    ...myPageRoutes,
  ]);
```

`App` 은 router 를 prop 으로 받는다.

```tsx
// App.tsx
import { RouterProvider, type RouterProviderProps } from 'react-router-dom';

// 이렇게 하면 App.tsx 를 import 할 때, router 를 생성하지 않는다. 
const App = ({ router }: { router: RouterProviderProps['router'] }) => (
  <RouterProvider router={router} />
);

export default App;
```

`main.tsx` 본문에서 `Amplify.configure -> 라우터 생성 -> render` 순서를 손으로 박는다.

```tsx
// main.tsx
import App from '@/App';
import configureAmplify from '@/lib/configureAmplify.ts';
import { createAppRouter } from '@/route/routes.tsx';
import { initializeMocks } from '@/mocks';

import '@/index.css';

// Amplify 설정을 router 생성보다 "먼저" — createAppRouter() 가 만드는 데이터 라우터의
// 초기 loader가 [인증 토큰 확인 함수]를 실행하므로, 순서를 함수 본문에서 명시적으로 보장한다.
configureAmplify();

const router = createAppRouter();

const rootElement = document.getElementById('root');

if (!rootElement) throw new Error('Root element not found');

initializeMocks().then(() => {
  createRoot(rootElement).render(
    <StrictMode>
      <App router={router} />
    </StrictMode>,
  );
});
```

`configureAmplify` 자체는 그대로 `Amplify.configure` 를 감싼 함수다.

```tsx
// configureAmplify.ts
const configureAmplify = () => {
  Amplify.configure(amplifyOutputs as unknown as ResourcesConfig, {
    Auth: {
      tokenProvider: CustomAuthTokenProvider,
    },
  });
};

export default configureAmplify;
```

라우터 생성이 더 이상 import 평가에 묶이지 않고 본문의 한 줄이 됐다. `Amplify 초기화 -> createAppRouter` 가 **실행 순서로** 보장되니, import 가 재정렬되든 린트가 줄을 옮기든 안 깨진다.


## 검증

수정 후 첫 [인증 토큰 확인 함수] 가 가짜 인증 토큰을 바로 읽었고, 폴백 강제 인증 토큰 refresh 는 사라졌다. 왕복 한 번이 통째로 없어진 것이다.


## 교훈: eager-init 와 전역 설정의 경합

이 버그의 본질은 Amplify 도 react-router 도 아니다. 순서가 중요한데 언어가 그 순서를 보장해 주지 않는다면, 순서를 코드로 명시해야 한다는 더 일반적인 이야기다.

**모듈 로드 시점에 부수효과/eager 작업을 하는 무언가**(여기선 `createBrowserRouter` 의 eager loader)는, **엔트리 본문에서 도는 전역 초기화**(Amplify·Firebase·Sentry·analytics·i18n 의 configure)와 경합할 수 있다. configure 가 코드상 위에 있어도, import 평가가 본문보다 먼저 끝나기 때문에 "위에 적혔으니 먼저 돈다"는 직관이 깨진다.


## 1줄 요약

앱 초기화 순서를 명시적으로 작성하라.


EOD

20260611

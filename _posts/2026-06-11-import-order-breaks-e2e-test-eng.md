---
layout: single
title: e2e exposed an init order race
date: 2026-06-11 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://raw.githubusercontent.com/wingsuitist/ecmascript-logo/master/es-ecmascript-logo.png"
    image: "https://raw.githubusercontent.com/wingsuitist/ecmascript-logo/master/es-ecmascript-logo.png"
categories:
- IT
tags: [javascript]
---

## Tech stack

- Amplify v6 Gen 2
- react-router v6
- react v19


## Observed behavior

Every time the app started up, it refreshed the Amplify Cognito auth token — even though the token hadn't expired. Same on local, same on production. It nagged at me, but since it caused no actual problem in using the service, I left it alone.

The real trouble showed up in e2e tests. When the e2e tests were configured to fetch real tokens from the actual Cognito server, there was no problem. But the e2e tests were so painfully slow that I switched the setup to mock auth with fake tokens — and that's when it broke. Most e2e tests needed a Cognito auth token, so I mocked the token: I pre-seeded a fake token into the localStorage that Amplify Cognito reads from in the e2e environment. With that, the tests shouldn't fail because auth couldn't be established — yet, astonishingly, auth failed. It was an infuriating situation.

The time had finally come to fix the bizarre Amplify Cognito behavior I'd been turning a blind eye to.


## The symptom

First I investigated why the app refreshed the auth token on every launch. It turned out the loader I'd attached to `react-router` was the culprit. I had the loader check whether the user was authenticated; when that check failed, the code that checks whether the token had expired would run. The problem: even though a valid token existed — so there was no reason for auth to fail — it was still taking the failure path.


## Boot structure

So I looked at the app's initialization code.

The boot looked simple. `main.tsx` is the entry; `import App from '@/App'` → `App` imports `routes.tsx`. `routes.tsx` does `export const router = createBrowserRouter([...])` at the **module top level**. Then `main.tsx` calls `Amplify.configure(...)` in its **body**, and renders. Reading it, nothing looks wrong — `Amplify.configure` sits above `render` in the source.


## A red herring: "Is the token missing or expired?"

Naturally I suspected the token first. Did I put the token in wrong? Is it expired? Did Amplify Cognito parse it wrong? To inspect the token I dropped a log into the `getTokens` of the Amplify Cognito auth provider. But at the moment of the first `fetchAuthSession` (the auth-check code), `getTokens` **wasn't even called.** It first showed up only when the forced refresh kicked in — and at that point the token was present and unexpired.

It wasn't that the token was missing. The first call never even **reached** the token provider to read it.


## The decisive clue: the order was reversed

Logging the call order gave the answer.

```
1. react-router loader calls the auth-check function
2. [Amplify.configure done]
3. auth fails -> token refresh
```

The react-router loader ran **before** `Amplify.configure`. Since Amplify wasn't configured yet, there was no fake auth token for Amplify to read, so the loader's auth check failed — and that triggered the code that checks whether the token had expired.

`Amplify.configure` is written higher up in the source — so how does the loader run before it?


## Root cause: imports-evaluated-first + eager loader

Two things overlapped.

**(a) ES modules evaluate imports before the body, in source order, depth-first.** When `main.tsx` hits `import App`, `App` → `routes.tsx` is evaluated during the import phase, and the top-level `createBrowserRouter([...])` runs right then — strictly before `main.tsx`'s body runs `Amplify.configure`.

This is the key mental model: an ESM import runs the module once, then consumes its exports.

**(b) The data router runs the current URL's initial route loader the moment it's created.** `createBrowserRouter` doesn't even wait for `RouterProvider` to mount. So the instant the router is created, the loader does its auth check.

Laid out as a timeline:

```
[import phase]
  main.tsx: import App
    └ App: import { router } from routes.tsx
        └ routes.tsx evaluated: createBrowserRouter([...]) runs
            └ eager: authLoader → first [auth-check function runs]  ← (1)
                   await yields
[body phase]
  main.tsx body: Amplify.configure(...)                 ← (2) too late
[back again]
  result of (1) = empty session → token refresh
```

While the `await` inside [auth-check function] yields, `main.tsx`'s body continues and runs `Amplify.configure`. At the first call there is no auth — an empty session — so it goes down the forced-token-refresh path.


## The fix: make the router a function, pin the order in the body

The key is to make **`configure` (entry body) run before router creation (= the eager loader)**. As long as the router is a module-top-level const, that order is left to the accident of import-evaluation order. So I turned router creation from a const into a **factory function** and pulled it down into the body.

### before

```tsx
// routes.tsx — the router is created at module evaluation time (= the eager loader runs then)
export const router = createBrowserRouter([{ path: '/', element: ..., loader: authLoader }, ...]);

// App.tsx
import { router } from '@/route/routes';

const App = () => <RouterProvider router={router} />;

// main.tsx — `import App` is evaluated before the body (configure)
import App from '@/App';

Amplify.configure(amplifyOutputs, { Auth: { tokenProvider: CustomAuthTokenProvider } });
render(<App />);
```

### after

Export the router as a function, so nothing runs until it's called.

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

`App` receives the router as a prop.

```tsx
// App.tsx
import { RouterProvider, type RouterProviderProps } from 'react-router-dom';

// This way, importing App.tsx does not create the router.
const App = ({ router }: { router: RouterProviderProps['router'] }) => (
  <RouterProvider router={router} />
);

export default App;
```

In `main.tsx`'s body, explicitly enforce the `Amplify.configure -> create router -> render` order.

```tsx
// main.tsx
import App from '@/App';
import configureAmplify from '@/lib/configureAmplify.ts';
import { createAppRouter } from '@/route/routes.tsx';
import { initializeMocks } from '@/mocks';

import '@/index.css';

// Configure Amplify BEFORE creating the router — the data router that createAppRouter()
// builds runs its initial loader (which calls the auth-check function), so the order is
// guaranteed explicitly in the body.
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

`configureAmplify` itself is just a wrapper around `Amplify.configure`.

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

Router creation is no longer tied to import evaluation — it's a single line in the body. Because `Amplify init -> createAppRouter` is guaranteed **by execution order**, it won't break even if the imports get reordered or a linter moves lines around.


## Verification

After the fix, the first [auth-check function] read the fake token right away, and the fallback forced token refresh was gone. A whole round-trip vanished.


## Lesson: the race between eager-init and global setup

The essence of this bug is neither Amplify nor react-router. It's the more general point that when order matters but the language doesn't guarantee that order, you have to make the order explicit in code.

**Something that performs side effects / eager work at module-load time** (here, `createBrowserRouter`'s eager loader) can race with **a global initialization that runs in the entry body** (the `configure` of Amplify·Firebase·Sentry·analytics·i18n). Even if `configure` sits higher in the source, import evaluation finishes before the body — so the intuition "it's written above, so it runs first" breaks.


## One-line summary

Write your app's initialization order explicitly.


*2026-06-11*

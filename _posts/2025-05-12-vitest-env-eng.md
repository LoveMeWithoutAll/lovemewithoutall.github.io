---
layout: single
title: "Want to Use .env Environment Variables in a Vitest Environment Too?"
date: 2025-05-12 10:49:30.000000000 +09:00
type: post
header:
    teaser: "https://vitest.dev/logo-shadow.svg"
    image: "https://vitest.dev/logo-shadow.svg"
categories:
- IT
tags: [vitest]
---

# Want to Use .env Environment Variables in a Vitest Environment Too?

## Environment
- vitest 3

Have you ever been puzzled when a value like import.meta.env.VITE_API_URL kept printing as undefined every time you ran your tests?
Those .env values that come through just fine in a production build or in vite dev disappear the moment you run Vitest. Here's why that happens, and how to fix it with just a single line.

## 1. Why is env empty in Vitest?

Vite reads the .env.* files and statically replaces values according to the following rules, based on the mode (--mode).

[A Mental Model for Vite Environment Variable Priority
](https://lovemewithoutall.github.io/it/vite-env-mental-model/)

Unlike webpack-era process.env, these values are replaced with strings at build time rather than at runtime. So if the mode specified when running the tests is different, the file you want won't be loaded.

### In other words, you have to run your tests in the same mode as the actual service for import.meta.env to be populated.


## 2. The Solution: vitest run --mode [mode]

For convenience, I set the mode to `local-proxy`.

```bash
# CLI
# mode = local-proxy
vitest run --mode local-proxy        # one-off run
vitest watch --mode local-proxy      # watch mode
```

Simple, but powerful.

When you pass `--mode local-proxy`, Vite/Vitest reads `.env.local-proxy` (and .env.local-proxy.local) and statically replaces all of the import.meta.env.* constants.

### Lock it in as a script in package.json

```json
{
  "scripts": {
    "test": "vitest run --mode local-proxy",
    "test:watch": "vitest watch --mode local-proxy"
  }
}
```

If you want to test with a different mode (such as production) in CI, you can write an additional script or override it with an inline argument like npm run test -- --mode production.

## 3. A Real-World Example

### 1) .env.local-proxy

```
VITE_HOME_URL=/
```

By default, Vite only exposes entries with the VITE_ prefix to client code.
For server-only variables (values used only through process.env), remove the prefix or manage them in some other way.

### 2) Logo.tsx

```tsx
const Logo = () => (
  <a className="c-icon c-icon--11st" href={import.meta.env.VITE_HOME_URL}>
    11번가
  </a>
);
```

### 3) Running Vitest

```bash
yarn test        # if you added the script above to package.json
# internally runs vitest run --mode local-proxy
```

The DOM that gets rendered:

```html
<a class="c-icon c-icon--11st" href="/">11번가</a>
```


Test code:

```ts
import { render, screen } from '@testing-library/react';

it('injects the link href correctly', () => {
  render(<Logo />);
  expect(screen.getByRole('link', { name: /11번가/ })).toHaveAttribute('href', '/');
});
```

PASS ✅

Since href is no longer undefined or #, the accessibility checks pass as well.

## 4. Frequently Asked Questions (FAQ)

### Q1. Can't I just use .env.test?

A. You can. However, instead of changing the mode, you'd have to specify the path with the envDir option or inject it directly with loadEnv(). For a simple project, the single line of --mode is far more convenient.

### Q2. Do I need --mode even if I only use process.env?

A. No, you don't. Since it's not subject to build-time replacement, you can load it at runtime with dotenv.config(). But if you need to expose it to client code (React components), you must use import.meta.env.

### Q3. What if I changed envPrefix to a custom value?

A. If you changed envPrefix in vite.config.ts, your tests need to be configured the same way. Otherwise the variable names will differ and the replacement will fail.

-------

## 5. Wrapping Up

### Summary
	1.	Vite/Vitest statically replaces .env.* files according to the mode.
	2.	You need to specify the same mode in your tests for import.meta.env to be populated.
	3.	vitest run --mode local-proxy — just that one line and you're done!

Just matching the mode solves most of the problems!

GPT wrote more than half of this.

EOD

20250512

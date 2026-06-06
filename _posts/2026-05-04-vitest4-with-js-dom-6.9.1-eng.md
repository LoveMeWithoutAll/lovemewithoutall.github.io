---
layout: single
title: "A Trial-and-Error Tale of Wiring vitest 4 with js-dom 6.9.1 on Node 25"
date: 2026-05-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/f/f1/Vitejs-logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/f/f1/Vitejs-logo.svg"
categories:
- IT
tags: [vite]
---

# A Trial-and-Error Tale of Wiring vitest 4 with js-dom 6.9.1 on Node 25

I asked an AI to set up the initial vitest environment, and it produced a bizarre config file. It seemed like all I needed to do was import `jest-dom` once in the `vitest.config.ts` file, so why did it write code like this?

## How the Trial-and-Error Began

When I looked at the `vitest.config.ts` the AI wrote, it had four elements.

```ts
// vitest.config.ts

// 1) register jest-dom matchers
import * as matchers from '@testing-library/jest-dom/matchers';
import { expect } from 'vitest';
expect.extend(matchers);

// 2) type augmentation
import type {} from '@testing-library/jest-dom/vitest';

// 3) localStorage polyfill (a 20-line Map-backed Storage implementation)
// especially crappy code
if (typeof globalThis.localStorage?.getItem !== 'function') { ... }
```

Why should I have to manually implement something the js-dom library is supposed to handle internally? Is the localStorage polyfill really necessary? Configuration should always be as simple as possible.

So I deleted everything. And immediately, an error popped up.

## The Symptom — `localStorage.clear is not a function`

I trimmed `vitest.config.ts` down to a single line:

```ts
import '@testing-library/jest-dom/vitest';
```

The result of `yarn test:run`:

```
FAIL src/features/Auth/lib/tests/cognito.test.ts
  TypeError: localStorage.clear is not a function
    src/features/Auth/lib/tests/cognito.test.ts:24:18
      22| describe('renewAccessTokens', () => {
      23|   beforeEach(() => {
      24|     localStorage.clear();
                            ^
```

At the same time, the following warning appeared:

```
Warning: `--localstorage-file` was provided without a valid path
```

localStorage doesn't have a `clear` method? Wasn't js-dom supposed to create it?

### Investigation 1 — Checking the vitest config

`vitest.config.ts` already had `environment: 'jsdom'` set. In a jsdom environment, localStorage should be provided normally. But it failed. In other words, jsdom's normal initialization was being *interfered with*.

### Investigation 2 — Checking Node's own behavior

So could it be that Node itself was jamming some localStorage into the global scope? There had been rumors that the Web Storage API was enabled by default starting from Node 25, and that could be the cause.

```bash
$ node --version
v25.9.0

$ node -e 'console.log(typeof localStorage, typeof localStorage?.clear)'
object undefined
(node:...) Warning: `--localstorage-file` was provided without a valid path
```

The same symptom and the same warning occurred **with Node alone**, not in a test environment. The problem was that `localStorage` existed as an object but had no `clear`.
Now it was clear that js-dom was not the problem. The problem was `Node25`. Node started creating localStorage but never finished.

What on earth is Node 25 doing?

### Investigation 3 — Node's Web Storage CLI option

If Node 25 is doing something, there must be a CLI option for it. I searched through `node --help` and found it.

```bash
$ node --help | grep -i storage
  --experimental-storage-inspection
  --localstorage-file=...     file used to persist localStorage data
  --webstorage, --no-experimental-webstorage
                              experimental Web Storage API
```

To summarize the help search results: **`--webstorage`** — Node 25 has an `experimental Web Storage`, and it can be disabled with `--no-webstorage`.

I'm not sure why an experimental feature is on by default, but apparently that's how it is.

### Investigation 4 — Verifying the effect of `--no-webstorage`

I tried disabling the experimental Web Storage.

```bash
$ node --no-webstorage -e 'console.log(typeof localStorage)'
ReferenceError: localStorage is not defined

$ NODE_OPTIONS=--no-webstorage yarn test:run cognito.test.ts
Tests  6 passed (6)
```

When Node's Web Storage is disabled, `localStorage` itself disappears, and jsdom initializes it normally(?) so the tests pass.

### Investigation 5 — Verifying directly on Node 24

Node 25's behavior was so absurd that I dug a little deeper, and apparently Node 24 does not enable the experimental Web Storage. So I ran the same code on Node 24 as well.

```bash
$ /Users/.../v24.15.0/bin/node -e 'console.log(typeof localStorage)'
ReferenceError: localStorage is not defined

# Node 24 + without polyfill
$ PATH=.../v24.15.0/bin:$PATH yarn test:run
Test Files  11 passed (11)
Tests       68 passed (68)
```

On Node 24, `--experimental-webstorage` is *opt-in* (OFF by default), so `localStorage` is initialized as undefined at startup. As a result, jsdom creates localStorage normally. The polyfill is unnecessary, because jsDom itself is a kind of polyfill.

## Summary

1. Node 25 promoted Web Storage to *enabled by default* (it was opt-in in Node 22).
2. If you don't add `--no-webstorage` to Node 25's runtime options, a `globalThis.localStorage` missing methods like `clear` and `getItem` is created, plus a warning.
3. The vitest worker's jsdom env tries to create `window.localStorage`.
4. jsdom checks that `globalThis.localStorage` already exists and does not inject a polyfill. But `globalThis.localStorage` cannot run all of `window.localStorage`'s methods.
5. The test runs `globalThis.localStorage.clear()` → since there's no `clear` method, it tries to execute undefined → TypeError occurs.

The root cause is that **Node creates an incomplete Web Storage, which conflicts with jsDom's default assumption (if there's no localStorage, let's create one)**. In Node 25, that conflict became the *default*.

## So What's My Choice?

Let's use Node 24.


| Option                                                     | Adopt? | Reason                                     |
|--------------------------------------------------------|---|----------------------------------------|
| Add a localStorage polyfill to `vitest.config.ts`              | ✗ | The code is messy and requires explanation                    |
| Keep only `engines: ">=24 <25"` in package.json + `.nvmrc=24` | ✓ | Node 25 users violate the spec — explicit failure is the best feedback |

Since the original polyfill in vitest.config.ts existed *for Node 25 users*, using version 24 makes the polyfill unnecessary.



## Aside... the compatibility problem between vitest v4 and js-dom

If you don't add the code below, you can't load jest-dom's types. This is because js-dom can't connect to vitest 4.

```typescript
import type {} from '@testing-library/jest-dom/vitest';
```

An error also occurs in vitest's expect. This is because the expect method isn't connected to jsDom's rejects.toThrow. So you have to forcibly extend vitest's expect as shown below. Before vitest v4, this was code that js-dom ran on its own.

```typescript
import * as matchers from '@testing-library/jest-dom/matchers';
import { expect } from 'vitest';

expect.extend(matchers);
```


## The Finished Version

Inevitably, I had to leave a giant comment.

```typescript
// issue #1. To work around the problem that jest-dom 6.9.1 doesn't support vitest 4,
// directly register vitest's expect so it recognizes js-dom's matchers.
// If you don't register them this way, expect.rejects.toThrow breaks.
// Once jest-dom starts supporting vitest4,
// replace this with `import '@testing-library/jest-dom/vitest'` (the proper way)
import * as matchers from '@testing-library/jest-dom/matchers';
import { expect } from 'vitest';

expect.extend(matchers);

// issue #2. Load the types so all of jest-dom's methods can be used in vitest
import type {} from '@testing-library/jest-dom/vitest';
```

If problems like this keep happening, I think I'll have to ditch vitest 4.

## References

- jest-dom 6.9.1 released: 2025-10-01 (stalled since).
- Vitest 4.0.0 released: 2025-10-22.
- Node 25's `--webstorage` being enabled by default is the result of it being promoted from opt-in to default over the course of Node 22 → 25. It can be disabled with `--no-webstorage`.

EOD

20260504

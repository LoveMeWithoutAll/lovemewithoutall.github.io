---
layout: single
title: Node 25에서 vitest 4와 js-dom 6.9.1 연결하는 삽질기
date: 2026-05-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/f/f1/Vitejs-logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/f/f1/Vitejs-logo.svg"
categories:
- IT
tags: [vite]
---

# Node 25에서 vitest 4와 js-dom 6.9.1 연결하는 삽질기

AI에게 vitest 초기 환경 설정을 맡겼더니 괴상한 설정 파일을 만들었다. 그냥 `vitest.config.ts` 파일에 `jest-dom`을 import 한 번 하면 될 것 같은데, 왜 이런 코드를 짰을까? 

## 삽질의 시작

AI가 짠 `vitest.config.ts`를 확인해 보니,  4 가지 요소를 들고 있었다.

```ts
// vitest.config.ts

// 1) jest-dom matcher 등록
import * as matchers from '@testing-library/jest-dom/matchers';
import { expect } from 'vitest';
expect.extend(matchers);

// 2) 타입 augmentation
import type {} from '@testing-library/jest-dom/vitest';

// 3) localStorage 폴리필 (20 줄 짜리 Map-backed Storage 구현체)
// 특히 거지 같은 코드
if (typeof globalThis.localStorage?.getItem !== 'function') { ... }
```

js-dom 라이브러리가 내부적으로 해야할 일을 왜 직접 구현해야 할까? localStorage 폴리필도 정말로 필요한 것일까? 설정은 항상 가장 단순해야 한다.

그래서 다 지워버렸다. 그러자 즉시 오류가 튀어나왔다.

## 현상 — `localStorage.clear is not a function`

`vitest.config.ts` 을 다음과 같이 한 줄로 줄였다:

```ts
import '@testing-library/jest-dom/vitest';
```

`yarn test:run` 결과:

```
FAIL src/features/Auth/lib/tests/cognito.test.ts
  TypeError: localStorage.clear is not a function
    src/features/Auth/lib/tests/cognito.test.ts:24:18
      22| describe('renewAccessTokens', () => {
      23|   beforeEach(() => {
      24|     localStorage.clear();
                            ^
```

동시에 다음 경고가 발생:

```
Warning: `--localstorage-file` was provided without a valid path
```

localStorage에 clear 메서드가 없다고? js-dom이 만들어 주는 거 아니었나?

### 추적 1 — vitest config 확인

`vitest.config.ts` 는 이미 `environment: 'jsdom'` 이 설정돼 있었다. jsdom 환경이면 localStorage 가 정상 제공되어야 한다. 그러나 실패. 즉 jsdom 의 정상적인 초기화가 *방해 받고* 있었다.

### 추적 2 — Node 자체 동작 확인

그러면 Node 자체가 뭔가 localStorage 를 글로벌에 박고 있는 건 아닐까? Node 25 부터 Web Storage API 가 기본 활성화됐다는 소문이 있었는데, 그게 원인일 수 있다.

```bash
$ node --version
v25.9.0

$ node -e 'console.log(typeof localStorage, typeof localStorage?.clear)'
object undefined
(node:...) Warning: `--localstorage-file` was provided without a valid path
```

테스트 환경이 아닌 **Node 단독으로도** 같은 증상과 같은 경고가 발생했다. `localStorage` 가 객체로 존재하지만 `clear` 가 없는 것이 문제였다.
이제 js-dom이 문제가 아니라는 사실이 명확해졌다. 문제는 `Node25`이다. Node가 localStorage를 만들다 말았다.

Node25는 대체 무슨 짓을 하고 있는 것일까?

### 추적 3 — Node 의 Web Storage CLI 옵션

Node25가 뭔가 하고 있다면, CLI 옵션이 있을 것이다. `node --help` 를 검색해 보니 있었다.

```bash
$ node --help | grep -i storage
  --experimental-storage-inspection
  --localstorage-file=...     file used to persist localStorage data
  --webstorage, --no-experimental-webstorage
                              experimental Web Storage API
```

help 검색 결과를 요약하자면, **`--webstorage`** — Node 25 의 `experimental Web Storage`가 있고, `--no-webstorage` 로 비활성 가능하다고 한다.

experimental 기능인데 왜 default로 떠 있는지는 모르겠지만, 그렇다고 한다.

### 추적 4 — `--no-webstorage` 효과 확인

experimental Web Storage를 비활성화 해 보았다.

```bash
$ node --no-webstorage -e 'console.log(typeof localStorage)'
ReferenceError: localStorage is not defined

$ NODE_OPTIONS=--no-webstorage yarn test:run cognito.test.ts
Tests  6 passed (6)
```

Node 의 Web Storage 가 비활성되면 `localStorage` 자체가 없어지고, jsdom 이 정상적(?)으로 초기화 하여 테스트를 통과한다.

### 추적 5 — Node 24 에서 직접 확인

Node25가 하는 짓이 너무나 황당하여 좀 더 조사해 보니, Node 24는 experimental Web Storage를 활성화 하지 않는다고 한다. 그래서 Node 24 에서도 같은 코드를 돌려 보았다.

```bash
$ /Users/.../v24.15.0/bin/node -e 'console.log(typeof localStorage)'
ReferenceError: localStorage is not defined

# Node 24 + 폴리필 없이
$ PATH=.../v24.15.0/bin:$PATH yarn test:run
Test Files  11 passed (11)
Tests       68 passed (68)
```

Node 24 에서는 `--experimental-webstorage` 가 *opt-in* (기본 OFF) 이라 `localStorage` 가 시작 시 undefined로 초기화 된다. 그래서 jsdom 이 localStorage를 정상적으로 만들어준다. 폴리필은 불필요하다. jsDom 자체가 일종의 폴리필이니까.

## 정리

1. Node 25 가 Web Storage 를 *기본 활성* 으로 승격 (Node 22 에선 opt-in 이었음).
2. Node 25 실행 옵션에 `--no-webstorage` 를 안 넣으면 `clear`, `getItem` 등의 메서드가 빠진 `globalThis.localStorage`가 만들어짐 + warning.
3. Vitest worker 의 jsdom env 가 `window.localStorage` 생성 시도.
4. jsdom 은 `globalThis.localStorage` 가 이미 존재함을 확인하고, 폴리필을 넣지 않음. 하지만 `globalThis.localStorage`은 `window.localStorage` 의 모든 메서드를 실행할 수 없다.
5. 테스트가 `globalThis.localStorage.clear()` 실행 → clear 메서드가 없기 때문에 undefined 를 실행하려고 함 → TypeError 발생.

근본 원인은 **Node가  불완전한 Web Storage 를 만들어서 jsDom의 기본 가정(localStorage가 없으면 localStorage를 만들자)과 충돌** 하는 것이다. Node 25 에서 그 충돌이 *기본값* 이 됐다.

## 나의 선택은?

Node 24를 쓰자.


| 옵션                                                     | 채택? | 이유                                     |
|--------------------------------------------------------|---|----------------------------------------|
| localStorage 폴리필을 `vitest.config.ts` 에 추가              | ✗ | 코드는 너저분하고 설명이 필요해진다                    |
| package.json에 `engines: ">=24 <25"` + `.nvmrc=24` 만 유지 | ✓ | Node 25 사용자는 명세 위반 — 명시적 실패가 가장 좋은 피드백 |

원래 vitest.config.ts 의 폴리필은 *Node 25 사용자를 위해* 있었던 것이기 때문에, 24 버전을 쓰면 폴리필은 불필요하다



## 그 외... vitests v4 와 js-dom의 호환 문제

아래 코드를 넣어주지 않으면 jest-dom의 type을 불러올 수 없다. vitest 4를 js-dom이 연결하지 못 하기 때문이다.

```typescript
import type {} from '@testing-library/jest-dom/vitest';
```

vitest의 expect에서도 오류가 발생한다. expect 메서드가 jsDom의 rejects.toThrow와 연결되지 않기 때문이다. 그래서 아래와 같이 vitest의 expect를 강제로 확장해줘야 한다. vitest v4 이전에는 js-dom이 알아서 실행하는 코드였다.

```typescript
import * as matchers from '@testing-library/jest-dom/matchers';
import { expect } from 'vitest';

expect.extend(matchers);
```


## 완성본

불가피하게 거대한 주석을 남겨야 했다.

```typescript
// issue #1. jest-dom 6.9.1이 vitest 4를 지원하지 않는 문제를 해결하기 위해
// vitest의 expect 가 js-dom의 matcher를 인식하도록 직접 등록한다.
// 이렇게 등록하지 않으면 expect.rejects.toThrow 가 깨진다
// jest-dom이 vitest4를 지원하기 시작하면
// `import '@testing-library/jest-dom/vitest'` 로 교체하라(정석)
import * as matchers from '@testing-library/jest-dom/matchers';
import { expect } from 'vitest';

expect.extend(matchers);

// issue #2. jest-dom의 모든 메서드를 vitest에서 사용할 수 있도록 타입을 불러온다
import type {} from '@testing-library/jest-dom/vitest';
```

아무래도 이런 문제가 계속 발생하면 vitest 4를 버려야 할 것 같다.

## 참고

- jest-dom 6.9.1 발행: 2025-10-01 (이후 정체).
- Vitest 4.0.0 발행: 2025-10-22.
- Node 25 의 `--webstorage` 기본 활성화는 Node 22 → 25 동안 opt-in → default 로 승격된 결과. `--no-webstorage` 로 비활성 가능.

EOD

20260504

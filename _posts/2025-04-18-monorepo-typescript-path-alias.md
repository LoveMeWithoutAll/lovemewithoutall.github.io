---
layout: single
title: 모노레포에서 패키지마다 Path Alias를 두는 가장 안전한 방법
date: 2025-04-18 10:49:30.000000000 +09:00
type: post
header:
    teaser: "http://image.yes24.com/Goods/90265564/L"
    image: "http://image.yes24.com/Goods/90265564/L"
categories:
- IT
tags: [typescript]
---

# 모노레포에서 패키지마다 Path Alias를 두는 가장 안전한 방법

## 2줄 요약
- 서로 다른 패키지가 동일한 별칭(@ 등)을 재사용하면 반드시 충돌한다.
- tsconfig.json의 paths 설정은 해당 파일이 포함된 컴파일 단계에만 적용된다. 패키지 경계를 넘어 자동 전파되지 않는다.

## 환경
| Tool | Version |
|------|---------|
| TypeScript | 5.x |
| Yarn (Berry, PnP) | 4.x |
| Vite | 6.x |
| Vitest | 3.x |


## 문제 정의

yarn workspace를 사용하여 패키지 간 import를 하고 싶다면, package.json에 설정한 name을 dependencies에 추가하여 해결할 수 있다.

```typescript
// 예시
import { redirectToLogin } from '@mso/shared/src';
```

하지만 정작 패키지 내부의 모듈을 import 하려면 끝없는 상대 경로 지옥이 기다린다.

```typescript
// 예시
import { isInApp } from '../../../utils/DeviceUtils.ts';
```

- 디렉터리를 옮길 때마다 경로가 깨지고,
- IDE 지원이 없으면 생산성이 급락한다.



그래서 각 패키지의 tsconfig.json에 별칭을 잡아주지만, 이 설정은 다른 패키지로 전파되지 않는다. 각 패키지의 tsconfig.json에서 path alias를 설정하여 패키지 내부의 파일을 import 하면, 해당 패키지를 다른 패키지에서 사용할 경우 오류가 발생한다.

```tsx
// 예시
src/Header.tsx:1:18 - error TS2307: Cannot find module '@header/src/Logo.tsx' or its corresponding type declarations.

1 import Logo from '@header/src/Logo.tsx';
                   ~~~~~~~~~~~~~~~~~~~~~~


Found 1 error in src/Header.tsx:1
```

각 패키지의 tsconfig.json은 그 패키지 자체로 완결되는 것이지, 다른 패키지에까지 전달되지 않기 때문이다.

그러니 root workspace의 tsconfig에 path alias를 지정하되, 각 패키지마다 다른 path alias를 지정해야 한다.

## 해결 전략

### 핵심 규칙

1. 루트 tsconfig에 모든 패키지의 별칭을 선언한다.
1. 각 패키지는 반드시 루트 설정을 extends 한다.
1. 빌드 툴(Vite, Vitest 등)에도 별칭을 알려준다.

### 1. root workspace의 tsconfig에 path 설정

tsconfig.base.json (혹은 tsconfig.json)을 모노레포 최상단에 둔다.

```json
// tsconfig.base.json
{
    // ...
    "compilerOptions": {
    // ...
        "baseUrl": "./",
        "paths": {
          "@header/*": ["packages/header/*"],
          "@home/*": ["apps/home/*"],
          // ...
        }
    }
}
```

### 2. 각 workspace에서 root workspace의 tsconfig를 extend

```jsonc
// packages/header/tsconfig.json
{
  "extends": "../../tsconfig.base.json",
   // ...
}
```

여기까지만 해도 tsc의 타입 검사는 통과한다. 빌드도 잘 된다.

```ts
import Logo from './Logo.tsx'; // before
import Logo from '@header/src/Logo'; // After
```

### 3. vite 설정

하지만 vite 로컬 서버는 다음과 같은 오류가 발생하며 실행되지 않는다. Vite는 TypeScript 설정을 자동으로 읽지 않는다. 따라서 여전히 다른 패키지를 `@header`로 불러오지 못 한다.

```bash
Error: The following dependencies are imported but could not be resolved:

  @header/src/Logo.tsx (imported by /Users/a1101586/dev/11st/mso-poc/packages/header/src/Header.tsx)

Are they installed?
    at file:///Users/a1101586/dev/11st/mso-poc/.yarn/__virtual__/vite-virtual-eb503654a3/0/cache/vite-npm-6.3.0-45709bed3a-5ba5ed3f87.zip/node_modules/vite/dist/node/chunks/dep-BuM4AdeL.js:14837:15
    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)
    at async file:///Users/a1101586/dev/11st/mso-poc/.yarn/__virtual__/vite-virtual-eb503654a3/0/cache/vite-npm-6.3.0-45709bed3a-5ba5ed3f87.zip/node_modules/vite/dist/node/chunks/dep-BuM4AdeL.js:46889:28
8:29:14 AM [vite] (client) Pre-transform error: Failed to resolve import "@header/src/Logo.tsx" from "../../packages/header/src/Header.tsx". Does the file exist?
  Plugin: vite:import-analysis
  File: /Users/a1101586/dev/11st/mso-poc/packages/header/src/Header.tsx:1:39
  16 |  }
  17 |  var _s = $RefreshSig$();
  18 |  import Logo from "@header/src/Logo.tsx";
     |                    ^
  19 |  import { redirectToLogin } from "@mso/shared/src";
  20 |  import useAuthorization from "@mso/shared/src/utils/hook/useAuthorization.ts";
8:29:14 AM [vite] Internal server error: Failed to resolve import "@header/src/Logo.tsx" from "../../packages/header/src/Header.tsx". Does the file exist?

```

여러 방법이 있지만 가장 간단한 방법을 소개한다.

각 패키지의 `vite.config.ts` 파일에 `vite-tsconfig-paths plugin`을 추가한다.

```ts
// packages/header/vite.config.ts

import { defineConfig } from 'vite';
import tsconfigPaths from 'vite-tsconfig-paths';

export default defineConfig({
    plugins: [
        // ...
        tsconfigPaths(),
    ]
})
```

이제 vite가 tsconfig.json에 설정된 path를 그대로 가져다 쓸 수 있다.

### 4. vitest 설정

vitest도 vite처럼 path alias를 설정해야 한다. 그렇지 않으면 아래처럼 테스트가 실패한다.

```
// test fail log
⎯⎯⎯⎯⎯⎯ Failed Suites 1 ⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/test/Header.test.tsx [ src/test/Header.test.tsx ]
Error: Failed to resolve import "@header/src/Logo.tsx" from "src/Header.tsx". Does the file exist?
  Plugin: vite:import-analysis
  File: /Users/a1101586/dev/11st/mso-poc/packages/header/src/Header.tsx:1:17
  1  |  import { Fragment, jsxDEV } from "react/jsx-dev-runtime";
  2  |  import Logo from "@header/src/Logo.tsx";
     |                    ^
  3  |  import { redirectToLogin } from "@mso/shared/src";
  4  |  import useAuthorization from "@mso/shared/src/utils/hook/useAuthorization.ts";
 ❯ TransformPluginContext._formatLog ../../.yarn/__virtual__/vite-virtual-79bbc04783/0/cache/vite-npm-6.2.5-813f4c9986-3540235894.zip/node_modules/vite/dist/node/chunks/dep-Pj_jxEzN.js:47885:41
 ❯ TransformPluginContext.error ../../.yarn/__virtual__/vite-virtual-79bbc04783/0/cache/vite-npm-6.2.5-813f4c9986-3540235894.zip/node_modules/vite/dist/node/chunks/dep-Pj_jxEzN.js:47882:16
 ❯ normalizeUrl ../../.yarn/__virtual__/vite-virtual-79bbc04783/0/cache/vite-npm-6.2.5-813f4c9986-3540235894.zip/node_modules/vite/dist/node/chunks/dep-Pj_jxEzN.js:46015:23
 ❯ ../../.yarn/__virtual__/vite-virtual-79bbc04783/0/cache/vite-npm-6.2.5-813f4c9986-3540235894.zip/node_modules/vite/dist/node/chunks/dep-Pj_jxEzN.js:46134:37
 ❯ TransformPluginContext.transform ../../.yarn/__virtual__/vite-virtual-79bbc04783/0/cache/vite-npm-6.2.5-813f4c9986-3540235894.zip/node_modules/vite/dist/node/chunks/dep-Pj_jxEzN.js:46061:7
 ❯ EnvironmentPluginContainer.transform ../../.yarn/__virtual__/vite-virtual-79bbc04783/0/cache/vite-npm-6.2.5-813f4c9986-3540235894.zip/node_modules/vite/dist/node/chunks/dep-Pj_jxEzN.js:47680:18
 ❯ loadAndTransform ../../.yarn/__virtual__/vite-virtual-79bbc04783/0/cache/vite-npm-6.2.5-813f4c9986-3540235894.zip/node_modules/vite/dist/node/chunks/dep-Pj_jxEzN.js:41327:27

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯


 Test Files  1 failed
```

vitest 설정 방법은 vite와 동일하다. vitests.config.ts 파일을 수정하여 plugin에 'vite-tsconfig-paths'를 추가하자.

```json
// vitests.config.ts

// ...
import tsconfigPaths from 'vite-tsconfig-paths';
import { defineConfig } from 'vitest/config';

export default defineConfig({
  plugins: [
    // ...
    tsconfigPaths()  // 추가
    ],
  // ...
});

```

## 주의

1. @ ?

Q. 모든 패키지에서 monorepo를 사용하지 않는다면 자주 그랬던 것처럼 똑같이 '@' 한 글자로만 쓰면 안 되나?

A. 서로 다른 물리 경로가 동일한 alias에 매팽되면 충돌한다. 패키지 단위로 고유한 prefix를 두자.

2. 귀찮아?

Q. 별칭 대신 그냥 상대 경로를 쓰면 안될까?

A. 가능은 하지만 유지보수 비용이 폭증한다.

3. lint는?

Q. ESLint가 경로를 못 찾는다.

A. eslint-import-resolver-typescript를 설치하고 settings.import.reslover.typescript.project에 root tsconfig 경로를 지정하라. 하지만 그냥 biome를 쓰면 이런 설정도 필요없다.

## 마무리

1.	루트에 모든 별칭을 모은다.
2.	모든 패키지가 그 설정을 extends 한다.
3.	Vite(혹은 다른 빌드 툴)에도 별칭을 알려준다.

monorepo에서 상대 경로 지옥을 잘 피해보자.

EOD

20250418

---
layout: single
title: "The Safest Way to Set Up a Path Alias per Package in a Monorepo"
date: 2025-04-18 10:49:30.000000000 +09:00
type: post
header:
    teaser: "http://image.yes24.com/Goods/90265564/L"
    image: "http://image.yes24.com/Goods/90265564/L"
categories:
- IT
tags: [typescript]
---

# The Safest Way to Set Up a Path Alias per Package in a Monorepo

## Two-Line Summary
- If different packages reuse the same alias (such as @), they will inevitably collide.
- The `paths` setting in tsconfig.json applies only to the compilation step that includes that file. It does not automatically propagate across package boundaries.

## Environment

- TypeScript: 5.x
- Yarn : 4.x
- Vite: 6.x 
- Vitest : 3.x 


## Defining the Problem

If you want to import between packages using yarn workspaces, you can solve it by adding the `name` you set in package.json to the dependencies.

```typescript
// Example
import { redirectToLogin } from '@mso/shared/src';
```

But when it comes to importing modules within a package, an endless hell of relative paths awaits.

```typescript
// Example
import { isInApp } from '../../../utils/DeviceUtils.ts';
```

- Every time you move a directory, the paths break,
- and without IDE support, productivity plummets.



So you set up aliases in each package's tsconfig.json, but this setting does not propagate to other packages. If you set up a path alias in each package's tsconfig.json and import files inside that package, an error occurs when that package is used from another package.

```tsx
// Example
src/Header.tsx:1:18 - error TS2307: Cannot find module '@header/src/Logo.tsx' or its corresponding type declarations.

1 import Logo from '@header/src/Logo.tsx';
                   ~~~~~~~~~~~~~~~~~~~~~~


Found 1 error in src/Header.tsx:1
```

This is because each package's tsconfig.json is self-contained within that package; it is not passed along to other packages.

So you should specify the path aliases in the root workspace's tsconfig, and specify a different path alias for each package.

## Solution Strategy

### Core Rules

1. Declare the aliases for all packages in the root tsconfig.
1. Each package must extend the root configuration.
1. Tell the build tools (Vite, Vitest, etc.) about the aliases too.

### 1. Set up the paths in the root workspace's tsconfig

Place tsconfig.base.json (or tsconfig.json) at the top of the monorepo.

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

### 2. Extend the root workspace's tsconfig in each workspace

```jsonc
// packages/header/tsconfig.json
{
  "extends": "../../tsconfig.base.json",
   // ...
}
```

Up to this point, tsc's type checking passes. The build works too.

```ts
import Logo from './Logo.tsx'; // before
import Logo from '@header/src/Logo'; // After
```

### 3. Vite configuration

However, the Vite local server fails to run with the following error. Vite does not automatically read the TypeScript configuration. As a result, it still cannot resolve other packages via `@header`.

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

There are several approaches, but here is the simplest one.

Add the `vite-tsconfig-paths plugin` to each package's `vite.config.ts` file.

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

Now Vite can use the paths configured in tsconfig.json as they are.

### 4. Vitest configuration

Like Vite, Vitest also needs the path aliases configured. Otherwise, the tests fail as shown below.

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

The way to configure Vitest is the same as Vite. Modify the vitests.config.ts file and add 'vite-tsconfig-paths' to the plugins.

```json
// vitests.config.ts

// ...
import tsconfigPaths from 'vite-tsconfig-paths';
import { defineConfig } from 'vitest/config';

export default defineConfig({
  plugins: [
    // ...
    tsconfigPaths()  // added
    ],
  // ...
});

```

## Caveats

### 1. @ ?

Q. If I'm not using a monorepo across all packages, can't I just use the single character '@' the way I usually have?

A. If different physical paths are mapped to the same alias, they collide. Give each package its own unique prefix.

### 2. Too much hassle?

Q. Can't I just use relative paths instead of aliases?

A. It's possible, but the maintenance cost explodes.

### 3. What about lint?

Q. ESLint can't find the paths.

A. Install eslint-import-resolver-typescript and specify the root tsconfig path in settings.import.reslover.typescript.project. But if you just use Biome, you don't even need this kind of configuration.

## Wrap-up

1.	Gather all aliases at the root.
2.	Have every package extend that configuration.
3.	Tell Vite (or other build tools) about the aliases too.

Let's steer clear of relative-path hell in monorepos.

EOD

20250418

---
layout: single
title: "How to Deploy Yarn Quickly in Bitbucket Pipeline (Don't Use Zero Install)"
date: 2025-04-03 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://yarnpkg.com/assets/images/yarn-hero-transparent-78703b8ad343990b13f47109c1e2cff9.webp"
    image: "https://yarnpkg.com/assets/images/yarn-hero-transparent-78703b8ad343990b13f47109c1e2cff9.webp"
categories:
- IT
tags: [yarn]
---

Don't use [zero install](https://yarnpkg.com/features/caching#zero-installs) in [bitbucket cloud pipeline](https://www.atlassian.com/ko/software/bitbucket/features/pipelines).

Don't waste your time. There's another way instead.

## Goal
### In a micro frontend project, deploy several packages at once. Use zero install to cut deployment time as much as possible.

## Environment
* bitbucket pipeline (cloud version 2025.04.03)
* yarn v4.8.1
* [vite + react + ts](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts)

## Symptom

No matter what you do, you'll run into this error.

```bash
yarn build:home
node:internal/process/promises:391
    triggerUncaughtException(err, true /* fromPromise */);
    ^
Error: Required unplugged package missing from disk. This may happen when switching branches without running installs (unplugged packages must be fully materialized on disk to work).
Missing package: esbuild@npm:0.25.1
Expected package location: /opt/atlassian/pipelines/agent/build/.yarn/unplugged/esbuild-npm-0.25.1-d9214fa98d/node_modules/esbuild/
    at makeError (/opt/atlassian/pipelines/agent/build/.pnp.cjs:11577:34)
    at resolveUnqualified (/opt/atlassian/pipelines/agent/build/.pnp.cjs:13300:17)
    at resolveRequest (/opt/atlassian/pipelines/agent/build/.pnp.cjs:13351:14)
    at Object.resolveRequest (/opt/atlassian/pipelines/agent/build/.pnp.cjs:13407:26)
    at resolve$1 (file:///opt/atlassian/pipelines/agent/build/.pnp.loader.mjs:2043:21)
    at async nextResolve (node:internal/modules/esm/hooks:866:22)
    at async Hooks.resolve (node:internal/modules/esm/hooks:304:24)
    at async handleMessage (node:internal/modules/esm/worker:196:18)
Node.js v20.16.0
```

It's long, but the core error log is simple.

```bash
Error: Required unplugged package missing from disk. # This is it!
Missing package: esbuild@npm:0.25.1
Expected package location: /.../.yarn/unplugged/esbuild-npm-0.25.1-xxxxxx/node_modules/esbuild/
```

## Cause

### This error happens because Yarn didn't extract the esbuild package onto disk in its unplugged state.

### unplugged package missing?
*	Packages that include native binaries, like esbuild, can't run in their .zip file form (the PnP cache).
*	Yarn copies these packages into .yarn/unplugged/ in an extracted state.
*	This only happens during yarn install.
* For zero install, I included .yarn/cache in git, but I didn't include `.yarn/unplugged/*` in git.

### What about in the CI environment?
* Since .yarn/unplugged/ isn't included in Git, the CI (bitbucket pipeline) has to run yarn install every time to regenerate this directory.
* But right now the CI skips yarn install in the name of zero install, so esbuild isn't on disk and the build fails.

## Solution

### There isn't one

Just run `yarn install` every time.

### Is there really no other way?

Practically, no. There are alternatives, but they're quite tricky. Here's why:
* Native binary packages like esbuild, node-sass, and sharp are installed differently depending on the operating system and CPU architecture. So the binary packages from my local system won't work in the CI's docker environment.
* Yarn installs unplugged packages differently depending on the environment (OS, Node version, Yarn version, library version, and so on). So every time each team member runs yarn install, a git merge conflict can occur.
* What if you remove the binary packages that trigger unplugging? Theoretically possible. But doing migration work like esbuild → esbuild-wasm is no different from jumping into a fire.
* What if you branch on the unplug-triggering binaries? It looks doable, but the difficulty seems high.
* Building with a custom docker image looks really good, but I couldn't use it due to company policy.

## So do we have to suffer slow deployments forever?

### Use bitbucket cache

Let's use the [Dependency caches](https://support.atlassian.com/bitbucket-cloud/docs/cache-dependencies/) provided by bitbucket pipeline. For whatever reason, bitbucket only caches node_modules by default, so you have to configure it manually as shown below. Because it caches the dependencies, there's no need to download them again.

```yml
definitions:
  caches:
    yarn:
      key:
        files:
          - yarn.lock # re-cache when the yarn.lock file changes
      path: .yarn/cache # directory to cache
  steps:
    - step: &build-js
    caches:
      - yarn
    script:
      - corepack enable yarn # from yarn v4 onward, using corepack is recommended over the .yarn/releases/yarn-x.x.x.cjs file
      - yarn set version 4.8.1
      - yarn install --immutable # prevents yarn.lock and .yarn/cache from changing (dependency changes) during yarn install. the default setting is actually true
      - yarn build
```

EOD

20250403

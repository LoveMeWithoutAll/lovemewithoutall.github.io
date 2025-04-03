---
layout: single
title: bitbucket pipeline에서 yarn 배포 빠르게 하는 법(zero install을 사용하지 마라)
date: 2025-04-03 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://yarnpkg.com/assets/images/yarn-hero-transparent-78703b8ad343990b13f47109c1e2cff9.webp"
    image: "https://yarnpkg.com/assets/images/yarn-hero-transparent-78703b8ad343990b13f47109c1e2cff9.webp"
categories:
- IT
tags: [yarn]
---

[bitbucket cloud pipeline](https://www.atlassian.com/ko/software/bitbucket/features/pipelines)에서 [zero install](https://yarnpkg.com/features/caching#zero-installs)을 사용하지 마라.

시간 낭비 하지 마라. 대신 다른 방법이 있다.

## 목표
### 마이크로 프론트엔드 프로젝트에서 여러 패키지를 한 번에 배포한다. 배포 시간을 최대한 단축하기 위하여 zero install을 사용한다.

## 환경
* bitbucket pipeline(cloud 2025.04.03. 버전) 
* yarn v4.8.1
* [vite + react + ts](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts)

## 현상

무슨 짓을 하든 이런 오류를 만나게 될 것이다.

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

길지만 핵심 오류 로그는 간단하다.

```bash
Error: Required unplugged package missing from disk. # 이거다!
Missing package: esbuild@npm:0.25.1
Expected package location: /.../.yarn/unplugged/esbuild-npm-0.25.1-xxxxxx/node_modules/esbuild/
```

## 원인

### Yarn이 esbuild 패키지를 unplugged 상태로 디스크에 풀어놓지 않았기 때문에 발생하는 오류.

### unplugged package missing?
*	esbuild 같이 native 바이너리를 포함하는 패키지는 .zip 파일 상태(PnP 캐시)로는 실행이 불가능
*	Yarn은 이런 패키지를 .yarn/unplugged/에 압축 해제된 상태로 복사한다
*	이 과정은 yarn install 시에만 발생
* zero install을 위하여 .yarn/cache를 git에 포함시켰으나, `.yarn/unplugged/*`는 git에 포함시키지 않았다

### CI 환경에서는?
* .yarn/unplugged/는 Git에 포함하지 않기 때문에, CI(bitbucket pipeline)에서는 매번 yarn install을 실행해서 이 디렉토리를 다시 생성해야 한다.
* 그런데 현재 CI에서는 zero install을 한답시고 yarn install을 생략했기 때문에, esbuild가 디스크에 없어서 실패한 것.

## 해결책

### 없다

그냥 매번 `yarn install`을 하라.

### 정말로 방법이 없는가?

없다. 그 이유는
* esbuild, node-sass, sharp 등 native 바이너리 패키지는 운영체제 및 CPU 아키텍처에 따라 다르게 설치된다. 그러니 내 로컬 시스템의 바이너리 패키지는 CI의 docker 환경에서 동작하지 않는다.
* yarn은 환경(OS, Node version, Yarn version, library version and etc)에 따라 unplugged package를 비결정적으로 설치한다. 그래서 각 팀원이 yarn install을 할 때마다 매번 git merge conflict가 발생할 수 있다.
* unplugged를 유발하는 바이너리 패키지를 제거한다면? 이론적으로는 가능하다. 하지만 esbuild → esbuild-wasm 등의 마이그레이션 작업을 하는 건 불 속에 뛰어드는 것과 다를 바 없다.
* unplugged 유발 바이너리를 분기 처리한다면? 가능해보이지만 난이도가 높아 보인다.
* custom docker 이미지를 사용해서 빌드하는 방법이 아주 좋아 보이지만, 회사 정책상 사용할 수 없었다.

## 그러면 영원히 느린 배포 속도로 고통받야야 하는가?

### bitbucket cache를 사용하라

bitbucket pipeline이 제공하는 [Dependency caches](https://support.atlassian.com/bitbucket-cloud/docs/cache-dependencies/)를 사용하자. bitbucket은 무슨 생각인지 기본적으로 node_modules만 캐싱해주기 때문에 아래와 같이 직접 설정해야 한다. 의존성을 캐싱해주기 때문에 다시 다운로드 할 필요가 없다.

```yml
definitions:
  caches:
    yarn:
      key:
        files:
          - yarn.lock # yarn.lock 파일이 변경되면 다시 캐싱한다
      path: .yarn/cache # 캐싱할 디렉토리
  steps:
    - step: &build-js
    caches:
      - yarn
    script:
      - corepack enable yarn # yarn v4 부터는 .yarn/releases/yarn-x.x.x.cjs 파일보다는 corepack 사용을 권장한다
      - yarn set version 4.8.1
      - yarn install --immutable # yarn install을 할 때 yarn.lock과 .yarn/cache가 변경(의존성 변경)되지 않도록 한다. default 설정이 true이기는 하다
      - yarn build
```

EOD

20250403

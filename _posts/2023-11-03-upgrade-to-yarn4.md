---
layout: single
title: Yarn4로 업그레이드(feat. AWS)
date: 2023-11-03 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://yarnpkg.com/assets/images/yarn-hero-transparent-78703b8ad343990b13f47109c1e2cff9.webp"
    image: "https://yarnpkg.com/assets/images/yarn-hero-transparent-78703b8ad343990b13f47109c1e2cff9.webp"
categories:
- IT
tags: [yarn, javascript]
---

며칠 전에 [Yarn4](https://yarnpkg.com/)가 나왔다. 조만간 업그레이드 하려고 생각했으나, 문제는 당장 터졌다.

## 환경
* Yarn3
* Node.js Corepack
* AWS CodePipeline을 사용하여 CI/CD

## 문제

`AWS Codebuild`에서 빌드 오류가 발생했다. 원인은

1. Yarn의 stable 버전이 3 -> 4로 변경됨
1. `Corepack`의 yarn 설정 스크립트가 Yarn4를 실행. stable 버전을 실행하도록 스크립트를 작성해놨기 때문...
1. 원인 불명으로 Node.js v16으로 Yarn이 실행됨
1. Node v16으로는 yarn4를 실행할 수 없어서 오류 발생...
(하기 AWS Codebuild 로그 참조)

```bash
# 이전 생략..

Setting up nodejs (18.17.1-deb-1nodesource1) ...

[Container] 2023/10/30 01:10:45.684838 Running command corepack enable

[Container] 2023/10/30 01:10:48.270979 Running command corepack prepare yarn@stable --activate
Preparing yarn@stable for immediate activation...

[Container] 2023/10/30 01:10:50.170671 Running command yarn add env-cmd
Usage Error: This tool requires a Node version compatible with >=18.12.0 (got 16.20.1). Upgrade Node, or set `YARN_IGNORE_NODE=1` in your environment.

Yarn Package Manager - 4.0.1

  $ yarn <command>

You can also print more details about any of these commands by calling them with 
the `-h,--help` flag right after the command name.

[Container] 2023/10/30 01:10:50.504655 Running command yarn add cross-env
Usage Error: This tool requires a Node version compatible with >=18.12.0 (got 16.20.1). Upgrade Node, or set `YARN_IGNORE_NODE=1` in your environment.

Yarn Package Manager - 4.0.1

  $ yarn <command>

You can also print more details about any of these commands by calling them with 
the `-h,--help` flag right after the command name.

[Container] 2023/10/30 01:10:50.712425 Phase complete: INSTALL State: SUCCEEDED
[Container] 2023/10/30 01:10:50.712444 Phase context status code:  Message: 

[Container] 2023/10/30 01:10:50.778867 Entering phase BUILD
[Container] 2023/10/30 01:10:50.779639 Running command yarn run build
Usage Error: This tool requires a Node version compatible with >=18.12.0 (got 16.20.1). Upgrade Node, or set `YARN_IGNORE_NODE=1` in your environment.

# 이하 생략...
...
```

## 원인
Yarn4가 Node18을 무시하고 Node16에서 실행되는 것이 문제다. 그렇다면 corepack이 Yarn3를 실행하도록 설정해야 하나? 그 또한 한 방법이겠으나, 나는 우회책을 찾고 싶었다.

Node18 설치 스크립트는 이미 deprecation warning을 띄우고 있기 때문이다.

(하기 로그 참조)

```bash
# 생략...
[Container] 2023/10/30 01:09:20.175270 Running command curl -sL https://deb.nodesource.com/setup_18.x | bash -

================================================================================
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
================================================================================

                           SCRIPT DEPRECATION WARNING                    

  
  This script, located at https://deb.nodesource.com/setup_X, used to
  install Node.js is deprecated now and will eventually be made inactive.

  Please visit the NodeSource distributions Github and follow the
  instructions to migrate your repo.
  https://github.com/nodesource/distributions

  The NodeSource Node.js Linux distributions GitHub repository contains
  information about which versions of Node.js and which Linux distributions
  are supported and how to install it.
  https://github.com/nodesource/distributions


                          SCRIPT DEPRECATION WARNING

================================================================================
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
================================================================================

TO AVOID THIS WAIT MIGRATE THE SCRIPT
Continuing in 60 seconds (press Ctrl-C to abort) ...


## Installing the NodeSource Node.js 18.x repo...
# 생략...
```

## 해결책

1. AWS Codebuild의 빌드용 docker 이미지를 업그레이드 한다. v7 image에는 Node18이 설치되어 있다. 그러니 빌드 스크립트에서 Node18을 다운받고 설치할 필요가 없다.
![v7](/assets/images/codebuild-standard-v7.jpg)

1. yarn4로 업그레이드 한다. 아주 간단하다! yarn4 업그레이드를 위해서는 이 외에 아무 것도 더 해줄 게 없다.

```json
// package.json
{
  "name": "name",
  "version": "x.x.x",
  "packageManager": "yarn@4.0.1", // yarn4
  // ...
}
```

## 개선 결과
* yarn3 -> yarn4: 빌드 속도 9% 향상
* AWS Codebuild: 빌드 속도 33% 향상

![performance](/assets/images/build-performance.jpg)

## 끝
Yarn4 편하게 쓰세요.

2023.11.03.

---
layout: single
title: 모노레포에서 lefthook 설정
date: 2026-01-27 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://github.com/evilmartians/lefthook/raw/master/logo_sign.svg"
    image: "https://github.com/evilmartians/lefthook/raw/master/logo_sign.svg"
categories:
- IT
tags: [CI]
---

모노레포나 하위 폴더에 프로젝트가 위치한 환경에서 Lefthook을 설정하다 보면, 설정 파일을 찾지 못하거나 yarn 명령어가 실패하는 상황을 겪게 됩니다. 

## 1. 문제 상황

프로젝트 구조가 다음과 같을 때:

```
/my-repo (Git Root, .git)
  └── /frontend (Project Root, package.json, lefthook.yml)
```

발생한 오류들:

1. 설정 파일 미인식: frontend/ 폴더에서 Lefthook 실행 시, Git 루트에서 설정 파일을 찾으려다 실패함. (No config files found)
2. Yarn 프로젝트 미인식: 설정 파일을 Git 루트로 옮기면, yarn 명령어가 루트에서 실행되어 package.json을 찾지 못함. (Usage Error: No project found)

## 2. 원인 분석

- Lefthook의 동작 원리: Lefthook은 Git 훅 도구이므로 항상 .git 폴더가 있는 Git Root를 기준으로 설정 파일을 찾고 명령어를 실행
- Yarn Berry의 엄격함: Yarn Berry는 실행 위치에 package.json이나 관련 설정이 없으면 명령 실행을 차단합니다. Lefthook의 root 옵션만으로는 Yarn Berry의 환경을 제어할 수 없다

## 3. 해결 방법: 명시적 경로 이동 (cd)

가장 확실한 해결책은 lefthook.yml을 Git 루트로 옮기고, 명령어 실행 시 직접 프로젝트 폴더로 진입하도록 설정하는 것

### 최종 설정 (/my-repo/lefthook.yml)

```YAML
pre-commit:
  commands:
    frontend-check:
      run: cd frontend && yarn check # frontend 폴더로 이동 -> 해당 위치에서 yarn 스크립트 실행
```

## 4. 왜 이 방법이 좋은가?

1. 확실한 환경 격리: cd frontend를 통해 Yarn Berry가 해당 폴더의 .yarnrc.yml과 의존성을 정확히 인식할 수 있다
2. 유연한 확장성: 나중에 backend/ 등 다른 폴더가 추가되어도, 루트의 lefthook.yml 한 곳에서 모든 프로젝트의 훅을 관리할 수 있다
3. Lefthook v2 호환성: 일부 환경에서 root 옵션이 의도치 않게 동작하는 문제를 원천 차단

## 1줄 요약 

Lefthook과 Yarn Berry를 함께 사용할 때는 "명령어가 실행되는 현재 디렉토리(CWD)"를 명확히 해주는 것이 핵심

## 그 외

Tip: 설정 후 `yarn lefthook run pre-commit` 명령어로 실제 커밋 없이도 동작을 테스트해 볼 수 있습니다!

EOD

20260127

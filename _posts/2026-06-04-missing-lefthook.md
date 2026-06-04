---
layout: single
title: lefthook 동작 원리 — linked worktree 에서 install 하면 안 되는 이유
date: 2026-06-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://lefthook.dev/assets/lefthook.png"
    image: "https://lefthook.dev/assets/lefthook.png"
categories:
- IT
tags: [git, lefthook]
---

## 환경

- lefthook v2.1.6
- git v2.54.0

## 배경 지식

- git 은 .git/hooks 아래 pre-commit 등 약속된 이름의 실행 파일을 특정 동작 시점에 실행한다. git 의 기본 기능이다.
- 초기에는 .sample 이 붙은 예제 파일만 있어 실행될 훅이 없다. 약속된 이름 그대로의 실행 파일을 만들면 해당 git hook이 앞으로 실행된다.
- `lefthook install`은 "lefthook 바이너리를 찾아서 실행하라"는 내용의 셸 스크립트를 생성해서 `.git/hooks/pre-*` 자리에 둔다. 이 때 lefthook 바이너리 파일의 경로는 하드코딩 된다.
- `lefthook`의 바이너리 실행 파일은 git hook이 실행될 때마다 `lefthook.yml`의 `dsl`에 기술된 job을 읽고 실행한다. 그래서 yml 의 job 수정은 `lefthook install`을 다시 할 필요없이 바로 반영된다.

## lefthook 동작 순서

1. git 명령 실행
2. git 이 .git/hooks 의 hook 스크립트 실행
3. git hook 스크립트가 lefthook 바이너리 파일을 실행
4. lefthook 바이너리 파일이 lefthook.yml에 등록된 job을 실행

## 이걸 알아야 하는 이유

`git worktree`를 신규 생성해도 `.git/hooks`는 신규 생성되지 않는다. 1개의 git 레포지토리에 `.git/hooks`는 1개 뿐이고, 모든 worktree에서 공유한다.

`primary worktree`에서만 `lefthook install`을 실행하면 아무 문제 없다. 

하지만 **신규 생성한 `linked worktree`에서 `lefthook install`을 하면 `.git/hooks`에 등록된 lefthook 바이너리 파일의 경로가 `linked worktree`의 lefthook 바이너리 파일로 하드코딩** 된다. 

신규 생성한 `linked worktree`가 살아있을 때는 아무런 문제가 없다. 하지만 `linked worktree`를 삭제하면 `.git/hooks`는 lefthook 바이너리 파일도 함께 삭제된다.

이후 git hook이 실행 되어도 오류가 발생하지도 않는다. "Can't find lefthook in PATH"라는 메시지만 나올 뿐이다. lefthook 바이너리 파일이 `linked worktree` 삭제와 함께 제거되었기 때문이다. lefthook만 믿고 있다가 배신감을 느낄 수 있다.

20260604

---
layout: single
title: git에서 더이상 추적하지 않는 디렉토리를 삭제해서 git 용량을 줄이자
date: 2025-04-04 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/Git-Logo-2Color.png"
    image: "/assets/images/Git-Logo-2Color.png"
categories:
- IT
tags: [git]
---

## 목표

### 더이상 추적하지 않는 파일들 때문에 git의 용량이 너무 커졌다. 과거 커밋에서 해당 디렉토리를 삭제하자.

## 환경
* git v2.45.0

## 해결법

### 1. 저장소의 모든 브랜치에서 지정한 디렉토리를 제거

```bash
# targetDirectory를 지우자
git filter-branch --force --index-filter \
  "git rm -r --cached --ignore-unmatch targetDirectory" \
  --prune-empty -- --all
```
#### flag의 의미
*	--index-filter "git rm -r --cached --ignore-unmatch targetDirectory" : 해당 디렉토리를 Git의 인덱스(스테이징 영역)에서 제거
*	--prune-empty: 삭제 후 빈 커밋은 제거
*	-- --all: 모든 브랜치에 대해 적용

### git 작업 내역 제거

Git은 작업 내역을 기록하는 reflog를 보관한다. 이를 만료시켜 불필요한 기록을 제거한다.

```bash
git reflog expire --expire=now --all
```

### 강제 Garbage Collection

Git의 가비지 컬렉션을 통해 사용하지 않는 객체들을 제거. Aggressive 옵션을 사용하면 좀 더 철저하게 최적화할 수 있다.

```bash
git gc --prune=now --aggressive
```

### git 객체 새로 패킹(압축)

대격변이 있었으니 git을 새로 압축하자

```bash
git repack -Ad
```

#### 객체 패킹이란?

Gpt 답변이다.

객체 패킹(pack) 은 이런 개별 객체들을 하나의 압축된 파일(또는 여러 파일)로 통합하는 과정입니다. 구체적으로:
* 공간 절약:
여러 개의 작은 파일 대신 하나의 큰 파일로 관리하면 디스크의 메타데이터 사용량이 줄어들어 저장 공간을 효율적으로 사용할 수 있습니다.
* 속도 향상:
파일 시스템에서 수많은 작은 파일을 읽는 것보다, 하나의 큰 파일에서 필요한 데이터를 찾는 것이 빠를 수 있습니다.
* Delta 압축:
유사한 내용의 객체들끼리 차이점(델타)만 기록하는 방식으로 압축하여, 더 큰 저장 공간 절약을 도모할 수 있습니다.

### git push

변경된 내용을 remote 저장소에 올리자. 대격변이 있었으니 반드시 `--force`를 써서 올려야 한다.

```bash
git push origin --force --all
git push origin --force --tags
```

EOD

20250404

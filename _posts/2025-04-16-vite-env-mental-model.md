---
layout: single
title: Vite 환경변수 우선순위 멘탈 모델
date: 2025-04-16 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://vitejs.dev/logo-with-shadow.png"
    image: "https://vitejs.dev/logo-with-shadow.png"
categories:
- IT
tags: [frontend, vite]
---

Vite는 여러 .env 파일들을 순서대로 로드하여 환경 변수를 설정합니다. 로드 순서와 우선순위를 아래와 같이 이해할 수 있습니다.

```
Pre-existing environment variables (이미 존재하는 환경 변수)
    │
    ├─ (.env.[mode].local)  → 특정 모드 & 로컬 전용 (예: .env.production.local)
    │
    ├─ (.env.[mode])        → 특정 모드 전용 (예: .env.production)
    │
    ├─ (.env.local)         → 모든 상황에서 사용되나, 로컬 전용 (Git에 의해 무시됨)
    │
    └─ (.env)               → 모든 상황에서 사용됨
```

## 1. Pre-existing Environment Variables
-	최우선 적용: 이미 OS나 커맨드라인에서 설정된 환경 변수 (예: VITE_SOME_KEY=123 vite build와 같이 지정된 변수)는 어떤 .env 파일보다 높은 우선순위를 가집니다.
-	의미: 이 변수들은 Vite가 실행되기 전에 이미 설정되어 있기 때문에, 이후에 로드되는 .env 파일들이 이 값을 덮어쓰지 않습니다.

## 2. 모드별 환경 변수 파일

Vite는 특정 모드(예: production, development 등)에 대해 전용 환경 변수 파일을 지원합니다.

### .env.[mode].local
-	적용 조건: 특정 모드에서만 사용되며, 로컬 개발 환경에서 한정하여 적용됩니다.
-	우선순위: .env.[mode] 파일보다 더 높은 우선순위를 가집니다.

###	.env.[mode]
-	적용 조건: 지정된 모드에 대해 사용됩니다.
-	우선순위: 일반 .env와 .env.local에 정의된 값보다 우선합니다.

## 3. 일반(공통) 환경 변수 파일

###	.env.local
-	적용 조건: 모든 상황에서 사용되나, 로컬 개발 환경에 한해서 적용되며 Git에 의해 무시됩니다.
-	우선순위: .env 파일보다 우선하지만, 모드별 파일보다는 낮은 우선순위를 갖습니다.

###	.env
-	적용 조건: 모든 상황에 기본적으로 적용되는 환경 변수 설정을 제공합니다.
-	우선순위: 가장 낮은 우선순위입니다.

------

## 예시: “production” 모드에서의 환경 변수 적용 순서

### 1.	Pre-existing Environment Variables:
-	예: VITE_SOME_KEY=123 (커맨드라인이나 운영체제에서 이미 설정한 변수)

### 2. 모드별 로컬 파일:

-	파일: .env.production.local
→ 로컬 개발 환경에서 “production” 모드 전용으로 적용

### 3. 모드별 파일:

-	파일: .env.production
→ “production” 모드 전용으로 적용

### 4. 일반 로컬 파일:

-	파일: .env.local
→ 로컬 환경에서 사용되며, Git에는 포함되지 않음

### 5. 일반 파일:

-	파일: .env
→ 모든 상황에 기본적으로 적용

------

## 핵심 정리
-	최우선: 실행 시 이미 존재하는 환경 변수가 가장 높은 우선순위를 가집니다.
-	모드별 파일 우선: 동일한 변수명이 있을 경우, .env.[mode].local 및 .env.[mode]에 정의된 값이 .env 및 .env.local에 정의된 값보다 우선 적용됩니다.
-	기본값 제공: .env와 .env.local은 모드별 파일이 없는 경우 또는 일부 변수만 지정된 경우 기본값을 제공합니다.

이 멘탈 모델을 통해 Vite의 환경 변수 로드 순서를 이해하면, 프로젝트 환경 설정에서 원하는 우선순위를 쉽게 관리하고, 특정 환경 변수 값이 어떻게 결정되는지 예측할 수 있습니다.

## 주의

`.env.local`은 `mode`보다 우선순위가 낮습니다.

------


EOD

20250416

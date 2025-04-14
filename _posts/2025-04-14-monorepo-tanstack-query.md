---
layout: single
title: monorepo에서 tanstack query가 No QueryClient 오류를 발생시키는 오류
date: 2025-04-14 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://tanstack.com/_build/assets/splash-light-CHqMsyq8.png"
    image: "https://tanstack.com/_build/assets/splash-light-CHqMsyq8.png"
categories:
- IT
tags: [tanstack-queyr, monorepo]
---

## 환경
- yarn 4(monorepo)
- react 19
- tanstack query(react query) 5

## 현상

모노레포 환경에서 A 패키지가 B 패키지의 @tanstack/react-query 훅을 사용하는 경우, 아래와 같은 오류가 발생할 수 있다.

```bash
Error: No QueryClient set, use QueryClientProvider to set one
```

이는 React Query의 내부 컨텍스트(Context)를 통해 QueryClient 인스턴스를 찾지 못했을 때 발생하는 오류다. 단순히 QueryClientProvider를 상단에 설정했다고 해서 항상 해결되지는 않는다.

## 원인

-	모노레포 내에서 패키지 간에 서로 다른 버전의 @tanstack/react-query 또는 react가 설치되었을 경우, QueryClient 인스턴스가 분리되어 context가 공유되지 않으므로 위 오류가 발생한다.

- 모노레포 내에서 패키지 간에 서로 같은 버전의 @tanstack/react-query를 사용한다고 하더라도, 각 패키지마다 별도의 context를 사용하면 위 오류가 발생할 수 있다.

## 해결책

여러 해결책이 있으나, 가장 안정적이고 단순한 방법을 소개한다.

### 1. QueryClient 및 Provider를 단일 패키지에만 정의하라

`Query provider 컴포넌트`와 `QueryClient`를 단 1개의 공용 패키지(shared 혹은 core)에만 정의하고, 다른 패키지에서는 이걸 import 해서 사용하면 된다. 이렇게 하면 버전 불일치 등의 문제가 발생할 수 없다.

### 2. 앱 전체에서 하나의 QueryClientProvider만 사용하라

여러 곳에서 Provider를 중복으로 생성하지 말고, 앱의 루트에서 단일 Provider를 설정해 전체 트리에 context가 일관되게 유지되도록 한다.

## React Context에 대하여

React의 Context는 동일한 React 인스턴스를 사용하는 컴포넌트끼리만 데이터를 공유할 수 있다. 하지만 모노레포 환경에서는 react 또는 @tanstack/react-query가 패키지별로 중복 설치되거나 다른 번들 경로로 로드되는 경우, 각 패키지마다 별개의 context를 가지게 되어 QueryClient를 공유하지 못한다.

이런 문제는 React Query뿐 아니라, context를 사용하는 모든 라이브러리에서 발생할 수 있는 일반적인 이슈다.

## 1줄 요약

모노레포에서는 QueryClient와 QueryClientProvider를 반드시 단일 인스턴스로 공유하고, React Query를 사용하는 모든 패키지가 동일한 context를 바라보도록 구성해야 한다.

## 참고

[Error: No QueryClient set, use QueryClientProvider to set one](https://github.com/TanStack/query/issues/7965)

EOD

20250414

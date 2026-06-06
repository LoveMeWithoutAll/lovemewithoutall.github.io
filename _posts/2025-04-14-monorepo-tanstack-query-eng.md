---
layout: single
title: The "No QueryClient" Error That TanStack Query Throws in a Monorepo
date: 2025-04-14 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://tanstack.com/_build/assets/splash-light-CHqMsyq8.png"
    image: "https://tanstack.com/_build/assets/splash-light-CHqMsyq8.png"
categories:
- IT
tags: [tanstack-queyr, monorepo]
---

## Environment
- yarn 4 (monorepo)
- react 19
- tanstack query (react query) 5

## Symptom

In a monorepo environment, when package A uses the `@tanstack/react-query` hooks from package B, the following error can occur.

```bash
Error: No QueryClient set, use QueryClientProvider to set one
```

This error happens when the QueryClient instance can't be found through React Query's internal Context. Simply placing a QueryClientProvider at the top doesn't always solve it.

## Cause

-	If different versions of `@tanstack/react-query` or `react` are installed across packages within the monorepo, the QueryClient instances become separate, the context is no longer shared, and the error above occurs.

- Even when packages within the monorepo use the same version of `@tanstack/react-query`, the error above can still occur if each package uses its own separate context.

## Solution

There are several solutions, but here I'll introduce the most stable and simplest one.

### 1. Define the QueryClient and Provider in a single package only

Define the `Query provider component` and the `QueryClient` in just one shared package (such as `shared` or `core`), and import them from there in the other packages. Doing this makes problems like version mismatches impossible.

### 2. Use only one QueryClientProvider across the entire app

Don't create multiple, duplicate Providers in various places. Instead, set up a single Provider at the root of the app so that the context stays consistent across the whole tree.

## About React Context

React's Context can only share data between components that use the same React instance. However, in a monorepo environment, when `react` or `@tanstack/react-query` is installed redundantly per package or loaded from different bundle paths, each package ends up with its own separate context and fails to share the QueryClient.

This problem isn't limited to React Query; it's a general issue that can occur with any library that uses context.

## One-line summary

In a monorepo, you must always share the QueryClient and QueryClientProvider as a single instance, and configure every package that uses React Query so they all look at the same context.

## References

[Error: No QueryClient set, use QueryClientProvider to set one](https://github.com/TanStack/query/issues/7965)

EOD

20250414

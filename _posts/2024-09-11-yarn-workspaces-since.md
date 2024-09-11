---
layout: single
title: Running Scripts in Changed Packages Based on Git Branch with Yarn Workspaces
date: 2024-09-11 20:24:30.000000000 +09:00
type: post
header:
    teaser: "https://yarnpkg.com/assets/images/yarn-hero-transparent-78703b8ad343990b13f47109c1e2cff9.webp"
    image: "https://yarnpkg.com/assets/images/yarn-hero-transparent-78703b8ad343990b13f47109c1e2cff9.webp"
categories:
- IT
tags: [yarn]
---

Based on Yarn v4.4.0.

This is a method to run scripts only in the packages that have changed, based on the git branch, when your monorepo is configured using Yarn workspaces.

```sh
yarn workspaces foreach --since=develop run build # runs the build script, comparing with the develop branch
```

Do not use `--since develop` this way!

20240911

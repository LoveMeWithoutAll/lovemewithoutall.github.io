---
layout: single
title: Things to Watch Out for with npm audit fix --force
date: 2021-11-23 12:54:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/d/db/Npm-logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/d/db/Npm-logo.svg"
categories:
- IT
tags: [npm]
---

Running `npm audit fix --force` can also downgrade the version of a node module. That's because it looks for a version where the security vulnerability doesn't occur.

20211027

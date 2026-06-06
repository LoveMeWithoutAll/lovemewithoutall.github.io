---
layout: single
title: Build artifact and source artifact in AWS CodeBuild
date: 2021-11-10 19:14:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2021-11-11-build-artifact-source-artifact.png"
    image: "/assets/images/2021-11-11-build-artifact-source-artifact.png"
categories:
- IT
tags: [aws]
---

A build artifact is the file that stores the result after CodeBuild finishes a build. It is used later during auto scaling.
A source artifact is the file containing the source code that CodeBuild pulled in order to run the build.

When you run CodeBuild, both are created.

You can configure the upload location at CodeBuild > Build projects > select a build > in the submenu > Build details > Artifacts.

![artifacts](/assets/images/2021-11-11-build-artifact-source-artifact.png)

20211018

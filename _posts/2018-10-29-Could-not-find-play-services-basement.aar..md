---
layout: single
title: Could not find play-services-basement.aar
date: 2018-10-30 09:03:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Android]
---

Android studio에서 앱을 build하려고 하는데, 이런 오류가 발생했다.

```
Error:Could not find play-services-basement.aar (com.google.android.gms:play-services-basement:15.0.1). 
Searched in the following locations:
    https://jcenter.bintray.com/com/google/android/gms/play-services-basement/15.0.1/play-services-basement-15.0.1.aar

```

말 그대로 빌드를 하려는데 `play-services-basement` 라이브러리를 레포지토리에서 찾을 수 없어 발생하는 에러다. 

지금까지 잘 되던 빌드가 왜 실패하냐면, 라이브러리를 찾는 레포지토리의 순서가 문제다. 

`android/build.gradle.` 이 파일을 수정해야 한다. 아마도 `jcenter()`가 가장 첫번째로 위치해 있을 것이다. 그렇다면,

## jcenter()의 순서를 가장 아래로 내린다.

```gradle
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    repositories {
        jcenter() // 순서 변경
        google() // jcenter()를 제일 아래로
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.2.1'
        classpath 'com.google.gms:google-services:4.0.0'
    }
}

allprojects {
    repositories {
        jcenter() // 순서 변경
        google() // jcenter()를 제일 아래로
    }
}
```

이렇게 해주는 이유는, [jcenter](https://bintray.com/bintray/jcenter) 라이브러리 저장소에 해당 라이브러리가 등록이 안되어 있고, 아무래도 google의 공식 저장소를 먼저 검색하는 편이 옳으니, 라이브러리를 검색하는 우선순위에서 jcenter를 밑으로 내린 것이다.

이제 잘 돌아간다!

참고: https://stackoverflow.com/questions/50563407/could-not-find-play-services-basement-aar/51213879#51213879

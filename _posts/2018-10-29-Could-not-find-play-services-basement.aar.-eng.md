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

I was trying to build an app in Android Studio when this error popped up.

```
Error:Could not find play-services-basement.aar (com.google.android.gms:play-services-basement:15.0.1). 
Searched in the following locations:
    https://jcenter.bintray.com/com/google/android/gms/play-services-basement/15.0.1/play-services-basement-15.0.1.aar

```

As the message says, this error occurs because the build can't find the `play-services-basement` library in the repository.

So why does a build that worked just fine until now suddenly fail? The problem is the order of the repositories where libraries are searched for.

You need to edit the `android/build.gradle.` file. Most likely `jcenter()` is positioned first. If so,

## Move jcenter() to the very bottom.

```gradle
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    repositories {
        jcenter() // order changed
        google() // move jcenter() to the very bottom
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.2.1'
        classpath 'com.google.gms:google-services:4.0.0'
    }
}

allprojects {
    repositories {
        jcenter() // order changed
        google() // move jcenter() to the very bottom
    }
}
```

The reason for doing this is that the library isn't registered in the [jcenter](https://bintray.com/bintray/jcenter) library repository. And since it makes more sense to search Google's official repository first anyway, I moved jcenter down in the priority order for searching libraries.

Now it works just fine!

Reference: https://stackoverflow.com/questions/50563407/could-not-find-play-services-basement-aar/51213879#51213879

---
layout: single
title: 
date: 2020-04-28 18:23:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Android]
---

When your Android App's minSdkVersion is prior to 21(using dalvik), and your app and the libraries it references exceed 65,536 methods, **Android studio debugger does not work** otherwise your expectation, silently. Of course, it is not that all break point is not work. Some Activity Classes trigger debugger. But Any other java classes and kotlin classes would betray you.

I couldn't know the cause exactly. However **Multiple DEX problem** might affect it. So believe me, upgrade minSdkVersion to 21 or higher and using ART. ART natively supports loading multiple DEX files. If using ART is impossible, add multidex support lirary to your project

You can find library version in this [page](https://developer.android.com/jetpack/androidx/versions)

```gradle
dependencies {
    // Using AndroidX
    // If you're not using AndroidX, find other support library
    implementation 'androidx.multidex:multidex:2.0.1'
}
```

Reference: [Enable multidex for apps with over 64K methods](https://developer.android.com/studio/build/multidex)

---
layout: single
title: 20180327 Diary - Android Development
date: 2018-03-28 00:07:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- Diary
tags: [android, diary, dev-diary]
---

### The Beginning
This is a diary I started in an attempt to do something, anything, to motivate myself. So, here we go!

### Developing an Android WebView App

In the afternoon I picked up Android. I had to rebuild the Inha University admissions app. There was existing source code that was up and running, but it hadn't been maintained for a long time, so it wasn't working properly. The compile SDK was the ancient Gingerbread. Naturally, it wouldn't even run in Android Studio. To make things easier on myself, I decided to build it from scratch. It had been a while since I last touched Android, but it wasn't all that difficult. First I brought up a WebView and handled, one by one, the special cases passed through the URL. It took less than 1 MD (one man-day) to finish development. The problem was that the finished code was quite messy and I couldn't stand looking at it. So I wouldn't be ashamed of myself, I refactored it over and over. After pouring in about two hours, I ended up with a chunk of code that more or less honored the single responsibility principle. But since I have no foundation in Android development, and likewise no experience collaborating on Android development, I still have no confidence about whether it's code that other developers would consider decent. The fundamentals matter in everything, but as a non-major who was always thrown into projects on short notice, I never had time to build a solid base. When I did have time, I wasted it on the pretext of recovering my ruined health. Even after six years working as a developer, this is still the case. It's troubling. I have to keep up with the latest technologies and also strengthen my fundamentals. There's a long way to go.

20180327

---
layout: single
title: The scrollTop bug in Android mobile Chrome
date: 2021-10-09 19:46:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [javascript]
---

In Android mobile Chrome, for reasons unknown, the scroll sometimes fails to move all the way to the very top or the very bottom.

Oddly enough, the cause is that you can only scroll up to a value that falls just slightly short of the maximum and minimum scrollTop values.

As a result, errors can occur when you rely on the scrollTop property to fire a scroll event.
Organized case by case, it looks like this:

1. When the scroll is at the very bottom
	1. Normal case
		1. scrollTop === scrollHeight - clientHeight
	2. Abnormal case
		1. scrollTop - 0.42xxxx.. === scrollHeight - clientHeight
			1. Oddly enough, you can only move up to a value that falls just slightly short of the maximum scrollTop
2. When the scroll is at the very top
	1. Normal case
		1. scrollTop === 0
	2. Abnormal case
		1. scrollTop === 0.5714285969734192
			1. It turns into a bizarre number

When flex-direction: column-revese is used, it behaves the other way around.
1. When the scroll is at the very bottom
	1. Normal case
		1. scrollTop === 0
	2. Abnormal case
		1. scrollTop === 0.5714285969734192
2. When the scroll is at the very top
	1. Normal case
		1. scrollTop === scrollHeight - clientHeight
	2. Abnormal case
		1. scrollTop < scrollHeight - clientHeight
			1. Oddly enough, it moves slightly past the maximum scrollTop and goes even higher

Test environment
- Device: LG Q52
- OS: Android 10; LM-Q520N Build/QKQ1.200614.002
- chrome version: 92.0.4515.131

20210804

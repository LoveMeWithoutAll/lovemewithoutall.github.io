---
layout: single
title: Safari's bounce scroll problem (2)
date: 2021-10-14 00:48:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/5/52/Safari_browser_logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/5/52/Safari_browser_logo.svg"
categories:
- IT
tags: [frontend]
---

iOS produces a bounce scroll effect when the scroll has strong momentum. It is a pleasant UX, but it causes problems when you want to trigger events based on scrollTop.

Cases by scroll direction
1. When the scroll reaches the very bottom
	1. scrollTop breaks past the bottom of the scrollable container
		1. scrollTop becomes greater than scrollHeight - clientHeight
2. When the scroll reaches the very top
	1. scrollTop breaks past the bottom of the scrollable container
		1. scrollTop becomes less than 0

The problem becomes especially severe when the clientHeight of the scrollable container is small, because the impact of the bounce scroll effect is relatively large. The bounce scroll effect always pushes scrollTop out by the same amount (regardless of clientHeight). Therefore, relative to the clientHeight value, the scrollTop value pushed out by the bounce scroll effect becomes relatively large. This problem stands out in JS code that checks the scroll position by comparing scrollTop and clientHeight. For example, if you use infinite scroll, you can see scroll events firing explosively because of the bounce scroll effect.

To solve this problem, there are various ways to remove the bounce scroll.

A summary by method
- Using CSS
	- It mostly doesn't work.
		- In my case, where I tried to control the bounce scroll of an inner container rather than the body, every attempt failed.
		- The CSS approach may stop working with browser version updates. Based on my search, there are such cases.
	- So I recommend not using it.
- Using JS
	- It is impossible for JS to detect every scroll event.
		- This is because scrolling can proceed faster than the JS code can run.
		- It is impossible to apply unless you completely block the touchmove event.
			- If you use this method, you can't make the mobile browser scroll at all.
	- So I recommend not using this approach.

Therefore, I recommend a very simple approach.

First, let the bounce scroll effect settle gradually. The bounce scroll effect occurs based on the inertial force produced by the strength of the user's touch. The inertial force weakens the longer the resulting scroll travels. Therefore, if clientHeight is large or scrollHeight is large, the phenomenon of scrollTop being pushed out by the bounce scroll effect is minimized.

Second, don't put scroll events directly on the call stack. Use setTimeout (task queue), promise (microtask queue), or requestAnimationFrame (animation frame). It is not a perfect solution, but it can prevent the phenomenon where a scroll event chains into triggering another scroll event.

20210805

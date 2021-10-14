---
layout: single
title: Setting scrollTop does not work while momentum scroll is ongoing. But there is a workaround.
date: 2021-10-14 19:11:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/5/52/Safari_browser_logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/5/52/Safari_browser_logo.svg"
categories:
- IT
tags: [frontend]
---

Environment
iOS Safari

Phenomenon
- User gesture
	- Do momentum(aka inertial) scrolling
- code
	- Fire onScroll event as long as inertial momentum scrolling

Problem
- Setting scrollTop does not work while momentum scroll is ongoing
	- Manually setting scrollTop is impossible while momentum scrolling
	- So events that have dependencies on scrollTop value and manipulate scrollTop at the same time could work incorrectly

Solution
- solution: setTimeout(onScrollEvent, 0)
	- There is not any perfect solution for the problem
	- But workaround is here. why?
		- While momentum scroll is ongoing, 
		- Before applying the solution
			- ScrollTop value is changed in real time while momentum scrolling is ongoing
			- The changed scrollTop value make onScrollEvent be fired as soon as possible
		- After applying the solution
			- The onScrollEvent will be fired after other tasks like rendering

20210809

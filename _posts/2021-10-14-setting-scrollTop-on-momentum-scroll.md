---
layout: single
title: Setting scrollTop does not work while momentum scroll is ongoing. But there is a workaround.
date: 2023-07-21 14:11:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/5/52/Safari_browser_logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/5/52/Safari_browser_logo.svg"
categories:
- IT
tags: [frontend]
---

## Environment
iOS Safari

## Phenomenon
- User gesture
	- Do momentum(aka inertial) scrolling
- code
	- Fire onScroll event as long as inertial momentum scrolling

## Problem
- Setting scrollTop does not work while momentum scroll is ongoing
	- Manually setting scrollTop is impossible while momentum scrolling
	- So events that have dependencies on scrollTop value and manipulate scrollTop at the same time could work incorrectly
	- Momentum scroll overrides updating scrollTop when scrolling time is too long

## Solution
- code
```javascript
const functionOnIos = () => {
	element.style.overflow = 'hidden'; // disable scrolling
	whatYouWantToDo();
	element.style.overflow = 'auto'; // enable scrolling
};
```
- description
	- There is not any perfect solution for the problem. Because Javascript sandbox cannot control momentum scroll.
	- But workaround is here. why?
		- While momentum scroll is ongoing, 
		- Before applying the solution
			- ScrollTop value is changed in real time while momentum scrolling is ongoing
			- The changed scrollTop value make onScrollEvent be fired as soon as possible
			- When momentum is too strong, updating scrollTop is not fired or overrided by momentum scroll moving
		- After applying the solution
			- The onScrollEvent will be fired immediately because scrolling is disabled

## Addtional
* If you want to fire codes that is just do something regardless of a scroll position, use `setTimeout(() => doSomething(), 0)`. It will be fired after scroll rendering.
* Must check whether it is iOS or iPadOS.

#### create: 20210809
#### last update: 20230721

### refrence
* [force-stop-momentum-scrolling](https://stackoverflow.com/questions/16109561/force-stop-momentum-scrolling-on-iphone-ipad-in-javascript)
* [before-inertia-momentum-scrolling](https://stackoverflow.com/questions/76496474/perform-action-when-the-user-scrolls-before-inertia-momentum-scrolling-takes-ov)

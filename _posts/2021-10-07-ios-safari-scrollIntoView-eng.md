---
layout: single
title: Notes on scrollIntoView method bugs in iOS Safari
date: 2021-10-07 19:10:30.000000000 +09:00
type: post
header:
    teaser: "https://km.support.apple.com/kb/image.jsp?productid=PL165&size=240x240"
    image: "https://km.support.apple.com/kb/image.jsp?productid=PL165&size=240x240"
categories:
- IT
tags: [javascript]
---

1. Summary: Safari does not support all options of scrollIntoView
	1. reference: https://caniuse.com/?search=scrollIntoView
	2. Tested on Safari 13 and 14
2. List of bugs
	1. The behavior: 'smooth' option is not supported
		1. Symptom: Scroll does not work
		2. Solution: Use behavior: 'auto'
		3. An officially recognized bug
	2. inline: 'nearest' behaves abnormally during horizontal scrolling
		1. Symptom: When triggering the scrollIntoView method on one of three or more elements, it cannot scroll to an element located in the middle (positioned between other elements)
		2. Solution: Use inline: 'end'
		3. An undocumented bug

20210729

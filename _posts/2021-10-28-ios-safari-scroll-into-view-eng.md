---
layout: single
title: A Roundup of scrollIntoView Method Bugs in iOS Safari
date: 2021-10-28 18:33:30.000000000 +09:00
type: post
header:
    teaser: "https://raw.githubusercontent.com/MoonHighway/learning-react/second-edition/learning-react.jpg"
    image: "https://raw.githubusercontent.com/MoonHighway/learning-react/second-edition/learning-react.jpg"
categories:
- IT
tags: [iOS]
---

1. Summary: Safari does not support all of the scrollIntoView options
	1. reference: https://caniuse.com/?search=scrollIntoView
	2. Tested on Safari 13 and 14
2. List of bugs
	1. The behavior: 'smooth' option is not supported
		1. Symptom: Scrolling does not work
		2. Solution: Use behavior: 'auto'
		3. An officially known bug
	2. inline: 'nearest' behaves abnormally on horizontal scroll
		1. Symptom: When triggering the scrollIntoView method on one of three or more elements, you cannot scroll to an element located in the middle (positioned between other elements)
		2. Solution: Use inline: 'end'
		3. An undocumented bug

20210729

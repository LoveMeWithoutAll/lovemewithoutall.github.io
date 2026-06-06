---
layout: single
title: "Bug where the mouse wheel doesn't work while dragging in a scrollable container"
date: 2021-08-05 22:05:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/22/3-Tasten-Maus_Microsoft.jpg/440px-3-Tasten-Maus_Microsoft.jpg"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/22/3-Tasten-Maus_Microsoft.jpg/440px-3-Tasten-Maus_Microsoft.jpg"
categories:
- IT
tags: [chrome]
---


1. Symptom
	1. Affected target: scrollable container
	2. Symptom details
		1. While dragging, the mouse wheel event does not work in the scrollable container
		2. The scrollable container is located between an element with position fixed, absolute, or sticky and an element with position relative
	3. Browser: macOS Chrome only ([It never worked on Windows Chrome in the first place](https://lovemewithoutall.github.io/it/scroll-whild-dragging-on-win/))
2. Solution
	4. Add a style
		1. Set the style of the element that is at the same level as the element with position fixed, absolute, or sticky, and that contains the scrollable container, to position: 'sticky'
		2. With this, wheel scroll works while dragging
3. Cause
	5. Presumed to be a Chrome bug
		1. position sticky causes a side effect on wheel scroll behavior
		2. position sticky and position relative affect each other
4. Open questions
   	1. Shouldn't position: 'sticky' only take effect when it's used together with CSS properties like top, left, etc.? It feels like fixing a bug with another bug, which isn't very satisfying.
5. Runtime environment

| Chrome          | 91.0.4472.114 (Official Build) (x86_64)                                                                                   |   |   |   |
|-----------------|---------------------------------------------------------------------------------------------------------------------------|---|---|---|
| Revision        | 4bb19460e8d88c3446b360b0df8fd991fee49c0b-refs/branch-heads/4472@{#1496}                                                   |   |   |   |
| OS              | V8 9.1.269.36                                                                                                             |   |   |   |
| User Agent      | Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36 |   |   |   |
| Command Line    | --no-first-run --disable-fre --no-default-browser-check --flag-switches-begin --flag-switches-end about:blank             |   |   |   |



20210723

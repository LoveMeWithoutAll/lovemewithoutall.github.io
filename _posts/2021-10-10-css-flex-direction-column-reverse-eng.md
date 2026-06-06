---
layout: single
title: scrollTop when the CSS flex-direction property is set to column-reverse
date: 2021-10-10 19:46:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/CSS3_logo_and_wordmark.svg/1200px-CSS3_logo_and_wordmark.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/CSS3_logo_and_wordmark.svg/1200px-CSS3_logo_and_wordmark.svg.png"
categories:
- IT
tags: [css]
---

- Summary
	- When flex-direction: column-reverse
		- The reference point for scrollTop is the very bottom of the scroll
		- scrollTop takes values from -n to 0
		- When the scroll bar is at the very top, scrollTop === -n
		- When the scroll bar is at the very bottom, scrollTop === 0
- The general case
	- When the scroll bar is at the very top
		- scrollTop === 0
	- When the scroll bar is at the very bottom
		- scrollTop === scrollHeight - clientHeight
- When flex-direction: column-reverse is applied via CSS
	- When the scroll bar is at the very top
		- -scrollTop === scrollHeight - clientHeight
	- When the scroll bar is at the very bottom
		- scrollTop === 0
		
20210810

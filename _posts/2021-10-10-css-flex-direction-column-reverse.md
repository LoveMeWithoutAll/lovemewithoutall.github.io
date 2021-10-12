---
layout: single
title: CSS flex-direction property의 값이 column-reverse 일 때의 scrollTop
date: 2021-10-10 19:46:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/CSS3_logo_and_wordmark.svg/1200px-CSS3_logo_and_wordmark.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/CSS3_logo_and_wordmark.svg/1200px-CSS3_logo_and_wordmark.svg.png"
categories:
- IT
tags: [css]
---

- 요약
	- flex-direction: column-reverse 일 때
		- scrollTop의 기준은 scroll 최하단이다
		- scrollTop은 -n ~ 0의 값을 가진다
		- scroll-bar가 최상단일 때 scrollTop === -n
		- scroll-bar가 최하단일 때 scrollTop === 0
- 일반적인 경우
	- scroll-bar가 최상단에 있을 때
		- scrollTop === 0
	- scroll-bar가 최하단에 있을 때
		- scrollTop === scrollHeight - clientHeight
- CSS로 flex-direction: column-reverse 적용되었을 때
	- scroll-bar가 최상단에 있을 때
		- -scrollTop === scrollHeight - clientHeight
	- scroll-bar가 최하단에 있을 때
		- scrollTop === 0
		
20210810

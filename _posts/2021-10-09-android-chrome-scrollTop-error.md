---
layout: single
title: Android mobile Chrome의 scrollTop 오류
date: 2021-10-09 19:46:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [javascript]
---

Android mobile chrome에서는 알 수 없는 이유로 scroll이 최상단, 최하단까지 이동하지 않는 경우가 있다. 

원인은 이상하게도 최대 및 최소 scrollTop 값에 아주 약간 못미치는 값까지만 이동 가능하기 때문이다.

따라서 scrollTop 속성에 의존하여 scroll event를 fire하는 경우 오류가 발생할 수 있다.
케이스별로 정리하면 다음과 같다.

1. scroll 이 최하단에 위치할 때
	1. 정상 케이스
		1. scrollTop === scrollHeight - clientHeight
	2. 비정상 케이스
		1. scrollTop - 0.42xxxx.. === scrollHeight - clientHeight
			1. 이상하게도 최대 scrollTop에 아주 약간 못미치는 값까지만 이동 가능하다
2. scroll이 최상단에 위치할 때
	1. 정상 케이스
		1. scrollTop === 0
	2. 비정상 케이스
		1. scrollTop === 0.5714285969734192
			1. 기묘한 숫자가 된다

flex-direction: column-revese일 때는 반대로 작동한다.
1. scroll이 최하단에 위치할 때
	1. 정상 케이스
		1. scrollTop === 0 
	2. 비정상 케이스
		1. scrollTop === 0.5714285969734192
2. scroll 이 최상단에 위치할 때
	1. 정상 케이스
		1. scrollTop === scrollHeight - clientHeight
	2. 비정상 케이스
		1. scrollTop < scrollHeight - clientHeight
			1. 이상하게도 최대 scrollTop에 아주 약간 뚫고 더 높이 이동한다

테스트 환경
- 기기: LG Q52
- OS: Android 10; LM-Q520N Build/QKQ1.200614.002
- chrome version: 92.0.4515.131

20210804

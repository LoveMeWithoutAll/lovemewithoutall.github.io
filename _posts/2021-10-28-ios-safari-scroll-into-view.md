---
layout: single
title: iOS safari에서의 scrollIntoView 메소드 오류 정리
date: 2021-10-28 18:33:30.000000000 +09:00
type: post
header:
    teaser: "https://raw.githubusercontent.com/MoonHighway/learning-react/second-edition/learning-react.jpg"
    image: "https://raw.githubusercontent.com/MoonHighway/learning-react/second-edition/learning-react.jpg"
categories:
- IT
tags: [iOS]
---

1. 요약: safari는 scrollIntoView의 모든 option을 지원하지 않음
	1. reference: https://caniuse.com/?search=scrollIntoView
	2. safari 13, 14에서 test함
2. 오류 목록
	1. behavior: 'smooth' 옵션을 지원하지 않음
		1. 현상: Scroll 동작하지 않음
		2. 해결책: behavior: 'auto' 를 쓸 것
		3. 공식적인 오류
	2. horizontal scroll에서 inline: 'nearest'가 비정상 동작
		1. 현상: 3개 이상의 Element 중 하나로 scrollIntoView 메소드를 발동시키는 경우, 가운데에 위치한(다른 element 사이에 위치한) element로는 스크롤 불가
		2. 해결책: inline: 'end' 를 사용
		3. 알려지지 않은 오류

20210729

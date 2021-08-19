---
layout: single
title: scrollable container에서 dragging 중에는 mouse wheel이 동작하지 않는 오류
date: 2021-08-05 22:05:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/22/3-Tasten-Maus_Microsoft.jpg/440px-3-Tasten-Maus_Microsoft.jpg"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/22/3-Tasten-Maus_Microsoft.jpg/440px-3-Tasten-Maus_Microsoft.jpg"
categories:
- IT
tags: [chrome]
---


1. 현상
	1. 오류 대상: scrollable container
	2. 현상 상세
		1. dragging 중에는 scrollable container에서 mouse wheel event 동작하지 않음
		2. scrollable container는 position fixed or absolute or sticky 인 element와 position relative 인 element 사이에 위치
	3. Browser: macOS Chrome only([Windows Chrome에서는 원래 안 됨](https://lovemewithoutall.github.io/it/scroll-whild-dragging-on-win/))
2. 해결 방법
	4. style 추가
		1. position fixed or absolute or sticky 인 element 와 같은 level에 위치하며, scrollable container를 포함하는 element의 style을 position: 'sticky'로 설정
		2. 이로서 dragging 중 wheel scroll 동작
3. 원인
	5. Chrome의 버그로 추정
		1. position sticky가 wheel scroll 동작에 side effect 발생시킴
		2. position sticky와 position relative가 서로 영향을 미침
4. 의문점
   	1. position: 'sticky'는 top, left 등의 css property와 함께 쓰이지 않으면 동작하지 않아야 하지 않을까요? 버그로 버그를 해결하는 모양새 같아서 상쾌하지 못합니다.
5. 실행환경

| Chrome          | 91.0.4472.114 (공식 빌드) (x86_64)                                                                                        |   |   |   |
|-----------------|---------------------------------------------------------------------------------------------------------------------------|---|---|---|
| 개정            | 4bb19460e8d88c3446b360b0df8fd991fee49c0b-refs/branch-heads/4472@{#1496}                                                   |   |   |   |
| OS              | V8 9.1.269.36                                                                                                             |   |   |   |
| 사용자 에이전트 | Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36 |   |   |   |
| 명령줄          | --no-first-run --disable-fre --no-default-browser-check --flag-switches-begin --flag-switches-end about:blank             |   |   |   |



20210723

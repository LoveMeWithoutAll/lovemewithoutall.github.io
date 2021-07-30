---
layout: single
title: Google chrome browser의 drag ghost image 오류
date: 2021-07-30 19:05:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/drag-ghost-image.png"
    image: "/assets/images/drag-ghost-image.png"
categories:
- IT
tags: [chrome]
---

1. 현상
- 오류 대상: drag ghost image
- 현상 상세
	- ghost image의 크기가 drag target에 한정되지 않음
	- ghost image가 scroll이 하단으로 내려간 만큼의 크기의 scrollable container 하단에 위치한 래스터 이미지를  포함
- 재현 방법: scrollable container에서 scroll을 하단으로 이동시킨 뒤 drag
- Browser: Chrome only
- capture
  ![ghost-image](/assets/images/drag-ghost-image.png)
1. 해결 방법
- style  추가
	- 추가한 style: position: 'sticky' or 'relative'
	- style 추가한 위치: draggable list의 상위 element에 style 추가
3. 원인
- Chrome의 버그로 추정
	- CSS position이 draggable list의  ghost image에 영향을 미치면 안된다
4. 의문점
- position: 'sticky'
	- sticky는 top, left 등의 offset이 설정되어 있지 않으면 무의미하다고 알고 있음
	- sticky에 offset을 설정하지 않으면 relative와 동일한 효과를 내는가?
	- 이러한 동작은 chromium의 오류로 보임
5. 실행환경


| Chrome          | 91.0.4472.114 (공식 빌드) (x86_64)                                                                                        |   |   |   |
|-----------------|---------------------------------------------------------------------------------------------------------------------------|---|---|---|
| 개정            | 4bb19460e8d88c3446b360b0df8fd991fee49c0b-refs/branch-heads/4472@{#1496}                                                   |   |   |   |
| OS              | V8 9.1.269.36                                                                                                             |   |   |   |
| 사용자 에이전트 | Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36 |   |   |   |
| 명령줄          | --no-first-run --disable-fre --no-default-browser-check --flag-switches-begin --flag-switches-end about:blank             |   |   |   |



20210723

---
layout: single
title: safari의 bounce scroll 문제(2)
date: 2021-10-14 00:48:30.000000000 +09:00
type: post
header:
    teaser: "https://www.google.com/url?sa=i&url=https%3A%2F%2Fko.m.wikipedia.org%2Fwiki%2F%25ED%258C%258C%25EC%259D%25BC%3ASafari_browser_logo.svg&psig=AOvVaw38ko7eFvwyGAfcECkpODrl&ust=1634226670836000&source=images&cd=vfe&ved=0CAsQjRxqFwoTCJC4hvbex_MCFQAAAAAdAAAAABAD"
    image: "https://www.google.com/url?sa=i&url=https%3A%2F%2Fko.m.wikipedia.org%2Fwiki%2F%25ED%258C%258C%25EC%259D%25BC%3ASafari_browser_logo.svg&psig=AOvVaw38ko7eFvwyGAfcECkpODrl&ust=1634226670836000&source=images&cd=vfe&ved=0CAsQjRxqFwoTCJC4hvbex_MCFQAAAAAdAAAAABAD"
categories:
- IT
tags: [frontend]
---

iOS는 스크롤 파워가 강할 때 bounce scroll effect를 낸다. 보기 좋은 UX지만, scrollTop을 기준으로 event를 발생시키려면 문제가 발생한다.

스크롤 방향별 케이스
1. 스크롤 최하단에 도달할 경우
	1. scrollTop이 scrollable container 최하단을 뚫고 나간다
		1. scrollTop이 scrollHeight - clientHeight보다 커진다
2. 스크롤 최상단에 도달할 경우
	1. scrollTop이 scrollable container 최하단을 뚫고 나간다
		1. scrollTop이 0보다 작아진다

 특히 scrollable container의 clientHeight가 작은 경우, bounce scroll effect의 영향력이 상대적으로 크기 때문에 문제가 심각해진다. bounce scroll effect는 항상 똑같은 값만큼(clientHeight과 무관) scrollTop을 돌출시킨다.  따라서 clientHeight 값에 비해 bounce scroll effect로 인하여 돌출되는 scrollTop값이 상대적으로 커진다. scrollTop와 clientHeight를 비교하여 scroll의 위치를 확인하는 JS code에서 이러한 문제가 두드러진다. 예를 들어 infinite scroll을 사용한다면 bounce scroll effect 때문에 scroll event가 폭발적으로 발동하는 모습을 볼 수 있다.

이러한 문제를 해결하기 위하여 bounce scroll을 없애기 위한 다양한 방법이 존재한다.

방법별 정리
- CSS를 사용하는 방법
	- 대체로 동작하지 않는다.
		- 적어도 body가 아닌 inner container의 bounce scroll을 통제하려던 나의 경우는 모두 실패했다.
		- CSS를 사용하는 방법은 브라우저 버전 업데이트와 함께 동작하지 않을 수 있다. 검색 결과 그런 사례가 존재한다.
	- 그러니 사용하지 말 것을 추천
- JS를 사용하는 방법
	- 모든 scroll event를 JS가 감지하기란 불가능하다.
		- JS 코드 동작 속도보다 scroll의 동작 속도가 빠르기 때문이다.
		- touchmove event를 완전히 차단하지 않는 한 적용 불가능하다.
			- 이 방법을 쓰면 모바일 브라우저의 스크롤을 동작시킬 수 없다.
	- 그러니 사용하지 않는 방법을 추천

따라서 아주 간단한 방법을 추천한다.

첫째, bounce scroll effect를 연착륙 시키는 것이다. bounce scroll effect는 사용자의 touch 강도에 따른 관성력을 기준으로 발생한다. 관성력은 그로 인해 움직이는 scroll이 길수록 약해진다. 따라서 clientHeight가 크거나, scrollHeight가 크면 bounce scroll effect로 인해 scrollTop이 돌출하는 현상이 최소화 된다.

둘째, scroll event를 call stack에 바로 넣지 말라. setTimeout(task queue)나 promise(microtask queue)  혹은 requestAnimationFrame(animation frame)을 사용하라. 완벽한 해결책은 아니지만 scroll event가 scroll event를 연쇄적으로 발동시키는 현상은 막을 수 있다.

20210805
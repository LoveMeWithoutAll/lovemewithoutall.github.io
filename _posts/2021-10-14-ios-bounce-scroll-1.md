---
layout: single
title: safari의 bounce scroll 문제(1)
date: 2021-10-14 00:46:30.000000000 +09:00
type: post
header:
    teaser: "https://www.google.com/url?sa=i&url=https%3A%2F%2Fko.m.wikipedia.org%2Fwiki%2F%25ED%258C%258C%25EC%259D%25BC%3ASafari_browser_logo.svg&psig=AOvVaw38ko7eFvwyGAfcECkpODrl&ust=1634226670836000&source=images&cd=vfe&ved=0CAsQjRxqFwoTCJC4hvbex_MCFQAAAAAdAAAAABAD"
    image: "https://www.google.com/url?sa=i&url=https%3A%2F%2Fko.m.wikipedia.org%2Fwiki%2F%25ED%258C%258C%25EC%259D%25BC%3ASafari_browser_logo.svg&psig=AOvVaw38ko7eFvwyGAfcECkpODrl&ust=1634226670836000&source=images&cd=vfe&ved=0CAsQjRxqFwoTCJC4hvbex_MCFQAAAAAdAAAAABAD"
categories:
- IT
tags: [frontend]
---

safari에서 scroll event를 다루다보면 괴상한 동작을 발견한다. scrollable container 최상단이 고무줄처럼 튕기는 UI인데, 이를 iOS의 native-style scrolling이라고 한다(출처: https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariCSSRef/Articles/StandardCSSProperties.html#//apple_ref/css/property/-webkit-overflow-scrolling).  이를 bounce scroll이라고 하자(많이들 이렇게 부른다). 

발생 기기 및 브라우저
- desktop safari
	- 기본적으로 발생하지는 않음
	- 관성 스크롤링이 적용되는 기기(예: macOS touchpad)에서 한정적으로 발생함
- iOS safari: touch를 이용한 scroll시 기본적으로 발생

scrollable container의 scroll-bar가 
- 최상단에 위치했을 때
	- bounce scroll이 동작하면
		- scrollTop이 0보다 작아진다.
- 최하단에 위치했을 때
	- bounce scroll이 동작하면
		- scrollTop이 scrollHeight - clientHeight 보다 커진다.

bounce scroll이 동작하는지 여부를 확인하기 위한 코드는 다음과 같다.

```javascript
const isBounceScroll = (div) => {
    if (reverse) { // scroll이 reverse(flex-direction: column-reverse) 모드일 때
      if (div.scrollTop > 0) return true; // scroll 최하단을 뚫음
      if (div.scrollTop < div.clientHeight - div.scrollHeight) return true; // scroll 최상단을 뚫음
    } else { // 일반적인 스크롤 방향일 때
      if (div.scrollTop < 0) return true; // scroll 최상단을 뚫음
      if (div.scrollTop > div.scrollHeight - div.clientHeight) return true; // scroll 최하단을 뚫음
    }
    return false;
}
```

bounce scroll이 동작하는 것을 확인했다면 적당한 방어 코드를 추가하면 된다. 예를 들어 infinte scroll에서 bounce scroll 동작하면 scroll event handler의 실행을 막아서 scroll이 휙~ 움직이는 버그를 해결할 수 있다.

그렇다면 denouce가 해결책이 될 수 있을까? denouce를 쓰면 timeout으로 지정한 시간 이후 side effect를 경험할 수 있다. 그러니 좀 더 살펴보자.

실행 환경
- iOS 14.6
- iPhone 12

20210804

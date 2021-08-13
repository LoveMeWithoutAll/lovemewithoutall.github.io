---
layout: single
title: Content Security Policy(CSP)
date: 2021-08-13 21:30:30.000000000 +09:00
type: post
header:
    teaser: "https://developers.google.com/web/fundamentals/security/csp/images/csp-error.png?hl=ko"
    image: "https://developers.google.com/web/fundamentals/security/csp/images/csp-error.png?hl=ko"
categories:
- IT
tags: [HTTP]
---

# Content Security Policy(CSP) 정리

1. http header에 들어가는 정보
2. 보안을 위한 white list
3. http header에 애당초 넣지 않았다면 csp 때문에 무언가 동작하지 않는 그런 일은 발생하지 않는다
	1. white list가 없으니 모두 허용이다
4. 서버에서 http header 설정으로 넣어줘야 한다
	2. apache web server에서 넣어도 된다
5. html meta tag로 넣어도 된다)****
	3. meta tag로 넣는 방법은 strict 강도가 header에 csp를 넣는 방법보다 약하다
		1. unsafe-inline이 명시되어 있지 않아도, inline javascript(react build output file 같은)를 실행할 수 있다.
6. 보안을 위해서는 csp 설정을 하는 것이 좋다
	4. 당연하다
7. 웹브라우저마다 지원 정도가 다르다
	5. safari는 전부 지원을 못한다
		1. 그러니 브라우저에 따라 적절히 apache web server에서 header 설정을 분기 처리해야 한다

참고: [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

20210729
	
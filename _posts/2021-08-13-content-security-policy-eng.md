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

# Content Security Policy (CSP) Notes

1. Information that goes into the HTTP header
2. A whitelist for security
3. If you never put it into the HTTP header in the first place, you won't run into a situation where something stops working because of CSP
	1. Since there is no whitelist, everything is allowed
4. It must be added on the server via the HTTP header setting
	2. You can also add it in the Apache web server
5. You can also add it via an HTML meta tag)****
	3. Adding it via a meta tag is less strict than adding the CSP via the header
		1. Even if unsafe-inline is not specified, you can still run inline JavaScript (such as a React build output file).
6. For security, it's better to set up a CSP
	4. Of course
7. The level of support differs from browser to browser
	5. Safari does not support all of it
		1. So depending on the browser, you need to branch the header settings appropriately in the Apache web server

Reference: [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

20210729

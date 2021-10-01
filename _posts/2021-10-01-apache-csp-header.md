---
layout: single
title: Apache HTTP server에 CSP(Content Security Policy) header 설정
date: 2021-10-01 23:16:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
categories:
- IT
tags: [Apache HTTP Server]
---

httpd.conf에다가

```apache
<IfModule mod_headers.c>
	Header set Content-Security-Policy "media-src 'blob:'; connect-src 'self' '*.yourdomain.com'; script-src 'self';"
</IfModule>
```

여러 개의 옵션을 넣을 때는 "" 쌍따옴표를 잊지 말도록...

20210723

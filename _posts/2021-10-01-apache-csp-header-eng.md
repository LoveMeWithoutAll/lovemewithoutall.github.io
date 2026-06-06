---
layout: single
title: Setting the CSP (Content Security Policy) header on the Apache HTTP server
date: 2021-10-01 23:16:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
categories:
- IT
tags: [Apache HTTP Server]
---

Add this to `httpd.conf`:

```apache
<IfModule mod_headers.c>
	Header set Content-Security-Policy "media-src 'blob:'; connect-src 'self' '*.yourdomain.com'; script-src 'self';"
</IfModule>
```

When adding multiple options, don't forget the double quotes ("")...

20210723

---
layout: single
title: Configuring Apache HTTP Server depend on web browser
date: 2021-09-15 19:18:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
categories:
- IT
tags: [Apache HTTP Server]
---

You can configure Apache HTTP Server's response header depend on web browser. Example is below.

```
# httpd.conf
<If “%{HTTP_USER_AGENT} =~ /Chrome/“>
    Header set blah blah
</If>
<ElseIf “%{HTTP_USER_AGENT} =~ /Safari/“>
    Header set whoolah whoolah 
</ElseIf>
```

20210915

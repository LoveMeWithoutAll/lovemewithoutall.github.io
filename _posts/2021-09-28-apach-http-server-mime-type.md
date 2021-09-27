---
layout: single
title: Apache HTTP Server에 mime type 추가
date: 2021-09-28 00:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
categories:
- IT
tags: [Apache HTTP Server]
---

Apache HTTP Server에 mime type을 추가하려면?

`IfModule mime_module`에서 `AddType type .확장자1 .확장자2`를 추가하면 된다.

아래는 예시

```apache
<!-- httpd.conf -->
<IfModule mime_module>
    TypesConfig conf/mime.types

    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz

    AddType application/x-httpd-php .php .php3 .inc .ph .htm
    AddType application/x-httpd-php-source .phps
</IfModule>
```

20210928
---
layout: single
title: Adding a MIME type to Apache HTTP Server
date: 2021-09-28 00:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/10/Apache_HTTP_server_logo_%282019-present%29.svg/1200px-Apache_HTTP_server_logo_%282019-present%29.svg.png"
categories:
- IT
tags: [Apache HTTP Server]
---

How do you add a MIME type to Apache HTTP Server?

Just add `AddType type .extension1 .extension2` inside `IfModule mime_module`.

Here is an example:

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

---
layout: single
title: Replacing \r\n (Newline Characters) in MSSQL
date: 2018-12-10 12:05:30.000000000 +09:00
type: post
header:
    teaser: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
    image: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
categories:
- IT
tags: [MSSQL, Database]
---

While working on replacing an HTML textarea-based board with TinyMCE, I ran into an annoying issue because the [newline characters were stored in the DB as `CRLF(\r\n)`](https://libsora.so/posts/what-is-textarea-newline/).

If you want to replace `CRLF` in MSSQL, you need to `REPLACE` `CHAR(13)` and `CHAR(10)`.

The QUERY below is an example of using `REPLACE` to change `CRLF` to a `<br />` tag.

```sql
update tbl_board
set contents = replace(replace(REPLACE(contents, CHAR(13), ' '), char(10), ' '), '  ' , '<br />')
```

Works like a charm!

* Reference: [What is the newline character in an HTML textarea?](https://libsora.so/posts/what-is-textarea-newline/)

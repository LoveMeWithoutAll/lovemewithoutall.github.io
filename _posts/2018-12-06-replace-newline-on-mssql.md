---
layout: single
title: MSSQL에서 \r\n(개행문자) replace
date: 2018-12-10 12:05:30.000000000 +09:00
type: post
header:
    teaser: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
    image: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
categories:
- IT
tags: [MSSQL, Database]
---

HTML textarea로 만들어진 게시판을 TinyMCE로 교체하는 작업을 하던 도중, [개행문자가 DB에 `CRLF(\r\n)`로 저장](https://libsora.so/posts/what-is-textarea-newline/)되어 귀찮은 일이 생겼다.

MSSQL에서 `CRLF`를 변경하고 싶다면 `CHAR(13)`과 `CHAR(10)`을 `REPLACE` 해줘야 한다.

아래 QUERY는 `CRLF`를 `<br />` 태그로 `REPLACE`하는 예제다.

```sql
update tbl_board
set contents = replace(replace(REPLACE(contents, CHAR(13), ' '), char(10), ' '), '  ' , '<br />')
```

잘 돌아간다!

* 참고: [HTML textarea의 개행문자는 무엇일까?](https://libsora.so/posts/what-is-textarea-newline/)

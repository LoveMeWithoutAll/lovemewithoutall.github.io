---
layout: single
title: MSSQL Error code 213 Column name or number of supplied values does not match table definition
date: 2021-03-30 12:16:30.000000000 +09:00
type: post
header:
    teaser: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
    image: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
categories:
- IT
tags: [SQL]
---

When dealing with ms-sql, there are times when you encounter the following errors...

```sql
[S0001][213] Column name or number of supplied values does not match table definition.
```

Of course, it is an error that occurs when the number of table columns is not correct. However it can sometiems be a error caused by a `table trigger`!

Be careful, after modify table! You shoud modify table trigger after alter table column especially add and delete.

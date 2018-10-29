---
layout: single
title: MSSQL active connection 확인
date: 2018-10-29 11:20:30.000000000 +09:00
type: post
header:
    teaser: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
    image: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
categories:
- IT
tags: [Database]
---

Microsoft SQL Server에서 프로그램당 active connection의 숫자를 확인하자.

```sql
SELECT PROGRAM_NAME, HOSTNAME, STATUS, COUNT(DBID) AS NumberOfConnections
FROM sys.sysprocesses
WHERE PROGRAM_NAME = '프로그램명(ex: node-mssql)'
GROUP BY PROGRAM_NAME, HOSTNAME, STATUS;
```

참고: https://docs.microsoft.com/en-us/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql?view=sql-server-2017
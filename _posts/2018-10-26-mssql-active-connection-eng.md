---
layout: single
title: Checking active connections in MSSQL
date: 2018-10-29 11:20:30.000000000 +09:00
type: post
header:
    teaser: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
    image: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
categories:
- IT
tags: [Database]
---

Let's check the number of active connections per program in Microsoft SQL Server.

```sql
SELECT PROGRAM_NAME, HOSTNAME, STATUS, COUNT(DBID) AS NumberOfConnections
FROM sys.sysprocesses
WHERE PROGRAM_NAME = 'program name (ex: node-mssql)'
GROUP BY PROGRAM_NAME, HOSTNAME, STATUS;
```

Reference: https://docs.microsoft.com/en-us/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql?view=sql-server-2017

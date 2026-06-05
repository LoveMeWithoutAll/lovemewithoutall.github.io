---
layout: single
title: MSSQL OPENQUERY Special Character Error
date: 2019-10-16 22:33:30.000000000 +09:00
type: post
header:
    teaser: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
    image: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
categories:
- IT
tags: [SQL]
---

When running OPENQUERY in MSSQL, you may run into an error like this.

```
The OLE DB provider "OraOLEDB.Oracle" for linked server "DB_SERVER_NAME" returned message "ORA-01756: quoted string not properly terminated".
Msg 7323, Level 16, State 2, Line 22
An error occurred while sending the query text to the OLE DB provider "OraOLEDB.Oracle" for linked server "DB_SERVER_NAME".
```

This error happens because special characters such as *''* are not transmitted correctly through OPENQUERY when you use built-in functions like NVL.

The solution is to wrap *''* with *''* as shown below.

```SQL
insert into MSSQL_TABLE_NAME
	SELECT * FROM OPENQUERY(DB_SERVER_NAME, 'SELECT NVL(ID, '''') AS ID
											FROM ORACLE_TABLE_NAME')
;
```

The end!

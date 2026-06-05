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

When using OPENQUERY in MSSQL, you may run into an error like this.

```
The OLE DB provider "OraOLEDB.Oracle" for linked server "DB_SERVER_NAME" returned the message "ORA-01756: quoted string not properly terminated".
Msg 7323, Level 16, State 2, Line 22
An error occurred while sending the query text to the OLE DB provider "OraOLEDB.Oracle" for linked server "DB_SERVER_NAME".
```

This error occurs because, when using built-in functions such as NVL, special characters like *''* are not transmitted correctly through OPENQUERY.

The fix is to escape each single quote by doubling it, as shown below.

```SQL
insert into MSSQL_TABLE_NAME
	SELECT * FROM OPENQUERY(DB_SERVER_NAME, 'SELECT NVL(ID, '''') AS ID
											FROM ORACLE_TABLE_NAME')
;
```

That's it!

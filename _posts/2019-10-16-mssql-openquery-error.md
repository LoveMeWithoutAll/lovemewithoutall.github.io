---
layout: single
title: MSSQL OPENQUERY 특수문자 오류
date: 2019-10-16 22:33:30.000000000 +09:00
type: post
header:
    teaser: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
    image: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
categories:
- IT
tags: [SQL]
---

MSSQL에서 OPENQUERY를 할 때 이런 오류가 발생할 수 있다.

```
연결된 서버 "DB_SERVER_NAME"의 OLE DB 공급자 "OraOLEDB.Oracle"이(가) 메시지 "ORA-01756: 단일 인용부를 지정해 주십시오"을(를) 반환했습니다.
메시지 7323, 수준 16, 상태 2, 줄 22
연결된 서버 "DB_SERVER_NAME"의 OLE DB 공급자 "OraOLEDB.Oracle"에 쿼리 텍스트를 전송하는 동안 오류가 발생했습니다.
```

NVL 등의 내장 함수를 사용할 때 *''* 등의 특수문자가 OPENQUERY에서 제대로 전송되지 않아서 발생하는 오류다.

해결책은 아래와 같이 *''*를 *''*로 감싸주면 된다.

```SQL
insert into MSSQL_TABLE_NAME
	SELECT * FROM OPENQUERY(DB_SERVER_NAME, 'SELECT NVL(ID, '''') AS ID
											FROM ORACLE_TABLE_NAME')
;
```

끝!

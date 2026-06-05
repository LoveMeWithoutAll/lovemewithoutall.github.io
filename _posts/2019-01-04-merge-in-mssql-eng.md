---
layout: single
title: Merge in MSSQL
date: 2019-01-04 15:05:30.000000000 +09:00
type: post
header:
    teaser: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
    image: "https://vignette.wikia.nocookie.net/logopedia/images/c/cd/MicrosoftSQLServer.png/revision/latest?cb=20150614233628"
categories:
- IT
tags: [SQL]
---

For seven years I've memorized this and forgotten it over and over, until I finally gave up...

```sql
DECLARE @INHAID  NVARCHAR(50) = '217255'
DECLARE @ACCOUNT NVARCHAR(50) = 'what@mail.com'

MERGE hpDEPTDB.dbo.OFFICE_ACCOUNT A
USING (SELECT @INHAID as INHAID) B
ON A.INHAID = B.INHAID
WHEN MATCHED THEN
  UPDATE
  SET A.OFFICE_ACCOUNT = @ACCOUNT,
      A.LDATE          = GETDATE()
WHEN NOT MATCHED THEN
  INSERT (INHAID, OFFICE_ACCOUNT, LDATE, CDATE)
  VALUES (@INHAID, @ACCOUNT, GETDATE(), GETDATE())
;
```

---
layout: single
title: Visual Studio Code(vs code)에서 MSSQL 사용하기
date: 2018-06-19 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Visual_Studio_Code_1.18_icon.svg/1200px-Visual_Studio_Code_1.18_icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Visual_Studio_Code_1.18_icon.svg/1200px-Visual_Studio_Code_1.18_icon.svg.png"
categories:
- IT
tags: [Tool]
---

[Visual Studio Code](https://code.visualstudio.com/)로 [Microsoft SQL Server](https://www.microsoft.com/ko-kr/sql-server/sql-server-2017?&OCID=AID631209_SEM_GyihB46V&gclid=CjwKCAjw06LZBRBNEiwA2vgMVSjablWckGVPWpHk_d-zlAtmrDN5WJ4_20Ytst2XIbVTWE5PKjxGVRoCccIQAvD_BwE)(이하 mssql)을 사용하고 있는데, 아주 좋다. [여기](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)서 설치하면 된다. 무거운 [SQL Server Management Studio](https://docs.microsoft.com/ko-kr/sql/ssms/sql-server-management-studio-ssms?view=sql-server-2017)를 실행할 것 없이, mssql을 가볍고 편리하게 사용할 수 있다.

간단한 CRUD 쿼리 실행 등의 기능은 튜토리얼에 다 나와있으니 따라하면 된다. 하지만 이 뿐만이 아니다. 튜토리얼에 안나온 기능 중 내가 자주 쓰는 기능을 정리해보았다.

## 1. 프로시져와 함수 정의 확인
exec 뒤에 프로시져나 함수명을 치고, 마우스 우클릭 후 정의 피킹(peeking)을 누른다.
```sql
exec getUserName -- 정의 피킹(Alt+F12)을 하면 프로시저의 정의를 보여준다.
```
![definition peeking](/assets/images/2018-06-19-mssql-with-vscode/mssql-with-vscode.png)

## 2. 빠진 파라미터 확인
프로시져나 함수를 실행할 때 필요한 파리미터를 확인하고 싶으면 이렇게 하면 된다.

 1. F8을 누르거나

 ![f8](/assets/images/2018-06-19-mssql-with-vscode/f8.png)

 2. 1초 기다리면 된다.
```sql
exec getUserName -- 이렇게 쳐놓고 1초만 기다리면 아래 화면이 뜬다.
```
 ![wait-1sec](/assets/images/2018-06-19-mssql-with-vscode/wait-1sec.png)

 끝!
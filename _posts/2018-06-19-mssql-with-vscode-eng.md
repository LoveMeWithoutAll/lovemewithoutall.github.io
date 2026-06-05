---
layout: single
title: Using MSSQL in Visual Studio Code (VS Code)
date: 2018-06-19 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Visual_Studio_Code_1.18_icon.svg/1200px-Visual_Studio_Code_1.18_icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Visual_Studio_Code_1.18_icon.svg/1200px-Visual_Studio_Code_1.18_icon.svg.png"
categories:
- IT
tags: [Tool]
---

I've been using [Microsoft SQL Server](https://www.microsoft.com/ko-kr/sql-server/sql-server-2017?&OCID=AID631209_SEM_GyihB46V&gclid=CjwKCAjw06LZBRBNEiwA2vgMVSjablWckGVPWpHk_d-zlAtmrDN5WJ4_20Ytst2XIbVTWE5PKjxGVRoCccIQAvD_BwE) (hereafter mssql) with [Visual Studio Code](https://code.visualstudio.com/), and it's really great. You can install it from [here](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql). Without having to launch the heavy [SQL Server Management Studio](https://docs.microsoft.com/ko-kr/sql/ssms/sql-server-management-studio-ssms?view=sql-server-2017), you can use mssql in a lightweight and convenient way.

Basic features like running simple CRUD queries are all covered in the tutorial, so you can just follow along. But that's not all. Among the features not mentioned in the [tutorial](https://docs.microsoft.com/ko-kr/sql/linux/sql-server-linux-develop-use-vscode?view=sql-server-linux-2017), I've put together the ones I use frequently.

## 1. Checking the definitions of procedures, functions & tables

1. Procedures and functions

After `exec`, type the name of the procedure or function, right-click, and select Peek Definition (peeking).

```sql
exec getUserName -- Peeking the definition (Alt+F12) shows the definition of the procedure.
```

![definition peeking](/assets/images/2018-06-19-mssql-with-vscode/mssql-with-vscode.png)

Selecting Go to Definition (F12) opens a new window and displays the create statement for that function or procedure.

2. Tables

Of course, you can check table definitions the same way.

```sql
select *
from users -- Peeking the definition here (Alt+F12) shows the table definition.
```

Go to Definition (F12) also brings up the create statement in a new window, just like with procedures.

## 2. Checking missing parameters
If you want to check which parameters are required when running a procedure or function, here's how.

 1. Press F8, or

 ![f8](/assets/images/2018-06-19-mssql-with-vscode/f8.png)

 2. Just wait one second.
```sql
exec getUserName -- Type this and wait just one second, and the screen below appears.
```
 ![wait-1sec](/assets/images/2018-06-19-mssql-with-vscode/wait-1sec.png)

 Done!

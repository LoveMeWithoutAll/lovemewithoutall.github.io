---
layout: single
title: .Net Core 3 Web API
date: 2019-10-15 22:33:30.000000000 +09:00
type: post
header:
    teaser: ""
    image: ""
categories:
- IT
tags: [.Net]
---

1. 프로젝트 시작

https://docs.microsoft.com/ko-kr/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.0&tabs=visual-studio

2. git init
프로젝트 폴더 안에서 git init

3. DB 연결

* Entity Framework
ORM을 위해 [Entity Framework](https://docs.microsoft.com/ko-kr/ef/#pivot=entityfmwk)를 설치한다

```powershell
# https://docs.microsoft.com/ko-kr/ef/core/get-started/?tabs=visual-studio#install-entity-framework-core
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 3.0.0
```

appsettings.json은 
[connectionstrings.com](https://www.connectionstrings.com/sql-server/)에서 확인

4. 모델 클래스 추가

각 컬럼 정보 넣기

```csharp
using System.ComponentModel.DataAnnotations;

public class VisitorCode
{
    [Key]
    public string V_CODE { get; set; }
    public string V_CODETYPE { get; set; }
    public string V_CODENAME { get; set; }
}
```

5. DbContext

```csharp
public class VisitorContext : DbContext
{
    public DbSet<VisitorContext> VisitorCodes { get; set; }

    public VisitorContext(DbContextOptions<VisitorContext> options)
        : base(options)
    {
    }
}
````

6. StartUp 등록

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    services.AddDbContext<VisitorContext>(options =>
        options.UseSqlServer("Server=164.246.66.33,1433;Database=Haksa;Trusted_Connection=True;User Id=sa;Password=dlgkrwh"));
}
```

MSSQL 기본 포트는 1433이다.

7. Controller

Controllers 폴더를 마우스 오른쪽 단추로 클릭합니다.
추가 > 스캐폴드 항목 새로 만들기를 선택합니다.
Entity Framework를 사용하며 동작이 포함된 API 컨트롤러를 선택하고 추가를 선택합니다.
Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가 대화 상자에서:
    모델 클래스에서 TodoItem (TodoApi.Models) 을 선택합니다.
    데이터 컨텍스트 클래스에서 TodoContext (TodoApi.Models) 를 선택합니다.
    추가를 선택합니다.

```csharp

```
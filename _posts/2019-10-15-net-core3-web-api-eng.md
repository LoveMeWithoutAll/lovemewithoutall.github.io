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

1. Starting the project

https://docs.microsoft.com/ko-kr/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.0&tabs=visual-studio

2. git init
Run git init inside the project folder.

3. Connecting the DB

* Entity Framework
Install [Entity Framework](https://docs.microsoft.com/ko-kr/ef/#pivot=entityfmwk) for the ORM.

```powershell
# https://docs.microsoft.com/ko-kr/ef/core/get-started/?tabs=visual-studio#install-entity-framework-core
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 3.0.0
```

For appsettings.json,
check [connectionstrings.com](https://www.connectionstrings.com/sql-server/)

4. Adding the model class

Add each column's information.

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

6. Registering in Startup

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    services.AddDbContext<VisitorContext>(options =>
        options.UseSqlServer("Server=164.246.66.33,1433;Database=Haksa;Trusted_Connection=True;User Id=sa;Password=dlgkrwh"));
}
```

The default MSSQL port is 1433.

7. Controller

Right-click the Controllers folder.
Select Add > New Scaffolded Item.
Select "API Controller with actions, using Entity Framework" and select Add.
In the "Add API Controller with actions, using Entity Framework" dialog:
    In Model class, select TodoItem (TodoApi.Models).
    In Data context class, select TodoContext (TodoApi.Models).
    Select Add.

```csharp

```
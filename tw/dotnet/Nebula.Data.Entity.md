Nebula.Data.Entity
================

目前版本支援 Entity Framework 5.0。

## Install
----------------

     Install-Package Nebula.Data.Entity

## web.config
----------------

透過修改 `defaultConnectionFactory` 的方式，讓 EntityFramework 可以取得存取資料庫資源的連線字串  
將預設的 `System.Data.Entity.Infrastructure.LocalDbConnectionFactory` 改為 `Nebula.Data.Entity.NebulaDbConnectionFactory`

```xml
<entityFramework>
    <defaultConnectionFactory type="Nebula.Data.Entity.NebulaDbConnectionFactory, Nebula.Data.Entity" />

    <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
    </providers>
</entityFramework>
```

## DbContext 範例  
----------------

```csharp
public class NorthwindDbContext : DbContext
{
    public NorthwindDbContext():
        base("Northwind")
    {
        // Code..
    }

    public IDbSet<Catelog> Categories { get; set; }
}

[Table("Categories")]
public class Catelog {
    [Key]
    public int CategoryID { get; set; }
    public string CategoryName { get; set; }
}
```

base 建構子參數，為資料庫名稱

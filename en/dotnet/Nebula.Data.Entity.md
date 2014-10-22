Nebula.Data.Entity
================

The currently supported Entity Framework version is 5.0.  

## Install
----------------

     Install-Package Nebula.Data.Entity

## web.config
----------------

By amending the `defaultConnectionFactory` way for EntityFramework can get access database connection string resources
The default `System.Data.Entity.Infrastructure.LocalDbConnectionFactory` to `Nebula.Data.Entity.NebulaDbConnectionFactory`  

```xml
<entityFramework>
    <defaultConnectionFactory type="Nebula.Data.Entity.NebulaDbConnectionFactory, Nebula.Data.Entity" />

    <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
    </providers>
</entityFramework>
```

## DbContext sample  
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

Base constructor with "DBName" parameter.

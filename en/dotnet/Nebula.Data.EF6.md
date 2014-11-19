Nebula.Data.EF6
================

Support Entity Framework 6  
In NEBULA in the provided Database Resource, can be accessed through the following ways.
Do not need to set their own connection string, you can get through the corresponding resource connection string SDK.  

## Install
----------------

	Install-Package Nebula.Data.EF6

## web.confg
----------------

By amending the `defaultConnectionFactory` way for EntityFramework can get access database connection string resources
The default `System.Data.Entity.Infrastructure.LocalDbConnectionFactory` to `Nebula.Data.Entity.NebulaDbConnectionFactory`  

```xml
<entityFramework>
    <defaultConnectionFactory type="Nebula.Data.Entity.NebulaDbConnectionFactory, Nebula.Data.EF6" />

    <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
    </providers>
</entityFramework>
```

## Usage
----------------

When implemented `DbContext`, and passed `DbContext` constructor parameters should be the name of the Database Resource
If you test the machine, you can use the config settings of `conenctionStrings`
Simply put `connectionString` name set to the same as you can DbName

```csharp
public class MyDbContext : DbContext {

	public MyDbContext() : base("DbName") {
		// Code
	}
}
```

Local side test, the connection string setting examples

```xml
<configuration>
     <connectionStrings>
        <add name="DbName" connectionString="...ConnectionString..." providerName="System.Data.SqlClient" />
    </connectionStrings>
</configuration>
```

> The local side of the connection string settings after deployment to NEBULA SaaS VM, will use the connection string actual NEBULA environment.
> Unless this database is not managed by NEBULA, but developers build their own database.  

use DbContext:  

```csharp
using (var context = new MyDbContext()) {
	// ..... Code .....
}
```

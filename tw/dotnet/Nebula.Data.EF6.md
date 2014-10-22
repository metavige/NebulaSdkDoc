Nebula.Data.EF6
================

Support Entity Framework 6  
在 NEBULA 中，所提供的 Database Resource，可以透過以下方式來存取  
不需要自行設定連線字串，可以透過 SDK 來取得對應的資源連線字串

## Install 
----------------

	Install-Package Nebula.Data.EF6

## web.confg
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

## 使用方式
----------------

在實作 DbContext 的時候，傳給 DbContext 建構子的參數，應為 Database Resource 的名稱  
如果在本機測試，可以使用 config 的 conenctionStrings 設定  
只需要把 connectionString 名稱設定為與 DbName 一樣就可以  

```csharp
public class MyDbContext : DbContext {

	public MyDbContext() : base("DbName") {
		// Code
	}
}
```

本地端測試, 連線字串設定範例

```xml
<configuration>
     <connectionStrings>
        <add name="DbName" connectionString="...ConnectionString..." providerName="System.Data.SqlClient" />
    </connectionStrings>
</configuration>
```

> 本地端設定的連線字串，佈署到 NEBULA SaaS VM 之後，將使用實際 NEBULA 環境的連線字串。  
> 除非此資料庫並非由 NEBULA 管理，而是開發者自行建立的資料庫。  

使用 DbContext:  

```csharp
using (var context = new MyDbContext()) {
	// ..... Code .....
}
```

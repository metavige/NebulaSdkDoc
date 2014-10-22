
Nebula.Module
================

Nebula.Module 是整個 Nebula SDK 的核心模組，所有的 Nebula 基本功能模組都包含在這裡

## Install
----------------

    Install-Package Nebula.Module

## 模組列表
----------------

* [Database Module](Module.Database.md)  
* [Sql Module](Module.Sql.md)
* [Mail Module](Module.Mail.md)
* [Logging Module](Module.Logging.md)
* [Exception Module](Module.Exception.md)
* [Dfs Module](Module.Dfs.md)
* [Office Module](Module.Office.md)
* [Management Module](Module.Management.md)
* [Solution Module](Module.Solution.md)

## AppContext
----------------

`Quanta.PaaS.Module.AppContext` 這個物件，為 Nebula SDK 的主體 ，所有的 Module或與一些與SaaS相關的屬性值，都透過此物件取得。  

目前 `Quanta.PaaS.Module.AppContext` 內部有結合 Unity 2.0。故也可使用 `ServiceLocator` 的機制取得 IoC 的物件。  

### 常用屬性說明
----------------

* Current

	靜態屬性，取得目前環境的 `AppContext`

```csharp
AppContext appContext = AppContext.Current;
```

* SolutionId

	取得此 SaaS 應用程式的 `SolutionId`

```csharp
string solutionId = AppContext.Current.SolutionId;
```

* ProductId

	取得此 SaaS 應用程式在 web.config 中設定的產品代碼

```csharp
string productId = AppContext.Current.ProductId;
```

* ApplicationId

	取得此 SaaS 應用程式在 web.config 設定的應用程式代碼

```csharp
string applicationId = AppContext.Current.ApplicationId;
```

* EnvironmentGuid

	取得 Multi-Tenancy 識別碼，詳情請參考 [Multi-Tenancy](MultiTenancy.md) 的說明

```csharp
string tenancyId = AppContext.Current.EnvironmentGuid;
```

### 常用方法說明
----------------

* GetModule&lt;IModuleInterface&gt;

	目前 Neubla Module 的取得，都需要透過這個方法來取得實體物件

```csharp
using Quanta.PaaS.Module;
using Quanta.PaaS.Module.Database;

public class SampleService
{
	public void Hello()
	{
		var sqlModule = AppContext.Current.GetModule<ISqlModule>();
	}
}
```

* Resolve&lt;RegisterType&gt;

	如果有使用 [BootStrapper](BootStrapper.md) 來做 IoC 註冊，也可透過 `AppContext` 來取得 Instance

```csharp
public interface IService
{
	void Hello();
}

public class Service : IService
{
	public void Hello()
	{
		// Hello!!!
	}
}

public class MyController : Controller
{
	public ActionResult Index()
	{
		var serviceInstance = AppContext.Current.Resolve<IService>();
	}
}
```

## BusinessLayerBootStrapper
----------------

一般 SaaS 應用程式中，會使用以下的 `Quanta.PaaS.Module.BootStrappers.BusinessLayerBootStrapper` 類別繼承實作

`BootStrapper` 的使用與設定，可以參考 [BootStrapper](BootStrapper.md)

以下說明常用的屬性以及方法：

### 常用屬性說明
----------------

* IUnityContainer Container

	提供 [IUnityContainer](http://msdn.microsoft.com/en-us/library/microsoft.practices.unity.iunitycontainer%28v=pandp.20%29.aspx) 物件，若 `BusinessLayerBootStrapper` 提供的方法不足使用，可直接使用 [IUnityContainer](http://msdn.microsoft.com/en-us/library/microsoft.practices.unity.iunitycontainer%28v=pandp.20%29.aspx) 來註冊  

```csharp
public class MyBootStrapper : BusinessLayerBootStrapper
{
	public override void Execute()
	{
		Container.RegisterType<Interface, Type>();
	}
}
```

### 常用方法說明

* void RegisterDataContext<TDataContext>() where TDataContext : PaaSEntityContext

	註冊 Nebula.Module 中的 EntityContext 物件


```csharp
public MyDataConetxt : PaaSEntityContext
{
	public MyDataContext : base("MyDatabase")
	{
	}
}

public class MyBootStrapper : BusinessLayerBootStrapper
{
	public override void Execute()
	{
		RegisterDataContext<MyDataContext>();
	}
}
```

* void RegisterTypeForInterception<TTo, TFrom>() where TFrom : TTo

	註冊類型，`TFrom` 類別必須繼承或實作 `TTO` 類別  
	透過這個方法註冊，Nebula SDK 會協助加上 Transaction Support 、Exception Handler


```csharp
public interface IService
{
	void Hello();
}

public class Service : IService
{
	public void Hello()
	{
		// Hello!!!
	}
}

public class MyBootStrapper : BusinessLayerBootStrapper
{
	public override void Execute()
	{
		RegisterTypeForInterception<IService, Service>();
	}
}
```


* void RegisterTypeForInterception<TTo, TFrom>(LifetimeManager lifetimeManager) where TFrom : TTo

	註冊類型，`TFrom` 類別必須繼承或實作 `TTO` 類別  
	透過這個方法註冊，Nebula SDK 會協助加上 Transaction Support 、Exception Handler

	[LifetimeManager](http://msdn.microsoft.com/en-us/library/microsoft.practices.unity.lifetimemanager%28v=pandp.20%29.aspx) 的使用方式，請參考 [Understanding Lifetime Managers](http://msdn.microsoft.com/en-us/library/ff660872%28v=pandp.20%29.aspx)  

```csharp
public interface IService
{
	void Hello();
}

public class Service : IService
{
	public void Hello()
	{
		// Hello!!!
	}
}

public class MyBootStrapper : BusinessLayerBootStrapper
{
	public override void Execute()
	{
		RegisterTypeForInterception<IService, Service>(new Microsoft.Practices.Unity.PerThreadLifetimeManager());
	}
}
```

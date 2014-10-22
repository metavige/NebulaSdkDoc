
Nebula.Module
================

Nebula.Module is the entire core module of Nebula SDK, all basic module functions of Nebula are contained here.  

## Install
----------------

    Install-Package Nebula.Module

## Module List
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

`Quanta.PaaS.Module.AppContext` object is the main body of Nebula SDK; all modules and SaaS related property values are obtained through this object.

Currently `Quanta.PaaS.Module.AppContext` contains Unity 2.0, hence it can obtain IoC object by `ServiceLocator` mechanism.  

### Common Property Descriptions
----------------

* Current

    Static property, obtain the current environmental `AppContext`  

```csharp
AppContext appContext = AppContext.Current;
```

* SolutionId

    Obtain this SaaS application's `SolutionId`  

```csharp
string solutionId = AppContext.Current.SolutionId;
```

* ProductId

	Obtain ProductId of this SaaS application set in web.config  

```csharp
string productId = AppContext.Current.ProductId;
```

* ApplicationId

	Obtain ApplicationId of this SaaS application set in web.config  

```csharp
string applicationId = AppContext.Current.ApplicationId;
```

* EnvironmentGuid

	Obtain Multi-Tenancy ID, pease read [Multi-Tenancy](MultiTenancy.md) for more information  

```csharp
string tenancyId = AppContext.Current.EnvironmentGuid;
```

### MultiTenancy
----------------

* GetModule&lt;IModuleInterface&gt;

	Get the current Nebula Module instance. All actual module instances must be obtained through this method.  

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

	If you use [BootStrapper] do IoC registration, you can also obtain instance through `AppContext`.  

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

Common SaaS application always use the following `Quanta.PaaS.Module.BootStrappers.BusinessLayerBootStrapper` class to inherit and to implement.  
You can read [BootStrapper] about use and settings of BootStrapper  
The following explains the commonly used properties and methods  

### Common Property Descriptions
----------------

* IUnityContainer Container

	Provides [IUnityContainer](http://msdn.microsoft.com/en-us/library/microsoft.practices.unity.iunitycontainer%28v=pandp.20%29.aspx) object. If there is insufficient methods provided by `BusinessLayerBootStrapper` to use, you can directly use [IUnityContainer](http://msdn.microsoft.com/en-us/library/microsoft.practices.unity.iunitycontainer%28v=pandp.20%29.aspx) to register.  

```csharp
public class MyBootStrapper : BusinessLayerBootStrapper
{
	public override void Execute()
	{
		Container.RegisterType<Interface, Type>();
	}
}
```

### Common Methods Descriptions

* void RegisterDataContext<TDataContext>() where TDataContext : PaaSEntityContext

    Register `EntityContext` object of Nebula.Module  

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

	Register type; `TFrom` class must inherit or implement `TTO` class
	Nebula SDK would assist to add Transaction Support, Exception Handler by registerion through this method  

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

	Register type; `TFrom` class must inherit or implement `TTO` class  
	Nebula SDK would assist to add Transaction Support, Exception Handler by registerion through this method  

	Please read [Understanding Lifetime Managers](http://msdn.microsoft.com/en-us/library/ff660872%28v=pandp.20%29.aspx) for use on [LifetimeManager](http://msdn.microsoft.com/en-us/library/microsoft.practices.unity.lifetimemanager%28v=pandp.20%29.aspx)  


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

[BootStrapper]: <BootStrapper.md>

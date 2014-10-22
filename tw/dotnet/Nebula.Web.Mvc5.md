
Nebula.Web.Mvc5
================

Asp.Net MVC 3.0 之後，有針對 IoC 機制提供了一個 `DependencyResolver` 的方式，來產生 `Controller` 的 Instance  
所以在 Asp.Net MVC 3.0/4.0 的作法，與 Asp.Net MVC 2.0 的方式有所區隔。

## Install
----------------

    Install-Package Nebula.Web.Mvc5

## WebActivator
----------------

在安裝 Nebula.Web.Mvc4 之後，在專案裡面會產生一個目錄 `App_Start`, 裡面會新增一個 `NebulaMvcWebActivator.cs` 檔案。透過了 [WebActivator](https://github.com/davidebbo/WebActivator) 的技術，在啟動 Application 的時候，會自動執行這個程式。

產生出來的 WebActivator 範例如下 :

```csharp
[assembly: WebActivator.PostApplicationStartMethod(typeof(WebApi2.App_Start.NebulaMvcWebActivator), "Init")]

namespace WebApi2.App_Start
{
    using System;
    using System.Linq;
    using System.Web.Mvc;
    using System.Web.Http;

    public class NebulaMvcWebActivator
    {
        /// <summary>
        /// Start PaaS.Web Mvc Application, Prepare IoC Container
        /// </summary>
        public static void Init()
        {
            System.Web.Mvc.DependencyResolver.SetResolver(new Quanta.PaaS.Web.Mvc.UnityDependencyResolver());
            System.Web.Http.GlobalConfiguration.Configuration.DependencyResolver
                = new Quanta.PaaS.Web.Mvc.WebApiUnityDependencyResolver();
        }
    }
}

```

此程式是透過 Nuget 所提供的 [Transformation](http://docs.nuget.org/docs/creating-packages/configuration-file-and-source-code-transformations) 功能產生，所產生出來的程式碼
namespace 會是專案的 namespace。  

這邊所提供的範例僅供參考。  

## Multi-Tenancy
----------------

透過修改 `App_Start/RouteConfig.cs` 的設定，讓 Mvc 可以支援 [Multi-Tenancy](../MultiTenancy.md)

```csharp
// 將 routes.MapRoute 方法改為 routes.MapPaaSRoute 方法，參數不變。
routes.MapPaaSRoute(
    "Default", // Route name
    "{controller}/{action}/{id}", // URL with parameters
    new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parameter defaults
);
```

修改 `App_Start/WebApiConfig.cs` 的 route 設定，讓 Web API 可以支援 [Multi-Tenancy](../MultiTenancy.md)：  

```csharp
// 將 MapHttpRoute 方法改為 MapPaaSHttpRoute 方法，參數不變。
config.Routes.MapPaaSHttpRoute(
    name: "DefaultApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { id = RouteParameter.Optional }
);
```

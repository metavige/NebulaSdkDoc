
Nebula.Web.Mvc5
================

After Asp.Net MVC 3.0, there is provided a mechanism for the IoC `DependencyResolver` way to generate `Controller` of Instance
So in Asp.Net MVC 3.0/4.0 practice, with Asp.Net MVC 2.0 way somewhat segment.

## Install
----------------

    Install-Package Nebula.Web.Mvc5

> Note: Do not install with other Mvc Package (such as Nebula.Web.Mvc4)

## WebActivator
----------------

After installing Nebula.Web.Mvc4, in a project which will generate a directory `App_Start`, which will add a `NebulaMvcWebActivator.cs` file. Through the [WebActivator](https://github.com/davidebbo/WebActivator) technology, when you start Application will automatically execute this program.  

The generated WebActivator code is as the sample below:

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


This code is created by [Transformation](http://docs.nuget.org/docs/creating-packages/configuration-file-and-source-code-transformations) of Nuget. The code namespace would be the same namespace of the project. This sample is for reference.  

## Multi-Tenancy
----------------

By modifing the `App_Start/RouteConfig.cs` setting, so Mvc can support [Multi-Tenancy](../MultiTenancy.md)

```csharp
// The routes.MapRoute method to routes.MapPaaSRoute method parameters unchanged.
routes.MapPaaSRoute(
    "Default", // Route name
    "{controller}/{action}/{id}", // URL with parameters
    new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parameter defaults
);
```

By modifing `App_Start/WebApiConfig.cs` route setting, So Web API can support [Multi-Tenancy](../MultiTenancy.md)：  

```csharp
// The MapHttpRoute method to routes.MapPaaSHttpRoute method parameters unchanged.
config.Routes.MapPaaSHttpRoute(
    name: "DefaultApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { id = RouteParameter.Optional }
);
```

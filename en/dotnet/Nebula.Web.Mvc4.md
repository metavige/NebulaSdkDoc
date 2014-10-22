
Nebula.Web.Mvc4
================

Asp.Net MVC 3.0 and later version provides a `DependencyResolver` method on IoC technique, which is used to create an instance of `Controller`. Hence there is a development difference between Asp.Net MVC 3.0/4.0.  

## Install
----------------

    Install-Package Nebula.Web.Mvc4

> Note: 請勿與其他 Mvc Package (如 Nebula.Web.Mvc3) 一起安裝

## WebActivator
----------------

After installing Nebula.Web.Mvc4, your project will create an `App_Start` folder, which contains a `NebulaMvc4WebActivator.cs` file. With the [WebActivator](https://github.com/davidebbo/WebActivator) technique, this program would be activated as the Application starts.

The generated WebActivator code is as the sample below:  

```csharp
[assembly: WebActivator.PostApplicationStartMethod(typeof(SaaS.Sample.Web.View.App_Start.NebulaMvc4WebActivator), "Init")]
namespace SaaS.Sample.Web.View.App_Start
{
    using System;
    using System.Linq;
    using System.Web.Mvc;

    public class NebulaMvc4WebActivator
    {
        /// <summary>
        /// Start PaaS.Web Mvc4 Application, Prepare IoC Container
        /// </summary>
        public static void Init()
        {
            DependencyResolver.SetResolver(new Quanta.PaaS.Web.Mvc.UnityDependencyResolver());
            System.Web.Http.GlobalConfiguration.Configuration.DependencyResolver
                = new Quanta.PaaS.Web.Mvc.WebApiDependencyResolver(System.Web.Http.GlobalConfiguration.Configuration.DependencyResolver);
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

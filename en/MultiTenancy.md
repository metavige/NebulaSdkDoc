
# Multi-Tenancy

Nebula CloudPlatform supports [Multi-Tenancy](http://en.wikipedia.org/wiki/Multitenancy) development. User's Solution ID is obtained through a series of TenancyId (in GUID format) by Nebula SDK, and it is used by every SaaS program in AppContext to be available to user.  

Programs can be used by enabling SaaS Product Multi-Tenancy in the Developer Area.  

## Solution Id
----------------
Each product through the [CAMP](https://www.quanta-camp.com/) shelves way to provide to the user to purchase.
After users purchase products to the user's company as a unit, each company will be assigned a Solution Id to the company.

After enabling multi-tenant capabilities, by [Developer Area](http://www.quanta-camp.com/Developer/) in the development and testing / simulation test function, or by the [Member Center](https://member.quanta-camp.com) into the SaaS application, the system will dynamically join a dynamic `TenancyId` on the url, SaaS application that can be used to obtain the user belongs Solution Id through Nebula SDK.

## Asp.Net Mvc

As Asp.Net Mvc has Route technique, hence it is easier to modify the code on Asp.Net Mvc.

* Based MVC version, install Nebula MVC Package
    * [Nebula.Web.Mvc](dotnet/Nebula.Web.Mvc.md) (Support Asp.Net Mvc 2)
    * [Nebula.Web.Mvc3](dotnet/Nebula.Web.Mvc3.md)  (Support Asp.Net Mvc 3)
    * [Nebula.Web.Mvc4](dotnet/Nebula.Web.Mvc4.md)  (Support Asp.Net Mvc 4)
    * [Nebula.Web.Mvc5](dotnet/Nebula.Web.Mvc5.md)  (Support Asp.Net Mvc 5)  
* Modify the `Route` setting in `App_Start/RouteConfig.cs`. Nebula SDK provides a set of Route Collection Extension method of `MapPaaSRoute`, therefore developer can still perform testing on Visual Studio on local machine without generating TenancyId.  

```csharp
// Change routes.MapRoute method to routes.MapPaaSRoute, there is no change on the variables。
routes.MapPaaSRoute(
    "Default", // Route name
    "{controller}/{action}/{id}", // URL with parameters
    new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parameter defaults
);
```

Web API in [Nebula.Web.Mvc5](dotnet/Nebula.Web.Mvc5.md), began to support Multi-Tenancy, program modifications similar way Mvc Router.
Modify `App_Start/WebApiConfig.cs` the route settings:

```csharp
config.Routes.MapPaaSHttpRoute(
    name: "DefaultApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { id = RouteParameter.Optional }
);
```

## Asp.Net

Asp.Net must use `HttpHandler` to obtain TenancyId.  

* Install [Nebula.Web](dotnet/Nebula.Web.md) Package
* Insert the following configuration, and select Asp.Net type in the application setting. The URL provided by Nebula Cloud Platform will have `/Startup` as the application portal and provides TenancyId.

```xml
<configuration>
    <system.web>
        <httpHandlers>
            <add path="Startup" verb="GET" type="Quanta.PaaS.Web.PaaSStartupHandler, Quanta.PaaS.Web" />
        </httpHandlers>
    </system.web>
    <system.webServer>
        <handlers>
            <add name="PaaSStartupHandler" verb="*" path="Startup"
              type="Quanta.PaaS.Web.PaaSStartupHandler, Quanta.PaaS.Web"/>
        </handlers>
    </system.webServer>
</configuration>
```

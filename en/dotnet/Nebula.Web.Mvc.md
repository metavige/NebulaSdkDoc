
Nebula.Web.Mvc
================

This Package is used to support Asp.Net MVC 2, which provides implementation on `ControlFactory` and `Route`, that can easily combine with [Multi-Tenancy](../MultiTenancy.md) and Nebula [Dependency Injection](DI.md) .  

## Install
----------------

    Install-Package Nebula.Web.Mvc

> Note: Do not install with other Mvc Package (such as Nebula.Web.Mvc3)

## Asp.Net Mvc2 Support

As SaaS development needs to use Nebula SDK, please change the code as the sample code below:  

* Modify Global.asax.cs  

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using Microsoft.Practices.Unity;
using Quanta.PaaS.Web.Mvc;
using Quanta.PaaS.Web.Mvc.Extension;

namespace Sample.Web
{

	public class MvcApplication : HttpApplication
	{
		public static void RegisterRoutes(RouteCollection routes)
		{
        	// modify Route Mapping for support Multi-Tenancy
			routes.MapPaaSRoute(”Default",
			  	”{controller}/{action}/{id}",
			 	new { controller = “Home", controller = “Home", id = UrlParameter.Optional }
		}

		protected void Application_Start()
		{
			RegisterRoutes(RouteTable.Routes);

			// Loading Nebula SDK provided ControllerFactory, easy to do Dependency Injection
			this.LoadSaaSMvcControllerFactory();
		}
	}
}
```

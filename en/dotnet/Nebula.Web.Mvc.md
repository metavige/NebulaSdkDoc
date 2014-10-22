
Nebula.Web.Mvc
================

這個 Package 主要用來支援 Asp.Net MVC 2 的版本。提供了 `ControlFactory` 以及 `Route` 的實作，方便與 [Multi-Tenancy](../MultiTenancy.md) 以及 Nebula 的 [Dependency Injection](DI.md) 結合。  

## Install
----------------

    Install-Package Nebula.Web.Mvc

> Note: 請勿與其他 Mvc Package (如 Nebula.Web.Mvc3) 一起安裝

## Asp.Net Mvc2 Support
----------------

在 Asp.Net MVC 2 專案中要使用 Nebula SDK，請參考以下方式作程式的調整：  

* 修改 Global.asax.cs  

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
        	// 修改 Route Mapping，用來支援 Multi-Tenancy
			routes.MapPaaSRoute(”Default",
			  	”{controller}/{action}/{id}",
			 	new { controller = “Home", controller = “Home", id = UrlParameter.Optional }
		}

		protected void Application_Start()
		{
			RegisterRoutes(RouteTable.Routes);

			// 載入 Nebula SDK 所提供的 ControllerFactory, 輕鬆的做到 Dependency Injection
			this.LoadSaaSMvcControllerFactory();
		}
	}
}
```

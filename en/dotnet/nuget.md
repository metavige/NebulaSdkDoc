
Module Introduction
================

The followings below are Nebula SDK Module Introduction. You add modules based on your project type.  

* Nebula.Core
Nebula core program, which communicates with Nebula Kernel Service.  

* [Nebula.Module](Nebula.Module.md)
SDK Module program; all Nebula SDK module are in this package.

* [Nebula.Web](Nebula.Web.md)
NEBULA Asp.Net Web Support Module, which provides basic services to Asp.Net.

* MVC
This module provides basic services to MVC, such as defining Route method of Multi-Tenancy, integration with `ControllerFactory`, and providing [Dependency Injection](DI.md)  
Depending on the MVC version, choose a different NEBULA SDK Package:  
  * [Nebula.Web.Mvc](Nebula.Web.Mvc.md) - Asp.Net MVC 2  
  * [Nebula.Web.Mvc3](Nebula.Web.Mvc3.md) - Asp.Net MVC 3  
  * [Nebula.Web.Mvc4](Nebula.Web.Mvc4.md) - Asp.Net MVC 4  
  * [Nebula.Web.Mvc5](Nebula.Web.Mvc5.md) - Asp.Net MVC 5  

* Entity Framework support  
Entity framework provides support, developers can access NEBULA database resources through Entity  
  * [Nebula.Data.Entity](Nebula.Data.Entity.md) - support Entity Framework 5  
  * [Nebula.Data.EF6](Nebula.Data.EF6.md) - support Entity Framework 6  

* [Nebula.Web.Identity](Nebula.Web.Identity.md)
Support Asp.Net Identity, applicable to Asp.Net MVC 5 project  

* [Nebula.Data.Mongo](Nebula.Data.Mongo.md)
Support Mongo Resource Access  

* [Nebula.Web.Security](Nebula.Web.Security.md)
This module supports OAuth2 login authentication module, provide `HttpHandler` of Asp.Net to integrate with [OAuth2](http://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0CDgQFjAA&url=http%3A%2F%2Foauth.net%2F2%2F&ei=KjbtUJi2F4zckgW1_YD4AQ&usg=AFQjCNG58oSRksGnoIWIhfWYBB1sg_YGng&sig2=L5mNNVdzBASopkaVxaQ_2A) in the authentication flow aspect by a simple method to integrate login accounts between Asp.Net/Asp.Net MVC project and [CAMP](http://www.quanta-camp.com/).  
OAuth2 process, you can refer to [Authetication](Authentication.md)

* [Neubla.WCF](Nebula.WCF.md)
This module provides WCF project supports on `ServiceFactory`, which can integrate with Nebula.Module [Dependency Injection](DI.md), you can inject [Nebula.Module](Nebula.Module.md) into WCF Service.  

* [Neubla.Schedule](Nebula.Schedule.md)
This modules supports schedule interface. After you program your job code, you can go to [Developer Area](http://www.quanta-camp.com/Developer/) to configure schedule settings, upload schedule job program and configure your schedule file.  

* [Neubla.Office](Nebula.Office.md)
This module provides read/write function of Excel file (xlsx) in Open XML format. You need to have Open XML SDK 2.0.  

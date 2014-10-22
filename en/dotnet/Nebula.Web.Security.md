
Nebula.Web.Security
================
This Package provides [OAuth2](http://oauth.net/2/) classes of login implementation.
This Package is currently only supports Asp.Net MVC 4 previous project, Asp.Net Mvc project after 5 Use [Nebula.Web.Identity](Nebula.Web.Identity.md)  

## Install
----------------

    Install-Package Nebula.Web.Security

## web.config settings
----------------

The following settings is an example.

Other than the setting of &lt;authentication/&gt; element, which needs to be manually inserted, the other settings of handler would be automatically inserted when `Nebula.Web.Security` Package is added.

```xml
<configuration>
    <appSettings>
        <add key="Nebula_LogOnSite" value="http://www.quanta-camp.com/OpenId" />
    </appSettings>
	<system.web>
		<authentication mode="Form">
			<forms loginUrl="~/LogOn" />
		</authentication>
		<httpHandlers>
			<add verb="GET" path="LogOn" type="Quanta.PaaS.Web.Security.OAuth2LogOnHandler, Quanta.PaaS.Web.Security" />
		</httpHandlers>
	</system.web>
	<system.webServer>
		<handlers>
			<add name="Nebula_OAuthLoginHandler" verb="GET" path="LogOn" type="Quanta.PaaS.Web.Security.OAuth2LogOnHandler, Quanta.PaaS.Web.Security" />
		</handlers>
	</system.webServer>
</configuration>
```

Please add code routes.IgnoreRoute("LogOn") on `Global.asax.cs` for error prevention, after you add the above settings.  

```csharp
public class MvcApplication : System.Web.HttpApplication
{

	public static void RegisterRoutes(RouteCollection routes)
	{
		routes.IgnoreRoute("{resource}.axd/{*pathInfo}");
		routes.IgnoreRoute("LogOn"); // add Ignore Route

		routes.MapRoute(
			"Default", // Route name
			"{controller}/{action}/{id}", // URL with parameters
			new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parameter defaults
		);
	}
}
```

## Program Authorization and Authentication Checks
----------------

There are two methods to add login authorization authentication code in programs.

### Global.asax.cs

GlobalFilter this mechanism, only support after Asp.Net Mvc 3.  
You can add GlobalFilter on `Global.asax.cs` in Asp.Net MVC 3, of all Controller/Action require login authentication.

```csharp
using Quanta.PaaS.Web.Security;

public class MvcApplication : System.Web.HttpApplication
{
	public static void RegisterGlobalFilters(GlobalFilterCollection filters)
	{
		filters.Add(new HandleErrorAttribute());
		filters.Add(new OAuth2AuthorizeAttribute());
	}
}
```

### FilterConfig.cs

If Asp.Net MVC 4, you need add filter setting on `App_Start/FilterConfig.cs`  

```csharp
using Quanta.PaaS.Web.Security;

public class FilterConfig
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        filters.Add(new OAuth2AuthorizeAttribute());
        filters.Add(new HandleErrorAttribute());
    }
}
```

### Attribute codes

When some Action requires login authentication, you can add attribute [OAuth2Authorize] on `Controller` or `Action`.  
When user navigates into this `Action`, it will check whetever the user has logged in.  

```csharp
using Quanta.PaaS.Web.Security;

public class HomeController : Controller
{
	[OAuth2Authorize]
	public ActionResult Index()
	{
		return View();
	}
}
```


## Get authorize account identity
----------------

```csharp
using System.Web.Http;

public class HomeController : Controller
{
	[OAuth2Authorize]
	public ActionResult Index()
	{
		var userId = User.Identity.Name;
		return View();
	}
}
```

The default URL of OAuth2 Provider in Nebula Public Cloud is [http://www.quanta-camp.com/OpenId](http://www.quanta-camp.com/OpenId).  

## Asp.Net Provider Support

Asp.Net 2.0 provides a [Provider](http://msdn.microsoft.com/en-us/library/aa478948.aspx) technique, which abstract role and user data. Nebula Security Module implement `RoleProvider` and `ProfileProvider` based on this Provider model, that suppports SaaS to integrate user data of Nebula Cloud Platform by the methods below.

![Asp.Net 2.0 Provider Model](http://i.msdn.microsoft.com/dynimg/IC126090.gif)  

The following below explain the related uses：  

### RoleProvider

`RoleProvider` settings of Nebula Cloud Platform are implemented in the following way below:

```xml
<configuration>
  <system.web>
    <roleManager defaultProvider="PaaSRoleProvider" enabled="true">
      <providers>
        <clear />
        <add name="PaaSRoleProvider" type="Quanta.PaaS.Web.Security.PaaSRoleProvider" />
      </providers>
    </roleManager>
  </system.web>
</configuration>
```

Please read [RoleManager](http://msdn.microsoft.com/en-us/library/ms164660%28v=vs.80%29.aspx) for other properties and settings.

Nebula.Security provides all `RoleProvider` implemented functions. All queries or maintained `Role` data will be within the `Solution` user logs in. (Please read [Multi-Tenancy](../MultiTenancy.md) for more about Solution)。

Sample code descriptions:
```csharp
using Quanta.PaaS.Web.Security;
using System.Web;
using System.Web.Mvc;
using System.Web.Security;

public class UserController : Controller
{

    public ActionResult Index()
    {
      // Get the logged in user\'s role data
      string[] allRoles = Roles.GetRolesForUser();

      return View();
    }
}
```

Please read MSDN [System.Web.Security.Roles](http://msdn.microsoft.com/en-us/library/system.web.security.roles.aspx) about how to use Asp.Net Role API and more examples (Please select the corresponding .NET Framework document). Or, refer to [Asp.Net Role Management](http://msdn.microsoft.com/en-us/library/5k850zwb.aspx)

### ProfileProvider

The Profile functions provided by Nebula Cloud Platform, which help developer to obtain logged in users' data at [CAMP](http://www.quanta-camp.com); The currently provided data are:

* UserInfo : User information
* CompanyInfo : User's company's information

#### web.config settings

```xml
<configuration>
  <system.web>
    <profile defaultProvider="PaaSProfileProvider" enabled="true"
      inherits="Quanta.PaaS.Web.Security.PaaSProfile">
      <providers>
        <clear />
        <add name="PaaSProfileProvider" type="Quanta.PaaS.Web.Security.PaaSProfileProvider"/>
      </providers>
    </profile>
  </system.web>
</configuration>
```

#### Use Profile

```csharp
using Quanta.PaaS.Web.Security;
using System.Web;
using System.Web.Security;

public class HelloWorld
{

    public void Hello()
    {
      PaaSProfile profile = HttpContext.Current.Profile as PaaSProfile;

      // eg. display username
      Console.WriteLine(" User Name : {0} ", profile.UserDetail.Name);
    }
}
```

#### PaaSProfile Property Description

The sample code below describes Profile properties:

```csharp
namespace Quanta.PaaS.Web.Security
{
  /// <summary>
  /// User Profile
  /// </summary>
  public class PaaSProfile
  {
    /// User's detail
    public UserInfo UserDetail { get; }
    /// User's company's detail
    public CompanyInfo CompanyDetail { get; }
  }

  /// <summary>
  /// User's detail
  /// </summary>
  public class UserInfo
  {
    /// User ID
    public string UserId { get; }
    /// Username
    public string Name { get; }
    /// User's English Name
    public string EnglishName { get; }
    /// E-mail address
    public string Email { get; }
    /// Company ID
    public string CompanyId { get; }
  }

  /// <summary>
  /// User's company's detail
  /// </summary>
  public class CompanyInfo
  {
    /// Company ID
    public string CompanyId { get; }
    /// Company Name
    public string Name { get; }
  }
}
```

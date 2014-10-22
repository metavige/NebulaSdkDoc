
Nebula.Web.Security
================

此 Package 提供了 [OAuth2](http://oauth.net/2/) 登入機制實作的相關類別。  
目前這個 Package 僅支援 Asp.Net MVC 4 以前的專案，Asp.Net Mvc 5 之後的專案請使用 [Nebula.Web.Identity](Nebula.Web.Identity.md)  

## Install
----------------

    Install-Package Nebula.Web.Security

## web.config 設定
----------------

以下的設定為參考範例。  

目前以下的設定，除了 &lt;authentication/&gt; 的設定需要自行加入，handler 的設定在加入 `Nebula.Web.Security` 的 Package 時，便會自動加入。

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

加入以上設定後，請在 `Global.asax.cs` 忽略 LogOn 這個網址，避免錯誤

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

## 程式授權驗證檢查
----------------

採用 SDK 之後，在程式中，有兩種方式來檢查是否有通過驗證

### Global.asax.cs

GlobalFilter 這個機制，在 Asp.Net Mvc 3 之後才支援  

若所有的 Controller/Action 都需要作登入驗證  
在 Asp.Net MVC 3 中，可以在 `Global.asax.cs` 中加入 GlobalFilter  

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

如果是 Asp.Net MVC4 ，要到 `App_Start/FilterConfig.cs` 檔案中去加入 Filter 設定  

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

### Attribute 標記

若某些 Action 需要登入驗證，可在 `Controller` 或 `Action` 上加入 [OAuth2Authorize] 這個屬性  
當進入這個 `Action` 的時候，就會檢查使用者是否有登入

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


## 取得驗證的登入帳號
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

在 Nebula Public Cloud 中，預設的 OAuth2 Provider 網址為 [http://www.quanta-camp.com/OpenId](http://www.quanta-camp.com/OpenId)。  


## Asp.Net Provider Support

Asp.Net 2.0 提供一個 [Provider](http://msdn.microsoft.com/zh-tw/library/aa478948.aspx) 的機制，將角色、使用者資料作抽象化。Nebula Security Module 根據這個 Provider 的 Model，實作出 `RoleProvider` 以及 `ProfileProvider`，提供給 SaaS 服務應用程式，可利用以下的方式，與 Nebula CloudPlatform 的使用者資料整合。

![Asp.Net 2.0 Provider Model](http://i.msdn.microsoft.com/dynimg/IC126090.gif)  

以下將說明相關的使用方式：  

### RoleProvider

目前 Nebula CloudPlatform 所實作的 `RoleProvider` 設定方式如下：

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

其他屬性請參考 [RoleManager](http://msdn.microsoft.com/zh-tw/library/ms164660%28v=vs.80%29.aspx) 的設定說明。  

目前 Nebula.Security 有提供 `RoleProvider` 所有的功能實作。所有查詢或維護的 `Role` 資料，都會是在與登入使用者相同的 `Solution` (Solution 說明請參考 [Multi-Tenancy](../MultiTenancy.md))。

使用範例說明 :  
```csharp
using Quanta.PaaS.Web.Security;
using System.Web;
using System.Web.Mvc;
using System.Web.Security;

public class UserController : Controller
{

    public ActionResult Index()
    {
      // 取得登入使用者所有角色資料
      string[] allRoles = Roles.GetRolesForUser();

      return View();
    }
}
```

如何使用 Asp.Net Role API，請參考 MSDN 的 [System.Web.Security.Roles](http://msdn.microsoft.com/en-us/library/system.web.security.roles.aspx) (請根據實際 .Net Framework 版本選擇相同版本的文件說明) 的文件說明以及範例。 或者是參考 [Asp.Net 角色管理](http://msdn.microsoft.com/en-us/library/5k850zwb.aspx)

### ProfileProvider

Nebula CloudPlatform 所提供的 Profile 功能，主要是提供程式開發人員可以取得登入使用者在 [CAMP](http://www.quanta-camp.com) 網站的相關資料。目前提供的資料有以下的項目：  

* UserInfo : 使用者相關資料
* CompanyInfo : 使用者所屬公司資料

#### web.config 設定

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

#### 使用 Profile

```csharp
using Quanta.PaaS.Web.Security;
using System.Web;
using System.Web.Security;

public class HelloWorld
{

    public void Hello()
    {
      PaaSProfile profile = HttpContext.Current.Profile as PaaSProfile;

      // 舉例，顯示使用者名稱
      Console.WriteLine(" User Name : {0} ", profile.UserDetail.Name);
    }
}
```

#### PaaSProfile 屬性說明

以下用程式方式，說明 Profile 的相關屬性：  

```csharp
namespace Quanta.PaaS.Web.Security
{
  /// <summary>
  /// 使用者 Profile
  /// </summary>
  public class PaaSProfile
  {
    /// 使用者相關資料
    public UserInfo UserDetail { get; }
    /// 使用者所屬公司資料
    public CompanyInfo CompanyDetail { get; }
  }

  /// <summary>
  /// 使用者相關資料
  /// </summary>
  public class UserInfo
  {
    /// 使用者登入代碼
    public string UserId { get; }
    /// 使用者名稱
    public string Name { get; }
    /// 使用者英文名稱
    public string EnglishName { get; }
    /// 電子郵件
    public string Email { get; }
    /// 所屬公司代碼
    public string CompanyId { get; }
  }

  /// <summary>
  /// 使用者所屬公司資料
  /// </summary>
  public class CompanyInfo
  {
    /// 公司代碼
    public string CompanyId { get; }
    /// 公司名稱
    public string Name { get; }
  }
}
```

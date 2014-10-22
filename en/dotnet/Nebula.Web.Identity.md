Nebula.Web.Identity
================

用來支援 Asp.Net Identity (2.0) 的登入機制。  

## 安裝 Package
-----------------

    Install-Package Nebula.Web.Identity


> Note: 請勿與 [Nebula.Web.Security](#Nebula.Web.Security) 一起安裝

## 簡介
-----------------

目前在 Asp.Net MVC 5 之後，內建的驗證機制已經由原本的 `Membership` 改成 `Asp.Net Identity`，為了與原本的驗證機制區分，將獨立一個 Package 用來支援 Asp.Net Identity
由於 `Asp.Net Identity` 與原本的 `Membership` 機制不相同，所以新專案建立的時候，在 `web.config` 有幾項與早期支援 `FormsAuthentication` 不一樣的地方：  

### 不再使用 FormsAuthentication 驗證機制

```xml
<configuration>
    <system.web>
        <authentication mode="None" />
    </system.web>
</configuration>
```

### 移除 IIS FormsAuthenticationModule 的使用  

```xml
<configuration>
	    <system.webServer>
        <modules>
            <remove name="FormsAuthenticationModule" />
        </modules>
    </system.webServer>
</configuration>
```

安裝 `Nebula.Web.Identity` Package 之後，會產生一個 `Controllers/CampAccountController.cs` 的檔案

以下程式片段為範例，請以實際產生出的檔案為主：  

```csharp
/// <summary>
/// CAMP 登入登出 Controller.
/// </summary>
public class CampAccountController : Controller
{
    #region Properties & Fields

    private CampAppUserManager _userManager;

    public IAuthenticationManager AuthenticationManager
    {
        get
        {
            return HttpContext.GetOwinContext().Authentication;
        }
    }

    public CampAppUserManager UserManager
    {
        get
        {
            return _userManager ?? HttpContext.GetOwinContext().GetUserManager<CampAppUserManager>();
        }
        private set
        {
            _userManager = value;
        }
    }

    #endregion

    // GET: CampAccount
    public ActionResult Login(string returnUrl)
    {
        return new ChallengeResult(Url.Action("LoginCallBack", new { ReturnUrl = returnUrl }));
    }

    public async Task<ActionResult> LoginCallBack(string returnUrl)
    {
        var info = await AuthenticationManager.GetExternalLoginInfoAsync();
        if (info == null)
        {
            return RedirectToAction("Login");
        }

        // Create User From ExternalLoginInfo
        var user = UserManager.CreateByExternalInfo(info);

        AuthenticationManager.SignOut(CampOAuth2Constant.CookieType);
        var claimsIdentity = await user.GenerateUserIdentityAsync(UserManager);

        AuthenticationManager.SignIn(claimsIdentity);

        return Redirect(returnUrl);
    }

    public ActionResult LogOut()
    {
        AuthenticationManager.SignOut();

        var logOnSiteHost = ConfigurationManager.AppSettings["Nebula_LogOnSite"];
        if (!logOnSiteHost.EndsWith("/"))
        {
            logOnSiteHost = logOnSiteHost + "/";
        }
        var logOutUri = new Uri(new Uri(logOnSiteHost), "Account/LogOff").ToString();
        Console.WriteLine(logOutUri);

        return Redirect(logOutUri);
    }
}

public class ChallengeResult : HttpUnauthorizedResult
{
    public ChallengeResult(string redirectUri)
    {
        RedirectUri = redirectUri;
    }

    public string RedirectUri { get; set; }

    public override void ExecuteResult(ControllerContext context)
    {
        var properties = new AuthenticationProperties() { RedirectUri = RedirectUri };

        // Challenge CAMP Login, if not login, redirect to CAMP Authentication Web
        context.HttpContext.GetOwinContext().Authentication.Challenge(properties, CampOAuth2Constant.AuthenticationType);
    }
}
```


## 程式修改說明
-----------------

請到 `App_Start/Startup.Auth.cs` 檔案中，做以下程式的修改  

### 修改 LoginPath

找到 `LoginPath = new PathString("/Account/Login")`，將路徑修改為 `LoginPath = new PathString("/CampAccount/Login")`  

### 加上 CampAuthentication 支援設定

在 ConfigurationAuth 方法最後加上一段程式碼 `app.UseCampAuthentication();`  

### 與原本的 ApplicationUserManager 結合
預設 `Nebula.Web.Identity` 有內建一個 `UserManager`，會與 CAMP 做結合，取得相關的 `Profile` 資料。  
但如果需要與專案中內建的 `ApplicationUserManager` 結合，當做一個 `ExternalLogin` 機制的話，設定 `CampAuthentication` 的時候需要加上 `useInternalUserManager: false` 的參數

    app.UseCampAuthentication(useInternalUserManager: false);

以下為 Startup.Auth.cs 的檔案範例：  

```csharp
public partial class Startup
{
    public void ConfigureAuth(IAppBuilder app)
    {
        // 如果不使用內建的 ApplicationUserManager，請將以下的程式碼註解
        // app.CreatePerOwinContext(ApplicationDbContext.Create);
        // app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);

        app.UseCookieAuthentication(new CookieAuthenticationOptions
        {
            AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
            LoginPath = new PathString("/CampAccount/Login"),
            Provider = new CookieAuthenticationProvider
            {
                OnValidateIdentity = SecurityStampValidator.OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
            }
        });

        app.UseCampAuthentication();
    }
}
```

### Cookie 相關設定

新的 Asp.Net Identity 已經不在 `web.config` 設定驗證相關的資料，如 Cookie 的設定，所以若要設定 Cookie，需要在 `App_Start/Startup.Auth.cs` 中設定  

以下為參考範例：

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
    CookieDomain = "DomainName",
    CookieName = ".AspNet.Identity.Cookie",
    ExpireTimeSpan = TimeSpan.FromMinutes(30),
    SlidingExpiration = false,
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
		{
			OnValidateIdentity = SecurityStampValidator.OnValidateIdentity&lt;ApplicationUserManager, ApplicationUser&gt;(
			    validateInterval: TimeSpan.FromSeconds(10),
			    regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
		}
});
```

## 驗證
----------------

Asp.Net Identity 仍舊支援 `AuthorizeAttribute`，在需要驗證的 `Controller`/`Action` 加上授權屬性的設定就可以  

```csharp
public class MyController : Controller {

    [Authorize]
    public ActionResult Index() {


    }
}

```


## 登出
----------------

新版本 One Asp.Net 專案中，登出的方式，目前採用的是 `POST` 的方式送出要求  
所以可以到 `Views/Shared/_LogPartial.html` 去修改登出路徑  
請將 Form Action 改成 `CampAccount/LogOff`  

以下為部分程式範例

```csharp
    @using Microsoft.AspNet.Identity
    @using Nebula.Web.Identity
    @if (Request.IsAuthenticated)
    {
        using (Html.BeginForm("LogOff", "CampAccount", FormMethod.Post, new { id = "logoutForm", @class = "navbar-right" }))
        {
        @Html.AntiForgeryToken()

        <ul class="nav navbar-nav navbar-right">
            <li>
                @Html.ActionLink("你好 " + User.Identity.GetUserName() + "!", "Manage", "Account", routeValues: null, htmlAttributes: new { title = "管理" })
            </li>
            <li><a href="javascript:document.getElementById('logoutForm').submit()">登出</a></li>
        </ul>
        }
    }
    else
    {
        <ul class="nav navbar-nav navbar-right">
            <li>@Html.ActionLink("註冊", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
            <li>@Html.ActionLink("登入", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
        </ul>
    }
```

## 取得使用者相關資料

以下程式範例，都是以在 `System.Web.Mvc.Controller` 的範例。使用的是 Controller 的 User 屬性。  
在其他地方，請使用 `HttpContext.Current.User` 一樣可以取得。
### 使用者名稱、電子郵件

```csharp
    User.Identity.GetUserName();
    User.Identity.GetEmail();
````

### 取得角色資料

```csharp
	User.Identity.GetRoles();
```

## 使用者公司代碼、公司名稱

```csharp
    User.Identity.GetCompanyId();
    User.Identity.GetCompanyName();
```

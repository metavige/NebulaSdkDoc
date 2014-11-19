Nebula.Web.Identity
================

To support Asp.Net Identity (2.0) login mechanism.  

## Install

    Install-Package Nebula.Web.Identity


> Note: Do not install with [Nebula.Web.Security] (Nebula.Web.Security.md)

## Introduction

Currently, after Asp.Net MVC 5, built-in authentication mechanism has been changed from the original `Membership` to `Asp.Net Identity`, in order to distinguish with the original authentication mechanism, used to support the independence of a Package Asp.Net Identity  
Because `Asp.Net Identity` with the original `Membership` mechanism is not the same, so when the new project created in `web.config` There are several early support `FormsAuthentication` not the same place:  

### No authentication mechanism using FormsAuthentication

```xml
<configuration>
    <system.web>
        <authentication mode="None" />
    </system.web>
</configuration>
```

### Remove IIS FormsAuthenticationModule  

```xml
<configuration>
	    <system.webServer>
        <modules>
            <remove name="FormsAuthenticationModule" />
        </modules>
    </system.webServer>
</configuration>
```

After installing `Nebula.Web.Identity` Package, will generate a `Controllers/CampAccountController.cs`

The following program fragment for example, the actual file mainly produce:  

```csharp
/// <summary>
/// CAMP LogOn/LogOut Controller.
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

## web.config modify

Add appSetting at web.config

```xml
<configuration>
    <appSettings>
        <add key="Nebula_LogOnSite" value="http://www.quanta-camp.com/OpenId" />
    </appSettings>
</configuration>
```

## How-to

Go `App_Start/Startup.Auth.cs` file, make the following changes to the program

### Change LoginPath

Find `LoginPath = new PathString("/Account/Login")`, modify the path to `LoginPath = new PathString("/CampAccount/Login")`  

### Add CampAuthentication external login support

Add the final paragraph in ConfigurationAuth method code: `app.UseCampAuthentication();`

### Combined with the original ApplicationUserManager
Default `Nebula.Web.Identity` has built a `UserManager`, CAMP do with the combination of `Profile` obtain the relevant information.  
But if you need the project built `ApplicationUserManager` combination as a `ExternalLogin` mechanism, then, when the need to set `CampAuthentication` add `useInternalUserManager: false` parameters  

    app.UseCampAuthentication(useInternalUserManager: false);

The following is an example of `Startup.Auth.cs` of file:  

```csharp
public partial class Startup
{
    public void ConfigureAuth(IAppBuilder app)
    {
        // If you do not use the built-in ApplicationUserManager, comment the following code
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

### Cookie settings

New Asp.Net Identity verification has not set `web.config` relevant information, such as the Cookie settings, so to set Cookie, needs to be set in the `App_Start/Startup.Auth.cs` in  

The following reference examples:

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

## Authorize


Asp.Net Identity still support `AuthorizeAttribute`, The need to verify the `Controller`/`Action` authorization attributes can be set  

```csharp
public class MyController : Controller {

    [Authorize]
    public ActionResult Index() {


    }
}

```


## LogOut

The new version of the One Asp.Net project, log out of the way, currently used is `POST` way to send request  
So can go to `Views/Shared/_LogPartial.html` to modify logout path  
Please Form Action into `CampAccount/LogOff`  

The following reference examples:  

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
                @Html.ActionLink("Hello " + User.Identity.GetUserName() + "!", "Manage", "Account", routeValues: null, htmlAttributes: new { title = "Admin" })
            </li>
            <li><a href="javascript:document.getElementById('logoutForm').submit()">登出</a></li>
        </ul>
        }
    }
    else
    {
        <ul class="nav navbar-nav navbar-right">
            <li>@Html.ActionLink("Register", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
            <li>@Html.ActionLink("Login", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
        </ul>
    }
```

## Get User Information

The following programming examples are based on the `System.Web.Mvc.Controller` paradigm. Using a Controller's User property.
In other places, use the same `HttpContext.Current.User` can get.

### User Name, Email

```csharp
    User.Identity.GetUserName();
    User.Identity.GetEmail();
```

### User's roles

```csharp
	User.Identity.GetRoles();
```

## User's company ID/Name

```csharp
    User.Identity.GetCompanyId();
    User.Identity.GetCompanyName();
```

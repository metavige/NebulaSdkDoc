
Dependency Injection
================

控制反轉 (Inversion of Control，縮寫為 IoC)，也叫做依賴注入 (Dependency Injection)，這是物件導向程式開發中的一種 Design Pattern，用來降低程式與程式間的耦合度。

在 Nebula SDK 中，採用了 [Microsoft Unity](http://unity.codeplex.com/) 這個 Framework，搭配 Unity 以及 [BootStrapper](BootStrapper.md) 的設定，開發者可以簡單的在 SaaS 應用程式中，透過 Unity 的注入機制，取得 Nebula SDK 的所有 Module Instance，或自行在 [BootStrapper](BootStrapper.md) 中所註冊的 Instance。

## IoC 取得物件的方式
----------------

以下用範例方式說明注入取得物件的方式

要注入的 Service 範例：
```csharp
public interface IUserService
{
	void Create(User user)
}

public class UserService : IUserService
{

	public void Create(User user)
	{
		// ...
	}
}
```

* Constructor 注入

```csharp
using System.Web;
using System.Web.Mvc;

public class HomeController : Controller
{
	private IUserService userService;

	public HomeController(IUserService userService)
	{
		this.userService = userService;
	}

	public ActionResult Create(User user)
	{
		this.userService.Create(user);
	}
}
```

* Property 注入

```csharp
using System.Web;
using System.Web.Mvc;
using Microsoft.Practices.Unity;

public class HomeController : Controller
{
	[Dependency]
	public IUserService UserService { get; set; }

	public ActionResult Create(User user)
	{
		this.UserService.Create(user);
	}
}
```

* 透過 AppContext 取得

```csharp
using Quanta.PaaS.Module;
using System.Web;
using System.Web.Mvc;

public class HomeController : Controller
{
	public ActionResult Create(User user)
	{
		IUserService UserService = AppContext.Current.Resolve<IUserService>();

		this.UserService.Create(user);
	}
}
```

## 結合 Nebula Module
----------------

因為 Nebula SDK 核心是採用 [Microsoft Unity](http://unity.codeplex.com/)，所以若要取得 Nebula SDK 所提供的 Module，也可透過 IoC 的方式取得

以 Constructor 注入的方式:

```csharp
using Quanta.PaaS.Module.Mail;
using System.Web;
using System.Web.Mvc;

public class MailController : Controller
{
	private IMailModule mailModule;

	public MailController(IMailModule mailModule)
	{
		this.mailModule = mailModule;
	}

	public ActionResult SendMail()
	{
		MailContext mailContext = new MailContext();
		// ... 設定郵件內容

		string errorMsg = "";
		mailModule.SendMailJob(mailContext, out errorMsg);
	}
}
```


## 其他參考文件
----------------

[Wiki - 控制反轉說明](http://zh.wikipedia.org/wiki/%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC)

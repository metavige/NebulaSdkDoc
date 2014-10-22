
Dependency Injection
================

Inversion of Control (abbreviated as IoC), can be also called Dependency Injection, it is a type of object-oriented program development design pattern，which is used to reduce the coupling relationship between programs.

Nebula SDK ues [Microsoft Unity](http://unity.codeplex.com/), with Unity and [BootStrapper] configuration, all which enable developer to easily obtain all the Nebula SDK Module Instances in SaaS programming, either through the Injection technique of Unity or the registered Instances in the configured [BootStrapper].

## Obtain objects by IoC
----------------

The following examples display methods injecting and obtaining objects

An example to inject a service：
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

* Constructor Injection

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

* Property Injection

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

* Obtain through `AppContext`

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

## Integrate with Nebula Module
----------------

As Nebula SDK Kernel uses [Microsoft Unity](http://unity.codeplex.com/), you can alternatively obtain the Modules provided by Nebula SDK through IoC, if you want to.

Injection by Constructor:

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
		// ... set mail context 

		string errorMsg = "";
		mailModule.SendMailJob(mailContext, out errorMsg);
	}
}
```

## Reference documents
----------------

[Wiki - Inversion of Control explanation](http://zh.wikipedia.org/wiki/%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC)

[BootStrapper]: <BootStrapper.md>


Exception Module
================

Nebula SDK provide a simple set of Exception Handler, which enable developer to log exception information through Nebula SDK. You can query for the information in the Developer Area or through the [Management Module](Module.Management.md).  

The followings describe Exception implemenation:

## Implement IExceptionHandle inteface
---------------

When you want all methods in a Class object to perform Error Log, you can use the following way to configure. Hence Error Log will be stored in database when error happens in runtime.  

* Class must inherit `IExceptionHandle` interface

```csharp
using Quanta.PaaS.Module.Business;

public class MailJob : IExceptionHandle
{
	public void SendMail()
	{
		// ... Code, if exception happen...
	}
}
```

* Class needs to set in [BootStrapper].

```csharp
using Quanta.PaaS.Module.Business;

public class MyBootStrapper : BusinessLayerBootStrapper
{
	public void Execute()
	{
		RegisterTypeForInterception<MailJob, MailJob>();
	}
}
```

* Create instance through `AppContext.Current.Resolve<T>()` or Injection  

```csharp
var job = AppContext.Current.Resolve<MailJob>();

try
{
	job.SendMail();
}
catch(Exception e)
{
	Console.WriteLine(e.Message);
}
```

When an error occurs in a method, it will log all Exception in the method through Unity Interception.  

## ExceptionHandlerAttribute
-----------------

This way is to log a certain method in a Class object,  

* Add `[ExceptionHandler]` above the method:  
As the example below, Error Log will log it when the Divide method has an error. The Add method, however, would not log, if there is an error.  

```csharp
using Quanta.PaaS.Module.Unity.Policy;

public class MathJob
{
    public int Add(int number1, int number2)
    {
        int total = number1 + number2;
        return total;
    }

    [ExceptionHandler]
    public int Divide(int number1, int number2)
    {
        int total = number1 / number2;
        return total;
    }
}
```

* Class needs to configure in [BootStrapper].
* Create Instance through `AppContext.Current.Resolve<T>()`

When a method has an error, it will log all Exception in the method through Unity Interception.

## Self-call ExceptionHandler
-----------------

If you use try-catch to handle error by yourself and want to log the error, you can use `ExceptionHander` provider by Logging Module to log.  

```csharp
using Quanta.PaaS.Module.Exceptions;

try
{
	var i = 1 / 0;
}
catch(Exception e)
{
	ExceptionHandler.Handle(e);
}
```

[BootStrapper]: <BootStrapper.md>

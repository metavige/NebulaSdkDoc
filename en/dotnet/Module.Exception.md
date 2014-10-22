
Exception Module
================

Nebula SDK 提供一組簡單的 Exception Handler，讓開發者可以透過 Nebula SDK 記錄錯誤的資訊，之後可在開發者專區中，或透過 [Management Module](Module.Management.md) 來查詢錯誤的紀錄。

以下說明 Exception 的使用方式:

## 實作 IExceptionHandle 介面
---------------

當使用者想針對 Class 物件內所有的方法進行 Error Log 紀錄時，可透過以下的方式設定，當 Class 執行方法有發生錯誤時，便會將 Error Log 記錄在資料庫中。  

* Class 必須繼承 `IExceptionHandle` 介面

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

* Class 需要在 [BootStrapper] 中設定。

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

* 產生 Instance。需要透過 `AppContext.Current.Resolve<T>()` 或透過 Injection 的方式來產生  

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

之後當方法中發生錯誤，會透過 Unity Interception 的機制，來記錄這個方法內所產生的所有 Exception。  

## ExceptionHandlerAttribute
-----------------

此方法是用於針對 Class 物件中某一個方法紀錄 Error Log ，

* 在該方法前面加上 `[ExceptionHandler]` 屬性。  
如下列物件的 Divide 方法有發生錯誤時，Error Log 會紀錄，但是 Add 方法若發生錯誤則不會紀錄 Error Log。

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

* Class 需要在 [BootStrapper] 中設定。
* 產生 Instance。需要透過 `AppContext.Current.Resolve<T>()` 的方式來產生  


之後當方法中發生錯誤，會透過 Unity Interception 的機制，來記錄這個方法內所產生的所有 Exception。  

## 自行呼叫 ExceptionHandler
-----------------

若使用 try-catch 自行處理錯誤，需要將錯誤記錄下來，可以透過 Logging Module 中所提供的 `ExceptionHandler` 來記錄  

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

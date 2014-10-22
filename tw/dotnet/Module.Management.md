Management Module
================

提供開發人員取得郵件寄發結果記錄、排程執行結果記錄以及錯誤訊息記錄。開發人員可以透過此模組，取得產品運作時相關歷史記錄。
透過查詢時間以及筆數的設定取得相對應結果。  
> 為避免使用者查詢大量資料，導致效能問題，目前查詢筆數上限為200筆。

## 取得 Module 方式
----------------

```csharp
using Quanta.PaaS.Module.Management;

IManageModule manageModule = AppContext.Current.GetModule<IManageModule>();
```

## 功能簡述
----------------

目前 Management Module 提供三個功能

* 取得郵件寄發結果記錄 - `SearchMailStatus`
* 取得排程執行結果記錄 - `SearchScheduleExecuteHistory`
* 取得 SaaS 應用程式錯誤歷史紀錄 - `SearchErrorLogHistory`

以上三個方法都是傳遞相同的參數，參數說明如下：

* int recCount : 查詢筆數，最大不可超過 200。如果超過 200，將拋出 `ArgumentException`。
* DateTime queryStart : 查詢區間開始時間
* DateTime queryEnd : 查詢區間結束時間

如果根據查詢日期區間，所查詢出來的資料大於 200 筆，回傳的結果將只會回傳前 200 筆，以 Log 日期作降冪排序。

## 回傳資料說明
----------------

以下列出各查詢記錄方法所回傳的資料物件，以程式方式說明物件的屬性內容：

* 郵件寄發記錄:

```csharp
namespace Quanta.PaaS.Module.Kernel.Contract.Management
{
	public class MailLogResultContext
	{
		public string MailFrom { get; }

		public string MailTo { get; }

		public string MailCc { get; }

		public string MailBcc { get; }

		public string MailSubject { get; }

		public string MailBody { get; }

		public string Remark { get; }

		public string MailStatus { get; }

		public DateTime? MailSendDate { get; }
	}
}
```

* 排程執行結果記錄 :

```csharp
namespace Quanta.PaaS.Module.Kernel.Contract.Management
{
	public class SchLogResultContext
	{
		public string ScheduleName { get; }

		public string ScheduleDesc { get; }

		public string TaskName { get; }

		public string TaskDesc { get; }

		public string ExeuteResult { get; }

		public DateTime ExeuteStartTime { get; }

		public DateTime ExeuteEndTime { get; }

		public string ProductId { get; }

		public string SolutionId { get; }

		public string ExeLog { get; }

		public string ExeErrorMessage { get; }

		public string ExeStackTrace { get; }

		public string InvokeErrorMessage { get; }

		public string InvokeStackTrace { get; }
	}
}
```

*  SaaS 應用程式錯誤歷史紀錄:

```csharp
namespace Quanta.PaaS.Module.Kernel.Contract.Management
{
	public class ErrorLogResultContext
	{
		public string SolutionId { get; }

		public string AssemblyName { get; }

		public string TypeName { get; }

		public string MethodName { get; }

		public string ServerIp { get; }

		public string ErrorTypeName { get; }

		public string ErrorDescription { get; }

		public string ErrorStackTrace { get; }

		public DateTime? CreateDate { get; }

		public DateTime? ClientLogTime { get; }

		public string ClientUserId { get; }

	}
}
```

## 使用範例
-----------------

```csharp
using Quanta.PaaS.Module;
using Quanta.PaaS.Module.Kernel.Contract.Management;

IManageModule module = AppContext.Current.GetModule<IManageModule>();
var result = module.SearchMailStatus(200, DateTime.Now.AddDays(-7), DateTime.Now);
```

Logging Module
================

Logging Module 提供使用者記錄與查詢 Error Log 以及 Transaction Log 的功能  
Error Logging 與 Transaction Log 資料將會記錄在 Nebula CloudPlatform 的資料庫中。
目前使用者可以透過 [Quanta CAMP 網站](http://www.quanta-camp.com/) 上 [開發者專區](http://www.quanta-camp.com/Developer/) 的紀錄查詢功能來查詢 Log 紀錄，亦可透過 Logging Module 的使用規範在開發程式過程中取出 Log 資料。  
若需要使用 Logging Module 功能，請依照以下步驟說明進行操作  

## TransactionLog 查詢
----------------

開發者需要先參考 [Database Module](Module.Database.md) 中，Transaction Log 記錄的程式修改方式，才可透過 Logging Module 取出異動記錄。

程式範例說明:

```csharp
using Quanta.PaaS.Module.Logging;

IList<LoggingTransactionContext> transLogs = LoggingModule.GetTransactionLog("Y", functionId: "SaveTransactionLog");
```

第一個參數是表示所要查詢的資料是否為正式環境，為必要參數，其餘參數可傳可不傳，傳遞參數時除了第一個必要參數之外，其餘均須具名指定傳入參數為何

以下是 `LoggingTransactionContext` 結果物件的介面說明：

```csharp
public class TransactionLogContext
{
	/// <summary>
	/// 是否為正式環境
	/// </summary>
	public string IsProduction { get; set; }

	/// <summary>
	/// SQL 語法
	/// </summary>
	public string SqlComand { get; set; }

	/// <summary>
	/// Remark Text 1
	/// </summary>
	public string RemarkText1 { get; set; }

	/// <summary>
	/// Remark Text 2
	/// </summary>
	public string RemarkText2 { get; set; }

	/// <summary>
	/// Remark Text 3
	/// </summary>
	public string RemarkText3 { get; set; }

	/// <summary>
	/// Remark Text 4
	/// </summary>
	public string RemarkText4 { get; set; }

	/// <summary>
	/// Remark Int 1
	/// </summary>
	public int RemarkInt1 { get; set; }

	/// <summary>
	/// Remark Int 2
	/// </summary>
	public int RemarkInt2 { get; set; }

	/// <summary>
	/// Remark Int 3
	/// </summary>
	public int RemarkInt3 { get; set; }

	/// <summary>
	/// Remark Int 4
	/// </summary>
	public int RemarkInt4 { get; set; }

	/// <summary>
	/// 紀錄 Client 端的 ThreadId
	/// </summary>
	public int ThreadId { get; set; }

	/// <summary>
	/// Property 的資料異動資訊
	/// </summary>
	public IList<TransactionLogProperty> Property { get; set; }
}

public class TransactionLogProperty
{
	/// <summary>
	/// 功能代碼
	/// </summary>
	public string FunctionId { get; set; }

	/// <summary>
	/// 屬性名稱
	/// </summary>
	public string PropertyName { get; set; }

	/// <summary>
	/// 變更前的舊資料
	/// </summary>
	public object OldValue { get; set; }

	/// <summary>
	/// 變更後的新資料
	/// </summary>
	public object NewValue { get; set; }
}
```

## Error Log
----------------

目前 [Management Module](Module.Management.md) 有提供錯誤訊息的查詢，建議採用該方法查詢。

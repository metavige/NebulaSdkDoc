
Nebula.Schedule
================

提供使用者撰寫特定工作服務以及排程工作的功能。  

此 Package 主要提供 `IJob` 介面類別，供開發者撰寫 Schedule Job 相關程式。  

使用者可以藉於實作 `IJob` 此介面將工作邏輯撰寫成為一個DLL檔，設定執行 Configuration 檔，將該 DLL 、Configuration 檔以及將服務所引用的相關 DLL 檔(若有使用)一起壓縮成為 ZIP 檔後，透過 [開發者專區](http://www.quanta-camp.com/Developer/) 將專案打包上傳。  

並在 [開發者專區](http://www.quanta-camp.com/Developer/) 設定該工作服務的執行時間，Quanta Cloud Platform 便會依照使用者設定的排程時間自動執行該工作服務。  
使用者可透過 [開發者專區](http://www.quanta-camp.com/Developer/) 中的應用程式管理工作，以及透過排程管理功能來設定排程時間。

## Install
----------------

    Install-Package Nebula.Schedule

## IJob
----------------

IJob 的實作範例：  

```csharp
using Quanta.PaaS.Schedule;

namespaec Sample
{
	public class HelloJob : IJob
	{
		public void Execute(JobExecutionContext context)
		{
			// Job Execute Logic .. .. ..
		}
	}
}
```

## 使用說明
----------------

Nebula.Schedule 中包含以下兩個物件:  

* `IJob`  
	Interface。所有服務所必須繼承的介面，此介面只提供 `Execute(JobExecutionContext context)` 方法，使用者必須將服務寫在此方法中。

* `JobExecutionContext` 類別  
	此為服務啟用時所傳入的參數。包含的相關資料如下表，這些欄位資料可以供使用者在開發工作時使用，以便服務工作的追蹤與設定。  

### 類別屬性說明

`JobExecutionContext` 類別屬性，以程式方式說明:  

```csharp
namespace Quanta.PaaS.Schedule
{
    /// <summary>
    /// 執行 Job 所傳遞的參數資料。
    /// </summary>
    public class JobExecutionContext
    {
        /// <summary>
        /// 工作排程 Id
        /// </summary>
        public long? Id { get; }

        /// <summary>
        /// 工作排程名稱
        /// </summary>
        public string Name { get; }

        /// <summary>
        /// 工作排程描述
        /// </summary>
        public string Description { get; }

        /// <summary>
        /// Product Id
        /// </summary>
        public string ProductId { get; }

        /// <summary>
        /// Solution Id.
        /// </summary>
        public string SolutionId { get; }

        /// <summary>
        /// 上次執行時間
        /// </summary>
        public DateTime? LastInvokeTime { get; }

        /// <summary>
        /// 服務工作參數。若此工作有需要設定參數時，在執行時可以透過此欄位取得預設值。
        /// </summary>
        public IDictionary<string, object> JobParas { get; }

        /// <summary>
        /// 服務執行紀錄
        /// </summary>
        public TextWriter Log { get; }

        /// <summary>
        /// 錯誤訊息紀錄
        /// </summary>
        public TextWriter Error { get; }

        /// <summary>
        /// Stack Trace 訊息紀錄
        /// </summary>
        public TextWriter StackTrace { get; }
    }
}
```

## IJob 範例
----------------

```csharp
using Quanta.PaaS.Schedule;

public class Example : IJob
{
	//若Job中有需要引用Service時可透過IoC方法使用Service
	public IMemberUserService obj { get; private set; }

	public Example(IMemberUserService ms)
	{
		obj = ms;
	}

	public void Execute(JobExecutionContext context)
	{
		try
		{
			// context.Log.WriteLine()方法提供開發者紀錄Job執行的相關資料
			// 若Job執行失敗時可以透過此資料發現問題
			// 此資料會在Job執行結束自動寫入資料庫中以便查詢
			context.Log.WriteLine("範例程式開始執行了");

			// 若該Job需要有參數各自設定時
			// 請透過context.JobParas[參數名稱]方法取得
			string strUser = context.JobParas["User"].ToString();
			obj.UpdateData(strUser);
		}
		catch (Exception ex)
		{
			//當Job執行失敗時，使用者可以利用context.Error.WriteLine()
			//以及context.StackTrace.WriteLine()來記錄Error Message以及Stack Trace
			context.Error.WriteLine(ex.Message);
			context.StackTrace.WriteLine(ex.StackTrace);
		}
	}
}
```

## 執行記錄
-----------------

Nebula CloudPlatform 在每次執行排程工作後，都會把排程的執行狀況記錄下來，開發者可透過 [開發者專區](http://www.quanta-camp.com/Developer/) 的 系統設定 -> 排程設定 -> 排程執行紀錄查詢功能、或透過 [Management Module](Module.Management.md) 的方法來查詢出排程執行的紀錄。

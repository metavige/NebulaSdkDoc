
Nebula.Schedule
================

提供使用者撰寫特定工作服務以及排程工作的功能。  

使用者可以藉於實作 `com.quanta.nebula.schedule.IJob` 此介面將工作邏輯撰寫成為一個 jar 檔，並在 [開發者專區] 設定該工作服務的執行時間。  

Quanta Cloud Platform 便會依照使用者設定的排程時間自動執行該工作服務。  
使用者可透過開發者專區中的排程管理功能來上傳排程工作的程式，以及透過排程管理功能來設定排程時間。  

IJob 的實作範例：

```java
import com.quanta.nebula.schedule;

public class HelloJob implements IJob {
	@Override
	public void Execute(JobExecutionContext context) {

		// Job Execute Logic .. .. ..

	}
}
```

## 使用說明

PaaS.Schedule中包含以下兩個物件:  

* `com.quanta.nebula.schedule.IJob`
	Interface。所有服務所必須繼承的介面，此介面只提供 `Execute(JobExecutionContext context)` 方法，使用者必須將服務寫在此方法中。

* `com.quanta.nebula.schedule.JobExecutionContext` 類別  
	此為服務啟用時所傳入的參數。包含的相關資料如下表，這些欄位資料可以供使用者在開發工作時使用，以便服務工作的追蹤與設定。  

### 類別屬性說明

`JobExecutionContext` 類別屬性，說明如下:  

屬性 | 方法 | 說明
--- | --- | ---
java.lang.String | getId() | 工作排程 Id
java.lang.String | getName() | 工作排程名稱
java.lang.String | getDescription() | 工作排程描述
java.lang.String | getProductId() | Product Id
java.lang.String | getSolutionId() | Solution Id.
java.util.Date | getLastInvokeTime() | 上次執行時間
java.util.Map<java.lang.String, java.lang.Object> | getJobParas() | 服務工作參數。若此工作有需要設定參數時，在執行時可以透過此欄位取得預設值。
java.io.PrintStream | getLog() | 服務執行紀錄
java.io.PrintStream | getError() | 錯誤訊息紀錄
java.io.PrintStream | getStackTrace() | Stack Trace 訊息紀錄

## IJob 範例

```java
import com.quanta.nebula.schedule;

public class HelloJob implements IJob {

	@Override
	public void Execute(JobExecutionContext context) {

        java.io.PrintStream log = context.getLog();
        java.io.PrintStream error = context.getError();
        java.io.PrintStream stackTrace = context.getStackTrace();

		// context.getLog().println()方法提供開發者紀錄Job執行的相關資料
		// 若Job執行失敗時可以透過此資料發現問題
		// 此資料會在Job執行結束自動寫入資料庫中以便查詢
		log.println("進去Task");

		// 若該Job需要有參數各自設定時
		// 請透過context.getJobParas().get("參數名稱")方法取得
		if (null == context.getJobParas().get("TestMode") ||
            !context.getJobParas().get("TestMode").equals("1")) {

			log.println("Hello World!");
		}
        else {
			try {

				List<Object> l = new ArrayList<Object>();
				l.get(9);

			}
            catch (Exception e) {

				//當Job執行失敗時，使用者可以利用context.getError().println()
				error.println(e.getMessage());

				// 將 Exception StackTrace 獨立寫入 StackTrace 的紀錄
				e.printStackTrace(stackTrace);
			}
		}

	}
}
```

## 執行記錄

Nebula CloudPlatform 在每次執行排程工作後，都會把排程的執行狀況記錄下來，開發者可透過 [開發者專區] 的 記錄查詢 -> 排程執行紀錄查詢功能、或透過 [Management Module](Module.Management.md) 的方法來查詢出排程執行的紀錄。

[開發者專區]: <http://www.quanta-camp.com/Developer/>

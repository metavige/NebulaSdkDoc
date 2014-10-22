
Database Module
================

Database Module 提供 SaaS 針對資料庫中的 Table 的基本操作，在 SaaS 開發中若要使用 Database Module 的相關功能，必須針對單一 table 建立對應 Entity 物件以及 Data Context 物件。  
目前 Database Module 是採用 [IQToolkit](http://iqtoolkit.codeplex.com/) 當作 LINQ Provider，所以相關的設定需要參考 `IQToolKit` 這套 framework。以下針對常用的功能作簡單的說明  

以下 Entity 的程式皆可使用 NEBULA Cloud Platform 所提供的程式產生器產生。 [CAMP](http://www.quanta-camp.com) 的 [開發者專區](http://www.quanta-camp.com/Developer/) 的 [產品管理] -> [SaaS產品資源總覽] 中，有提供程式產生器的功能  

## Entity Context
-----------------

每一個資料表都需要建立一個 EntityContext 對應物件，各欄位分別建立 public property 對應  

```csharp
using Quanta.PaaS.Module.Database;

public partial class Customer : PaaSEntityBase
{
	private string _CustomerID;
	public string CustomerID
	{
		get
		{
			return _CustomerID;
		}
		set
		{
			if (_CustomerID != value)
			{
				ReportPropertyChanged("CustomerID", value);
				this._CustomerID = value;
			}
		}
	}
}
// 此程式是透過開發者專區的程式產生器所產生的 Entity 程式
```

由程式產生器產生出來的程式，會加上 `ReportPropertyChanged` 方法，會傳入本身的欄位名稱以及值，Quanta Cloud Platform 會將欄位的變更記錄下來  
如果要關閉欄位的異動記錄，可使用以下的兩種方式：  

* web.config 設定

加入一組 AppSetting 設定，如果設定為 `false`，表示關閉異動記錄。預設為 `true`。  
此方法加入後，影響範圍為全系統  

```xml
<configuration>
	<appSettings>
		<add key="Nebula_EntityReportPropertyChanged" value="false" />
	</appSettings>
</configuration>
```

* Entity Override Property

此方式影響單一的 Entity，若系統中有單一資料表不需要記錄異動記錄，如 Log 記錄檔，可採用此方式。  

```csharp
using Quanta.PaaS.Module.Database;

public partial class Customer : PaaSEntityBase
{
	public override bool NeedReportPropertiesChanged
	{
		get
		{
			return false;
		}
	}
}
```

## Data Context
-----------------

`PaaSEntityContext` 的建構子，需要設定 `DatabaseName` 以及資料庫類型。由於目前 Nebula SDK 僅支援 MSSQL，所以目前預設使用 `TSqlLanauge.Default`  

```csharp
using System;
using System.Linq;
using IQToolkit;
using IQToolKit.Data;
using IQToolkit.Data.Mapping;
using IQToolkit.Data.SqlClient;

/// <summary>
/// 帶有DbName的建構子
/// </summary>
public NorthwindContext() : base("Northwind", TSqlLanguage.Default)
{

	// 將有需要根據關聯性一起取出的Data Member，在這邊宣告
	// 以下方法，第二個參數表示是否要Lazy Load
	// (在第一次取得資料的時候才去下查詢條件實際查詢出資料來)
	IncludeAssocation<Customer>((c) => c.Orders, true);
}
```

設定好 `EntityContext` 之後，需要在 `DataContext` 中設定每個資料表欄位的對應  

每個 `EntityContext` 需要在 `DataContext` 中設定一組 Public Property，透過 `PaaSEntityContext` 的 `GetTable` 方法，設定 `EntityID`  

在 `Property` 上，透過 `Attribute` 設定，設定每個資料表與 `EntityContext` 的屬性關係。

程式範例：  

```csharp
public class Customer
{
    public string CustomerID;
    public string ContactName;
    public string CompanyName;
    public string Phone;
    public string City;
    public string Country;

    public IList<Order> Orders;
}

/// <summary>
/// Customer 對應的 Entity Table
/// </summary>
[Table]
[Column(Member = "CustomerId", IsPrimaryKey = true)]
[Column(Member = "ContactName")]
[Column(Member = "CompanyName")]
[Column(Member = "Phone")]
[Column(Member = "City", DbType = "NVARCHAR(20)")]
[Column(Member = "Country")]
[Association(Member = "Orders", KeyMembers = "CustomerID", RelatedEntityID = "Orders", RelatedKeyMembers = "CustomerID")]
public IEntityTable&lt;Customer&gt; Customers
{
	get { return this.Provider.GetTable&lt;Customer&gt;("Customers"); }
}
```

可設定的屬性清單  

#### TableAttribute

用來設定對應的資料表名稱，預設會使用 Class Name  

可以使用的屬性 :

* Name : 資料表名稱，預設為類別名稱
* Alias : 資料表別名

#### ColumnAttribute

設定每個欄位對應資料表欄位的名稱、類型以及其他設定  


* Member : 物件對應屬性名稱
* Name(Optional) : 欄位名稱，預設會與物件對應屬性名稱一樣
* Alias : 欄位別名
* DbType(Optional) : 欄位類型，如果不設定，將由程式自行判斷
* IsComputed : 是否為複合資料型別
* IsPrimaryKey : 是否為主鍵
* IsGenerated : 是否由資料庫產生，通常用在 Primary Key

#### AssociationAttribute

這是用來設定外來鍵關連，如果有一對多、多對一、多對多關係的物件關連，可以透過這個設定，來設定關連資料表的關聯方式  

* Member : 物件對應屬性名稱
* Name(Optional) : 關連名稱，通常與物件對應屬性名稱一樣
* KeyMembers : 對應的主鍵屬性名稱
* RelatedEntityID : 對應的 EntityContext id
* RelatedEntityType(Optional) : 對應的 EntityContext 類型名稱，如果不設定，將由程式自行判斷
* RelatedKeyMembers : 關連的外來鍵屬性名稱
* IsForeignKey : 是否為外來鍵

## TransactionLog

Transaction Log 的紀錄，是針對每一個 Table 的各個欄位去記錄，包括新增、刪除以及修改便會自動記錄變更前後的數據。

當開發者在程式中呼叫 `EntityTable` 的新增、修改、刪除等語法，NEBULA 將自動把異動前後的資料記錄下來，之後便可在 [開發者專區](http://www.quanta-camp.com/Developer/) 的紀錄查詢功能來查詢 Log 紀錄，或透過 Logging Module 取出 Log 資料

```csharp
var dataContext = new NorthwindContext();

var customer = new Customer();
customer.CustomerID = "NewCus1";
cuscomer.CompanyName = "Quanta";

dataContext.Customers.Insert(customer);
```

### 實作方式

若需要異動記錄，需要根據以下的方式，設定 Entity 的程式內容 (可透過 [開發者專區](http://www.quanta-camp.com/Developer/) 程式產生器產生)  

使用者所實作的每一個 Entity 都需繼承 [Database Module](Module.Database.md) 的 Quanat.PaaS.Module.Database.PaaSEntityBase ，以及針對屬性欄位引用 ReportPropertyChanged 事件

```csharp
using Quanat.PaaS.Module.Database;

public class User : PaaSEntityBase
{
	private string _name;

	public string Name
	{
		get { return _name; }
		set
		{
			if (_name != value)
			{
				ReportPropertyChanged("Name", value);
				this._name = value;
			}
		}
	}
}
```
以上程式可透過 [開發者專區](http://www.quanta-camp.com/Developer/) 的程式產生器產生。

若開發者透過程式產生器產生 Entity 程式碼，預設是關閉 TransactionLog 的紀錄。若需要開啟 TransactionLog，可透過以下方式，開啟 TransactionLog

#### AppSettings 設定

透過在 `web.config` 中的 `AppSettings` 加入一個 `Nebula_EntityReportPropertyChanged` 的設定值，可將所有 Entity 的 TransactionLog 紀錄開啟。

```xml
<configuration>
	<appSettings>
		<add key="Nebula_EntityReportPropertyChanged" value="true" />
	</appSettings>
</configuration>
```

#### Entity Override

```csharp
using Quanat.PaaS.Module.Database;

public class User : PaaSEntityBase
{
	private string _name;

	public string Name
	{
		get { return _name; }
		set
		{
			if (_name != value)
			{
				ReportPropertyChanged("Name", value);
				this._name = value;
			}
		}
	}

    /// <summary>
    /// 用來判斷這個 Entity 是否需要作交易異動紀錄。
    /// 可單獨針對各 Entity 作設定
    ///
    /// 若未 Override 此屬性，將採用預設的方式或讀取 web.config 中的設定。
    /// </summary>
    /// <value>
    /// 	如果這個 Entity 需要紀錄交易異動，設定成<c>true</c> ; 否則就設定成<c>false</c>.
    /// </value>
	public override bool NeedReportPropertiesChanged
	{
		get
		{
			return false;
		}
	}
}
```

### 附註說明

目前 EntityTable 的 `BatchInsert`, `BatchUpdate` 方法不支援異動記錄。  

## 交易機制
----------------

供開發者在開發資料庫相關程式的時候，也可使用到交易功能  

###	交易範圍設定選項：
列舉類型為 `System.Transactions.TransactionScopeOption`

目前有 `Required`、`RequiresNew`、`Suppress` 三種類型  

類型 | 說明
--- | ---
Required | 交易是範圍所需的，如果已經存在環境交易，則會使用環境交易，否則會在進入範圍前建立新的交易。這是預設值。  
RequiresNew | 新交易一定會針對範圍而建立。  
Suppress | 當建立範圍時會隱藏環境交易內容，範圍內的所有作業都是在不使用環境交易內容的情況下完成。  

詳細文件可參考 MSDN [System.Transactions.TransactionScopeOption]  

### 交易隔離設定：

列舉類型為 `Quanta.PaaS.Module.Database.TransIsolationLevel`

目前 Nebula SDK 的交易隔離設定僅支援 `RepeatableRead`、`ReadCommited`、`ReadUncommited`、`Serialized` 四種類型。  

類型 | 說明
--- | ---
Serializable | 在交易期間可以讀取 Volatile 資料，但無法修改該資料，且不能加入新資料。
RepeatableRead | 在交易期間可以讀取 Volatile 資料，但無法修改該資料。 在交易期間可以加入新資料。
ReadCommitted | 在交易期間無法讀取 Volatile 資料，但可以修改該資料。
ReadUncommitted | 在交易期間可以讀取和修改 Volatile 資料。

以上設定可參考 MSDN 針對 [交易隔離](http://msdn.microsoft.com/zh-tw/library/system.transactions.isolationlevel.aspx) 的說明文件

### Default Settings

目前 sdk 中預設的交易類型如下

* Isolation Level : ReadCommited
* Timeout : 5 mins

### 使用方式

目前使用交易機制的方式有以下兩種：  

* 自行宣告交易範圍  

```csharp
using Quanta.PaaS.Module.Database;

using (var tx = new TransScope())
{
    // TODO: Sql 程式
    tx.Complete();
}
// 自行設定交易範圍、交易隔離、Timeout 時間。
using (var tx = new TransScope(TransactionScopeOption.RequiresNew,
        TransIsolationLevel.ReadCommitted, TimeSpan.FromMinutes(5))
{
    // TODO: Sql Code
	tx.Complete();
}
```  

* 透過 Unity Interceptor 機制
如果要透過這個方式，需搭配 [BootStrapper] 註冊的機制，將要採用交易機制的方法，透過介面宣告，並且將介面與實作在 [BootStrapper] 中透過 `RegisterTypeForInterception`  方法註冊，便可在此方法上加上交易範圍 (`TransactionScope`)  
當方法正常結束時，交易才算完成。若有錯誤發生，交易將取消。

```csharp
using Quanta.PaaS.Module.Unity.Policy;

public interface IDataService
{  
	[Transactional]
	string Modify(Data modifyData);

	[Transactional(IsolationLevel = TransIsolationLevel.ReadCommitted,
	Timeout = 300,
	TransactionScopeOption = TransactionScopeOption.Required]
	void Delete();
}

public class DataService : IDataService
{
	// Implementation Code
}
```

## Direct Connect to Database
----------------

在 Nebula SDK 1.6 前，存取資料庫的動作是透過 Nebula Kernel Service 作 SQL 處理。  
後續考量到效能影響，增加了一個機制可以直接與資料庫連線  
僅需透過 `web.config` 的設定，在 `cloudPlatform` 上加上一個 `production="true"` 設定，便可直接切換。  

```xml
<configuration>
	<cloudPlatform production="true">
		.. .. ..
	</cloudPlatform>
</configuration>
```

[BootStrapper]: <BootStrapper.md>
[System.Transactions.TransactionScopeOption]: <http://msdn.microsoft.com/zh-tw/library/system.transactions.transactionscopeoption(v=vs.80).aspx>

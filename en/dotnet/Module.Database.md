
Database Module
================

Database Module provides SaaS basic controls on database tables. You must create a corresponding Entity object and DataContext object to a single table when you want to use Database Module functions during SaaS development.

Database Module currently use [IQToolkit](http://iqtoolkit.codeplex.com/) as LINQ Provider, hence necessary configuration must refer to IQToolKit framework. The following below explain simple descriptions on frequent functions.

The following code can be created by Code Generator, which is provide by Quanta Cloud Platform. The Code Generator is at [Developer Area] -> SaaS Product Management -> SaaS Product Resource Overview.


## Entity Context
-----------------

Every datatable must create a corresponding EntityContext object, and each field must have a corresponding public property.  

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
// This entity code is created by the Code Generator in the Developer Area.
```

The code created by the Code Generator, coupled with the `ReportPropertyChanged` method, Quanta Cloud Platform will record and log the field data transaction when the field is edited.  

If you want to disable the transaction log, there are two ways to do it:  

* web.config settings

Add an AppSetting configuration. The transaction log setting is `true` by default, and is disabled when the setting is `false`. The entire system would be affected after this method is added.  

```xml
<configuration>
	<appSettings>
		<add key="Nebula_EntityReportPropertyChanged" value="false" />
	</appSettings>
</configuration>
```

* Entity Override Property

This way only affects a single Entity, if your system has one datatable which does not need to have transaction logs. Log-type entity class can use this way.  

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

DataContext is used to map Database and define its Database name.
`PaaSEntityContext` Constructor needs to define `DatabaseName` and database type. As the current Nebula SDK only supports MSQL, therefore the current default is `TSqlLanguage.Default`.  

```csharp
using System;
using System.Linq;
using IQToolkit;
using IQToolKit.Data;
using IQToolkit.Data.Mapping;
using IQToolkit.Data.SqlClient;

/// <summary>
/// Consturctor with DbName argument
/// </summary>
public NorthwindContext() : base("Northwind", TSqlLanguage.Default)
{

	// Related Data Member in different DataTables must be declared here.
	// The second variable in the method below indicates whetever needs Lazy Load.
	// (When system wants to obtain data for the first time, it would use the search conditions and actually query for data)
	IncludeAssocation<Customer>((c) => c.Orders, true);
}
```

After you configure `EntityContext`, you need to then configure the mapping on each data field in the Data Context.

Every `EntityContext` need to configure a set of Public Property in `DataContext`. Configure `EntityID` through the `GetTable` method of `PaaSEntityContext`.

Configure the mapping properties between DataTable and `EntityContext` by Attribute settings on Property.

Sample Code：  

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
/// Entity Table mapped to Customer
/// </summary>
[Table]
[Column(Member = "CustomerId", IsPrimaryKey = true)]
[Column(Member = "ContactName")]
[Column(Member = "CompanyName")]
[Column(Member = "Phone")]
[Column(Member = "City", DbType = "NVARCHAR(20)")]
[Column(Member = "Country")]
[Association(Member = "Orders", KeyMembers = "CustomerID", RelatedEntityID = "Orders", RelatedKeyMembers = "CustomerID")]
public IEntityTable<Customer> Customers
{
	get { return this.Provider.GetTable<Customer>("Customers"); }
}
```

Configurable attribute list

#### TableAttribute

Used to set the corresponding DataTable name, default by class name.

Usable attributes:

* Name : DataTable name, default by class name
* Alias : DataTable alias

#### ColumnAttribute

Configure every field correspond DataTable's fields, types and other settings.

* Member : object corresponding property name
* Name(Optional) : field name, default by object corresponding name
* Alias : field alias
* DbType(Optional) : data type; if it is not configured, it would be decided by the program itself.
* IsComputed : whetever it is composite data type.
* IsPrimaryKey : whetever it is Primary key.
* IsGenerated : whetever the data is generated by database. It is usually used on Primary key.

#### AssociationAttribute

This is to configure foreign key association. You can configure the related tables through this setting if the table has a one-to-many, many-to-one, many-to-many object relationship.

* Member : corresponding object property name
* Name(Optional) : corresponding name, usually the same with Corresponding object property name
* KeyMembers : Corresponding key property name
* RelatedEntityID : Corresponding EntityContext ID
* RelatedEntityType(Optional) : corresponding EntityContext type name. The program will configure by itself, if you do not configure it.
* RelatedKeyMembers : corresponding class foreign key
* IsForeignKey : whetever it is foreign key

## TransactionLog
-----------------

Transaction Log record, which is to log every field of every `DataTable`, including insert, delete and update, as well as its previous and new data.  

When developer call insert, update, and delete commands, Nebula Cloud Platform would automatically record the old and new data between the transaction. Hence you can use the Log Query function to query the Log history at [Developer Area], or by Logging Module to retrieve Log data.  


```csharp
var dataContext = new NorthwindContext();

var customer = new Customer();
customer.CustomerID = "NewCus1";
cuscomer.CompanyName = "Quanta";

dataContext.Customers.Insert(customer);
```

### Implementation

You can use the following demostration below to configure the Entity code if you need to log transaction. You can create it through the Code Generator in [Developer Area]

Every Entity class needs to inherit `Quanta.PaaS.Module.Database.PaaSEntityBase` of [Database Module](Module.Database.md), and use the ReportPropertyChange event on the target field.

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
The code above can be created by the Code Generator in the [Developer Area] .

If developer use the Entity code created by the Code Generator, and the TransactionLog record is off by default. You can use the following steps to enable it if you want to.

#### AppSettings configuration

Add a `Nebula_EntityReportPropertyChanged` value on the AppSettings, and TransactionLog on all tables would be enabled.

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
    /// Determine if this Entity requires TransactionLog.
    /// It can be configured on individual Entity.
    ///
    /// If there is no override, it would use the default settings or the web.config settings.
    /// </summary>
    /// <value>
    /// 	If this Entity requires TransactionLog, set it to:<c>true</c> ; otherwise <c>false</c>.
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

### Additional Description

Currently the `BatchInsert` and `BatchUpdate` of EntityTable are not supported by TransactionLog。

## Transaction Mechanism

Developer can use the Transaction function while developing database-based programs.

###	Transaction scope configuration options:
Enumeration type is `System.Transactions.TransactionScopeOption`

Currently there are three types: `Required`、`RequiresNew`、`Suppress`

Type | Description
--- | ---
Required | A transaction is required by the scope. It uses an ambient transaction if one already exists. Otherwise, it creates a new transaction before entering the scope. This is the default value.  
RequiresNew | A new transaction is always created for the scope.  
Suppress | The ambient transaction context is suppressed when creating the scope. All operations within the scope are done without an ambient transaction context.  

Reference document MSDN [System.Transactions.TransactionScopeOption]  

### Transaction isolation settings:
Enumeration type is `Quanta.PaaS.Module.Database.TransIsolationLevel`  

Currently there are four types of isolation transaction supported by Nebula SDK: `RepeatableRead`、`ReadCommited`、`ReadUncommited`、`Serialized`.  

Type | Description
--- | ---
Serializable | Volatile data can be read but not modified, and no new data can be added during the transaction.
RepeatableRead | Volatile data can be read but not modified during the transaction. New data can be added during the transaction.
ReadCommitted | Volatile data cannot be read during the transaction, but can be modified.
ReadUncommitted | Volatile data can be read and modified during the transaction.

Reference document [Transaction Isolation](http://msdn.microsoft.com/en-us/library/system.transactions.isolationlevel.aspx)

### Default Settings

The current SDK default transaction settings are as the followings:

* Isolation Level : ReadCommited
* Timeout : 5 mins

### Implementation

There are two ways to implement transaction mechanism：

* Declare transaction scope by yourself

```csharp
using Quanta.PaaS.Module.Database;

using (var tx = new TransScope())
{
    // TODO: Sql code
    tx.Complete();
}
// You set transaction scope option, transaction isolation level and timeout.
using (var tx = new TransScope(TransactionScopeOption.RequiresNew,
        TransIsolationLevel.ReadCommitted, TimeSpan.FromMinutes(5))
{
    // TODO: Sql Code
	tx.Complete();
}
```

* Through Unity Interceptor mechanism

If you want to use this method with the registration mechanism of [BootStrapper], use interface declaration on the required method, and register both the interface and the implementation class through RegisterTypeForInterception method on [BootStrapper], then this method is equipped with `TransactionScope`.
Transaction is considered complete when all operations are finished. Transaction would be cancelled if there is an error.

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

Prior Nebula SDK 1.6, all database SQL management are done through Nebula Kernel SDK.
Considering the follow-up efficiency affects, we add a new mechanism which enable connecting to database directly.
Only through `web.config` set on `cloudPlatform` with a `production="true"` set, you can switch directly.

```xml
<configuration>
	<cloudPlatform production="true">
		.. .. ..
	</cloudPlatform>
</configuration>
```

[Developer Area]: <http://www.quanta-camp.com/Developer/>
[BootStrapper]: <BootStrapper.md>
[System.Transactions.TransactionScopeOption]: <http://msdn.microsoft.com/zh-tw/library/system.transactions.transactionscopeoption(v=vs.80).aspx>

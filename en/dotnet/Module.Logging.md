Logging Module
================

Logging Module offers user to log and query for Error Log and Transaction Log

Error Logging and Transaction Log data are saved in Nebula CloudPlatform database.

User can use the Log Query in the [Quanta CAMP portal](http://www.quanta-camp.com/) [Developer Area](http://www.quanta-camp.com/Developer/) to search for Log records, or use Logging Module to get Log data.

If you want to use the Logging Module, please follow the instructions below


## TransactionLog Query
-----------------

Developer must reference [Database Module](Module.Database.md)

Sample code:

```csharp
using Quanta.PaaS.Module.Logging;

IList<LoggingTransactionContext> transLogs = LoggingModule.GetTransactionLog("Y", functionId: "SaveTransactionLog");
```

The first variable determines whetever the queried data is of Product environment, and it is a mandatory field. Other variables are optional and can be null.

The following is `LoggingTransactionContext` result object interface description：

```csharp
public class TransactionLogContext
{
	/// <summary>
	/// Whetever it is Production environment
	/// </summary>
	public string IsProduction { get; set; }

	/// <summary>
	/// SQL command
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
	/// Log the ThreadId on Client side
	/// </summary>
	public int ThreadId { get; set; }

	/// <summary>
	/// Value transaction information of the Property
	/// </summary>
	public IList<TransactionLogProperty> Property { get; set; }
}

public class TransactionLogProperty
{
	/// <summary>
	/// Function ID
	/// </summary>
	public string FunctionId { get; set; }

	/// <summary>
	/// Property Name
	/// </summary>
	public string PropertyName { get; set; }

	/// <summary>
	/// Old value data prior change
	/// </summary>
	public object OldValue { get; set; }

	/// <summary>
	/// New value data after change
	/// </summary>
	public object NewValue { get; set; }
}
```

## Error Log
---------------

[Management Module](Module.Management.md) can query for error message. It is strongly recommended to use this method to query.

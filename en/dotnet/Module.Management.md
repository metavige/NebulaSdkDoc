Management Module
================

Developer can use it to get logs of E-mail delivery, schedule execution result, and error. Developer can use this module to get necessary log data of a Product's operations.  

It is queried by time and setting of number of rows to get the corresponding results.
> The maximum query result rows are 200, for preventing user query for large amount of data which would affect the performance efficiency.

## Obtain Module
----------------

```csharp
using Quanta.PaaS.Module.Management;

IManageModule manageModule = AppContext.Current.GetModule<IManageModule>();
```

## Function descriptions
----------------

Management Module provides three functions

* Search for mail delivery result - `SearchMailStatus`
* Search for schedle execution result - `SearchScheduleExecuteHistory`
* Search for SaaS error log history - `SearchErrorLogHistory`

All three methods above use same inputs. The inputs are described as the followings:

* int recCount : Result number, which cannot be over 200. ArgumentException would be thrown if it is over 200.
* DateTime queryStart : Query start time
* DateTime queryEnd : Query end time

If the returned number of results is over 200, the system would return only the first 200 rows by descending Log datetime.  

## Returned data descriptions
----------------

The followings display return data types of each query; the object properties are described by the sample codes below.

* Mail delivery log:  

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

* Schedule execution result log:  

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

* SaaS application error log:  

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

## Examples
-----------------

```csharp
using Quanta.PaaS.Module;
using Quanta.PaaS.Module.Kernel.Contract.Management;

IManageModule module = AppContext.Current.GetModule<IManageModule>();
var result = module.SearchMailStatus(200, DateTime.Now.AddDays(-7), DateTime.Now);
```

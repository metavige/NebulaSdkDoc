Schedule Module
================

Provides user functions to code certain task services and schedule job.

Developer can code a task into a DLL file by implementing `IJob` interface, and set the execution configuration file. Then compress the DLL, Configuration file and all related DLL files used by the task into a ZIP file, and upload the ZIP file to [Developer Area]. Finally set the schedule execution for this task services on CAMP portal.

Quanta Cloud Platform would automatically perform the task service execution according to the user's schedule setting.
Developer can use the Schedule Maintain in the [Developer Area] to upload program, and use the Schedule Maintain to schedule execution time.

## Install
----------------

    Install-Package Nebula.Schedule

## IJob
----------------

IJob implementation：
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

## Description
-----------------

PaaS.Schedule contains the following two objects:  

* `IJob`  
	Interface. All services must inherit this interface; this interface only provide `Execute(JobExecutionContext context)` method , and developer must code services inside this method.

* `JobExecutionContext` type
	This is the input variable when service is activated, including related data as the following below. These data fields can be used by developer during development to track and set the task easily.

### Class property descriptions

`JobExecutionContext` class properties, described by the sample code below:  

```csharp
namespace Quanta.PaaS.Schedule
{
    /// <summary>
    /// Job execution data.
    /// </summary>
    public class JobExecutionContext
    {
        /// <summary>
        /// task schedule ID
        /// </summary>
        public long? Id { get; }

        /// <summary>
        /// task scheule name
        /// </summary>
        public string Name { get; }

        /// <summary>
        /// task schedule description
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
        /// last execution time
        /// </summary>
        public DateTime? LastInvokeTime { get; }

        /// <summary>
        /// task service variables. If this task requires configuration variables, you can use this field to get default values in runtime.
        /// </summary>
        public IDictionary<string, object> JobParas { get; }

        /// <summary>
        /// service execution log
        /// </summary>
        public TextWriter Log { get; }

        /// <summary>
        /// service error log
        /// </summary>
        public TextWriter Error { get; }

        /// <summary>
        /// Stack Trace message log
        /// </summary>
        public TextWriter StackTrace { get; }
    }
}
```

## IJob sample
-----------------

```csharp
using Quanta.PaaS.Schedule;

public class Example : IJob
{
	//You can use IoC method to use service if Job needs to invoke it
	public IMemberUserService obj { get; private set; }

	public Example(IMemberUserService ms)
	{
		obj = ms;
	}

	public void Execute(JobExecutionContext context)
	{
		try
		{
			// context.Log.WriteLine() method helps developer to log necessary data of job execution
			// you can use this data to find the bug if Job execution fails
			// data would be automatically saved to database for future query when Job execution is finished
			context.Log.WriteLine("Sample code starts to run");

			// If this Job has a variable to set
			// Please use context.JobParas[Variable name]
			string strUser = context.JobParas["User"].ToString();
			obj.UpdateData(strUser);
		}
		catch (Exception ex)
		{
			//When job execution fail, developer can use context.Error.WriteLine()
			//and use context.StackTrace.WriteLine() to log Error Message and Stack Trac
			context.Error.WriteLine(ex.Message);
			context.StackTrace.WriteLine(ex.StackTrace);
		}
	}
}
```

## Execution log
-----------------

Everytime a scheduled task is done, Nebula Cloud Platform would log the whole status. Developer can use SaaS Log -> Schedule Log in [Developer Area], or by [Management Module](Module.Management.md) , to get the schedule execution logs.

[Developer Area]: <http://www.quanta-camp.com/Developer/>

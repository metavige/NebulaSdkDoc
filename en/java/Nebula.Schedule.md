
Nebula.Schedule
================

Provide developers functions to code particular task services and schedule tasks.

Developers can create task codes into a jar file through implementing interface `com.quanta.nebula.schedule.IJob`, then set up the task execution time on [Developer Area].  

Quanta Cloud Platform would automatically execute task services according to the schedule time set up by developers.
Developers can upload schedule task codes on the Schedule Management function on Developer Area. Developers can also use the Schedule Management to set up schedule time.  

IJob Implementation Sample:  

```java
import com.quanta.nebula.schedule;

public class HelloJob implements IJob {
	@Override
	public void Execute(JobExecutionContext context) {

		// Job Execute Logic .. .. ..

	}
}
```

## Instruction Steps

PaaS.Schedule contains the following two objects:  

* `com.quanta.nebula.schedule.IJob`  
	Interface. All services must implement this interface. This interface provides `Execute(JobExecutionContext context)` function only, and developers must code the service in this function.  

* `com.quanta.nebula.schedule.JobExecutionContext` class  
	The parameter to run the service. Its data are as the following. These property data can be used to track and configure service tasks during development.  

### Class property description

`JobExecutionContext` class properties:

Property | Function | Description
--- | --- | ---
java.lang.String | getId() | Task Schedule Id
java.lang.String | getName() | Task Schedule Name
java.lang.String | getDescription() | Task Schedule Description
java.lang.String | getProductId() | Product Id
java.lang.String | getSolutionId() | Solution Id.
java.util.Date | getLastInvokeTime() | Last Execution time
java.util.Map<java.lang.String, java.lang.Object> | getJobParas() | Service task parameters. If the task requires to configure parameters, their default values can be obtained through this property.
java.io.PrintStream | getLog() | Service Execution Log
java.io.PrintStream | getError() | Error Message Log
java.io.PrintStream | getStackTrace() | Stack Trace Message Log

## IJob Example

```java
import com.quanta.nebula.schedule;

public class HelloJob implements IJob {

	@Override
	public void Execute(JobExecutionContext context) {

        java.io.PrintStream log = context.getLog();
        java.io.PrintStream error = context.getError();
        java.io.PrintStream stackTrace = context.getStackTrace();

		// context.getLog().println() provides developers Job execution log data
		// You can find the issues with these data if Job execution fails
		// These data would be saved into database for future query after the Job finishes
		log.println("Enter Task");

		// If this job requires parameter configuration
		// Please use context.getJobParas().get("Parameter Name")
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

				//When Job execution fails, developers can use context.getError().println()
				error.println(e.getMessage());

				// Write Exception StackTrace into StackTrace log seperately
				e.printStackTrace(stackTrace);
			}
		}

	}
}
```

## Execution Log

Nebula Cloud Platform logs task execution everytime it is performed. Developers can query schedule execution logs through SaaS Log -> Schedule Log on [Developer Area], or through [Management Module](Module.Management.md).

[Developer Area]: <http://www.quanta-camp.com/Developer/>

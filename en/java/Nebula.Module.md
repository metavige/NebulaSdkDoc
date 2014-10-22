Nebula.Module
================

Nebula.Module is the core module of the entire Nebula SDK; all Nebula fundamental features are contained here.  

Module List

* Database Module
* Mail Module
* Logging Module
* Exception Module

## AppContext
----------------

`com.quanta.nebula.AppContext` object is the main body of Nebula SDK. All Modules or some properties related to SaaS are obtained from this object.

### Common Property Instructions

* Obtain the current `AppContext`

```java
AppContextFactory appContextFactory = new AppContextFactoryImpl();
AppContext appContext = appContextFactory.create();

SolutionContext sc = appContext.getSolutionContext();
```

* ProductId

	Obtain the SaaS Product ID in `nebula.properties`

```java
string productId = appContext.getSolutionContext().getProductId();
```

* ApplicationId

	Obtain the SaaS Application ID in `nebula.properties`

```java
string applicationId = appContext.getSolutionContext().getApplicationId();
```

### Common Function Instructions

* GetModule&lt;IModuleInterface&gt;

	Object entities can only be obtained through this Nebula Module method

```java
IModuleInterface moduleInterface = appContext.getModule(IModuleInterface.class);
```

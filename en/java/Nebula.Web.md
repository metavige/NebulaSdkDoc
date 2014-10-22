Nebula.Web
================

JAVA Web Project and provides necessary Provider supports  

## WebAppContext
----------------

Obtain Modules and SaaS related property values by `AppContext` through `com.quanta.nebula.web.WebAppContext`  

## Examples
----------------

### Obtain Module

```java
AppContext appContext = WebAppContext.getCurrentAppContext();

Module module = appContext.getModule(Module.class);
```

### Obtain SaaS properties

```java
// Obtain the SaaS Product ID in the nebula.properties configuration
string productId = WebAppContext.getCurrentAppContext().getSolutionContext().getProductId();

// Obtain the SaaS Application ID in the nebula.properties configuration
string applicationId = WebAppContext.getCurrentAppContext().getSolutionContext().getApplicationId();
```

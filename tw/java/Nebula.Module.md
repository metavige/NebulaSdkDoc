Nebula.Module
================

Nebula.Module 是整個 Nebula SDK 的核心模組，所有的 Nebula 基本功能模組都包含在這裡

模組列表

* Database Module
* Mail Module
* Logging Module
* Exception Module

## AppContext
----------------

`com.quanta.nebula.AppContext` 這個物件，為 Nebula SDK 的主體 ，所有的 Module 或與一些與 SaaS 相關的屬性值，都透過此物件取得。

### 常用屬性說明

* 取得目前環境的 `AppContext`

```java
AppContextFactory appContextFactory = new AppContextFactoryImpl();
AppContext appContext = appContextFactory.create();

SolutionContext sc = appContext.getSolutionContext();
```

* ProductId

	取得此 SaaS 應用程式在 `nebula.properties` 中設定的產品代碼

```java
string productId = appContext.getSolutionContext().getProductId();
```

* ApplicationId

	取得此 SaaS 應用程式在 `nebula.properties` 設定的應用程式代碼

```java
string applicationId = appContext.getSolutionContext().getApplicationId();
```

### 常用方法說明

* GetModule&lt;IModuleInterface&gt;

	目前 Nebula Module 的取得，都需要透過這個方法來取得實體物件

```java
IModuleInterface moduleInterface = appContext.getModule(IModuleInterface.class);
```

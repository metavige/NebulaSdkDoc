Nebula.Web
================

支援 JAVA Web Project，提供相關的 Provider 支援

## WebAppContext
----------------

透過 `com.quanta.nebula.web.WebAppContext` 取得 `AppContext`, 以利取得 Module 與 Saas 相關屬性值

## 使用範例
----------------

### 取得 Module

```java
AppContext appContext = WebAppContext.getCurrentAppContext();

Module module = appContext.getModule(Module.class);
```

### 取得Saas相關屬性值

```java
// 取得此 SaaS 應用程式在 nebula.properties 中設定的產品代碼
string productId = WebAppContext.getCurrentAppContext().getSolutionContext().getProductId();

// 取得此 SaaS 應用程式在 nebula.properties 設定的應用程式代碼
string applicationId = WebAppContext.getCurrentAppContext().getSolutionContext().getApplicationId();
```

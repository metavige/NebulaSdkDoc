Quick Start
================

透過簡單的說明，讓開發者快速建立 Java Project

## 系統需求
----------------

* JAVA JDK 6 以上
* Nebula SDK (目前版本為2.3.0.0)

## 專案建立步驟
----------------

以下說明開發工具以 Eclipse 為例  

### 建立新專案

開啟 Eclipse, 選擇 New->Dynamic Web Project  

![QuickStart1](../../images/javaImages/QuickStart01.png)  

![QuickStart2](../../images/javaImages/QuickStart02.png)  

![QuickStart3](../../images/javaImages/QuickStart03.png)  

### 將jar檔至於WEB-INF/lib

Jar 擋如下列所示

* nebula.core.jar
* nebula.module.jar
* nebula.web.jar
* activation-1.1.jar
* cometd-6.1.4.jar
* dwr-3.0.M1.jar
* jackson-core-asl-1.9.2.jar
* jackson-jaxrs-1.9.2.jar
* jackson-mapper-asl-1.9.2.jar
* jackson-xc-1.9.2.jar
* javaee-web-api-6.0.jar
* jaxb-api-2.2.2.jar
* jaxb-impl-2.2.3-1.jar
* jersey-bundle-1.8.jar
* jersey-client-1.17.1.jar
* jersey-core-1.17.1.jar
* jersey-json-1.17.1.jar
* jsr311-api-1.1.1.jar
* mail-1.4.7.jar
* slf4j-api-1.7.5.jar
* stax-api-1.0-2.jar

### 設定nebula.properties

nebula.properties需置於 src/WEB-INF 資料夾下

```
# Define Nebula Kernel Service Position
cloudplatform.paas.location=http://nebula.quanta-camp.com

# Define Solution Information
cloudplatform.solution.productId=[產品代碼]
cloudplatform.solution.applicationId=[應用程式代碼]

# Define Developer Authorize Information
cloudplatform.developer.account=[帳號]
cloudplatform.developer.authorityCode=[授權碼]

# Define JDBC URL
cloudplatform.jdbc.driver=com.microsoft.sqlserver.jdbc.SQLServerDriver
cloudplatform.jdbc.url=jdbc:sqlserver://[DBIP]:1433;databasename=***;user=***;password=***;selectMethod=cursor
cloudplatform.jndi=java:/comp/env/jdbc/resource_name
```

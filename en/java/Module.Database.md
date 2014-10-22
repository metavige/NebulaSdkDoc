Database Module
==================

Database Module 提供 SaaS 針對資料庫中的 Table 的基本操作，在 SaaS 開發中若要使用 Database Module 的相關功能，需先設定 JNDI。

## JNDI設定方式
-----------------

* 正式環境
無需設定，建立VM時會自動設定  

* 測試環境
透過 `nebula.properties` 設定，參數如下，至少需設定一個，若兩個都有設定以 `cloudplatform.jndi` 為主  

	* `cloudplatform.jndi`: 使用 context.xml 中 JNDI 設定
	* `cloudplatform.jdbc.url`, `cloudplatform.jdbc.driver`: 使用JDBC連線, 此為一組, 必須同時設定


`nebula.properties` 範例:  

```
# Define JDBC URL
cloudplatform.jdbc.driver=com.microsoft.sqlserver.jdbc.SQLServerDriver
cloudplatform.jdbc.url=jdbc:sqlserver://192.168.0.111:1433;databasename=***;user=***;password=***;selectMethod=cursor
cloudplatform.jndi=java:/comp/env/jdbc/resource_name
```

## 實作方式
------------------

透過 DBModule 輸入 resource name 取得 datasource，再進一步取得 connection

### 直接撰寫

```java
AppContext appContext = WebAppContext.getCurrentAppContext();
appContext.start();
DBModule dbModule = appContext.getModule(DBModule.class);

DataSource ds = dbModule.getDataSource("resource_name");
Connection conn = ds.getConnection();
```


以下範例是使用 Spring Framework  

------------------
### web project

#### spring-config.xml

```xml
<bean id="nebulaAppContext" class="com.quanta.nebula.web.bean.NebulaAppContext">
	<property name="webAppContext" ref="webAppContext" />
</bean>
<bean id="webAppContext" class="com.quanta.nebula.web.WebAppContext"/>
<bean id="appContext" factory-bean="nebulaAppContext" factory-method="getAppContext"/>

<bean id="nebulaDdataSource" class="com.quanta.nebula.module.bean.NebulaDataSource">
	<property name="jndiName" value="resource_name" />
	<property name="appContext" ref="appContext" />
</bean>

<bean id="dataSource" factory-bean="nebulaDdataSource" factory-method="getDataSource"/>
```

#### java code

```java
WebApplicationContext ctx = WebApplicationContextUtils.getRequiredWebApplicationContext(this.getServletContext());
DataSource ds = (DataSource) ctx.getBean("dataSource");
Connection conn = ds.getConnection();
```

------------------
### java project

#### spring-config.xml

```xml
<bean id="nebulaDdataSource" class="com.quanta.nebula.module.bean.NebulaDataSource">
	<property name="jndiName" value="resource_name" />
</bean>

<bean id="dataSource" factory-bean="nebulaDdataSource" factory-method="getDataSource"/>
```

#### java code

```java
ApplicationContext context = new ClassPathXmlApplicationContext("spring-config.xml");
DataSource dataSource = (DataSource) context.getBean("dataSource");
Connection conn = null;
conn = dataSource.getConnection();
```

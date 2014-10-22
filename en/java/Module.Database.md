Database Module
==================

Database Module provides SaaS basic controls on database tables. You must configure JNDI first when you want to use Database Module functions during SaaS development.  

## JNDI Configuration
-----------------

* Production Environment
No configuration required; it will be automatically configured during VM creation.  

* Testing Environments

The followings are the variables in `nebula.properties` configuration; and you must configure at least one of them. If both are configured then it will be based on `cloudplatform.jndi`

	* `cloudplatform.jndi`: Use JNDI configuration in context.xml
	* `cloudplatform.jdbc.url`, `cloudplatform.jdbc.driver`: For using JDBC connection; both variables must be configured.

`nebula.properties` examples:

```
# Define JDBC URL
cloudplatform.jdbc.driver=com.microsoft.sqlserver.jdbc.SQLServerDriver
cloudplatform.jdbc.url=jdbc:sqlserver://192.168.0.111:1433;databasename=***;user=***;password=***;selectMethod=cursor
cloudplatform.jndi=java:/comp/env/jdbc/resource_name
```

## Implementation
------------------

Obtain datasource by entering resource name through DBModule, to obtain a connection

### Code directly

```java
AppContext appContext = WebAppContext.getCurrentAppContext();
appContext.start();
DBModule dbModule = appContext.getModule(DBModule.class);

DataSource ds = dbModule.getDataSource("resource_name");
Connection conn = ds.getConnection();
```


The following example is using the Spring Framework:   

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

Quick Start
================

Enable user to quickly create a JAVA project through simple instructions

## System Requirements
----------------

* JAVA JDK 6 or higher
* Nebula SDK(Current version is 2.3.0.0)

## Project set up steps
----------------

The following instructions are based on Eclipse IDE

### Create new project
Open Eclipse and select New->Dynamic Web Project

![QuickStart1](../../images/javaImages/QuickStart01.png)  

![QuickStart2](../../images/javaImages/QuickStart02.png)  

![QuickStart3](../../images/javaImages/QuickStart03.png)  

### Put jar files into WEB-INF/lib

Jar files are displayed as the followings

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

### Configure nebula.properties

nebula.properties must be put under the src/WEB-INF folder  

```
# Define Nebula Kernel Service Position
cloudplatform.paas.location=http://nebula.quanta-camp.com

# Define Solution Information
cloudplatform.solution.productId=[Product ID]
cloudplatform.solution.applicationId=[Application ID]

# Define Developer Authorize Information
cloudplatform.developer.account=[Account]
cloudplatform.developer.authorityCode=[Authority Code]

# Define JDBC URL
cloudplatform.jdbc.driver=com.microsoft.sqlserver.jdbc.SQLServerDriver
cloudplatform.jdbc.url=jdbc:sqlserver://[DBIP]:1433;databasename=***;user=***;password=***;selectMethod=cursor
cloudplatform.jndi=java:/comp/env/jdbc/resource_name
```

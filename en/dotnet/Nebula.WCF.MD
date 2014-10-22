Nebula.WCF
================

This Package is used to support WCF project. If developer wants to code WCF project and wants to use [Dependency Injection], developer can install this package to combine with Nebula SDK.

## Install
----------------

    Install-Package Nebula.WCF

## Implementation
----------------

You can use the [Dependency Injection] functions provided by Nebula SDK through modifying `Factory` settings on SVC file .  

```xml
<%@ ServiceHost Language="C#" Debug="true"
	Factory="Quanta.PaaS.WcfService.SaaSServiceHostFactory"
	Service="SampleWCF.HelloService"
	CodeBehind="HelloService.svc.cs" %>
```

[Dependency Injection]: <DI.MD>

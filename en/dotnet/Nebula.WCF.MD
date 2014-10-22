
Nebula.WCF
================

這個 Package 是用來支援 WCF 專案的。  
若開發者需要撰寫 WCF 專案，並且要結合 [Dependency Injection](DI.md) ，可以安裝這個 Package 來與 Nebula SDK 結合。  


## Install
----------------

    Install-Package Nebula.WCF

## 使用方式
----------------

透過修改 SVC 檔案的設定，設定 `Factory` 屬性  
便可使用 NEBULA SDK 中所提供的 [Dependency Injection](DI.MD) 功能。  

```xml
<%@ ServiceHost Language="C#" Debug="true"
	Factory="Quanta.PaaS.WcfService.SaaSServiceHostFactory"
	Service="SampleWCF.HelloService"
	CodeBehind="HelloService.svc.cs" %>
```

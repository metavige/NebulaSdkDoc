
模組簡介
================

目前 Nebula SDK 所提供的模組簡要說明如下，各位可視專案的種類自行加入使用

* Nebula.Core
Nebula 核心程式，與 Nebula Kernel Service 溝通的核心模組。

* [Nebula.Module](Nebula.Module.md)
NEBULA SDK 模組程式，所有目前 Nebula SDK 所提供的模組程式都在這個 package 中。  

* [Nebula.Web](Nebula.Web.md)
NEBULA SDK Asp.Net Web支援模組，針對 Asp.Net 提供一些基礎的服務。  

* MVC
支援 MVC 基礎的服務，如定義 Multi-Tenancy 的 Route 方法，以及與 ControllerFactory 結合，提供 [Dependency Injection](DI.md)  
根據不同的 MVC 版本，選擇不同的 NEBULA SDK Package：  
  * [Nebula.Web.Mvc](Nebula.Web.Mvc.md) - Asp.Net MVC 2
  * [Nebula.Web.Mvc3](Nebula.Web.Mvc3.md) - Asp.Net MVC 3
  * [Nebula.Web.Mvc4](Nebula.Web.Mvc4.md) - Asp.Net MVC 4
  * [Nebula.Web.Mvc5](Nebula.Web.Mvc5.md) - Asp.Net MVC 5
  

* Entity Framework support
提供 Entity framework 支援，開發者可以透過 Entity 來存取 NEBULA 的資料庫資源  
  * [Nebula.Data.Entity](Nebula.Data.Entity.md) - 支援 Entity Framework 5
  * [Nebula.Data.EF6](Nebula.Data.EF6.md) - 支援 Entity Framework 6


* [Nebula.Web.Identity](Nebula.Web.Identity.md)
支援 Asp.Net Identity，適用於 Asp.Net MVC 5 之後的專案  

* [Nebula.Data.Mongo](Nebula.Data.Mongo.md)
支援 Mongo 資源存取 

* [Nebula.Web.Security](Nebula.Web.Security.md)
支援 OAuth2 的登入驗證模組，提供 Asp.Net 的 HttpHandler 將 [OAuth2](http://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0CDgQFjAA&url=http%3A%2F%2Foauth.net%2F2%2F&ei=KjbtUJi2F4zckgW1_YD4AQ&usg=AFQjCNG58oSRksGnoIWIhfWYBB1sg_YGng&sig2=L5mNNVdzBASopkaVxaQ_2A) 驗證流程作整合，用一個簡單的方式提供一般的 Asp.Net 專案與 [CAMP](http://www.quanta-camp.com/) 的帳號作整合。  
OAuth2 的流程，可以參考 [登入驗證](Authentication.md)  

* [Neubla.WCF](Nebula.WCF.md)
針對 WCF 專案，提供 ServiceFactory 的支援，可與 [Nebula.Module](Nebula.Module.md) 結合 [Dependency Injection](DI.md) ，可以將 [Nebula.Module](Nebula.Module.md) 注入 WCF Service。  

* [Neubla.Schedule](Nebula.Schedule.md)
支援排程 Job 的介面，撰寫排程 Job 程式後，可透過 [開發者專區](http://www.quanta-camp.com/Developer) 的排程設定功能，上傳排程的程式以及設定排程檔案  

* [Neubla.Office](Nebula.Office.md)
針對 Open XML 格式的 Excel文件 (xlsx) 的存取，需搭配 Open XML SDK 2.0   

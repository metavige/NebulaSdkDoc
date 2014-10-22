
Multi-Tenancy
================

Nebula CloudPlatform 支援 [多租戶 (Multi-Tenancy)](http://en.wikipedia.org/wiki/Multitenancy) 的程式開發，透過傳遞給程式一組 `TenancyId` ，Nebula SDK 會將該使用者所使用到的 Solution Id 取回，在 `AppContext` 中提供給各產品的程式中使用。

透過在 [開發者專區](http://www.quanta-camp.com/Developer/) 啟用產品的多租戶功能，便可在程式中使用  

## Solution Id
----------------

每個產品，透過在 [CAMP](https://www.quanta-camp.com/) 上架的方式，提供給使用者購買。
使用者購買產品之後，以該使用者的公司為單位，每個公司將分配一組方案代碼 (Solution Id) 給該公司。  

啟用多租戶功能之後，由 [開發者專區](http://www.quanta-camp.com/Developer/) 中的開發測試/模擬測試功能，或由 [會員中心](https://member.quanta-camp.com) 進入 SaaS 應用程式，系統都會動態在網址上加入一組動態的 `TenancyId`，SaaS 應用程式中便可以透過 Nebula SDK 來取得該使用者所屬的 Solution Id。  



根據不同的專案類型，專案中若要支援 Multi-Tenancy，請參考以下的設定與程式：  

## Asp.Net Mvc
----------------

由於 Asp.Net Mvc 有 Route 機制，故在 Asp.Net Mvc 上，修改的方式比較簡單。

* 根據 MVC 的版本，加入不同版本的 Nebula MVC Package
    * [Nebula.Web.Mvc](dotnet/Nebula.Web.Mvc.md) (Support Asp.Net Mvc 2)
    * [Nebula.Web.Mvc3](dotnet/Nebula.Web.Mvc3.md)  (Support Asp.Net Mvc 3)
    * [Nebula.Web.Mvc4](dotnet/Nebula.Web.Mvc4.md)  (Support Asp.Net Mvc 4)
    * [Nebula.Web.Mvc5](dotnet/Nebula.Web.Mvc5.md)  (Support Asp.Net Mvc 5)  
* 修改 `App_Start/RouteConfig.asax.cs` 中的 `Route` 設定。Nebula SDK 提供了一組 `MapPaaSRoute` 的 Route Collection Extension 方法，這樣開發者在本機使用 Visual Studio 開發的時候，因為無法產生 TenancyId，仍舊可以測試。  


```csharp
// 將 routes.MapRoute 方法改為 routes.MapPaaSRoute 方法，參數不變。
routes.MapPaaSRoute(
    "Default", // Route name
    "{controller}/{action}/{id}", // URL with parameters
    new { controller = "Home", action = "Index", id = UrlParameter.Optional } // Parameter defaults
);
```

Web API 在 [Nebula.Web.Mvc5](dotnet/Nebula.Web.Mvc5.md) 之後，開始支援 Multi-Tenancy，程式修改方式與 Mvc Router 類似。  
修改 `App_Start/WebApiConfig.cs` 的 route 設定：  

```csharp
config.Routes.MapPaaSHttpRoute(
    name: "DefaultApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { id = RouteParameter.Optional }
);
```

## Asp.Net
----------------

在 Asp.Net 中，需要透過 `HttpHandler` 來取得 TenancyId。  

* 加入 [Nebula.Web](dotnet/Nebula.Web.md) Package
* 加入以下設定，並且在應用程式設定中選擇 Asp.Net 的類型，Nebula CloudPlatform 所提供的連結，將會以 `/Startup` 為應用程式的入口，提供 TenancyId。

```xml
<configuration>
    <system.web>
        <!-- CAMP 網站進入 Asp.Net 的入口，Path 固定為 Startup -->
        <httpHandlers>
            <add path="Startup" verb="GET" type="Quanta.PaaS.Web.PaaSStartupHandler, Quanta.PaaS.Web" />
        </httpHandlers>
    </system.web>
    <system.webServer>
        <handlers>
            <add name="PaaSStartupHandler" verb="*" path="Startup"
              type="Quanta.PaaS.Web.PaaSStartupHandler, Quanta.PaaS.Web"/>
        </handlers>
    </system.webServer>
</configuration>
```

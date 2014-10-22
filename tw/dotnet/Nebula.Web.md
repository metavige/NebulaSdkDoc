Nebula.Web
================

支援 Asp.Net 專案，並且提供 Asp.Net Security 相關的 Provider 支援

此 Package 提供了一個 [Security Module](Module.Security.md) 模組，詳細內容請參考 [Security Module](Module.Security.md) 內容說明。

## Install
----------------

    Install-Package Nebula.Web

## Multi-Tenancy Support
----------------

Asp.Net 由於沒有 Asp.Net Mvc 的 Routes 特性，故在支援 Multi-Tenancy 時，需要加入由 Nebula.Web 所提供的 `HttpHandler`，來支援 Multi-Tenancy。
詳細的設定參考 [Multi-Tenancy](../MultiTenancy.md)

## Session Provider Support
----------------

目前 Nebula.Web Package 中提供了兩種 Session Provider 的方式

* 以資料庫方式同步: `Quanta.PaaS.Web.Security.PaaSSessionProvider`

Nebula SDK 提供 Session State Provider 主要目的是為了 Cloud Load Balance 機制，需要同步 Session State 所使用。若 SaaS 應用程式並不需要同步 Session 狀態，則可不需要設定。

設定方式範例:
```xml
<configuration>
	<system.web>
		<sessionState mode="Custom" customProvider="PaaSSessionProvider">
			<providers>
				<add name="PaaSSessionProvider" type="Quanta.PaaS.Web.Security.PaaSSessionProvider"
					timeout="60" regenerateExpiredSessionId="true" />
			</providers>
		</sessionState>
	</system.web>
</configuration>
```

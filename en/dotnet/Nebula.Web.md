Nebula.Web
================

Supports Asp.Net project, and provides Asp.Net Security related Provider supports.

This Package provides a [Security Module] module. Please read [Security Module] for more information.

## Install
----------------

    Install-Package Nebula.Web

## Multi-Tenancy Support
----------------

As Asp.Net does not have the Routes feature of Asp.Net Mvc, hence you need to add `HttpHandler` provided by Nebula.Web to support Multi-Tenancy if your SaaS is Multi-Tenancy.
Please read [Multi-Tenancy](../MultiTenancy.md) for more information

## Session Provider Support
----------------

Nebula.Web Package currently provide  Session Provider

* Database synchronization: `Quanta.PaaS.Web.Security.PaaSSessionProvider`

Nebula SDK provides Session State Provider for Cloud Load Balance technique, which needs to synchronize with Session State. If SaaS application does not need to synchronize with Session State, then the configuration is not required.

Setting example:
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

[Security Module]: <Module.Security.md>

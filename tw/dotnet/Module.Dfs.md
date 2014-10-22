Dfs Module
================

Dfs Module 提供使用者查詢 dfs 路徑的功能。  
取得相對路徑之後，便可在 NEBULA 的 SaaS 環境上透過 File IO 方式存取檔案或目錄。

## 使用範例
----------------

透過 Resource ID 取得 dfs path  

```csharp
using Quanta.PaaS.Module;
using Quanta.PaaS.Module.Dfs;

var dfsModule = AppContext.Current.getModule<IDfsModule>();

var resId = "***";
var path = dfsModule.GetRelativeDfsPath(resId);
```

> Resource ID 請到 [開發者專區](http://www.quanta-camp.com/Developer/) 申請 DFS 資源後取得

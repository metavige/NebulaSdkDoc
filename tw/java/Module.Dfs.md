Dfs Module
=================

Dfs Module 提供使用者查詢 dfs 路徑的功能

## 使用範例
----------------

透過 Resource ID 取得 dfs path

```java
AppContext appContext = WebAppContext.getCurrentAppContext();

DfsModule dfsModule = appContext.getModule(DfsModule.class);

String resId = "***";
String path = dfsModule.getRelativeDfsPath(resId);
```

> Resource ID 請到 [開發者專區](http://www.quanta-camp.com/Developer/) 申請 DFS 資源後取得

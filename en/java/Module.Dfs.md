Dfs Module
=================

Dfs module provides users function to query dfs paths

## Examples
----------------

Obtain DFS path through Resource ID

```java
AppContext appContext = WebAppContext.getCurrentAppContext();

DfsModule dfsModule = appContext.getModule(DfsModule.class);

String resId = "***";
String path = dfsModule.getRelativeDfsPath(resId);
```

> Resource ID go [Developer Area](http://www.quanta-camp.com/Developer/) apply after obtaining the DFS resource

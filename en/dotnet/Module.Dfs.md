Dfs Module
================

Dfs module provides users function to query dfs paths.

## Examples
----------------

Obtain DFS path through Resource ID  

```csharp
using Quanta.PaaS.Module;
using Quanta.PaaS.Module.Dfs;

var dfsModule = AppContext.Current.getModule<IDfsModule>();

var resId = "***";
var path = dfsModule.GetRelativeDfsPath(resId);
```

> Resource ID go [Developer Area](http://www.quanta-camp.com/Developer/) apply after obtaining the DFS resource

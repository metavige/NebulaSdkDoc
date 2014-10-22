Solution Module
=================

此模組，提供給開發者針對 Solution 處理的一些共用方法。

## 取得購買產品的公司資料
-----------------

開發者若需要取得購買此產品的公司資料，可透過 `GetCompanyInfos` 這個方法來取得：

介面說明：

```csharp
namespace Quanta.PaaS.Module.Solution
{
	public interface ISolutionModule
	{
	    /// <summary>
	    /// 取得目前產品所關聯的公司代碼與名稱資料。
	    /// </summary>
	    /// <returns></returns>
	    IEnumerable<Company> GetCompanyInfos();
	}

	/// <summary>
	/// 公司代碼與名稱資料。
	/// </summary>
	public class Company
	{
	    /// <summary>
	    /// 公司代碼
	    /// </summary>
	    public string Id { get; }
	    /// <summary>
	    /// 公司名稱。
	    /// </summary>
	    public string Name { get; }
	}
}
```

使用範例：

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using System.Web;
using System.Web.Mvc;
using Quanta.PaaS.Module;
using Quanta.PaaS.Module.Solution;

public class MyController : Controller
{

	public ActionResult Index()
	{
		var solutionModule = AppContext.Current.GetModule<ISolutionModule>();

		IEnumerable<Company> allCompanyInfos = solutionModule.GetCompanyInfos();

		return View();
	}
}
```

Solution Module
=================

This module provide developer some common management functions on Solution.  

## Get company's data which purchases this SaaS Product
-----------------

Developer can use `GetCompanyInfos` method to get the data of all companys which purchases this SaaS Product.

Interface descriptions:  

```csharp
namespace Quanta.PaaS.Module.Solution
{
	public interface ISolutionModule
	{
	    /// <summary>
	    /// Get the Product's currently related companys' IDs and names.
	    /// </summary>
	    /// <returns></returns>
	    IEnumerable<Company> GetCompanyInfos();
	}

	/// <summary>
	/// Data of Company ID and name.
	/// </summary>
	public class Company
	{
	    /// <summary>
	    /// Company ID.
	    /// </summary>
	    public string Id { get; }
	    /// <summary>
	    /// Company name.
	    /// </summary>
	    public string Name { get; }
	}
}
```

Sample Code:  

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

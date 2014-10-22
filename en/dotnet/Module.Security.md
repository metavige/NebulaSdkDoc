
Security Module
=================

This Module is currently in the [Nebula.Web](Nebula.Web.md) package.  

## Get a list of functions which user has privilege to use.
-----------------

```csharp
IList<FuncContext> GetFuncList(string[] roleIds, string language);
```

If you want to use this function, you need to use the AP Setting -> Function Setting in the [Developer Area](http://www.quanta-camp.com/Developer/), and set every user role in [CAMP](http://www.quanta-camp.com/). Therefore through this function, you can obtain a list of functions you can use when log in.  

Sample code:  

```csharp
using Quanta.PaaS.Web.Security;

// First obtain Module Instance
ISecurityModule securityModule = AppContext.Current.GetModule<ISecurityModule>();

string[] roleIds = System.Web.Security.Roles.GetRolesForUser();
IList<FuncContext> allFuncList = securityModule.GetFuncList(roleIds, "zh-TW");
```

Get `FuncContext` object properties; they are described in the sample code below:  

```csharp
namespace Quanta.PaaS.Web.Security
{
    /// <summary>
    /// Function List variable data。
    /// </summary>
	public class FuncContext
	{
		/// <summary>
        /// function ID。
		/// </summary>
		public string FunctionId { get; set; }

		/// <summary>
        /// parent group ID.
		/// </summary>
		public string ParentGroup { get; set; }

		/// <summary>
        /// function sequential order.
		/// </summary>
		public string OrderBy { get; set; }

		/// <summary>
        /// whetever requires ID authentication.
		/// </summary>
		public string Authorized { get; set; }

		/// <summary>
        /// Link Link URL of function.
		/// </summary>
		public string LinkUrl { get; set; }

		/// <summary>
        /// function type
		/// </summary>
        /// <remarks>G = Group, F = Function。</remarks>
		public string Type { get; set; }

		/// <summary>
        /// whetever to display the list.
		/// </summary>
		public string Visible { get; set; }

		/// <summary>
        /// whetever control action.
		/// </summary>
		public string ActionControl { get; set; }

		/// <summary>
        /// function name.
		/// </summary>
		public string Name { get; set; }

		/// <summary>
        /// function description.
		/// </summary>
		public string Description { get; set; }
    }
}
```

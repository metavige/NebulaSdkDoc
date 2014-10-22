
Security Module
=================

這個 Module 目前在 [Nebula.Web](Nebula.Web.md) package 中。  

## 取得使用者有權限的功能清單資料
-----------------

```csharp
IList<FuncContext> GetFuncList(string[] roleIds, string language);
```

若要使用這個功能，需要先利用 [開發者專區](http://www.quanta-camp.com/Developer/) 中的 系統設定 -> 權限設定 等相關功能，設定好系統的相關功能與權限關係，並且透過 [CAMP](http://www.quanta-camp.com/) 中設定每個使用者的角色，便可透過此功能取得登入使用者所可使用的功能清單。  

使用範例說明:

```csharp
using Quanta.PaaS.Web.Security;

// 先取得 Module Instance
ISecurityModule securityModule = AppContext.Current.GetModule<ISecurityModule>();

string[] roleIds = System.Web.Security.Roles.GetRolesForUser();
IList<FuncContext> allFuncList = securityModule.GetFuncList(roleIds, "zh-TW");
```

取出的 `FuncContext` 物件屬性，以程式方式說明：

```csharp
namespace Quanta.PaaS.Web.Security
{
    /// <summary>
    /// Function List 的傳遞參數資料。
    /// </summary>
	public class FuncContext
	{
		/// <summary>
        /// 功能 Id。
		/// </summary>
		public string FunctionId { get; set; }

		/// <summary>
        /// 父群組的群組序號。
		/// </summary>
		public string ParentGroup { get; set; }

		/// <summary>
        /// 功能順序。
		/// </summary>
		public string OrderBy { get; set; }

		/// <summary>
        /// 是否需身份驗證。
		/// </summary>
		public string Authorized { get; set; }

		/// <summary>
        /// 功能的 Link Url。
		/// </summary>
		public string LinkUrl { get; set; }

		/// <summary>
        /// 功能類型。
		/// </summary>
        /// <remarks>G = Group, F = Function。</remarks>
		public string Type { get; set; }

		/// <summary>
        /// 是否清單中顯示。
		/// </summary>
		public string Visible { get; set; }

		/// <summary>
        /// 是否控制 Action。
		/// </summary>
		public string ActionControl { get; set; }

		/// <summary>
        /// 功能名稱。
		/// </summary>
		public string Name { get; set; }

		/// <summary>
        /// 功能描述。
		/// </summary>
		public string Description { get; set; }
    }
}
```

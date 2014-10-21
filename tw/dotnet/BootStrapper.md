
BootStrapper
================

有部份的程式，需要在 Application 啟動階段就執行，如 Business Layer 中 Service 的 IoC 註冊  
目前 NEBULA SDK 將提供一個 BootStrapper 的機制，用來設定需要在系統一啟動就執行的註冊程式，以便這些程式可以共用，以下為空白範例。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using Quanta.PaaS.Module.BootStrappers;
using Microsoft.Practices.Unity;
 
namespace Sample.BL
{
    public class SampleRegister : BusinessLayerBootStrapper
    {
        public override void Execute()
        {
            RegisterDataContext<DataContext>();
            RegisterTypeForInterception<IProductService, ProductService>();
        }
    }
}
```

註冊 Dependency Injection
----------------
在 BusinessLayerBootStrapper 中，除了啟動時要執行的程式碼以外，另外一個重要的作用，就是註冊 Dependency Injection  
在 BusinessLayerBootStrapper 中提供了以下的屬性與方法，開發者可以透過以下方式來註冊所需要的介面與實作類別  
  

啟動 BootStarpper
----------------

啟動 BootStrapper 的方式，有以下兩種：

### web.config 設定
----------------

透過以下的設定， Nebula SDK 將在啟動的時候，載入在此宣告的 BootStrapper 類別

```xml
<cloudPlatform>
    <bootstrappers>
        <add bootstrapper ="Sample.BL.SampleRegister, Sample.BL" />
    </bootstrappers>
</cloudPlatform>
```

### 程式宣告
----------------

```csharp
using Quanta.PaaS.Common.BootStrapper;

namespace Sample.BL
{
    [AutoRun]
    public class SampleRegister : BusinessLayerBootStrapper
    {
        public override void Execute()
        {
            // .. ..
        }
    }
}
```

### 注意事項
----------------

* 類型的註冊請勿重複
* 程式宣告與 web.config 設定請擇一使用

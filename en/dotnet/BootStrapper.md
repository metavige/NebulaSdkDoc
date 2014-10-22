
BootStrapper
================

Some programs of an application must be booted alreay when the application starts, such as the IoC registration of Services in the Business Layer. Quanta Cloud Platform provide a `BootStrapper` technique to configure registered programs booted and can be used when application starts. The following is an example.

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

Developer can code the require-to-boot programs in the `Execute` method.  
[BusinessLayerBootStrapper](Nebula.Module.md#BusinessLayerBootStrapper) method used in the program as well as property, please refer to [Nebula.Module](Nebula.Module.md)

## Activate BootStarpper
----------------

There are two ways to activate BootStrapper:

### web.config settings
----------------

The declared `BootStrapper` class would be loaded when Nebula SDK starts through the following configuration below.  

```xml
<cloudPlatform>
    <bootstrappers>
        <add bootstrapper ="Sample.BL.SampleRegister, Sample.BL" />
    </bootstrappers>
</cloudPlatform>
```

### Code Declaration
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

### aution
----------------

* Type registration cannot be repeated.
* You can only either choose to use code declaration or web.config.

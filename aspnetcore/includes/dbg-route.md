---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 087c5a7dd20bc653a05c337dde905716032e00b3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552442"
---
## <a name="debug-diagnostics"></a>调试诊断

要获取详细的路由诊断输出，请将 `Logging:LogLevel:Microsoft` 设置为 `Debug`。 在开发环境中，在 appsettings.Development.json 中设置日志级别：

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

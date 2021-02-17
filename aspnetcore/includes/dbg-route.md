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
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552365"
---
## <a name="debug-diagnostics"></a><span data-ttu-id="33e97-101">デバッグ診断</span><span class="sxs-lookup"><span data-stu-id="33e97-101">Debug diagnostics</span></span>

<span data-ttu-id="33e97-102">詳細なルーティング診断出力を行うには、`Logging:LogLevel:Microsoft` を `Debug` に設定してください。</span><span class="sxs-lookup"><span data-stu-id="33e97-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="33e97-103">開発環境では、*appsettings.Development.json* でログ レベルを次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="33e97-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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

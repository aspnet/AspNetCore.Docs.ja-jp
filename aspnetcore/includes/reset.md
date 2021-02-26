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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552855"
---
<span data-ttu-id="36211-101">リセットを使用すると、指定されたエラー コードを使用してサーバーに HTTP/2 要求をリセットさせることができます。</span><span class="sxs-lookup"><span data-stu-id="36211-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="36211-102">リセット要求は中止されたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="36211-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="36211-103">上記のコード例の `Reset` により、`INTERNAL_ERROR` エラー コードが指定されています。</span><span class="sxs-lookup"><span data-stu-id="36211-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="36211-104">HTTP/2 エラー コードの詳細については、[HTTP/2 仕様の「エラーコード」セクション](https://tools.ietf.org/html/rfc7540#page-50)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36211-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>
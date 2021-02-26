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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551499"
---
<span data-ttu-id="bd10e-101">HTTP トレーラーは HTTP ヘッダーに似ていますが、応答本文の送信後に送信される点が異なります。</span><span class="sxs-lookup"><span data-stu-id="bd10e-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="bd10e-102">IIS と HTTP.sys の場合は、HTTP/2 応答トレーラーのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="bd10e-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="bd10e-103">上記のコード例では次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="bd10e-103">In the preceding example code:</span></span>

* <span data-ttu-id="bd10e-104">`SupportsTrailers` によって、応答におけるトレーラーが確実にサポートされます。</span><span class="sxs-lookup"><span data-stu-id="bd10e-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="bd10e-105">`DeclareTrailer` によって、指定したトレーラー名が `Trailer` 応答ヘッダーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="bd10e-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="bd10e-106">応答のトレーラーを宣言することは省略可能ですが、推奨されています。</span><span class="sxs-lookup"><span data-stu-id="bd10e-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="bd10e-107">`DeclareTrailer` を呼び出す場合、それは、応答ヘッダーを送信する前に行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd10e-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="bd10e-108">`AppendTrailer` によって、トレーラーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="bd10e-108">`AppendTrailer` appends the trailer.</span></span>

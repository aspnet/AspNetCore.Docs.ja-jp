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
ms.openlocfilehash: 73395ab632f38fef1348b4657770eb52db5778d9
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551974"
---
> [!WARNING]
> <span data-ttu-id="e6c09-101"><xref:System.Text.RegularExpressions> を使用して信頼できない入力を処理するときは、タイムアウトを渡します。</span><span class="sxs-lookup"><span data-stu-id="e6c09-101">When using <xref:System.Text.RegularExpressions> to process untrusted input, pass a timeout.</span></span> <span data-ttu-id="e6c09-102">悪意のあるユーザーが `RegularExpressions` に入力を提供して、[サービス拒否攻撃](https://www.us-cert.gov/ncas/tips/ST04-015)を行う可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e6c09-102">A malicious user can provide input to `RegularExpressions` causing a [Denial-of-Service attack](https://www.us-cert.gov/ncas/tips/ST04-015).</span></span> <span data-ttu-id="e6c09-103">`RegularExpressions` を使用する ASP.NET Core フレームワーク API は、タイムアウトを渡します。</span><span class="sxs-lookup"><span data-stu-id="e6c09-103">ASP.NET Core framework APIs that use `RegularExpressions` pass a timeout.</span></span>
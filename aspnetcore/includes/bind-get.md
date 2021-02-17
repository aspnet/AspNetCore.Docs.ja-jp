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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552458"
---
> [!WARNING]
> <span data-ttu-id="d7fcf-101">セキュリティ上の理由から、ページ モデルのプロパティに対して `GET` 要求データのバインドをオプトインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7fcf-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="d7fcf-102">プロパティにマップする前に、ユーザー入力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="d7fcf-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="d7fcf-103">`GET` バインドをオプトインするのは、クエリ文字列やルート値に依存するシナリオに対処する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="d7fcf-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="d7fcf-104">`GET` 要求のプロパティをバインドするには、[`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性の `SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d7fcf-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="d7fcf-105">詳細については、[ASP.NET Core コミュニティ スタンドアップ: GET のディスカッションでのバインド (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s) に関する動画を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7fcf-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>

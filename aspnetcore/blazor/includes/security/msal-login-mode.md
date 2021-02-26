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
ms.openlocfilehash: 1b045d437d1a16eabc0ab41573c8b66d9c4bb77e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552702"
---
<span data-ttu-id="2f39b-101">フレームワークは、既定ではポップアップ ログイン モードになり、ポップアップを開くことができない場合はリダイレクト ログイン モードに戻ります。</span><span class="sxs-lookup"><span data-stu-id="2f39b-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="2f39b-102"><xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> の `LoginMode` プロパティを `redirect` に設定して、リダイレクト ログイン モードを使用するように MSAL を構成します。</span><span class="sxs-lookup"><span data-stu-id="2f39b-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="2f39b-103">既定の設定は `popup` であり、文字列の値の大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="2f39b-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>

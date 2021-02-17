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
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552617"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a><span data-ttu-id="cc13e-101">既定のアカウントの検証を無効にする</span><span class="sxs-lookup"><span data-stu-id="cc13e-101">Disable default account verification</span></span>

<span data-ttu-id="cc13e-102">既定のテンプレートでは、ユーザーはにリダイレクトされ `Account.RegisterConfirmation` ます。ここで、アカウントを確認するためのリンクを選択できます。</span><span class="sxs-lookup"><span data-stu-id="cc13e-102">With the default templates, the user is redirected to the `Account.RegisterConfirmation` where they can select a link to have the account confirmed.</span></span> <span data-ttu-id="cc13e-103">既定値 `Account.RegisterConfirmation` はテストに ***のみ*** 使用され、運用アプリでは自動アカウント検証を無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc13e-103">The default `Account.RegisterConfirmation` is used ***only*** for testing, automatic account verification should be disabled in a production app.</span></span>

<span data-ttu-id="cc13e-104">確認済みのアカウントを要求し、登録時にすぐにログインできないようにするには、 `DisplayConfirmAccountLink = false` */Areas/ Identity /Pages/Account/RegisterConfirmation.cshtml.cs* でを設定します。</span><span class="sxs-lookup"><span data-stu-id="cc13e-104">To require a confirmed account and prevent immediate login at registration, set `DisplayConfirmAccountLink = false` in */Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*:</span></span>

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]
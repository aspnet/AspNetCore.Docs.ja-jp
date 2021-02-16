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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536301"
---
<span data-ttu-id="ccb54-101">`:` の区切り記号は、すべてのプラットフォームの環境変数階層キーには対応していません。</span><span class="sxs-lookup"><span data-stu-id="ccb54-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="ccb54-102">`__`（ダブルアンダースコア）は、</span><span class="sxs-lookup"><span data-stu-id="ccb54-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="ccb54-103">すべてのプラットフォームに対応しています。</span><span class="sxs-lookup"><span data-stu-id="ccb54-103">Supported by all platforms.</span></span> <span data-ttu-id="ccb54-104">たとえば、[Bash](https://linuxhint.com/bash-environment-variables/) は`:` の区切り記号には対応していませんが、`__` には対応しています。</span><span class="sxs-lookup"><span data-stu-id="ccb54-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="ccb54-105">自動で `:` に置換されます。</span><span class="sxs-lookup"><span data-stu-id="ccb54-105">Automatically replaced by a `:`</span></span>
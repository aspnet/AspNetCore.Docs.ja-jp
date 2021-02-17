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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551627"
---
* `-F|--no-formatting`

  <span data-ttu-id="ead02-101">存在することで HTTP 応答の書式設定を抑制するフラグ。</span><span class="sxs-lookup"><span data-stu-id="ead02-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="ead02-102">HTTP 要求ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="ead02-102">Sets an HTTP request header.</span></span> <span data-ttu-id="ead02-103">次の 2 つの値の形式がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ead02-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="ead02-104">HTTP 応答の本文を書き込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="ead02-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="ead02-105">たとえば、「 `--response:body "C:\response.json"` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ead02-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="ead02-106">ファイルが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="ead02-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="ead02-107">HTTP 応答のヘッダーを書き込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="ead02-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="ead02-108">たとえば、「 `--response:headers "C:\response.txt"` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ead02-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="ead02-109">ファイルが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="ead02-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="ead02-110">存在することで HTTP 応答のストリーミングを有効にするフラグ。</span><span class="sxs-lookup"><span data-stu-id="ead02-110">A flag whose presence enables streaming of the HTTP response.</span></span>

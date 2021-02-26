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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552120"
---
```console
npm run release
```

<span data-ttu-id="12795-101">このコマンドは、アプリの実行中に提供するクライアント側資産を生成します。</span><span class="sxs-lookup"><span data-stu-id="12795-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="12795-102">資産は、*wwwroot* フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="12795-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="12795-103">Webpack は、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="12795-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="12795-104">*wwwroot* ディレクトリの内容を消去しました。</span><span class="sxs-lookup"><span data-stu-id="12795-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="12795-105">"*トランスコンパイル*" と呼ばれるプロセスで TypeScript を JavaScript に変換しました。</span><span class="sxs-lookup"><span data-stu-id="12795-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="12795-106">"*縮小*" と呼ばれるプロセスで、生成後の JavaScript ファイルのサイズを縮小しました。</span><span class="sxs-lookup"><span data-stu-id="12795-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="12795-107">処理済みの JavaScript、CSS、および HTML ファイルを *src* から *wwwroot* ディレクトリにコピーしました。</span><span class="sxs-lookup"><span data-stu-id="12795-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="12795-108">次の要素を *wwwroot/index.html* ファイルに挿入しました。</span><span class="sxs-lookup"><span data-stu-id="12795-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="12795-109">*wwwroot/main.\<hash\>.css* ファイルを参照している `<link>` タグ。</span><span class="sxs-lookup"><span data-stu-id="12795-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="12795-110">このタグは、終了 `</head>` タグの直前に置かれます。</span><span class="sxs-lookup"><span data-stu-id="12795-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="12795-111">縮小された *wwwroot/main.\<hash\>.js* ファイルを参照している `<script>` タグ。</span><span class="sxs-lookup"><span data-stu-id="12795-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="12795-112">このタグは、終了 `</body>` タグの直前に置かれます。</span><span class="sxs-lookup"><span data-stu-id="12795-112">This tag is placed immediately before the closing `</body>` tag.</span></span>
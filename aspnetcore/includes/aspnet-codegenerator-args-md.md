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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551344"
---
<!-- Options common to Razor Pages and Controller -->
| <span data-ttu-id="fa8c5-101">オプション</span><span class="sxs-lookup"><span data-stu-id="fa8c5-101">Option</span></span>               | <span data-ttu-id="fa8c5-102">説明</span><span class="sxs-lookup"><span data-stu-id="fa8c5-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="fa8c5-103">--model または -m</span><span class="sxs-lookup"><span data-stu-id="fa8c5-103">--model or -m</span></span>  | <span data-ttu-id="fa8c5-104">使用するモデル クラス。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-104">Model class to use.</span></span> |
| <span data-ttu-id="fa8c5-105">--dataContext または -dc</span><span class="sxs-lookup"><span data-stu-id="fa8c5-105">--dataContext or -dc</span></span>  | <span data-ttu-id="fa8c5-106">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="fa8c5-107">--bootstrapVersion または -b</span><span class="sxs-lookup"><span data-stu-id="fa8c5-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="fa8c5-108">ブートストラップのバージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="fa8c5-109">有効な値は `3` または `4`です。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="fa8c5-110">既定値は `4` です。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-110">Default is `4`.</span></span> <span data-ttu-id="fa8c5-111">指定されたバージョンのブートストラップ ファイルを含む *wwwroot* ディレクトリが必要で存在しない場合は、作成されます。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="fa8c5-112">--referenceScriptLibraries または -scripts</span><span class="sxs-lookup"><span data-stu-id="fa8c5-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="fa8c5-113">生成されたビューでスクリプト ライブラリを参照します。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="fa8c5-114">[編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="fa8c5-115">--layout または -l</span><span class="sxs-lookup"><span data-stu-id="fa8c5-115">--layout or -l</span></span> | <span data-ttu-id="fa8c5-116">使用するカスタム レイアウト ページ。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="fa8c5-117">--useDefaultLayout または -udl</span><span class="sxs-lookup"><span data-stu-id="fa8c5-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="fa8c5-118">ビューの既定のレイアウトを使用します。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="fa8c5-119">--force または -f</span><span class="sxs-lookup"><span data-stu-id="fa8c5-119">--force or -f</span></span> | <span data-ttu-id="fa8c5-120">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="fa8c5-121">--relativeFolderPath または -outDir</span><span class="sxs-lookup"><span data-stu-id="fa8c5-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="fa8c5-122">ファイルが生成されるプロジェクトの相対出力フォルダー パス。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="fa8c5-123">指定しない場合、ファイルはプロジェクト フォルダーに生成されます。</span><span class="sxs-lookup"><span data-stu-id="fa8c5-123">If not specified, files are generated in the project folder.</span></span> |
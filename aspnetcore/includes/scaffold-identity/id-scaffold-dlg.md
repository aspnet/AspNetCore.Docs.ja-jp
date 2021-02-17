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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552970"
---
<span data-ttu-id="c28ce-101">Scaffolder を実行し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c28ce-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c28ce-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c28ce-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c28ce-103">**ソリューションエクスプローラー** で、プロジェクトを右クリックし、[ **Add**  >  **New スキャフォールディング Item**] > ます。</span><span class="sxs-lookup"><span data-stu-id="c28ce-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c28ce-104">[ **Add New スキャフォールディング Item** ] ダイアログボックスの左ペインで、[追加] を選択し **Identity**  >  ます。</span><span class="sxs-lookup"><span data-stu-id="c28ce-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c28ce-105">[**追加 Identity** ] ダイアログで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="c28ce-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c28ce-106">既存のレイアウトページを選択するか、正しくないマークアップでレイアウトファイルが上書きされます:</span><span class="sxs-lookup"><span data-stu-id="c28ce-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="c28ce-107">`~/Pages/Shared/_Layout.cshtml`ページの場合 Razor</span><span class="sxs-lookup"><span data-stu-id="c28ce-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="c28ce-108">`~/Views/Shared/_Layout.cshtml` MVC プロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="c28ce-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="c28ce-109">Blazor Server テンプレート () から作成されたアプリ Blazor Server `blazorserver` は、 Razor 既定ではページまたは MVC 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="c28ce-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="c28ce-110">レイアウトページのエントリは空白のままにします。</span><span class="sxs-lookup"><span data-stu-id="c28ce-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="c28ce-111">**+** 新しい **データコンテキストクラス** を作成するには、このボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c28ce-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="c28ce-112">既定値をそのまま使用するか、クラス (など) を指定し `MyApplication.Data.ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="c28ce-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="c28ce-113">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c28ce-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c28ce-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c28ce-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c28ce-115">ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="c28ce-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c28ce-116">必要な NuGet パッケージ参照をプロジェクトファイル (*.csproj*) に追加します。</span><span class="sxs-lookup"><span data-stu-id="c28ce-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="c28ce-117">プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c28ce-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="c28ce-118">次のコマンドを実行して、scaffolder オプションの一覧を表示し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c28ce-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="c28ce-119">プロジェクトフォルダーで、 Identity 必要なオプションを指定して scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="c28ce-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c28ce-120">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c28ce-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---

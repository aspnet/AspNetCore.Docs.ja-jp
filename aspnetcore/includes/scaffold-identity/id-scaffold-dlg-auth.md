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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552731"
---
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="62dbc-101">Scaffolder を実行し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62dbc-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62dbc-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="62dbc-103">**ソリューションエクスプローラー** で、プロジェクトを右クリックし、[ **Add** > **New スキャフォールディング Item**] > ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="62dbc-104">[**スキャフォールディングの追加**] ダイアログボックスの左ペインで、[追加] を選択し **Identity** > ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="62dbc-105">[**追加 Identity** ] ダイアログで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="62dbc-106">レイアウトファイルが正しくないマークアップで上書きされないように、既存のレイアウトページを選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="62dbc-107">既存の *\_ Layout* ファイルが選択されている場合、そのファイルは上書きされ **ません**。</span><span class="sxs-lookup"><span data-stu-id="62dbc-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="62dbc-108">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-108">For example:</span></span>
    * <span data-ttu-id="62dbc-109">`~/Pages/Shared/_Layout.cshtml`Razor Blazor Server 既存の Razor ページインフラストラクチャを使用するページまたはプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="62dbc-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="62dbc-110">`~/Views/Shared/_Layout.cshtml` mvc プロジェクトまたは Blazor Server 既存の mvc インフラストラクチャを使用するプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="62dbc-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="62dbc-111">既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="62dbc-112">データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="62dbc-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="62dbc-113">データコンテキストクラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-113">Select your data context class.</span></span>
  * <span data-ttu-id="62dbc-114">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-114">Select **Add**.</span></span>
* <span data-ttu-id="62dbc-115">新しいユーザーコンテキストを作成し、場合によってはのカスタムユーザークラスを作成するには、次のようにし Identity ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="62dbc-116">**+** 新しい **データコンテキストクラス** を作成するには、このボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="62dbc-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="62dbc-117">既定値をそのまま使用するか、クラス (など) を指定し `MyApplication.Data.ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="62dbc-118">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-118">Select **Add**.</span></span>

<span data-ttu-id="62dbc-119">注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="62dbc-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="62dbc-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="62dbc-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="62dbc-121">ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="62dbc-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="62dbc-122">必要な NuGet パッケージ参照をプロジェクトファイル (*.csproj*) に追加します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="62dbc-123">プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="62dbc-124">次のコマンドを実行して、scaffolder オプションの一覧を表示し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="62dbc-125">プロジェクトフォルダーで、 Identity 必要なオプションを指定して scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="62dbc-126">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="62dbc-127">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="62dbc-128">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="62dbc-129">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="62dbc-130">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="62dbc-131">Identityフラグやフラグを指定せずに scaffolder を実行すると `--files` `--useDefaultUI` 、使用可能なすべての Identity UI ページがプロジェクト内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="62dbc-132">Scaffolder を実行し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62dbc-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62dbc-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="62dbc-134">**ソリューションエクスプローラー** で、プロジェクトを右クリックし、[ **Add** > **New スキャフォールディング Item**] > ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="62dbc-135">[**スキャフォールディングの追加**] ダイアログボックスの左ペインで、[追加] を選択し **Identity** > ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="62dbc-136">[**追加 Identity** ] ダイアログで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="62dbc-137">既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="62dbc-138">既存の *\_ Layout* ファイルが選択されている場合、そのファイルは上書きされ **ません**。</span><span class="sxs-lookup"><span data-stu-id="62dbc-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="62dbc-139">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-139">For example:</span></span>
    * <span data-ttu-id="62dbc-140">`~/Pages/Shared/_Layout.cshtml`ページの場合 Razor</span><span class="sxs-lookup"><span data-stu-id="62dbc-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="62dbc-141">`~/Views/Shared/_Layout.cshtml` MVC プロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="62dbc-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="62dbc-142">既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="62dbc-143">データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="62dbc-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="62dbc-144">データコンテキストクラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-144">Select your data context class.</span></span>
  * <span data-ttu-id="62dbc-145">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-145">Select **Add**.</span></span>
* <span data-ttu-id="62dbc-146">新しいユーザーコンテキストを作成し、場合によってはのカスタムユーザークラスを作成するには、次のようにし Identity ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="62dbc-147">**+** 新しい **データコンテキストクラス** を作成するには、このボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="62dbc-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="62dbc-148">既定値をそのまま使用するか、クラス (など) を指定し `MyApplication.Data.ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="62dbc-149">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-149">Select **Add**.</span></span>

<span data-ttu-id="62dbc-150">注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="62dbc-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="62dbc-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="62dbc-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="62dbc-152">ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="62dbc-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="62dbc-153">[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクトファイル (*.csproj*) に追加してください。</span><span class="sxs-lookup"><span data-stu-id="62dbc-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="62dbc-154">プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="62dbc-155">次のコマンドを実行して、scaffolder オプションの一覧を表示し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="62dbc-156">プロジェクトフォルダーで、 Identity 必要なオプションを指定して scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="62dbc-157">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="62dbc-158">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="62dbc-159">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="62dbc-160">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="62dbc-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="62dbc-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="62dbc-162">Identityフラグやフラグを指定せずに scaffolder を実行すると `--files` `--useDefaultUI` 、使用可能なすべての Identity UI ページがプロジェクト内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="62dbc-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

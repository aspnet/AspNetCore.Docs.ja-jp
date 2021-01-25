---
title: ASP.NET Core のクラス ライブラリの再利用可能 Razor UI
author: Rick-Anderson
description: ASP.NET Core のクラス ライブラリで部分ビューを使用して、再利用可能な Razor UI を作成する方法について説明します。
ms.author: riande
ms.date: 01/19/2021
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: a878a3485ecee0782b21ac69c5ec6ff832b9f06c
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571008"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="30cd8-103">ASP.NET Core の Razor クラス ライブラリ プロジェクトを使用した再利用可能 UI の作成</span><span class="sxs-lookup"><span data-stu-id="30cd8-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="30cd8-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="30cd8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="30cd8-105">Razor ビュー、ページ、コントローラー、ページ モデル、[Razor コンポーネント](xref:blazor/components/class-libraries)、[ビュー コンポーネント](xref:mvc/views/view-components)、データ モデルは、Razor クラス ライブラリ (RCL) に組み込むことが可能です。</span><span class="sxs-lookup"><span data-stu-id="30cd8-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="30cd8-106">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="30cd8-107">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="30cd8-108">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="30cd8-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="30cd8-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="30cd8-110">Razor UI を含むクラス ライブラリの作成</span><span class="sxs-lookup"><span data-stu-id="30cd8-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30cd8-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30cd8-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30cd8-112">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="30cd8-113">**[Razor クラス ライブラリ]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="30cd8-114">ライブラリに名前を付け ("RazorClassLib" など)、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="30cd8-115">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="30cd8-116">ビューをサポートする必要がある場合は、 **[ページとビューのサポート]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="30cd8-117">既定では、Razor ページのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="30cd8-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="30cd8-118">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-118">Select **Create**.</span></span>

<span data-ttu-id="30cd8-119">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="30cd8-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="30cd8-120">**[ページとビューのサポート]** オプションによって、ページとビューがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30cd8-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="30cd8-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30cd8-122">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="30cd8-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="30cd8-124">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="30cd8-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="30cd8-125">`--support-pages-and-views` オプションを渡す (`dotnet new razorclasslib --support-pages-and-views`) と、ページとビューがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="30cd8-126">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="30cd8-127">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="30cd8-128">RCL に Razor ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="30cd8-129">ASP.NET Core テンプレートでは、RCL コンテンツが *Areas* フォルダーにあるものとしています。</span><span class="sxs-lookup"><span data-stu-id="30cd8-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="30cd8-130">`~/Areas/Pages` ではなく `~/Pages` でコンテンツを公開する RCL を作成する場合は、「[RCL ページのレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="30cd8-131">RCL コンテンツを参照する</span><span class="sxs-lookup"><span data-stu-id="30cd8-131">Reference RCL content</span></span>

<span data-ttu-id="30cd8-132">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="30cd8-133">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="30cd8-133">NuGet package.</span></span> <span data-ttu-id="30cd8-134">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="30cd8-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="30cd8-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="30cd8-136">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="30cd8-137">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="30cd8-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="30cd8-138">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="30cd8-139">たとえば、*WebApp1/Areas/MyFeature/Pages/Page1.cshtml* を WebApp1 に追加すると、WebApp1 の Page1 は RCL の Page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="30cd8-140">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="30cd8-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="30cd8-141">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* にコピーします。</span><span class="sxs-lookup"><span data-stu-id="30cd8-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="30cd8-142">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="30cd8-143">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="30cd8-144">RCL ページのレイアウト</span><span class="sxs-lookup"><span data-stu-id="30cd8-144">RCL Pages layout</span></span>

<span data-ttu-id="30cd8-145">RCL コンテンツを Web アプリの *Pages* フォルダーの一部であるかのように参照するには、次のファイル構造で RCL プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="30cd8-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="30cd8-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="30cd8-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="30cd8-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="30cd8-148">*RazorUIClassLib/Pages/Shared* に 2 つの部分ファイル、 *_Header.cshtml* と *_Footer.cshtml* が含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="30cd8-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="30cd8-149">これらの `<partial>` タグを *_Layout.cshtml* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

<span data-ttu-id="30cd8-150">*_ViewStart.cshtml* ファイルを、RCL プロジェクトの *Pages* フォルダーに追加して、ホスト Web アプリの *_Layout.cshtml* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-150">Add the *_ViewStart.cshtml* file to the RCL project's *Pages* folder to use the *_Layout.cshtml* file from the host web app:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="30cd8-151">静的アセットを含む RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="30cd8-151">Create an RCL with static assets</span></span>

<span data-ttu-id="30cd8-152">RCL では、RCL または RCL の使用アプリで参照できる静的なコンパニオン アセットが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="30cd8-152">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="30cd8-153">ASP.NET Core では、使用アプリで利用できる静的アセットを含む RCL の作成が可能です。</span><span class="sxs-lookup"><span data-stu-id="30cd8-153">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="30cd8-154">コンパニオン アセットを RCL の一部として含めるには、クラス ライブラリに *wwwroot* フォルダーを作成し、必要なファイルをすべてそのフォルダーに含めます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-154">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="30cd8-155">RCL をパックすると、*wwwroot* フォルダー内のすべてのコンパニオン アセットがパッケージに自動的に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-155">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="30cd8-156">Nuget.exe バージョン `nuget pack` ではなく、`dotnet pack` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-156">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="30cd8-157">静的アセットを除外する</span><span class="sxs-lookup"><span data-stu-id="30cd8-157">Exclude static assets</span></span>

<span data-ttu-id="30cd8-158">静的アセットを除外するには、目的の除外パスをプロジェクトファイル内の `$(DefaultItemExcludes)` プロパティ グループに追加します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-158">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="30cd8-159">各エントリは、セミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="30cd8-159">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="30cd8-160">次の例では、*wwwroot* フォルダー内の *lib.css* スタイルシートは、静的アセットとは見なされず、公開された RCL には組み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="30cd8-160">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="30cd8-161">Typescript の統合</span><span class="sxs-lookup"><span data-stu-id="30cd8-161">Typescript integration</span></span>

<span data-ttu-id="30cd8-162">TypeScript ファイルを RCL に含めるには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="30cd8-162">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="30cd8-163">TypeScript ファイル ( *.ts*) を *wwwroot* フォルダーの外側に配置します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-163">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="30cd8-164">たとえば、ファイルを *Client* フォルダーに入れます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-164">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="30cd8-165">*wwwroot* フォルダーの TypeScript ビルド出力を構成します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-165">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="30cd8-166">プロジェクト ファイルの `PropertyGroup` の内側に `TypescriptOutDir` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-166">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="30cd8-167">プロジェクト ファイルの `PropertyGroup` の内側に次のターゲットを追加して、TypeScript ターゲットを `ResolveCurrentProjectStaticWebAssets` ターゲットの依存関係として含めます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-167">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="30cd8-168">参照されている RCL からコンテンツを使用する</span><span class="sxs-lookup"><span data-stu-id="30cd8-168">Consume content from a referenced RCL</span></span>

<span data-ttu-id="30cd8-169">RCL の *wwwroot* フォルダーに含まれるファイルは、`_content/{LIBRARY NAME}/` プレフィックスに基づいて RCL または使用アプリのいずれかに公開されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-169">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="30cd8-170">たとえば、 *Razor.Class.Lib* という名前のライブラリを使用すると、`_content/Razor.Class.Lib/` にある静的コンテンツへのパスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-170">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="30cd8-171">NuGet パッケージを生成するときに、アセンブリ名がパッケージ ID と異なる場合は、`{LIBRARY NAME}` のパッケージ ID を使用します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-171">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="30cd8-172">使用アプリは、ライブラリによって提供される静的アセットを `<script>`、`<style>`、`<img>`、およびその他の HTML タグ付きで参照します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-172">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="30cd8-173">使用アプリの `Startup.Configure` で [静的ファイルのサポート](xref:fundamentals/static-files)が有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="30cd8-173">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="30cd8-174">使用アプリをビルド出力から実行 (`dotnet run`) すると、開発環境で、静的な Web アセットが既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="30cd8-174">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="30cd8-175">ビルド出力から実行するときに他の環境のアセットをサポートするには、*Program.cs* のホスト ビルダーで `UseStaticWebAssets` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-175">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="30cd8-176">発行された出力からアプリを実行する (`dotnet publish`) 場合、`UseStaticWebAssets` の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="30cd8-176">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="30cd8-177">複数プロジェクトの開発フロー</span><span class="sxs-lookup"><span data-stu-id="30cd8-177">Multi-project development flow</span></span>

<span data-ttu-id="30cd8-178">使用アプリの実行時は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="30cd8-178">When the consuming app runs:</span></span>

* <span data-ttu-id="30cd8-179">RCL のアセットは元のフォルダー内に保持されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-179">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="30cd8-180">これらのアセットは、使用アプリに移行されません。</span><span class="sxs-lookup"><span data-stu-id="30cd8-180">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="30cd8-181">RCL の *wwwroot* フォルダー内の変更は、使用アプリをリビルドしなくても、RCL がリビルドされた後で、使用アプリに反映されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-181">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="30cd8-182">RCL がビルドされると、静的な Web アセットの場所を記述するマニフェストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-182">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="30cd8-183">使用アプリは、実行時にそのマニフェストを読み取って、参照されているプロジェクトおよびパッケージのアセットを使用します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-183">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="30cd8-184">RCL に新しいアセットが追加された場合は、使用アプリがその新しいアセットにアクセスする前に、RCL をリビルドしてそのマニフェストを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="30cd8-184">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="30cd8-185">公開</span><span class="sxs-lookup"><span data-stu-id="30cd8-185">Publish</span></span>

<span data-ttu-id="30cd8-186">アプリが公開されると、参照されているすべてのプロジェクトおよびパッケージのコンパニオン アセットが、`_content/{LIBRARY NAME}/` の下の公開済みアプリの *wwwroot* フォルダーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-186">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="30cd8-187">Razor ビュー、ページ、コントローラー、ページ モデル、[Razor コンポーネント](xref:blazor/components/class-libraries)、[ビュー コンポーネント](xref:mvc/views/view-components)、データ モデルは、Razor クラス ライブラリ (RCL) に組み込むことが可能です。</span><span class="sxs-lookup"><span data-stu-id="30cd8-187">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="30cd8-188">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-188">The RCL can be packaged and reused.</span></span> <span data-ttu-id="30cd8-189">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-189">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="30cd8-190">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-190">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="30cd8-191">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="30cd8-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="30cd8-192">Razor UI を含むクラス ライブラリの作成</span><span class="sxs-lookup"><span data-stu-id="30cd8-192">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30cd8-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30cd8-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30cd8-194">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="30cd8-195">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-195">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="30cd8-196">ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-196">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="30cd8-197">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-197">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="30cd8-198">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-198">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="30cd8-199">**[Razor クラス ライブラリ]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-199">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="30cd8-200">RCL には次のプロジェクト ファイルがあります。</span><span class="sxs-lookup"><span data-stu-id="30cd8-200">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="30cd8-201">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="30cd8-201">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30cd8-202">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-202">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="30cd8-203">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-203">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="30cd8-204">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-204">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="30cd8-205">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-205">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="30cd8-206">RCL に Razor ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-206">Add Razor files to the RCL.</span></span>

<span data-ttu-id="30cd8-207">ASP.NET Core テンプレートでは、RCL コンテンツが *Areas* フォルダーにあるものとしています。</span><span class="sxs-lookup"><span data-stu-id="30cd8-207">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="30cd8-208">`~/Areas/Pages` ではなく `~/Pages` でコンテンツを公開する RCL を作成する場合は、「[RCL ページのレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-208">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="30cd8-209">RCL コンテンツを参照する</span><span class="sxs-lookup"><span data-stu-id="30cd8-209">Reference RCL content</span></span>

<span data-ttu-id="30cd8-210">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-210">The RCL can be referenced by:</span></span>

* <span data-ttu-id="30cd8-211">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="30cd8-211">NuGet package.</span></span> <span data-ttu-id="30cd8-212">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-212">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="30cd8-213">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="30cd8-213">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="30cd8-214">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-214">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="30cd8-215">チュートリアル: RCL プロジェクトを作成し、Razor Pages プロジェクトから使用する</span><span class="sxs-lookup"><span data-stu-id="30cd8-215">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="30cd8-216">作成しなくても、[完全なプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)をダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-216">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="30cd8-217">サンプル ダウンロードには、プロジェクトのテストを簡単にする追加のコードやリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="30cd8-217">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="30cd8-218">GitHub の問題に関するフィードバックは[こちら](https://github.com/dotnet/AspNetCore.Docs/issues/6098)で扱っています。ダウンロード サンプルと段階的指示の違いについてコメントを投稿できます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-218">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="30cd8-219">ダウンロード アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="30cd8-219">Test the download app</span></span>

<span data-ttu-id="30cd8-220">完全なアプリをダウンロードしておらず、チュートリアル プロジェクトを作成する場合、[次のセクション](#create-an-rcl)に進んでください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-220">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30cd8-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30cd8-221">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30cd8-222">Visual Studio で *.sln* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-222">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="30cd8-223">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-223">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30cd8-224">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="30cd8-224">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30cd8-225">*cli* ディレクトリのコマンド プロンプトから、RCL と Web アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="30cd8-225">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="30cd8-226">*WebApp1* ディレクトリに移動し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-226">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="30cd8-227">[テスト WebApp1](#test-webapp1) の指示に従ってください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-227">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="30cd8-228">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="30cd8-228">Create an RCL</span></span>

<span data-ttu-id="30cd8-229">このセクションでは、RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-229">In this section, an RCL is created.</span></span> <span data-ttu-id="30cd8-230">Razor ファイルが RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-230">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30cd8-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30cd8-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30cd8-232">RCL プロジェクトの作成:</span><span class="sxs-lookup"><span data-stu-id="30cd8-232">Create the RCL project:</span></span>

* <span data-ttu-id="30cd8-233">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-233">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="30cd8-234">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-234">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="30cd8-235">アプリに **RazorUIClassLib** という名前を付け、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-235">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="30cd8-236">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-236">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="30cd8-237">**[Razor クラス ライブラリ]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-237">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="30cd8-238">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* という名前が付いた Razor 部分ビュー ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-238">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30cd8-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="30cd8-239">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30cd8-240">コマンド ラインから次を実行します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-240">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="30cd8-241">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-241">The preceding commands:</span></span>

* <span data-ttu-id="30cd8-242">`RazorUIClassLib` RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-242">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="30cd8-243">Razor _Message ページが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-243">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="30cd8-244">`-np` パラメーターによって、`PageModel` なしでページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-244">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="30cd8-245">[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) ファイルが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-245">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="30cd8-246">(次のセクションで追加される) RazorPages プロジェクトのレイアウトを使用するには、 *_ViewStart.cshtml* ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="30cd8-246">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="30cd8-247">Razor ファイルとフォルダーをプロジェクトに追加する</span><span class="sxs-lookup"><span data-stu-id="30cd8-247">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="30cd8-248">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="30cd8-249">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-249">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="30cd8-250">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` は部分ビュー (`<partial name="_Message" />`) を使用するために必要です。</span><span class="sxs-lookup"><span data-stu-id="30cd8-250">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="30cd8-251">`@addTagHelper` ディレクティブを含める代わりに、 *_ViewImports.cshtml* ファイルを追加できます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-251">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="30cd8-252">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-252">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="30cd8-253">*_ViewImports.cshtml* について詳しくは、「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30cd8-253">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="30cd8-254">クラス ライブラリをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-254">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="30cd8-255">ビルド出力には、 *RazorUIClassLib.dll* と *RazorUIClassLib.Views.dll* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="30cd8-255">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="30cd8-256">*RazorUIClassLib.Views.dll* には、コンパイル済みの Razor コンテンツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="30cd8-256">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="30cd8-257">Razor ページ プロジェクトから Razor UI ライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-257">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30cd8-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30cd8-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30cd8-259">Razor ページ Web アプリの作成:</span><span class="sxs-lookup"><span data-stu-id="30cd8-259">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="30cd8-260">**ソリューション エクスプローラー** で、ソリューションを右クリックし、 **[追加]** > **[新しいプロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-260">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="30cd8-261">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-261">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="30cd8-262">アプリに **WebApp1** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-262">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="30cd8-263">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-263">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="30cd8-264">**[Web アプリケーション]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-264">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="30cd8-265">**ソリューション エクスプローラー** で、**WebApp1** を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-265">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="30cd8-266">**ソリューション エクスプローラー** で、**WebApp1** を右クリックし、 **[ビルド依存関係]** > **[プロジェクトの依存関係]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-266">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="30cd8-267">**WebApp1** の依存関係として **RazorUIClassLib** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-267">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="30cd8-268">**ソリューション エクスプローラー** で、**WebApp1** を右クリックし、 **[追加]** > **[参照]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-268">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="30cd8-269">**[参照マネージャー]** ダイアログで、 **[RazorUIClassLib]** をオンにして **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-269">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="30cd8-270">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-270">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30cd8-271">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="30cd8-271">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30cd8-272">Razor Pages Web アプリと、Razor Pages アプリと RCL を含むソリューション ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-272">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="30cd8-273">Web アプリをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-273">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="30cd8-274">テスト WebApp1</span><span class="sxs-lookup"><span data-stu-id="30cd8-274">Test WebApp1</span></span>

<span data-ttu-id="30cd8-275">`/MyFeature/Page1` を参照して、Razor UI クラス ライブラリが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-275">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="30cd8-276">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="30cd8-276">Override views, partial views, and pages</span></span>

<span data-ttu-id="30cd8-277">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-277">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="30cd8-278">たとえば、*WebApp1/Areas/MyFeature/Pages/Page1.cshtml* を WebApp1 に追加すると、WebApp1 の Page1 は RCL の Page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-278">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="30cd8-279">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="30cd8-279">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="30cd8-280">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* にコピーします。</span><span class="sxs-lookup"><span data-stu-id="30cd8-280">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="30cd8-281">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-281">Update the markup to indicate the new location.</span></span> <span data-ttu-id="30cd8-282">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-282">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="30cd8-283">RCL ページのレイアウト</span><span class="sxs-lookup"><span data-stu-id="30cd8-283">RCL Pages layout</span></span>

<span data-ttu-id="30cd8-284">RCL コンテンツを Web アプリの *Pages* フォルダーの一部であるかのように参照するには、次のファイル構造で RCL プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="30cd8-284">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="30cd8-285">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="30cd8-285">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="30cd8-286">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="30cd8-286">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="30cd8-287">*RazorUIClassLib/Pages/Shared* に 2 つの部分ファイル、 *_Header.cshtml* と *_Footer.cshtml* が含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="30cd8-287">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="30cd8-288">これらの `<partial>` タグを *_Layout.cshtml* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="30cd8-288">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="30cd8-289">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="30cd8-289">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end

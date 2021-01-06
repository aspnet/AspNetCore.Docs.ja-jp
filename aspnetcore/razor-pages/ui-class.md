---
title: ASP.NET Core のクラス ライブラリの再利用可能 Razor UI
author: Rick-Anderson
description: ASP.NET Core のクラス ライブラリで部分ビューを使用して、再利用可能な Razor UI を作成する方法について説明します。
ms.author: riande
ms.date: 01/25/2020
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
ms.openlocfilehash: 0bfdb1932d829ec00c9de1bd38b7920cb1f40c51
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "94570173"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="f2bf4-103">ASP.NET Core の Razor クラス ライブラリ プロジェクトを使用した再利用可能 UI の作成</span><span class="sxs-lookup"><span data-stu-id="f2bf4-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="f2bf4-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f2bf4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2bf4-105">Razor ビュー、ページ、コントローラー、ページ モデル、[Razor コンポーネント](xref:blazor/components/class-libraries)、[ビュー コンポーネント](xref:mvc/views/view-components)、データ モデルは、Razor クラス ライブラリ (RCL) に組み込むことが可能です。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="f2bf4-106">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="f2bf4-107">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="f2bf4-108">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="f2bf4-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="f2bf4-110">Razor UI を含むクラス ライブラリの作成</span><span class="sxs-lookup"><span data-stu-id="f2bf4-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2bf4-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2bf4-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2bf4-112">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="f2bf4-113">**[Razor クラス ライブラリ]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="f2bf4-114">ライブラリに名前を付け ("RazorClassLib" など)、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="f2bf4-115">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="f2bf4-116">ビューをサポートする必要がある場合は、 **[ページとビューのサポート]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="f2bf4-117">既定では、Razor ページのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="f2bf4-118">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-118">Select **Create**.</span></span>

<span data-ttu-id="f2bf4-119">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="f2bf4-120">**[ページとビューのサポート]** オプションによって、ページとビューがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2bf4-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f2bf4-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2bf4-122">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="f2bf4-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="f2bf4-124">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="f2bf4-125">`--support-pages-and-views` オプションを渡す (`dotnet new razorclasslib --support-pages-and-views`) と、ページとビューがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="f2bf4-126">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="f2bf4-127">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="f2bf4-128">RCL に Razor ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="f2bf4-129">ASP.NET Core テンプレートでは、RCL コンテンツが *Areas* フォルダーにあるものとしています。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="f2bf4-130">`~/Areas/Pages` ではなく `~/Pages` でコンテンツを公開する RCL を作成する場合は、「[RCL ページのレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="f2bf4-131">RCL コンテンツを参照する</span><span class="sxs-lookup"><span data-stu-id="f2bf4-131">Reference RCL content</span></span>

<span data-ttu-id="f2bf4-132">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="f2bf4-133">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-133">NuGet package.</span></span> <span data-ttu-id="f2bf4-134">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="f2bf4-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f2bf4-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="f2bf4-136">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="f2bf4-137">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="f2bf4-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="f2bf4-138">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="f2bf4-139">たとえば、*WebApp1/Areas/MyFeature/Pages/Page1.cshtml* を WebApp1 に追加すると、WebApp1 の Page1 は RCL の Page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="f2bf4-140">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="f2bf4-141">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* にコピーします。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="f2bf4-142">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="f2bf4-143">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="f2bf4-144">RCL ページのレイアウト</span><span class="sxs-lookup"><span data-stu-id="f2bf4-144">RCL Pages layout</span></span>

<span data-ttu-id="f2bf4-145">RCL コンテンツを Web アプリの *Pages* フォルダーの一部であるかのように参照するには、次のファイル構造で RCL プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="f2bf4-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="f2bf4-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="f2bf4-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="f2bf4-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="f2bf4-148">*RazorUIClassLib/Pages/Shared* に 2 つの部分ファイル、 *_Header.cshtml* と *_Footer.cshtml* が含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="f2bf4-149">これらの `<partial>` タグを *_Layout.cshtml* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="f2bf4-150">静的アセットを含む RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="f2bf4-150">Create an RCL with static assets</span></span>

<span data-ttu-id="f2bf4-151">RCL では、RCL または RCL の使用アプリで参照できる静的なコンパニオン アセットが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="f2bf4-152">ASP.NET Core では、使用アプリで利用できる静的アセットを含む RCL の作成が可能です。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="f2bf4-153">コンパニオン アセットを RCL の一部として含めるには、クラス ライブラリに *wwwroot* フォルダーを作成し、必要なファイルをすべてそのフォルダーに含めます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="f2bf4-154">RCL をパックすると、*wwwroot* フォルダー内のすべてのコンパニオン アセットがパッケージに自動的に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="f2bf4-155">Nuget.exe バージョン `nuget pack` ではなく、`dotnet pack` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="f2bf4-156">静的アセットを除外する</span><span class="sxs-lookup"><span data-stu-id="f2bf4-156">Exclude static assets</span></span>

<span data-ttu-id="f2bf4-157">静的アセットを除外するには、目的の除外パスをプロジェクトファイル内の `$(DefaultItemExcludes)` プロパティ グループに追加します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="f2bf4-158">各エントリは、セミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="f2bf4-159">次の例では、*wwwroot* フォルダー内の *lib.css* スタイルシートは、静的アセットとは見なされず、公開された RCL には組み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="f2bf4-160">Typescript の統合</span><span class="sxs-lookup"><span data-stu-id="f2bf4-160">Typescript integration</span></span>

<span data-ttu-id="f2bf4-161">TypeScript ファイルを RCL に含めるには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="f2bf4-162">TypeScript ファイル ( *.ts*) を *wwwroot* フォルダーの外側に配置します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-162">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="f2bf4-163">たとえば、ファイルを *Client* フォルダーに入れます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="f2bf4-164">*wwwroot* フォルダーの TypeScript ビルド出力を構成します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="f2bf4-165">プロジェクト ファイルの `PropertyGroup` の内側に `TypescriptOutDir` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="f2bf4-166">プロジェクト ファイルの `PropertyGroup` の内側に次のターゲットを追加して、TypeScript ターゲットを `ResolveCurrentProjectStaticWebAssets` ターゲットの依存関係として含めます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="f2bf4-167">参照されている RCL からコンテンツを使用する</span><span class="sxs-lookup"><span data-stu-id="f2bf4-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="f2bf4-168">RCL の *wwwroot* フォルダーに含まれるファイルは、`_content/{LIBRARY NAME}/` プレフィックスに基づいて RCL または使用アプリのいずれかに公開されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="f2bf4-169">たとえば、 *Razor.Class.Lib* という名前のライブラリを使用すると、`_content/Razor.Class.Lib/` にある静的コンテンツへのパスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="f2bf4-170">NuGet パッケージを生成するときに、アセンブリ名がパッケージ ID と異なる場合は、`{LIBRARY NAME}` のパッケージ ID を使用します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="f2bf4-171">使用アプリは、ライブラリによって提供される静的アセットを `<script>`、`<style>`、`<img>`、およびその他の HTML タグ付きで参照します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="f2bf4-172">使用アプリの `Startup.Configure` で [静的ファイルのサポート](xref:fundamentals/static-files)が有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="f2bf4-173">使用アプリをビルド出力から実行 (`dotnet run`) すると、開発環境で、静的な Web アセットが既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="f2bf4-174">ビルド出力から実行するときに他の環境のアセットをサポートするには、*Program.cs* のホスト ビルダーで `UseStaticWebAssets` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="f2bf4-175">発行された出力からアプリを実行する (`dotnet publish`) 場合、`UseStaticWebAssets` の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="f2bf4-176">複数プロジェクトの開発フロー</span><span class="sxs-lookup"><span data-stu-id="f2bf4-176">Multi-project development flow</span></span>

<span data-ttu-id="f2bf4-177">使用アプリの実行時は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-177">When the consuming app runs:</span></span>

* <span data-ttu-id="f2bf4-178">RCL のアセットは元のフォルダー内に保持されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="f2bf4-179">これらのアセットは、使用アプリに移行されません。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="f2bf4-180">RCL の *wwwroot* フォルダー内の変更は、使用アプリをリビルドしなくても、RCL がリビルドされた後で、使用アプリに反映されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="f2bf4-181">RCL がビルドされると、静的な Web アセットの場所を記述するマニフェストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="f2bf4-182">使用アプリは、実行時にそのマニフェストを読み取って、参照されているプロジェクトおよびパッケージのアセットを使用します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="f2bf4-183">RCL に新しいアセットが追加された場合は、使用アプリがその新しいアセットにアクセスする前に、RCL をリビルドしてそのマニフェストを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="f2bf4-184">公開</span><span class="sxs-lookup"><span data-stu-id="f2bf4-184">Publish</span></span>

<span data-ttu-id="f2bf4-185">アプリが公開されると、参照されているすべてのプロジェクトおよびパッケージのコンパニオン アセットが、`_content/{LIBRARY NAME}/` の下の公開済みアプリの *wwwroot* フォルダーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2bf4-186">Razor ビュー、ページ、コントローラー、ページ モデル、[Razor コンポーネント](xref:blazor/components/class-libraries)、[ビュー コンポーネント](xref:mvc/views/view-components)、データ モデルは、Razor クラス ライブラリ (RCL) に組み込むことが可能です。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-186">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="f2bf4-187">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="f2bf4-188">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="f2bf4-189">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="f2bf4-190">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="f2bf4-191">Razor UI を含むクラス ライブラリの作成</span><span class="sxs-lookup"><span data-stu-id="f2bf4-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2bf4-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2bf4-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2bf4-193">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f2bf4-194">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f2bf4-195">ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-195">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="f2bf4-196">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="f2bf4-197">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="f2bf4-198">**[Razor クラス ライブラリ]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-198">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="f2bf4-199">RCL には次のプロジェクト ファイルがあります。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f2bf4-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f2bf4-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2bf4-201">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="f2bf4-202">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="f2bf4-203">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="f2bf4-204">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="f2bf4-205">RCL に Razor ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="f2bf4-206">ASP.NET Core テンプレートでは、RCL コンテンツが *Areas* フォルダーにあるものとしています。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="f2bf4-207">`~/Areas/Pages` ではなく `~/Pages` でコンテンツを公開する RCL を作成する場合は、「[RCL ページのレイアウト](#rcl-pages-layout)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="f2bf4-208">RCL コンテンツを参照する</span><span class="sxs-lookup"><span data-stu-id="f2bf4-208">Reference RCL content</span></span>

<span data-ttu-id="f2bf4-209">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="f2bf4-210">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-210">NuGet package.</span></span> <span data-ttu-id="f2bf4-211">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="f2bf4-212">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f2bf4-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="f2bf4-213">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="f2bf4-214">チュートリアル: RCL プロジェクトを作成し、Razor Pages プロジェクトから使用する</span><span class="sxs-lookup"><span data-stu-id="f2bf4-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="f2bf4-215">作成しなくても、[完全なプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)をダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="f2bf4-216">サンプル ダウンロードには、プロジェクトのテストを簡単にする追加のコードやリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="f2bf4-217">GitHub の問題に関するフィードバックは[こちら](https://github.com/dotnet/AspNetCore.Docs/issues/6098)で扱っています。ダウンロード サンプルと段階的指示の違いについてコメントを投稿できます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="f2bf4-218">ダウンロード アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="f2bf4-218">Test the download app</span></span>

<span data-ttu-id="f2bf4-219">完全なアプリをダウンロードしておらず、チュートリアル プロジェクトを作成する場合、[次のセクション](#create-an-rcl)に進んでください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2bf4-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2bf4-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2bf4-221">Visual Studio で *.sln* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="f2bf4-222">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2bf4-223">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f2bf4-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2bf4-224">*cli* ディレクトリのコマンド プロンプトから、RCL と Web アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="f2bf4-225">*WebApp1* ディレクトリに移動し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="f2bf4-226">[テスト WebApp1](#test-webapp1) の指示に従ってください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="f2bf4-227">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="f2bf4-227">Create an RCL</span></span>

<span data-ttu-id="f2bf4-228">このセクションでは、RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-228">In this section, an RCL is created.</span></span> <span data-ttu-id="f2bf4-229">Razor ファイルが RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-229">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2bf4-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2bf4-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2bf4-231">RCL プロジェクトの作成:</span><span class="sxs-lookup"><span data-stu-id="f2bf4-231">Create the RCL project:</span></span>

* <span data-ttu-id="f2bf4-232">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f2bf4-233">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f2bf4-234">アプリに **RazorUIClassLib** という名前を付け、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-234">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="f2bf4-235">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="f2bf4-236">**[Razor クラス ライブラリ]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-236">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="f2bf4-237">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* という名前が付いた Razor 部分ビュー ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2bf4-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f2bf4-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2bf4-239">コマンド ラインから次を実行します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="f2bf4-240">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-240">The preceding commands:</span></span>

* <span data-ttu-id="f2bf4-241">`RazorUIClassLib` RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="f2bf4-242">Razor _Message ページが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="f2bf4-243">`-np` パラメーターによって、`PageModel` なしでページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="f2bf4-244">[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) ファイルが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="f2bf4-245">(次のセクションで追加される) RazorPages プロジェクトのレイアウトを使用するには、 *_ViewStart.cshtml* ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="f2bf4-246">Razor ファイルとフォルダーをプロジェクトに追加する</span><span class="sxs-lookup"><span data-stu-id="f2bf4-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="f2bf4-247">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="f2bf4-248">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="f2bf4-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` は部分ビュー (`<partial name="_Message" />`) を使用するために必要です。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="f2bf4-250">`@addTagHelper` ディレクティブを含める代わりに、 *_ViewImports.cshtml* ファイルを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f2bf4-251">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="f2bf4-252">*_ViewImports.cshtml* について詳しくは、「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-252">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="f2bf4-253">クラス ライブラリをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="f2bf4-254">ビルド出力には、 *RazorUIClassLib.dll* と *RazorUIClassLib.Views.dll* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="f2bf4-255">*RazorUIClassLib.Views.dll* には、コンパイル済みの Razor コンテンツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="f2bf4-256">Razor ページ プロジェクトから Razor UI ライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2bf4-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2bf4-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2bf4-258">Razor ページ Web アプリの作成:</span><span class="sxs-lookup"><span data-stu-id="f2bf4-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="f2bf4-259">**ソリューション エクスプローラー** で、ソリューションを右クリックし、 **[追加]** > **[新しいプロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-259">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="f2bf4-260">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f2bf4-261">アプリに **WebApp1** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="f2bf4-262">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="f2bf4-263">**[Web アプリケーション]** > **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="f2bf4-264">**ソリューション エクスプローラー** で、**WebApp1** を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-264">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="f2bf4-265">**ソリューション エクスプローラー** で、**WebApp1** を右クリックし、 **[ビルド依存関係]** > **[プロジェクトの依存関係]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-265">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="f2bf4-266">**WebApp1** の依存関係として **RazorUIClassLib** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-266">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="f2bf4-267">**ソリューション エクスプローラー** で、**WebApp1** を右クリックし、 **[追加]** > **[参照]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-267">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="f2bf4-268">**[参照マネージャー]** ダイアログで、 **[RazorUIClassLib]** をオンにして **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="f2bf4-269">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2bf4-270">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f2bf4-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2bf4-271">Razor Pages Web アプリと、Razor Pages アプリと RCL を含むソリューション ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="f2bf4-272">Web アプリをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="f2bf4-273">テスト WebApp1</span><span class="sxs-lookup"><span data-stu-id="f2bf4-273">Test WebApp1</span></span>

<span data-ttu-id="f2bf4-274">`/MyFeature/Page1` を参照して、Razor UI クラス ライブラリが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="f2bf4-275">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="f2bf4-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="f2bf4-276">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="f2bf4-277">たとえば、*WebApp1/Areas/MyFeature/Pages/Page1.cshtml* を WebApp1 に追加すると、WebApp1 の Page1 は RCL の Page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="f2bf4-278">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="f2bf4-279">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* にコピーします。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="f2bf4-280">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="f2bf4-281">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="f2bf4-282">RCL ページのレイアウト</span><span class="sxs-lookup"><span data-stu-id="f2bf4-282">RCL Pages layout</span></span>

<span data-ttu-id="f2bf4-283">RCL コンテンツを Web アプリの *Pages* フォルダーの一部であるかのように参照するには、次のファイル構造で RCL プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="f2bf4-284">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="f2bf4-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="f2bf4-285">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="f2bf4-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="f2bf4-286">*RazorUIClassLib/Pages/Shared* に 2 つの部分ファイル、 *_Header.cshtml* と *_Footer.cshtml* が含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="f2bf4-287">これらの `<partial>` タグを *_Layout.cshtml* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="f2bf4-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f2bf4-288">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f2bf4-288">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end

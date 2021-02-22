---
title: Swashbuckle と ASP.NET Core の概要
author: zuckerthoben
description: Swashbuckle を ASP.NET Core Web API プロジェクトに追加し、Swagger UI を統合する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
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
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: df9c78770f3494b1ae9e0d3604926d77cb1e67bb
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "99578322"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="6a955-103">Swashbuckle と ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="6a955-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="6a955-104">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6a955-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6a955-105">Swashbuckle には 3 つの主要なコンポーネントがあります。</span><span class="sxs-lookup"><span data-stu-id="6a955-105">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="6a955-106">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): `SwaggerDocument` オブジェクトを JSON エンドポイントとして公開するための Swagger オブジェクト モデルとミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="6a955-106">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="6a955-107">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): ルート、コントローラー、モデルから直接 `SwaggerDocument` オブジェクトを構築する Swagger ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="6a955-107">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="6a955-108">通常、Swagger エンドポイント ミドルウェアと組み合わせて Swagger JSON を自動的に公開します。</span><span class="sxs-lookup"><span data-stu-id="6a955-108">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="6a955-109">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger UI ツールの埋め込みバージョン。</span><span class="sxs-lookup"><span data-stu-id="6a955-109">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="6a955-110">Swagger JSON を解釈して、Web API の機能を説明するカスタマイズ可能な機能豊富なエクスペリエンスを構築します。</span><span class="sxs-lookup"><span data-stu-id="6a955-110">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="6a955-111">これには、パブリック メソッド用の組み込みのテスト ハーネスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6a955-111">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="6a955-112">パッケージ インストール</span><span class="sxs-lookup"><span data-stu-id="6a955-112">Package installation</span></span>

<span data-ttu-id="6a955-113">Swashbuckle は、次の方法で追加できます。</span><span class="sxs-lookup"><span data-stu-id="6a955-113">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="6a955-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a955-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a955-115">**[パッケージ マネージャー コンソール]** ウィンドウから:</span><span class="sxs-lookup"><span data-stu-id="6a955-115">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="6a955-116">**[ビュー]**  >  **[Other Windows]** \(その他の Windows\) >  **[パッケージ マネージャー コンソール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="6a955-116">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="6a955-117">*TodoApi.csproj* ファイルが存在するディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="6a955-117">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="6a955-118">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6a955-118">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.6.3
    ```

* <span data-ttu-id="6a955-119">**[NuGet パッケージの管理]** ダイアログ ボックスから:</span><span class="sxs-lookup"><span data-stu-id="6a955-119">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="6a955-120">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="6a955-120">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="6a955-121">**パッケージ ソース** を "nuget.org" に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a955-121">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="6a955-122">[プレリリースを含める] オプションが有効になっていることを確認します</span><span class="sxs-lookup"><span data-stu-id="6a955-122">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="6a955-123">検索ボックスに「Swashbuckle.AspNetCore」と入力します。</span><span class="sxs-lookup"><span data-stu-id="6a955-123">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="6a955-124">**[参照]** タブから最新の "Swashbuckle.AspNetCore"パッケージを選択して、 **[インストール]** をクリックします</span><span class="sxs-lookup"><span data-stu-id="6a955-124">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a955-125">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6a955-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6a955-126">**[Solution Pad]**  >  **[パッケージを追加]** で [*パッケージ*] フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="6a955-126">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="6a955-127">**[パッケージを追加]** ウィンドウの **[ソース]** ドロップダウンを "nuget.org" に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a955-127">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="6a955-128">[プレリリースパッケージを表示する] オプションが有効になっていることを確認します</span><span class="sxs-lookup"><span data-stu-id="6a955-128">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="6a955-129">検索ボックスに「Swashbuckle.AspNetCore」と入力します。</span><span class="sxs-lookup"><span data-stu-id="6a955-129">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="6a955-130">結果ウィンドウから最新の Swashbuckle.AspNetCore パッケージを選択し、 **[パッケージを追加]** をクリックします</span><span class="sxs-lookup"><span data-stu-id="6a955-130">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="6a955-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a955-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6a955-132">**統合ターミナル** からから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6a955-132">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

### <a name="net-core-cli"></a>[<span data-ttu-id="6a955-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6a955-133">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6a955-134">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6a955-134">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="6a955-135">Swagger ミドルウェアを追加して構成する</span><span class="sxs-lookup"><span data-stu-id="6a955-135">Add and configure Swagger middleware</span></span>

<span data-ttu-id="6a955-136">`Startup.ConfigureServices` メソッドで Swagger ジェネレーターをサービス コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-136">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

<span data-ttu-id="6a955-137">`Startup.Configure` メソッドで、生成された JSON ドキュメントと Swagger UI 対応のミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="6a955-137">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="6a955-138">Swashbuckle では、MVC の <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> に依存してルートとエンドポイントが検出されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-138">Swashbuckle relies on MVC's <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> to discover the routes and endpoints.</span></span> <span data-ttu-id="6a955-139">プロジェクトが <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> を呼び出すと、ルートとエンドポイントが自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-139">If the project calls <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, routes and endpoints are discovered automatically.</span></span> <span data-ttu-id="6a955-140"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> を呼び出すときは、<xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> メソッドを明示的に呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a955-140">When calling <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, the <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> method must be explicitly called.</span></span> <span data-ttu-id="6a955-141">詳細については、[Swashbuckle、ApiExplorer、およびルーティング](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a955-141">For more information, see [Swashbuckle, ApiExplorer, and Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span></span>

<span data-ttu-id="6a955-142">前述の `UseSwaggerUI` メソッド呼び出しにより、[静的ファイル ミドルウェア](xref:fundamentals/static-files)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="6a955-142">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="6a955-143">.NET Framework または .NET Core 1.x を対象とする場合は、[Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-143">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="6a955-144">アプリを起動し、`http://localhost:<port>/swagger/v1/swagger.json` に移動します。</span><span class="sxs-lookup"><span data-stu-id="6a955-144">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="6a955-145">[OpenAPI 仕様 (openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson) に基づき、エンドポイントについて説明するドキュメントが生成され、表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-145">The generated document describing the endpoints appears as shown in [OpenAPI specification (openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).</span></span>

<span data-ttu-id="6a955-146">Swagger UI は `http://localhost:<port>/swagger` にあります。</span><span class="sxs-lookup"><span data-stu-id="6a955-146">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="6a955-147">Swagger UI から API を探し、他のプログラムに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="6a955-147">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="6a955-148">アプリのルート (`http://localhost:<port>/`) で Swagger UI にサービスを提供するには、`RoutePrefix` プロパティを空の文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a955-148">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="6a955-149">IIS やリバース プロキシを使用するディレクトリを使用している場合は、`./` プレフィックスを使用して Swagger のエンドポイントを相対パスに設定します。</span><span class="sxs-lookup"><span data-stu-id="6a955-149">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="6a955-150">たとえば、`./swagger/v1/swagger.json` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6a955-150">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="6a955-151">`/swagger/v1/swagger.json` を使用することで、アプリが URL の真のルート (使用されている場合は、これに加えてルート プレフィックス) にある JSON ファイルを検索するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-151">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="6a955-152">たとえば、`http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` の代わりに `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6a955-152">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

> [!NOTE]
> <span data-ttu-id="6a955-153">既定では、Swashbuckle は、仕様 (正式には OpenAPI 仕様と呼ばれます) のバージョン 3.0 の Swagger JSON を生成して公開します。</span><span class="sxs-lookup"><span data-stu-id="6a955-153">By default, Swashbuckle generates and exposes Swagger JSON in version 3.0 of the specification&mdash;officially called the OpenAPI Specification.</span></span> <span data-ttu-id="6a955-154">下位互換性をサポートするために、代わりに 2.0 形式で JSON を公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="6a955-154">To support backwards compatibility, you can opt into exposing JSON in the 2.0 format instead.</span></span> <span data-ttu-id="6a955-155">この 2.0 形式は、現在 OpenAPI バージョン 2.0 をサポートしている Microsoft Power Apps や Microsoft Flow などの統合において重要です。</span><span class="sxs-lookup"><span data-stu-id="6a955-155">This 2.0 format is important for integrations such as Microsoft Power Apps and Microsoft Flow that currently support OpenAPI version 2.0.</span></span> <span data-ttu-id="6a955-156">2\.0 形式を選択するには、`Startup.Configure` で `SerializeAsV2` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="6a955-156">To opt into the 2.0 format, set the `SerializeAsV2` property in `Startup.Configure`:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a><span data-ttu-id="6a955-157">カスタマイズと拡張</span><span class="sxs-lookup"><span data-stu-id="6a955-157">Customize and extend</span></span>

<span data-ttu-id="6a955-158">Swagger は、テーマに合わせてオブジェクト モデルを文書化して、UI をカスタマイズするオプションを提供します。</span><span class="sxs-lookup"><span data-stu-id="6a955-158">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="6a955-159">`Startup` クラスに、次の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-159">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="6a955-160">API 情報と説明</span><span class="sxs-lookup"><span data-stu-id="6a955-160">API info and description</span></span>

<span data-ttu-id="6a955-161">`AddSwaggerGen` メソッドに渡された構成アクションによって、作成者、ライセンス、説明などの情報が追加されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-161">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

<span data-ttu-id="6a955-162">`Startup` クラスで、次の名前空間をインポートし、`OpenApiInfo` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="6a955-162">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="6a955-163">`OpenApiInfo` クラスを使用して、UI に表示される情報を変更します。</span><span class="sxs-lookup"><span data-stu-id="6a955-163">Using the `OpenApiInfo` class, modify the information displayed in the UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="6a955-164">Swagger UI には、バージョンの情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-164">The Swagger UI displays the version's information:</span></span>

![バージョン情報を含む Swagger UI: 説明、作成者、およびその他のリンクの表示](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="6a955-166">XML コメント</span><span class="sxs-lookup"><span data-stu-id="6a955-166">XML comments</span></span>

<span data-ttu-id="6a955-167">XML コメントは、次の方法で有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6a955-167">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="6a955-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a955-168">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="6a955-169">**ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[<project_name>.csproj の編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a955-169">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="6a955-170">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-170">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="6a955-171">**ソリューション エクスプローラー** でプロジェクトを右クリックして、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a955-171">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="6a955-172">**[ビルド]** タブの **[出力]** セクションの下にある **[XML ドキュメント ファイル]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6a955-172">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a955-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6a955-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="6a955-174">*Solution Pad* から **Ctrl** キーを押しながらプロジェクト名をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6a955-174">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="6a955-175">**[ツール]**  >  **[ファイルの編集]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="6a955-175">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="6a955-176">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-176">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="6a955-177">**[プロジェクト オプション]** ダイアログ > **[ビルド]** > **[コンパイラ]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="6a955-177">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="6a955-178">**[全般オプション]** セクションの下にある **[XML ドキュメントを生成する]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6a955-178">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="6a955-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a955-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6a955-180">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-180">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="6a955-181">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6a955-181">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6a955-182">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-182">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="6a955-183">XML コメントを有効にすると、ドキュメントに未記載のパブリック型とメンバーのデバッグ情報を提供することができます。</span><span class="sxs-lookup"><span data-stu-id="6a955-183">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="6a955-184">文書化されない型とメンバーは警告メッセージで示されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-184">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="6a955-185">たとえば、次のメッセージは警告コード 1591 の違反を示します。</span><span class="sxs-lookup"><span data-stu-id="6a955-185">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="6a955-186">プロジェクト全体で警告を非表示にするには、プロジェクト ファイルで無視する警告コードの一覧をセミコロン区切りで定義します。</span><span class="sxs-lookup"><span data-stu-id="6a955-186">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="6a955-187">警告コードを `$(NoWarn);` に追加すると、[C# の既定値](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)も適用されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-187">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="6a955-188">特定のメンバーに向けた警告のみを非表示にするには、コードを [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) プリプロセッサ ディレクティブで囲みます。</span><span class="sxs-lookup"><span data-stu-id="6a955-188">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="6a955-189">この方法は、API ドキュメント経由で公開すべきではないコードの場合に役立ちます。次の例では、警告コード CS1591 が `Program` クラス全体で無視されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-189">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="6a955-190">警告コードの強制は、クラス定義の終了時に復元されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-190">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="6a955-191">コンマ区切りのリストで複数の警告コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="6a955-191">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="6a955-192">上記の手順で生成される XML ファイルを使用するように、Swagger を構成します。</span><span class="sxs-lookup"><span data-stu-id="6a955-192">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="6a955-193">Linux または Windows 以外のオペレーティング システムでは、ファイル名やパスで大文字小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-193">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="6a955-194">たとえば、*TodoApi.XML* ファイルは Windows では有効ですが、CentOS では無効です。</span><span class="sxs-lookup"><span data-stu-id="6a955-194">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="6a955-195">前述のコードでは、[Reflection](/dotnet/csharp/programming-guide/concepts/reflection) を使用し、Web API プロジェクトの名前に一致する XML ファイル名が構築されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-195">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="6a955-196">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) プロパティは、XML ファイルのパス構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-196">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="6a955-197">Swagger の一部の機能 (たとえば、入力パラメーターや HTTP メソッドのスキーマ、それぞれの属性からの応答コード) は、XML ドキュメント ファイルを使わなくても動作します。</span><span class="sxs-lookup"><span data-stu-id="6a955-197">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="6a955-198">メソッドのサマリーや、パラメーターと応答コードの記述など、ほとんどの機能には、XML ファイルの使用が必須です。</span><span class="sxs-lookup"><span data-stu-id="6a955-198">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="6a955-199">アクションにトリプル スラッシュのコメントを追加すると、セクション ヘッダーに説明が追加され、Swagger UI が向上します。</span><span class="sxs-lookup"><span data-stu-id="6a955-199">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="6a955-200">`Delete` アクションの上に [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-200">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="6a955-201">Swagger UI には、前述のコードの `<summary>` 要素の内部テキストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-201">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![DELETE メソッドの XML コメント 'Deletes a specific TodoItem.' を](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="6a955-204">UI は、生成された JSON スキーマによって決まります。</span><span class="sxs-lookup"><span data-stu-id="6a955-204">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="6a955-205">[\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 要素を `Create` アクション メソッドのドキュメントに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-205">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="6a955-206">これは、`<summary>` 要素で指定された情報を補足し、Swagger UI をより堅牢にします。</span><span class="sxs-lookup"><span data-stu-id="6a955-206">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="6a955-207">`<remarks>` 要素の内容は、テキスト、JSON、または XML で構成されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-207">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="6a955-208">追加コメントで UI が向上している点にご注目ください。</span><span class="sxs-lookup"><span data-stu-id="6a955-208">Notice the UI enhancements with these additional comments:</span></span>

![追加のコメントが表示された Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="6a955-210">データの注釈</span><span class="sxs-lookup"><span data-stu-id="6a955-210">Data annotations</span></span>

<span data-ttu-id="6a955-211">[System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 名前空間にある属性でモデルをマークし、Swagger UI コンポーネントの向上に役立てます。</span><span class="sxs-lookup"><span data-stu-id="6a955-211">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="6a955-212">`[Required]` 属性を `TodoItem` クラスの `Name` プロパティに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-212">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="6a955-213">この属性の有無は、UI 動作を変更し、基になる JSON スキーマを変更します。</span><span class="sxs-lookup"><span data-stu-id="6a955-213">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="6a955-214">API コントローラーに `[Produces("application/json")]` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-214">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="6a955-215">その目的は、コントローラーのアクションが *application/json* の応答コンテンツ タイプをサポートすることを宣言することです。</span><span class="sxs-lookup"><span data-stu-id="6a955-215">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="6a955-216">**[応答コンテンツ タイプ]** ドロップダウンがコント ローラーの GET 操作の既定値としてこのコンテンツ タイプを選択します。</span><span class="sxs-lookup"><span data-stu-id="6a955-216">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![既定の応答のコンテンツ タイプを持つ Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="6a955-218">Web API のデータの注釈の使用量が多いほど、UI と API のヘルプ ページはわかりやすく便利になります。</span><span class="sxs-lookup"><span data-stu-id="6a955-218">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="6a955-219">応答の種類の説明</span><span class="sxs-lookup"><span data-stu-id="6a955-219">Describe response types</span></span>

<span data-ttu-id="6a955-220">Web API を使用する開発者は、何が返されるかを最も考慮します&mdash;具体的には、応答の種類とエラー コードです (標準以外の場合)。</span><span class="sxs-lookup"><span data-stu-id="6a955-220">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="6a955-221">応答の種類とエラー コードは、XML コメントとデータ注釈に示されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-221">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="6a955-222">`Create` アクションでは、成功すると HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-222">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="6a955-223">HTTP 400 状態コードは、投稿された要求本文が null のときに返されます。</span><span class="sxs-lookup"><span data-stu-id="6a955-223">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="6a955-224">Swagger UI に適切なドキュメントがないと、利用者にはこれらの予期される結果の知識がありません。</span><span class="sxs-lookup"><span data-stu-id="6a955-224">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="6a955-225">その問題を解決するために、次の例では強調表示された行を追加しています。</span><span class="sxs-lookup"><span data-stu-id="6a955-225">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="6a955-226">Swagger UI は、予期される HTTP 応答コードを明確に記述するようになりました。</span><span class="sxs-lookup"><span data-stu-id="6a955-226">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![ステータスコードの POST 応答クラスの説明 'Returns the newly created Todo item' and '400 - If the item is null' および応答メッセージの下に理由を示す Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6a955-228">ASP.NET Core 2.2 以降では、明示的に個別のアクションを `[ProducesResponseType]` で装飾する代わりに、規約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6a955-228">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="6a955-229">詳細については、「<xref:web-api/advanced/conventions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a955-229">For more information, see <xref:web-api/advanced/conventions>.</span></span>

<span data-ttu-id="6a955-230">`[ProducesResponseType]` の装飾をサポートするために、[Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) パッケージには、応答、スキーマ、およびパラメーターのメタデータを有効にし、強化する拡張機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="6a955-230">To support the `[ProducesResponseType]` decoration, the [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) package offers extensions to enable and enrich the response, schema, and parameter metadata.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="6a955-231">UI をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="6a955-231">Customize the UI</span></span>

<span data-ttu-id="6a955-232">既定の UI は機能も見栄えも優れています。</span><span class="sxs-lookup"><span data-stu-id="6a955-232">The default UI is both functional and presentable.</span></span> <span data-ttu-id="6a955-233">しかしながら、API ドキュメント ページでブランドやテーマを表す必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a955-233">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="6a955-234">Swashbuckle コンポーネントをブランド化するには、静的ファイルにサービスを提供するリソースを追加し、それらのファイルをホストするフォルダー構造を構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a955-234">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="6a955-235">.NET Framework または .NET Core 1.x を対象としている場合、プロジェクトに [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a955-235">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="6a955-236">.NET Core 2.x を対象とし、[メタパッケージ](xref:fundamentals/metapackage)を使用している場合、前述の NuGet パッケージが既にインストールされています。</span><span class="sxs-lookup"><span data-stu-id="6a955-236">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="6a955-237">静的ファイル ミドルウェアの有効化:</span><span class="sxs-lookup"><span data-stu-id="6a955-237">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="6a955-238">追加の CSS スタイルシートを挿入するには、プロジェクトの *wwwroot* フォルダーに追加し、ミドルウェア オプションで相対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="6a955-238">To inject additional CSS stylesheets, add them to the project's *wwwroot* folder and specify the relative path in the middleware options:</span></span>

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```

## <a name="additional-resources"></a><span data-ttu-id="6a955-239">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="6a955-239">Additional resources</span></span>

* [<span data-ttu-id="6a955-240">Microsoft Learn: Swagger ドキュメントで API の開発者エクスペリエンスを向上させる</span><span class="sxs-lookup"><span data-stu-id="6a955-240">Microsoft Learn: Improve the developer experience of an API with Swagger documentation</span></span>](/learn/modules/improve-api-developer-experience-with-swagger/)

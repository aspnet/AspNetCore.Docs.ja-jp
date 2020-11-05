---
title: NSwag と ASP.NET Core の概要
author: zuckerthoben
description: NSwag を使用し、ASP.NET Core Web API のドキュメント ページとヘルプ ページを生成する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 78d58d4d544c33862cf502ce63e83560e8009c65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060574"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="46900-103">NSwag と ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="46900-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="46900-104">作成者: [Christoph Nienaber](https://twitter.com/zuckerthoben)、[Rico Suter](https://rsuter.com)、[Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="46900-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="46900-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="46900-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="46900-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="46900-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="46900-107">NSwag には次の機能があります。</span><span class="sxs-lookup"><span data-stu-id="46900-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="46900-108">Swagger UI と Swagger Generator を利用できます。</span><span class="sxs-lookup"><span data-stu-id="46900-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="46900-109">柔軟なコード生成が可能です。</span><span class="sxs-lookup"><span data-stu-id="46900-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="46900-110">NSwag では、既存の API は必要ありません&mdash;Swagger が組み込まれているサードパーティ製の API を利用し、クライアント実装を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="46900-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="46900-111">NSwag を使用すれば、開発サイクルを短縮でき、API の変更にも容易に対応できます。</span><span class="sxs-lookup"><span data-stu-id="46900-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="46900-112">NSwag ミドルウェアの登録</span><span class="sxs-lookup"><span data-stu-id="46900-112">Register the NSwag middleware</span></span>

<span data-ttu-id="46900-113">NSwag ミドルウェアの登録でできること:</span><span class="sxs-lookup"><span data-stu-id="46900-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="46900-114">実装済みの Web API に対して Swagger 仕様を生成します。</span><span class="sxs-lookup"><span data-stu-id="46900-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="46900-115">Web API を参照し、テストするサービスを Swagger UI に提供します。</span><span class="sxs-lookup"><span data-stu-id="46900-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="46900-116">[NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core ミドルウェアを使用するには、[NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="46900-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="46900-117">このパッケージには、Swagger 仕様、Swagger UI (v2 と v3)、[ReDoc UI](https://github.com/Rebilly/ReDoc) を生成し、それらにサービスを提供するミドルウェアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="46900-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="46900-118">次のいずれかの方法で NSwag NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="46900-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46900-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46900-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="46900-120">**[パッケージ マネージャー コンソール]** ウィンドウから:</span><span class="sxs-lookup"><span data-stu-id="46900-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="46900-121">**[ビュー]**  >  **[Other Windows]** \(その他の Windows\) >  **[パッケージ マネージャー コンソール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="46900-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="46900-122">*TodoApi.csproj* ファイルが存在するディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="46900-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="46900-123">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="46900-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="46900-124">**[NuGet パッケージの管理]** ダイアログ ボックスから:</span><span class="sxs-lookup"><span data-stu-id="46900-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="46900-125">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="46900-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="46900-126">**パッケージ ソース** を "nuget.org" に設定します。</span><span class="sxs-lookup"><span data-stu-id="46900-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="46900-127">検索ボックスに「NSwag.AspNetCore」と入力します。</span><span class="sxs-lookup"><span data-stu-id="46900-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="46900-128">**[参照]** タブから "NSwag.AspNetCore" パッケージを選択して、 **[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46900-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46900-129">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="46900-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="46900-130">**[Solution Pad]**  >  **[パッケージを追加]** で [ *パッケージ* ] フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="46900-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="46900-131">**[パッケージを追加]** ウィンドウの **[ソース]** ドロップダウンを "nuget.org" に設定します。</span><span class="sxs-lookup"><span data-stu-id="46900-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="46900-132">検索ボックスに「NSwag.AspNetCore」と入力します。</span><span class="sxs-lookup"><span data-stu-id="46900-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="46900-133">結果ウィンドウから NSwag.AspNetCore パッケージを選択し、 **[パッケージを追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46900-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="46900-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="46900-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="46900-135">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="46900-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="46900-136">Swagger ミドルウェアを追加して構成する</span><span class="sxs-lookup"><span data-stu-id="46900-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="46900-137">次の手順を実行することで、ASP.NET Core アプリに Swagger を追加して構成します。</span><span class="sxs-lookup"><span data-stu-id="46900-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="46900-138">`Startup.ConfigureServices` メソッドで、必須の Swagger サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="46900-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="46900-139">`Startup.Configure` メソッドで、生成された Swagger 仕様と SwaggerUI 対応のミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="46900-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="46900-140">アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="46900-140">Launch the app.</span></span> <span data-ttu-id="46900-141">次に移動します。</span><span class="sxs-lookup"><span data-stu-id="46900-141">Navigate to:</span></span>
  * <span data-ttu-id="46900-142">`http://localhost:<port>/swagger` (Swagger UI が表示されます)。</span><span class="sxs-lookup"><span data-stu-id="46900-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="46900-143">`http://localhost:<port>/swagger/v1/swagger.json` (Swagger 仕様が表示されます)。</span><span class="sxs-lookup"><span data-stu-id="46900-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="46900-144">コード生成</span><span class="sxs-lookup"><span data-stu-id="46900-144">Code generation</span></span>

<span data-ttu-id="46900-145">次のオプションのいずれかを選択することで、NSwag のコード生成機能を活用できます。</span><span class="sxs-lookup"><span data-stu-id="46900-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="46900-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio):API のクライアント コードを C# または TypeScript で生成するための Windows デスクトップ アプリ。</span><span class="sxs-lookup"><span data-stu-id="46900-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): A Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="46900-147">[NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) または [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet パッケージ。これは、プロジェクト内でコードを生成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="46900-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="46900-148">[コマンド ライン](https://github.com/RicoSuter/NSwag/wiki/CommandLine)からの NSwag。</span><span class="sxs-lookup"><span data-stu-id="46900-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="46900-149">[NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="46900-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="46900-150">[Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice):C# または TypeScript で API クライアント コードを生成するための Visual Studio の接続済みサービス。</span><span class="sxs-lookup"><span data-stu-id="46900-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): A Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="46900-151">NSwag を使用した OpenAPI サービス用の C# コントローラーも生成されます。</span><span class="sxs-lookup"><span data-stu-id="46900-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="46900-152">NSwagStudio を使用したコード生成</span><span class="sxs-lookup"><span data-stu-id="46900-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="46900-153">[NSwagStudio GitHub リポジトリ](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio)の手順に従って、NSwagStudio をインストールします。</span><span class="sxs-lookup"><span data-stu-id="46900-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span> <span data-ttu-id="46900-154">NSwag リリース ページで、インストールや管理者特権なしで起動できる xcopy バージョンをダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="46900-154">On the NSwag release page you can download an xcopy version which can be started without installation and admin privileges.</span></span>
* <span data-ttu-id="46900-155">NSwagStudio を起動し、 **[Swagger Specification URL]\(Swagger 仕様 URL\)** テキスト ボックスに *swagger.json* ファイルの URL を入力します。</span><span class="sxs-lookup"><span data-stu-id="46900-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="46900-156">たとえば、 *http://localhost:44354/swagger/v1/swagger.json* です。</span><span class="sxs-lookup"><span data-stu-id="46900-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="46900-157">**[Create local Copy]\(ローカル コピーの作成\)** をクリックして、Swagger 仕様の JSON 表現を生成します。</span><span class="sxs-lookup"><span data-stu-id="46900-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Swagger 仕様のローカル コピーの作成](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="46900-159">**[Outputs]\(出力\)** 領域で、 **[CSharp Client]\(CSharp クライアント\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="46900-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="46900-160">ご自身のプロジェクトに応じて、 **[TypeScript Client]\(TypeScript クライアント\)** または **[CSharp Web API Controller]\(CSharp Web API コントローラー\)** を選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="46900-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="46900-161">**[CSharp Web API Controller]\(CSharp Web API コントローラー\)** を選択した場合は、逆方向の生成が行われ、サービスの仕様からサービスが再構築されます。</span><span class="sxs-lookup"><span data-stu-id="46900-161">If you select **CSharp Web API Controller** , a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="46900-162">**[Generate Outputs]\(出力の生成\)** をクリックすると、 *TodoApi.NSwag* プロジェクトの完全な C# クライアントの実装が作成されます。</span><span class="sxs-lookup"><span data-stu-id="46900-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="46900-163">生成されたクライアント コードを表示するには、 **[CSharp Client]\(CSharp クライアント\)** タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="46900-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="46900-164">**[Settings]\(設定\)** タブでの選択に基づいて、C# クライアント コードが生成されます。この設定を変更して、既定の名前空間の名前変更や同期メソッドの生成などのタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="46900-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="46900-165">生成された C# コードを、API を消費するクライアント プロジェクト内のファイルにコピーします。</span><span class="sxs-lookup"><span data-stu-id="46900-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="46900-166">Web API の使用を開始します。</span><span class="sxs-lookup"><span data-stu-id="46900-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="46900-167">API ドキュメントのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="46900-167">Customize API documentation</span></span>

<span data-ttu-id="46900-168">Swagger には、Web API の消費を容易にする、オブジェクト モデルをドキュメント化するオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="46900-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="46900-169">API 情報と説明</span><span class="sxs-lookup"><span data-stu-id="46900-169">API info and description</span></span>

<span data-ttu-id="46900-170">`Startup.ConfigureServices`メソッドでは、`AddSwaggerDocument` メソッドに渡された構成アクションによって、作成者、ライセンス、説明などの情報が追加されます。</span><span class="sxs-lookup"><span data-stu-id="46900-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="46900-171">Swagger UI には、バージョンの情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="46900-171">The Swagger UI displays the version's information:</span></span>

![バージョン情報を含む Swagger UI](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="46900-173">XML コメント</span><span class="sxs-lookup"><span data-stu-id="46900-173">XML comments</span></span>

<span data-ttu-id="46900-174">XML コメントを有効にするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="46900-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46900-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46900-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="46900-176">**ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[<project_name>.csproj の編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="46900-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="46900-177">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="46900-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="46900-178">**ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="46900-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="46900-179">**[ビルド]** タブの **[出力]** セクションの下にある **[XML ドキュメント ファイル]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="46900-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46900-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="46900-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="46900-181">*Solution Pad* から **Ctrl** キーを押しながらプロジェクト名をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46900-181">From the *Solution Pad* , press **control** and click the project name.</span></span> <span data-ttu-id="46900-182">**[ツール]**  >  **[ファイルの編集]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="46900-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="46900-183">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="46900-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="46900-184">**[プロジェクト オプション]** ダイアログ > **[ビルド]** > **[コンパイラ]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="46900-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="46900-185">**[全般オプション]** セクションの下にある **[XML ドキュメントを生成する]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="46900-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-cli"></a>[<span data-ttu-id="46900-186">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="46900-186">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="46900-187">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="46900-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="46900-188">データの注釈</span><span class="sxs-lookup"><span data-stu-id="46900-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="46900-189">NSwag では [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) が使用されます。また、Web API アクションに推奨される戻り値の型は [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult) です。そのため、アクションの内容や戻り値の型を推測することはできません。</span><span class="sxs-lookup"><span data-stu-id="46900-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="46900-190">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="46900-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="46900-191">先行するアクションによって `IActionResult` が返されますが、このアクションの内部では [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) または [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) を返しています。</span><span class="sxs-lookup"><span data-stu-id="46900-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="46900-192">データ注釈を使用して、このアクションによって返される既知の HTTP 状態コードをクライアントに通知します。</span><span class="sxs-lookup"><span data-stu-id="46900-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="46900-193">次の属性でアクションをマークします。</span><span class="sxs-lookup"><span data-stu-id="46900-193">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="46900-194">NSwag では [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) が使用されます。また、Web API アクションに推奨される戻り値の型は [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601) です。そのため、`T` によって定義される戻り値の型のみを推測できます。</span><span class="sxs-lookup"><span data-stu-id="46900-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="46900-195">考えられる他の戻り値の型を自動的に推測することはできません。</span><span class="sxs-lookup"><span data-stu-id="46900-195">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="46900-196">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="46900-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="46900-197">前のアクションでは、`ActionResult<T>` を返します。</span><span class="sxs-lookup"><span data-stu-id="46900-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="46900-198">アクションの内部では、[CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) を返します。</span><span class="sxs-lookup"><span data-stu-id="46900-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="46900-199">このコントローラーには [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性が指定されているため、[BadRequest](xref:System.Web.Http.ApiController.BadRequest*) 応答が発生する場合もあります。</span><span class="sxs-lookup"><span data-stu-id="46900-199">Since the controller has the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="46900-200">詳細については、「[自動的な HTTP 400 応答](xref:web-api/index#automatic-http-400-responses)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="46900-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="46900-201">データ注釈を使用して、このアクションによって返される既知の HTTP 状態コードをクライアントに通知します。</span><span class="sxs-lookup"><span data-stu-id="46900-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="46900-202">次の属性でアクションをマークします。</span><span class="sxs-lookup"><span data-stu-id="46900-202">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="46900-203">ASP.NET Core 2.2 以降では、明示的に個別のアクションを `[ProducesResponseType]` で修飾する代わりに、規約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="46900-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="46900-204">詳細については、「<xref:web-api/advanced/conventions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="46900-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="46900-205">Swagger ジェネレーターでは、このアクションを正確に表現できるようになりました。生成されたクライアントでは、エンドポイントの呼び出し時に受け取るものが認識されます。</span><span class="sxs-lookup"><span data-stu-id="46900-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="46900-206">すべてのアクションをこれらの属性でマークすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="46900-206">As a recommendation, mark all actions with these attributes.</span></span>

<span data-ttu-id="46900-207">API アクションで返される HTTP 応答のガイドラインについては、[RFC 7231 仕様](https://tools.ietf.org/html/rfc7231#section-4.3)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="46900-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>

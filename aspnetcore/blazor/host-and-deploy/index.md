---
title: ASP.NET Core Blazor のホストと展開
author: guardrex
description: Blazor アプリをホストおよび展開する方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: a23bee120611ee603305a88dabac76566481fa4a
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485889"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a><span data-ttu-id="22b07-103">ASP.NET Core Blazor のホストと展開</span><span class="sxs-lookup"><span data-stu-id="22b07-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="22b07-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="22b07-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="22b07-105">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="22b07-105">Publish the app</span></span>

<span data-ttu-id="22b07-106">アプリは、リリース構成での展開のために発行されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22b07-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22b07-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="22b07-108">**[ビルド]**  >  **[Publish {APPLICATION}]\({アプリケーション} を発行する\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="22b07-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="22b07-109">*[publish target]\(発行先\)* を選択します。</span><span class="sxs-lookup"><span data-stu-id="22b07-109">Select the *publish target*.</span></span> <span data-ttu-id="22b07-110">ローカルに発行するには、 **[フォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="22b07-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="22b07-111">**[フォルダーの選択]** フィールド内で既定の場所を受け入れるか、または別の場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="22b07-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="22b07-112">**[`Publish`]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="22b07-112">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="22b07-113">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="22b07-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="22b07-114">**[ビルド]**  >  **[Publish to Folder]\(フォルダーに発行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="22b07-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="22b07-115">発行されたアセットを受信するフォルダーを確認し、 **[`Publish`]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="22b07-115">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="22b07-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="22b07-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="22b07-117">[`dotnet publish`](/dotnet/core/tools/dotnet-publish) コマンドを使用して、リリース構成でアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="22b07-117">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="22b07-118">アプリを発行すると、プロジェクトの依存関係の[復元](/dotnet/core/tools/dotnet-restore)がトリガーされ、展開されるアセットを作成する前にプロジェクトが[ビルド](/dotnet/core/tools/dotnet-build)されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="22b07-119">ビルド プロセスの一環として、アプリのダウンロード サイズを縮小し読み込み時間を短縮するため、未使用のメソッドおよびアセンブリが削除されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="22b07-120">発行場所:</span><span class="sxs-lookup"><span data-stu-id="22b07-120">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="22b07-121">スタンドアロン: アプリは `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-121">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="22b07-122">アプリを静的サイトとして展開するには、`wwwroot` フォルダーの内容を静的サイトのホストにコピーします。</span><span class="sxs-lookup"><span data-stu-id="22b07-122">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="22b07-123">ホストされている: クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-123">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="22b07-124">`publish` フォルダーの内容をホストに展開します。</span><span class="sxs-lookup"><span data-stu-id="22b07-124">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="22b07-125">Blazor Server:アプリは `/bin/Release/{TARGET FRAMEWORK}/publish` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-125">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="22b07-126">`publish` フォルダーの内容をホストに展開します。</span><span class="sxs-lookup"><span data-stu-id="22b07-126">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="22b07-127">フォルダー内のアセットは、Web サーバーに展開されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-127">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="22b07-128">展開のプロセスが手動であるか自動であるかは、ご使用の展開ツールによって異なります。</span><span class="sxs-lookup"><span data-stu-id="22b07-128">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="22b07-129">アプリのベース パス</span><span class="sxs-lookup"><span data-stu-id="22b07-129">App base path</span></span>

<span data-ttu-id="22b07-130">*アプリのベース パス* とは、アプリのルート URL パスのことです。</span><span class="sxs-lookup"><span data-stu-id="22b07-130">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="22b07-131">次の ASP.NET Core アプリと Blazor サブアプリについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="22b07-131">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="22b07-132">ASP.NET Core アプリは `MyApp` と命名します。</span><span class="sxs-lookup"><span data-stu-id="22b07-132">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="22b07-133">このアプリは、物理的に `d:/MyApp` に存在します。</span><span class="sxs-lookup"><span data-stu-id="22b07-133">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="22b07-134">要求は、`https://www.contoso.com/{MYAPP RESOURCE}` で受信されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-134">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="22b07-135">`CoolApp` という名前の Blazor アプリは `MyApp` のサブアプリです。</span><span class="sxs-lookup"><span data-stu-id="22b07-135">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="22b07-136">このサブアプリは、物理的に `d:/MyApp/CoolApp` に存在します。</span><span class="sxs-lookup"><span data-stu-id="22b07-136">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="22b07-137">要求は、`https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` で受信されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-137">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="22b07-138">`CoolApp` に対して構成を追加指定しない場合、このシナリオではサブ アプリにはそれがサーバー上のどこの場所にあるかわかりません。</span><span class="sxs-lookup"><span data-stu-id="22b07-138">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="22b07-139">たとえば、相対 URL パス `/CoolApp/` にあることがわからない場合、アプリはそのリソースに対する正しい相対 URL を作成できません。</span><span class="sxs-lookup"><span data-stu-id="22b07-139">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="22b07-140">`<base>` タグの `href` 属性は、Blazor アプリのベース パス `https://www.contoso.com/CoolApp/` に構成を指定するため、`wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) の相対ルート パスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-140">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="22b07-141">Blazor WebAssembly (`wwwroot/index.html`):</span><span class="sxs-lookup"><span data-stu-id="22b07-141">Blazor WebAssembly (`wwwroot/index.html`):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="22b07-142">Blazor Server (`Pages/_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="22b07-142">Blazor Server (`Pages/_Host.cshtml`):</span></span>

```html
<base href="~/CoolApp/">
```

<span data-ttu-id="22b07-143">Blazor Server アプリはさらに、`Startup.Configure` のアプリの要求パイプラインで <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> を呼び出すことによって、サーバー側のベース パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="22b07-143">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="22b07-144">相対 URL を指定することにより、ルート ディレクトリに存在しないコンポーネントでアプリのルート パスへの相対 URL を構築できます。</span><span class="sxs-lookup"><span data-stu-id="22b07-144">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="22b07-145">ディレクトリ構造の別のレベルに存在するコンポーネントが、アプリ内のさまざまな場所にある他のリソースに対するリンクを構築できます。</span><span class="sxs-lookup"><span data-stu-id="22b07-145">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="22b07-146">アプリのベース パスは、リンクの `href` ターゲットがアプリのベース パス URI 空間内にある、選択されたハイパーリンクの阻止にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-146">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="22b07-147">内部のナビゲーションは、Blazor ルーターによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="22b07-147">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="22b07-148">多くのホスティング シナリオでは、アプリへの相対 URL パスは、アプリのルートです。</span><span class="sxs-lookup"><span data-stu-id="22b07-148">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="22b07-149">これらの場合、アプリの相対 URL ベース パスにフォワード スラッシュ (`<base href="/" />`) が付きます。これは、Blazor アプリの既定の構成です。</span><span class="sxs-lookup"><span data-stu-id="22b07-149">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="22b07-150">GitHub ページと IIS サブアプリなど、その他のホスティング シナリオの場合、アプリのベースパスは、アプリへのサーバーの相対 URL パスに設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="22b07-150">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="22b07-151">アプリのベース パスを設定するには、`Pages/_Host.cshtml` ファイル (Blazor Server) または `wwwroot/index.html` ファイル (Blazor WebAssembly) の `<head>` タグ要素内の `<base>` タグを更新します。</span><span class="sxs-lookup"><span data-stu-id="22b07-151">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="22b07-152">`href` 属性値を `/{RELATIVE URL PATH}/` (Blazor WebAssembly) または `~/{RELATIVE URL PATH}/` (Blazor Server) に設定します。</span><span class="sxs-lookup"><span data-stu-id="22b07-152">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (Blazor WebAssembly) or `~/{RELATIVE URL PATH}/` (Blazor Server).</span></span> <span data-ttu-id="22b07-153">**末尾にスラッシュが必要です。**</span><span class="sxs-lookup"><span data-stu-id="22b07-153">**The trailing slash is required.**</span></span> <span data-ttu-id="22b07-154">プレースホルダー `{RELATIVE URL PATH}` は、アプリの完全な相対 URL パスです。</span><span class="sxs-lookup"><span data-stu-id="22b07-154">The placeholder `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="22b07-155">ルート以外の相対 URL パスが構成されている Blazor WebAssembly アプリの場合 (例: `<base href="/CoolApp/">`)、そのアプリは "*ローカルで実行されると*" 自身のリソースを見つけることができません。</span><span class="sxs-lookup"><span data-stu-id="22b07-155">For a Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="22b07-156">ローカルでの開発およびテスト中は、実行時の `<base>` タグの `href` 値と一致する *パス ベース* 引数を指定することで、この問題を克服することができます。</span><span class="sxs-lookup"><span data-stu-id="22b07-156">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="22b07-157">**末尾にはスラッシュを含めないでください。**</span><span class="sxs-lookup"><span data-stu-id="22b07-157">**Don't include a trailing slash.**</span></span> <span data-ttu-id="22b07-158">アプリをローカルで実行しているときにパス ベースの引数を渡すには、アプリのディレクトリから `--pathbase` オプションを指定して `dotnet run` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="22b07-158">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="22b07-159">相対 URL パスが `/CoolApp/` (`<base href="/CoolApp/">`) の Blazor WebAssembly アプリについては、このコマンドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="22b07-159">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="22b07-160">Blazor WebAssembly アプリは、`http://localhost:port/CoolApp` でローカルに応答します。</span><span class="sxs-lookup"><span data-stu-id="22b07-160">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="22b07-161">**Blazor Server `MapFallbackToPage` の構成**</span><span class="sxs-lookup"><span data-stu-id="22b07-161">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="22b07-162">`Startup.Configure` で <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> に次のパスを渡します。</span><span class="sxs-lookup"><span data-stu-id="22b07-162">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="22b07-163">`{RELATIVE PATH}` プレースホルダーは、サーバー上の非ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="22b07-163">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="22b07-164">たとえば、アプリの非ルート URL が `https://{HOST}:{PORT}/CoolApp/`) の場合、`CoolApp` がプレースホルダー セグメントです。</span><span class="sxs-lookup"><span data-stu-id="22b07-164">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="22b07-165">**複数の Blazor WebAssembly アプリをホストする**</span><span class="sxs-lookup"><span data-stu-id="22b07-165">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="22b07-166">ホストされた Blazor ソリューションで複数の Blazor WebAssembly アプリをホストする方法の詳細については、「<xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="22b07-166">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="22b07-167">配置</span><span class="sxs-lookup"><span data-stu-id="22b07-167">Deployment</span></span>

<span data-ttu-id="22b07-168">展開のガイダンスについては、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22b07-168">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>

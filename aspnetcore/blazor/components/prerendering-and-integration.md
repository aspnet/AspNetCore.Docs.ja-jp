---
title: ASP.NET Core Razor コンポーネントのプリレンダリングと統合を行う
author: guardrex
description: サーバー上の Razor コンポーネントのプリレンダリングを含む Blazor アプリの Razor コンポーネントの統合シナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506982"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="f3acb-103">ASP.NET Core Razor コンポーネントのプリレンダリングと統合を行う</span><span class="sxs-lookup"><span data-stu-id="f3acb-103">Prerender and integrate ASP.NET Core Razor components</span></span>

<span data-ttu-id="f3acb-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f3acb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f3acb-105">ホストされる Blazor WebAssembly ソリューションの Razor Pages と MVC アプリに Razor コンポーネントを統合できます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-105">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="f3acb-106">ページまたはビューがレンダリングされるときには、コンポーネントを同時に事前レンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="f3acb-107">構成</span><span class="sxs-lookup"><span data-stu-id="f3acb-107">Configuration</span></span>

<span data-ttu-id="f3acb-108">Blazor WebAssembly アプリのプリレンダリングを設定するには:</span><span class="sxs-lookup"><span data-stu-id="f3acb-108">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="f3acb-109">ASP.NET Core アプリで Blazor WebAssembly をホストします。</span><span class="sxs-lookup"><span data-stu-id="f3acb-109">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="f3acb-110">スタンドアロンの Blazor WebAssembly アプリを ASP.NET Core ソリューションに追加することも、Blazor ホスト プロジェクト テンプレートから作成されたホスト型の Blazor WebAssembly アプリを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-110">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the Blazor Hosted project template.</span></span>

1. <span data-ttu-id="f3acb-111">既定の静的な `wwwroot/index.html` ファイルを Blazor WebAssembly クライアント プロジェクトから削除します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-111">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="f3acb-112">クライアント プロジェクトで `Program.Main` 内の次の行を削除します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="f3acb-113">サーバー プロジェクトに `Pages/_Host.cshtml` ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="f3acb-114">コマンド シェルの `dotnet new blazorserver -o BlazorServer` コマンドを使用して、Blazor Server テンプレートから作成されたアプリから `_Host.cshtml` ファイルを取得できます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-114">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="f3acb-115">ホストされる Blazor WebAssembly ソリューションのサーバー アプリに `Pages/_Host.cshtml` ファイルを配置した後、ファイルに次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="f3acb-116">サーバー アプリの `Pages` フォルダーに名前空間を設定します (例: `@namespace BlazorHosted.Server.Pages`)。</span><span class="sxs-lookup"><span data-stu-id="f3acb-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="f3acb-117">クライアント プロジェクト用の [`@using`](xref:mvc/views/razor#using) ディレクティブを設定します (例: `@using BlazorHosted.Client`)。</span><span class="sxs-lookup"><span data-stu-id="f3acb-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="f3acb-118">スタイルシートのリンクを、WebAssembly アプリのスタイルシートをポイントするように更新します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="f3acb-119">次の例では、クライアント アプリの名前空間は `BlazorHosted.Client`、になります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-119">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="f3acb-120">[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)の `render-mode` を、ルートの `App` コンポーネントをプリレンダリングするように更新します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="f3acb-121">Blazor スクリプトのソースを、クライアント側の Blazor WebAssembly スクリプトを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-121">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="f3acb-122">サーバー プロジェクトの `Startup.Configure` (`Startup.cs`) で:</span><span class="sxs-lookup"><span data-stu-id="f3acb-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="f3acb-123">開発環境で、アプリ ビルダーで `UseDeveloperExceptionPage` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="f3acb-124">アプリ ビルダーで `UseBlazorFrameworkFiles` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-124">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="f3acb-125">フォールバックを、`index.html` ページ (`endpoints.MapFallbackToFile("index.html");`) から `_Host.cshtml` ページに変更します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="f3acb-126">コンポーネント タグ ヘルパーを使用してページまたはビューのコンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="f3acb-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="f3acb-127">コンポーネント タグ ヘルパーでは、ページまたはビューに Blazor WebAssembly アプリからコンポーネントをレンダリングするための 2 つのレンダリング モードがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f3acb-127">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="f3acb-128">`WebAssembly`: ブラウザーに読み込まれるときに対話型コンポーネントを含めるために使用される Blazor WebAssembly アプリ用のマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="f3acb-128">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="f3acb-129">コンポーネントはプリレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="f3acb-129">The component isn't prerendered.</span></span> <span data-ttu-id="f3acb-130">このオプションを使用すると、異なる Blazor WebAssembly コンポーネントを異なるページにレンダリングしやすくなります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-130">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="f3acb-131">`WebAssemblyPrerendered`: コンポーネントを静的 HTML にプリレンダリングし、ブラウザーに読み込まれるときにコンポーネントを対話型にするために後で使用される Blazor WebAssembly アプリ用のマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="f3acb-132">次の Razor Pages の例では、`Counter` コンポーネントがページにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-132">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="f3acb-133">コンポーネントを対話形式にするために、ページの[レンダリング セクション](xref:mvc/views/layout#sections)に Blazor WebAssembly スクリプトが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-133">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="f3acb-134">コンポーネント タグ ヘルパー (`{APP ASSEMBLY}.Pages.Counter`) による `Counter` コンポーネントの完全な名前空間の使用を避けるために、クライアント アプリの `Pages` 名前空間に [`@using`](xref:mvc/views/razor#using) ディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="f3acb-135">次の例では、クライアント アプリの名前空間は `BlazorHosted.Client`、になります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-135">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="f3acb-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="f3acb-137">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="f3acb-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="f3acb-138">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="f3acb-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="f3acb-139">パラメーターの渡し方や <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> の構成などのコンポーネント タグ ヘルパーの詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="f3acb-140">前の例では、サーバー アプリのレイアウト (`_Layout.cshtml`) の `</body>` 終了タグの中に、スクリプトの[レンダリング セクション](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="f3acb-141">`_Layout.cshtml` ファイルは、Razor Pages アプリの `Pages/Shared` フォルダーまたは MVC アプリの `Views/Shared` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="f3acb-142">アプリで Blazor WebAssembly アプリのスタイルを使用してコンポーネントのスタイルを指定する必要がある場合は、アプリのスタイルを `_Layout.cshtml` ファイルに含めます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-142">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="f3acb-143">次の例では、クライアント アプリの名前空間は `BlazorHosted.Client`、になります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-143">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="f3acb-144">CSS セレクターを使用してページまたはビューのコンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="f3acb-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="f3acb-145">ルートコンポーネントを `Program.Main` (`Program.cs`) の "*クライアント*" プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="f3acb-146">次の例では、`my-counter` と一致する `id` を持つ要素を選択する CSS セレクターを使用して、ルート コンポーネントとして `Counter` コンポーネントが宣言されています。</span><span class="sxs-lookup"><span data-stu-id="f3acb-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="f3acb-147">次の例では、クライアント アプリの名前空間は `BlazorHosted.Client`、になります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-147">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="f3acb-148">次の Razor Pages の例では、`Counter` コンポーネントがページにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-148">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="f3acb-149">コンポーネントを対話形式にするために、ページの[レンダリング セクション](xref:mvc/views/layout#sections)に Blazor WebAssembly スクリプトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f3acb-149">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="f3acb-150">前の例では、サーバー アプリのレイアウト (`_Layout.cshtml`) の `</body>` 終了タグの中に、スクリプトの[レンダリング セクション](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="f3acb-151">`_Layout.cshtml` ファイルは、Razor Pages アプリの `Pages/Shared` フォルダーまたは MVC アプリの `Views/Shared` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="f3acb-152">アプリで Blazor WebAssembly アプリのスタイルを使用してコンポーネントのスタイルを指定する必要がある場合は、アプリのスタイルを `_Layout.cshtml` ファイルに含めます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-152">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="f3acb-153">次の例では、クライアント アプリの名前空間は `BlazorHosted.Client`、になります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-153">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f3acb-154">ホストされる Blazor WebAssembly ソリューションの Razor Pages と MVC アプリへの Razor コンポーネントの統合は、.NET 5 以降の ASP.NET Core でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f3acb-154">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="f3acb-155">この記事の .NET 5 以降のバージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="f3acb-156">Razor コンポーネントは、Blazor Server アプリ内の Razor Pages と MVC アプリに統合できます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-156">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="f3acb-157">ページまたはビューがレンダリングされるときには、コンポーネントを同時に事前レンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="f3acb-158">[アプリを構成](#configuration)した後、アプリの要件に応じて、次のセクションのガイダンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="f3acb-159">ルーティング可能なコンポーネント:ユーザー要求から直接ルーティング可能なコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="f3acb-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="f3acb-160">訪問者が [`@page`](xref:mvc/views/razor#page) ディレクティブを含むコンポーネントのブラウザーで HTTP 要求を行うことができる必要がある場合は、このガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="f3acb-161">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="f3acb-161">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="f3acb-162">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="f3acb-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="f3acb-163">[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view):ユーザー要求から直接ルーティングできないコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="f3acb-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="f3acb-164">アプリによって[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を含む既存のページやビューにコンポーネントが埋め込まれる場合は、このガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="f3acb-165">構成</span><span class="sxs-lookup"><span data-stu-id="f3acb-165">Configuration</span></span>

<span data-ttu-id="f3acb-166">既存の Razor Pages や MVC アプリでは、Razor コンポーネントをページとビューに統合できます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-166">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="f3acb-167">アプリのレイアウト ファイル (`_Layout.cshtml`) で:</span><span class="sxs-lookup"><span data-stu-id="f3acb-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="f3acb-168">次の `<base>` タグを `<head>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="f3acb-169">前の例の `href` 値 (*アプリ ベースのパス*) は、アプリがルート URL パス (`/`) に置かれていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="f3acb-169">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="f3acb-170">アプリがサブアプリケーションになっている場合は、記事 <xref:blazor/host-and-deploy/index#app-base-path> の「*アプリのベース パス*」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="f3acb-171">`_Layout.cshtml` ファイルは、Razor Pages アプリの `Pages/Shared` フォルダーまたは MVC アプリの `Views/Shared` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="f3acb-172">`blazor.server.js` スクリプトの `<script>` タグを `Scripts` レンダリング セクションの直前に追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="f3acb-173">フレームワークによって `blazor.server.js` スクリプトがアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="f3acb-174">手動でアプリにスクリプトを追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f3acb-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="f3acb-175">次の内容を含む `_Imports.razor` ファイルをプロジェクトのルート フォルダーに追加します (最後の名前空間 `MyAppNamespace` をアプリの名前空間に変更します)。</span><span class="sxs-lookup"><span data-stu-id="f3acb-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="f3acb-176">`Startup.ConfigureServices` で、Blazor Server サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-176">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="f3acb-177">`Startup.Configure` で、Blazor Hub エンドポイントを `app.UseEndpoints` に追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-177">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="f3acb-178">コンポーネントを任意のページまたはビューに統合します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-178">Integrate components into any page or view.</span></span> <span data-ttu-id="f3acb-179">詳細については、「[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="f3acb-180">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="f3acb-180">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="f3acb-181">*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*</span><span class="sxs-lookup"><span data-stu-id="f3acb-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="f3acb-182">Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="f3acb-182">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="f3acb-183">「[構成](#configuration)」セクションのガイダンスに従います。</span><span class="sxs-lookup"><span data-stu-id="f3acb-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="f3acb-184">次の内容の `App.razor` ファイルをプロジェクト ルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-184">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="f3acb-185">次の内容の `_Host.cshtml` ファイルを `Pages` フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="f3acb-186">コンポーネントでは、そのレイアウトで共有される `_Layout.cshtml` ファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="f3acb-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、`App` コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="f3acb-188">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="f3acb-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="f3acb-189">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="f3acb-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="f3acb-190">パラメーターの渡し方や <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> の構成などのコンポーネント タグ ヘルパーの詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="f3acb-191">`_Host.cshtml` ページの優先度が低いルートを、`Startup.Configure` 内のエンドポイント構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="f3acb-192">ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-192">Add routable components to the app.</span></span> <span data-ttu-id="f3acb-193">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="f3acb-194">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="f3acb-195">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="f3acb-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="f3acb-196">*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*</span><span class="sxs-lookup"><span data-stu-id="f3acb-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="f3acb-197">MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="f3acb-197">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="f3acb-198">「[構成](#configuration)」セクションのガイダンスに従います。</span><span class="sxs-lookup"><span data-stu-id="f3acb-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="f3acb-199">次の内容の `App.razor` ファイルを、プロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="f3acb-200">次の内容の `_Host.cshtml` ファイルを `Views/Home` フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="f3acb-201">コンポーネントでは、そのレイアウトで共有される `_Layout.cshtml` ファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="f3acb-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、`App` コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="f3acb-203">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="f3acb-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="f3acb-204">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="f3acb-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="f3acb-205">パラメーターの渡し方や <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> の構成などのコンポーネント タグ ヘルパーの詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="f3acb-206">Home コントローラーにアクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="f3acb-207">`Startup.Configure` 内のエンドポイント構成に `_Host.cshtml` ビューを返すコントローラー アクションのために、優先度が低いルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="f3acb-208">`Pages` フォルダーを作成し、アプリにルーティング可能なコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="f3acb-209">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="f3acb-210">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="f3acb-211">ページまたはビューからコンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="f3acb-211">Render components from a page or view</span></span>

<span data-ttu-id="f3acb-212">*これは、コンポーネントをユーザー要求から直接ルーティングできないページまたはビューにコンポーネントを追加することに関係するセクションです。*</span><span class="sxs-lookup"><span data-stu-id="f3acb-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="f3acb-213">ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="f3acb-214">ステートフル対話型コンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="f3acb-214">Render stateful interactive components</span></span>

<span data-ttu-id="f3acb-215">Razor ページまたはビューには、ステートフル対話型コンポーネントを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-215">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="f3acb-216">ページまたはビューがレンダリングされると、次の処理が行われます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-216">When the page or view renders:</span></span>

* <span data-ttu-id="f3acb-217">ページまたはビューと共にコンポーネントがプリレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="f3acb-218">プリレンダリングに使用された初期のコンポーネント状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="f3acb-219">SignalR 接続が確立されると、新しいコンポーネント状態が作成されます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-219">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="f3acb-220">次の Razor ページには、`Counter` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-220">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="f3acb-221">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="f3acb-222">非対話型コンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="f3acb-222">Render noninteractive components</span></span>

<span data-ttu-id="f3acb-223">次の Razor ページには、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="f3acb-223">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="f3acb-224">コンポーネントは静的にレンダリングされるため、コンポーネントは対話型ではありません。</span><span class="sxs-lookup"><span data-stu-id="f3acb-224">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="f3acb-225">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="f3acb-226">コンポーネントの名前空間</span><span class="sxs-lookup"><span data-stu-id="f3acb-226">Component namespaces</span></span>

<span data-ttu-id="f3acb-227">カスタム フォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を、ページまたはビューのいずれかに追加するか、`_ViewImports.cshtml` ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="f3acb-228">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-228">In the following example:</span></span>

* <span data-ttu-id="f3acb-229">`MyAppNamespace` をアプリの名前空間に変更します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="f3acb-230">コンポーネントを保持するために `Components` という名前のフォルダーを使用していない場合は、`Components` を、コンポーネントが置かれているフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="f3acb-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="f3acb-231">`_ViewImports.cshtml` ファイルは、Razor Pages アプリの `Pages` フォルダーまたは MVC アプリの `Views` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="f3acb-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="f3acb-232">詳細については、「<xref:blazor/components/index#namespaces>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3acb-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end

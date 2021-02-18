---
title: ASP.NET Core Blazor プロジェクトの構造
author: guardrex
description: ASP.NET Core Blazor アプリ プロジェクトの構造について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: 94b5a3d8c0f5b94ecac32e6fc5f94efeb8337f37
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280355"
---
# <a name="aspnet-core-blazor-project-structure"></a><span data-ttu-id="7888f-103">ASP.NET Core Blazor プロジェクトの構造</span><span class="sxs-lookup"><span data-stu-id="7888f-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="7888f-104">この記事では、Blazor プロジェクト テンプレートから生成された Blazor アプリを構成するファイルとフォルダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="7888f-104">This article describes the files and folders that make up a Blazor app generated from the Blazor project templates.</span></span>

## Blazor WebAssembly

<span data-ttu-id="7888f-105">Blazor WebAssembly テンプレート (`blazorwasm`) によって、Blazor WebAssembly アプリの初期ファイルおよびディレクトリ構造が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-105">The Blazor WebAssembly template (`blazorwasm`) creates the initial files and directory structure for a Blazor WebAssembly app.</span></span> <span data-ttu-id="7888f-106">このアプリには、静的資産からデータを読み込む `FetchData` コンポーネント、`weather.json`、および `Counter` コンポーネントとのユーザーの対話のためのデモンストレーション コードが設定されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-106">The app is populated with demonstration code for a `FetchData` component that loads data from a static asset, `weather.json`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="7888f-107">`Pages` フォルダー:Blazor アプリを構成するルーティング可能なコンポーネントまたはページ (`.razor`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7888f-107">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app.</span></span> <span data-ttu-id="7888f-108">各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="7888f-108">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="7888f-109">テンプレートには、以下のコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7888f-109">The template includes the following components:</span></span>
  * <span data-ttu-id="7888f-110">`Counter` コンポーネント (`Counter.razor`): カウンター ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-110">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="7888f-111">`FetchData` コンポーネント (`FetchData.razor`): フェッチ データ ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-111">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="7888f-112">`Index` コンポーネント (`Index.razor`): ホーム ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-112">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="7888f-113">`Properties/launchSettings.json`:[開発環境の構成](xref:fundamentals/environments#development-and-launchsettingsjson)を保持します。</span><span class="sxs-lookup"><span data-stu-id="7888f-113">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7888f-114">`Shared` フォルダー:次の共有コンポーネントおよびスタイルシートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7888f-114">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="7888f-115">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="7888f-115">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="7888f-116">`MainLayout.razor.css`: アプリのメイン レイアウト用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="7888f-116">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="7888f-117">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-117">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="7888f-118">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-118">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="7888f-119"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7888f-119">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="7888f-120">`NavMenu.razor.css`: アプリのナビゲーション メニュー用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="7888f-120">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="7888f-121">`SurveyPrompt` コンポーネント (`SurveyPrompt.razor`): Blazor のアンケート用コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="7888f-121">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7888f-122">`Shared` フォルダー:次の共有コンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7888f-122">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="7888f-123">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="7888f-123">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="7888f-124">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-124">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="7888f-125">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-125">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="7888f-126"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7888f-126">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="7888f-127">`SurveyPrompt` コンポーネント (`SurveyPrompt.razor`): Blazor のアンケート用コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="7888f-127">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7888f-128">`wwwroot`: アプリのパブリックな静的資産が含まれる、アプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。[構成設定](xref:blazor/fundamentals/configuration)のための `appsettings.json` と環境アプリ設定ファイルなどが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-128">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="7888f-129">`index.html` Web ページは、HTML ページとして実装されるアプリのルート ページです。</span><span class="sxs-lookup"><span data-stu-id="7888f-129">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="7888f-130">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-130">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="7888f-131">このページは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="7888f-131">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="7888f-132">コンポーネントは、`app` の `id` (`<div id="app">Loading...</div>`) を持つ `div` DOM 要素の位置にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7888f-132">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7888f-133">`wwwroot`: アプリのパブリックな静的資産が含まれる、アプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。[構成設定](xref:blazor/fundamentals/configuration)のための `appsettings.json` と環境アプリ設定ファイルなどが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-133">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="7888f-134">`index.html` Web ページは、HTML ページとして実装されるアプリのルート ページです。</span><span class="sxs-lookup"><span data-stu-id="7888f-134">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="7888f-135">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-135">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="7888f-136">このページは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="7888f-136">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="7888f-137">コンポーネントは `app` DOM 要素 (`<app>Loading...</app>`) の位置に表示されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-137">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7888f-138">`wwwroot/index.html` ファイルに追加する JavaScript (JS) ファイルは、終了タグ `</body>` の前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7888f-138">JavaScript (JS) files added to the `wwwroot/index.html` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="7888f-139">JS ファイルからカスタム JS コードが読み込まれる順序は、一部のシナリオで重要となります。</span><span class="sxs-lookup"><span data-stu-id="7888f-139">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="7888f-140">たとえば、相互運用メソッドを含む JS ファイルは、Blazor フレームワークの JS ファイルよりも先に含めるようにします。</span><span class="sxs-lookup"><span data-stu-id="7888f-140">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="7888f-141">`_Imports.razor`:名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント (`.razor`) に含める一般的な Razor ディレクティブが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-141">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="7888f-142">`App.razor`:<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="7888f-142">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="7888f-143"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="7888f-143">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7888f-144">`Program.cs`: WebAssembly ホストを設定するアプリのエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="7888f-144">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>
  
  * <span data-ttu-id="7888f-145">`App` コンポーネントは、アプリのルート コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="7888f-145">The `App` component is the root component of the app.</span></span> <span data-ttu-id="7888f-146">`App` コンポーネントは、ルート コンポーネント コレクション (`builder.RootComponents.Add<App>("#app")`) に対する `app` の `id` (`wwwroot/index.html` の `<div id="app">Loading...</div>`) を持つ `div` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-146">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
  * <span data-ttu-id="7888f-147">[サービス](xref:blazor/fundamentals/dependency-injection)が追加され、構成されます (例: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`)。</span><span class="sxs-lookup"><span data-stu-id="7888f-147">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7888f-148">`Program.cs`: WebAssembly ホストを設定するアプリのエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="7888f-148">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>

  * <span data-ttu-id="7888f-149">`App` コンポーネントは、アプリのルート コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="7888f-149">The `App` component is the root component of the app.</span></span> <span data-ttu-id="7888f-150">`App` コンポーネントは、ルート コンポーネント コレクション (`builder.RootComponents.Add<App>("app")`) に対する `app` DOM 要素 (`wwwroot/index.html` の `<app>Loading...</app>`) として指定されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-150">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
  * <span data-ttu-id="7888f-151">[サービス](xref:blazor/fundamentals/dependency-injection)が追加され、構成されます (例: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`)。</span><span class="sxs-lookup"><span data-stu-id="7888f-151">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="7888f-152">Blazor Server テンプレート (`blazorserver`) によって、Blazor Server アプリの初期ファイルおよびディレクトリ構造が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-152">The Blazor Server template (`blazorserver`) creates the initial files and directory structure for a Blazor Server app.</span></span> <span data-ttu-id="7888f-153">このアプリには、登録済みサービスからデータを読み込む `FetchData` コンポーネント、`WeatherForecastService`、および `Counter` コンポーネントとのユーザーの対話のためのデモンストレーション コードが設定されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-153">The app is populated with demonstration code for a `FetchData` component that loads data from a registered service, `WeatherForecastService`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="7888f-154">`Data` フォルダー:アプリの `FetchData` コンポーネントに気象データの例を提供する、`WeatherForecast` クラスと `WeatherForecastService` の実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-154">`Data` folder: Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provides example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="7888f-155">`Pages` フォルダー:Blazor アプリと Blazor Server アプリのルート Razor ページを構成するルーティング可能なコンポーネントまたはページ (`.razor`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7888f-155">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="7888f-156">各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="7888f-156">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="7888f-157">テンプレートには以下が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7888f-157">The template includes the following:</span></span>
  * <span data-ttu-id="7888f-158">`_Host.cshtml`: Razor ページとして実装されるアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="7888f-158">`_Host.cshtml`: The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="7888f-159">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-159">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="7888f-160">[ホスト] ページは、ルート `App` コンポーネント (`App.razor`) を表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="7888f-160">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="7888f-161">`Counter` コンポーネント (`Counter.razor`): カウンター ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-161">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="7888f-162">`Error` コンポーネント (`Error.razor`): アプリでハンドルされない例外が発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-162">`Error` component (`Error.razor`): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="7888f-163">`FetchData` コンポーネント (`FetchData.razor`): フェッチ データ ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-163">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="7888f-164">`Index` コンポーネント (`Index.razor`): ホーム ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-164">`Index` component (`Index.razor`): Implements the Home page.</span></span>

> [!NOTE]
> <span data-ttu-id="7888f-165">`Pages/_Host.cshtml` ファイルに追加する JavaScript (JS) ファイルは、終了タグ `</body>` の前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7888f-165">JavaScript (JS) files added to the `Pages/_Host.cshtml` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="7888f-166">JS ファイルからカスタム JS コードが読み込まれる順序は、一部のシナリオで重要となります。</span><span class="sxs-lookup"><span data-stu-id="7888f-166">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="7888f-167">たとえば、相互運用メソッドを含む JS ファイルは、Blazor フレームワークの JS ファイルよりも先に含めるようにします。</span><span class="sxs-lookup"><span data-stu-id="7888f-167">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="7888f-168">`Properties/launchSettings.json`:[開発環境の構成](xref:fundamentals/environments#development-and-launchsettingsjson)を保持します。</span><span class="sxs-lookup"><span data-stu-id="7888f-168">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7888f-169">`Shared` フォルダー:次の共有コンポーネントおよびスタイルシートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7888f-169">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="7888f-170">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="7888f-170">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="7888f-171">`MainLayout.razor.css`: アプリのメイン レイアウト用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="7888f-171">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="7888f-172">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-172">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="7888f-173">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-173">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="7888f-174"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7888f-174">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="7888f-175">`NavMenu.razor.css`: アプリのナビゲーション メニュー用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="7888f-175">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="7888f-176">`SurveyPrompt` コンポーネント (`SurveyPrompt.razor`): Blazor のアンケート用コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="7888f-176">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7888f-177">`Shared` フォルダー:次の共有コンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7888f-177">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="7888f-178">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="7888f-178">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="7888f-179">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="7888f-179">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="7888f-180">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-180">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="7888f-181"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7888f-181">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="7888f-182">`SurveyPrompt` コンポーネント (`SurveyPrompt.razor`): Blazor のアンケート用コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="7888f-182">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

* <span data-ttu-id="7888f-183">`wwwroot`:アプリのパブリックな静的アセットを含むアプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。</span><span class="sxs-lookup"><span data-stu-id="7888f-183">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="7888f-184">`_Imports.razor`:名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント (`.razor`) に含める一般的な Razor ディレクティブが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7888f-184">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="7888f-185">`App.razor`:<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="7888f-185">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="7888f-186"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="7888f-186">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="7888f-187">`appsettings.json` および環境アプリ設定ファイル:アプリの[構成設定](xref:blazor/fundamentals/configuration)を指定します。</span><span class="sxs-lookup"><span data-stu-id="7888f-187">`appsettings.json` and environmental app settings files: Provide [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span>

* <span data-ttu-id="7888f-188">`Program.cs`: ASP.NET Core [ホスト](xref:fundamentals/host/generic-host)を設定するアプリのエントリ ポイント。</span><span class="sxs-lookup"><span data-stu-id="7888f-188">`Program.cs`: The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span>

* <span data-ttu-id="7888f-189">`Startup.cs`: アプリのスタートアップ ロジックを含みます。</span><span class="sxs-lookup"><span data-stu-id="7888f-189">`Startup.cs`: Contains the app's startup logic.</span></span> <span data-ttu-id="7888f-190">`Startup` クラスには、次の 2 つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="7888f-190">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="7888f-191">`ConfigureServices`:アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="7888f-191">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="7888f-192"><xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> を呼び出すことによってサービスが追加されます。`WeatherForecastService` は、サンプルの `FetchData` コンポーネントで使用するためにサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-192">Services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="7888f-193">`Configure`:アプリの要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="7888f-193">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="7888f-194"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> は、ブラウザーとのリアルタイム接続用のエンドポイントを設定するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-194"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="7888f-195">この接続は [SignalR](xref:signalr/introduction) で作成されます。これは、アプリにリアルタイム Web 機能を追加するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="7888f-195">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="7888f-196">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) は、アプリ (`Pages/_Host.cshtml`) のルート ページを設定し、ナビゲーションを有効にするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7888f-196">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

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
ms.openlocfilehash: 958fa23a1befac3696d850d5409d4021dd109c22
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751543"
---
# <a name="aspnet-core-blazor-project-structure"></a><span data-ttu-id="31838-103">ASP.NET Core Blazor プロジェクトの構造</span><span class="sxs-lookup"><span data-stu-id="31838-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="31838-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="31838-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="31838-105">この記事では、Blazor プロジェクト テンプレートから生成された Blazor アプリを構成するファイルとフォルダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="31838-105">This article describes the files and folders that make up a Blazor app generated from the Blazor project templates.</span></span>

## Blazor WebAssembly

<span data-ttu-id="31838-106">Blazor WebAssembly テンプレート (`blazorwasm`) によって、Blazor WebAssembly アプリの初期ファイルおよびディレクトリ構造が作成されます。</span><span class="sxs-lookup"><span data-stu-id="31838-106">The Blazor WebAssembly template (`blazorwasm`) creates the initial files and directory structure for a Blazor WebAssembly app.</span></span> <span data-ttu-id="31838-107">このアプリには、静的資産からデータを読み込む `FetchData` コンポーネント、`weather.json`、および `Counter` コンポーネントとのユーザーの対話のためのデモンストレーション コードが設定されます。</span><span class="sxs-lookup"><span data-stu-id="31838-107">The app is populated with demonstration code for a `FetchData` component that loads data from a static asset, `weather.json`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="31838-108">`Pages` フォルダー:Blazor アプリを構成するルーティング可能なコンポーネントまたはページ (`.razor`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="31838-108">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app.</span></span> <span data-ttu-id="31838-109">各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="31838-109">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="31838-110">テンプレートには、以下のコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="31838-110">The template includes the following components:</span></span>
  * <span data-ttu-id="31838-111">`Counter` コンポーネント (`Counter.razor`): カウンター ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-111">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="31838-112">`FetchData` コンポーネント (`FetchData.razor`): フェッチ データ ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-112">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="31838-113">`Index` コンポーネント (`Index.razor`): ホーム ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-113">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="31838-114">`Properties/launchSettings.json`:[開発環境の構成](xref:fundamentals/environments#development-and-launchsettingsjson)を保持します。</span><span class="sxs-lookup"><span data-stu-id="31838-114">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="31838-115">`Shared` フォルダー:次の共有コンポーネントおよびスタイルシートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="31838-115">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="31838-116">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="31838-116">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="31838-117">`MainLayout.razor.css`: アプリのメイン レイアウト用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="31838-117">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="31838-118">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-118">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="31838-119">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-119">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="31838-120"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="31838-120">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="31838-121">`NavMenu.razor.css`: アプリのナビゲーション メニュー用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="31838-121">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="31838-122">`SurveyPrompt` コンポーネント (`SurveyPrompt.razor`): Blazor のアンケート用コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="31838-122">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="31838-123">`Shared` フォルダー:次の共有コンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="31838-123">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="31838-124">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="31838-124">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="31838-125">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-125">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="31838-126">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-126">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="31838-127"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="31838-127">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="31838-128">`SurveyPrompt` コンポーネント (`SurveyPrompt.razor`): Blazor のアンケート用コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="31838-128">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="31838-129">`wwwroot`: アプリのパブリックな静的資産が含まれる、アプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。[構成設定](xref:blazor/fundamentals/configuration)のための `appsettings.json` と環境アプリ設定ファイルなどが含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-129">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="31838-130">`index.html` Web ページは、HTML ページとして実装されるアプリのルート ページです。</span><span class="sxs-lookup"><span data-stu-id="31838-130">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="31838-131">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="31838-131">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="31838-132">このページは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="31838-132">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="31838-133">コンポーネントは、`app` の `id` (`<div id="app">Loading...</div>`) を持つ `div` DOM 要素の位置にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31838-133">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="31838-134">`wwwroot`: アプリのパブリックな静的資産が含まれる、アプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。[構成設定](xref:blazor/fundamentals/configuration)のための `appsettings.json` と環境アプリ設定ファイルなどが含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-134">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="31838-135">`index.html` Web ページは、HTML ページとして実装されるアプリのルート ページです。</span><span class="sxs-lookup"><span data-stu-id="31838-135">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="31838-136">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="31838-136">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="31838-137">このページは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="31838-137">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="31838-138">コンポーネントは `app` DOM 要素 (`<app>Loading...</app>`) の位置に表示されます。</span><span class="sxs-lookup"><span data-stu-id="31838-138">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="31838-139">`wwwroot/index.html` ファイルに追加する JavaScript (JS) ファイルは、終了タグ `</body>` の前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31838-139">JavaScript (JS) files added to the `wwwroot/index.html` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="31838-140">JS ファイルからカスタム JS コードが読み込まれる順序は、一部のシナリオで重要となります。</span><span class="sxs-lookup"><span data-stu-id="31838-140">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="31838-141">たとえば、相互運用メソッドを含む JS ファイルは、Blazor フレームワークの JS ファイルよりも先に含めるようにします。</span><span class="sxs-lookup"><span data-stu-id="31838-141">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="31838-142">`_Imports.razor`:名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント (`.razor`) に含める一般的な Razor ディレクティブが含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-142">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="31838-143">`App.razor`:<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="31838-143">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="31838-144"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="31838-144">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="31838-145">`Program.cs`: WebAssembly ホストを設定するアプリのエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="31838-145">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>
  
  * <span data-ttu-id="31838-146">`App` コンポーネントは、アプリのルート コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="31838-146">The `App` component is the root component of the app.</span></span> <span data-ttu-id="31838-147">`App` コンポーネントは、ルート コンポーネント コレクション (`builder.RootComponents.Add<App>("#app")`) に対する `app` の `id` (`wwwroot/index.html` の `<div id="app">Loading...</div>`) を持つ `div` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="31838-147">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
  * <span data-ttu-id="31838-148">[サービス](xref:blazor/fundamentals/dependency-injection)が追加され、構成されます (例: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`)。</span><span class="sxs-lookup"><span data-stu-id="31838-148">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="31838-149">`Program.cs`: WebAssembly ホストを設定するアプリのエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="31838-149">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>

  * <span data-ttu-id="31838-150">`App` コンポーネントは、アプリのルート コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="31838-150">The `App` component is the root component of the app.</span></span> <span data-ttu-id="31838-151">`App` コンポーネントは、ルート コンポーネント コレクション (`builder.RootComponents.Add<App>("app")`) に対する `app` DOM 要素 (`wwwroot/index.html` の `<app>Loading...</app>`) として指定されます。</span><span class="sxs-lookup"><span data-stu-id="31838-151">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
  * <span data-ttu-id="31838-152">[サービス](xref:blazor/fundamentals/dependency-injection)が追加され、構成されます (例: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`)。</span><span class="sxs-lookup"><span data-stu-id="31838-152">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="31838-153">Blazor Server テンプレート (`blazorserver`) によって、Blazor Server アプリの初期ファイルおよびディレクトリ構造が作成されます。</span><span class="sxs-lookup"><span data-stu-id="31838-153">The Blazor Server template (`blazorserver`) creates the initial files and directory structure for a Blazor Server app.</span></span> <span data-ttu-id="31838-154">このアプリには、登録済みサービスからデータを読み込む `FetchData` コンポーネント、`WeatherForecastService`、および `Counter` コンポーネントとのユーザーの対話のためのデモンストレーション コードが設定されます。</span><span class="sxs-lookup"><span data-stu-id="31838-154">The app is populated with demonstration code for a `FetchData` component that loads data from a registered service, `WeatherForecastService`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="31838-155">`Data` フォルダー:アプリの `FetchData` コンポーネントに気象データの例を提供する、`WeatherForecast` クラスと `WeatherForecastService` の実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-155">`Data` folder: Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provides example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="31838-156">`Pages` フォルダー:Blazor アプリと Blazor Server アプリのルート Razor ページを構成するルーティング可能なコンポーネントまたはページ (`.razor`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="31838-156">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="31838-157">各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="31838-157">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="31838-158">テンプレートには以下が含まれています。</span><span class="sxs-lookup"><span data-stu-id="31838-158">The template includes the following:</span></span>
  * <span data-ttu-id="31838-159">`_Host.cshtml`: Razor ページとして実装されるアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="31838-159">`_Host.cshtml`: The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="31838-160">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="31838-160">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="31838-161">[ホスト] ページは、ルート `App` コンポーネント (`App.razor`) を表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="31838-161">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="31838-162">`Counter` コンポーネント (`Counter.razor`): カウンター ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-162">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="31838-163">`Error` コンポーネント (`Error.razor`): アプリでハンドルされない例外が発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="31838-163">`Error` component (`Error.razor`): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="31838-164">`FetchData` コンポーネント (`FetchData.razor`): フェッチ データ ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-164">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="31838-165">`Index` コンポーネント (`Index.razor`): ホーム ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-165">`Index` component (`Index.razor`): Implements the Home page.</span></span>

> [!NOTE]
> <span data-ttu-id="31838-166">`Pages/_Host.cshtml` ファイルに追加する JavaScript (JS) ファイルは、終了タグ `</body>` の前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31838-166">JavaScript (JS) files added to the `Pages/_Host.cshtml` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="31838-167">JS ファイルからカスタム JS コードが読み込まれる順序は、一部のシナリオで重要となります。</span><span class="sxs-lookup"><span data-stu-id="31838-167">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="31838-168">たとえば、相互運用メソッドを含む JS ファイルは、Blazor フレームワークの JS ファイルよりも先に含めるようにします。</span><span class="sxs-lookup"><span data-stu-id="31838-168">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="31838-169">`Properties/launchSettings.json`:[開発環境の構成](xref:fundamentals/environments#development-and-launchsettingsjson)を保持します。</span><span class="sxs-lookup"><span data-stu-id="31838-169">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="31838-170">`Shared` フォルダー:次の共有コンポーネントおよびスタイルシートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="31838-170">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="31838-171">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="31838-171">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="31838-172">`MainLayout.razor.css`: アプリのメイン レイアウト用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="31838-172">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="31838-173">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-173">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="31838-174">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-174">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="31838-175"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="31838-175">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="31838-176">`NavMenu.razor.css`: アプリのナビゲーション メニュー用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="31838-176">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="31838-177">`SurveyPrompt` コンポーネント (`SurveyPrompt.razor`): Blazor のアンケート用コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="31838-177">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="31838-178">`Shared` フォルダー:次の共有コンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="31838-178">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="31838-179">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="31838-179">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="31838-180">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="31838-180">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="31838-181">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-181">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="31838-182"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="31838-182">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="31838-183">`SurveyPrompt` コンポーネント (`SurveyPrompt.razor`): Blazor のアンケート用コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="31838-183">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

* <span data-ttu-id="31838-184">`wwwroot`:アプリのパブリックな静的アセットを含むアプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。</span><span class="sxs-lookup"><span data-stu-id="31838-184">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="31838-185">`_Imports.razor`:名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント (`.razor`) に含める一般的な Razor ディレクティブが含まれます。</span><span class="sxs-lookup"><span data-stu-id="31838-185">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="31838-186">`App.razor`:<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="31838-186">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="31838-187"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="31838-187">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="31838-188">`appsettings.json` および環境アプリ設定ファイル:アプリの[構成設定](xref:blazor/fundamentals/configuration)を指定します。</span><span class="sxs-lookup"><span data-stu-id="31838-188">`appsettings.json` and environmental app settings files: Provide [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span>

* <span data-ttu-id="31838-189">`Program.cs`: ASP.NET Core [ホスト](xref:fundamentals/host/generic-host)を設定するアプリのエントリ ポイント。</span><span class="sxs-lookup"><span data-stu-id="31838-189">`Program.cs`: The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span>

* <span data-ttu-id="31838-190">`Startup.cs`: アプリのスタートアップ ロジックを含みます。</span><span class="sxs-lookup"><span data-stu-id="31838-190">`Startup.cs`: Contains the app's startup logic.</span></span> <span data-ttu-id="31838-191">`Startup` クラスには、次の 2 つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="31838-191">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="31838-192">`ConfigureServices`:アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="31838-192">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="31838-193"><xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> を呼び出すことによってサービスが追加されます。`WeatherForecastService` は、サンプルの `FetchData` コンポーネントで使用するためにサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="31838-193">Services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="31838-194">`Configure`:アプリの要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="31838-194">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="31838-195"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> は、ブラウザーとのリアルタイム接続用のエンドポイントを設定するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31838-195"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="31838-196">この接続は [SignalR](xref:signalr/introduction) で作成されます。これは、アプリにリアルタイム Web 機能を追加するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="31838-196">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="31838-197">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) は、アプリ (`Pages/_Host.cshtml`) のルート ページを設定し、ナビゲーションを有効にするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31838-197">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

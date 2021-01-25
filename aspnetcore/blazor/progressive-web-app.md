---
title: ASP.NET Core Blazor WebAssembly を使用してプログレッシブ Web アプリケーションをビルドする
author: guardrex
description: 最新のブラウザー機能を使用してデスクトップ アプリのように動作する、Blazor ベースのプログレッシブ Web アプリケーション (PWA) をビルドする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2020
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
uid: blazor/progressive-web-app
ms.openlocfilehash: 196e19528341e98ac06cefb08ba92f9e47d265ea
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252475"
---
# <a name="build-progressive-web-applications-with-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="40e3e-103">ASP.NET Core Blazor WebAssembly を使用してプログレッシブ Web アプリケーションをビルドする</span><span class="sxs-lookup"><span data-stu-id="40e3e-103">Build Progressive Web Applications with ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="40e3e-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="40e3e-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="40e3e-105">プログレッシブ Web アプリケーション (PWA) は、通常、最新のブラウザーの API と機能を使用してデスクトップ アプリのように動作するシングル ページ アプリケーション (SPA) です。</span><span class="sxs-lookup"><span data-stu-id="40e3e-105">A Progressive Web Application (PWA) is usually a Single Page Application (SPA) that uses modern browser APIs and capabilities to behave like a desktop app.</span></span> <span data-ttu-id="40e3e-106">Blazor WebAssembly は、標準ベースのクライアント側 Web アプリ プラットフォームであるため、次の機能に必要な任意のブラウザー API (PWA API を含む) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-106">Blazor WebAssembly is a standards-based client-side web app platform, so it can use any browser API, including PWA APIs required for the following capabilities:</span></span>

* <span data-ttu-id="40e3e-107">ネットワーク速度に関係なく、オフラインで動作し、瞬時に読み込む。</span><span class="sxs-lookup"><span data-stu-id="40e3e-107">Working offline and loading instantly, independent of network speed.</span></span>
* <span data-ttu-id="40e3e-108">ブラウザー ウィンドウだけでなく、独自のアプリ ウィンドウでも実行できる。</span><span class="sxs-lookup"><span data-stu-id="40e3e-108">Running in its own app window, not just a browser window.</span></span>
* <span data-ttu-id="40e3e-109">ホスト オペレーティング システムのスタート メニュー、ドッキング、またはホーム画面から起動される。</span><span class="sxs-lookup"><span data-stu-id="40e3e-109">Being launched from the host's operating system start menu, dock, or home screen.</span></span>
* <span data-ttu-id="40e3e-110">ユーザーがアプリを使用していない場合でも、バックエンド サーバーからプッシュ通知を受信する。</span><span class="sxs-lookup"><span data-stu-id="40e3e-110">Receiving push notifications from a backend server, even while the user isn't using the app.</span></span>
* <span data-ttu-id="40e3e-111">バックグラウンドで自動更新される。</span><span class="sxs-lookup"><span data-stu-id="40e3e-111">Automatically updating in the background.</span></span>

<span data-ttu-id="40e3e-112">このようなアプリを説明するために "*プログレッシブ*" という単語を使用しているのは、次の理由からです。</span><span class="sxs-lookup"><span data-stu-id="40e3e-112">The word *progressive* is used to describe such apps because:</span></span>

* <span data-ttu-id="40e3e-113">ユーザーは、他の SPA と同じように、まず自分の Web ブラウザー内でアプリを検出して使用する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-113">A user might first discover and use the app within their web browser like any other SPA.</span></span>
* <span data-ttu-id="40e3e-114">その後、ユーザーはそれの OS へのインストールと、プッシュ通知の有効化に進みます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-114">Later, the user progresses to installing it in their OS and enabling push notifications.</span></span>

## <a name="create-a-project-from-the-pwa-template"></a><span data-ttu-id="40e3e-115">PWA テンプレートからプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="40e3e-115">Create a project from the PWA template</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40e3e-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40e3e-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40e3e-117">**[新しいプロジェクトの作成]** ダイアログで新しい **Blazor WebAssembly アプリ** を作成するときに、 **[プログレッシブ Web アプリケーション]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-117">When creating a new **Blazor WebAssembly App** in the **Create a New Project** dialog, select the **Progressive Web Application** check box:</span></span>

![Visual Studio の [新しいプロジェクトの作成] ダイアログで [プログレッシブ Web アプリケーション] チェック ボックスが選択されている。](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="40e3e-119">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="40e3e-119">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="40e3e-120">次のコマンドを使用し、コマンド シェルで `--pwa` スイッチを使用して PWA プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-120">Use the following command to create a PWA project in a command shell with the `--pwa` switch:</span></span>

```dotnetcli
dotnet new blazorwasm -o MyBlazorPwa --pwa
```

<span data-ttu-id="40e3e-121">上記のコマンドでは、`-o|--output` オプションによって `MyBlazorPwa` という名前のアプリ用の新しいフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-121">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>

---

<span data-ttu-id="40e3e-122">必要に応じて、ASP.NET Core でホストされるテンプレートから作成されたアプリに対して PWA を構成できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-122">Optionally, PWA can be configured for an app created from the ASP.NET Core Hosted template.</span></span> <span data-ttu-id="40e3e-123">PWA シナリオは、ホスティング モデルに依存しません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-123">The PWA scenario is independent of the hosting model.</span></span>

## <a name="convert-an-existing-no-locblazor-webassembly-app-into-a-pwa"></a><span data-ttu-id="40e3e-124">既存の Blazor WebAssembly アプリを PWA に変換する</span><span class="sxs-lookup"><span data-stu-id="40e3e-124">Convert an existing Blazor WebAssembly app into a PWA</span></span>

<span data-ttu-id="40e3e-125">このセクションのガイダンスに従って、既存の Blazor WebAssembly アプリを PWA に変換します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-125">Convert an existing Blazor WebAssembly app into a PWA following the guidance in this section.</span></span>

<span data-ttu-id="40e3e-126">アプリのプロジェクト ファイル内で:</span><span class="sxs-lookup"><span data-stu-id="40e3e-126">In the app's project file:</span></span>

* <span data-ttu-id="40e3e-127">次の `ServiceWorkerAssetsManifest` プロパティを `PropertyGroup` に追加します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-127">Add the following `ServiceWorkerAssetsManifest` property to a `PropertyGroup`:</span></span>

  ```xml
    ...
    <ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
  </PropertyGroup>
   ```

* <span data-ttu-id="40e3e-128">次の `ServiceWorker` 項目を `ItemGroup` に追加します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-128">Add the following `ServiceWorker` item to an `ItemGroup`:</span></span>

  ```xml
  <ItemGroup>
    <ServiceWorker Include="wwwroot\service-worker.js" 
      PublishedContent="wwwroot\service-worker.published.js" />
  </ItemGroup>
  ```

<span data-ttu-id="40e3e-129">静的資産を取得するには、次の **いずれか** の方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-129">To obtain static assets, use **one** of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="40e3e-130">コマンド シェルで [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用して、新しい PWA プロジェクトを個別に作成します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-130">Create a separate, new PWA project with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa
  ```
  
  <span data-ttu-id="40e3e-131">上記のコマンドでは、`-o|--output` オプションによって `MyBlazorPwa` という名前のアプリ用の新しいフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-131">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>
  
  <span data-ttu-id="40e3e-132">アプリを最新リリース用に変換しない場合は、`-f|--framework` オプションを渡します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-132">If you aren't converting an app for the latest release, pass the `-f|--framework` option.</span></span> <span data-ttu-id="40e3e-133">次の例は、ASP.NET Core バージョン 3.1 用のアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-133">The following example creates the app for ASP.NET Core version 3.1:</span></span>
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```

* <span data-ttu-id="40e3e-134">次の URL の ASP.NET Core GitHub リポジトリに移動します。これは、5.0 リリースの参照ソースと資産にリンクしています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-134">Navigate to the ASP.NET Core GitHub repository at the following URL, which links to 5.0 release reference source and assets.</span></span> <span data-ttu-id="40e3e-135">5\.0 リリースのアプリを変換しない場合は、アプリに適用する **[Switch branches or tags]\(分岐またはタグの切り替え\)** ドロップダウン リストから、使用しているリリースを選択します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-135">If you aren't converting an app for the 5.0 release, select the release that you're working with from the **Switch branches or tags** drop-down list that applies to your app.</span></span>

  [<span data-ttu-id="40e3e-136">dotnet/aspnetcore (リリース 5.0) Blazor WebAssembly プロジェクト テンプレート `wwwroot` フォルダー</span><span class="sxs-lookup"><span data-stu-id="40e3e-136">dotnet/aspnetcore (release 5.0) Blazor WebAssembly project template `wwwroot` folder</span></span>](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="40e3e-137">コマンド シェルで [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用して、新しい PWA プロジェクトを個別に作成します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-137">Create a separate, new PWA project with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="40e3e-138">`-f|--framework` オプションを渡してバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-138">Pass the `-f|--framework` option to select the version.</span></span> <span data-ttu-id="40e3e-139">次の例は、ASP.NET Core バージョン 3.1 用のアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-139">The following example creates the app for ASP.NET Core version 3.1:</span></span>
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```
  
  <span data-ttu-id="40e3e-140">上記のコマンドでは、`-o|--output` オプションによって `MyBlazorPwa` という名前のアプリ用の新しいフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-140">In the preceding command, the `-o|--output` option creates a new folder for the app named `MyBlazorPwa`.</span></span>

* <span data-ttu-id="40e3e-141">次の URL の ASP.NET Core GitHub リポジトリに移動します。これは、3.1 リリースの参照ソースと資産にリンクしています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-141">Navigate to the ASP.NET Core GitHub repository at the following URL, which links to 3.1 release reference source and assets:</span></span>

  [<span data-ttu-id="40e3e-142">dotnet/aspnetcore (リリース 3.1) Blazor WebAssembly プロジェクト テンプレート `wwwroot` フォルダー</span><span class="sxs-lookup"><span data-stu-id="40e3e-142">dotnet/aspnetcore (release 3.1) Blazor WebAssembly project template `wwwroot` folder</span></span>](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/ProjectTemplates/ComponentsWebAssembly.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

  > [!NOTE]
  > <span data-ttu-id="40e3e-143">Blazor WebAssembly プロジェクト テンプレートの URL は、ASP.NET Core 3.1 のリリース後に変更されました。</span><span class="sxs-lookup"><span data-stu-id="40e3e-143">The URL for Blazor WebAssembly project template changed after the release of ASP.NET Core 3.1.</span></span> <span data-ttu-id="40e3e-144">5\.0 以降の参照資産は、次の URL から入手できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-144">Reference assets for 5.0 or later are available at the following URL:</span></span>
  >
  > [<span data-ttu-id="40e3e-145">dotnet/aspnetcore (リリース 5.0) Blazor WebAssembly プロジェクト テンプレート `wwwroot` フォルダー</span><span class="sxs-lookup"><span data-stu-id="40e3e-145">dotnet/aspnetcore (release 5.0) Blazor WebAssembly project template `wwwroot` folder</span></span>](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

<span data-ttu-id="40e3e-146">作成したアプリのソース `wwwroot` フォルダーから、または `dotnet/aspnetcore` GitHub リポジトリの参照資産から、次のファイルをアプリの `wwwroot` フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-146">From the source `wwwroot` folder either in the app that you created or from the reference assets in the `dotnet/aspnetcore` GitHub repository, copy the following files into the app's `wwwroot` folder:</span></span>

* `icon-512.png`
* `manifest.json`
* `service-worker.js`
* `service-worker.published.js`

<span data-ttu-id="40e3e-147">アプリの `wwwroot/index.html` ファイルで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="40e3e-147">In the app's `wwwroot/index.html` file:</span></span>

* <span data-ttu-id="40e3e-148">マニフェストとアプリのアイコンに `<link>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-148">Add `<link>` elements for the manifest and app icon:</span></span>

  ```html
  <link href="manifest.json" rel="manifest" />
  <link rel="apple-touch-icon" sizes="512x512" href="icon-512.png" />
  ```

* <span data-ttu-id="40e3e-149">`blazor.webassembly.js` スクリプト タグの直後に、次の `<script>` タグを終了タグ `</body>` の内側に追加します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-149">Add the following `<script>` tag inside the closing `</body>` tag immediately after the `blazor.webassembly.js` script tag:</span></span>

  ```html
      ...
      <script>navigator.serviceWorker.register('service-worker.js');</script>
  </body>
  ```

## <a name="installation-and-app-manifest"></a><span data-ttu-id="40e3e-150">インストールとアプリ マニフェスト</span><span class="sxs-lookup"><span data-stu-id="40e3e-150">Installation and app manifest</span></span>

<span data-ttu-id="40e3e-151">PWA テンプレートを使用して作成されたアプリにアクセスするときに、ユーザーはアプリを OS のスタート メニュー、ドッキング、またはホーム画面にインストールすることを選択できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-151">When visiting an app created using the PWA template, users have the option of installing the app into their OS's start menu, dock, or home screen.</span></span> <span data-ttu-id="40e3e-152">このオプションがどのように表示されるかは、ユーザーのブラウザーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-152">The way this option is presented depends on the user's browser.</span></span> <span data-ttu-id="40e3e-153">Microsoft Edge や Chrome などのデスクトップ Chromium ベースのブラウザーを使用している場合、URL バーに **[追加]** ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-153">When using desktop Chromium-based browsers, such as Edge or Chrome, an **Add** button appears within the URL bar.</span></span> <span data-ttu-id="40e3e-154">ユーザーが **[追加]** ボタンを選択すると、確認のダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-154">After the user selects the **Add** button, they receive a confirmation dialog:</span></span>

![Google Chrome の確認ダイアログには、"MyBlazorPwa" アプリのユーザーのための [インストール] ボタンがあります。](progressive-web-app/_static/image2.png)

<span data-ttu-id="40e3e-156">iOS では、Safari の **[共有]** ボタンとその **[ホーム画面に追加]** オプションを使用して、PWA をインストールできます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-156">On iOS, visitors can install the PWA using Safari's **Share** button and its **Add to Homescreen** option.</span></span> <span data-ttu-id="40e3e-157">Android 用の Chrome では、ユーザーは右上隅の **[メニュー]** ボタンを選択してから、 **[ホーム画面に追加]** を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-157">On Chrome for Android, users should select the **Menu** button in the upper-right corner, followed by **Add to Home screen**.</span></span>

<span data-ttu-id="40e3e-158">インストールが完了すると、アプリはアドレス バーのない独自のウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-158">Once installed, the app appears in its own window without an address bar:</span></span>

!['MyBlazorPwa' アプリは、Google Chrome でアドレス バーなしで実行されます。](progressive-web-app/_static/image3.png)

<span data-ttu-id="40e3e-160">ウィンドウのタイトル、配色、アイコン、またはその他の詳細をカスタマイズするには、プロジェクトの `wwwroot` ディレクトリにある `manifest.json` ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-160">To customize the window's title, color scheme, icon, or other details, see the `manifest.json` file in the project's `wwwroot` directory.</span></span> <span data-ttu-id="40e3e-161">このファイルのスキーマは、Web 標準によって定義されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-161">The schema of this file is defined by web standards.</span></span> <span data-ttu-id="40e3e-162">詳細については、[MDN Web ドキュメント:Web アプリ マニフェスト](https://developer.mozilla.org/docs/Web/Manifest)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-162">For more information, see [MDN web docs: Web App Manifest](https://developer.mozilla.org/docs/Web/Manifest).</span></span>

## <a name="offline-support"></a><span data-ttu-id="40e3e-163">オフライン サポート</span><span class="sxs-lookup"><span data-stu-id="40e3e-163">Offline support</span></span>

<span data-ttu-id="40e3e-164">既定では、PWA テンプレート オプションを使用して作成されたアプリでは、オフラインでの実行がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-164">By default, apps created using the PWA template option have support for running offline.</span></span> <span data-ttu-id="40e3e-165">ユーザーのアプリへの最初のアクセスは、アプリがオンラインになっている間に行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-165">A user must first visit the app while they're online.</span></span> <span data-ttu-id="40e3e-166">オフラインでの操作に必要なすべてのリソースが、ブラウザーによって自動的にダウンロードされてキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-166">The browser automatically downloads and caches all of the resources required to operate offline.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40e3e-167">開発サポートにより、変更してテストするという通常の開発サイクルが妨げられる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-167">Development support would interfere with the usual development cycle of making changes and testing them.</span></span> <span data-ttu-id="40e3e-168">そのため、オフライン サポートは、"*公開された*" アプリに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="40e3e-168">Therefore, offline support is only enabled for *published* apps.</span></span> 

> [!WARNING]
> <span data-ttu-id="40e3e-169">オフライン対応 PWA を配布することを予定している場合は、[重要な警告と注意事項](#caveats-for-offline-pwas)がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-169">If you intend to distribute an offline-enabled PWA, there are [several important warnings and caveats](#caveats-for-offline-pwas).</span></span> <span data-ttu-id="40e3e-170">これらのシナリオはオフラインの PWA に固有のものであり、Blazor に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-170">These scenarios are inherent to offline PWAs and not specific to Blazor.</span></span> <span data-ttu-id="40e3e-171">オフライン対応アプリがどのように動作するかを想定する前に、これらの注意事項を読んで理解しておいてください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-171">Be sure to read and understand these caveats before making assumptions about how your offline-enabled app will work.</span></span>

<span data-ttu-id="40e3e-172">オフライン サポートのしくみを確認するには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="40e3e-172">To see how offline support works:</span></span>

1. <span data-ttu-id="40e3e-173">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="40e3e-173">Publish the app.</span></span> <span data-ttu-id="40e3e-174">詳細については、「<xref:blazor/host-and-deploy/index#publish-the-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-174">For more information, see <xref:blazor/host-and-deploy/index#publish-the-app>.</span></span>
1. <span data-ttu-id="40e3e-175">HTTPS をサポートするサーバーにアプリを配置し、アプリのセキュリティで保護された HTTPS アドレスを使用して、ブラウザーでアプリにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-175">Deploy the app to a server that supports HTTPS, and access the app in a browser at its secure HTTPS address.</span></span>
1. <span data-ttu-id="40e3e-176">ブラウザーの開発者ツールを開き、 **[アプリケーション]** タブで *サービス ワーカー* がホストに登録されていることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-176">Open the browser's dev tools and verify that a *Service Worker* is registered for the host on the **Application** tab:</span></span>

   ![サービス ワーカーがアクティブ化され、実行されていることが示されている Google Chrome 開発者ツールの [アプリケーション] タブ。](progressive-web-app/_static/image4.png)

1. <span data-ttu-id="40e3e-178">ページを再度読み込み、 **[ネットワーク]** タブを確認します。**サービス ワーカー** または **メモリ キャッシュ** が、すべてのページのアセットのソースとして一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-178">Reload the page and examine the **Network** tab. **Service Worker** or **memory cache** are listed as the sources for all of the page's assets:</span></span>

   ![すべてのページのアセットのソースが表示されている Google Chrome 開発者ツールの [ネットワーク] タブ。](progressive-web-app/_static/image5.png)

1. <span data-ttu-id="40e3e-180">アプリを読み込むためにブラウザーがネットワーク アクセスに依存していないことを確認するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-180">To verify that the browser isn't dependent on network access to load the app, either:</span></span>

   * <span data-ttu-id="40e3e-181">Web サーバーをシャットダウンし、アプリが引き続き正常に機能することを確認します。これには、ページの再読み込みも含まれます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-181">Shut down the web server and see how the app continues to function normally, which includes page reloads.</span></span> <span data-ttu-id="40e3e-182">低速のネットワーク接続がある場合も同様に、アプリは引き続き正常に機能します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-182">Likewise, the app continues to function normally when there's a slow network connection.</span></span>
   * <span data-ttu-id="40e3e-183">**[ネットワーク]** タブでオフライン モードをシミュレートするようにブラウザーに指示します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-183">Instruct the browser to simulate offline mode in the **Network** tab:</span></span>

   ![ブラウザー モードのドロップダウンが [オンライン] から [オフライン] に変更されている Google Chrome 開発者ツールの [ネットワーク] タブ。](progressive-web-app/_static/image6.png)

<span data-ttu-id="40e3e-185">サービス ワーカーを使用したオフライン サポートは、Blazor 固有ではなく、Web 標準です。</span><span class="sxs-lookup"><span data-stu-id="40e3e-185">Offline support using a service worker is a web standard, not specific to Blazor.</span></span> <span data-ttu-id="40e3e-186">サービス ワーカーの詳細については、[MDN Web ドキュメント:サービス ワーカー API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-186">For more information on service workers, see [MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).</span></span> <span data-ttu-id="40e3e-187">サービス ワーカーの一般的な使用パターンの詳細については、[Google Web:サービス ワーカーのライフサイクル](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-187">To learn more about common usage patterns for service workers, see [Google Web: The Service Worker Lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).</span></span>

<span data-ttu-id="40e3e-188">Blazor の PWA テンプレートでは、次の 2 つのサービス ワーカー ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-188">Blazor's PWA template produces two service worker files:</span></span>

* <span data-ttu-id="40e3e-189">`wwwroot/service-worker.js`: 開発中に使用されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-189">`wwwroot/service-worker.js`, which is used during development.</span></span>
* <span data-ttu-id="40e3e-190">`wwwroot/service-worker.published.js`: アプリが発行された後に使用されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-190">`wwwroot/service-worker.published.js`, which is used after the app is published.</span></span>

<span data-ttu-id="40e3e-191">2 つのサービス ワーカー ファイル間でロジックを共有するには、次のアプローチを検討してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-191">To share logic between the two service worker files, consider the following approach:</span></span>

* <span data-ttu-id="40e3e-192">共通のロジックを保持する 3 番目の JavaScript ファイルを追加する。</span><span class="sxs-lookup"><span data-stu-id="40e3e-192">Add a third JavaScript file to hold the common logic.</span></span>
* <span data-ttu-id="40e3e-193">[`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) を使用して、共通のロジックを両方のサービス ワーカー ファイルに読み込む。</span><span class="sxs-lookup"><span data-stu-id="40e3e-193">Use [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) to load the common logic into both service worker files.</span></span>

### <a name="cache-first-fetch-strategy"></a><span data-ttu-id="40e3e-194">キャッシュ優先のフェッチ戦略</span><span class="sxs-lookup"><span data-stu-id="40e3e-194">Cache-first fetch strategy</span></span>

<span data-ttu-id="40e3e-195">組み込みの `service-worker.published.js` サービス ワーカーでは、"*キャッシュ優先*" 戦略を使用して要求が解決されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-195">The built-in `service-worker.published.js` service worker resolves requests using a *cache-first* strategy.</span></span> <span data-ttu-id="40e3e-196">これは、ユーザーがネットワークにアクセスできるかどうか、またはサーバーで使用可能な新しいコンテンツがあるかどうかに関係なく、サービス ワーカーがキャッシュされたコンテンツを返すことを意味します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-196">This means that the service worker prefers to return cached content, regardless of whether the user has network access or newer content is available on the server.</span></span>

<span data-ttu-id="40e3e-197">キャッシュ優先の戦略が重要な理由は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="40e3e-197">The cache-first strategy is valuable because:</span></span>

* <span data-ttu-id="40e3e-198">**信頼性が確保されます。**</span><span class="sxs-lookup"><span data-stu-id="40e3e-198">**It ensures reliability.**</span></span> <span data-ttu-id="40e3e-199">ネットワーク アクセスはブール型の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-199">Network access isn't a boolean state.</span></span> <span data-ttu-id="40e3e-200">ユーザーは単純にオンラインまたはオフラインではありません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-200">A user isn't simply online or offline:</span></span>

  * <span data-ttu-id="40e3e-201">ユーザーのデバイスではオンラインと見なされていても、ネットワークの速度が遅すぎるため、待機するのが現実的ではない場合があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-201">The user's device may assume it's online, but the network might be so slow as to be impractical to wait for.</span></span>
  * <span data-ttu-id="40e3e-202">ネットワークが特定の URL に対して無効な結果を返す場合があります。たとえば、特定の要求を現在ブロックまたはリダイレクトしている固定 WIFI ポータルがある場合などです。</span><span class="sxs-lookup"><span data-stu-id="40e3e-202">The network might return invalid results for certain URLs, such as when there's a captive WIFI portal that's currently blocking or redirecting certain requests.</span></span>
  
  <span data-ttu-id="40e3e-203">このように、ブラウザーの `navigator.onLine` API は信頼できないため、依存しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-203">This is why the browser's `navigator.onLine` API isn't reliable and shouldn't be depended upon.</span></span>

* <span data-ttu-id="40e3e-204">**正確性が保証されます。**</span><span class="sxs-lookup"><span data-stu-id="40e3e-204">**It ensures correctness.**</span></span> <span data-ttu-id="40e3e-205">オフライン リソースのキャッシュを構築する場合、サービス ワーカーではコンテンツ ハッシュを使用して、リソースの完全かつ自己整合スナップショットが一度にフェッチされることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-205">When building a cache of offline resources, the service worker uses content hashing to guarantee it has fetched a complete and self-consistent snapshot of resources at a single instant in time.</span></span> <span data-ttu-id="40e3e-206">このキャッシュはアトミック ユニットとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-206">This cache is then used as an atomic unit.</span></span> <span data-ttu-id="40e3e-207">必要な唯一のバージョンが既にキャッシュされているので、ネットワークに新しいリソースを要求する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-207">There's no point asking the network for newer resources, since the only versions required are the ones already cached.</span></span> <span data-ttu-id="40e3e-208">その他のすべてのバージョンには、不整合と非互換性のリスクがあります (たとえば、一緒にコンパイルされていない .NET アセンブリのバージョンを使用しようとした場合など)。</span><span class="sxs-lookup"><span data-stu-id="40e3e-208">Anything else risks inconsistency and incompatibility (for example, trying to use versions of .NET assemblies that weren't compiled together).</span></span>

### <a name="background-updates"></a><span data-ttu-id="40e3e-209">バックグラウンド更新</span><span class="sxs-lookup"><span data-stu-id="40e3e-209">Background updates</span></span>

<span data-ttu-id="40e3e-210">メンタル モデルとして、オフラインファーストの PWA は、インストール可能なモバイル アプリのように動作すると考えることができます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-210">As a mental model, you can think of an offline-first PWA as behaving like a mobile app that can be installed.</span></span> <span data-ttu-id="40e3e-211">アプリはネットワーク接続に関係なく、すぐに起動しますが、インストールされているアプリ ロジックは、最新バージョンではない可能性がある特定の時点のスナップショットから取得されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-211">The app starts up immediately regardless of network connectivity, but the installed app logic comes from a point-in-time snapshot that might not be the latest version.</span></span>

<span data-ttu-id="40e3e-212">Blazor PWA テンプレートを使用すると、ユーザーがアクセスしてネットワークに接続するたびに、バックグラウンドで自動的に自己更新を試行するアプリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-212">The Blazor PWA template produces apps that automatically try to update themselves in the background whenever the user visits and has a working network connection.</span></span> <span data-ttu-id="40e3e-213">このしくみは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="40e3e-213">The way this works is as follows:</span></span>

* <span data-ttu-id="40e3e-214">コンパイル中に、プロジェクトによって "*サービス ワーカー アセット マニフェスト*" が生成されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-214">During compilation, the project generates a *service worker assets manifest*.</span></span> <span data-ttu-id="40e3e-215">既定では、これは `service-worker-assets.js` と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-215">By default, this is called `service-worker-assets.js`.</span></span> <span data-ttu-id="40e3e-216">マニフェストには、アプリがオフラインで動作するために必要なすべての静的リソース (.NET アセンブリ、JavaScript ファイル、CSS など) が、そのコンテンツ ハッシュと共にリストされています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-216">The manifest lists all the static resources that the app requires to function offline, such as .NET assemblies, JavaScript files, and CSS, including their content hashes.</span></span> <span data-ttu-id="40e3e-217">リソース リストは、キャッシュするリソースを認識できるように、サービス ワーカーによって読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-217">The resource list is loaded by the service worker so that it knows which resources to cache.</span></span>
* <span data-ttu-id="40e3e-218">ユーザーがアプリにアクセスするたびに、ブラウザーによって `service-worker.js` と `service-worker-assets.js` がバックグラウンドで再要求されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-218">Each time the user visits the app, the browser re-requests `service-worker.js` and `service-worker-assets.js` in the background.</span></span> <span data-ttu-id="40e3e-219">ファイルは、インストールされている既存のサービス ワーカーとバイト単位で比較されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-219">The files are compared byte-for-byte with the existing installed service worker.</span></span> <span data-ttu-id="40e3e-220">サーバーがこれらのファイルのいずれかに対して変更されたコンテンツを返す場合、サービス ワーカーはそれ自体の新しいバージョンをインストールしようとします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-220">If the server returns changed content for either of these files, the service worker attempts to install a new version of itself.</span></span>
* <span data-ttu-id="40e3e-221">サービス ワーカーでは、それ自体の新しいバージョンをインストールするときに、オフライン リソース用に新しい別のキャッシュが作成され、そのキャッシュに `service-worker-assets.js` にリストされたリソースの入力が開始されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-221">When installing a new version of itself, the service worker creates a new, separate cache for offline resources and starts populating the cache with resources listed in `service-worker-assets.js`.</span></span> <span data-ttu-id="40e3e-222">このロジックは、`service-worker.published.js` 内の `onInstall` 関数に実装されています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-222">This logic is implemented in the `onInstall` function inside `service-worker.published.js`.</span></span>
* <span data-ttu-id="40e3e-223">すべてのリソースがエラーなしで読み込まれ、すべてのコンテンツ ハッシュが一致すると、プロセスは正常に完了します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-223">The process completes successfully when all of the resources are loaded without error and all content hashes match.</span></span> <span data-ttu-id="40e3e-224">成功した場合、新しいサービス ワーカーは、"*アクティブ化を待機している*" 状態になります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-224">If successful, the new service worker enters a *waiting for activation* state.</span></span> <span data-ttu-id="40e3e-225">ユーザーがアプリを閉じると (アプリのタブやウィンドウが残っていない状態)、新しいサービス ワーカーが "*アクティブ*" になり、後続のアプリへのアクセスに使用されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-225">As soon as the user closes the app (no remaining app tabs or windows), the new service worker becomes *active* and is used for subsequent app visits.</span></span> <span data-ttu-id="40e3e-226">古いサービス ワーカーとそのキャッシュは削除されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-226">The old service worker and its cache are deleted.</span></span>
* <span data-ttu-id="40e3e-227">プロセスが正常に完了しなかった場合、新しいサービス ワーカー インスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-227">If the process doesn't complete successfully, the new service worker instance is discarded.</span></span> <span data-ttu-id="40e3e-228">ユーザーが次にアクセスすると (このとき、要求を完了できるほどクライアントのネットワーク接続が良好であることが望ましい)、更新プロセスがもう一度試行されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-228">The update process is attempted again on the user's next visit, when hopefully the client has a better network connection that can complete the requests.</span></span>

<span data-ttu-id="40e3e-229">サービス ワーカー ロジックを編集して、このプロセスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-229">Customize this process by editing the service worker logic.</span></span> <span data-ttu-id="40e3e-230">上記の動作はいずれも Blazor 固有のものではなく、PWA テンプレート オプションによって提供される既定のエクスペリエンスにすぎません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-230">None of the preceding behavior is specific to Blazor but is merely the default experience provided by the PWA template option.</span></span> <span data-ttu-id="40e3e-231">詳細については、[MDN Web ドキュメント:サービス ワーカー API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-231">For more information, see [MDN web docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).</span></span>

### <a name="how-requests-are-resolved"></a><span data-ttu-id="40e3e-232">要求の解決方法</span><span class="sxs-lookup"><span data-stu-id="40e3e-232">How requests are resolved</span></span>

<span data-ttu-id="40e3e-233">「[キャッシュ優先のフェッチ戦略](#cache-first-fetch-strategy)」セクションで説明したように、既定のサービス ワーカーでは "*キャッシュ優先*" 戦略が使用されます。これは、キャッシュされたコンテンツがあればそれが提供されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-233">As described in the [Cache-first fetch strategy](#cache-first-fetch-strategy) section, the default service worker uses a *cache-first* strategy, meaning that it tries to serve cached content when available.</span></span> <span data-ttu-id="40e3e-234">特定の URL に対してキャッシュされたコンテンツがない場合 (バックエンド API からデータを要求する場合など)、サービス ワーカーは通常のネットワーク要求にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-234">If there is no content cached for a certain URL, for example when requesting data from a backend API, the service worker falls back on a regular network request.</span></span> <span data-ttu-id="40e3e-235">サーバーに到達可能な場合は、ネットワーク要求は成功します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-235">The network request succeeds if the server is reachable.</span></span> <span data-ttu-id="40e3e-236">このロジックは、`service-worker.published.js` 内の `onFetch` 関数内に実装されています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-236">This logic is implemented inside `onFetch` function within `service-worker.published.js`.</span></span>

<span data-ttu-id="40e3e-237">アプリの Razor コンポーネントがバックエンド API からのデータの要求に依存しており、ネットワークが利用できないことが原因で失敗した要求に対してわかりやすいユーザー エクスペリエンスを提供する場合は、アプリのコンポーネント内にロジックを実装します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-237">If the app's Razor components rely on requesting data from backend APIs and you want to provide a friendly user experience for failed requests due to network unavailability, implement logic within the app's components.</span></span> <span data-ttu-id="40e3e-238">たとえば、<xref:System.Net.Http.HttpClient> 要求について `try/catch` を使用します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-238">For example, use `try/catch` around <xref:System.Net.Http.HttpClient> requests.</span></span>

### <a name="support-server-rendered-pages"></a><span data-ttu-id="40e3e-239">サーバーでレンダリングされるページのサポート</span><span class="sxs-lookup"><span data-stu-id="40e3e-239">Support server-rendered pages</span></span>

<span data-ttu-id="40e3e-240">ユーザーが最初に `/counter` などの URL、またはアプリ内の他のディープ リンクに移動すると、どうなるかを考えます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-240">Consider what happens when the user first navigates to a URL such as `/counter` or any other deep link in the app.</span></span> <span data-ttu-id="40e3e-241">このような場合は、`/counter` としてキャッシュされたコンテンツを返すのではなく、ブラウザーでキャッシュされたコンテンツを `/index.html` として読み込み、Blazor WebAssembly アプリを起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-241">In these cases, you don't want to return content cached as `/counter`, but instead need the browser to load the content cached as `/index.html` to start up your Blazor WebAssembly app.</span></span> <span data-ttu-id="40e3e-242">これらの初期要求は、次の対語として、"*ナビゲーション*" 要求と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-242">These initial requests are known as *navigation* requests, as opposed to:</span></span>

* <span data-ttu-id="40e3e-243">`subresource` からは、画像、スタイルシート、またはその他のファイルが要求されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-243">`subresource` requests for images, stylesheets, or other files.</span></span>
* <span data-ttu-id="40e3e-244">`fetch/XHR` からは API データが要求されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-244">`fetch/XHR` requests for API data.</span></span>

<span data-ttu-id="40e3e-245">既定のサービス ワーカーには、ナビゲーション要求用の特殊なケースのロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-245">The default service worker contains special-case logic for navigation requests.</span></span> <span data-ttu-id="40e3e-246">サービス ワーカーは、要求された URL に関係なく、`/index.html` のキャッシュされたコンテンツを返すことで要求を解決します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-246">The service worker resolves the requests by returning the cached content for `/index.html`, regardless of the requested URL.</span></span> <span data-ttu-id="40e3e-247">このロジックは、`service-worker.published.js` 内の `onFetch` 関数に実装されています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-247">This logic is implemented in the `onFetch` function inside `service-worker.published.js`.</span></span>

<span data-ttu-id="40e3e-248">サーバーでレンダリングされた HTML を返す (キャッシュからの `/index.html` を提供するのではなく) 必要がある特定の URL がアプリにある場合は、サービス ワーカーのロジックを編集する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-248">If your app has certain URLs that must return server-rendered HTML, and not serve `/index.html` from the cache, then you need to edit the logic in your service worker.</span></span> <span data-ttu-id="40e3e-249">`/Identity/` を含むすべての URL を、サーバーに対する通常のオンライン専用の要求として処理する必要がある場合は、`service-worker.published.js` `onFetch` ロジックを変更します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-249">If all URLs containing `/Identity/` need to be handled as regular online-only requests to the server, then modify `service-worker.published.js` `onFetch` logic.</span></span> <span data-ttu-id="40e3e-250">次のコードを見つけます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-250">Locate the following code:</span></span>

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

<span data-ttu-id="40e3e-251">コードを次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-251">Change the code to the following:</span></span>

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

<span data-ttu-id="40e3e-252">この変更を行わないと、ネットワーク接続に関係なく、サービス ワーカーによってこのような URL に対する要求がインターセプトされ、`/index.html` を使用してそれらが解決されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-252">If you don't do this, then regardless of network connectivity, the service worker intercepts requests for such URLs and resolves them using `/index.html`.</span></span>

### <a name="control-asset-caching"></a><span data-ttu-id="40e3e-253">アセット キャッシュの制御</span><span class="sxs-lookup"><span data-stu-id="40e3e-253">Control asset caching</span></span>

<span data-ttu-id="40e3e-254">プロジェクトで `ServiceWorkerAssetsManifest` MSBuild プロパティが定義されている場合、Blazor のビルド ツールによって、指定された名前でサービス ワーカー アセット マニフェストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-254">If your project defines the `ServiceWorkerAssetsManifest` MSBuild property, Blazor's build tooling generates a service worker assets manifest with the specified name.</span></span> <span data-ttu-id="40e3e-255">既定の PWA テンプレートでは、次のプロパティを含むプロジェクト ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-255">The default PWA template produces a project file containing the following property:</span></span>

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

<span data-ttu-id="40e3e-256">ファイルは `wwwroot` 出力ディレクトリに配置されるため、ブラウザーでは `/service-worker-assets.js` を要求することによって、このファイルを取得できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-256">The file is placed in the `wwwroot` output directory, so the browser can retrieve this file by requesting `/service-worker-assets.js`.</span></span> <span data-ttu-id="40e3e-257">このファイルの内容を表示するには、テキスト エディターで `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` を開きます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-257">To see the contents of this file, open `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` in a text editor.</span></span> <span data-ttu-id="40e3e-258">ただし、ファイルは各ビルドで再生成されるため、編集しないでください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-258">However, don't edit the file, as it's regenerated on each build.</span></span>

<span data-ttu-id="40e3e-259">既定では、このマニフェストには次の項目がリストされています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-259">By default, this manifest lists:</span></span>

* <span data-ttu-id="40e3e-260">.NET アセンブリや .NET WebAssembly アセンブリ ランタイム ファイルなど、オフラインで機能するために必要な、Blazor 管理対象リソース。</span><span class="sxs-lookup"><span data-stu-id="40e3e-260">Any Blazor-managed resources, such as .NET assemblies and the .NET WebAssembly runtime files required to function offline.</span></span>
* <span data-ttu-id="40e3e-261">アプリの `wwwroot` ディレクトリに公開するためのすべてのリソース (画像、スタイルシート、JavaScript ファイルなど)。外部プロジェクトや NuGet パッケージによって提供される静的な Web アセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-261">All resources for publishing to the app's `wwwroot` directory, such as images, stylesheets, and JavaScript files, including static web assets supplied by external projects and NuGet packages.</span></span>

<span data-ttu-id="40e3e-262">サービス ワーカーによってフェッチおよびキャッシュされるリソースを制御するには、`service-worker.published.js` 内の `onInstall` でロジックを編集します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-262">You can control which of these resources are fetched and cached by the service worker by editing the logic in `onInstall` in `service-worker.published.js`.</span></span> <span data-ttu-id="40e3e-263">既定では、`.html`、`.css`、`.js`、`.wasm` などの一般的な Web ファイル名の拡張子に一致するファイルのほか、Blazor WebAssembly に固有のファイルの種類 (`.dll`、`.pdb`) がサービス ワーカーによってフェッチおよびキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-263">By default, the service worker fetches and caches files matching typical web filename extensions such as `.html`, `.css`, `.js`, and `.wasm`, plus file types specific to Blazor WebAssembly (`.dll`, `.pdb`).</span></span>

<span data-ttu-id="40e3e-264">アプリの `wwwroot` ディレクトリに存在しない追加のリソースを含めるには、次の例に示すように、追加の MSBuild `ItemGroup` エントリを定義します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-264">To include additional resources that aren't present in the app's `wwwroot` directory, define extra MSBuild `ItemGroup` entries, as shown in the following example:</span></span>

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

<span data-ttu-id="40e3e-265">`AssetUrl` メタデータにより、ブラウザーでキャッシュするリソースをフェッチするときに使用されるベース相対 URL が指定されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-265">The `AssetUrl` metadata specifies the base-relative URL that the browser should use when fetching the resource to cache.</span></span> <span data-ttu-id="40e3e-266">これは、ディスク上の元のソース ファイル名に依存しないものにすることができます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-266">This can be independent of its original source file name on disk.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40e3e-267">`ServiceWorkerAssetsManifestItem` を追加しても、ファイルはアプリの `wwwroot` ディレクトリに公開されません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-267">Adding a `ServiceWorkerAssetsManifestItem` doesn't cause the file to be published in the app's `wwwroot` directory.</span></span> <span data-ttu-id="40e3e-268">公開の出力は個別に制御する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-268">The publish output must be controlled separately.</span></span> <span data-ttu-id="40e3e-269">`ServiceWorkerAssetsManifestItem` では、サービス ワーカー アセット マニフェストに追加のエントリが表示されるだけです。</span><span class="sxs-lookup"><span data-stu-id="40e3e-269">The `ServiceWorkerAssetsManifestItem` only causes an additional entry to appear in the service worker assets manifest.</span></span>

## <a name="push-notifications"></a><span data-ttu-id="40e3e-270">プッシュ通知</span><span class="sxs-lookup"><span data-stu-id="40e3e-270">Push notifications</span></span>

<span data-ttu-id="40e3e-271">他の PWA と同様に、Blazor WebAssembly PWA では、バックエンド サーバーからプッシュ通知を受信できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-271">Like any other PWA, a Blazor WebAssembly PWA can receive push notifications from a backend server.</span></span> <span data-ttu-id="40e3e-272">サーバーは、ユーザーがアプリを積極的に使用していない場合でも、いつでもプッシュ通知を送信できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-272">The server can send push notifications at any time, even when the user isn't actively using the app.</span></span> <span data-ttu-id="40e3e-273">たとえば、別のユーザーが関連するアクションを実行したときに、プッシュ通知を送信できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-273">For example, push notifications can be sent when a different user performs a relevant action.</span></span>

<span data-ttu-id="40e3e-274">プッシュ通知を送信するメカニズムは、任意のテクノロジを使用できるバックエンド サーバーによって実装されているため、Blazor WebAssembly から完全に独立しています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-274">The mechanism for sending a push notification is entirely independent of Blazor WebAssembly, since it's implemented by the backend server which can use any technology.</span></span> <span data-ttu-id="40e3e-275">ASP.NET Core サーバーからプッシュ通知を送信する場合は、[Blazing Pizza ワークショップで行ったのと同様の手法を使用する](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)ことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-275">If you want to send push notifications from an ASP.NET Core server, consider [using a technique similar to the approach taken in the Blazing Pizza workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).</span></span>

<span data-ttu-id="40e3e-276">クライアントでプッシュ通知を受信して表示するメカニズムは、サービス ワーカー JavaScript ファイルに実装されているため、Blazor WebAssembly にも依存しません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-276">The mechanism for receiving and displaying a push notification on the client is also independent of Blazor WebAssembly, since it's implemented in the service worker JavaScript file.</span></span> <span data-ttu-id="40e3e-277">例として、[Blazing Pizza ワークショップで使用されている方法](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-277">For an example, see [the approach used in the Blazing Pizza workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).</span></span>

## <a name="caveats-for-offline-pwas"></a><span data-ttu-id="40e3e-278">オフラインの PWA に関する注意事項</span><span class="sxs-lookup"><span data-stu-id="40e3e-278">Caveats for offline PWAs</span></span>

<span data-ttu-id="40e3e-279">すべてのアプリでオフラインでの使用をサポートする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-279">Not all apps should attempt to support offline use.</span></span> <span data-ttu-id="40e3e-280">オフライン サポートは、複雑さが大幅に増すうえに、必要なユース ケースに常に関連しているとは限りません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-280">Offline support adds significant complexity, while not always being relevant for the use cases required.</span></span>

<span data-ttu-id="40e3e-281">オフライン サポートは通常、次の場合にのみ関連性を持ちます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-281">Offline support is usually relevant only:</span></span>

* <span data-ttu-id="40e3e-282">プライマリ データ ストアがブラウザーに対してローカルである場合。</span><span class="sxs-lookup"><span data-stu-id="40e3e-282">If the primary data store is local to the browser.</span></span> <span data-ttu-id="40e3e-283">たとえば、このアプローチは、`localStorage` または [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API) にデータを格納する [IoT](https://en.wikipedia.org/wiki/Internet_of_things) デバイス用の UI があるアプリに関連しています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-283">For example, the approach is relevant in an app with a UI for an [IoT](https://en.wikipedia.org/wiki/Internet_of_things) device that stores data in `localStorage` or [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).</span></span>
* <span data-ttu-id="40e3e-284">ユーザーがデータ内をオフラインで移動できるように、アプリが各ユーザーに関連するバックエンド API データをフェッチしてキャッシュする大量の作業を実行する場合。</span><span class="sxs-lookup"><span data-stu-id="40e3e-284">If the app performs a significant amount of work to fetch and cache the backend API data relevant to each user so that they can navigate through the data offline.</span></span> <span data-ttu-id="40e3e-285">アプリで編集がサポートされている必要がある場合は、変更を追跡し、バックエンドとデータを同期するためのシステムを構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-285">If the app must support editing, a system for tracking changes and synchronizing data with the backend must be built.</span></span>
* <span data-ttu-id="40e3e-286">ネットワークの状態に関係なく、直ちにアプリが読み込まれることを保証することが、目的の場合。</span><span class="sxs-lookup"><span data-stu-id="40e3e-286">If the goal is to guarantee that the app loads immediately regardless of network conditions.</span></span> <span data-ttu-id="40e3e-287">ネットワークが使用できないことが原因で要求が失敗した場合に、要求の進行状況を表示し、適切に動作するため、バックエンド API 要求を中心にした適切なユーザー エクスペリエンスを実装します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-287">Implement a suitable user experience around backend API requests to show the progress of requests and behave gracefully when requests fail due to network unavailability.</span></span>

<span data-ttu-id="40e3e-288">さらに、オフライン対応の PWA では、さまざまな複雑さに対処する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-288">Additionally, offline-capable PWAs must deal with a range of additional complications.</span></span> <span data-ttu-id="40e3e-289">開発者は、以降のセクションの注意点をよく理解しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-289">Developers should carefully familiarize themselves with the caveats in the following sections.</span></span>

### <a name="offline-support-only-when-published"></a><span data-ttu-id="40e3e-290">オフライン サポートは公開された場合のみ</span><span class="sxs-lookup"><span data-stu-id="40e3e-290">Offline support only when published</span></span>

<span data-ttu-id="40e3e-291">開発時には、バックグラウンド更新プロセスを実行せずに、各変更がブラウザーにすぐに反映されることを確認したいことがよくあります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-291">During development you typically want to see each change reflected immediately in the browser without going through a background update process.</span></span> <span data-ttu-id="40e3e-292">そのため、Blazor の PWA テンプレートでは、公開された場合にのみオフライン サポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-292">Therefore, Blazor's PWA template enables offline support only when published.</span></span>

<span data-ttu-id="40e3e-293">オフライン対応アプリをビルドする場合、開発環境でアプリをテストするだけでは不十分です。</span><span class="sxs-lookup"><span data-stu-id="40e3e-293">When building an offline-capable app, it's not enough to test the app in the Development environment.</span></span> <span data-ttu-id="40e3e-294">アプリがさまざまなネットワークの状態にどのように応答するかを理解するには、アプリが公開された状態でテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-294">You must test the app in its published state to understand how it responds to different network conditions.</span></span>

### <a name="update-completion-after-user-navigation-away-from-app"></a><span data-ttu-id="40e3e-295">ユーザーがアプリから移動した後に更新が完了する</span><span class="sxs-lookup"><span data-stu-id="40e3e-295">Update completion after user navigation away from app</span></span>

<span data-ttu-id="40e3e-296">ユーザーがすべてのタブでアプリから移動するまで、更新は完了しません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-296">Updates don't complete until the user has navigated away from the app in all tabs.</span></span> <span data-ttu-id="40e3e-297">「[バックグラウンド更新](#background-updates)」セクションで説明したように、アプリに更新プログラムを配置すると、更新されたサービス ワーカー ファイルがブラウザーによってフェッチされ、更新プロセスが開始されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-297">As explained in the [Background updates](#background-updates) section, after you deploy an update to the app, the browser fetches the updated service worker files to begin the update process.</span></span>

<span data-ttu-id="40e3e-298">多くの開発者は、この更新が完了しても、ユーザーがすべてのタブでアプリケーションから移動しない限り、この更新が有効に **ならない** ことを意外に思うでしょう。</span><span class="sxs-lookup"><span data-stu-id="40e3e-298">What surprises many developers is that, even when this update completes, it does **not** take effect until the user has navigated away in all tabs.</span></span> <span data-ttu-id="40e3e-299">アプリを表示するタブを更新するだけでは、たとえそれがアプリを表示する唯一のタブであっても、十分では **ありません**。</span><span class="sxs-lookup"><span data-stu-id="40e3e-299">It is **not** sufficient to refresh the tab displaying the app, even if it's the only tab displaying the app.</span></span> <span data-ttu-id="40e3e-300">アプリが完全に閉じられるまで、新しいサービス ワーカーは "*アクティブ化の待機中*" 状態のままになります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-300">Until your app is completely closed, the new service worker remains in the *waiting to activate* status.</span></span> <span data-ttu-id="40e3e-301">**これは Blazor に固有のものではなく、標準的な Web プラットフォームの動作です。**</span><span class="sxs-lookup"><span data-stu-id="40e3e-301">**This is not specific to Blazor, but rather is a standard web platform behavior.**</span></span>

<span data-ttu-id="40e3e-302">これは、サービス ワーカーまたはオフラインでキャッシュされたリソースに対する更新をテストしようとしている開発者にとって、共通の問題となります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-302">This commonly troubles developers who are trying to test updates to their service worker or offline cached resources.</span></span> <span data-ttu-id="40e3e-303">ブラウザーの開発者ツールをチェックインすると、次のような内容が表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-303">If you check in the browser's developer tools, you may see something like the following:</span></span>

![Google Chrome の [アプリケーション] タブに、アプリのサービス ワーカーが "アクティブ化の待機中" と表示されます。](progressive-web-app/_static/image7.png)

<span data-ttu-id="40e3e-305">"クライアント" のリスト (アプリを表示しているタブまたはウィンドウ) が空でない限り、ワーカーは待機を続けます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-305">For as long as the list of "clients," which are tabs or windows displaying your app, is nonempty, the worker continues waiting.</span></span> <span data-ttu-id="40e3e-306">サービス ワーカーがこれを行う理由は、整合性を保証するためです。</span><span class="sxs-lookup"><span data-stu-id="40e3e-306">The reason service workers do this is to guarantee consistency.</span></span> <span data-ttu-id="40e3e-307">整合性とは、すべてのリソースが同じアトミック キャッシュからフェッチされることを意味します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-307">Consistency means that all resources are fetched from the same atomic cache.</span></span>

<span data-ttu-id="40e3e-308">変更をテストする際には、前のスクリーンショットに示されているように、"skipWaiting" リンクをクリックし、ページを再度読み込むと便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-308">When testing changes, you may find it convenient to click the "skipWaiting" link as shown in the preceding screenshot, then reload the page.</span></span> <span data-ttu-id="40e3e-309">["待機中" のフェーズをスキップして、更新時にすぐにアクティブ化](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase)するように、サービス ワーカーをコーディングすることで、すべてのユーザーに対してこの動作を自動化できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-309">You can automate this for all users by coding your service worker to [skip the "waiting" phase and immediately activate on update](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase).</span></span> <span data-ttu-id="40e3e-310">待機中のフェーズをスキップすると、リソースが常に同じキャッシュ インスタンスから一貫してフェッチされることを保証できなくなります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-310">If you skip the waiting phase, you're giving up the guarantee that resources are always fetched consistently from the same cache instance.</span></span>

### <a name="users-may-run-any-historical-version-of-the-app"></a><span data-ttu-id="40e3e-311">ユーザーはアプリのあらゆる履歴バージョンを実行する可能性がある</span><span class="sxs-lookup"><span data-stu-id="40e3e-311">Users may run any historical version of the app</span></span>

<span data-ttu-id="40e3e-312">Web 開発者は、ユーザーが、配置された最新バージョンの Web アプリを実行するものと思いがちです。これは、従来の Web 配布モデルでは自然なことでした。</span><span class="sxs-lookup"><span data-stu-id="40e3e-312">Web developers habitually expect that users only run the latest deployed version of their web app, since that's normal within the traditional web distribution model.</span></span> <span data-ttu-id="40e3e-313">しかし、オフラインファーストの PWA は、ユーザーが必ずしも最新バージョンを実行しているとは限らないネイティブ モバイル アプリに似ています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-313">However, an offline-first PWA is more akin to a native mobile app, where users aren't necessarily running the latest version.</span></span>

<span data-ttu-id="40e3e-314">「[バックグラウンド更新](#background-updates)」セクションで説明したように、アプリに更新プログラムを配置すると、**既存の各ユーザーは、少なくともあと 1 回のアクセスで以前のバージョンを使用し続けます**。これは、更新がバックグラウンドで行われ、ユーザーがその後アプリから移動するまでアクティブ化されないからです。</span><span class="sxs-lookup"><span data-stu-id="40e3e-314">As explained in the [Background updates](#background-updates) section, after you deploy an update to your app, **each existing user continues to use a previous version for at least one further visit** because the update occurs in the background and isn't activated until the user thereafter navigates away.</span></span> <span data-ttu-id="40e3e-315">また、以前に使用されていたバージョンが、以前に配置したものと必ずしも同じであるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="40e3e-315">Plus, the previous version being used isn't necessarily the previous one you deployed.</span></span> <span data-ttu-id="40e3e-316">以前のバージョンは、ユーザーが最後に更新を完了した日時に応じて、"*あらゆる*" 履歴バージョンが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-316">The previous version can be *any* historical version, depending on when the user last completed an update.</span></span>

<span data-ttu-id="40e3e-317">これは、アプリのフロントエンドとバックエンドの部分が API 要求のスキーマに関する合意を必要とする場合に問題になることがあります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-317">This can be an issue if the frontend and backend parts of your app require agreement about the schema for API requests.</span></span> <span data-ttu-id="40e3e-318">すべてのユーザーがアップグレードしたことが確認できるまで、下位互換性のない API スキーマの変更を配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-318">You must not deploy backward-incompatible API schema changes until you can be sure that all users have upgraded.</span></span> <span data-ttu-id="40e3e-319">または、ユーザーが互換性のない古いバージョンのアプリを使用できないようにします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-319">Alternatively, block users from using incompatible older versions of the app.</span></span> <span data-ttu-id="40e3e-320">このシナリオ要件は、ネイティブ モバイル アプリの場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="40e3e-320">This scenario requirement is the same as for native mobile apps.</span></span> <span data-ttu-id="40e3e-321">サーバー API に重大な変更を配置すると、まだ更新していないユーザーに対してクライアント アプリが中断されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-321">If you deploy a breaking change in server APIs, the client app is broken for users who haven't yet updated.</span></span>

<span data-ttu-id="40e3e-322">可能であれば、バックエンド API に重大な変更を配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-322">If possible, don't deploy breaking changes to your backend APIs.</span></span> <span data-ttu-id="40e3e-323">これを行う必要がある場合は、[ServiceWorkerRegistration などの標準のサービス ワーカー API](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) を使用して、アプリが最新であるかどうかを判断し、最新でない場合は使用できないようにすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-323">If you must do so, consider using [standard Service Worker APIs such as ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) to determine whether the app is up-to-date, and if not, to prevent usage.</span></span>

### <a name="interference-with-server-rendered-pages"></a><span data-ttu-id="40e3e-324">サーバーでレンダリングされるページによる干渉</span><span class="sxs-lookup"><span data-stu-id="40e3e-324">Interference with server-rendered pages</span></span>

<span data-ttu-id="40e3e-325">「[サーバーでレンダリングされるページのサポート](#support-server-rendered-pages)」セクションで説明したように、すべてのナビゲーション要求に対して `/index.html` コンテンツを返すサービス ワーカーの動作をバイパスする場合は、サービス ワーカーのロジックを編集します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-325">As described in the [Support server-rendered pages](#support-server-rendered-pages) section, if you want to bypass the service worker's behavior of returning `/index.html` contents for all navigation requests, edit the logic in your service worker.</span></span>

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a><span data-ttu-id="40e3e-326">サービス ワーカー アセット マニフェストのすべてのコンテンツが既定でキャッシュされる</span><span class="sxs-lookup"><span data-stu-id="40e3e-326">All service worker asset manifest contents are cached by default</span></span>

<span data-ttu-id="40e3e-327">「[アセット キャッシュの制御](#control-asset-caching)」セクションで説明したように、ファイル `service-worker-assets.js` はビルド時に生成され、サービス ワーカーでフェッチおよびキャッシュする必要があるすべてのアセットがリストされます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-327">As described in the [Control asset caching](#control-asset-caching) section, the file `service-worker-assets.js` is generated during build and lists all assets the service worker should fetch and cache.</span></span>

<span data-ttu-id="40e3e-328">既定では、このリストには `wwwroot` に出力されるすべての項目 (外部のパッケージとプロジェクトによって提供されるコンテンツを含む) が含まれるので、コンテンツの数が多くなりすぎないように注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-328">Since this list by default includes everything emitted to `wwwroot`, including content supplied by external packages and projects, you must be careful not to put too much content there.</span></span> <span data-ttu-id="40e3e-329">`wwwroot` ディレクトリに数百万のイメージが含まれている場合、サービス ワーカーはそれらすべてをフェッチおよびキャッシュしようとするため、帯域幅が過剰に消費されて、正常に完了しない可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-329">If the `wwwroot` directory contains millions of images, the service worker tries to fetch and cache them all, consuming excessive bandwidth and most likely not completing successfully.</span></span>

<span data-ttu-id="40e3e-330">`service-worker.published.js` 内の `onInstall` 関数を編集することで、マニフェストのコンテンツのどのサブセットをフェッチおよびキャッシュするかを制御する任意のロジックを実装します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-330">Implement arbitrary logic to control which subset of the manifest's contents should be fetched and cached by editing the `onInstall` function in `service-worker.published.js`.</span></span>

### <a name="interaction-with-authentication"></a><span data-ttu-id="40e3e-331">認証との相互作用</span><span class="sxs-lookup"><span data-stu-id="40e3e-331">Interaction with authentication</span></span>

<span data-ttu-id="40e3e-332">PWA テンプレートは、認証と組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-332">The PWA template can be used in conjunction with authentication.</span></span> <span data-ttu-id="40e3e-333">オフライン対応 PWA では、ユーザーが最初のネットワーク接続を行うときに認証をサポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-333">An offline-capable PWA can also support authentication when the user has initial network connectivity.</span></span>

<span data-ttu-id="40e3e-334">ユーザーがネットワークに接続していない場合は、アクセス トークンを認証したり取得したりすることはできません</span><span class="sxs-lookup"><span data-stu-id="40e3e-334">When a user doesn't have network connectivity, they can't authenticate or obtain access tokens.</span></span> <span data-ttu-id="40e3e-335">既定では、ネットワーク アクセスなしでログイン ページにアクセスしようとすると、"ネットワーク エラー" メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-335">By default, attempting to visit the login page without network access results in a "network error" message.</span></span> <span data-ttu-id="40e3e-336">ユーザーを認証したりアクセス トークンを取得したりすることなく、ユーザーがオフラインで便利なタスクを実行できるようにする UI フローを設計する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-336">You must design a UI flow that allows the user perform useful tasks while offline without attempting to authenticate the user or obtain access tokens.</span></span> <span data-ttu-id="40e3e-337">または、ネットワークを使用できないときに適切に失敗するようにアプリを設計することもできます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-337">Alternatively, you can design the app to gracefully fail when the network isn't available.</span></span> <span data-ttu-id="40e3e-338">このようなシナリオを処理するようにアプリを設計できない場合は、オフライン サポートを有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-338">If the app can't be designed to handle these scenarios, you might not want to enable offline support.</span></span>

<span data-ttu-id="40e3e-339">オンラインとオフラインで使用するように設計されたアプリが再びオンラインになった場合:</span><span class="sxs-lookup"><span data-stu-id="40e3e-339">When an app that's designed for online and offline use is online again:</span></span>

* <span data-ttu-id="40e3e-340">場合によっては、アプリで新しいアクセス トークンをプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-340">The app might need to provision a new access token.</span></span>
* <span data-ttu-id="40e3e-341">アプリでは、別のユーザーがサービスにサインインしているかどうかを検出して、オフライン中に行われた操作をユーザーのアカウントに適用できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="40e3e-341">The app must detect if a different user is signed into the service so that it can apply operations to the user's account that were made while they were offline.</span></span>

<span data-ttu-id="40e3e-342">認証と対話するオフラインの PWA アプリを作成するには:</span><span class="sxs-lookup"><span data-stu-id="40e3e-342">To create an offline PWA app that interacts with authentication:</span></span>

* <span data-ttu-id="40e3e-343"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> を、最後にサインインしたユーザーを格納し、アプリがオフラインのときに格納されたユーザーを使用するファクトリに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="40e3e-343">Replace the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> with a factory that stores the last signed-in user and uses the stored user when the app is offline.</span></span>
* <span data-ttu-id="40e3e-344">アプリがオフラインのときに操作をキューに格納し、アプリがオンラインに戻ったときにそれらを適用します。</span><span class="sxs-lookup"><span data-stu-id="40e3e-344">Queue operations while the app is offline and apply them when the app returns online.</span></span>
* <span data-ttu-id="40e3e-345">サインアウト中に、格納されているユーザーをクリアします。</span><span class="sxs-lookup"><span data-stu-id="40e3e-345">During sign out, clear the stored user.</span></span>

<span data-ttu-id="40e3e-346">[`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) サンプル アプリでは、前述の方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="40e3e-346">The [`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) sample app demonstrates the preceding approaches.</span></span> <span data-ttu-id="40e3e-347">アプリの次の部分を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40e3e-347">See the following parts of the app:</span></span>

* <span data-ttu-id="40e3e-348">`OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)</span><span class="sxs-lookup"><span data-stu-id="40e3e-348">`OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)</span></span>
* <span data-ttu-id="40e3e-349">`LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)</span><span class="sxs-lookup"><span data-stu-id="40e3e-349">`LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)</span></span>
* <span data-ttu-id="40e3e-350">`LoginStatus` コンポーネント (`Client/Shared/LoginStatus.razor`)</span><span class="sxs-lookup"><span data-stu-id="40e3e-350">`LoginStatus` component (`Client/Shared/LoginStatus.razor`)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40e3e-351">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="40e3e-351">Additional resources</span></span>

* [<span data-ttu-id="40e3e-352">整合性 PowerShell スクリプトのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="40e3e-352">Troubleshoot integrity PowerShell script</span></span>](xref:blazor/host-and-deploy/webassembly#troubleshoot-integrity-powershell-script)
* [<span data-ttu-id="40e3e-353">認証のための SignalR のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="40e3e-353">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)

---
title: ASP.NET Core Blazor WebAssembly をデバッグする
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 9214fa10a2bf7d53a4cb12263a3fa69bded84b29
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536234"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="f3db5-103">ASP.NET Core Blazor WebAssembly をデバッグする</span><span class="sxs-lookup"><span data-stu-id="f3db5-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="f3db5-104">Blazor WebAssembly アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-104">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="f3db5-105">次の統合開発環境 (IDE) を使用して、アプリをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-105">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="f3db5-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3db5-106">Visual Studio</span></span>
* <span data-ttu-id="f3db5-107">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f3db5-107">Visual Studio for Mac</span></span>
* <span data-ttu-id="f3db5-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3db5-108">Visual Studio Code</span></span>

<span data-ttu-id="f3db5-109">利用可能なシナリオには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-109">Available scenarios include:</span></span>

* <span data-ttu-id="f3db5-110">ブレークポイントの設定と削除を行う。</span><span class="sxs-lookup"><span data-stu-id="f3db5-110">Set and remove breakpoints.</span></span>
* <span data-ttu-id="f3db5-111">IDE でデバッグ サポートを使用してアプリを実行する。</span><span class="sxs-lookup"><span data-stu-id="f3db5-111">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="f3db5-112">コードを 1 ステップずつ実行する。</span><span class="sxs-lookup"><span data-stu-id="f3db5-112">Single-step through the code.</span></span>
* <span data-ttu-id="f3db5-113">IDE でキーボード ショートカットを使用してコード実行を再開する。</span><span class="sxs-lookup"><span data-stu-id="f3db5-113">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="f3db5-114">*[ローカル]* ウィンドウで、ローカル変数の値を観察する。</span><span class="sxs-lookup"><span data-stu-id="f3db5-114">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="f3db5-115">JavaScript と .NET の間の呼び出しチェーンなど、呼び出し履歴を確認する。</span><span class="sxs-lookup"><span data-stu-id="f3db5-115">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="f3db5-116">現時点では、次の操作を行うことは *できません*。</span><span class="sxs-lookup"><span data-stu-id="f3db5-116">For now, you *can't*:</span></span>

* <span data-ttu-id="f3db5-117">未処理の例外の発生時に中断する。</span><span class="sxs-lookup"><span data-stu-id="f3db5-117">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="f3db5-118">デバッグ プロキシが実行される前に、アプリの起動中にブレークポイントにヒットします。</span><span class="sxs-lookup"><span data-stu-id="f3db5-118">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="f3db5-119">これには、`Program.Main` (`Program.cs`) のブレークポイントと、アプリから要求された最初のページによって読み込まれるコンポーネントの [`OnInitialized{Async}` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)のブレークポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-119">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="f3db5-120">ローカル以外のシナリオ (たとえば、[Linux 用 Windows サブシステム (WSL)](/windows/wsl/) や [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)) でデバッグします。</span><span class="sxs-lookup"><span data-stu-id="f3db5-120">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>
* <span data-ttu-id="f3db5-121">[`dotnet watch run`](xref:tutorials/dotnet-watch) を使用してアプリを実行するなどして、デバッグ中にホステッド Blazor ソリューションのバックエンド `*Server*` アプリを自動的に再構築します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-121">Automatically rebuild the backend `*Server*` app of a hosted Blazor solution during debugging, for example by running the app with [`dotnet watch run`](xref:tutorials/dotnet-watch).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f3db5-122">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f3db5-122">Prerequisites</span></span>

<span data-ttu-id="f3db5-123">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="f3db5-124">Google Chrome (バージョン 70 以降) (既定値)</span><span class="sxs-lookup"><span data-stu-id="f3db5-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="f3db5-125">Microsoft Edge (バージョン 80 以降)</span><span class="sxs-lookup"><span data-stu-id="f3db5-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="f3db5-126">ファイアウォールまたはプロキシでデバッグ プロキシ (`NodeJS` プロセス) との通信がブロックされていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="f3db5-127">詳細については、「[ファイアウォールの構成](#firewall-configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="f3db5-128">Visual Studio for Mac のバージョン 8.8 (ビルド 1532) 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-128">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="f3db5-129">Visual Studio for Mac の最新リリースをインストールします。それには、 **[Visual Studio for Mac をダウンロードする]** ボタンを選択します ([Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/))。</span><span class="sxs-lookup"><span data-stu-id="f3db5-129">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="f3db5-130">Visual Studio 内から "*プレビュー*" チャネルを選択します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-130">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="f3db5-131">詳細については、「[Visual Studio for Mac のプレビュー バージョンをインストールする](/visualstudio/mac/install-preview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-131">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="f3db5-132">現在、macOS での Apple Safari はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f3db5-132">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="f3db5-133">デバッグの有効化</span><span class="sxs-lookup"><span data-stu-id="f3db5-133">Enable debugging</span></span>

<span data-ttu-id="f3db5-134">既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの `launchSettings.json` ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-134">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="f3db5-135">更新されると、`launchSettings.json` ファイルは次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-135">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="f3db5-136">`inspectUri` プロパティ:</span><span class="sxs-lookup"><span data-stu-id="f3db5-136">The `inspectUri` property:</span></span>

* <span data-ttu-id="f3db5-137">アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。</span><span class="sxs-lookup"><span data-stu-id="f3db5-137">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="f3db5-138">スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-138">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="f3db5-139">起動したブラウザー (`browserInspectUri`) の Websocket プロトコル (`wsProtocol`)、ホスト (`url.hostname`)、ポート (`url.port`)、およびインスペクター URI のプレースホルダー値は、フレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-139">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3db5-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3db5-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3db5-141">Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="f3db5-141">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="f3db5-142">新しい ASP.NET Core でホストされる Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-142">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="f3db5-143"><kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-143">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="f3db5-144">**デバッグなしの開始** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f3db5-144">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="f3db5-145">アプリがデバッグ構成で実行されている場合、デバッグのオーバーヘッドによって常にパフォーマンスがわずかに低下します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-145">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="f3db5-146">"`*Client*`" アプリで、`Pages/Counter.razor` の `currentCount++;` 行にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-146">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="f3db5-147">ブラウザーで `Counter` ページに移動し、 **[Click me]** ボタンを選択して、ブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-147">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="f3db5-148">Visual Studio の **[ローカル]** ウィンドウで、`currentCount` フィールドの値を調べます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-148">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="f3db5-149"><kbd>F5</kbd> キーを押して、実行を続行します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-149">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="f3db5-150">Blazor WebAssembly アプリをデバッグしている間に、サーバー コードをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-150">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="f3db5-151">`Pages/FetchData.razor` ページの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-151">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="f3db5-152">`Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-152">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="f3db5-153">`Fetch Data` ページに移動し、サーバーに HTTP 要求を発行する直前の、`FetchData` コンポーネント内の最初のブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-153">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="f3db5-154"><kbd>F5</kbd> キーを押して実行を続け、サーバーの `WeatherForecastController` 内のブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-154">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="f3db5-155"><kbd>F5</kbd> キーをもう一度押して実行を続け、天気予報テーブルがブラウザーにレンダリングされることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="f3db5-156">デバッグ プロキシが実行状態になる前のアプリの起動中には、ブレークポイントで停止することは **ありません**。</span><span class="sxs-lookup"><span data-stu-id="f3db5-156">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="f3db5-157">これには、`Program.Main` (`Program.cs`) のブレークポイントと、アプリから要求された最初のページによって読み込まれるコンポーネントの [`OnInitialized{Async}` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)のブレークポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-157">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="f3db5-158">アプリが `/` 以外の[アプリ ベース パス](xref:blazor/host-and-deploy/index#app-base-path)でホストされている場合、`Properties/launchSettings.json` で次のプロパティを更新し、アプリのベース パスを反映させます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-158">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="f3db5-159">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="f3db5-159">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="f3db5-160">各プロファイルの `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="f3db5-160">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="f3db5-161">前の設定のプレースホルダー:</span><span class="sxs-lookup"><span data-stu-id="f3db5-161">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="f3db5-162">`{INSECURE PORT}`:セキュリティで保護されていないポート。</span><span class="sxs-lookup"><span data-stu-id="f3db5-162">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="f3db5-163">既定では、ランダムな値が提供されますが、カスタム ポートが許可されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="f3db5-164">`{APP BASE PATH}`:アプリのベース パス。</span><span class="sxs-lookup"><span data-stu-id="f3db5-164">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="f3db5-165">`{SECURE PORT}`:セキュリティで保護されているポート。</span><span class="sxs-lookup"><span data-stu-id="f3db5-165">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="f3db5-166">既定では、ランダムな値が提供されますが、カスタム ポートが許可されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-166">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="f3db5-167">`{PROFILE 1, 2, ... N}`:起動設定プロファイル。</span><span class="sxs-lookup"><span data-stu-id="f3db5-167">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="f3db5-168">通常、既定では、1 つのアプリから複数のプロファイルが指定されます (たとえば、IIS Express 用のプロファイルと、Kestrel サーバーで使用されるプロジェクト プロファイル)。</span><span class="sxs-lookup"><span data-stu-id="f3db5-168">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="f3db5-169">次の例では、アプリは `/OAT` でホストされており、アプリ ベース パスは `<base href="/OAT/">` として `wwwroot/index.html` に構成されています。</span><span class="sxs-lookup"><span data-stu-id="f3db5-169">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="f3db5-170">Blazor WebAssembly アプリにカスタム アプリ ベース パスを使用する方法については、「<xref:blazor/host-and-deploy/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-170">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3db5-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3db5-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="f3db5-172">スタンドアロン Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="f3db5-172">Debug standalone Blazor WebAssembly</span></span></h2>

<span data-ttu-id="f3db5-173">`.vscode` フォルダー内の VS Code 資産の構成の詳細については、「<xref:blazor/tooling>」の **Linux** オペレーティング システムのガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-173">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="f3db5-174">VS Code でスタンドアロンの Blazor WebAssembly アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-174">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="f3db5-175">デバッグを有効にするために追加の設定が必要であることを示す通知が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-175">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="f3db5-176">Blazor WebAssembly アプリケーションをデバッグするには、追加のセットアップが必要です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-176">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="f3db5-177">通知を受信した場合:</span><span class="sxs-lookup"><span data-stu-id="f3db5-177">If you receive the notification:</span></span>

   * <span data-ttu-id="f3db5-178">最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-178">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="f3db5-179">インストールされている拡張機能を確認するには、メニュー バーから **[表示]**  >  **[拡張機能]** を開くか、**アクティビティ** サイド バーにある **拡張機能** アイコンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-179">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="f3db5-180">JavaScript のプレビュー デバッグが有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-180">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="f3db5-181">メニュー バーから設定を開きます ( **[ファイル]**  >  **[ユーザー設定]**  >  **[設定]** )。</span><span class="sxs-lookup"><span data-stu-id="f3db5-181">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="f3db5-182">キーワード「`debug preview`」を使用して検索します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-182">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="f3db5-183">検索結果で、 **[デバッグ] > [JavaScript:Use Preview]\(JavaScript: 使用プレビュー\)** のチェック ボックスがオンになっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-183">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="f3db5-184">プレビュー デバッグを有効にするオプションがない場合、VS Code の最新版にアップグレードするか、[JavaScript デバッガー拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code バージョン 1.46 以前) をインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-184">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="f3db5-185">ウィンドウを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-185">Reload the window.</span></span>

1. <span data-ttu-id="f3db5-186"><kbd>F5</kbd> キーボード ショートカットまたはメニュー項目を使用してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-186">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="f3db5-187">**デバッグなしの開始** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f3db5-187">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="f3db5-188">アプリがデバッグ構成で実行されている場合、デバッグのオーバーヘッドによって常にパフォーマンスがわずかに低下します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-188">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="f3db5-189">プロンプトが表示されたら、 **[Blazor WebAssembly Debug]** オプションを選択してデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-189">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="f3db5-190">スタンドアロン アプリが起動され、デバッグ ブラウザーが開きます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-190">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="f3db5-191">"`*Client*`" アプリで、`Pages/Counter.razor` の `currentCount++;` 行にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-191">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="f3db5-192">ブラウザーで `Counter` ページに移動し、 **[Click me]** ボタンを選択して、ブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-192">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="f3db5-193">デバッグ プロキシが実行状態になる前のアプリの起動中には、ブレークポイントで停止することは **ありません**。</span><span class="sxs-lookup"><span data-stu-id="f3db5-193">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="f3db5-194">これには、`Program.Main` (`Program.cs`) のブレークポイントと、アプリから要求された最初のページによって読み込まれるコンポーネントの [`OnInitialized{Async}` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)のブレークポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-194">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="f3db5-195">ホストされた Blazor WebAssembly のデバッグ</span><span class="sxs-lookup"><span data-stu-id="f3db5-195">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="f3db5-196">VS Code で、ホストされた Blazor WebAssembly アプリのソリューション フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-196">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="f3db5-197">プロジェクトの起動構成が設定されていない場合は、次の通知が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-197">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="f3db5-198">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-198">Select **Yes**.</span></span>

   > <span data-ttu-id="f3db5-199">Required assets to build and debug are missing from '{APPLICATION NAME}'. (ビルドとデバッグに必要な資産が、'<アプリケーション名>' にありません。)</span><span class="sxs-lookup"><span data-stu-id="f3db5-199">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="f3db5-200">追加しますか?</span><span class="sxs-lookup"><span data-stu-id="f3db5-200">Add them?</span></span>

   <span data-ttu-id="f3db5-201">`.vscode` フォルダー内の VS Code 資産の構成の詳細については、「<xref:blazor/tooling>」の **Linux** オペレーティング システムのガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-201">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="f3db5-202">ウィンドウの上部にあるコマンド パレットで、ホストされているソリューション内の *Server* プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-202">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="f3db5-203">`launch.json` ファイルが、デバッガーを起動するための起動構成を使用して生成されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-203">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="f3db5-204">既存のデバッグセッションにアタッチする</span><span class="sxs-lookup"><span data-stu-id="f3db5-204">Attach to an existing debugging session</span></span>

<span data-ttu-id="f3db5-205">実行中の Blazor アプリにアタッチするには、次のように構成された `launch.json` ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-205">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="f3db5-206">デバッグ セッションへのアタッチは、スタンドアロン アプリでのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f3db5-206">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="f3db5-207">フルスタック デバッグを使用するには、VS Code からアプリを起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-207">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="f3db5-208">起動構成のオプション</span><span class="sxs-lookup"><span data-stu-id="f3db5-208">Launch configuration options</span></span>

<span data-ttu-id="f3db5-209">`blazorwasm` デバッグの種類では、次の起動構成オプションがサポートされています (`.vscode/launch.json`)。</span><span class="sxs-lookup"><span data-stu-id="f3db5-209">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="f3db5-210">オプション</span><span class="sxs-lookup"><span data-stu-id="f3db5-210">Option</span></span>    | <span data-ttu-id="f3db5-211">説明</span><span class="sxs-lookup"><span data-stu-id="f3db5-211">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="f3db5-212">`launch` を使用して、Blazor WebAssembly アプリにデバッグ セッションを起動してアタッチするか、`attach` を使用して既に実行中のアプリにデバッグ セッションをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="f3db5-212">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="f3db5-213">デバッグ時にブラウザーで開く URL。</span><span class="sxs-lookup"><span data-stu-id="f3db5-213">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="f3db5-214">既定値は `https://localhost:5001` です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-214">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="f3db5-215">デバッグ セッション用に起動するブラウザー。</span><span class="sxs-lookup"><span data-stu-id="f3db5-215">The browser to launch for the debugging session.</span></span> <span data-ttu-id="f3db5-216">`edge` または `chrome` に設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-216">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="f3db5-217">既定値は `chrome` です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-217">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="f3db5-218">JS デバッガーからログを生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-218">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="f3db5-219">ログを生成するには `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-219">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="f3db5-220">ホストされている Blazor WebAssembly アプリを起動およびデバッグする場合は、`true` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-220">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="f3db5-221">Web サーバーの絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-221">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="f3db5-222">アプリをサブルートから提供する場合は設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-222">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="f3db5-223">デバッグ セッションがアタッチされるのを待機するミリ秒単位の時間。</span><span class="sxs-lookup"><span data-stu-id="f3db5-223">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="f3db5-224">既定値は 30,000 ミリ秒 (30 秒) です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-224">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="f3db5-225">ホストされたアプリのサーバーを実行する実行可能ファイルへの参照。</span><span class="sxs-lookup"><span data-stu-id="f3db5-225">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="f3db5-226">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-226">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="f3db5-227">アプリを起動する作業ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="f3db5-227">The working directory to launch the app under.</span></span> <span data-ttu-id="f3db5-228">`hosted` が `true` の場合に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-228">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="f3db5-229">起動されたプロセスに提供する環境変数。</span><span class="sxs-lookup"><span data-stu-id="f3db5-229">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="f3db5-230">`hosted` が `true`に設定されている場合にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-230">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="f3db5-231">起動構成の例</span><span class="sxs-lookup"><span data-stu-id="f3db5-231">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="f3db5-232">スタンドアロン Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="f3db5-232">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="f3db5-233">指定された URL で実行中のアプリにアタッチする</span><span class="sxs-lookup"><span data-stu-id="f3db5-233">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-blazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="f3db5-234">Microsoft Edge でホストされている Blazor WebAssembly アプリを起動してデバッグする</span><span class="sxs-lookup"><span data-stu-id="f3db5-234">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="f3db5-235">ブラウザーの構成の既定値は Google Chrome です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-235">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="f3db5-236">デバッグに Microsoft Edge を使用する場合は、`browser` を `edge` に設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-236">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="f3db5-237">Google Chrome を使用するには、`browser` オプションを設定しないか、オプションの値を `chrome` に設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-237">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="f3db5-238">前の例では、`MyHostedApp.Server.dll` は *Server* アプリのアセンブリです。</span><span class="sxs-lookup"><span data-stu-id="f3db5-238">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="f3db5-239">`.vscode` フォルダーは、`Client`、`Server`、および `Shared` フォルダーの隣にあるソリューションのフォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-239">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3db5-240">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f3db5-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f3db5-241">Visual Studio for Mac で Blazor WebAssembly アプリをデバッグするには:</span><span class="sxs-lookup"><span data-stu-id="f3db5-241">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="f3db5-242">新しい ASP.NET Core でホストされる Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-242">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="f3db5-243"><kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> キーを押して、デバッガーでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-243">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="f3db5-244">**デバッグなしの開始** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f3db5-244">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="f3db5-245">アプリがデバッグ構成で実行されている場合、デバッグのオーバーヘッドによって常にパフォーマンスがわずかに低下します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-245">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="f3db5-246">デバッグ セッション用のブラウザーとしては、Google Chrome または Microsoft Edge を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-246">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="f3db5-247">"`*Client*`" アプリで、`Pages/Counter.razor` の `currentCount++;` 行にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-247">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="f3db5-248">ブラウザーで `Counter` ページに移動し、 **[Click me]** ボタンを選択して、ブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-248">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="f3db5-249">Visual Studio の **[ローカル]** ウィンドウで、`currentCount` フィールドの値を調べます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-249">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="f3db5-250"><kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> キーを押して、実行を続けます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-250">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="f3db5-251">Blazor WebAssembly アプリをデバッグしている間に、サーバー コードをデバッグすることもできます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-251">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="f3db5-252">`Pages/FetchData.razor` ページの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-252">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="f3db5-253">`Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-253">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="f3db5-254">`Fetch Data` ページに移動し、サーバーに HTTP 要求を発行する直前の、`FetchData` コンポーネント内の最初のブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-254">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="f3db5-255"><kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> キーを押して実行を続け、サーバーの `WeatherForecastController` 内のブレークポイントで停止させます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-255">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="f3db5-256"><kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> キーをもう一度押して実行を続け、天気予報テーブルがブラウザーにレンダリングされることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-256">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="f3db5-257">デバッグ プロキシが実行状態になる前のアプリの起動中には、ブレークポイントで停止することは **ありません**。</span><span class="sxs-lookup"><span data-stu-id="f3db5-257">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="f3db5-258">これには、`Program.Main` (`Program.cs`) のブレークポイントと、アプリから要求された最初のページによって読み込まれるコンポーネントの [`OnInitialized{Async}` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)のブレークポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-258">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="f3db5-259">詳細については、「[Visual Studio for Mac を使用したデバッグ](/visualstudio/mac/debugging)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-259">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="f3db5-260">ブラウザーでデバッグする</span><span class="sxs-lookup"><span data-stu-id="f3db5-260">Debug in the browser</span></span>

<span data-ttu-id="f3db5-261">*このセクションのガイダンスは、Windows で実行されている Google Chrome と Microsoft Edge に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="f3db5-261">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="f3db5-262">開発環境でアプリのデバッグ ビルドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-262">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="f3db5-263">ブラウザーを起動し、アプリの URL (`https://localhost:5001` など) に移動します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-263">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="f3db5-264">ブラウザーで <kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd> キーを押すことにより、リモート デバッグの開始を試みます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-264">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="f3db5-265">ブラウザーはリモート デバッグが有効で実行されている必要があります。これは既定ではありません。</span><span class="sxs-lookup"><span data-stu-id="f3db5-265">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="f3db5-266">リモート デバッグが無効になっている場合、"**デバッグ可能なブラウザー タブが見つからない**" というエラー ページと、デバッグ ポートを開いた状態でブラウザーを起動する手順がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-266">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="f3db5-267">ブラウザーの指示に従って、新しいブラウザー ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-267">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="f3db5-268">前のブラウザー ウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-268">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="f3db5-269">リモート デバッグが有効な状態でブラウザーが実行されていると、前のステップのデバッグ キーボード ショートカットにより、新しいデバッガー タブが開きます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-269">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="f3db5-270">少しすると、 **[ソース]** タブに、`file://` ノード内にあるアプリの .NET アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-270">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="f3db5-271">コンポーネント コード (`.razor` ファイル) と C# コード ファイル (`.cs`) で、設定したブレークポイントがコードの実行時にヒットします。</span><span class="sxs-lookup"><span data-stu-id="f3db5-271">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="f3db5-272">ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-272">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="f3db5-273">Blazor は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。</span><span class="sxs-lookup"><span data-stu-id="f3db5-273">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="f3db5-274">デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-274">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="f3db5-275">プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="f3db5-275">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="f3db5-276">ブラウザー ソース マップ</span><span class="sxs-lookup"><span data-stu-id="f3db5-276">Browser source maps</span></span>

<span data-ttu-id="f3db5-277">ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-277">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="f3db5-278">ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。</span><span class="sxs-lookup"><span data-stu-id="f3db5-278">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="f3db5-279">その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。</span><span class="sxs-lookup"><span data-stu-id="f3db5-279">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="f3db5-280">ファイアウォールの構成</span><span class="sxs-lookup"><span data-stu-id="f3db5-280">Firewall configuration</span></span>

<span data-ttu-id="f3db5-281">ファイアウォールでデバッグ プロキシとの通信がブロックされている場合は、ブラウザーと `NodeJS` プロセス間の通信を許可するファイアウォールの例外規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-281">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="f3db5-282">セキュリティの脆弱性が発生しないようにするには、ファイアウォールの構成を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-282">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="f3db5-283">セキュリティ ガイダンスを慎重に適用し、セキュリティに関するベスト プラクティスに従い、ファイアウォールの製造元から発行された警告を順守します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-283">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="f3db5-284">`NodeJS` プロセスとのオープンな通信を許可する:</span><span class="sxs-lookup"><span data-stu-id="f3db5-284">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="f3db5-285">ファイアウォールの機能と構成に応じて、Node サーバーが開いて接続されます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-285">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="f3db5-286">ネットワークによっては危険な場合があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-286">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="f3db5-287">**開発者のマシンでのみ行うことをお勧めします。**</span><span class="sxs-lookup"><span data-stu-id="f3db5-287">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="f3db5-288">可能な場合は、**信頼されているネットワークまたはプライベート ネットワーク上の** `NodeJS` プロセスとのオープン通信のみを許可します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-288">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="f3db5-289">[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security)の構成ガイダンスについては、[受信プログラムまたはサービス規則の作成](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-289">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="f3db5-290">詳細については、Windows ファイアウォールのドキュメント セットで[高度なセキュリティを備えた Windows Defender ファイアウォール](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-290">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f3db5-291">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="f3db5-291">Troubleshoot</span></span>

<span data-ttu-id="f3db5-292">エラーが発生している場合は、次のヒントが役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-292">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="f3db5-293">**[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-293">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="f3db5-294">コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-294">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="f3db5-295">ASP.NET Core HTTPS 開発証明書がインストールされ、信頼されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-295">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="f3db5-296">詳細については、「<xref:security/enforcing-ssl#troubleshoot-certificate-problems>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-296">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="f3db5-297">Visual Studio では、 **[ツール]**  >  **[オプション]**  >  **[デバッグ]**  >  **[全般]** で **[Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)] (ASP.NET (Chrome、Edge、IE) の JavaScript デバッグを有効にする)** オプションをオンにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-297">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="f3db5-298">これは、Visual Studio の既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-298">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="f3db5-299">デバッグが機能していない場合は、オプションがオンになっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-299">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="f3db5-300">ご利用の環境で HTTP プロキシを使用している場合は、プロキシ バイパス設定に `localhost` が含まれていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-300">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="f3db5-301">これを行うには、次のいずれかに `NO_PROXY` 環境変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-301">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="f3db5-302">プロジェクトの `launchSettings.json` ファイル。</span><span class="sxs-lookup"><span data-stu-id="f3db5-302">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="f3db5-303">すべてのアプリに適用する場合は、ユーザーまたはシステム環境変数レベルで。</span><span class="sxs-lookup"><span data-stu-id="f3db5-303">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="f3db5-304">環境変数を使用する場合は、Visual Studio を再起動して変更を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-304">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="f3db5-305">ファイアウォールまたはプロキシでデバッグ プロキシ (`NodeJS` プロセス) との通信がブロックされていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3db5-305">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="f3db5-306">詳細については、「[ファイアウォールの構成](#firewall-configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f3db5-306">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="f3db5-307">`OnInitialized{Async}` 内のブレークポイントにヒットしない</span><span class="sxs-lookup"><span data-stu-id="f3db5-307">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="f3db5-308">Blazor フレームワークのデバッグ プロキシは、起動に少し時間がかかります。そのため、[`OnInitialized{Async}` ライフサイクル メソッド](xref:blazor/components/lifecycle#component-initialization-methods)内のブレークポイントにヒットしない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f3db5-308">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="f3db5-309">デバッグ プロキシが起動してブレークポイントにヒットするまでの時間を確保するために、メソッド本体の開始に対して遅延を追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f3db5-309">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="f3db5-310">[`if` コンパイラ ディレクティブ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)に基づいて遅延を含めると、アプリのリリース ビルドに対しては遅延が存在しないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-310">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="f3db5-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="f3db5-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="f3db5-312"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="f3db5-312"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="f3db5-313">Visual Studio (Windows) のタイムアウト</span><span class="sxs-lookup"><span data-stu-id="f3db5-313">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="f3db5-314">Visual Studio で、タイムアウトに達したことを示すデバッグ アダプターの起動失敗の例外がスローされた場合、レジストリ設定でタイムアウトを調整できます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-314">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="f3db5-315">上記のコマンドの `{TIMEOUT}` プレースホルダーはミリ秒単位です。</span><span class="sxs-lookup"><span data-stu-id="f3db5-315">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="f3db5-316">たとえば、1 分は `60000` として割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="f3db5-316">For example, one minute is assigned as `60000`.</span></span>

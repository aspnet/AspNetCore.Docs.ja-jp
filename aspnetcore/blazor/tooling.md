---
title: ASP.NET Core Blazor 用のツール
author: guardrex
description: Blazor アプリのビルドに使用できるツールについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 6b61d9a4645d273b0c78fae0388d569771c43a2d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536247"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="a4a65-103">ASP.NET Core Blazor 用のツール</span><span class="sxs-lookup"><span data-stu-id="a4a65-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="a4a65-104">**ASP.NET および Web 開発** ワークロードと共に、最新バージョンの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールする</span><span class="sxs-lookup"><span data-stu-id="a4a65-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="a4a65-105">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-105">Create a new project.</span></span>

1. <span data-ttu-id="a4a65-106">**[Blazor アプリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-106">Select **Blazor App**.</span></span> <span data-ttu-id="a4a65-107">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-107">Select **Next**.</span></span>

1. <span data-ttu-id="a4a65-108">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a4a65-109">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a4a65-110">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-110">Select **Create**.</span></span>

1. <span data-ttu-id="a4a65-111">Blazor WebAssembly エクスペリエンスの場合は、 **Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a4a65-112">Blazor Server エクスペリエンスの場合は、 **Blazor Server アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a4a65-113">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-113">Select **Create**.</span></span>

   <span data-ttu-id="a4a65-114">ホステッド Blazor WebAssembly エクスペリエンスの場合は、 **[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a4a65-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="a4a65-115">2 つの Blazor ホスティング モデル、 *Blazor WebAssembly* (スタンドアロンとホステッド) と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a65-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a4a65-116"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="a4a65-117">ASP.NET Core HTTPS 開発証明書の信頼の詳細については、「<xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a65-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="a4a65-118">ホストされている Blazor WebAssembly アプリを実行する場合は、ソリューションの **`Server`** プロジェクトから、そのアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="a4a65-119">最新バージョンの [.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4a65-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a4a65-120">以前に SDK をインストールした場合、コマンド シェルでコマンドを実行し、インストールされているバージョンを判断できます。</span><span class="sxs-lookup"><span data-stu-id="a4a65-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="a4a65-121">[Visual Studio Code](https://code.visualstudio.com) の最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4a65-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="a4a65-122">最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4a65-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="a4a65-123">Blazor WebAssembly エクスペリエンスの場合は、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="a4a65-124">ホステッド Blazor WebAssembly エクスペリエンスの場合は、ホステッド オプション (`-ho` または `--hosted`) をコマンドに追加します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="a4a65-125">Blazor Server エクスペリエンスの場合は、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="a4a65-126">2 つの Blazor ホスティング モデル、 *Blazor WebAssembly* (スタンドアロンとホステッド) と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a65-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a4a65-127">Visual Studio Code で `WebApplication1` フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="a4a65-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="a4a65-128">IDE によって、プロジェクトをビルドおよびデバッグするためにアセットを追加するよう要求されます。</span><span class="sxs-lookup"><span data-stu-id="a4a65-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a4a65-129">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-129">Select **Yes**.</span></span>

   <span data-ttu-id="a4a65-130">**ホストされている Blazor WebAssembly の起動とタスクの構成**</span><span class="sxs-lookup"><span data-stu-id="a4a65-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="a4a65-131">ホストされている Blazor WebAssembly ソリューションについては、`launch.json` ファイルと `tasks.json` ファイルを含む `.vscode` フォルダーを追加 (または移動) します。このフォルダーは、`Client`、`Server`、`Shared` という、通常のプロジェクト フォルダー名を含むものです。</span><span class="sxs-lookup"><span data-stu-id="a4a65-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="a4a65-132">**`Server`** プロジェクトでホストされている Blazor WebAssembly アプリが `launch.json` ファイルと `tasks.json` ファイルの構成によって実行されるように更新または確認します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="a4a65-133">**`.vscode/launch.json`** (`launch` の構成):</span><span class="sxs-lookup"><span data-stu-id="a4a65-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="a4a65-134">現在の作業ディレクトリの前の構成 (`cwd`) では、`{SERVER APP FOLDER}` プレースホルダーは **`Server`** プロジェクトのフォルダーです (通常は "`Server`")。</span><span class="sxs-lookup"><span data-stu-id="a4a65-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="a4a65-135">システム上で Microsoft Edge が使用されており、Google Chrome がインストールされていない場合は、`"browser": "edge"` の追加のプロパティを構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="a4a65-136">`Server` のプロジェクト フォルダー、およびそれによって、既定のブラウザーである Google Chrome ではなく、Microsoft Edge がデバッグ実行用のブラウザーとして生成される例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="a4a65-137">**`.vscode/tasks.json`** ([`dotnet` コマンド](/dotnet/core/tools/dotnet)引数):</span><span class="sxs-lookup"><span data-stu-id="a4a65-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="a4a65-138">前の引数は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a4a65-138">In the preceding argument:</span></span>

   * <span data-ttu-id="a4a65-139">`{SERVER APP FOLDER}` プレースホルダーは **`Server`** プロジェクトのフォルダーです (通常は "`Server`")。</span><span class="sxs-lookup"><span data-stu-id="a4a65-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="a4a65-140">`{PROJECT NAME}` プレースホルダーはアプリの名前です。通常、この名前は、Blazor プロジェクト テンプレートから生成されたアプリ内のソリューションの名前とそれに続く "`.Server`" から付けられます。</span><span class="sxs-lookup"><span data-stu-id="a4a65-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the Blazor project template.</span></span>

   <span data-ttu-id="a4a65-141">[Blazor WebAssembly アプリでの SignalR の使用に関するチュートリアル](xref:tutorials/signalr-blazor)の次の例では、`Server` のプロジェクト フォルダー名と `BlazorWebAssemblySignalRApp.Server` のプロジェクト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="a4a65-142"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="a4a65-143">開発証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="a4a65-143">Trust a development certificate</span></span>

<span data-ttu-id="a4a65-144">Linux で証明書を信頼するための一元的な方法はありません。</span><span class="sxs-lookup"><span data-stu-id="a4a65-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="a4a65-145">通常、次のいずれかの方法が採用されます。</span><span class="sxs-lookup"><span data-stu-id="a4a65-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="a4a65-146">ブラウザーの除外リストでアプリの URL を除外します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="a4a65-147">`localhost` に対するすべての自己署名証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="a4a65-148">ブラウザーの信頼された証明書の一覧に証明書を追加します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="a4a65-149">詳細については、ご利用のブラウザーの製造元および Linux ディストリビューションによって提供されているガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a65-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="a4a65-150">[Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="a4a65-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="a4a65-151">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から **新しい** プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="a4a65-152">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="a4a65-153">Blazor WebAssembly エクスペリエンスの場合は、 **Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a4a65-154">Blazor Server エクスペリエンスの場合は、 **Blazor Server アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a4a65-155">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-155">Select **Next**.</span></span>

   <span data-ttu-id="a4a65-156">2 つの Blazor ホスティング モデル、 *Blazor WebAssembly* (スタンドアロンとホステッド) と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a65-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a4a65-157">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="a4a65-158">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-158">Select **Next**.</span></span>

1. <span data-ttu-id="a4a65-159">ホステッド Blazor WebAssembly エクスペリエンスの場合は、 **[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a4a65-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="a4a65-160">**[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="a4a65-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="a4a65-161">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-161">Select **Create**.</span></span>

1. <span data-ttu-id="a4a65-162">*デバッガーを使用せずに* アプリを実行するには、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="a4a65-163">**[実行]**  > 、 **[デバッグの開始]** か [実行] (&#9654;) ボタンでアプリを実行すると、"*デバッガーなしで*" アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="a4a65-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="a4a65-164">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="a4a65-165">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="a4a65-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="a4a65-166">ASP.NET Core HTTPS 開発証明書の信頼の詳細については、「<xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a65-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="a4a65-167">ホストされている Blazor WebAssembly アプリを実行する場合は、ソリューションの **`Server`** プロジェクトから、そのアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="a4a65-168">クロスプラットフォームの Blazor 開発に Visual Studio Code を使用する</span><span class="sxs-lookup"><span data-stu-id="a4a65-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="a4a65-169">[Visual Studio Code](https://code.visualstudio.com/) は、Blazor アプリの開発に使用できるオープン ソースのクロスプラットフォーム統合開発環境 (IDE) です。</span><span class="sxs-lookup"><span data-stu-id="a4a65-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="a4a65-170">.NET CLI を使用して、Visual Studio Code で開発用の新しい Blazor アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="a4a65-171">詳細については、[この記事の Linux バージョン](?pivots=linux)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a65-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="a4a65-172">Blazor テンプレート オプション</span><span class="sxs-lookup"><span data-stu-id="a4a65-172">Blazor template options</span></span>

<span data-ttu-id="a4a65-173">Blazor フレームワークには、Blazor ホスティング モデルのそれぞれに対して新しいアプリを作成するためのテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a4a65-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="a4a65-174">テンプレートは、Blazor 開発用に選択したツール (Visual Studio、Visual Studio for Mac、Visual Studio Code、または .NET CLI) に関係なく、新しい Blazor プロジェクトとソリューションを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a4a65-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="a4a65-175">Blazor WebAssembly プロジェクト テンプレート: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="a4a65-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="a4a65-176">Blazor Server プロジェクト テンプレート: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="a4a65-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="a4a65-177">Blazor のホスティング モデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a65-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="a4a65-178">テンプレート オプションを使用するには、コマンド シェルで [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI コマンドにヘルプ オプション (`-h` または `--help`) を渡します。</span><span class="sxs-lookup"><span data-stu-id="a4a65-178">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```

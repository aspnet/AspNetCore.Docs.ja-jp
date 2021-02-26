---
title: ASP.NET Core SignalR を Blazor と共に使用する
author: guardrex
description: Blazor で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: f4e51b39c4c3b0c444b08025e9bd74eec0747541
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536383"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="4fc23-103">ASP.NET Core SignalR を Blazor と共に使用する</span><span class="sxs-lookup"><span data-stu-id="4fc23-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="4fc23-104">このチュートリアルでは、Blazor と共に SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="4fc23-105">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4fc23-106">Blazor プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="4fc23-106">Create a Blazor project</span></span>
> * <span data-ttu-id="4fc23-107">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="4fc23-108">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="4fc23-109">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="4fc23-110">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-110">Add Razor component code for chat</span></span>

<span data-ttu-id="4fc23-111">このチュートリアルの最後には、動作するチャット アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="4fc23-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4fc23-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4fc23-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4fc23-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4fc23-115">**ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.8 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="4fc23-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="4fc23-118">Visual Studio for Mac バージョン 8.8 以降</span><span class="sxs-lookup"><span data-stu-id="4fc23-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-119">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4fc23-121">**ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="4fc23-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="4fc23-124">Visual Studio for Mac バージョン 8.6 以降</span><span class="sxs-lookup"><span data-stu-id="4fc23-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="4fc23-126">ホストされる Blazor WebAssembly アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="4fc23-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="4fc23-127">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4fc23-129">Visual Studio 16.8 以降と .NET Core SDK 5.0.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4fc23-130">Visual Studio 16.6 以降と .NET Core SDK 3.1.300 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="4fc23-131">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-131">Create a new project.</span></span>

1. <span data-ttu-id="4fc23-132">**[Blazor アプリ]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="4fc23-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="4fc23-133">**[プロジェクト名]** フィールドに「`BlazorWebAssemblySignalRApp`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="4fc23-134">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4fc23-135">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-135">Select **Create**.</span></span>

1. <span data-ttu-id="4fc23-136">**Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="4fc23-137">**[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4fc23-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="4fc23-138">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4fc23-140">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="4fc23-141">`-ho|--hosted` オプションを指定すると、ホスト型 Blazor WebAssembly ソリューションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="4fc23-142">`.vscode` フォルダー内の VS Code 資産の構成の詳細については、「<xref:blazor/tooling>」の **Linux** オペレーティング システムのガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="4fc23-143">`-o|--output` オプションを指定すると、ソリューション用のフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="4fc23-144">ソリューション用のフォルダーを作成し、そのフォルダーでコマンド シェルが開いている場合は、`-o|--output` オプションと値を省略してソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="4fc23-145">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="4fc23-146">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="4fc23-147">Visual Studio Code では、生成された `launch.json` ファイルと `tasks.json` ファイルが含まれる `.vscode` フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4fc23-149">最新バージョンの [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) をインストールし、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="4fc23-150">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から **新しい** プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="4fc23-151">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="4fc23-152">**Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="4fc23-153">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-153">Select **Next**.</span></span>

1. <span data-ttu-id="4fc23-154">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="4fc23-155">**[ASP.NET Core Hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4fc23-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="4fc23-156">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-156">Select **Next**.</span></span>

1. <span data-ttu-id="4fc23-157">**[プロジェクト名]** フィールドで、アプリに `BlazorWebAssemblySignalRApp` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="4fc23-158">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-158">Select **Create**.</span></span>

   <span data-ttu-id="4fc23-159">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="4fc23-160">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="4fc23-161">プロジェクト フォルダーに移動し、プロジェクトのソリューション ファイル (`.sln`) を開いてプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4fc23-163">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="4fc23-164">`-ho|--hosted` オプションを指定すると、ホスト型 Blazor WebAssembly ソリューションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="4fc23-165">`-o|--output` オプションを指定すると、ソリューション用のフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="4fc23-166">ソリューション用のフォルダーを作成し、そのフォルダーでコマンド シェルが開いている場合は、`-o|--output` オプションと値を省略してソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="4fc23-167">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4fc23-169">**ソリューション エクスプローラー** で、`BlazorWebAssemblySignalRApp.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4fc23-170">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4fc23-171">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="4fc23-172">検索結果から [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="4fc23-173">アプリの共有フレームワークに合わせてバージョンを設定します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="4fc23-174">**[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-174">Select **Install**.</span></span>

1. <span data-ttu-id="4fc23-175">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4fc23-176">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4fc23-178">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="4fc23-179">以前のバージョンのパッケージを追加するには、`--version {VERSION}` オプションを指定します。`{VERSION}` プレースホルダーは、追加するパッケージのバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4fc23-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4fc23-181">**[ソリューション]** サイドバーで、`BlazorWebAssemblySignalRApp.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4fc23-182">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4fc23-183">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="4fc23-184">検索結果の [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージの横にあるチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4fc23-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="4fc23-185">アプリの共有フレームワークに合わせてバージョンを設定します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="4fc23-186">**[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="4fc23-187">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-188">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4fc23-189">コマンド シェルでソリューションのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="4fc23-190">以前のバージョンのパッケージを追加するには、`--version {VERSION}` オプションを指定します。`{VERSION}` プレースホルダーは、追加するパッケージのバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4fc23-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="4fc23-191">System.Text.Encodings.Web パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="4fc23-192">"*このセクションは、ASP.NET Core バージョン 3.x のアプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="4fc23-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="4fc23-193">ASP.NET Core 3.x アプリで [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x を使用する場合、パッケージの解決に問題があるため、`BlazorWebAssemblySignalRApp.Server` プロジェクトには [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) のパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="4fc23-194">基になる問題は、.NET 5 の今後のパッチ リリースで解決される予定です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="4fc23-195">詳細については、「[System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="4fc23-196">ASP.NET Core 3.1 でホストされている Blazor ソリューションの `BlazorWebAssemblySignalRApp.Server` プロジェクトに [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) を追加するには、使用するツールのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4fc23-198">**ソリューション エクスプローラー** で、`BlazorWebAssemblySignalRApp.Server` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4fc23-199">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4fc23-200">**[参照]** が選択されている状態で、検索ボックスに「`System.Text.Encodings.Web`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="4fc23-201">検索結果から [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="4fc23-202">使用している共有フレームワークに一致するパッケージのバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="4fc23-203">**[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-203">Select **Install**.</span></span>

1. <span data-ttu-id="4fc23-204">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4fc23-205">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4fc23-207">**統合ターミナル** (ツール バーの **[表示]** > **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="4fc23-208">以前のバージョンのパッケージを追加するには、`--version {VERSION}` オプションを指定します。`{VERSION}` プレースホルダーは、追加するパッケージのバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4fc23-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4fc23-210">**[ソリューション]** サイドバーで、`BlazorWebAssemblySignalRApp.Server` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4fc23-211">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4fc23-212">**[参照]** が選択されている状態で、検索ボックスに「`System.Text.Encodings.Web`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="4fc23-213">検索結果で、[`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) パッケージの横にあるチェック ボックスをオンにし、使用している共有フレームワークに一致するパッケージの正しいバージョンを選択して、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="4fc23-214">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-215">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4fc23-216">コマンド シェルでソリューションのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="4fc23-217">以前のバージョンのパッケージを追加するには、`--version {VERSION}` オプションを指定します。`{VERSION}` プレースホルダーは、追加するパッケージのバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4fc23-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="4fc23-218">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-218">Add a SignalR hub</span></span>

<span data-ttu-id="4fc23-219">`BlazorWebAssemblySignalRApp.Server` プロジェクトで、`Hubs` (複数形) フォルダーを作成し、次の `ChatHub` クラス (`Hubs/ChatHub.cs`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="4fc23-220">サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="4fc23-221">`BlazorWebAssemblySignalRApp.Server` プロジェクトで、`Startup.cs` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="4fc23-222">ファイルの先頭に `ChatHub` クラスの名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4fc23-223">SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="4fc23-224">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="4fc23-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4fc23-225">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4fc23-226">コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4fc23-227">SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="4fc23-228">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="4fc23-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4fc23-229">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4fc23-230">コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="4fc23-231">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="4fc23-232">`BlazorWebAssemblySignalRApp.Client` プロジェクトで、`Pages/Index.razor` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4fc23-233">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4fc23-234">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="4fc23-235">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="4fc23-235">Run the app</span></span>

<span data-ttu-id="4fc23-236">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4fc23-238">**ソリューション エクスプローラー** で `BlazorWebAssemblySignalRApp.Server` プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="4fc23-239"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4fc23-240">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4fc23-241">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4fc23-242">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-242">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor サンプル アプリ。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4fc23-244">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4fc23-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4fc23-246">`.vscode` フォルダー内の VS Code 資産の構成の詳細については、「<xref:blazor/tooling>」の **Linux** オペレーティング システムのガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="4fc23-247"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4fc23-248">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4fc23-249">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4fc23-250">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-250">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor サンプル アプリ。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4fc23-252">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4fc23-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-253">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4fc23-254">**[ソリューション]** サイドバーで、`BlazorWebAssemblySignalRApp.Server` プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="4fc23-255"><kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4fc23-256">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4fc23-257">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4fc23-258">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-258">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor サンプル アプリ。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4fc23-260">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4fc23-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="4fc23-262">コマンド シェルでソリューションのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="4fc23-263">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4fc23-264">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4fc23-265">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-265">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor サンプル アプリ。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4fc23-267">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4fc23-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="4fc23-268">Blazor Server アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="4fc23-268">Create a Blazor Server app</span></span>

<span data-ttu-id="4fc23-269">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4fc23-271">Visual Studio 16.8 以降と .NET Core SDK 5.0.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="4fc23-272">Visual Studio 16.6 以降と .NET Core SDK 3.1.300 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="4fc23-273">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-273">Create a new project.</span></span>

1. <span data-ttu-id="4fc23-274">**[Blazor アプリ]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="4fc23-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="4fc23-275">**[プロジェクト名]** フィールドに「`BlazorServerSignalRApp`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="4fc23-276">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4fc23-277">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-277">Select **Create**.</span></span>

1. <span data-ttu-id="4fc23-278">**Blazor Server アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="4fc23-279">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4fc23-281">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="4fc23-282">`-o|--output` オプションを指定すると、プロジェクト用のフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="4fc23-283">プロジェクト用のフォルダーを作成し、そのフォルダーでコマンド シェルが開いている場合は、`-o|--output` オプションと値を省略してプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="4fc23-284">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="4fc23-285">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="4fc23-286">Visual Studio Code では、生成された `launch.json` ファイルと `tasks.json` ファイルが含まれる `.vscode` フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="4fc23-287">`.vscode` フォルダー内の VS Code 資産の構成の詳細については、「<xref:blazor/tooling>」の **Linux** オペレーティング システムのガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-288">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4fc23-289">最新バージョンの [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) をインストールし、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="4fc23-290">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から **新しい** プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="4fc23-291">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="4fc23-292">**Blazor Server アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="4fc23-293">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-293">Select **Next**.</span></span>

1. <span data-ttu-id="4fc23-294">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="4fc23-295">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-295">Select **Next**.</span></span>

1. <span data-ttu-id="4fc23-296">**[プロジェクト名]** フィールドで、アプリに `BlazorServerSignalRApp` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="4fc23-297">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-297">Select **Create**.</span></span>

   <span data-ttu-id="4fc23-298">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="4fc23-299">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="4fc23-300">プロジェクト フォルダーに移動し、プロジェクトのソリューション ファイル (`.sln`) を開いてプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-301">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4fc23-302">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="4fc23-303">`-o|--output` オプションを指定すると、プロジェクト用のフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="4fc23-304">プロジェクト用のフォルダーを作成し、そのフォルダーでコマンド シェルが開いている場合は、`-o|--output` オプションと値を省略してプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="4fc23-305">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4fc23-307">**ソリューション エクスプローラー** で、`BlazorServerSignalRApp` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4fc23-308">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4fc23-309">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="4fc23-310">検索結果から [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="4fc23-311">アプリの共有フレームワークに合わせてバージョンを設定します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="4fc23-312">**[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-312">Select **Install**.</span></span>

1. <span data-ttu-id="4fc23-313">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4fc23-314">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4fc23-316">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="4fc23-317">以前のバージョンのパッケージを追加するには、`--version {VERSION}` オプションを指定します。`{VERSION}` プレースホルダーは、追加するパッケージのバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4fc23-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-318">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4fc23-319">**[ソリューション]** サイドバーで、`BlazorServerSignalRApp` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4fc23-320">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4fc23-321">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="4fc23-322">検索結果の [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージの横にあるチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4fc23-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="4fc23-323">アプリの共有フレームワークに合わせてバージョンを設定します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="4fc23-324">**[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="4fc23-325">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-326">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4fc23-327">コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="4fc23-328">以前のバージョンのパッケージを追加するには、`--version {VERSION}` オプションを指定します。`{VERSION}` プレースホルダーは、追加するパッケージのバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4fc23-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="4fc23-329">System.Text.Encodings.Web パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="4fc23-330">"*このセクションは、ASP.NET Core バージョン 3.x のアプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="4fc23-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="4fc23-331">ASP.NET Core 3.x アプリで [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x を使用する場合、パッケージの解決に問題があるため、プロジェクトには [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) のパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="4fc23-332">基になる問題は、.NET 5 の今後のパッチ リリースで解決される予定です。</span><span class="sxs-lookup"><span data-stu-id="4fc23-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="4fc23-333">詳細については、「[System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="4fc23-334">[`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) をプロジェクトに追加するには、使用するツールのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4fc23-336">**ソリューション エクスプローラー** で、`BlazorServerSignalRApp` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4fc23-337">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4fc23-338">**[参照]** が選択されている状態で、検索ボックスに「`System.Text.Encodings.Web`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="4fc23-339">検索結果から [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="4fc23-340">使用している共有フレームワークに一致するパッケージのバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="4fc23-341">**[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-341">Select **Install**.</span></span>

1. <span data-ttu-id="4fc23-342">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4fc23-343">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4fc23-345">**統合ターミナル** (ツール バーの **[表示]** > **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="4fc23-346">以前のバージョンのパッケージを追加するには、`--version {VERSION}` オプションを指定します。`{VERSION}` プレースホルダーは、追加するパッケージのバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4fc23-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-347">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4fc23-348">**[ソリューション]** サイドバーで、`BlazorServerSignalRApp` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4fc23-349">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="4fc23-350">**[参照]** が選択されている状態で、検索ボックスに「`System.Text.Encodings.Web`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="4fc23-351">検索結果で、[`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) パッケージの横にあるチェック ボックスをオンにし、使用している共有フレームワークに一致するパッケージの正しいバージョンを選択して、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="4fc23-352">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-353">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4fc23-354">コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="4fc23-355">以前のバージョンのパッケージを追加するには、`--version {VERSION}` オプションを指定します。`{VERSION}` プレースホルダーは、追加するパッケージのバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4fc23-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="4fc23-356">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-356">Add a SignalR hub</span></span>

<span data-ttu-id="4fc23-357">`Hubs` (複数形) フォルダーを作成し、次の `ChatHub` クラス (`Hubs/ChatHub.cs`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="4fc23-358">サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="4fc23-359">`Startup.cs` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="4fc23-360"><xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> の名前空間と `ChatHub` クラスをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4fc23-361">応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="4fc23-362">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="4fc23-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4fc23-363">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4fc23-364">Blazor ハブとクライアント側フォールバックをマッピングするためのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4fc23-365">応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="4fc23-366">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="4fc23-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4fc23-367">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4fc23-368">Blazor ハブとクライアント側フォールバックをマッピングするためのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="4fc23-369">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="4fc23-370">`Pages/Index.razor` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4fc23-371">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-371">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4fc23-372">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-372">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="4fc23-373">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="4fc23-373">Run the app</span></span>

<span data-ttu-id="4fc23-374">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc23-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc23-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4fc23-376"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4fc23-377">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4fc23-378">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4fc23-379">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-379">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor サンプル アプリ。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4fc23-381">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4fc23-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4fc23-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4fc23-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4fc23-383"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4fc23-384">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4fc23-385">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4fc23-386">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-386">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor サンプル アプリ。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4fc23-388">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4fc23-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4fc23-389">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4fc23-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4fc23-390"><kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4fc23-391">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4fc23-392">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4fc23-393">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-393">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor サンプル アプリ。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4fc23-395">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4fc23-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc23-396">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fc23-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="4fc23-397">コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="4fc23-398">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4fc23-399">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4fc23-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="4fc23-400">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fc23-400">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor サンプル アプリ。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="4fc23-402">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4fc23-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="4fc23-403">次の手順</span><span class="sxs-lookup"><span data-stu-id="4fc23-403">Next steps</span></span>

<span data-ttu-id="4fc23-404">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="4fc23-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4fc23-405">Blazor プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="4fc23-405">Create a Blazor project</span></span>
> * <span data-ttu-id="4fc23-406">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="4fc23-407">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="4fc23-408">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="4fc23-409">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="4fc23-409">Add Razor component code for chat</span></span>

<span data-ttu-id="4fc23-410">Blazor アプリの構築について詳しくは、Blazor のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fc23-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="4fc23-411"><xref:blazor/index>
> [Identity Server、WebSockets、Server-Sent Events によるベアラー トークン認証](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="4fc23-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4fc23-412">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4fc23-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="4fc23-413">認証のための SignalR のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="4fc23-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>

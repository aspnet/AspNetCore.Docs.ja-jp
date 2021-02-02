---
title: ASP.NET Core MVC の概要
author: rick-anderson
description: ASP.NET Core MVC の概要について説明します。
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710799"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="9357b-103">ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="9357b-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="9357b-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9357b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="9357b-105">これは、コントローラーとビューを使用した ASP.NET Core MVC Web 開発について説明するシリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="9357b-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="9357b-106">シリーズの最後には、映画のデータを管理および表示できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="9357b-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="9357b-107">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9357b-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9357b-108">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="9357b-108">Create a web app.</span></span>
> * <span data-ttu-id="9357b-109">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="9357b-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="9357b-110">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="9357b-110">Work with a database.</span></span>
> * <span data-ttu-id="9357b-111">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="9357b-111">Add search and validation.</span></span>

<span data-ttu-id="9357b-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9357b-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9357b-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9357b-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9357b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9357b-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9357b-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9357b-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9357b-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9357b-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="9357b-117">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="9357b-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9357b-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9357b-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9357b-119">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="9357b-120">**[新しいプロジェクトの作成]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="9357b-121">**[新しいプロジェクトの構成]** ダイアログで、 **[プロジェクト名]** に「`MvcMovie`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="9357b-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="9357b-122">プロジェクトに *MvcMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="9357b-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="9357b-123">コードをコピーする際に、大文字と小文字の区別が各 `namespace` と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9357b-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="9357b-124">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="9357b-124">Select **Create**.</span></span>
* <span data-ttu-id="9357b-125">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで、次のものを選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="9357b-126">ドロップダウンで **[.NET Core]** と **[ASP.NET Core 5.0]**</span><span class="sxs-lookup"><span data-stu-id="9357b-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="9357b-127">**ASP.NET Core Web アプリ (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="9357b-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="9357b-128">**Create**。</span><span class="sxs-lookup"><span data-stu-id="9357b-128">**Create**.</span></span>

![<span data-ttu-id="9357b-129">新しい ASP.NET Core Web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="9357b-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="9357b-130">プロジェクトを作成する別の方法については、「[Visual Studio で新しいプロジェクトを作成する](/visualstudio/ide/create-new-project)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9357b-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="9357b-131">Visual Studio により、作成した MVC プロジェクトに既定のプロジェクト テンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="9357b-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="9357b-132">作成したプロジェクトは:</span><span class="sxs-lookup"><span data-stu-id="9357b-132">The created project:</span></span>

* <span data-ttu-id="9357b-133">動作するアプリです。</span><span class="sxs-lookup"><span data-stu-id="9357b-133">Is a working app.</span></span>
* <span data-ttu-id="9357b-134">基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="9357b-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9357b-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9357b-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9357b-136">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9357b-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="9357b-137">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)に関するページと「[Visual Studio Code ヘルプ](#visual-studio-code-help)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9357b-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="9357b-138">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="9357b-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9357b-139">プロジェクトを格納するディレクトリに移動します (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="9357b-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="9357b-140">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9357b-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="9357b-141">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " というダイアログ ボックスが表示されたら、 **[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="9357b-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="9357b-142">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="9357b-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="9357b-143">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9357b-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9357b-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9357b-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9357b-145">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-145">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="9357b-147">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="9357b-148">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="9357b-150">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="9357b-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="9357b-151">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9357b-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="9357b-152">**[ターゲット フレームワーク]** を選択するオプションが表示されている場合は、最新の 5.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="9357b-153">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-153">Select **Next**.</span></span>

* <span data-ttu-id="9357b-154">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="9357b-156">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9357b-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9357b-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9357b-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9357b-158">Ctrl + F5 キーを選択して、デバッガーなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9357b-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9357b-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9357b-159">Visual Studio:</span></span>

  * <span data-ttu-id="9357b-160">[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を起動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="9357b-161">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9357b-161">Runs the app.</span></span>

  <span data-ttu-id="9357b-162">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9357b-163">ローカル コンピューターの標準的なホスト名は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="9357b-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="9357b-164">Visual Studio で Web プロジェクトが作成されるとき、Web サーバーにはランダムなポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="9357b-165">Ctrl + F5 キーを選択してデバッグなしでアプリを起動すると、次の操作を行えます。</span><span class="sxs-lookup"><span data-stu-id="9357b-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="9357b-166">コードを変更します。</span><span class="sxs-lookup"><span data-stu-id="9357b-166">Make code changes.</span></span>
* <span data-ttu-id="9357b-167">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9357b-167">Save the file.</span></span>
* <span data-ttu-id="9357b-168">ブラウザーをすぐに更新して、コードの変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="9357b-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="9357b-169">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="9357b-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![[デバッグ] メニュー](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="9357b-171">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="9357b-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="9357b-173">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="9357b-173">The following image shows the app:</span></span>

![ホームまたはインデックス ページ](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9357b-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9357b-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9357b-176">Ctrl + F5 キーを選択して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="9357b-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="9357b-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="9357b-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="9357b-178">[Kestrel](xref:fundamentals/servers/kestrel) を起動します</span><span class="sxs-lookup"><span data-stu-id="9357b-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="9357b-179">ブラウザーを起動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-179">Launches a browser.</span></span>
  * <span data-ttu-id="9357b-180">`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="9357b-181">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="9357b-182">ローカル コンピューターの標準的なホスト名は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="9357b-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="9357b-183">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="9357b-184">Ctrl + F5 キーを選択してデバッグなしでアプリを起動すると、次の操作を行えます。</span><span class="sxs-lookup"><span data-stu-id="9357b-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="9357b-185">コードを変更します。</span><span class="sxs-lookup"><span data-stu-id="9357b-185">Make code changes.</span></span>
* <span data-ttu-id="9357b-186">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9357b-186">Save the file.</span></span>
* <span data-ttu-id="9357b-187">ブラウザーをすぐに更新して、コードの変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="9357b-187">Quickly refresh the browser and see the code changes.</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9357b-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9357b-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9357b-190">**[実行]** > **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="9357b-191">Visual Studio for Mac:</span><span class="sxs-lookup"><span data-stu-id="9357b-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="9357b-192">[Kestrel](xref:fundamentals/servers/index#kestrel) サーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="9357b-193">ブラウザーを起動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-193">Launches a browser.</span></span>
  * <span data-ttu-id="9357b-194">`http://localhost:port` に移動します。*port* はランダムに選択されるポート番号です。</span><span class="sxs-lookup"><span data-stu-id="9357b-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="9357b-195">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9357b-196">ローカル コンピューターの標準的なホスト名は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="9357b-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="9357b-197">Visual Studio で Web プロジェクトが作成されるとき、Web サーバーにはランダムなポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="9357b-198">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="9357b-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="9357b-199">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="9357b-199">The following image shows the app:</span></span>

![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="9357b-201">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="9357b-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9357b-202">次へ: コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="9357b-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="9357b-203">これは、コントローラーとビューを使用した ASP.NET Core MVC Web 開発について説明するシリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="9357b-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="9357b-204">シリーズの最後には、映画のデータを管理および表示できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="9357b-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="9357b-205">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9357b-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9357b-206">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="9357b-206">Create a web app.</span></span>
> * <span data-ttu-id="9357b-207">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="9357b-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="9357b-208">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="9357b-208">Work with a database.</span></span>
> * <span data-ttu-id="9357b-209">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="9357b-209">Add search and validation.</span></span>

<span data-ttu-id="9357b-210">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9357b-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9357b-211">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9357b-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9357b-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9357b-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9357b-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9357b-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9357b-214">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9357b-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="9357b-215">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="9357b-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9357b-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9357b-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9357b-217">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="9357b-218">**[ASP.NET Core Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション プロジェクトを作成します](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="9357b-220">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="9357b-221">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="9357b-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しいプロジェクトを構成する](start-mvc/_static/config.png)

* <span data-ttu-id="9357b-223">**[Web アプリケーション (モデル ビュー コントローラー)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="9357b-224">ドロップダウン ボックスから **[.NET Core]** と **[ASP.NET Core 3.1]** を選択した後、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="9357b-225">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="9357b-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="9357b-226">Visual Studio により、作成した MVC プロジェクトに既定のプロジェクト テンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="9357b-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="9357b-227">作成したプロジェクトは:</span><span class="sxs-lookup"><span data-stu-id="9357b-227">The created project:</span></span>

* <span data-ttu-id="9357b-228">動作するアプリです。</span><span class="sxs-lookup"><span data-stu-id="9357b-228">Is a working app.</span></span>
* <span data-ttu-id="9357b-229">基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="9357b-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9357b-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9357b-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9357b-231">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9357b-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="9357b-232">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)に関するページと「[Visual Studio Code ヘルプ](#visual-studio-code-help)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9357b-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="9357b-233">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="9357b-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9357b-234">ディレクトリを、プロジェクトを格納するフォルダーに変更します (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="9357b-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="9357b-235">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9357b-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="9357b-236">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " というダイアログ ボックスが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="9357b-237">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="9357b-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="9357b-238">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9357b-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9357b-239">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9357b-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9357b-240">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-240">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="9357b-242">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="9357b-243">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="9357b-245">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="9357b-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="9357b-246">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9357b-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="9357b-247">**[ターゲット フレームワーク]** を選択するオプションが表示されている場合は、最新の 3.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="9357b-248">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-248">Select **Next**.</span></span>

* <span data-ttu-id="9357b-249">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9357b-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="9357b-251">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9357b-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9357b-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9357b-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9357b-253">Ctrl + F5 キーを選択して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9357b-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9357b-254">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9357b-254">Visual Studio:</span></span>

  * <span data-ttu-id="9357b-255">[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を起動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="9357b-256">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9357b-256">Runs the app.</span></span>

  <span data-ttu-id="9357b-257">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9357b-258">ローカル コンピューターの標準的なホスト名は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="9357b-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="9357b-259">Visual Studio で Web プロジェクトが作成されるとき、Web サーバーにはランダムなポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="9357b-260">Ctrl + F5 キーを選択してデバッグなしでアプリを起動すると、次の操作を行えます。</span><span class="sxs-lookup"><span data-stu-id="9357b-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="9357b-261">コードを変更します。</span><span class="sxs-lookup"><span data-stu-id="9357b-261">Make code changes.</span></span>
* <span data-ttu-id="9357b-262">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9357b-262">Save the file.</span></span>
* <span data-ttu-id="9357b-263">ブラウザーをすぐに更新して、コードの変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="9357b-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="9357b-264">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="9357b-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

<span data-ttu-id="9357b-266">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="9357b-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="9357b-268">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="9357b-268">The following image shows the app:</span></span>

![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9357b-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9357b-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9357b-271">Ctrl + F5 キーを選択して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9357b-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="9357b-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="9357b-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="9357b-273">[Kestrel](xref:fundamentals/servers/kestrel) を起動します</span><span class="sxs-lookup"><span data-stu-id="9357b-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="9357b-274">ブラウザーを起動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-274">Launches a browser.</span></span>
  * <span data-ttu-id="9357b-275">`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="9357b-276">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="9357b-277">ローカル コンピューターの標準的なホスト名は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="9357b-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="9357b-278">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="9357b-279">Ctrl + F5 キーを選択してデバッグなしでアプリを起動すると、次の操作を行えます。</span><span class="sxs-lookup"><span data-stu-id="9357b-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="9357b-280">コードを変更します。</span><span class="sxs-lookup"><span data-stu-id="9357b-280">Make code changes.</span></span>
* <span data-ttu-id="9357b-281">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9357b-281">Save the file.</span></span>
* <span data-ttu-id="9357b-282">ブラウザーをすぐに更新して、コードの変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="9357b-282">Quickly refresh the browser and see the code changes.</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9357b-284">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9357b-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9357b-285">**[実行]** > **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9357b-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="9357b-286">Visual Studio for Mac: [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="9357b-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="9357b-287">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9357b-288">ローカル コンピューターの標準的なホスト名は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="9357b-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="9357b-289">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="9357b-290">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9357b-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="9357b-291">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="9357b-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="9357b-292">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="9357b-292">The following image shows the app:</span></span>

![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="9357b-294">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="9357b-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9357b-295">次へ</span><span class="sxs-lookup"><span data-stu-id="9357b-295">Next</span></span>](adding-controller.md)

::: moniker-end

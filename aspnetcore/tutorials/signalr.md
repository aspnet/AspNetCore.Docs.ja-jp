---
title: 'ASP.NET Core SignalR の概要'
author: bradygaster
description: 'このチュートリアルでは、ASP.NET Core SignalR を使用するチャット アプリを作成します。'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/signalr
ms.openlocfilehash: 59c296f3388e71254badb02fa3ae4279005c359c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056882"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="a4e63-103">チュートリアル: ASP.NET Core SignalR の概要</span><span class="sxs-lookup"><span data-stu-id="a4e63-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a4e63-104">このチュートリアルでは、SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="a4e63-105">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a4e63-106">Web プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-106">Create a web project.</span></span>
> * <span data-ttu-id="a4e63-107">SignalR クライアント ライブラリを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="a4e63-108">SignalR ハブを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="a4e63-109">SignalR を使用するようにプロジェクトを構成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="a4e63-110">任意のクライアントから、接続されているすべてのクライアントにメッセージを送信するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="a4e63-111">最後には、次のように動作するチャット アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-111">At the end, you'll have a working chat app:</span></span>

![SignalR サンプル アプリ](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="a4e63-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a4e63-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e63-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e63-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e63-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e63-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e63-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e63-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="a4e63-117">Web アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="a4e63-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e63-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e63-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="a4e63-119">メニューから、 **[ファイル]、[新規プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="a4e63-120">**[新しいプロジェクト]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** , and then select **Next**.</span></span>

* <span data-ttu-id="a4e63-121">**[新しいプロジェクトの構成]** ダイアログで、プロジェクトに *SignalRChat* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-121">In the **Configure your new project** dialog, name the project *SignalRChat* , and then select **Create**.</span></span>

* <span data-ttu-id="a4e63-122">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.1**.</span></span> 

* <span data-ttu-id="a4e63-123">Razor Pages を使用するプロジェクトを作成するには **[Web アプリケーション]** を選択し、次に **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e63-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e63-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="a4e63-126">新しいプロジェクト フォルダーを作成するフォルダーで[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="a4e63-127">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e63-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e63-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a4e63-129">メニューから、 **[ファイル]、[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="a4e63-130">**[.NET Core] > [アプリ] > [Web アプリケーション]** の順に選択し ( **[Web アプリケーション (Model-View-Controller)]** は選択しないでください)、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)** ), and then select **Next**.</span></span>

* <span data-ttu-id="a4e63-131">**[ターゲット フレームワーク]** が **.NET Core 3.1** に設定されていることを確認してから、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-131">Make sure the **Target Framework** is set to **.NET Core 3.1** , and then select **Next**.</span></span>

* <span data-ttu-id="a4e63-132">プロジェクトに *SignalRChat* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-132">Name the project *SignalRChat* , and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="a4e63-133">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="a4e63-133">Add the SignalR client library</span></span>

<span data-ttu-id="a4e63-134">SignalR サーバー ライブラリは、ASP.NET Core 3.1 共有フレームワークに含まれています。</span><span class="sxs-lookup"><span data-stu-id="a4e63-134">The SignalR server library is included in the ASP.NET Core 3.1 shared framework.</span></span> <span data-ttu-id="a4e63-135">JavaScript クライアント ライブラリはプロジェクトに自動的に含まれません。</span><span class="sxs-lookup"><span data-stu-id="a4e63-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="a4e63-136">このチュートリアルでは、ライブラリ マネージャー (LibMan) を使用して *unpkg* からクライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="a4e63-137">unpkg は、npm (Node.js パッケージ マネージャー) で見つかるものすべてを配信できるコンテンツ配信ネットワーク (CDN) です。</span><span class="sxs-lookup"><span data-stu-id="a4e63-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e63-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e63-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="a4e63-139">**ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[追加]** > **[クライアント側のライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-139">In **Solution Explorer** , right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="a4e63-140">**[Add Client-Side Library]\(クライアント側のライブラリの追加\)** ダイアログで、 **[プロバイダー]** に対して **[unpkg]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="a4e63-141">**[ライブラリ]** に、「`@microsoft/signalr@latest`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-141">For **Library** , enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="a4e63-142">**[Choose specific files]\(特定のファイルの選択\)** を選択して *dist/browser* フォルダーを展開し、 *signalr.js* と *signalr.min.js* を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-142">Select **Choose specific files** , expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="a4e63-143">**[ターゲットの場所]** を *wwwroot/js/signalr/* に設定して、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-143">Set **Target Location** to *wwwroot/js/signalr/* , and select **Install**.</span></span>

  ![クライアント側のライブラリの追加ダイアログ - ライブラリの選択](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="a4e63-145">LibMan によって *wwwroot/js/signalr* フォルダーが作成され、そこに選択したファイルがコピーされます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e63-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e63-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="a4e63-147">統合ターミナルで、次のコマンドを実行して LibMan をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4e63-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="a4e63-148">次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="a4e63-149">出力が表示されるまでに数秒待機する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="a4e63-150">パラメーターによって次のオプションが指定されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="a4e63-151">unpkg プロバイダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="a4e63-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="a4e63-152">ファイルを *wwwroot/js/signalr* にコピーする。</span><span class="sxs-lookup"><span data-stu-id="a4e63-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="a4e63-153">指定したファイルのみをコピーする。</span><span class="sxs-lookup"><span data-stu-id="a4e63-153">Copy only the specified files.</span></span>

  <span data-ttu-id="a4e63-154">出力は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e63-155">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e63-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a4e63-156">**[端末]** で、次のコマンドを実行して LibMan をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4e63-156">In the **Terminal** , run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="a4e63-157">プロジェクト フォルダー (ファイル *SignalRChat.csproj* を含んでいるフォルダー) に移動します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="a4e63-158">次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="a4e63-159">パラメーターによって次のオプションが指定されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="a4e63-160">unpkg プロバイダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="a4e63-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="a4e63-161">ファイルを *wwwroot/js/signalr* にコピーする。</span><span class="sxs-lookup"><span data-stu-id="a4e63-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="a4e63-162">指定したファイルのみをコピーする。</span><span class="sxs-lookup"><span data-stu-id="a4e63-162">Copy only the specified files.</span></span>

  <span data-ttu-id="a4e63-163">出力は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="a4e63-164">SignalR ハブを作成する</span><span class="sxs-lookup"><span data-stu-id="a4e63-164">Create a SignalR hub</span></span>

<span data-ttu-id="a4e63-165">*ハブ* はクライアント サーバー通信を処理するハイレベル パイプラインとして機能するクラスです。</span><span class="sxs-lookup"><span data-stu-id="a4e63-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="a4e63-166">SignalRChat プロジェクト フォルダー内で、 *Hubs* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="a4e63-167">*Hubs* フォルダー内で、次のコードを使用して *ChatHub.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="a4e63-168">`ChatHub` クラスは SignalR `Hub` クラスを継承します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="a4e63-169">`Hub` クラスでは、接続、グループ、およびメッセージングが管理されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="a4e63-170">`SendMessage` メソッドは、メッセージをすべてのクライアントに送信するために、接続されたクライアントによって呼び出される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="a4e63-171">このメソッドを呼び出す JavaScript クライアント コードは、チュートリアルの後半で示されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="a4e63-172">最大のスケーラビリティを実現するために、SignalR コードは非同期になっています。</span><span class="sxs-lookup"><span data-stu-id="a4e63-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="a4e63-173">SignalR を構成する</span><span class="sxs-lookup"><span data-stu-id="a4e63-173">Configure SignalR</span></span>

<span data-ttu-id="a4e63-174">SignalR 要求が SignalR に渡されるように SignalR サーバーを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="a4e63-175">次の強調表示されたコードを *Startup.cs* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="a4e63-176">これらの変更によって、SignalR が ASP.NET Core 依存関係挿入およびルーティング システムに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="a4e63-177">SignalR クライアント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="a4e63-177">Add SignalR client code</span></span>

* <span data-ttu-id="a4e63-178">*Pages\Index.cshtml* のコンテンツを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="a4e63-179">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-179">The preceding code:</span></span>

  * <span data-ttu-id="a4e63-180">名前およびメッセージ テキスト用のテキスト ボックスと送信ボタンを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="a4e63-181">SignalR ハブから受信したメッセージを表示するために、`id="messagesList"` を使用してリストを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="a4e63-182">SignalR へのスクリプト参照と、次の手順で作成する *chat.js* アプリケーション コードを含めます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="a4e63-183">*wwwroot/js* フォルダー内に、次のコードを使用して *chat.js* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="a4e63-184">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-184">The preceding code:</span></span>

  * <span data-ttu-id="a4e63-185">接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="a4e63-186">ハブにメッセージを送信するハンドラーを送信ボタンに追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="a4e63-187">ハブからメッセージを受信してからそれをリストに追加するハンドラーを接続オブジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="a4e63-188">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="a4e63-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e63-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e63-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a4e63-190">**Ctrl + F5** キーを押して、デバッグを行わずにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e63-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e63-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a4e63-192">統合ターミナルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e63-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e63-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a4e63-194">メニューから、 **[実行]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="a4e63-195">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="a4e63-196">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[Send Message]\(メッセージの送信\)** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="a4e63-197">次の瞬間、両方のページに名前とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR サンプル アプリ](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="a4e63-199">アプリが動作しない場合は、ご利用のブラウザーの開発者ツール (F12) を開き、コンソールに移動します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="a4e63-200">HTML および JavaScript コードに関連するエラーが発生している場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="a4e63-201">たとえば、 *signalr.js* を指示されたフォルダーとは別のフォルダーに配置したとします。</span><span class="sxs-lookup"><span data-stu-id="a4e63-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="a4e63-202">その場合、そのファイルへの参照は機能せず、コンソールに 404 エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="a4e63-203">![エラー "signalr.js が見つかりませんでした"](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="a4e63-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="a4e63-204">Chrome でエラー ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY が発生する場合は、次のコマンドを実行して開発証明書を更新します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a4e63-205">このチュートリアルでは、SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="a4e63-206">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="a4e63-207">Web プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-207">Create a web project.</span></span>   
> * <span data-ttu-id="a4e63-208">SignalR クライアント ライブラリを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="a4e63-209">SignalR ハブを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="a4e63-210">SignalR を使用するようにプロジェクトを構成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="a4e63-211">任意のクライアントから、接続されているすべてのクライアントにメッセージを送信するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="a4e63-212">最後には、動作するチャット アプリが作成されます。![SignalR サンプル アプリ](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="a4e63-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="a4e63-213">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a4e63-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="a4e63-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e63-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e63-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e63-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e63-216">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e63-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="a4e63-217">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="a4e63-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="a4e63-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e63-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="a4e63-219">メニューから、 **[ファイル]、[新規プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="a4e63-220">**[新しいプロジェクト]** ダイアログで、 **[インストール]、[Visual C#]、[Web]、[ASP.NET Core Web アプリケーション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a4e63-221">プロジェクトに *SignalRChat* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-221">Name the project *SignalRChat*.</span></span>   

  ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="a4e63-223">**[Web アプリケーション]** を選択して、Razor Pages を使用するプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="a4e63-224">**.NET Core** のターゲット フレームワークを選択し、 **ASP.NET Core 2.2** を選択して、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="a4e63-224">Select a target framework of **.NET Core** , select **ASP.NET Core 2.2** , and click **OK**.</span></span>    

  ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e63-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e63-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="a4e63-227">新しいプロジェクト フォルダーを作成するフォルダーで[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="a4e63-228">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e63-229">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e63-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="a4e63-230">メニューから、 **[ファイル]、[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="a4e63-231">**[.NET Core]、[アプリ]、[ASP.NET Core Web アプリ]** の順に選択します ( **[ASP.NET Core Web アプリ (MVC)]** を選択しないでください)。</span><span class="sxs-lookup"><span data-stu-id="a4e63-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)** ).</span></span>  

* <span data-ttu-id="a4e63-232">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-232">Select **Next**.</span></span>  

* <span data-ttu-id="a4e63-233">プロジェクトに *SignalRChat* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-233">Name the project *SignalRChat* , and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="a4e63-234">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="a4e63-234">Add the SignalR client library</span></span> 

<span data-ttu-id="a4e63-235">SignalR サーバー ライブラリは、`Microsoft.AspNetCore.App` メタパッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="a4e63-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="a4e63-236">JavaScript クライアント ライブラリはプロジェクトに自動的に含まれません。</span><span class="sxs-lookup"><span data-stu-id="a4e63-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="a4e63-237">このチュートリアルでは、ライブラリ マネージャー (LibMan) を使用して *unpkg* からクライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="a4e63-238">unpkg は、npm (Node.js パッケージ マネージャー) で見つかるものすべてを配信できるコンテンツ配信ネットワーク (CDN) です。</span><span class="sxs-lookup"><span data-stu-id="a4e63-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="a4e63-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e63-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="a4e63-240">**ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[追加]** > **[クライアント側のライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-240">In **Solution Explorer** , right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="a4e63-241">**[Add Client-Side Library]\(クライアント側のライブラリの追加\)** ダイアログで、 **[プロバイダー]** に対して **[unpkg]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="a4e63-242">**[ライブラリ]** に対して「`@microsoft/signalr@3`」を入力し、プレビューでない最新のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-242">For **Library** , enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![クライアント側のライブラリの追加ダイアログ - ライブラリの選択](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="a4e63-244">**[Choose specific files]\(特定のファイルの選択\)** を選択して *dist/browser* フォルダーを展開し、 *signalr.js* と *signalr.min.js* を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-244">Select **Choose specific files** , expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="a4e63-245">**[ターゲットの場所]** を *wwwroot/lib/signalr/* に設定して、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-245">Set **Target Location** to *wwwroot/lib/signalr/* , and select **Install**.</span></span>    

  ![クライアント側のライブラリの追加ダイアログ - ファイルと保存先の選択](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="a4e63-247">LibMan によって *wwwroot/lib/signalr* フォルダーが作成され、そこに選択したファイルがコピーされます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e63-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e63-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="a4e63-249">統合ターミナルで、次のコマンドを実行して LibMan をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4e63-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="a4e63-250">次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="a4e63-251">出力が表示されるまでに数秒待機する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="a4e63-252">パラメーターによって次のオプションが指定されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="a4e63-253">unpkg プロバイダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="a4e63-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="a4e63-254">ファイルを *wwwroot/lib/signalr* にコピーする。</span><span class="sxs-lookup"><span data-stu-id="a4e63-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="a4e63-255">指定したファイルのみをコピーする。</span><span class="sxs-lookup"><span data-stu-id="a4e63-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="a4e63-256">出力は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e63-257">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e63-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="a4e63-258">**[端末]** で、次のコマンドを実行して LibMan をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4e63-258">In the **Terminal** , run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="a4e63-259">プロジェクト フォルダー (ファイル *SignalRChat.csproj* を含んでいるフォルダー) に移動します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="a4e63-260">次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="a4e63-261">パラメーターによって次のオプションが指定されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="a4e63-262">unpkg プロバイダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="a4e63-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="a4e63-263">ファイルを *wwwroot/lib/signalr* にコピーする。</span><span class="sxs-lookup"><span data-stu-id="a4e63-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="a4e63-264">指定したファイルのみをコピーする。</span><span class="sxs-lookup"><span data-stu-id="a4e63-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="a4e63-265">出力は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="a4e63-266">SignalR ハブを作成する</span><span class="sxs-lookup"><span data-stu-id="a4e63-266">Create a SignalR hub</span></span>   

<span data-ttu-id="a4e63-267">*ハブ* はクライアント サーバー通信を処理するハイレベル パイプラインとして機能するクラスです。</span><span class="sxs-lookup"><span data-stu-id="a4e63-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="a4e63-268">SignalRChat プロジェクト フォルダー内で、 *Hubs* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="a4e63-269">*Hubs* フォルダー内で、次のコードを使用して *ChatHub.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="a4e63-270">`ChatHub` クラスは SignalR `Hub` クラスを継承します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="a4e63-271">`Hub` クラスでは、接続、グループ、およびメッセージングが管理されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="a4e63-272">`SendMessage` メソッドは、メッセージをすべてのクライアントに送信するために、接続されたクライアントによって呼び出される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="a4e63-273">このメソッドを呼び出す JavaScript クライアント コードは、チュートリアルの後半で示されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="a4e63-274">最大のスケーラビリティを実現するために、SignalR コードは非同期になっています。</span><span class="sxs-lookup"><span data-stu-id="a4e63-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="a4e63-275">SignalR を構成する</span><span class="sxs-lookup"><span data-stu-id="a4e63-275">Configure SignalR</span></span>  

<span data-ttu-id="a4e63-276">SignalR 要求が SignalR に渡されるように SignalR サーバーを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="a4e63-277">次の強調表示されたコードを *Startup.cs* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="a4e63-278">これらの変更によって、SignalR が ASP.NET Core 依存関係挿入システムとミドルウェア パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="a4e63-279">SignalR クライアント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="a4e63-279">Add SignalR client code</span></span>    

* <span data-ttu-id="a4e63-280">*Pages\Index.cshtml* のコンテンツを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="a4e63-281">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-281">The preceding code:</span></span>   

  * <span data-ttu-id="a4e63-282">名前およびメッセージ テキスト用のテキスト ボックスと送信ボタンを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="a4e63-283">SignalR ハブから受信したメッセージを表示するために、`id="messagesList"` を使用してリストを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="a4e63-284">SignalR へのスクリプト参照と、次の手順で作成する *chat.js* アプリケーション コードを含めます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="a4e63-285">*wwwroot/js* フォルダー内に、次のコードを使用して *chat.js* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="a4e63-286">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-286">The preceding code:</span></span>   

  * <span data-ttu-id="a4e63-287">接続を作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="a4e63-288">ハブにメッセージを送信するハンドラーを送信ボタンに追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="a4e63-289">ハブからメッセージを受信してからそれをリストに追加するハンドラーを接続オブジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="a4e63-290">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="a4e63-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="a4e63-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e63-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="a4e63-292">**Ctrl + F5** キーを押して、デバッグを行わずにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e63-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e63-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="a4e63-294">統合ターミナルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e63-295">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e63-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a4e63-296">メニューから、 **[実行]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="a4e63-297">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="a4e63-298">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[Send Message]\(メッセージの送信\)** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="a4e63-299">次の瞬間、両方のページに名前とメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-299">The name and message are displayed on both pages instantly.</span></span>   

  ![SignalR サンプル アプリ](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="a4e63-301">アプリが動作しない場合は、ご利用のブラウザーの開発者ツール (F12) を開き、コンソールに移動します。</span><span class="sxs-lookup"><span data-stu-id="a4e63-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="a4e63-302">HTML および JavaScript コードに関連するエラーが発生している場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4e63-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="a4e63-303">たとえば、 *signalr.js* を指示されたフォルダーとは別のフォルダーに配置したとします。</span><span class="sxs-lookup"><span data-stu-id="a4e63-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="a4e63-304">その場合、そのファイルへの参照は機能せず、コンソールに 404 エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4e63-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="a4e63-305">![エラー "signalr.js が見つかりませんでした"](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="a4e63-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="a4e63-306">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a4e63-306">Additional resources</span></span> 
* [<span data-ttu-id="a4e63-307">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="a4e63-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end

---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 1f7c7db857090ff0a174d37b86e1265bab40b4fd
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564094"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="3c57d-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="3c57d-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="3c57d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5)、[Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="3c57d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="3c57d-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3c57d-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c57d-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-107">Create a web API project.</span></span>
> * <span data-ttu-id="3c57d-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3c57d-109">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="3c57d-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="3c57d-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="3c57d-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="3c57d-111">Call the web API with Postman.</span></span>

<span data-ttu-id="3c57d-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="3c57d-113">概要</span><span class="sxs-lookup"><span data-stu-id="3c57d-113">Overview</span></span>

<span data-ttu-id="3c57d-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3c57d-115">API</span><span class="sxs-lookup"><span data-stu-id="3c57d-115">API</span></span> | <span data-ttu-id="3c57d-116">説明</span><span class="sxs-lookup"><span data-stu-id="3c57d-116">Description</span></span> | <span data-ttu-id="3c57d-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="3c57d-117">Request body</span></span> | <span data-ttu-id="3c57d-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="3c57d-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="3c57d-119">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-119">Get all to-do items</span></span> | <span data-ttu-id="3c57d-120">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-120">None</span></span> | <span data-ttu-id="3c57d-121">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="3c57d-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="3c57d-122">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-122">Get an item by ID</span></span> | <span data-ttu-id="3c57d-123">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-123">None</span></span> | <span data-ttu-id="3c57d-124">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="3c57d-125">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-125">Add a new item</span></span> | <span data-ttu-id="3c57d-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-126">To-do item</span></span> | <span data-ttu-id="3c57d-127">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="3c57d-128">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3c57d-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-129">To-do item</span></span> | <span data-ttu-id="3c57d-130">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-130">None</span></span> |
|<span data-ttu-id="3c57d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="3c57d-132">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3c57d-133">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-133">None</span></span> | <span data-ttu-id="3c57d-134">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-134">None</span></span>|

<span data-ttu-id="3c57d-135">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-135">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="3c57d-141">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c57d-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3c57d-145">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="3c57d-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-147">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3c57d-148">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3c57d-149">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3c57d-150">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 5.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="3c57d-151">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-151">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3c57d-154">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3c57d-155">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3c57d-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="3c57d-157">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="3c57d-158">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-158">The preceding commands:</span></span>

  * <span data-ttu-id="3c57d-159">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="3c57d-160">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c57d-162">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-162">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3c57d-164">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="3c57d-165">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS での API テンプレートの選択](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="3c57d-167">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 5.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="3c57d-168">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-168">Select **Next**.</span></span>

* <span data-ttu-id="3c57d-169">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="3c57d-171">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="3c57d-172">プロジェクトをテストする</span><span class="sxs-lookup"><span data-stu-id="3c57d-172">Test the project</span></span>

<span data-ttu-id="3c57d-173">プロジェクト テンプレートにより、[Swagger](xref:tutorials/web-api-help-pages-using-swagger) をサポートする `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3c57d-175">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="3c57d-176">Visual Studio により、以下が起動されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-176">Visual Studio launches:</span></span>

* <span data-ttu-id="3c57d-177">IIS Express Web サーバー。</span><span class="sxs-lookup"><span data-stu-id="3c57d-177">The IIS Express web server.</span></span>
* <span data-ttu-id="3c57d-178">既定のブラウザーです。`https://localhost:<port>/swagger/index.html` に移動します。`<port>` はランダムに選択されるポート番号です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="3c57d-180">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3c57d-181">ブラウザーで、次の URL に移動します: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="3c57d-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3c57d-183">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3c57d-184">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3c57d-185">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3c57d-186">URL に `/swagger` を追加します (URL を `https://localhost:<port>/swagger` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="3c57d-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="3c57d-187">Swagger ページ `/swagger/index.html` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="3c57d-188">**[取得]**  >  **[試してみる]**  >  **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="3c57d-189">ページに以下が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-189">The page displays:</span></span>

* <span data-ttu-id="3c57d-190">WeatherForecast API をテストするための [Curl](https://curl.haxx.se/) コマンド。</span><span class="sxs-lookup"><span data-stu-id="3c57d-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="3c57d-191">WeatherForecast API をテストする URL。</span><span class="sxs-lookup"><span data-stu-id="3c57d-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="3c57d-192">応答コード、本文、およびヘッダー。</span><span class="sxs-lookup"><span data-stu-id="3c57d-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="3c57d-193">メディアの種類と、値とスキーマの例を含むドロップ ダウン リスト ボックス。</span><span class="sxs-lookup"><span data-stu-id="3c57d-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="3c57d-194">Swagger は、Web API の有用なドキュメントやヘルプ ページを生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="3c57d-195">このチュートリアルでは、Web API の作成について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="3c57d-196">Swagger の詳細については、<xref:tutorials/web-api-help-pages-using-swagger> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="3c57d-197">ブラウザーで **要求 URL** をコピーして貼り付けます: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="3c57d-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="3c57d-198">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="3c57d-199">launchUrl を更新する</span><span class="sxs-lookup"><span data-stu-id="3c57d-199">Update the launchUrl</span></span>

<span data-ttu-id="3c57d-200">*Properties\launchSettings.json* で、`launchUrl` を `"swagger"` から `"api/TodoItems"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="3c57d-201">Swagger が削除されているため、上記のマークアップにより、開始される URL が、次のセクションで追加されるコントローラーの GET メソッドに変更されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="3c57d-202">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-202">Add a model class</span></span>

<span data-ttu-id="3c57d-203">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3c57d-204">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-206">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3c57d-207">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3c57d-208">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="3c57d-209">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3c57d-210">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3c57d-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3c57d-213">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3c57d-214">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c57d-216">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-216">Right-click the project.</span></span> <span data-ttu-id="3c57d-217">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3c57d-218">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-218">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3c57d-220">*Models* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3c57d-221">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3c57d-222">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="3c57d-223">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3c57d-224">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="3c57d-225">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-225">Add a database context</span></span>

<span data-ttu-id="3c57d-226">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3c57d-227">このクラスは <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="3c57d-229">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="3c57d-229">Add NuGet packages</span></span>

* <span data-ttu-id="3c57d-230">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="3c57d-231">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.InMemory**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="3c57d-232">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.InMemory]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-232">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="3c57d-233">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="3c57d-235">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="3c57d-236">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3c57d-237">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-238">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3c57d-239">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3c57d-240">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="3c57d-241">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="3c57d-241">Register the database context</span></span>

<span data-ttu-id="3c57d-242">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3c57d-243">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="3c57d-244">次のコードを使用して *Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="3c57d-245">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-245">The preceding code:</span></span>

* <span data-ttu-id="3c57d-246">Swagger 呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="3c57d-247">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3c57d-248">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3c57d-249">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="3c57d-250">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="3c57d-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-252">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3c57d-253">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3c57d-254">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="3c57d-255">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="3c57d-256">**モデル クラス** で **TodoItem (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="3c57d-257">**データ コンテキスト クラス** で **TodoContext (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="3c57d-258">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-259">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3c57d-260">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="3c57d-261">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-261">The preceding commands:</span></span>

* <span data-ttu-id="3c57d-262">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="3c57d-263">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="3c57d-264">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="3c57d-265">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-265">The generated code:</span></span>

* <span data-ttu-id="3c57d-266">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="3c57d-267">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3c57d-268">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3c57d-269">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3c57d-270">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="3c57d-271">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="3c57d-272">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="3c57d-273">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="3c57d-274">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="3c57d-275">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="3c57d-276">PostTodoItem 作成メソッドの更新</span><span class="sxs-lookup"><span data-stu-id="3c57d-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="3c57d-277">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを更新します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="3c57d-278">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="3c57d-279">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3c57d-280">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3c57d-281"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="3c57d-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="3c57d-282">成功すると、[HTTP 201 状態コード](https://developer.mozilla.org/docs/Web/HTTP/Status/201)が返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="3c57d-283">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3c57d-284">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="3c57d-285">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="3c57d-286">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3c57d-287">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3c57d-288">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="3c57d-289">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="3c57d-289">Install Postman</span></span>

<span data-ttu-id="3c57d-290">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3c57d-291">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="3c57d-292">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-292">Start the web app.</span></span>
* <span data-ttu-id="3c57d-293">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-293">Start Postman.</span></span>
* <span data-ttu-id="3c57d-294">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="3c57d-295">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="3c57d-296">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="3c57d-297">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="3c57d-298">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-298">Create a new request.</span></span>
* <span data-ttu-id="3c57d-299">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3c57d-300">URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3c57d-301">たとえば、「 `https://localhost:5001/api/TodoItems` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3c57d-302">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="3c57d-303">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3c57d-304">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3c57d-305">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3c57d-306">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-306">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="3c57d-308">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-308">Test the location header URI</span></span>

<span data-ttu-id="3c57d-309">場所ヘッダー URI は、ブラウザーでテストできます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="3c57d-310">場所ヘッダー URI をコピーしてブラウザーに貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="3c57d-311">Postman でテストするには:</span><span class="sxs-lookup"><span data-stu-id="3c57d-311">To test in Postman:</span></span>

* <span data-ttu-id="3c57d-312">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3c57d-313">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-313">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="3c57d-315">HTTP メソッドを `GET` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="3c57d-316">URI を `https://localhost:<port>/api/TodoItems/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="3c57d-317">たとえば、「 `https://localhost:5001/api/TodoItems/1` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="3c57d-318">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="3c57d-319">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="3c57d-319">Examine the GET methods</span></span>

<span data-ttu-id="3c57d-320">2 つの GET エンドポイントが実装されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="3c57d-321">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="3c57d-322">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="3c57d-323">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="3c57d-324">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="3c57d-324">Test Get with Postman</span></span>

* <span data-ttu-id="3c57d-325">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-325">Create a new request.</span></span>
* <span data-ttu-id="3c57d-326">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="3c57d-327">要求 URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3c57d-328">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3c57d-329">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3c57d-330">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-330">Select **Send**.</span></span>

<span data-ttu-id="3c57d-331">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-331">This app uses an in-memory database.</span></span> <span data-ttu-id="3c57d-332">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="3c57d-333">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="3c57d-334">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="3c57d-334">Routing and URL paths</span></span>

<span data-ttu-id="3c57d-335">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3c57d-336">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3c57d-337">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="3c57d-338">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3c57d-339">このサンプルでは、コントローラー クラス名は **TodoItems** Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="3c57d-340">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3c57d-341">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3c57d-342">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-342">This sample doesn't use a template.</span></span> <span data-ttu-id="3c57d-343">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3c57d-344">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3c57d-345">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="3c57d-346">戻り値</span><span class="sxs-lookup"><span data-stu-id="3c57d-346">Return values</span></span>

<span data-ttu-id="3c57d-347">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3c57d-348">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3c57d-349">ハンドルされない例外がないと仮定すると、この戻り値の型の応答コードは [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200) です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3c57d-350">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3c57d-351">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3c57d-352">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3c57d-353">要求された ID に一致するアイテムがない場合、このメソッドにより [404 ステータス](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="3c57d-354">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3c57d-355">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="3c57d-356">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="3c57d-356">The PutTodoItem method</span></span>

<span data-ttu-id="3c57d-357">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="3c57d-358">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3c57d-359">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3c57d-360">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3c57d-361">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3c57d-362">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="3c57d-363">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="3c57d-364">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3c57d-365">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3c57d-366">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3c57d-367">Id = 1 の To Do アイテムを更新し、その名前を `"feed fish"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3c57d-368">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-368">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="3c57d-370">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="3c57d-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="3c57d-371">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="3c57d-372">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="3c57d-373">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3c57d-374">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3c57d-375">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="3c57d-376">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="3c57d-377">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="3c57d-377">Prevent over-posting</span></span>

<span data-ttu-id="3c57d-378">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="3c57d-379">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="3c57d-380">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="3c57d-381">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="3c57d-382">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="3c57d-383">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-383">A DTO may be used to:</span></span>

* <span data-ttu-id="3c57d-384">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-384">Prevent over-posting.</span></span>
* <span data-ttu-id="3c57d-385">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="3c57d-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="3c57d-386">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="3c57d-387">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="3c57d-388">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="3c57d-389">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="3c57d-390">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="3c57d-391">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="3c57d-392">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="3c57d-393">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="3c57d-394">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="3c57d-395">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="3c57d-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="3c57d-396">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3c57d-397">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c57d-398">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-398">Create a web API project.</span></span>
> * <span data-ttu-id="3c57d-399">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3c57d-400">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="3c57d-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="3c57d-401">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="3c57d-402">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="3c57d-402">Call the web API with Postman.</span></span>

<span data-ttu-id="3c57d-403">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="3c57d-404">概要</span><span class="sxs-lookup"><span data-stu-id="3c57d-404">Overview</span></span>

<span data-ttu-id="3c57d-405">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3c57d-406">API</span><span class="sxs-lookup"><span data-stu-id="3c57d-406">API</span></span> | <span data-ttu-id="3c57d-407">説明</span><span class="sxs-lookup"><span data-stu-id="3c57d-407">Description</span></span> | <span data-ttu-id="3c57d-408">要求本文</span><span class="sxs-lookup"><span data-stu-id="3c57d-408">Request body</span></span> | <span data-ttu-id="3c57d-409">応答本文</span><span class="sxs-lookup"><span data-stu-id="3c57d-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="3c57d-410">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-410">Get all to-do items</span></span> | <span data-ttu-id="3c57d-411">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-411">None</span></span> | <span data-ttu-id="3c57d-412">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="3c57d-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="3c57d-413">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-413">Get an item by ID</span></span> | <span data-ttu-id="3c57d-414">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-414">None</span></span> | <span data-ttu-id="3c57d-415">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="3c57d-416">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-416">Add a new item</span></span> | <span data-ttu-id="3c57d-417">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-417">To-do item</span></span> | <span data-ttu-id="3c57d-418">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="3c57d-419">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3c57d-420">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-420">To-do item</span></span> | <span data-ttu-id="3c57d-421">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-421">None</span></span> |
|<span data-ttu-id="3c57d-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="3c57d-423">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3c57d-424">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-424">None</span></span> | <span data-ttu-id="3c57d-425">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-425">None</span></span>|

<span data-ttu-id="3c57d-426">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-426">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="3c57d-432">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c57d-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-435">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3c57d-436">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="3c57d-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-438">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3c57d-439">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3c57d-440">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3c57d-441">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="3c57d-442">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-442">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3c57d-445">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3c57d-446">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3c57d-447">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="3c57d-448">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="3c57d-449">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-449">The preceding commands:</span></span>

  * <span data-ttu-id="3c57d-450">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="3c57d-451">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-452">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c57d-453">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-453">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3c57d-455">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="3c57d-456">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS での API テンプレートの選択](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="3c57d-458">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 3.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="3c57d-459">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-459">Select **Next**.</span></span>

* <span data-ttu-id="3c57d-460">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="3c57d-462">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="3c57d-463">API のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-463">Test the API</span></span>

<span data-ttu-id="3c57d-464">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="3c57d-465">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3c57d-467">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3c57d-468">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="3c57d-469">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="3c57d-470">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3c57d-472">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3c57d-473">ブラウザーで、次の URL に移動します: `https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="3c57d-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-474">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3c57d-475">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3c57d-476">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3c57d-477">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3c57d-478">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="3c57d-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="3c57d-479">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-479">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="3c57d-480">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-480">Add a model class</span></span>

<span data-ttu-id="3c57d-481">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3c57d-482">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-484">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3c57d-485">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3c57d-486">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="3c57d-487">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3c57d-488">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3c57d-489">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3c57d-491">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3c57d-492">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-493">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c57d-494">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-494">Right-click the project.</span></span> <span data-ttu-id="3c57d-495">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3c57d-496">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-496">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3c57d-498">*Models* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3c57d-499">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3c57d-500">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="3c57d-501">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3c57d-502">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="3c57d-503">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-503">Add a database context</span></span>

<span data-ttu-id="3c57d-504">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3c57d-505">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="3c57d-507">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="3c57d-507">Add NuGet packages</span></span>

* <span data-ttu-id="3c57d-508">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="3c57d-509">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.InMemory**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="3c57d-510">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.InMemory]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="3c57d-511">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="3c57d-513">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="3c57d-514">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3c57d-515">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-516">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3c57d-517">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3c57d-518">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="3c57d-519">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="3c57d-519">Register the database context</span></span>

<span data-ttu-id="3c57d-520">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3c57d-521">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="3c57d-522">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="3c57d-523">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-523">The preceding code:</span></span>

* <span data-ttu-id="3c57d-524">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3c57d-525">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3c57d-526">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="3c57d-527">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="3c57d-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-529">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3c57d-530">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3c57d-531">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="3c57d-532">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="3c57d-533">**モデル クラス** で **TodoItem (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="3c57d-534">**データ コンテキスト クラス** で **TodoContext (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="3c57d-535">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-536">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3c57d-537">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="3c57d-538">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-538">The preceding commands:</span></span>

* <span data-ttu-id="3c57d-539">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="3c57d-540">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="3c57d-541">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="3c57d-542">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-542">The generated code:</span></span>

* <span data-ttu-id="3c57d-543">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="3c57d-544">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3c57d-545">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3c57d-546">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3c57d-547">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="3c57d-548">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="3c57d-549">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="3c57d-550">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="3c57d-551">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="3c57d-552">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="3c57d-553">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="3c57d-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="3c57d-554">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="3c57d-555">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="3c57d-556">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3c57d-557">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3c57d-558"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="3c57d-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="3c57d-559">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="3c57d-560">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3c57d-561">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="3c57d-562">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="3c57d-563">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3c57d-564">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3c57d-565">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="3c57d-566">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="3c57d-566">Install Postman</span></span>

<span data-ttu-id="3c57d-567">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3c57d-568">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="3c57d-569">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-569">Start the web app.</span></span>
* <span data-ttu-id="3c57d-570">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-570">Start Postman.</span></span>
* <span data-ttu-id="3c57d-571">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="3c57d-572">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="3c57d-573">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="3c57d-574">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="3c57d-575">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-575">Create a new request.</span></span>
* <span data-ttu-id="3c57d-576">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3c57d-577">URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3c57d-578">たとえば、「 `https://localhost:5001/api/TodoItems` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3c57d-579">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="3c57d-580">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3c57d-581">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3c57d-582">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3c57d-583">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-583">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="3c57d-585">Postman で Location ヘッダーの URI をテストする</span><span class="sxs-lookup"><span data-stu-id="3c57d-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="3c57d-586">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3c57d-587">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-587">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="3c57d-589">HTTP メソッドを `GET` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="3c57d-590">URI を `https://localhost:<port>/api/TodoItems/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="3c57d-591">たとえば、「 `https://localhost:5001/api/TodoItems/1` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="3c57d-592">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="3c57d-593">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="3c57d-593">Examine the GET methods</span></span>

<span data-ttu-id="3c57d-594">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="3c57d-595">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="3c57d-596">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="3c57d-597">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="3c57d-598">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="3c57d-598">Test Get with Postman</span></span>

* <span data-ttu-id="3c57d-599">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-599">Create a new request.</span></span>
* <span data-ttu-id="3c57d-600">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="3c57d-601">要求 URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3c57d-602">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3c57d-603">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3c57d-604">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-604">Select **Send**.</span></span>

<span data-ttu-id="3c57d-605">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-605">This app uses an in-memory database.</span></span> <span data-ttu-id="3c57d-606">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="3c57d-607">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="3c57d-608">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="3c57d-608">Routing and URL paths</span></span>

<span data-ttu-id="3c57d-609">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3c57d-610">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3c57d-611">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="3c57d-612">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3c57d-613">このサンプルでは、コントローラー クラス名は **TodoItems** Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="3c57d-614">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3c57d-615">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3c57d-616">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-616">This sample doesn't use a template.</span></span> <span data-ttu-id="3c57d-617">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3c57d-618">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3c57d-619">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="3c57d-620">戻り値</span><span class="sxs-lookup"><span data-stu-id="3c57d-620">Return values</span></span> 

<span data-ttu-id="3c57d-621">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3c57d-622">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3c57d-623">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3c57d-624">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3c57d-625">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3c57d-626">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3c57d-627">要求された ID と一致するアイテムがない場合、メソッドは 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="3c57d-628">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3c57d-629">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="3c57d-630">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="3c57d-630">The PutTodoItem method</span></span>

<span data-ttu-id="3c57d-631">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="3c57d-632">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3c57d-633">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3c57d-634">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3c57d-635">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3c57d-636">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="3c57d-637">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="3c57d-638">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3c57d-639">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3c57d-640">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3c57d-641">Id = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3c57d-642">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-642">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="3c57d-644">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="3c57d-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="3c57d-645">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="3c57d-646">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="3c57d-647">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3c57d-648">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3c57d-649">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="3c57d-650">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="3c57d-651">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="3c57d-651">Prevent over-posting</span></span>

<span data-ttu-id="3c57d-652">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="3c57d-653">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="3c57d-654">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="3c57d-655">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="3c57d-656">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="3c57d-657">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-657">A DTO may be used to:</span></span>

* <span data-ttu-id="3c57d-658">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-658">Prevent over-posting.</span></span>
* <span data-ttu-id="3c57d-659">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="3c57d-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="3c57d-660">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="3c57d-661">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="3c57d-662">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="3c57d-663">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="3c57d-664">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="3c57d-665">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="3c57d-666">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="3c57d-667">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="3c57d-668">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="3c57d-669">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="3c57d-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="3c57d-670">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3c57d-671">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c57d-672">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-672">Create a web API project.</span></span>
> * <span data-ttu-id="3c57d-673">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3c57d-674">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-674">Add a controller.</span></span>
> * <span data-ttu-id="3c57d-675">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="3c57d-676">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="3c57d-677">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="3c57d-677">Specify return values.</span></span>
> * <span data-ttu-id="3c57d-678">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="3c57d-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="3c57d-679">JavaScript を使用した Web API の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="3c57d-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="3c57d-680">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="3c57d-681">概要 2.1</span><span class="sxs-lookup"><span data-stu-id="3c57d-681">Overview 2.1</span></span>

<span data-ttu-id="3c57d-682">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3c57d-683">API</span><span class="sxs-lookup"><span data-stu-id="3c57d-683">API</span></span> | <span data-ttu-id="3c57d-684">説明</span><span class="sxs-lookup"><span data-stu-id="3c57d-684">Description</span></span> | <span data-ttu-id="3c57d-685">要求本文</span><span class="sxs-lookup"><span data-stu-id="3c57d-685">Request body</span></span> | <span data-ttu-id="3c57d-686">応答本文</span><span class="sxs-lookup"><span data-stu-id="3c57d-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="3c57d-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3c57d-687">GET /api/TodoItems</span></span> | <span data-ttu-id="3c57d-688">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-688">Get all to-do items</span></span> | <span data-ttu-id="3c57d-689">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-689">None</span></span> | <span data-ttu-id="3c57d-690">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="3c57d-690">Array of to-do items</span></span>|
|<span data-ttu-id="3c57d-691">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3c57d-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="3c57d-692">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-692">Get an item by ID</span></span> | <span data-ttu-id="3c57d-693">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-693">None</span></span> | <span data-ttu-id="3c57d-694">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-694">To-do item</span></span>|
|<span data-ttu-id="3c57d-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3c57d-695">POST /api/TodoItems</span></span> | <span data-ttu-id="3c57d-696">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-696">Add a new item</span></span> | <span data-ttu-id="3c57d-697">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-697">To-do item</span></span> | <span data-ttu-id="3c57d-698">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-698">To-do item</span></span> |
|<span data-ttu-id="3c57d-699">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3c57d-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="3c57d-700">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3c57d-701">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="3c57d-701">To-do item</span></span> | <span data-ttu-id="3c57d-702">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-702">None</span></span> |
|<span data-ttu-id="3c57d-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="3c57d-704">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3c57d-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3c57d-705">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-705">None</span></span> | <span data-ttu-id="3c57d-706">None</span><span class="sxs-lookup"><span data-stu-id="3c57d-706">None</span></span>|

<span data-ttu-id="3c57d-707">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-707">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="3c57d-713">前提条件 2.1</span><span class="sxs-lookup"><span data-stu-id="3c57d-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-716">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="3c57d-717">Web プロジェクト 2.1 の作成</span><span class="sxs-lookup"><span data-stu-id="3c57d-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-719">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3c57d-720">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3c57d-721">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3c57d-722">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="3c57d-723">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="3c57d-724">**[Enable Docker Support]\(Docker サポートを有効にする\)** は **選択しないで** ください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-724">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3c57d-727">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3c57d-728">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3c57d-729">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="3c57d-730">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="3c57d-731">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-732">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c57d-733">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-733">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3c57d-735">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="3c57d-736">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="3c57d-737">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 2.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="3c57d-738">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-738">Select **Next**.</span></span>

* <span data-ttu-id="3c57d-739">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="3c57d-741">API 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-741">Test the API 2.1</span></span>

<span data-ttu-id="3c57d-742">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-742">The project template creates a `values` API.</span></span> <span data-ttu-id="3c57d-743">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3c57d-745">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3c57d-746">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="3c57d-747">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="3c57d-748">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3c57d-750">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3c57d-751">ブラウザーで、次の URL に移動します: `https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="3c57d-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-752">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3c57d-753">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3c57d-754">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3c57d-755">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3c57d-756">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="3c57d-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="3c57d-757">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="3c57d-758">モデル クラス 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-758">Add a model class 2.1</span></span>

<span data-ttu-id="3c57d-759">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3c57d-760">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-762">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3c57d-763">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3c57d-764">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="3c57d-765">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3c57d-766">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3c57d-767">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3c57d-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c57d-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3c57d-769">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3c57d-770">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-771">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c57d-772">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-772">Right-click the project.</span></span> <span data-ttu-id="3c57d-773">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3c57d-774">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-774">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3c57d-776">*Models* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3c57d-777">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3c57d-778">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="3c57d-779">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3c57d-780">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="3c57d-781">データベース コンテキスト 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-781">Add a database context 2.1</span></span>

<span data-ttu-id="3c57d-782">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3c57d-783">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-785">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3c57d-786">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-787">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3c57d-788">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3c57d-789">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="3c57d-790">データベース コンテキスト 2.1 の登録</span><span class="sxs-lookup"><span data-stu-id="3c57d-790">Register the database context 2.1</span></span>

<span data-ttu-id="3c57d-791">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3c57d-792">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="3c57d-793">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="3c57d-794">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-794">The preceding code:</span></span>

* <span data-ttu-id="3c57d-795">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3c57d-796">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3c57d-797">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="3c57d-798">コントローラー 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-800">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3c57d-801">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="3c57d-802">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="3c57d-803">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-805">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3c57d-806">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="3c57d-807">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="3c57d-808">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-808">The preceding code:</span></span>

* <span data-ttu-id="3c57d-809">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="3c57d-810">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="3c57d-811">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3c57d-812">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3c57d-813">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3c57d-814">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="3c57d-815">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="3c57d-816">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="3c57d-817">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="3c57d-818">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="3c57d-819">Get メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-819">Add Get methods 2.1</span></span>

<span data-ttu-id="3c57d-820">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="3c57d-821">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="3c57d-822">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-822">Stop the app if it's still running.</span></span> <span data-ttu-id="3c57d-823">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="3c57d-824">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="3c57d-825">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="3c57d-826">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="3c57d-827">ルーティングと URL パス 2.1</span><span class="sxs-lookup"><span data-stu-id="3c57d-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="3c57d-828">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3c57d-829">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3c57d-830">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="3c57d-831">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3c57d-832">このサンプルでは、コントローラー クラス名は **Todo** Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="3c57d-833">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3c57d-834">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3c57d-835">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="3c57d-835">This sample doesn't use a template.</span></span> <span data-ttu-id="3c57d-836">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3c57d-837">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3c57d-838">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="3c57d-839">戻り値 2.1</span><span class="sxs-lookup"><span data-stu-id="3c57d-839">Return values 2.1</span></span>

<span data-ttu-id="3c57d-840">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3c57d-841">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3c57d-842">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3c57d-843">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3c57d-844">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3c57d-845">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3c57d-846">要求された ID と一致するアイテムがない場合、メソッドは 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="3c57d-847">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3c57d-848">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="3c57d-849">GetTodoItems メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="3c57d-850">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3c57d-851">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="3c57d-852">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-852">Start the web app.</span></span>
* <span data-ttu-id="3c57d-853">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-853">Start Postman.</span></span>
* <span data-ttu-id="3c57d-854">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3c57d-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c57d-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c57d-856">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3c57d-857">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3c57d-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3c57d-858">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="3c57d-859">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="3c57d-860">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="3c57d-861">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-861">Create a new request.</span></span>
  * <span data-ttu-id="3c57d-862">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="3c57d-863">要求 URI を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="3c57d-864">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="3c57d-865">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3c57d-866">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-866">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="3c57d-868">Create メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-868">Add a Create method 2.1</span></span>

<span data-ttu-id="3c57d-869">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="3c57d-870">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="3c57d-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="3c57d-871">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3c57d-872">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="3c57d-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="3c57d-873">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="3c57d-874">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="3c57d-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3c57d-875">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="3c57d-876">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="3c57d-877">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3c57d-878">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3c57d-879">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="3c57d-880">PostTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="3c57d-881">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-881">Build the project.</span></span>
* <span data-ttu-id="3c57d-882">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3c57d-883">URI を `https://localhost:<port>/api/Todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="3c57d-884">たとえば、「 `https://localhost:5001/api/Todo` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="3c57d-885">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="3c57d-886">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3c57d-887">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3c57d-888">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3c57d-889">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-889">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="3c57d-891">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="3c57d-892">場所ヘッダー URI 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="3c57d-893">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3c57d-894">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="3c57d-894">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="3c57d-896">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-896">Set the method to GET.</span></span>
* <span data-ttu-id="3c57d-897">URI を `https://localhost:<port>/api/TodoItems/2` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="3c57d-898">たとえば、「 `https://localhost:5001/api/TodoItems/2` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="3c57d-899">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="3c57d-900">PutTodoItem メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="3c57d-901">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="3c57d-902">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3c57d-903">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3c57d-904">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3c57d-905">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3c57d-906">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="3c57d-907">PutTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="3c57d-908">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3c57d-909">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3c57d-910">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3c57d-911">Id = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3c57d-912">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-912">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="3c57d-914">DeleteTodoItem メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="3c57d-915">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="3c57d-916">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="3c57d-917">DeleteTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="3c57d-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="3c57d-918">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3c57d-919">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3c57d-920">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="3c57d-921">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-921">Select **Send**.</span></span>

<span data-ttu-id="3c57d-922">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="3c57d-923">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="3c57d-924">JavaScript 2.1 を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="3c57d-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="3c57d-925">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="3c57d-926">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-926">jQuery initiates the request.</span></span> <span data-ttu-id="3c57d-927">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="3c57d-928">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)し、[既定のファイル マッピングを有効にする](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="3c57d-929">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="3c57d-930">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="3c57d-931">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="3c57d-932">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="3c57d-933">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="3c57d-934">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="3c57d-935">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="3c57d-936">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="3c57d-937">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="3c57d-938">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="3c57d-939">To Do アイテム 2.1 のリストの取得</span><span class="sxs-lookup"><span data-stu-id="3c57d-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="3c57d-940">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="3c57d-941">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="3c57d-942">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="3c57d-943">To Do アイテム 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="3c57d-944">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="3c57d-945">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="3c57d-946">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="3c57d-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="3c57d-947">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="3c57d-948">To Do アイテム 2.1 の更新</span><span class="sxs-lookup"><span data-stu-id="3c57d-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="3c57d-949">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="3c57d-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="3c57d-950">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="3c57d-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="3c57d-951">To Do アイテム 2.1 の削除</span><span class="sxs-lookup"><span data-stu-id="3c57d-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="3c57d-952">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="3c57d-953">Web API 2.1 への認証サポートの追加</span><span class="sxs-lookup"><span data-stu-id="3c57d-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="3c57d-954">その他のリソース 2.1</span><span class="sxs-lookup"><span data-stu-id="3c57d-954">Additional resources 2.1</span></span>

<span data-ttu-id="3c57d-955">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="3c57d-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="3c57d-956">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="3c57d-957">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c57d-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="3c57d-958">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="3c57d-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

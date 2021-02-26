---
title: 'パート 2: ASP.NET Core MVC アプリにコントローラーを追加する'
author: rick-anderson
description: ASP.NET Core MVC のチュートリアル シリーズのパート 2。
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975262"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="e2b0f-103">パート 2: ASP.NET Core MVC アプリにコントローラーを追加する</span><span class="sxs-lookup"><span data-stu-id="e2b0f-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="e2b0f-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e2b0f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2b0f-105">モデル ビュー コントローラー (MVC) アーキテクチャ パターンでは、アプリが 3 つの主要なコンポーネントに分けられます。**モデル**、**ビュー**、**コントローラー**。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="e2b0f-106">MVC パターンでは、よりテスト可能で、従来のモノリシック アプリより更新しやすいアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="e2b0f-107">MVC ベースのアプリには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="e2b0f-108">**モデル**: アプリのデータを表すクラス。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="e2b0f-109">モデル クラスでは検証ロジックを使用して、そのデータにビジネス ルールを適用します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="e2b0f-110">通常、モデル オブジェクトはモデルの状態を取得して、データベースに格納します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="e2b0f-111">このチュートリアルでは、`Movie` モデルはデータベースからムービーデータを取得し、それをビューに提供するか、更新します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="e2b0f-112">更新されたデータはデータベースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="e2b0f-113">**ビュー**: ビューは、アプリのユーザー インターフェイス (UI) を表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="e2b0f-114">一般に、この UI ではモデル データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="e2b0f-115">**コントローラー**: 次を行うクラスです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="e2b0f-116">ブラウザーの要求を処理する。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-116">Handle browser requests.</span></span>
  * <span data-ttu-id="e2b0f-117">モデル データを取得する。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-117">Retrieve model data.</span></span>
  * <span data-ttu-id="e2b0f-118">応答を返すビュー テンプレートを呼び出す。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-118">Call view templates that return a response.</span></span>

<span data-ttu-id="e2b0f-119">MVC アプリでは、ビューに情報のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="e2b0f-120">コントローラーによってユーザーの入力と操作が処理され応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="e2b0f-121">たとえば、コントローラーによって URL セグメントとクエリ文字列の値が処理され、それらの値がモデルに渡されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="e2b0f-122">モデルはこれらの値を使用して、データベースを照会する場合があります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-122">The model might use these values to query the database.</span></span> <span data-ttu-id="e2b0f-123">例:</span><span class="sxs-lookup"><span data-stu-id="e2b0f-123">For example:</span></span>

* <span data-ttu-id="e2b0f-124">`Https://localhost:5001/Home/Privacy`: `Home` コントローラーと `Privacy` アクションを指定します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="e2b0f-125">`Https://localhost:5001/Movies/Edit/5`: `Movies` コントローラーと `Edit` アクションを使用して ID = 5 のムービーを編集するための要求です。これについては、このチュートリアルで後ほど詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="e2b0f-126">ルート データについては、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="e2b0f-127">アプリは、MVC アーキテクチャ パターンによって、モデル、ビュー、コントローラーという 3 つの主要なコンポーネントのグループに分けられます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="e2b0f-128">このパターンは、UI ロジックがビューに属している、という点で、関心の分離を実現するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="e2b0f-129">入力ロジックはコントローラーに属しています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="e2b0f-130">ビジネス ロジックはモデルに属しています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-130">Business logic belongs in the model.</span></span> <span data-ttu-id="e2b0f-131">このように分離することで、他のコードに影響を与えることなく、一度に実装の 1 つの側面の作業に専念できるため、アプリを構築するときの複雑さが管理しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="e2b0f-132">たとえば、ビジネス ロジック コードに依存することなく、ビュー コードに専念できます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="e2b0f-133">このチュートリアル シリーズでは、ムービー アプリを構築しながら、これらの概念について紹介しデモを行います。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="e2b0f-134">MVC プロジェクトには、*Controllers* と *Views* の各フォルダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e2b0f-135">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="e2b0f-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2b0f-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2b0f-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2b0f-137">**ソリューション エクスプローラー** で、 **[Controllers] を右クリックし、[追加]、[コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![ソリューション エクスプローラーで、[Controllers] を右クリックし、[追加]、[コントローラー] の順に選択する](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="e2b0f-139">**[スキャフォールディングを追加]** ダイアログ ボックスで、 **[MVC コント ローラー - 空]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![MVC コント ローラーを追加し、名前を付けます](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="e2b0f-141">**[新しい項目の追加 - MvcMovie]** ダイアログで、「**HelloWorldController.cs**」と入力し、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2b0f-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2b0f-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2b0f-143">**[エクスプローラー]** アイコンで、 **[コントローラー] を右クリックして [新しいファイル] を選択し**、新しいファイルに「*HelloWorldController.cs*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![コンテキスト メニュー](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2b0f-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e2b0f-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2b0f-146">**ソリューション エクスプローラー** で、 **[Controllers] を右クリックし、[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![コンテキスト メニュー](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="e2b0f-148">**[ASP.NET Core]** と **[コントローラー クラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="e2b0f-149">コントローラーに「**HelloWorldController**」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-149">Name the controller **HelloWorldController**.</span></span>

![MVC コント ローラーを追加し、名前を付けます](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="e2b0f-151">*Controllers/HelloWorldController.cs* の内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="e2b0f-152">コントローラーのすべての `public` メソッドが、HTTP エンドポイントとして呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="e2b0f-153">上のサンプルでは、両方のメソッドが文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="e2b0f-154">各メソッドの前のコメントに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="e2b0f-155">HTTP エンドポイント:</span><span class="sxs-lookup"><span data-stu-id="e2b0f-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="e2b0f-156">Web アプリケーションのターゲット設定可能な URL です (`https://localhost:5001/HelloWorld` など)。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="e2b0f-157">組み合わせ:</span><span class="sxs-lookup"><span data-stu-id="e2b0f-157">Combines:</span></span>
  * <span data-ttu-id="e2b0f-158">使用されるプロトコル: `HTTPS`。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="e2b0f-159">TCP ポートなど、Web サーバーのネットワークの場所: `localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="e2b0f-160">ターゲット URI: `HelloWorld`</span><span class="sxs-lookup"><span data-stu-id="e2b0f-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="e2b0f-161">1 番目のコメントは、これが [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) メソッドであり、ベース URL に `/HelloWorld/` を追加することによって呼び出されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="e2b0f-162">2 番目のコメントでは、URL に `/HelloWorld/Welcome/` を追加することによって呼び出される [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) メソッドが示されています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="e2b0f-163">このチュートリアルではこの後、スキャフォールディング エンジンを使用して、データを更新する `HTTP POST` メソッドを生成します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="e2b0f-164">デバッガーなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-164">Run the app without the debugger.</span></span>

<span data-ttu-id="e2b0f-165">アドレス バーのパスに "HelloWorld" を追加します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="e2b0f-166">`Index` メソッドが文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-166">The `Index` method returns a string.</span></span>

!["This is my default action" というアプリの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="e2b0f-168">着信 URL に応じて、コントローラー クラスおよびそれらに含まれるアクション メソッドが MVC によって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="e2b0f-169">MVC によって使用される既定の [URL ルーティング ロジック](xref:mvc/controllers/routing)では、次のような形式を使用して、呼び出すコードが決定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="e2b0f-170">ルーティングの形式は、*Startup.cs* ファイル内の`Configure` メソッドで設定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="e2b0f-171">URL セグメントを指定しないでアプリを参照すると、既定では、"Home" コントローラーと "Index" メソッドが、上の強調表示されている template 行で指定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="e2b0f-172">上記の URL セグメントでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="e2b0f-173">1 番目の URL セグメントでは、実行するコントローラー クラスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="e2b0f-174">そのため、`localhost:5001/HelloWorld` は、**HelloWorld** コントローラー クラスにマップされます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="e2b0f-175">URL セグメントの 2 番目の部分では、クラスのアクション メソッドが決定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="e2b0f-176">したがって、`localhost:5001/HelloWorld/Index` を使用すると、`HelloWorldController` クラスの `Index` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="e2b0f-177">参照する必要があるのは `localhost:5001/HelloWorld` だけであり、`Index` メソッドは既定で呼び出されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="e2b0f-178">`Index` はメソッド名が明示的に指定されていない場合にコントローラーで呼び出される既定のメソッドです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="e2b0f-179">URL セグメントの 3 番目の部分 (`id`) はルート データ用です。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="e2b0f-180">ルート データについては、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="e2b0f-181">`https://localhost:{PORT}/HelloWorld/Welcome` を参照します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="e2b0f-182">`{PORT}` は実際のポート番号に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="e2b0f-183">`Welcome` メソッドが実行され、文字列 `This is the Welcome action method...` が返されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="e2b0f-184">この URL では、コントローラーは `HelloWorld` で、`Welcome` がアクション メソッドです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="e2b0f-185">URL の `[Parameters]` の部分はまだ使っていません。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

!["This is the Welcome action method" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="e2b0f-187">URL からコントローラーにいくつかのパラメーター情報を渡すように、コードを変更します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="e2b0f-188">たとえば、`/HelloWorld/Welcome?name=Rick&numtimes=4` のようにします。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="e2b0f-189">次のコードで示すように、2 つのパラメーターを含むように `Welcome` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="e2b0f-190">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-190">The preceding code:</span></span>

* <span data-ttu-id="e2b0f-191">C# のオプション パラメーター機能を使って、`numTimes` パラメーターに値が渡されない場合の既定値が 1 であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="e2b0f-192">`HtmlEncoder.Default.Encode` を使用して、JavaScript などによる悪意のある入力からアプリを保護します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="e2b0f-193">`$"Hello {name}, NumTimes is: {numTimes}"` 内で[補間文字列](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)を使います。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="e2b0f-194">アプリを実行して、`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4` を参照します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="e2b0f-195">`{PORT}` は実際のポート番号に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="e2b0f-196">URL の `name` と `numtimes` に違う値を指定してみてください。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="e2b0f-197">MVC の[モデル バインド](xref:mvc/models/model-binding) システムによって、名前付きパラメーターがアドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="e2b0f-198">詳しくは、「[モデル バインド](xref:mvc/models/model-binding)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

!["Hello Rick, NumTimes is\: 4" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="e2b0f-200">上図では、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-200">In the previous image:</span></span>

* <span data-ttu-id="e2b0f-201">URL セグメント `Parameters` は使用されません。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="e2b0f-202">`name` および `numTimes` パラメーターは、[クエリ文字列 ](https://wikipedia.org/wiki/Query_string) で渡されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="e2b0f-203">上の URL の `?` (疑問符) は区切り記号であり、後にクエリ文字列が続きます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="e2b0f-204">`&` 文字を使ってフィールドと値のペアを区切ります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="e2b0f-205">`Welcome` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="e2b0f-206">アプリを実行し、次の URL を入力します: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="e2b0f-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="e2b0f-207">上のコード URL では、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-207">In the preceding URL:</span></span>

* <span data-ttu-id="e2b0f-208">3 番目の URL セグメントがルート パラメーター `id` と一致しました。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="e2b0f-209">`Welcome` メソッドには、`MapControllerRoute` メソッドの URL テンプレートと一致したパラメーター `id` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="e2b0f-210">末尾の `?` (`id?`) は、`id` パラメーターが省略可能であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="e2b0f-211">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="e2b0f-211">In the preceding example:</span></span>

* <span data-ttu-id="e2b0f-212">3 番目の URL セグメントがルート パラメーター `id` と一致しました。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="e2b0f-213">`Welcome` メソッドには、`MapControllerRoute` メソッドの URL テンプレートと一致したパラメーター `id` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="e2b0f-214">末尾の `?` (`id?`) は、`id` パラメーターが省略可能であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2b0f-215">[前へ: 作業の開始](start-mvc.md)
> [次へ: ビューを追加する](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="e2b0f-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e2b0f-216">モデル ビュー コントローラー (MVC) アーキテクチャ パターンでは、アプリが 3 つの主要なコンポーネントに分けられます。**モデル**、**ビュー**、**コントローラー**。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="e2b0f-217">MVC パターンでは、よりテスト可能で、従来のモノリシック アプリより更新しやすいアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="e2b0f-218">MVC ベースのアプリには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="e2b0f-219">**モデル**: アプリのデータを表すクラス。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="e2b0f-220">モデル クラスでは検証ロジックを使用して、そのデータにビジネス ルールを適用します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="e2b0f-221">通常、モデル オブジェクトはモデルの状態を取得して、データベースに格納します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="e2b0f-222">このチュートリアルでは、`Movie` モデルはデータベースからムービーデータを取得し、それをビューに提供するか、更新します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="e2b0f-223">更新されたデータはデータベースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="e2b0f-224">**ビュー**: ビューは、アプリのユーザー インターフェイス (UI) を表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="e2b0f-225">一般に、この UI ではモデル データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="e2b0f-226">**コントローラー**: ブラウザー要求を処理するクラスです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="e2b0f-227">モデル データを取得し、応答を返すビュー テンプレートを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="e2b0f-228">MVC アプリでは、ビューに情報のみが表示され、コントローラーがユーザーの入力と操作を処理して応答します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="e2b0f-229">たとえば、コントローラーはルート データとクエリ文字列の値を処理し、これらの値をモデルに渡します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="e2b0f-230">モデルはこれらの値を使用して、データベースを照会する場合があります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-230">The model might use these values to query the database.</span></span> <span data-ttu-id="e2b0f-231">たとえば、`https://localhost:5001/Home/About` には、`Home` (コントローラー) と `About` (ホーム コントローラーに対して呼び出すアクション メソッド) のルート データがあります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="e2b0f-232">`https://localhost:5001/Movies/Edit/5` は、ムービー コントローラーを使用して ID=5 のムービーを編集する要求です。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="e2b0f-233">ルート データについては、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="e2b0f-234">MVC パターンは、これらの要素間の疎結合を提供しながら、アプリのさまざまな側面 (入力ロジック、ビジネス ロジック、および UI ロジック) を分離するアプリを作成するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="e2b0f-235">このパターンは、アプリで各種類のロジックを配置する必要がある場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="e2b0f-236">UI ロジックはビューに属しています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="e2b0f-237">入力ロジックはコントローラーに属しています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="e2b0f-238">ビジネス ロジックはモデルに属しています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-238">Business logic belongs in the model.</span></span> <span data-ttu-id="e2b0f-239">このように分離することで、他のコードに影響を与えることなく、実装の 1 つの側面に専念できるため、アプリを構築するときの複雑さが管理しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="e2b0f-240">たとえば、ビジネス ロジック コードに依存することなく、ビュー コードに専念できます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="e2b0f-241">このチュートリアルで示すこれらの概念を使用して、ムービー アプリを構築する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="e2b0f-242">MVC プロジェクトには、*Controllers* と *Views* の各フォルダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e2b0f-243">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="e2b0f-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2b0f-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2b0f-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e2b0f-245">**ソリューション エクスプローラー** で、 **[Controllers] を右クリックし、[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![コンテキスト メニュー](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="e2b0f-247">**[スキャフォールディングを追加]** ダイアログ ボックスで、 **[MVC コント ローラー - 空]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![MVC コント ローラーを追加し、名前を付けます](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="e2b0f-249">**[Add Empty MVC Controller]\(空の MVC コント ローラーの追加\)** ダイアログで、「**HelloWorldController**」と入力して、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e2b0f-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2b0f-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2b0f-251">**[エクスプローラー]** アイコンで、 **[コントローラー] を右クリックして [新しいファイル] を選択し**、新しいファイルに「*HelloWorldController.cs*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![コンテキスト メニュー](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2b0f-253">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e2b0f-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2b0f-254">**ソリューション エクスプローラー** で、 **[Controllers] を右クリックし、[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![コンテキスト メニュー](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="e2b0f-256">**[ASP.NET Core]** と **[MVC コントローラー クラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="e2b0f-257">コントローラーに「**HelloWorldController**」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-257">Name the controller **HelloWorldController**.</span></span>

![MVC コント ローラーを追加し、名前を付けます](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="e2b0f-259">*Controllers/HelloWorldController.cs* の内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="e2b0f-260">コントローラーのすべての `public` メソッドが、HTTP エンドポイントとして呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="e2b0f-261">上のサンプルでは、両方のメソッドが文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="e2b0f-262">各メソッドの前のコメントに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="e2b0f-263">HTTP エンドポイントは、Web アプリケーション内のターゲット設定可能な URL (`https://localhost:5001/HelloWorld` など) であり、使われているプロトコル (`HTTPS`)、Web サーバーの (TCP ポートを含む) ネットワーク上の場所 (`localhost:5001`)、ターゲットの URI (`HelloWorld`) を組み合わせたものです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="e2b0f-264">1 番目のコメントは、これが [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) メソッドであり、ベース URL に `/HelloWorld/` を追加することによって呼び出されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="e2b0f-265">2 番目のコメントでは、URL に `/HelloWorld/Welcome/` を追加することによって呼び出される [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) メソッドが示されています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="e2b0f-266">このチュートリアルではこの後、スキャフォールディング エンジンを使って、データを更新する `HTTP POST` メソッドを生成します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="e2b0f-267">非デバッグ モードでアプリを実行し、アドレス バーのパスに "HelloWorld" を追加します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="e2b0f-268">`Index` メソッドが文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-268">The `Index` method returns a string.</span></span>

!["This is my default action" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="e2b0f-270">MVC は、着信 URL に応じてコントローラー クラス (およびそれらに含まれるアクション メソッド) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="e2b0f-271">MVC によって使われる既定の [URL ルーティング ロジック](xref:mvc/controllers/routing)では、次のような形式を使って呼び出すコードが決定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="e2b0f-272">ルーティングの形式は、*Startup.cs* ファイル内の`Configure` メソッドで設定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="e2b0f-273">URL セグメントを指定しないでアプリを参照すると、既定では、"Home" コントローラーと "Index" メソッドが、上の強調表示されている template 行で指定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="e2b0f-274">1 番目の URL セグメントでは、実行するコントローラー クラスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="e2b0f-275">したがって、`localhost:{PORT}/HelloWorld` は `HelloWorldController` クラスにマップします。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="e2b0f-276">URL セグメントの 2 番目の部分では、クラスのアクション メソッドが決定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="e2b0f-277">したがって、`localhost:{PORT}/HelloWorld/Index` では `HelloWorldController` クラスの `Index` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="e2b0f-278">参照する必要があるのは `localhost:{PORT}/HelloWorld` だけであり、`Index` メソッドは既定で呼び出されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="e2b0f-279">これは、`Index` はメソッド名が明示的に指定されていない場合にコントローラーで呼び出される既定のメソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="e2b0f-280">URL セグメントの 3 番目の部分 (`id`) はルート データ用です。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="e2b0f-281">ルート データについては、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="e2b0f-282">`https://localhost:{PORT}/HelloWorld/Welcome` を参照します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="e2b0f-283">`Welcome` メソッドが実行され、文字列 `This is the Welcome action method...` が返されます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="e2b0f-284">この URL では、コントローラーは `HelloWorld` で、`Welcome` がアクション メソッドです。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="e2b0f-285">URL の `[Parameters]` の部分はまだ使っていません。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

!["This is the Welcome action method" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="e2b0f-287">URL からコントローラーにいくつかのパラメーター情報を渡すように、コードを変更します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="e2b0f-288">たとえば、`/HelloWorld/Welcome?name=Rick&numtimes=4` のようにします。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="e2b0f-289">次のコードで示すように、2 つのパラメーターを含むように `Welcome` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="e2b0f-290">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-290">The preceding code:</span></span>

* <span data-ttu-id="e2b0f-291">C# のオプション パラメーター機能を使って、`numTimes` パラメーターに値が渡されない場合の既定値が 1 であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="e2b0f-292">`HtmlEncoder.Default.Encode` を使って、悪意のある入力 (つまり JavaScript) からアプリを保護します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="e2b0f-293">`$"Hello {name}, NumTimes is: {numTimes}"` 内で[補間文字列](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)を使います。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="e2b0f-294">アプリを実行して次を参照します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="e2b0f-295">(`{PORT}` は実際のポート番号に置き換えます。)URL の `name` と `numtimes` に違う値を指定してみてください。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="e2b0f-296">MVC の[モデル バインド](xref:mvc/models/model-binding) システムは、名前付きパラメーターを、アドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="e2b0f-297">詳しくは、「[モデル バインド](xref:mvc/models/model-binding)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

!["Hello Rick, NumTimes is\: 4" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="e2b0f-299">上の図では、URL セグメント (`Parameters`) は使われておらず、`name` および `numTimes` パラメーターは[クエリ文字列](https://wikipedia.org/wiki/Query_string)で渡されています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="e2b0f-300">上の URL の `?` (疑問符) は区切り記号であり、後にクエリ文字列が続きます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="e2b0f-301">`&` 文字を使ってフィールドと値のペアを区切ります。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="e2b0f-302">`Welcome` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="e2b0f-303">アプリを実行し、次の URL を入力します: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="e2b0f-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="e2b0f-304">今度は、3 番目の URL セグメントがルート パラメーター `id` と一致しました。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="e2b0f-305">`Welcome` メソッドには、`MapRoute` メソッドの URL テンプレートと一致したパラメーター `id` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="e2b0f-306">末尾の `?` (`id?`) は、`id` パラメーターが省略可能であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="e2b0f-307">これらの例では、コントローラーによって MVC の "VC" 部分が実行されています。つまり、ビューとコントローラーが動作します。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="e2b0f-308">コントローラーは HTML を直接返しています。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="e2b0f-309">一般に、コントローラーが HTML を直接返すのは、コーディングと保守が非常に面倒になるので、望ましくありません。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="e2b0f-310">代わりに、通常は、別の Razor ビュー テンプレート ファイルを使って、HTML 応答を生成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="e2b0f-311">これは次のチュートリアルで行います。</span><span class="sxs-lookup"><span data-stu-id="e2b0f-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2b0f-312">[前へ](start-mvc.md)
> [次へ](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="e2b0f-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end

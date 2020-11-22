---
title: 'パート 2: ASP.NET Core MVC アプリにコントローラーを追加する'
author: rick-anderson
description: ASP.NET Core MVC のチュートリアル シリーズのパート 2。
ms.author: riande
ms.date: 11/12/2020
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
ms.openlocfilehash: e51edc15b14a5bdd1d53e547e0b469ad608f46d0
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688409"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="178ee-103">パート 2: ASP.NET Core MVC アプリにコントローラーを追加する</span><span class="sxs-lookup"><span data-stu-id="178ee-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="178ee-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="178ee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="178ee-105">モデル ビュー コントローラー (MVC) アーキテクチャ パターンでは、アプリが 3 つの主要なコンポーネントに分けられます。**モデル**、**ビュー**、**コントローラー**。</span><span class="sxs-lookup"><span data-stu-id="178ee-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="178ee-106">MVC パターンでは、よりテスト可能で、従来のモノリシック アプリより更新しやすいアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="178ee-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="178ee-107">MVC ベースのアプリには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="178ee-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="178ee-108">**モデル**: アプリのデータを表すクラス。</span><span class="sxs-lookup"><span data-stu-id="178ee-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="178ee-109">モデル クラスでは検証ロジックを使用して、そのデータにビジネス ルールを適用します。</span><span class="sxs-lookup"><span data-stu-id="178ee-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="178ee-110">通常、モデル オブジェクトはモデルの状態を取得して、データベースに格納します。</span><span class="sxs-lookup"><span data-stu-id="178ee-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="178ee-111">このチュートリアルでは、`Movie` モデルはデータベースからムービーデータを取得し、それをビューに提供するか、更新します。</span><span class="sxs-lookup"><span data-stu-id="178ee-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="178ee-112">更新されたデータはデータベースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="178ee-112">Updated data is written to a database.</span></span>

* <span data-ttu-id="178ee-113">**ビュー**: ビューは、アプリのユーザー インターフェイス (UI) を表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="178ee-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="178ee-114">一般に、この UI ではモデル データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-114">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="178ee-115">**コントローラー**: ブラウザー要求を処理するクラスです。</span><span class="sxs-lookup"><span data-stu-id="178ee-115">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="178ee-116">モデル データを取得し、応答を返すビュー テンプレートを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="178ee-116">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="178ee-117">MVC アプリでは、ビューに情報のみが表示され、コントローラーがユーザーの入力と操作を処理して応答します。</span><span class="sxs-lookup"><span data-stu-id="178ee-117">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="178ee-118">たとえば、コントローラーはルート データとクエリ文字列の値を処理し、これらの値をモデルに渡します。</span><span class="sxs-lookup"><span data-stu-id="178ee-118">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="178ee-119">モデルはこれらの値を使用して、データベースを照会する場合があります。</span><span class="sxs-lookup"><span data-stu-id="178ee-119">The model might use these values to query the database.</span></span> <span data-ttu-id="178ee-120">たとえば、`https://localhost:5001/Home/Privacy` には、`Home` (コントローラー) と `Privacy` (ホーム コントローラーに対して呼び出すアクション メソッド) のルート データがあります。</span><span class="sxs-lookup"><span data-stu-id="178ee-120">For example, `https://localhost:5001/Home/Privacy` has route data of `Home` (the controller) and `Privacy` (the action method to call on the home controller).</span></span> <span data-ttu-id="178ee-121">`https://localhost:5001/Movies/Edit/5` は、ムービー コントローラーを使用して ID=5 のムービーを編集する要求です。</span><span class="sxs-lookup"><span data-stu-id="178ee-121">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="178ee-122">ルート データについては、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="178ee-122">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="178ee-123">MVC パターンは、これらの要素間の疎結合を提供しながら、アプリのさまざまな側面 (入力ロジック、ビジネス ロジック、および UI ロジック) を分離するアプリを作成するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="178ee-123">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="178ee-124">このパターンは、アプリで各種類のロジックを配置する必要がある場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="178ee-124">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="178ee-125">UI ロジックはビューに属しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-125">The UI logic belongs in the view.</span></span> <span data-ttu-id="178ee-126">入力ロジックはコントローラーに属しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-126">Input logic belongs in the controller.</span></span> <span data-ttu-id="178ee-127">ビジネス ロジックはモデルに属しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-127">Business logic belongs in the model.</span></span> <span data-ttu-id="178ee-128">このように分離することで、他のコードに影響を与えることなく、実装の 1 つの側面に専念できるため、アプリを構築するときの複雑さが管理しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="178ee-128">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="178ee-129">たとえば、ビジネス ロジック コードに依存することなく、ビュー コードに専念できます。</span><span class="sxs-lookup"><span data-stu-id="178ee-129">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="178ee-130">このチュートリアルで示すこれらの概念を使用して、ムービー アプリを構築する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="178ee-130">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="178ee-131">MVC プロジェクトには、*Controllers* と *Views* の各フォルダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="178ee-131">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="178ee-132">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="178ee-132">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="178ee-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="178ee-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="178ee-134">**ソリューション エクスプローラー** で、 **[コントローラー] を右クリックして [追加] > [コントローラー] の順に選択します。** 
  ![ソリューション エクスプローラーで、[コントローラー] を右クリックして [追加] > [コントローラー] の順に選択します。](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)</span><span class="sxs-lookup"><span data-stu-id="178ee-134">In the **Solution Explorer**, right-click **Controllers > Add > Controller**
![Solution Explorer, right click Controllers > Add > Controller](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)</span></span>

* <span data-ttu-id="178ee-135">**[スキャフォールディングを追加]** ダイアログ ボックスで、 **[MVC コント ローラー - 空]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="178ee-135">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![MVC コント ローラーを追加し、名前を付けます](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

* <span data-ttu-id="178ee-137">**[新しい項目の追加 - MvcMovie]** ダイアログで、「**HelloWorldController.cs**」と入力し、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="178ee-137">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="178ee-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="178ee-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="178ee-139">**[エクスプローラー]** アイコンで、 **[コントローラー] を右クリックして [新しいファイル] を選択し**、新しいファイルに「*HelloWorldController.cs*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="178ee-139">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![コンテキスト メニュー](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="178ee-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="178ee-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="178ee-142">**ソリューション エクスプローラー** で、 **[Controllers] を右クリックし、[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="178ee-142">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>
<span data-ttu-id="178ee-143">![コンテキスト メニュー](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span><span class="sxs-lookup"><span data-stu-id="178ee-143">![Contextual menu](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span></span>

<span data-ttu-id="178ee-144">**[ASP.NET Core]** と **[コントローラー クラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="178ee-144">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="178ee-145">コントローラーに「**HelloWorldController**」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="178ee-145">Name the controller **HelloWorldController**.</span></span>

![MVC コント ローラーを追加し、名前を付けます](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="178ee-147">*Controllers/HelloWorldController.cs* の内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="178ee-147">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="178ee-148">コントローラーのすべての `public` メソッドが、HTTP エンドポイントとして呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-148">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="178ee-149">上のサンプルでは、両方のメソッドが文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="178ee-149">In the sample above, both methods return a string.</span></span> <span data-ttu-id="178ee-150">各メソッドの前のコメントに注意してください。</span><span class="sxs-lookup"><span data-stu-id="178ee-150">Note the comments preceding each method.</span></span>

<span data-ttu-id="178ee-151">HTTP エンドポイントは、Web アプリケーション内のターゲット設定可能な URL (`https://localhost:5001/HelloWorld` など) であり、使われているプロトコル (`HTTPS`)、Web サーバーの (TCP ポートを含む) ネットワーク上の場所 (`localhost:5001`)、ターゲットの URI (`HelloWorld`) を組み合わせたものです。</span><span class="sxs-lookup"><span data-stu-id="178ee-151">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="178ee-152">1 番目のコメントは、これが [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) メソッドであり、ベース URL に `/HelloWorld/` を追加することによって呼び出されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-152">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="178ee-153">2 番目のコメントでは、URL に `/HelloWorld/Welcome/` を追加することによって呼び出される [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) メソッドが示されています。</span><span class="sxs-lookup"><span data-stu-id="178ee-153">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="178ee-154">このチュートリアルではこの後、スキャフォールディング エンジンを使って、データを更新する `HTTP POST` メソッドを生成します。</span><span class="sxs-lookup"><span data-stu-id="178ee-154">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="178ee-155">非デバッグ モードでアプリを実行し、アドレス バーのパスに "HelloWorld" を追加します。</span><span class="sxs-lookup"><span data-stu-id="178ee-155">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="178ee-156">`Index` メソッドが文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="178ee-156">The `Index` method returns a string.</span></span>

!["This is my default action" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="178ee-158">MVC は、着信 URL に応じてコントローラー クラス (およびそれらに含まれるアクション メソッド) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="178ee-158">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="178ee-159">MVC によって使われる既定の [URL ルーティング ロジック](xref:mvc/controllers/routing)では、次のような形式を使って呼び出すコードが決定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-159">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="178ee-160">ルーティングの形式は、*Startup.cs* ファイル内の`Configure` メソッドで設定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-160">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="178ee-161">URL セグメントを指定しないでアプリを参照すると、既定では、"Home" コントローラーと "Index" メソッドが、上の強調表示されている template 行で指定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-161">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="178ee-162">1 番目の URL セグメントでは、実行するコントローラー クラスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-162">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="178ee-163">そのため、`localhost:{PORT}/HelloWorld` は、**HelloWorld** コントローラー クラスにマップされます。</span><span class="sxs-lookup"><span data-stu-id="178ee-163">So `localhost:{PORT}/HelloWorld` maps to the **HelloWorld** Controller class.</span></span> <span data-ttu-id="178ee-164">URL セグメントの 2 番目の部分では、クラスのアクション メソッドが決定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-164">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="178ee-165">したがって、`localhost:{PORT}/HelloWorld/Index` では `HelloWorldController` クラスの `Index` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-165">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="178ee-166">参照する必要があるのは `localhost:{PORT}/HelloWorld` だけであり、`Index` メソッドは既定で呼び出されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="178ee-166">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="178ee-167">これは、`Index` がメソッド名が明示的に指定されていない場合にコントローラーで呼び出される既定のメソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="178ee-167">That's because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="178ee-168">URL セグメントの 3 番目の部分 (`id`) はルート データ用です。</span><span class="sxs-lookup"><span data-stu-id="178ee-168">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="178ee-169">ルート データについては、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="178ee-169">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="178ee-170">`https://localhost:{PORT}/HelloWorld/Welcome` を参照します。</span><span class="sxs-lookup"><span data-stu-id="178ee-170">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="178ee-171">`Welcome` メソッドが実行され、文字列 `This is the Welcome action method...` が返されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-171">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="178ee-172">この URL では、コントローラーは `HelloWorld` で、`Welcome` がアクション メソッドです。</span><span class="sxs-lookup"><span data-stu-id="178ee-172">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="178ee-173">URL の `[Parameters]` の部分はまだ使っていません。</span><span class="sxs-lookup"><span data-stu-id="178ee-173">You haven't used the `[Parameters]` part of the URL yet.</span></span>

!["This is the Welcome action method" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="178ee-175">URL からコントローラーにいくつかのパラメーター情報を渡すように、コードを変更します。</span><span class="sxs-lookup"><span data-stu-id="178ee-175">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="178ee-176">たとえば、`/HelloWorld/Welcome?name=Rick&numtimes=4` のようにします。</span><span class="sxs-lookup"><span data-stu-id="178ee-176">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="178ee-177">次のコードで示すように、2 つのパラメーターを含むように `Welcome` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="178ee-177">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="178ee-178">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="178ee-178">The preceding code:</span></span>

* <span data-ttu-id="178ee-179">C# のオプション パラメーター機能を使って、`numTimes` パラメーターに値が渡されない場合の既定値が 1 であることを示します。</span><span class="sxs-lookup"><span data-stu-id="178ee-179">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="178ee-180">`HtmlEncoder.Default.Encode` を使って、悪意のある入力 (つまり JavaScript) からアプリを保護します。</span><span class="sxs-lookup"><span data-stu-id="178ee-180">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="178ee-181">`$"Hello {name}, NumTimes is: {numTimes}"` 内で[補間文字列](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)を使います。</span><span class="sxs-lookup"><span data-stu-id="178ee-181">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="178ee-182">アプリを実行して次を参照します。</span><span class="sxs-lookup"><span data-stu-id="178ee-182">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="178ee-183">(`{PORT}` は実際のポート番号に置き換えます。)URL の `name` と `numtimes` に違う値を指定してみてください。</span><span class="sxs-lookup"><span data-stu-id="178ee-183">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="178ee-184">MVC の[モデル バインド](xref:mvc/models/model-binding) システムは、名前付きパラメーターを、アドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="178ee-184">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="178ee-185">詳しくは、「[モデル バインド](xref:mvc/models/model-binding)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="178ee-185">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

!["Hello Rick, NumTimes is\: 4" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="178ee-187">上の図では、URL セグメント (`Parameters`) は使われておらず、`name` および `numTimes` パラメーターは[クエリ文字列](https://wikipedia.org/wiki/Query_string)で渡されています。</span><span class="sxs-lookup"><span data-stu-id="178ee-187">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="178ee-188">上の URL の `?` (疑問符) は区切り記号であり、後にクエリ文字列が続きます。</span><span class="sxs-lookup"><span data-stu-id="178ee-188">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="178ee-189">`&` 文字を使ってフィールドと値のペアを区切ります。</span><span class="sxs-lookup"><span data-stu-id="178ee-189">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="178ee-190">`Welcome` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="178ee-190">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="178ee-191">アプリを実行し、次の URL を入力します: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="178ee-191">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="178ee-192">今度は、3 番目の URL セグメントがルート パラメーター `id` と一致しました。</span><span class="sxs-lookup"><span data-stu-id="178ee-192">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="178ee-193">`Welcome` メソッドには、`MapControllerRoute` メソッドの URL テンプレートと一致したパラメーター `id` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="178ee-193">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span> <span data-ttu-id="178ee-194">末尾の `?` (`id?`) は、`id` パラメーターが省略可能であることを示します。</span><span class="sxs-lookup"><span data-stu-id="178ee-194">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="178ee-195">これらの例では、コントローラーによって MVC の "VC" 部分が実行されています。つまり、ビュー (**V** iew) とコントローラー (**C** ontroller) が動作します。</span><span class="sxs-lookup"><span data-stu-id="178ee-195">In these examples the controller has been doing the "VC" portion of MVC - that is, the **V** iew and the **C** ontroller work.</span></span> <span data-ttu-id="178ee-196">コントローラーは HTML を直接返しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-196">The controller is returning HTML directly.</span></span> <span data-ttu-id="178ee-197">一般に、コントローラーが HTML を直接返すのは、コーディングと保守が非常に面倒になるので、望ましくありません。</span><span class="sxs-lookup"><span data-stu-id="178ee-197">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="178ee-198">代わりに、通常は、別の Razor ビュー テンプレート ファイルを使って、HTML 応答を生成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="178ee-198">Instead you typically use a separate Razor view template file to generate the HTML response.</span></span> <span data-ttu-id="178ee-199">これは次のチュートリアルで行います。</span><span class="sxs-lookup"><span data-stu-id="178ee-199">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="178ee-200">[前へ](start-mvc.md)
> [次へ](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="178ee-200">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="178ee-201">モデル ビュー コントローラー (MVC) アーキテクチャ パターンでは、アプリが 3 つの主要なコンポーネントに分けられます。**モデル**、**ビュー**、**コントローラー**。</span><span class="sxs-lookup"><span data-stu-id="178ee-201">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="178ee-202">MVC パターンでは、よりテスト可能で、従来のモノリシック アプリより更新しやすいアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="178ee-202">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="178ee-203">MVC ベースのアプリには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="178ee-203">MVC-based apps contain:</span></span>

* <span data-ttu-id="178ee-204">**モデル**: アプリのデータを表すクラス。</span><span class="sxs-lookup"><span data-stu-id="178ee-204">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="178ee-205">モデル クラスでは検証ロジックを使用して、そのデータにビジネス ルールを適用します。</span><span class="sxs-lookup"><span data-stu-id="178ee-205">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="178ee-206">通常、モデル オブジェクトはモデルの状態を取得して、データベースに格納します。</span><span class="sxs-lookup"><span data-stu-id="178ee-206">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="178ee-207">このチュートリアルでは、`Movie` モデルはデータベースからムービーデータを取得し、それをビューに提供するか、更新します。</span><span class="sxs-lookup"><span data-stu-id="178ee-207">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="178ee-208">更新されたデータはデータベースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="178ee-208">Updated data is written to a database.</span></span>

* <span data-ttu-id="178ee-209">**ビュー**: ビューは、アプリのユーザー インターフェイス (UI) を表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="178ee-209">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="178ee-210">一般に、この UI ではモデル データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-210">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="178ee-211">**コントローラー**: ブラウザー要求を処理するクラスです。</span><span class="sxs-lookup"><span data-stu-id="178ee-211">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="178ee-212">モデル データを取得し、応答を返すビュー テンプレートを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="178ee-212">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="178ee-213">MVC アプリでは、ビューに情報のみが表示され、コントローラーがユーザーの入力と操作を処理して応答します。</span><span class="sxs-lookup"><span data-stu-id="178ee-213">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="178ee-214">たとえば、コントローラーはルート データとクエリ文字列の値を処理し、これらの値をモデルに渡します。</span><span class="sxs-lookup"><span data-stu-id="178ee-214">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="178ee-215">モデルはこれらの値を使用して、データベースを照会する場合があります。</span><span class="sxs-lookup"><span data-stu-id="178ee-215">The model might use these values to query the database.</span></span> <span data-ttu-id="178ee-216">たとえば、`https://localhost:5001/Home/About` には、`Home` (コントローラー) と `About` (ホーム コントローラーに対して呼び出すアクション メソッド) のルート データがあります。</span><span class="sxs-lookup"><span data-stu-id="178ee-216">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="178ee-217">`https://localhost:5001/Movies/Edit/5` は、ムービー コントローラーを使用して ID=5 のムービーを編集する要求です。</span><span class="sxs-lookup"><span data-stu-id="178ee-217">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="178ee-218">ルート データについては、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="178ee-218">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="178ee-219">MVC パターンは、これらの要素間の疎結合を提供しながら、アプリのさまざまな側面 (入力ロジック、ビジネス ロジック、および UI ロジック) を分離するアプリを作成するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="178ee-219">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="178ee-220">このパターンは、アプリで各種類のロジックを配置する必要がある場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="178ee-220">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="178ee-221">UI ロジックはビューに属しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-221">The UI logic belongs in the view.</span></span> <span data-ttu-id="178ee-222">入力ロジックはコントローラーに属しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-222">Input logic belongs in the controller.</span></span> <span data-ttu-id="178ee-223">ビジネス ロジックはモデルに属しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-223">Business logic belongs in the model.</span></span> <span data-ttu-id="178ee-224">このように分離することで、他のコードに影響を与えることなく、実装の 1 つの側面に専念できるため、アプリを構築するときの複雑さが管理しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="178ee-224">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="178ee-225">たとえば、ビジネス ロジック コードに依存することなく、ビュー コードに専念できます。</span><span class="sxs-lookup"><span data-stu-id="178ee-225">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="178ee-226">このチュートリアルで示すこれらの概念を使用して、ムービー アプリを構築する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="178ee-226">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="178ee-227">MVC プロジェクトには、*Controllers* と *Views* の各フォルダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="178ee-227">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="178ee-228">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="178ee-228">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="178ee-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="178ee-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="178ee-230">**ソリューション エクスプローラー** で、 **[コントローラー] を右クリックし、[追加]、[コントローラー]、[コンテキスト メニュー] の順に選択します。** 
  ![コンテキスト メニュー](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)</span><span class="sxs-lookup"><span data-stu-id="178ee-230">In **Solution Explorer**, right-click **Controllers > Add > Controller**
![Contextual menu](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)</span></span>

* <span data-ttu-id="178ee-231">**[スキャフォールディングを追加]** ダイアログ ボックスで、 **[MVC コント ローラー - 空]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="178ee-231">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![MVC コント ローラーを追加し、名前を付けます](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="178ee-233">**[Add Empty MVC Controller]\(空の MVC コント ローラーの追加\)** ダイアログで、「**HelloWorldController**」と入力して、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="178ee-233">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="178ee-234">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="178ee-234">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="178ee-235">**[エクスプローラー]** アイコンで、 **[コントローラー] を右クリックして [新しいファイル] を選択し**、新しいファイルに「*HelloWorldController.cs*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="178ee-235">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![コンテキスト メニュー](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="178ee-237">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="178ee-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="178ee-238">**ソリューション エクスプローラー** で、 **[Controllers] を右クリックし、[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="178ee-238">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>
<span data-ttu-id="178ee-239">![コンテキスト メニュー](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span><span class="sxs-lookup"><span data-stu-id="178ee-239">![Contextual menu](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span></span>

<span data-ttu-id="178ee-240">**[ASP.NET Core]** と **[MVC コントローラー クラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="178ee-240">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="178ee-241">コントローラーに「**HelloWorldController**」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="178ee-241">Name the controller **HelloWorldController**.</span></span>

![MVC コント ローラーを追加し、名前を付けます](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="178ee-243">*Controllers/HelloWorldController.cs* の内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="178ee-243">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="178ee-244">コントローラーのすべての `public` メソッドが、HTTP エンドポイントとして呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-244">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="178ee-245">上のサンプルでは、両方のメソッドが文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="178ee-245">In the sample above, both methods return a string.</span></span> <span data-ttu-id="178ee-246">各メソッドの前のコメントに注意してください。</span><span class="sxs-lookup"><span data-stu-id="178ee-246">Note the comments preceding each method.</span></span>

<span data-ttu-id="178ee-247">HTTP エンドポイントは、Web アプリケーション内のターゲット設定可能な URL (`https://localhost:5001/HelloWorld` など) であり、使われているプロトコル (`HTTPS`)、Web サーバーの (TCP ポートを含む) ネットワーク上の場所 (`localhost:5001`)、ターゲットの URI (`HelloWorld`) を組み合わせたものです。</span><span class="sxs-lookup"><span data-stu-id="178ee-247">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="178ee-248">1 番目のコメントは、これが [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) メソッドであり、ベース URL に `/HelloWorld/` を追加することによって呼び出されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-248">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="178ee-249">2 番目のコメントでは、URL に `/HelloWorld/Welcome/` を追加することによって呼び出される [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) メソッドが示されています。</span><span class="sxs-lookup"><span data-stu-id="178ee-249">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="178ee-250">このチュートリアルではこの後、スキャフォールディング エンジンを使って、データを更新する `HTTP POST` メソッドを生成します。</span><span class="sxs-lookup"><span data-stu-id="178ee-250">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="178ee-251">非デバッグ モードでアプリを実行し、アドレス バーのパスに "HelloWorld" を追加します。</span><span class="sxs-lookup"><span data-stu-id="178ee-251">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="178ee-252">`Index` メソッドが文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="178ee-252">The `Index` method returns a string.</span></span>

!["This is my default action" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="178ee-254">MVC は、着信 URL に応じてコントローラー クラス (およびそれらに含まれるアクション メソッド) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="178ee-254">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="178ee-255">MVC によって使われる既定の [URL ルーティング ロジック](xref:mvc/controllers/routing)では、次のような形式を使って呼び出すコードが決定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-255">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="178ee-256">ルーティングの形式は、*Startup.cs* ファイル内の`Configure` メソッドで設定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-256">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="178ee-257">URL セグメントを指定しないでアプリを参照すると、既定では、"Home" コントローラーと "Index" メソッドが、上の強調表示されている template 行で指定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-257">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="178ee-258">1 番目の URL セグメントでは、実行するコントローラー クラスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-258">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="178ee-259">したがって、`localhost:{PORT}/HelloWorld` は `HelloWorldController` クラスにマップします。</span><span class="sxs-lookup"><span data-stu-id="178ee-259">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="178ee-260">URL セグメントの 2 番目の部分では、クラスのアクション メソッドが決定されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-260">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="178ee-261">したがって、`localhost:{PORT}/HelloWorld/Index` では `HelloWorldController` クラスの `Index` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-261">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="178ee-262">参照する必要があるのは `localhost:{PORT}/HelloWorld` だけであり、`Index` メソッドは既定で呼び出されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="178ee-262">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="178ee-263">これは、`Index` はメソッド名が明示的に指定されていない場合にコントローラーで呼び出される既定のメソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="178ee-263">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="178ee-264">URL セグメントの 3 番目の部分 (`id`) はルート データ用です。</span><span class="sxs-lookup"><span data-stu-id="178ee-264">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="178ee-265">ルート データについては、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="178ee-265">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="178ee-266">`https://localhost:{PORT}/HelloWorld/Welcome` を参照します。</span><span class="sxs-lookup"><span data-stu-id="178ee-266">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="178ee-267">`Welcome` メソッドが実行され、文字列 `This is the Welcome action method...` が返されます。</span><span class="sxs-lookup"><span data-stu-id="178ee-267">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="178ee-268">この URL では、コントローラーは `HelloWorld` で、`Welcome` がアクション メソッドです。</span><span class="sxs-lookup"><span data-stu-id="178ee-268">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="178ee-269">URL の `[Parameters]` の部分はまだ使っていません。</span><span class="sxs-lookup"><span data-stu-id="178ee-269">You haven't used the `[Parameters]` part of the URL yet.</span></span>

!["This is the Welcome action method" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="178ee-271">URL からコントローラーにいくつかのパラメーター情報を渡すように、コードを変更します。</span><span class="sxs-lookup"><span data-stu-id="178ee-271">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="178ee-272">たとえば、`/HelloWorld/Welcome?name=Rick&numtimes=4` のようにします。</span><span class="sxs-lookup"><span data-stu-id="178ee-272">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="178ee-273">次のコードで示すように、2 つのパラメーターを含むように `Welcome` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="178ee-273">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="178ee-274">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="178ee-274">The preceding code:</span></span>

* <span data-ttu-id="178ee-275">C# のオプション パラメーター機能を使って、`numTimes` パラメーターに値が渡されない場合の既定値が 1 であることを示します。</span><span class="sxs-lookup"><span data-stu-id="178ee-275">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="178ee-276">`HtmlEncoder.Default.Encode` を使って、悪意のある入力 (つまり JavaScript) からアプリを保護します。</span><span class="sxs-lookup"><span data-stu-id="178ee-276">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="178ee-277">`$"Hello {name}, NumTimes is: {numTimes}"` 内で[補間文字列](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)を使います。</span><span class="sxs-lookup"><span data-stu-id="178ee-277">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="178ee-278">アプリを実行して次を参照します。</span><span class="sxs-lookup"><span data-stu-id="178ee-278">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="178ee-279">(`{PORT}` は実際のポート番号に置き換えます。)URL の `name` と `numtimes` に違う値を指定してみてください。</span><span class="sxs-lookup"><span data-stu-id="178ee-279">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="178ee-280">MVC の[モデル バインド](xref:mvc/models/model-binding) システムは、名前付きパラメーターを、アドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="178ee-280">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="178ee-281">詳しくは、「[モデル バインド](xref:mvc/models/model-binding)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="178ee-281">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

!["Hello Rick, NumTimes is\: 4" というアプリケーションの応答が表示されているブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="178ee-283">上の図では、URL セグメント (`Parameters`) は使われておらず、`name` および `numTimes` パラメーターは[クエリ文字列](https://wikipedia.org/wiki/Query_string)で渡されています。</span><span class="sxs-lookup"><span data-stu-id="178ee-283">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="178ee-284">上の URL の `?` (疑問符) は区切り記号であり、後にクエリ文字列が続きます。</span><span class="sxs-lookup"><span data-stu-id="178ee-284">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="178ee-285">`&` 文字を使ってフィールドと値のペアを区切ります。</span><span class="sxs-lookup"><span data-stu-id="178ee-285">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="178ee-286">`Welcome` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="178ee-286">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="178ee-287">アプリを実行し、次の URL を入力します: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="178ee-287">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="178ee-288">今度は、3 番目の URL セグメントがルート パラメーター `id` と一致しました。</span><span class="sxs-lookup"><span data-stu-id="178ee-288">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="178ee-289">`Welcome` メソッドには、`MapRoute` メソッドの URL テンプレートと一致したパラメーター `id` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="178ee-289">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="178ee-290">末尾の `?` (`id?`) は、`id` パラメーターが省略可能であることを示します。</span><span class="sxs-lookup"><span data-stu-id="178ee-290">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="178ee-291">これらの例では、コントローラーによって MVC の "VC" 部分が実行されています。つまり、ビューとコントローラーが動作します。</span><span class="sxs-lookup"><span data-stu-id="178ee-291">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="178ee-292">コントローラーは HTML を直接返しています。</span><span class="sxs-lookup"><span data-stu-id="178ee-292">The controller is returning HTML directly.</span></span> <span data-ttu-id="178ee-293">一般に、コントローラーが HTML を直接返すのは、コーディングと保守が非常に面倒になるので、望ましくありません。</span><span class="sxs-lookup"><span data-stu-id="178ee-293">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="178ee-294">代わりに、通常は、別の Razor ビュー テンプレート ファイルを使って、HTML 応答を生成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="178ee-294">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="178ee-295">これは次のチュートリアルで行います。</span><span class="sxs-lookup"><span data-stu-id="178ee-295">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="178ee-296">[前へ](start-mvc.md)
> [次へ](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="178ee-296">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end

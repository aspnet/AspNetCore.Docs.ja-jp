---
title: パート 3、ASP.NET Core MVC アプリへのビューの追加
author: rick-anderson
description: ASP.NET Core MVC のチュートリアル シリーズのパート 3。
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804570"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="b60ce-103">パート 3、ASP.NET Core MVC アプリへのビューの追加</span><span class="sxs-lookup"><span data-stu-id="b60ce-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="b60ce-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b60ce-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b60ce-105">このセクションでは、[Razor](xref:mvc/views/razor) ビュー ファイルを使用するよう、`HelloWorldController` クラスを変更します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="b60ce-106">これにより、クライアントへの HTML 応答を生成するプロセスが完全にカプセル化されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="b60ce-107">ビュー テンプレートは Razor を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-107">View templates are created using Razor.</span></span> <span data-ttu-id="b60ce-108">Razor ベースのビュー テンプレートは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b60ce-108">Razor-based view templates:</span></span>

* <span data-ttu-id="b60ce-109">ファイル拡張子は *.cshtml* です。</span><span class="sxs-lookup"><span data-stu-id="b60ce-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="b60ce-110">C# を使用して HTML 出力を作成する洗練された方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="b60ce-111">現在、`Index` メソッドは、コントローラー クラスにメッセージを含め文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="b60ce-112">`HelloWorldController` クラスでは、`Index` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="b60ce-113">上記のコードでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-113">The preceding code:</span></span>

* <span data-ttu-id="b60ce-114">コントローラーの <xref:Microsoft.AspNetCore.Mvc.Controller.View*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="b60ce-115">ビュー テンプレートを使用し HTML 応答を生成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="b60ce-116">コントローラー メソッドは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b60ce-116">Controller methods:</span></span>

* <span data-ttu-id="b60ce-117">"*アクション メソッド*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="b60ce-118">たとえば、前のコードの `Index` アクション メソッドです。</span><span class="sxs-lookup"><span data-stu-id="b60ce-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="b60ce-119">通常、`string` のような型ではなく <xref:Microsoft.AspNetCore.Mvc.IActionResult> または <xref:Microsoft.AspNetCore.Mvc.ActionResult> から派生したクラスを返します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="b60ce-120">ビューを追加する</span><span class="sxs-lookup"><span data-stu-id="b60ce-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b60ce-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b60ce-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b60ce-122">*Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="b60ce-123">*Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="b60ce-124">**[新しい項目の追加 - MvcMovie]** ダイアログで次を行います。</span><span class="sxs-lookup"><span data-stu-id="b60ce-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="b60ce-125">右上の検索ボックスに「*view*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="b60ce-126">**[Razor ビュー]** を選択します</span><span class="sxs-lookup"><span data-stu-id="b60ce-126">Select **Razor View**</span></span>
* <span data-ttu-id="b60ce-127">**[名前]** ボックスの値、*Index.cshtml* を維持します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="b60ce-128">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-128">Select **Add**</span></span>

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b60ce-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b60ce-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b60ce-131">次のように `HelloWorldController` に `Index` ビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="b60ce-132">*Views/HelloWorld* という名前の新しいフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="b60ce-133">*Views/HelloWorld* フォルダー名 *Index.cshtml* に新しいファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b60ce-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b60ce-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b60ce-135">*Views* フォルダーを Control キーを押しながらクリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="b60ce-136">*Views/HelloWorld* フォルダーを Control キーを押しながらクリックし、 **[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="b60ce-137">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="b60ce-138">左側のウィンドウで、 **[ASP.NET Core]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="b60ce-139">中央のウィンドウで **[Razor View - Empty]** \(Razor ビュー - 空\) を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="b60ce-140">**[名前]** ボックスに「*Index*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="b60ce-141">**[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-141">Select **New**.</span></span>

  ![[新しい項目の追加] ダイアログ](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="b60ce-143">*Views/HelloWorld/Index.cshtml* Razor ビュー ファイルを次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="b60ce-144">次のように `https://localhost:{PORT}/HelloWorld` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="b60ce-145">`HelloWorldController` の `Index` メソッドにより、ビュー テンプレート ファイルを使用して、ブラウザーに応答をレンダリングするようメソッドを指定する、ステートメント `return View();` が実行されました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="b60ce-146">ビュー テンプレートのファイル名が指定されていないため、MVC で既定のビュー ファイルが使われました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="b60ce-147">ビュー ファイル名を指定しない場合は、既定のビューが返されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="b60ce-148">この例では、既定のビューの名前は、アクション メソッド `Index` と同じ名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="b60ce-149">ビュー テンプレート */Views/HelloWorld/Index.cshtml* が使用されています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="b60ce-150">次のイメージは、ビューにハード コーディングされた "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="b60ce-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="b60ce-151">という文字列を示しています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-151">hard-coded in the view:</span></span>

  ![ブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="b60ce-153">ビューとレイアウト ページを変更する</span><span class="sxs-lookup"><span data-stu-id="b60ce-153">Change views and layout pages</span></span>

<span data-ttu-id="b60ce-154">メニューのリンク、 **[MvcMovie]** 、 **[ホーム]** 、 **[プライバシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="b60ce-155">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="b60ce-156">メニューのレイアウトは、*Views/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b60ce-157">*Views/Shared/_Layout.cshtml* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b60ce-158">[レイアウト](xref:mvc/views/layout) テンプレートでは、次のことが可能です。</span><span class="sxs-lookup"><span data-stu-id="b60ce-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="b60ce-159">1 か所でサイトの HTML コンテナー レイアウトを指定できます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="b60ce-160">サイト内の複数のページに HTML コンテナー レイアウトを適用できます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="b60ce-161">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b60ce-162">`RenderBody` は、作成したビュー固有のページがすべて表示されるプレースホルダーで、レイアウト ページに *ラップ* されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b60ce-163">たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Views/Home/Privacy.cshtml** ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="b60ce-164">レイアウト ファイルでのタイトル、フッター、およびメニュー リンクの変更</span><span class="sxs-lookup"><span data-stu-id="b60ce-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="b60ce-165">*Views/Shared/_Layout.cshtml* ファイルの内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="b60ce-166">変更が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b60ce-167">上記のマークアップでは、次の変更が加えられています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="b60ce-168">`MvcMovie` から `Movie App` が 3 回発生します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="b60ce-169">アンカー要素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` が `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` になりました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="b60ce-170">上記のマークアップでは、このアプリでは [Area](xref:mvc/controllers/areas) が使用されていないため、`asp-area=""` [アンカー タグ ヘルパー属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)と属性値が省略されています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="b60ce-171">**注:** まだ `Movies` コントローラーは実装されていません。</span><span class="sxs-lookup"><span data-stu-id="b60ce-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="b60ce-172">この時点では、`Movie App` リンクは機能していません。</span><span class="sxs-lookup"><span data-stu-id="b60ce-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="b60ce-173">変更を保存し、 **[プライバシー]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="b60ce-174">ブラウザー タブのタイトルが、**Privacy Policy - Mvc Movie** ではなく、**Privacy Policy - Movie App** になっていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![[プライバシー] タブ](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="b60ce-176">**[ホーム]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-176">Select the **Home** link.</span></span>

<span data-ttu-id="b60ce-177">タイトルとアンカー テキストに **Movie App** と表示されていることに着目してください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="b60ce-178">レイアウト テンプレートが一度変更され、この新しいリンク テキストと新しいタイトルがサイト上のすべてのページに反映されました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="b60ce-179">*Views/_ViewStart.cshtml* ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="b60ce-180">*Views/_ViewStart.cshtml* ファイルは *Views/Shared/_Layout.cshtml* ファイルに取り込まれ、各ビューに適用されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="b60ce-181">`Layout` プロパティを使用すれば、別のレイアウト ビューを設定することも、`null` に設定してレイアウト ファイルが使用されないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="b60ce-182">*Views/HelloWorld/Index.cshtml* ビュー ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="b60ce-183">タイトルと `<h2>` 要素を、以下の強調表示どおりに変更します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="b60ce-184">タイトルと `<h2>` 要素は若干違うので、コードのどの部分によって表示が変更されるのかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="b60ce-185">上のコードの `ViewData["Title"] = "Movie List";` では、`Title` ディクショナリの `ViewData` プロパティを "Movie List" に設定します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="b60ce-186">`Title` プロパティは、次のように、レイアウト ページの `<title>` HTML 要素で使用されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="b60ce-187">変更内容を保存して、`https://localhost:{PORT}/HelloWorld` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="b60ce-188">次が変更されたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="b60ce-189">ブラウザー タイトル。</span><span class="sxs-lookup"><span data-stu-id="b60ce-189">Browser title.</span></span>
* <span data-ttu-id="b60ce-190">1 番目の見出し。</span><span class="sxs-lookup"><span data-stu-id="b60ce-190">Primary heading.</span></span>
* <span data-ttu-id="b60ce-191">2 番目の見出し。</span><span class="sxs-lookup"><span data-stu-id="b60ce-191">Secondary headings.</span></span>

<span data-ttu-id="b60ce-192">ブラウザーに変更がない場合は、キャッシュされたコンテンツが表示されている場合があります。</span><span class="sxs-lookup"><span data-stu-id="b60ce-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="b60ce-193">ブラウザーで Ctrl + F5 キーを押して、サーバーからの応答が強制的に読み込まれるようにします。</span><span class="sxs-lookup"><span data-stu-id="b60ce-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="b60ce-194">ブラウザーのタイトルは、*Index.cshtml* ビュー テンプレートで設定した `ViewData["Title"]` で作成されます。レイアウト ファイルには "- Movie App" が追加されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="b60ce-195">*Index.cshtml* ビュー テンプレート内のコンテンツは、*Views/Shared/_Layout.cshtml* ビュー テンプレートにマージされます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="b60ce-196">1 つの HTML 応答がブラウザーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="b60ce-197">レイアウト テンプレートを使用すれば、アプリのすべてのページに適用される変更を簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="b60ce-198">詳細については、「[Layout](xref:mvc/views/layout)」(レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![ムービー リスト ビュー](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="b60ce-200">ここでは、"データ" のごく一部 (この場合は "Hello from our View Template!" という</span><span class="sxs-lookup"><span data-stu-id="b60ce-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="b60ce-201">メッセージ) をハード コーディングしました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-201">message, is hard-coded however.</span></span> <span data-ttu-id="b60ce-202">MVC アプリケーションには "V" (ビュー) があり、"C" (コントローラー) もありますが、"M" (モデル) はまだありません。</span><span class="sxs-lookup"><span data-stu-id="b60ce-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="b60ce-203">コントローラーからビューへのデータの受け渡し</span><span class="sxs-lookup"><span data-stu-id="b60ce-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="b60ce-204">コントローラー アクションは、受信 URL 要求への応答として呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="b60ce-205">コントローラー クラスでは、受信ブラウザー要求を処理するコードが記述されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="b60ce-206">コントローラーはデータ ソースからデータを取得し、ブラウザーに返す応答の種類を決定します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="b60ce-207">ビュー テンプレートを使用すれば、コントローラーからブラウザーへの HTML 応答を生成して書式を設定できます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="b60ce-208">コントローラーは、ビュー テンプレートで応答をレンダリングするために必要なデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="b60ce-209">ビュー テンプレートでは、次を **行いません**。</span><span class="sxs-lookup"><span data-stu-id="b60ce-209">View templates should **not**:</span></span>

* <span data-ttu-id="b60ce-210">ビジネス ロジックの実行</span><span class="sxs-lookup"><span data-stu-id="b60ce-210">Do business logic</span></span>
* <span data-ttu-id="b60ce-211">データベースとの直接のやりとり</span><span class="sxs-lookup"><span data-stu-id="b60ce-211">Interact with a database directly.</span></span>

<span data-ttu-id="b60ce-212">ビュー テンプレートは、コントローラーから提供されるデータのみを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b60ce-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="b60ce-213">この "関心の分離" を維持すれば、コードを次のように保つことができます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="b60ce-214">クリーン。</span><span class="sxs-lookup"><span data-stu-id="b60ce-214">Clean.</span></span>
* <span data-ttu-id="b60ce-215">テスト可能。</span><span class="sxs-lookup"><span data-stu-id="b60ce-215">Testable.</span></span>
* <span data-ttu-id="b60ce-216">保守しやすい。</span><span class="sxs-lookup"><span data-stu-id="b60ce-216">Maintainable.</span></span>

<span data-ttu-id="b60ce-217">現時点では、`HelloWorldController` クラスの `Welcome` メソッドは `name` と `ID` パラメーターを受け取ってから、ブラウザーに直接値を出力します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="b60ce-218">コントローラーにこの応答を文字列としてレンダリングさせるのではなく、代わりにビュー テンプレートを使用するようにコントローラーを変更します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="b60ce-219">このビュー テンプレートは、応答を動的に生成します。つまり、応答の生成には、コントローラーからビューに適切なデータが渡される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b60ce-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="b60ce-220">そのためには、ビュー テンプレートが必要とする動的データ (パラメーター) を、コントローラーに `ViewData` ディレクトリに配置させます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="b60ce-221">これにより、ビュー テンプレートが動的データにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="b60ce-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="b60ce-222">*HelloWorldController.cs* 内で、`Welcome` メソッドを変更して `Message` および `NumTimes` 値を `ViewData` ディクショナリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="b60ce-223">`ViewData` ディクショナリは、すべての型を使用できる動的なオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="b60ce-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="b60ce-224">`ViewData` オブジェクトには、何かが追加されるまで、プロパティは定義されていません。</span><span class="sxs-lookup"><span data-stu-id="b60ce-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="b60ce-225">[MVC のモデル バインド システム](xref:mvc/models/model-binding)は、`name` と `numTimes` の名前付きパラメーターをクエリ文字列からメソッドのパラメーターに自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="b60ce-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="b60ce-226">完全な `HelloWorldController` は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b60ce-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="b60ce-227">`ViewData` ディクショナリ オブジェクトには、ビューに渡されるデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="b60ce-228">*Views/HelloWorld/Welcome.cshtml* という名前の [ようこそ] ビュー テンプレートを作成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="b60ce-229">"Hello" `NumTimes` を表示する *Welcome.cshtml* ビュー テンプレートでループを作成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="b60ce-230">*Views/HelloWorld/Welcome.cshtml* の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="b60ce-231">変更内容を保存し、次の URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="b60ce-232">データは URL から取得され、[MVC モデル バインダー](xref:mvc/models/model-binding)を使用してコントローラーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b60ce-233">コントローラーはデータを `ViewData` ディクショナリにパッケージ化し、そのオブジェクトをビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="b60ce-234">その後、ビューでブラウザーに HTML としてデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-234">The view then renders the data as HTML to the browser.</span></span>

![[ようこそ] ラベルと、Hello Rick という語句が 4 つ示された [プライバシー] ビュー](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="b60ce-236">上のサンプルでは、`ViewData` ディクショナリを使用して、コントローラーからビューにデータを渡しました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="b60ce-237">チュートリアルの後半では、ビュー モデルを使用して、コントローラーからビューにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="b60ce-238">データを渡すには、`ViewData` ディクショナリを使用する方法より、ビュー モデルを使用する方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="b60ce-239">次のチュートリアルでは、ムービーのデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b60ce-240">[前: コントローラーの追加](adding-controller.md)
> [次: モデルの追加](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="b60ce-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b60ce-241">このセクションでは、クライアントに対する HTML 応答を生成するプロセスを完全にカプセル化するために、[Razor](xref:mvc/views/razor) ビュー ファイルを使用して、`HelloWorldController` クラスを変更します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="b60ce-242">ビュー テンプレート ファイルは Razor を使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-242">You create a view template file using Razor.</span></span> <span data-ttu-id="b60ce-243">Razor ベースのビュー テンプレートには *.cshtml* ファイル拡張子が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="b60ce-244">C# を使用して HTML 出力を作成する洗練された方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="b60ce-245">現在、`Index` メソッドは、コントローラー クラスでハード コーディングされるメッセージを含む文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="b60ce-246">`HelloWorldController` クラスでは、`Index` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="b60ce-247">上のコードでは、コントローラーの <xref:Microsoft.AspNetCore.Mvc.Controller.View*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="b60ce-248">ビュー テンプレートを使用して、HTML 応答を生成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="b60ce-249">上記の `Index` メソッドなどのコントローラー メソッド (*アクション メソッド* ともいう) は、一般に、`string` などの型ではなく、<xref:Microsoft.AspNetCore.Mvc.IActionResult> (または <xref:Microsoft.AspNetCore.Mvc.ActionResult> から派生したクラス) を返します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="b60ce-250">ビューを追加する</span><span class="sxs-lookup"><span data-stu-id="b60ce-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b60ce-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b60ce-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b60ce-252">*Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="b60ce-253">*Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="b60ce-254">**[新しい項目の追加 - MvcMovie]** ダイアログ</span><span class="sxs-lookup"><span data-stu-id="b60ce-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="b60ce-255">右上の検索ボックスに「*view*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="b60ce-256">**[Razor ビュー]** を選択します</span><span class="sxs-lookup"><span data-stu-id="b60ce-256">Select **Razor View**</span></span>

  * <span data-ttu-id="b60ce-257">**[名前]** ボックスの値、*Index.cshtml* を維持します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="b60ce-258">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-258">Select **Add**</span></span>

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b60ce-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b60ce-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b60ce-261">`HelloWorldController` の `Index` ビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="b60ce-262">*Views/HelloWorld* という名前の新しいフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="b60ce-263">*Views/HelloWorld* フォルダー名 *Index.cshtml* に新しいファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b60ce-264">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b60ce-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b60ce-265">*Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="b60ce-266">*Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="b60ce-267">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b60ce-268">左側のウィンドウで **[Web]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="b60ce-269">中央のウィンドウで **[空の HTML ファイル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="b60ce-270">**[名前]** ボックスに「*Index.cshtml*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="b60ce-271">**[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-271">Select **New**.</span></span>

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="b60ce-273">*Views/HelloWorld/Index.cshtml* Razor ビュー ファイルを次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="b60ce-274">`https://localhost:{PORT}/HelloWorld` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b60ce-275">`HelloWorldController` の `Index` メソッドでは多くのことは行いませんでした。つまり、ステートメント `return View();` を実行し、メソッドでビュー テンプレート ファイルを使用して、ブラウザーへの応答をレンダリングするよう指定しただけです。</span><span class="sxs-lookup"><span data-stu-id="b60ce-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="b60ce-276">ビュー テンプレートのファイル名が指定されていないため、MVC では既定のビュー ファイルが使われました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="b60ce-277">既定のビュー ファイルはメソッド (`Index`) と同じ名前なので、 */Views/HelloWorld/Index.cshtml* が使われます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="b60ce-278">次のイメージは、ビューにハード コーディングされた "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="b60ce-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="b60ce-279">という文字列を示しています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-279">hard-coded in the view.</span></span>

![ブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="b60ce-281">ビューとレイアウト ページを変更する</span><span class="sxs-lookup"><span data-stu-id="b60ce-281">Change views and layout pages</span></span>

<span data-ttu-id="b60ce-282">メニューのリンク ( **[MvcMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="b60ce-283">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="b60ce-284">メニューのレイアウトは、*Views/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b60ce-285">*Views/Shared/_Layout.cshtml* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b60ce-286">[[レイアウト]](xref:mvc/views/layout) テンプレートでは、1 か所でサイトの HTML コンテナー レイアウトを指定し、それをサイト内の複数のページに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="b60ce-287">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b60ce-288">`RenderBody` は、作成したビュー固有のページがすべて表示されるプレースホルダーで、レイアウト ページに *ラップ* されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b60ce-289">たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Views/Home/Privacy.cshtml** ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="b60ce-290">レイアウト ファイルでのタイトル、フッター、およびメニュー リンクの変更</span><span class="sxs-lookup"><span data-stu-id="b60ce-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="b60ce-291">タイトル要素とフッター要素で、`MvcMovie` を `Movie App` に変更します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="b60ce-292">アンカー要素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` を `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` に変更します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="b60ce-293">次のマークアップには、強調表示された変更点が示されています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="b60ce-294">上記のマークアップでは、このアプリで[領域](xref:mvc/controllers/areas)が使用されていないため、`asp-area` [アンカー タグ ヘルパー属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)は省略されました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="b60ce-295">**注**:`Movies` コントローラーは実装されていません。</span><span class="sxs-lookup"><span data-stu-id="b60ce-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="b60ce-296">この時点で、`Movie App`リンクは機能しません。</span><span class="sxs-lookup"><span data-stu-id="b60ce-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="b60ce-297">ご自分の変更を保存し、**プライバシー** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="b60ce-298">ブラウザー タブのタイトルが、**Privacy Policy - Mvc Movie** ではなく、**Privacy Policy - Movie App** になっていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![[プライバシー] タブ](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="b60ce-300">**[ホーム]** リンクをタップし、タイトルとアンカー テキストにも **[Movie App]** と表示されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="b60ce-301">レイアウト テンプレートで一度変更しただけで、サイト上のすべてのページに新しいリンク テキストと新しいタイトルが反映できました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="b60ce-302">*Views/_ViewStart.cshtml* ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="b60ce-303">*Views/_ViewStart.cshtml* ファイルは *Views/Shared/_Layout.cshtml* ファイルに取り込まれ、各ビューに適用されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="b60ce-304">`Layout` プロパティを使用すれば、別のレイアウト ビューを設定することも、`null` に設定してレイアウト ファイルが使用されないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="b60ce-305">*Views/HelloWorld/Index.cshtml* ビュー ファイルのタイトルと `<h2>` 要素を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="b60ce-306">タイトルと `<h2>` 要素は若干異なります。これにより、コードのどの部分によって表示が変更されるのかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="b60ce-307">上のコードの `ViewData["Title"] = "Movie List";` では、`Title` ディクショナリの `ViewData` プロパティを "Movie List" に設定します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="b60ce-308">`Title` プロパティは、次のように、レイアウト ページの `<title>` HTML 要素で使用されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="b60ce-309">変更内容を保存して、`https://localhost:{PORT}/HelloWorld` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b60ce-310">ブラウザーのタイトル、プライマリ見出し、およびセカンダリ見出しが変更されていることに注意してください</span><span class="sxs-lookup"><span data-stu-id="b60ce-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="b60ce-311">(ブラウザーに変更内容が表示されない場合は、キャッシュされたコンテンツを表示している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b60ce-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="b60ce-312">ブラウザーで Ctrl + F5 キーを押して、サーバーからの応答が強制的に読み込まれるようにしてください)。ブラウザーのタイトルは、*Index.cshtml* ビュー テンプレートで設定した `ViewData["Title"]` で作成されます。レイアウト ファイルには "- Movie App" が追加されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="b60ce-313">*Index.cshtml* ビュー テンプレートのコンテンツがどのように *Views/Shared/_Layout.cshtml* ビュー テンプレートにマージされ、1 つの HTML 応答がブラウザーに送信されたかにも注目してください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="b60ce-314">レイアウト テンプレートを使用すれば、アプリケーションのすべてのページに適用される変更をとても簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="b60ce-315">詳細については、「[Layout](xref:mvc/views/layout)」 (レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![ムービー リスト ビュー](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="b60ce-317">ここでは、"データ" のごく一部 (この場合は "Hello from our View Template!" というメッセージ) を</span><span class="sxs-lookup"><span data-stu-id="b60ce-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="b60ce-318">ハード コーディングしました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-318">message) is hard-coded, though.</span></span> <span data-ttu-id="b60ce-319">MVC アプリケーションには "V" (ビュー) があり、"C" (コントローラー) もありますが、"M" (モデル) はまだありません。</span><span class="sxs-lookup"><span data-stu-id="b60ce-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="b60ce-320">コントローラーからビューへのデータの受け渡し</span><span class="sxs-lookup"><span data-stu-id="b60ce-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="b60ce-321">コントローラー アクションは、受信 URL 要求への応答として呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="b60ce-322">コントローラー クラスでは、受信ブラウザー要求を処理するコードが記述されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="b60ce-323">コントローラーはデータ ソースからデータを取得し、ブラウザーに返す応答の種類を決定します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="b60ce-324">ビュー テンプレートを使用すれば、コントローラーからブラウザーへの HTML 応答を生成して書式を設定できます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="b60ce-325">コントローラーは、ビュー テンプレートで応答をレンダリングするために必要なデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="b60ce-326">ベスト プラクティス: ビュー テンプレートでは、ビジネス ロジックを実行したり、データベースと直接やりとりしたり **しない** でください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="b60ce-327">ビュー テンプレートでは、コントローラーによって提供されるデータのみを処理するようにしてください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="b60ce-328">この "懸念事項の分離" を維持すれば、コードをクリーンでテスト可能な保守しやすい状態に保つことが楽になります。</span><span class="sxs-lookup"><span data-stu-id="b60ce-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="b60ce-329">現時点では、`HelloWorldController` クラスの `Welcome` メソッドは `name` と `ID` パラメーターを受け取ってから、ブラウザーに直接値を出力します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="b60ce-330">コントローラーにこの応答を文字列としてレンダリングさせるのではなく、代わりにビュー テンプレートを使用するようにコントローラーを変更します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="b60ce-331">このビュー テンプレートでは動的応答が生成されます。これは、応答を生成するために、コントローラーからビューに適量のデータを渡す必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="b60ce-332">そのためには、コントローラーで動的データ (パラメーター) を設定します。これは、ビュー テンプレートでアクセスできるようにするために `ViewData` ディレクトリに必要なデータです。</span><span class="sxs-lookup"><span data-stu-id="b60ce-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="b60ce-333">*HelloWorldController.cs* 内で、`Welcome` メソッドを変更して `Message` および `NumTimes` 値を `ViewData` ディクショナリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="b60ce-334">`ViewData` ディクショナリは動的オブジェクトです。つまり、任意の型を使用することができます。`ViewData` オブジェクトでは、その内部に何かを設定するまでプロパティは定義されません。</span><span class="sxs-lookup"><span data-stu-id="b60ce-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="b60ce-335">[MVC のモデル バインド システム](xref:mvc/models/model-binding)は、名前付きパラメーター (`name` と `numTimes`) を、アドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="b60ce-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="b60ce-336">完全な *HelloWorldController.cs* ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b60ce-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="b60ce-337">`ViewData` ディクショナリ オブジェクトには、ビューに渡されるデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b60ce-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="b60ce-338">*Views/HelloWorld/Welcome.cshtml* という名前の [ようこそ] ビュー テンプレートを作成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="b60ce-339">"Hello" `NumTimes` を表示する *Welcome.cshtml* ビュー テンプレートでループを作成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="b60ce-340">*Views/HelloWorld/Welcome.cshtml* の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="b60ce-341">変更内容を保存し、次の URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="b60ce-342">データは URL から取得され、[MVC モデル バインダー](xref:mvc/models/model-binding)を使用してコントローラーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="b60ce-343">コントローラーはデータを `ViewData` ディクショナリにパッケージ化し、そのオブジェクトをビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="b60ce-344">その後、ビューでブラウザーに HTML としてデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-344">The view then renders the data as HTML to the browser.</span></span>

![[ようこそ] ラベルと、Hello Rick という語句が 4 つ示された [プライバシー] ビュー](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="b60ce-346">上のサンプルでは、`ViewData` ディクショナリを使用して、コントローラーからビューにデータを渡しました。</span><span class="sxs-lookup"><span data-stu-id="b60ce-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="b60ce-347">チュートリアルの後半では、ビュー モデルを使用して、コントローラーからビューにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="b60ce-348">一般には、`ViewData` ディクショナリを使用する方法より、ビュー モデルを使用してデータを渡す方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="b60ce-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="b60ce-349">詳細については、[ViewBag、ViewData、または TempData を使用するタイミング](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b60ce-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="b60ce-350">次のチュートリアルでは、ムービーのデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b60ce-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b60ce-351">[前へ](adding-controller.md)
> [次へ](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="b60ce-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

---
title: パート 3、ASP.NET Core MVC アプリへのビューの追加
author: rick-anderson
description: ASP.NET Core MVC のチュートリアル シリーズのパート 3。
ms.author: riande
ms.date: 11/16/2019
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
ms.openlocfilehash: ce2754d22b61578bdbe5e78a69e08bdc1e83190d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688360"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="b7bfc-103">パート 3、ASP.NET Core MVC アプリへのビューの追加</span><span class="sxs-lookup"><span data-stu-id="b7bfc-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="b7bfc-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7bfc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7bfc-105">このセクションでは、[Razor](xref:mvc/views/razor) ビュー ファイルを使用して、クライアントへの HTML 応答を生成するプロセスを完全にカプセル化するために `HelloWorldController` クラスを変更します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="b7bfc-106">ビュー テンプレート ファイルは Razor を使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-106">You create a view template file using Razor.</span></span> <span data-ttu-id="b7bfc-107">Razor ベースのビュー テンプレートには *.cshtml* ファイル拡張子が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="b7bfc-108">C# を使用して HTML 出力を作成する洗練された方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="b7bfc-109">現在、`Index` メソッドは、コントローラー クラスでハード コーディングされるメッセージを含む文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="b7bfc-110">`HelloWorldController` クラスでは、`Index` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="b7bfc-111">上のコードでは、コントローラーの <xref:Microsoft.AspNetCore.Mvc.Controller.View*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="b7bfc-112">ビュー テンプレートを使用して、HTML 応答を生成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="b7bfc-113">上記の `Index` メソッドなどのコントローラー メソッド (*アクション メソッド* ともいう) は、一般に、`string` などの型ではなく、<xref:Microsoft.AspNetCore.Mvc.IActionResult> (または <xref:Microsoft.AspNetCore.Mvc.ActionResult> から派生したクラス) を返します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="b7bfc-114">ビューを追加する</span><span class="sxs-lookup"><span data-stu-id="b7bfc-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7bfc-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7bfc-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7bfc-116">*Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="b7bfc-117">*Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="b7bfc-118">**[新しい項目の追加 - MvcMovie]** ダイアログ</span><span class="sxs-lookup"><span data-stu-id="b7bfc-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="b7bfc-119">右上の検索ボックスに「*view*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="b7bfc-120">**[Razor ビュー]** を選択します</span><span class="sxs-lookup"><span data-stu-id="b7bfc-120">Select **Razor View**</span></span>

  * <span data-ttu-id="b7bfc-121">**[名前]** ボックスの値、*Index.cshtml* を維持します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="b7bfc-122">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-122">Select **Add**</span></span>

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7bfc-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7bfc-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b7bfc-125">`HelloWorldController` の `Index` ビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="b7bfc-126">*Views/HelloWorld* という名前の新しいフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="b7bfc-127">*Views/HelloWorld* フォルダー名 *Index.cshtml* に新しいファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7bfc-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b7bfc-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b7bfc-129">*Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="b7bfc-130">*Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="b7bfc-131">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b7bfc-132">左側のウィンドウで、 **[ASP .NET Core]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="b7bfc-133">中央のウィンドウで **Razor [表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-133">Select **Razor View** in the center pane.</span></span>
  * <span data-ttu-id="b7bfc-134">**[名前]** ボックスに「*Index*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="b7bfc-135">**[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-135">Select **New**.</span></span>

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="b7bfc-137">*Views/HelloWorld/Index.cshtml* Razor ビュー ファイルを次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="b7bfc-138">`https://localhost:{PORT}/HelloWorld` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b7bfc-139">`HelloWorldController` の `Index` メソッドでは多くのことは行いませんでした。つまり、ステートメント `return View();` を実行し、メソッドでビュー テンプレート ファイルを使用して、ブラウザーへの応答をレンダリングするよう指定しただけです。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="b7bfc-140">ビュー テンプレートのファイル名が指定されていないため、MVC では既定のビュー ファイルが使われました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="b7bfc-141">既定のビュー ファイルはメソッド (`Index`) と同じ名前なので、 */Views/HelloWorld/Index.cshtml* のビュー テンプレートが使われます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="b7bfc-142">次のイメージは、ビューにハード コーディングされた "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="b7bfc-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="b7bfc-143">という文字列を示しています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-143">hard-coded in the view.</span></span>

![ブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="b7bfc-145">ビューとレイアウト ページを変更する</span><span class="sxs-lookup"><span data-stu-id="b7bfc-145">Change views and layout pages</span></span>

<span data-ttu-id="b7bfc-146">メニューのリンク ( **[MvcMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="b7bfc-147">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="b7bfc-148">メニューのレイアウトは、*Views/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b7bfc-149">*Views/Shared/_Layout.cshtml* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b7bfc-150">[[レイアウト]](xref:mvc/views/layout) テンプレートでは、1 か所でサイトの HTML コンテナー レイアウトを指定し、それをサイト内の複数のページに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="b7bfc-151">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b7bfc-152">`RenderBody` は、作成したビュー固有のページがすべて表示されるプレースホルダーで、レイアウト ページに *ラップ* されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b7bfc-153">たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Views/Home/Privacy.cshtml** ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="b7bfc-154">レイアウト ファイルでのタイトル、フッター、およびメニュー リンクの変更</span><span class="sxs-lookup"><span data-stu-id="b7bfc-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="b7bfc-155">*Views/Shared/_Layout.cshtml* ファイルの内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="b7bfc-156">変更が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-156">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7bfc-157">上記のマークアップでは、次の変更が加えられています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="b7bfc-158">`MvcMovie` ではなく `Movie App` の発生回数が 3 回になりました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="b7bfc-159">アンカー要素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` が `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` になりました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="b7bfc-160">上記のマークアップでは、このアプリで[領域](xref:mvc/controllers/areas)が使用されていないため、`asp-area=""` [アンカー タグ ヘルパー属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)と属性値は省略されました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="b7bfc-161">**注**:`Movies` コントローラーは実装されていません。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="b7bfc-162">この時点で、`Movie App`リンクは機能しません。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="b7bfc-163">ご自分の変更を保存し、**プライバシー** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="b7bfc-164">ブラウザー タブのタイトルが、**Privacy Policy - Mvc Movie** ではなく、**Privacy Policy - Movie App** になっていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![[プライバシー] タブ](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="b7bfc-166">**[ホーム]** リンクをタップし、タイトルとアンカー テキストにも **[Movie App]** と表示されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="b7bfc-167">レイアウト テンプレートで一度変更しただけで、サイト上のすべてのページに新しいリンク テキストと新しいタイトルが反映できました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="b7bfc-168">*Views/_ViewStart.cshtml* ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="b7bfc-169">*Views/_ViewStart.cshtml* ファイルは *Views/Shared/_Layout.cshtml* ファイルに取り込まれ、各ビューに適用されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="b7bfc-170">`Layout` プロパティを使用すれば、別のレイアウト ビューを設定することも、`null` に設定してレイアウト ファイルが使用されないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="b7bfc-171">*Views/HelloWorld/Index.cshtml* ビュー ファイルのタイトルと `<h2>` 要素を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="b7bfc-172">タイトルと `<h2>` 要素は若干異なります。これにより、コードのどの部分によって表示が変更されるのかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="b7bfc-173">上のコードの `ViewData["Title"] = "Movie List";` では、`Title` ディクショナリの `ViewData` プロパティを "Movie List" に設定します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="b7bfc-174">`Title` プロパティは、次のように、レイアウト ページの `<title>` HTML 要素で使用されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="b7bfc-175">変更内容を保存して、`https://localhost:{PORT}/HelloWorld` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b7bfc-176">ブラウザーのタイトル、プライマリ見出し、およびセカンダリ見出しが変更されていることに注意してください</span><span class="sxs-lookup"><span data-stu-id="b7bfc-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="b7bfc-177">(ブラウザーに変更内容が表示されない場合は、キャッシュされたコンテンツを表示している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="b7bfc-178">ブラウザーで Ctrl + F5 キーを押して、サーバーからの応答が強制的に読み込まれるようにしてください)。ブラウザーのタイトルは、*Index.cshtml* ビュー テンプレートで設定した `ViewData["Title"]` で作成されます。レイアウト ファイルには "- Movie App" が追加されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="b7bfc-179">*Index.cshtml* ビュー テンプレート内のコンテンツは、*Views/Shared/_Layout.cshtml* ビュー テンプレートにマージされます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="b7bfc-180">1 つの HTML 応答がブラウザーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="b7bfc-181">レイアウト テンプレートを使用すれば、アプリのすべてのページに適用される変更を簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="b7bfc-182">詳細については、「[Layout](xref:mvc/views/layout)」(レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![ムービー リスト ビュー](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="b7bfc-184">ここでは、"データ" のごく一部 (この場合は "Hello from our View Template!" というメッセージ) を</span><span class="sxs-lookup"><span data-stu-id="b7bfc-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="b7bfc-185">ハード コーディングしました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-185">message) is hard-coded, though.</span></span> <span data-ttu-id="b7bfc-186">MVC アプリケーションには "V" (ビュー) があり、"C" (コントローラー) もありますが、"M" (モデル) はまだありません。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="b7bfc-187">コントローラーからビューへのデータの受け渡し</span><span class="sxs-lookup"><span data-stu-id="b7bfc-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="b7bfc-188">コントローラー アクションは、受信 URL 要求への応答として呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="b7bfc-189">コントローラー クラスでは、受信ブラウザー要求を処理するコードが記述されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="b7bfc-190">コントローラーはデータ ソースからデータを取得し、ブラウザーに返す応答の種類を決定します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="b7bfc-191">ビュー テンプレートを使用すれば、コントローラーからブラウザーへの HTML 応答を生成して書式を設定できます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="b7bfc-192">コントローラーは、ビュー テンプレートで応答をレンダリングするために必要なデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="b7bfc-193">ベスト プラクティス: ビュー テンプレートでは、ビジネス ロジックを実行したり、データベースと直接やりとりしたり **しない** でください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="b7bfc-194">ビュー テンプレートでは、コントローラーによって提供されるデータのみを処理するようにしてください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="b7bfc-195">この "懸念事項の分離" を維持すれば、コードをクリーンでテスト可能な保守しやすい状態に保つことが楽になります。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="b7bfc-196">現時点では、`HelloWorldController` クラスの `Welcome` メソッドは `name` と `ID` パラメーターを受け取ってから、ブラウザーに直接値を出力します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="b7bfc-197">コントローラーにこの応答を文字列としてレンダリングさせるのではなく、代わりにビュー テンプレートを使用するようにコントローラーを変更します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="b7bfc-198">このビュー テンプレートでは動的応答が生成されます。これは、応答を生成するために、コントローラーからビューに適量のデータを渡す必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="b7bfc-199">そのためには、コントローラーで動的データ (パラメーター) を設定します。これは、ビュー テンプレートでアクセスできるようにするために `ViewData` ディレクトリに必要なデータです。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="b7bfc-200">*HelloWorldController.cs* 内で、`Welcome` メソッドを変更して `Message` および `NumTimes` 値を `ViewData` ディクショナリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="b7bfc-201">`ViewData` ディクショナリは動的オブジェクトです。つまり、任意の型を使用することができます。`ViewData` オブジェクトでは、その内部に何かを設定するまでプロパティは定義されません。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="b7bfc-202">[MVC のモデル バインド システム](xref:mvc/models/model-binding)は、名前付きパラメーター (`name` と `numTimes`) を、アドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="b7bfc-203">完全な *HelloWorldController.cs* ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="b7bfc-204">`ViewData` ディクショナリ オブジェクトには、ビューに渡されるデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="b7bfc-205">*Views/HelloWorld/Welcome.cshtml* という名前の [ようこそ] ビュー テンプレートを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="b7bfc-206">"Hello" `NumTimes` を表示する *Welcome.cshtml* ビュー テンプレートでループを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="b7bfc-207">*Views/HelloWorld/Welcome.cshtml* の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="b7bfc-208">変更内容を保存し、次の URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="b7bfc-209">データは URL から取得され、[MVC モデル バインダー](xref:mvc/models/model-binding)を使用してコントローラーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="b7bfc-210">コントローラーはデータを `ViewData` ディクショナリにパッケージ化し、そのオブジェクトをビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="b7bfc-211">その後、ビューでブラウザーに HTML としてデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-211">The view then renders the data as HTML to the browser.</span></span>

![[ようこそ] ラベルと、Hello Rick という語句が 4 つ示された [プライバシー] ビュー](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="b7bfc-213">上のサンプルでは、`ViewData` ディクショナリを使用して、コントローラーからビューにデータを渡しました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="b7bfc-214">チュートリアルの後半では、ビュー モデルを使用して、コントローラーからビューにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="b7bfc-215">一般には、`ViewData` ディクショナリを使用する方法より、ビュー モデルを使用してデータを渡す方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="b7bfc-216">詳細については、[ViewBag、ViewData、または TempData を使用するタイミング](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="b7bfc-217">次のチュートリアルでは、ムービーのデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b7bfc-218">[前へ](adding-controller.md)
> [次へ](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="b7bfc-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b7bfc-219">このセクションでは、[Razor](xref:mvc/views/razor) ビュー ファイルを使用して、クライアントへの HTML 応答を生成するプロセスを完全にカプセル化するために `HelloWorldController` クラスを変更します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="b7bfc-220">ビュー テンプレート ファイルは Razor を使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-220">You create a view template file using Razor.</span></span> <span data-ttu-id="b7bfc-221">Razor ベースのビュー テンプレートには *.cshtml* ファイル拡張子が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="b7bfc-222">C# を使用して HTML 出力を作成する洗練された方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="b7bfc-223">現在、`Index` メソッドは、コントローラー クラスでハード コーディングされるメッセージを含む文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="b7bfc-224">`HelloWorldController` クラスでは、`Index` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="b7bfc-225">上のコードでは、コントローラーの <xref:Microsoft.AspNetCore.Mvc.Controller.View*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="b7bfc-226">ビュー テンプレートを使用して、HTML 応答を生成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="b7bfc-227">上記の `Index` メソッドなどのコントローラー メソッド (*アクション メソッド* ともいう) は、一般に、`string` などの型ではなく、<xref:Microsoft.AspNetCore.Mvc.IActionResult> (または <xref:Microsoft.AspNetCore.Mvc.ActionResult> から派生したクラス) を返します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="b7bfc-228">ビューを追加する</span><span class="sxs-lookup"><span data-stu-id="b7bfc-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7bfc-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7bfc-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7bfc-230">*Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="b7bfc-231">*Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="b7bfc-232">**[新しい項目の追加 - MvcMovie]** ダイアログ</span><span class="sxs-lookup"><span data-stu-id="b7bfc-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="b7bfc-233">右上の検索ボックスに「*view*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="b7bfc-234">**[Razor ビュー]** を選択します</span><span class="sxs-lookup"><span data-stu-id="b7bfc-234">Select **Razor View**</span></span>

  * <span data-ttu-id="b7bfc-235">**[名前]** ボックスの値、*Index.cshtml* を維持します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="b7bfc-236">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-236">Select **Add**</span></span>

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7bfc-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7bfc-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b7bfc-239">`HelloWorldController` の `Index` ビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="b7bfc-240">*Views/HelloWorld* という名前の新しいフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="b7bfc-241">*Views/HelloWorld* フォルダー名 *Index.cshtml* に新しいファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7bfc-242">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b7bfc-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b7bfc-243">*Views* フォルダーを右クリックし、 **[追加]、[新しいフォルダー]** の順に選択し、フォルダーに *HelloWorld* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="b7bfc-244">*Views/HelloWorld* フォルダーを右クリックし、 **[追加]、[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="b7bfc-245">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b7bfc-246">左側のウィンドウで **[Web]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="b7bfc-247">中央のウィンドウで **[空の HTML ファイル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="b7bfc-248">**[名前]** ボックスに「*Index.cshtml*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="b7bfc-249">**[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-249">Select **New**.</span></span>

![[新しい項目の追加] ダイアログ](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="b7bfc-251">*Views/HelloWorld/Index.cshtml* Razor ビュー ファイルを次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="b7bfc-252">`https://localhost:{PORT}/HelloWorld` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b7bfc-253">`HelloWorldController` の `Index` メソッドでは多くのことは行いませんでした。つまり、ステートメント `return View();` を実行し、メソッドでビュー テンプレート ファイルを使用して、ブラウザーへの応答をレンダリングするよう指定しただけです。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="b7bfc-254">ビュー テンプレートのファイル名が指定されていないため、MVC では既定のビュー ファイルが使われました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="b7bfc-255">既定のビュー ファイルはメソッド (`Index`) と同じ名前なので、 */Views/HelloWorld/Index.cshtml* が使われます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="b7bfc-256">次のイメージは、ビューにハード コーディングされた "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="b7bfc-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="b7bfc-257">という文字列を示しています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-257">hard-coded in the view.</span></span>

![ブラウザー ウィンドウ](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="b7bfc-259">ビューとレイアウト ページを変更する</span><span class="sxs-lookup"><span data-stu-id="b7bfc-259">Change views and layout pages</span></span>

<span data-ttu-id="b7bfc-260">メニューのリンク ( **[MvcMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="b7bfc-261">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="b7bfc-262">メニューのレイアウトは、*Views/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="b7bfc-263">*Views/Shared/_Layout.cshtml* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="b7bfc-264">[[レイアウト]](xref:mvc/views/layout) テンプレートでは、1 か所でサイトの HTML コンテナー レイアウトを指定し、それをサイト内の複数のページに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="b7bfc-265">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="b7bfc-266">`RenderBody` は、作成したビュー固有のページがすべて表示されるプレースホルダーで、レイアウト ページに *ラップ* されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="b7bfc-267">たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Views/Home/Privacy.cshtml** ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="b7bfc-268">レイアウト ファイルでのタイトル、フッター、およびメニュー リンクの変更</span><span class="sxs-lookup"><span data-stu-id="b7bfc-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="b7bfc-269">タイトル要素とフッター要素で、`MvcMovie` を `Movie App` に変更します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="b7bfc-270">アンカー要素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` を `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` に変更します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="b7bfc-271">次のマークアップには、強調表示された変更点が示されています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-271">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="b7bfc-272">上記のマークアップでは、このアプリで[領域](xref:mvc/controllers/areas)が使用されていないため、`asp-area` [アンカー タグ ヘルパー属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)は省略されました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="b7bfc-273">**注**:`Movies` コントローラーは実装されていません。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="b7bfc-274">この時点で、`Movie App`リンクは機能しません。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="b7bfc-275">ご自分の変更を保存し、**プライバシー** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="b7bfc-276">ブラウザー タブのタイトルが、**Privacy Policy - Mvc Movie** ではなく、**Privacy Policy - Movie App** になっていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![[プライバシー] タブ](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="b7bfc-278">**[ホーム]** リンクをタップし、タイトルとアンカー テキストにも **[Movie App]** と表示されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="b7bfc-279">レイアウト テンプレートで一度変更しただけで、サイト上のすべてのページに新しいリンク テキストと新しいタイトルが反映できました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="b7bfc-280">*Views/_ViewStart.cshtml* ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="b7bfc-281">*Views/_ViewStart.cshtml* ファイルは *Views/Shared/_Layout.cshtml* ファイルに取り込まれ、各ビューに適用されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="b7bfc-282">`Layout` プロパティを使用すれば、別のレイアウト ビューを設定することも、`null` に設定してレイアウト ファイルが使用されないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="b7bfc-283">*Views/HelloWorld/Index.cshtml* ビュー ファイルのタイトルと `<h2>` 要素を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="b7bfc-284">タイトルと `<h2>` 要素は若干異なります。これにより、コードのどの部分によって表示が変更されるのかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="b7bfc-285">上のコードの `ViewData["Title"] = "Movie List";` では、`Title` ディクショナリの `ViewData` プロパティを "Movie List" に設定します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="b7bfc-286">`Title` プロパティは、次のように、レイアウト ページの `<title>` HTML 要素で使用されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="b7bfc-287">変更内容を保存して、`https://localhost:{PORT}/HelloWorld` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="b7bfc-288">ブラウザーのタイトル、プライマリ見出し、およびセカンダリ見出しが変更されていることに注意してください</span><span class="sxs-lookup"><span data-stu-id="b7bfc-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="b7bfc-289">(ブラウザーに変更内容が表示されない場合は、キャッシュされたコンテンツを表示している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="b7bfc-290">ブラウザーで Ctrl + F5 キーを押して、サーバーからの応答が強制的に読み込まれるようにしてください)。ブラウザーのタイトルは、*Index.cshtml* ビュー テンプレートで設定した `ViewData["Title"]` で作成されます。レイアウト ファイルには "- Movie App" が追加されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="b7bfc-291">*Index.cshtml* ビュー テンプレートのコンテンツがどのように *Views/Shared/_Layout.cshtml* ビュー テンプレートにマージされ、1 つの HTML 応答がブラウザーに送信されたかにも注目してください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="b7bfc-292">レイアウト テンプレートを使用すれば、アプリケーションのすべてのページに適用される変更をとても簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="b7bfc-293">詳細については、「[Layout](xref:mvc/views/layout)」 (レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![ムービー リスト ビュー](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="b7bfc-295">ここでは、"データ" のごく一部 (この場合は "Hello from our View Template!" というメッセージ) を</span><span class="sxs-lookup"><span data-stu-id="b7bfc-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="b7bfc-296">ハード コーディングしました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-296">message) is hard-coded, though.</span></span> <span data-ttu-id="b7bfc-297">MVC アプリケーションには "V" (ビュー) があり、"C" (コントローラー) もありますが、"M" (モデル) はまだありません。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="b7bfc-298">コントローラーからビューへのデータの受け渡し</span><span class="sxs-lookup"><span data-stu-id="b7bfc-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="b7bfc-299">コントローラー アクションは、受信 URL 要求への応答として呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="b7bfc-300">コントローラー クラスでは、受信ブラウザー要求を処理するコードが記述されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="b7bfc-301">コントローラーはデータ ソースからデータを取得し、ブラウザーに返す応答の種類を決定します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="b7bfc-302">ビュー テンプレートを使用すれば、コントローラーからブラウザーへの HTML 応答を生成して書式を設定できます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="b7bfc-303">コントローラーは、ビュー テンプレートで応答をレンダリングするために必要なデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="b7bfc-304">ベスト プラクティス: ビュー テンプレートでは、ビジネス ロジックを実行したり、データベースと直接やりとりしたり **しない** でください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="b7bfc-305">ビュー テンプレートでは、コントローラーによって提供されるデータのみを処理するようにしてください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="b7bfc-306">この "懸念事項の分離" を維持すれば、コードをクリーンでテスト可能な保守しやすい状態に保つことが楽になります。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="b7bfc-307">現時点では、`HelloWorldController` クラスの `Welcome` メソッドは `name` と `ID` パラメーターを受け取ってから、ブラウザーに直接値を出力します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="b7bfc-308">コントローラーにこの応答を文字列としてレンダリングさせるのではなく、代わりにビュー テンプレートを使用するようにコントローラーを変更します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="b7bfc-309">このビュー テンプレートでは動的応答が生成されます。これは、応答を生成するために、コントローラーからビューに適量のデータを渡す必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="b7bfc-310">そのためには、コントローラーで動的データ (パラメーター) を設定します。これは、ビュー テンプレートでアクセスできるようにするために `ViewData` ディレクトリに必要なデータです。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="b7bfc-311">*HelloWorldController.cs* 内で、`Welcome` メソッドを変更して `Message` および `NumTimes` 値を `ViewData` ディクショナリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="b7bfc-312">`ViewData` ディクショナリは動的オブジェクトです。つまり、任意の型を使用することができます。`ViewData` オブジェクトでは、その内部に何かを設定するまでプロパティは定義されません。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="b7bfc-313">[MVC のモデル バインド システム](xref:mvc/models/model-binding)は、名前付きパラメーター (`name` と `numTimes`) を、アドレス バーのクエリ文字列からメソッドのパラメーターに自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="b7bfc-314">完全な *HelloWorldController.cs* ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="b7bfc-315">`ViewData` ディクショナリ オブジェクトには、ビューに渡されるデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="b7bfc-316">*Views/HelloWorld/Welcome.cshtml* という名前の [ようこそ] ビュー テンプレートを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="b7bfc-317">"Hello" `NumTimes` を表示する *Welcome.cshtml* ビュー テンプレートでループを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="b7bfc-318">*Views/HelloWorld/Welcome.cshtml* の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="b7bfc-319">変更内容を保存し、次の URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="b7bfc-320">データは URL から取得され、[MVC モデル バインダー](xref:mvc/models/model-binding)を使用してコントローラーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="b7bfc-321">コントローラーはデータを `ViewData` ディクショナリにパッケージ化し、そのオブジェクトをビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="b7bfc-322">その後、ビューでブラウザーに HTML としてデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-322">The view then renders the data as HTML to the browser.</span></span>

![[ようこそ] ラベルと、Hello Rick という語句が 4 つ示された [プライバシー] ビュー](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="b7bfc-324">上のサンプルでは、`ViewData` ディクショナリを使用して、コントローラーからビューにデータを渡しました。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="b7bfc-325">チュートリアルの後半では、ビュー モデルを使用して、コントローラーからビューにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="b7bfc-326">一般には、`ViewData` ディクショナリを使用する方法より、ビュー モデルを使用してデータを渡す方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="b7bfc-327">詳細については、[ViewBag、ViewData、または TempData を使用するタイミング](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="b7bfc-328">次のチュートリアルでは、ムービーのデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b7bfc-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b7bfc-329">[前へ](adding-controller.md)
> [次へ](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="b7bfc-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

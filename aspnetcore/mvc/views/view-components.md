---
title: ASP.NET Core のビュー コンポーネント
author: rick-anderson
description: ASP.NET Core でのビュー コンポーネントの使用方法とそれらをアプリに追加する方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/view-components
ms.openlocfilehash: e0ff97b53d12fbf6c6a89e94704de1aee9d7f9e6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060587"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="c3721-103">ASP.NET Core のビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c3721-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="c3721-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c3721-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c3721-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c3721-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="c3721-106">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c3721-106">View components</span></span>

<span data-ttu-id="c3721-107">ビュー コンポーネントは部分ビューと似ていますが、はるかに強力なものです。</span><span class="sxs-lookup"><span data-stu-id="c3721-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="c3721-108">ビュー コンポーネントでは、モデル バインドを使用せず、呼び出すときに指定されたデータのみに依存します。</span><span class="sxs-lookup"><span data-stu-id="c3721-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="c3721-109">この記事はコントローラーとビューを使用して記述されていますが、ビューコンポーネントはページでも機能し :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-109">This article was written using controllers and views, but view components also work with :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="c3721-110">ビュー コンポーネントの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3721-110">A view component:</span></span>

* <span data-ttu-id="c3721-111">応答全体ではなく、チャンクをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="c3721-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="c3721-112">コントローラーとビューの間にあるのと同じ関心の分離とテストの容易性の利点があります。</span><span class="sxs-lookup"><span data-stu-id="c3721-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="c3721-113">パラメーターとビジネス ロジックを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c3721-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="c3721-114">通常、レイアウト ページから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="c3721-115">ビュー コンポーネントは、次のような部分ビューには複雑すぎる、再利用可能なレンダリング ロジックをどこでも使用できるようにするためのものです。</span><span class="sxs-lookup"><span data-stu-id="c3721-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="c3721-116">動的なナビゲーション メニュー</span><span class="sxs-lookup"><span data-stu-id="c3721-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="c3721-117">タグ クラウド (データベースをクエリする場所)</span><span class="sxs-lookup"><span data-stu-id="c3721-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="c3721-118">ログイン パネル</span><span class="sxs-lookup"><span data-stu-id="c3721-118">Login panel</span></span>
* <span data-ttu-id="c3721-119">ショッピング カート</span><span class="sxs-lookup"><span data-stu-id="c3721-119">Shopping cart</span></span>
* <span data-ttu-id="c3721-120">新着情報の記事</span><span class="sxs-lookup"><span data-stu-id="c3721-120">Recently published articles</span></span>
* <span data-ttu-id="c3721-121">一般的なブログのサイドバーのコンテンツ</span><span class="sxs-lookup"><span data-stu-id="c3721-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="c3721-122">すべてのページでレンダリングされ、ユーザーの状態のログに応じて、ログアウトまたはログインのいずれかのリンクを示すログイン パネル</span><span class="sxs-lookup"><span data-stu-id="c3721-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="c3721-123">ビュー コンポーネントは、次の 2 つのパーツで構成されます。クラス (通常、[ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent) から派生) と、クラスで返される結果 (通常はビュー) です。</span><span class="sxs-lookup"><span data-stu-id="c3721-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="c3721-124">コントローラーと同様に、ビュー コンポーネントは POCO の場合がありますが、ほとんどの開発者は `ViewComponent` から派生させて、利用できるメソッドとプロパティを活用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3721-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="c3721-125">ビューコンポーネントがアプリの仕様を満たしているかどうかを検討する場合は、代わりにコンポーネントを使用することを検討してください :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="c3721-125">When considering if view components meet an app's specifications, consider using :::no-loc(Razor)::: Components instead.</span></span> <span data-ttu-id="c3721-126">:::no-loc(Razor)::: コンポーネントでは、マークアップと C# コードを組み合わせて、再利用可能な UI ユニットを生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="c3721-126">:::no-loc(Razor)::: Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="c3721-127">:::no-loc(Razor)::: コンポーネントは、クライアント側の UI ロジックとコンポジションを提供するときに、開発者の生産性を高めるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="c3721-127">:::no-loc(Razor)::: Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="c3721-128">詳細については、「<xref:blazor/components/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3721-128">For more information, see <xref:blazor/components/index>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="c3721-129">ビューのコンポーネントを作成する</span><span class="sxs-lookup"><span data-stu-id="c3721-129">Creating a view component</span></span>

<span data-ttu-id="c3721-130">このセクションには、ビュー コンポーネントを作成するための高レベルの要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c3721-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="c3721-131">記事の後半で、各ステップの詳細を検証し、ビュー コンポーネントを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3721-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="c3721-132">ビュー コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="c3721-132">The view component class</span></span>

<span data-ttu-id="c3721-133">ビュー コンポーネント クラスは、次のいずれかによって作成できます。</span><span class="sxs-lookup"><span data-stu-id="c3721-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="c3721-134">*ViewComponent* から派生させる</span><span class="sxs-lookup"><span data-stu-id="c3721-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="c3721-135">`[ViewComponent]` 属性でクラスを装飾するか、`[ViewComponent]` 属性でクラスから派生させる</span><span class="sxs-lookup"><span data-stu-id="c3721-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="c3721-136">名前がサフィックス *ViewComponent* で終わるクラスを作成する</span><span class="sxs-lookup"><span data-stu-id="c3721-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="c3721-137">コントローラーと同様に、ビュー コンポーネントは、パブリック クラス、入れ子にされていないクラス、および非抽象クラスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3721-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="c3721-138">ビュー コンポーネント名は、"ViewComponent" サフィックスを除いたクラス名です。</span><span class="sxs-lookup"><span data-stu-id="c3721-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="c3721-139">また、これは `ViewComponentAttribute.Name` プロパティを使用して、明示的に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="c3721-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="c3721-140">ビュー コンポーネント クラスの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3721-140">A view component class:</span></span>

* <span data-ttu-id="c3721-141">コンストラクターの[依存性の注入](../../fundamentals/dependency-injection.md)を完全にサポートします</span><span class="sxs-lookup"><span data-stu-id="c3721-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="c3721-142">コントローラーのライフサイクルに関わりません。つまり、ビュー コンポーネントで[フィルター](../controllers/filters.md)を使用できないということです</span><span class="sxs-lookup"><span data-stu-id="c3721-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="c3721-143">ビュー コンポーネント メソッド</span><span class="sxs-lookup"><span data-stu-id="c3721-143">View component methods</span></span>

<span data-ttu-id="c3721-144">ビュー コンポーネントでは、`Task<IViewComponentResult>` を返す `InvokeAsync` メソッドまたは `IViewComponentResult` を返す同期 `Invoke` メソッドでロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="c3721-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="c3721-145">パラメーターは、モデル バインドではなく、ビュー コンポーネントから直接取得します。</span><span class="sxs-lookup"><span data-stu-id="c3721-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="c3721-146">ビュー コンポーネントが要求を直接処理することはありません。</span><span class="sxs-lookup"><span data-stu-id="c3721-146">A view component never directly handles a request.</span></span> <span data-ttu-id="c3721-147">通常、ビュー コンポーネントは、モデルを初期化し、`View` メソッドを呼び出してビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="c3721-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="c3721-148">要約すると、ビュー コンポーネント メソッドの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3721-148">In summary, view component methods:</span></span>

* <span data-ttu-id="c3721-149">`Task<IViewComponentResult>` を返す `InvokeAsync` メソッドまたは `IViewComponentResult` を返す同期 `Invoke` メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="c3721-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="c3721-150">通常、モデルを初期化し、メソッドを呼び出してビューに渡し `ViewComponent` `View` ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="c3721-151">パラメーターは HTTP ではなく、呼び出し元のメソッドから取得されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="c3721-152">モデル バインドはありません。</span><span class="sxs-lookup"><span data-stu-id="c3721-152">There's no model binding.</span></span>
* <span data-ttu-id="c3721-153">HTTP エンドポイントとして直接到達することはできません。</span><span class="sxs-lookup"><span data-stu-id="c3721-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="c3721-154">(通常はビュー内で) コードから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="c3721-155">ビュー コンポーネントでは要求が処理されません。</span><span class="sxs-lookup"><span data-stu-id="c3721-155">A view component never handles a request.</span></span>
* <span data-ttu-id="c3721-156">現在の HTTP 要求からの詳細ではなく、シグネチャでオーバーロードされます。</span><span class="sxs-lookup"><span data-stu-id="c3721-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="c3721-157">ビューの検索パス</span><span class="sxs-lookup"><span data-stu-id="c3721-157">View search path</span></span>

<span data-ttu-id="c3721-158">ランタイムでは、次のパスでビューを検索します。</span><span class="sxs-lookup"><span data-stu-id="c3721-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="c3721-159">/Views/{コントローラー名}/Components/{ビュー コンポーネント名}/{ビュー名}</span><span class="sxs-lookup"><span data-stu-id="c3721-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="c3721-160">/Views/Shared/Components/{ビュー コンポーネント名}/{ビュー名}</span><span class="sxs-lookup"><span data-stu-id="c3721-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="c3721-161">/Pages/Shared/Components/{ビュー コンポーネント名}/{ビュー名}</span><span class="sxs-lookup"><span data-stu-id="c3721-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="c3721-162">検索パスは、コントローラー + ビューおよびページを使用してプロジェクトに適用され :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-162">The search path applies to projects using controllers + views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="c3721-163">ビュー コンポーネントの既定のビュー名は、 *Default* です。つまり、通常、ビュー ファイルは *Default.cshtml* という名前になるということです。</span><span class="sxs-lookup"><span data-stu-id="c3721-163">The default view name for a view component is *Default* , which means your view file will typically be named *Default.cshtml* .</span></span> <span data-ttu-id="c3721-164">ビュー コンポーネントの結果を作成したり、`View` メソッドを呼び出したりするときに、別のビュー名を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="c3721-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="c3721-165">ビュー ファイルに *Default.cshtml* という名前を付けて、" *Views/Shared/Components/{ビュー コンポーネント名}/{ビュー名}* " というパスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c3721-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="c3721-166">このサンプルで使用される `PriorityList` ビュー コンポーネントは、ビュー コンポーネント ビューに *Views/Shared/Components/PriorityList/Default.cshtml* を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3721-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="c3721-167">ビューの検索パスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="c3721-167">Customize the view search path</span></span>

<span data-ttu-id="c3721-168">ビューの検索パスをカスタマイズするには、のコレクションを変更し :::no-loc(Razor)::: <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::ViewEngineOptions.ViewLocationFormats> ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-168">To customize the view search path, modify :::no-loc(Razor):::'s <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::ViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="c3721-169">たとえば、"/Components/{ビュー コンポーネント名}/{ビュー名}" というパス内のビューを検索するには、次のように新しい項目をコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="c3721-169">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="c3721-170">上記のコードでは、プレースホルダー "{0}" はパス "/Components/{ビュー コンポーネント名}/{ビュー名}" を表しています。</span><span class="sxs-lookup"><span data-stu-id="c3721-170">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="c3721-171">ビュー コンポーネントを呼び出す</span><span class="sxs-lookup"><span data-stu-id="c3721-171">Invoking a view component</span></span>

<span data-ttu-id="c3721-172">ビュー コンポーネントを使用するには、ビュー内で以下を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3721-172">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="c3721-173">パラメーターは、`InvokeAsync` メソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-173">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="c3721-174">`PriorityList`記事で開発したビューコンポーネントは、 *Views/ToDo/Index. cshtml* ビューファイルから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-174">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="c3721-175">以下では、`InvokeAsync` メソッドは、2 つのパラメーターで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-175">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="c3721-176">タグ ヘルパーとしてビュー コンポーネントを呼び出す</span><span class="sxs-lookup"><span data-stu-id="c3721-176">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="c3721-177">ASP.NET Core 1.1 以降の場合は、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)としてビュー コンポーネントを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="c3721-177">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="c3721-178">タグ ヘルパーのパスカル ケースのクラスとメソッドのパラメーターは、それぞれ[ケバブ ケース](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)に変換されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-178">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="c3721-179">ビュー コンポーネントを呼び出すタグ ヘルパーでは、`<vc></vc>` 要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3721-179">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="c3721-180">ビュー コンポーネントは、次のように指定されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-180">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="c3721-181">ビュー コンポーネントをタグ ヘルパーとして使用するには、`@addTagHelper` ディレクティブを使用して、ビュー コンポーネントを含むアセンブリを登録します。</span><span class="sxs-lookup"><span data-stu-id="c3721-181">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="c3721-182">ビュー コンポーネントが `MyWebApp` と呼ばれるアセンブリにある場合は、次のディレクティブを *_ViewImports.cshtml* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c3721-182">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="c3721-183">ビュー コンポーネントを参照する任意のファイルへのタグ ヘルパーとして、ビュー コンポーネントを登録できます。</span><span class="sxs-lookup"><span data-stu-id="c3721-183">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="c3721-184">タグ ヘルパーを登録する方法の詳細については、「[Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)」 (タグ ヘルパーのスコープの管理) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3721-184">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="c3721-185">このチュートリアルで使用される `InvokeAsync` メソッドは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c3721-185">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="c3721-186">タグ ヘルパーのマークアップでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c3721-186">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="c3721-187">上記のサンプルでは、`PriorityList` ビュー コンポーネントは `priority-list` になります。</span><span class="sxs-lookup"><span data-stu-id="c3721-187">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="c3721-188">ビュー コンポーネントに対するパラメーターは、ケバブ ケースの属性として渡されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-188">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="c3721-189">ビュー コンポーネントをコントローラーから直接呼び出す</span><span class="sxs-lookup"><span data-stu-id="c3721-189">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="c3721-190">通常、ビュー コンポーネントはビューから呼び出されますが、コントローラー メソッドから直接呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="c3721-190">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="c3721-191">ビュー コンポーネントでコントローラーなどのエンドポイントを定義しないときに、`ViewComponentResult` のコンテンツを返すコントローラー アクションを簡単に実装できます。</span><span class="sxs-lookup"><span data-stu-id="c3721-191">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="c3721-192">この例では、ビュー コンポーネントは、コントローラーから直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-192">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="c3721-193">チュートリアル: 単純なビュー コンポーネントの作成</span><span class="sxs-lookup"><span data-stu-id="c3721-193">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="c3721-194">スタート コードを[ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)、ビルド、およびテストします。</span><span class="sxs-lookup"><span data-stu-id="c3721-194">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="c3721-195">これは、 `ToDo` *ToDo* 項目の一覧を表示するコントローラーを持つ単純なプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="c3721-195">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![[ToDo] のリスト](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="c3721-197">ViewComponent クラスの追加</span><span class="sxs-lookup"><span data-stu-id="c3721-197">Add a ViewComponent class</span></span>

<span data-ttu-id="c3721-198">*ViewComponents* フォルダーを作成して、次の `PriorityListViewComponent` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c3721-198">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="c3721-199">コードに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="c3721-199">Notes on the code:</span></span>

* <span data-ttu-id="c3721-200">ビュー コンポーネント クラスは、プロジェクト内の **任意** のフォルダーに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c3721-200">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="c3721-201">クラス名 PriorityList **ViewComponent** は、サフィックス **ViewComponent** で終わるため、ビューからクラス コンポーネントを参照するときに、ランタイムでは文字列 "PriorityList" を使用します。</span><span class="sxs-lookup"><span data-stu-id="c3721-201">Because the class name PriorityList **ViewComponent** ends with the suffix **ViewComponent** , the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="c3721-202">これについては、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="c3721-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="c3721-203">`[ViewComponent]` 属性では、ビュー コンポーネントを参照するために使用する名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="c3721-203">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="c3721-204">たとえば、クラスに `XYZ` という名前を付けて、`ViewComponent` 属性を適用することができます。</span><span class="sxs-lookup"><span data-stu-id="c3721-204">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="c3721-205">上述の `[ViewComponent]` 属性は、ビュー コンポーネント セレクターに、コンポーネントに関連付けられたビューを探す場合は `PriorityList` という名前を使用し、ビューからクラス コンポーネントを参照する場合は文字列 "PriorityList" を使用するように指示します。</span><span class="sxs-lookup"><span data-stu-id="c3721-205">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="c3721-206">これについては、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="c3721-206">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="c3721-207">コンポーネントでは、[依存性の注入](../../fundamentals/dependency-injection.md)を使用して、データ コンテキストを利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c3721-207">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="c3721-208">`InvokeAsync` ではビューから呼び出すことができるメソッドを表示し、任意の数の引数を取得できます。</span><span class="sxs-lookup"><span data-stu-id="c3721-208">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="c3721-209">`InvokeAsync` メソッドでは、`isDone` と `maxPriority` パラメーターを満たす `ToDo` 項目のセットを返します。</span><span class="sxs-lookup"><span data-stu-id="c3721-209">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-no-locrazor-view"></a><span data-ttu-id="c3721-210">ビューコンポーネントビューを作成する :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="c3721-210">Create the view component :::no-loc(Razor)::: view</span></span>

* <span data-ttu-id="c3721-211">*Views/Shared/Components* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3721-211">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="c3721-212">このフォルダーは、 *Components* という名前にする **必要があります** 。</span><span class="sxs-lookup"><span data-stu-id="c3721-212">This folder **must** be named *Components* .</span></span>

* <span data-ttu-id="c3721-213">*Views/Shared/Components/PriorityList* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3721-213">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="c3721-214">このフォルダー名は、ビュー コンポーネント クラスの名前、または (規則に従い、クラス名に *ViewComponent* サフィックスを使用した場合は) サフィックスを差し引いたクラスの名前に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3721-214">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="c3721-215">`ViewComponent` 属性を使用した場合は、クラス名は属性の指定に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c3721-215">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="c3721-216">Views/ *Shared/Components/優先順位リスト/既定の cshtml* ビューを作成し :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-216">Create a *Views/Shared/Components/PriorityList/Default.cshtml* :::no-loc(Razor)::: view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="c3721-217">:::no-loc(Razor):::ビューはの一覧を取得し、表示し `TodoItem` ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-217">The :::no-loc(Razor)::: view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="c3721-218">ビュー コンポーネントの `InvokeAsync` メソッドで (サンプルのように) ビューの名前を渡さない場合、 *Default* が規則によってビュー名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-218">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="c3721-219">このチュートリアルの後半で、ビューの名前を渡す方法について示します。</span><span class="sxs-lookup"><span data-stu-id="c3721-219">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="c3721-220">特定のコントローラーの既定のスタイルをオーバーライドするには、コントローラー固有のビューフォルダーにビューを追加します (たとえば、 *Views/ToDo/Components/指定リスト/既定値)* 。</span><span class="sxs-lookup"><span data-stu-id="c3721-220">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)* .</span></span>

    <span data-ttu-id="c3721-221">ビューコンポーネントがコントローラー固有の場合は、コントローラー固有のフォルダーに追加できます ( *Views/ToDo/Components/の優先リスト/既定値* )。</span><span class="sxs-lookup"><span data-stu-id="c3721-221">If the view component is controller-specific, you can add it to the controller-specific folder ( *Views/ToDo/Components/PriorityList/Default.cshtml* ).</span></span>

* <span data-ttu-id="c3721-222">`div`Priority list コンポーネントへの呼び出しを含むを *Views/ToDo/index. cshtml* ファイルの一番下に追加します。</span><span class="sxs-lookup"><span data-stu-id="c3721-222">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="c3721-223">`@await Component.InvokeAsync` マークアップは、ビュー コンポーネントを呼び出すための構文を示します。</span><span class="sxs-lookup"><span data-stu-id="c3721-223">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="c3721-224">最初の引数は、呼び出す必要があるコンポーネントの名前です。</span><span class="sxs-lookup"><span data-stu-id="c3721-224">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="c3721-225">後続のパラメーターは、そのコンポーネントに渡されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-225">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="c3721-226">`InvokeAsync` では、任意の数の引数を取得できます。</span><span class="sxs-lookup"><span data-stu-id="c3721-226">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="c3721-227">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="c3721-227">Test the app.</span></span> <span data-ttu-id="c3721-228">次の画像は、[ToDo] リストと優先順位の項目を示します。</span><span class="sxs-lookup"><span data-stu-id="c3721-228">The following image shows the ToDo list and the priority items:</span></span>

![[ToDo] リストと優先順位の項目](view-components/_static/pi.png)

<span data-ttu-id="c3721-230">また、コントローラーから直接ビュー コンポーネントを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="c3721-230">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC アクションからの優先順位の項目](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="c3721-232">ビュー名を指定する</span><span class="sxs-lookup"><span data-stu-id="c3721-232">Specifying a view name</span></span>

<span data-ttu-id="c3721-233">複雑なビュー コンポーネントでは、いくつかの条件下で、既定以外のビューを指定する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="c3721-233">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="c3721-234">次のコードでは、`InvokeAsync` メソッドから "PVC" ビューを指定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="c3721-234">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="c3721-235">`PriorityListViewComponent` クラスで `InvokeAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="c3721-235">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="c3721-236">*Views/Shared/Components/PriorityList/Default.cshtml* ファイルを *Views/Shared/Components/PriorityList/PVC.cshtml* という名前のビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="c3721-236">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml* .</span></span> <span data-ttu-id="c3721-237">PVC ビューが使用されていることを示すために、見出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="c3721-237">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="c3721-238">*Views/ToDo/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="c3721-238">Update *Views/ToDo/Index.cshtml* :</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="c3721-239">アプリを実行して、PVC ビューを確認します。</span><span class="sxs-lookup"><span data-stu-id="c3721-239">Run the app and verify PVC view.</span></span>

![優先順位のビュー コンポーネント](view-components/_static/pvc.png)

<span data-ttu-id="c3721-241">PVC ビューがレンダリングされない場合は、4 以上の優先順位でビュー コンポーネントを呼び出していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c3721-241">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="c3721-242">ビューのパスを調べる</span><span class="sxs-lookup"><span data-stu-id="c3721-242">Examine the view path</span></span>

* <span data-ttu-id="c3721-243">優先順位ビューが返されないように、優先順位パラメーターを 3 以下に変更します。</span><span class="sxs-lookup"><span data-stu-id="c3721-243">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="c3721-244">*Views/ToDo/Components/の優先リスト/既定値* の名前を一時的 *に変更します。*</span><span class="sxs-lookup"><span data-stu-id="c3721-244">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml* .</span></span>
* <span data-ttu-id="c3721-245">アプリをテストすると、次のエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-245">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="c3721-246">*Views/ToDo/components/優先順位リスト/1Default. cshtml* を *views/Shared/Components/優先順位リスト/既定の cshtml* にコピーします。</span><span class="sxs-lookup"><span data-stu-id="c3721-246">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml* .</span></span>
* <span data-ttu-id="c3721-247">*共有の ToDo ビュー* コンポーネントビューにマークアップを追加して、ビューが *共有* フォルダーからのものであることを示します。</span><span class="sxs-lookup"><span data-stu-id="c3721-247">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="c3721-248">**[Shared]** コンポーネント ビューをテストします。</span><span class="sxs-lookup"><span data-stu-id="c3721-248">Test the **Shared** component view.</span></span>

![[Shared] コンポーネント ビューを含む [ToDo] 出力](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="c3721-250">ハードコーディングされた文字列の回避</span><span class="sxs-lookup"><span data-stu-id="c3721-250">Avoiding hard-coded strings</span></span>

<span data-ttu-id="c3721-251">コンパイル時間の安全性を確保する必要がある場合は、ハードコーディングされたビュー コンポーネント名をクラス名に置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="c3721-251">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="c3721-252">"ViewComponent" サフィックスのないビュー コンポーネントを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3721-252">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="c3721-253">ステートメントを `using` :::no-loc(Razor)::: ビューファイルに追加し、演算子を使用し `nameof` ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-253">Add a `using` statement to your :::no-loc(Razor)::: view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="c3721-254">同期作業を実行する</span><span class="sxs-lookup"><span data-stu-id="c3721-254">Perform synchronous work</span></span>

<span data-ttu-id="c3721-255">非同期作業を実行する必要がない場合は、フレームワークで同期 `Invoke` メソッドの呼び出しが処理されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-255">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="c3721-256">次のメソッドでは同期 `Invoke` ビュー コンポーネントを作成します。</span><span class="sxs-lookup"><span data-stu-id="c3721-256">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="c3721-257">ビューコンポーネントのファイルには、 :::no-loc(Razor)::: メソッドに渡された文字列が一覧表示され `Invoke` ます ( *Views/Home/Components/の設定/既定値は cshtml* )。</span><span class="sxs-lookup"><span data-stu-id="c3721-257">The view component's :::no-loc(Razor)::: file lists the strings passed to the `Invoke` method ( *Views/Home/Components/PriorityList/Default.cshtml* ):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="c3721-258">ビューコンポーネントは、 :::no-loc(Razor)::: 次のいずれかの方法を使用して、ファイル ( *Views/Home/Index. cshtml* など) で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c3721-258">The view component is invoked in a :::no-loc(Razor)::: file (for example, *Views/Home/Index.cshtml* ) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="c3721-259">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="c3721-259">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="c3721-260"><xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> の方法を使用するには、`Component.InvokeAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3721-260">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="c3721-261">ビューコンポーネントが :::no-loc(Razor)::: ファイル (たとえば、 *Views/Home/Index. cshtml* ) で呼び出され <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-261">The view component is invoked in a :::no-loc(Razor)::: file (for example, *Views/Home/Index.cshtml* ) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="c3721-262">`Component.InvokeAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c3721-262">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="c3721-263">タグ ヘルパーを使用するには、`@addTagHelper` ディレクティブを使用して、ビュー コンポーネントを含むアセンブリを登録します (ビュー コンポーネントは、`MyWebApp` と呼ばれるアセンブリ内にあります)。</span><span class="sxs-lookup"><span data-stu-id="c3721-263">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="c3721-264">マークアップファイルのビューコンポーネントタグヘルパーを使用し :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-264">Use the view component Tag Helper in the :::no-loc(Razor)::: markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="c3721-265">のメソッドシグネチャ `PriorityList.Invoke` は同期ですが、 :::no-loc(Razor)::: マークアップファイル内のでメソッドを検索して呼び出し `Component.InvokeAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="c3721-265">The method signature of `PriorityList.Invoke` is synchronous, but :::no-loc(Razor)::: finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="c3721-266">ビュー コンポーネントのすべてのパラメーターが必要</span><span class="sxs-lookup"><span data-stu-id="c3721-266">All view component parameters are required</span></span>

<span data-ttu-id="c3721-267">ビュー コンポーネントの各パラメーターは、必須の属性です。</span><span class="sxs-lookup"><span data-stu-id="c3721-267">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="c3721-268">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5011)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c3721-268">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="c3721-269">パラメーターを省略した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c3721-269">If any  parameter is omitted:</span></span>

* <span data-ttu-id="c3721-270">`InvokeAsync` メソッドのシグネチャが一致しないため、メソッドが実行されません。</span><span class="sxs-lookup"><span data-stu-id="c3721-270">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="c3721-271">ViewComponent がマークアップをレンダリングしません。</span><span class="sxs-lookup"><span data-stu-id="c3721-271">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="c3721-272">エラーがスローされません。</span><span class="sxs-lookup"><span data-stu-id="c3721-272">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3721-273">その他の資料</span><span class="sxs-lookup"><span data-stu-id="c3721-273">Additional resources</span></span>

* [<span data-ttu-id="c3721-274">ビューへの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="c3721-274">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)

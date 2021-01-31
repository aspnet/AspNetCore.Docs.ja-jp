---
title: ASP.NET Core でのコントローラー アクションへのルーティング
author: rick-anderson
description: ASP.NET Core MVC でルーティング ミドルウェアを使って、受信した要求の URL を照合し、アクションにマップする方法について説明します。
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: 0863b5758f33b720636f3b927fcb9014cd106c21
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217545"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="59af9-103">ASP.NET Core でのコントローラー アクションへのルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="59af9-104">作成者: [Ryan Nowak](https://github.com/rynowak)、[Kirk Larkin](https://twitter.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="59af9-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59af9-105">ASP.NET Core コントローラーは、ルーティング [ミドルウェア](xref:fundamentals/middleware/index) を使用して受信要求の url を照合し、 [アクション](#action)にマップします。</span><span class="sxs-lookup"><span data-stu-id="59af9-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="59af9-106">ルートテンプレート:</span><span class="sxs-lookup"><span data-stu-id="59af9-106">Route templates:</span></span>

* <span data-ttu-id="59af9-107">はスタートアップコードまたは属性で定義されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="59af9-108">URL パスと [アクション](#action)の照合方法を記述します。</span><span class="sxs-lookup"><span data-stu-id="59af9-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="59af9-109">リンクの Url を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="59af9-110">生成されたリンクは通常、応答で返されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="59af9-111">アクションは、 [慣例的にルーティング](#cr) されるか、または [属性ルーティング](#ar)されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="59af9-112">コントローラーまたは [アクション](#action) にルートを配置すると、ルートが属性ルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="59af9-113">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="59af9-114">このドキュメント:</span><span class="sxs-lookup"><span data-stu-id="59af9-114">This document:</span></span>

* <span data-ttu-id="59af9-115">MVC とルーティングの間の相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="59af9-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="59af9-116">一般的な MVC アプリでルーティング機能を利用する方法。</span><span class="sxs-lookup"><span data-stu-id="59af9-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="59af9-117">次の両方について説明します。</span><span class="sxs-lookup"><span data-stu-id="59af9-117">Covers both:</span></span>
    * <span data-ttu-id="59af9-118">通常は、コントローラーとビューで使用される[従来のルーティング](#cr)です。</span><span class="sxs-lookup"><span data-stu-id="59af9-118">[Conventional routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="59af9-119">REST Api で使用される *属性ルーティング*。</span><span class="sxs-lookup"><span data-stu-id="59af9-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="59af9-120">REST Api のルーティングに主に関心がある場合は、「 [Rest api の属性ルーティング](#ar) 」セクションに進んでください。</span><span class="sxs-lookup"><span data-stu-id="59af9-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="59af9-121">詳細については、「 [ルーティング](xref:fundamentals/routing) の詳細」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="59af9-122">ASP.NET Core 3.0 で追加された既定のルーティングシステムを指します (エンドポイントルーティングと呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="59af9-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="59af9-123">以前のバージョンのルーティングでは、互換性のためにコントローラーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="59af9-124">手順については、 [2.2-3.0 移行ガイド](xref:migration/22-to-30) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="59af9-125">レガシルーティングシステムのリファレンス資料については、 [このドキュメントの2.2 バージョン](xref:mvc/controllers/routing?view=aspnetcore-2.2) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="59af9-126">従来のルートの設定</span><span class="sxs-lookup"><span data-stu-id="59af9-126">Set up conventional route</span></span>

<span data-ttu-id="59af9-127">`Startup.Configure` 通常、 [従来のルーティング](#crd)を使用する場合、には次のようなコードがあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="59af9-128">の呼び出しの内部で <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> は、を使用して <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> 1 つのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="59af9-129">単一のルートの名前は `default` route です。</span><span class="sxs-lookup"><span data-stu-id="59af9-129">The single route is named `default` route.</span></span> <span data-ttu-id="59af9-130">コントローラーとビューを使用するほとんどのアプリでは、ルートと同様のルートテンプレートが使用さ `default` れます。</span><span class="sxs-lookup"><span data-stu-id="59af9-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="59af9-131">REST Api では、 [属性ルーティング](#ar)を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="59af9-132">ルートテンプレート `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="59af9-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="59af9-133">次のような URL パスと一致します `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="59af9-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="59af9-134">パスをトークン化して、ルートの値を抽出し `{ controller = Products, action = Details, id = 5 }` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="59af9-135">アプリケーションにという名前のコントローラーがあり、アクションがある場合、ルート値を抽出した結果が一致し `ProductsController` `Details` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="59af9-136">`/Products/Details/5` モデルは、の値をバインドし `id = 5` て、 `id` パラメーターをに設定 `5` します。</span><span class="sxs-lookup"><span data-stu-id="59af9-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="59af9-137">詳細については、「 [モデルバインド](xref:mvc/models/model-binding) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="59af9-138">`{controller=Home}``Home`既定値としてを定義し `controller` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="59af9-139">`{action=Index}``Index`既定値としてを定義し `action` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="59af9-140">`?`の文字は `{id?}` 、 `id` 省略可能として定義されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="59af9-141">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="59af9-142">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="59af9-143">URL パスと一致し `/` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="59af9-144">ルート値を生成 `{ controller = Home, action = Index }` します。</span><span class="sxs-lookup"><span data-stu-id="59af9-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="59af9-145">との値によって、 `controller` `action` 既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="59af9-146">`id` URL パスに対応するセグメントがないため、は値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="59af9-147">`/` とのアクションが存在する場合にのみ一致 `HomeController` し `Index` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="59af9-148">前のコントローラー定義とルートテンプレートを使用して、 `HomeController.Index` 次の URL パスに対してアクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="59af9-149">URL パスは、 `/` ルートテンプレートの既定の `Home` コントローラーとアクションを使用し `Index` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="59af9-150">URL パスは `/Home` 、ルートテンプレートの既定のアクションを使用し `Index` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="59af9-151">便利なメソッド <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span><span class="sxs-lookup"><span data-stu-id="59af9-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="59af9-152">もの</span><span class="sxs-lookup"><span data-stu-id="59af9-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="59af9-153">ルーティングは、とミドルウェアを使用して構成され <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="59af9-154">コントローラーを使用するには:</span><span class="sxs-lookup"><span data-stu-id="59af9-154">To use controllers:</span></span>
>
> * <span data-ttu-id="59af9-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> `UseEndpoints` [属性ルーティング](#ar)コントローラーをマップするには、内でを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59af9-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="59af9-156"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>または <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> を呼び出して、[従来のルーティング](#cr)コントローラーと[属性ルーティング](#ar)コントローラーの両方をマップします。</span><span class="sxs-lookup"><span data-stu-id="59af9-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="59af9-157">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-157">Conventional routing</span></span>

<span data-ttu-id="59af9-158">従来のルーティングは、コントローラーとビューで使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="59af9-159">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="59af9-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="59af9-160">前のは、 *従来のルート* の例です。</span><span class="sxs-lookup"><span data-stu-id="59af9-160">The preceding is an example of a *conventional route*.</span></span> <span data-ttu-id="59af9-161">これは、URL パスの *規則* を確立するため、*従来のルーティング* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="59af9-162">最初のパスセグメントは、 `{controller=Home}` コントローラー名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="59af9-163">2番目のセグメントは、 `{action=Index}` [アクション](#action) 名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="59af9-164">3番目のセグメント `{id?}` は、オプションとして使用され `id` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="59af9-165">の `?` は `{id?}` 省略可能になります。</span><span class="sxs-lookup"><span data-stu-id="59af9-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="59af9-166">`id` は、モデルエンティティにマップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="59af9-167">このルートを使用して `default` 、次の URL パスを使用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="59af9-168">`/Products/List` アクションに割り当てら `ProductsController.List` れます。</span><span class="sxs-lookup"><span data-stu-id="59af9-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="59af9-169">`/Blog/Article/17` はにマップされ、 `BlogController.Article` 通常、モデルは `id` パラメーターを17にバインドします。</span><span class="sxs-lookup"><span data-stu-id="59af9-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="59af9-170">このマッピングは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="59af9-170">This mapping:</span></span>

* <span data-ttu-id="59af9-171">は、コントローラー名と [アクション](#action) 名 **のみ** に基づいています。</span><span class="sxs-lookup"><span data-stu-id="59af9-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="59af9-172">は、名前空間、ソースファイルの場所、またはメソッドのパラメーターに基づいていません。</span><span class="sxs-lookup"><span data-stu-id="59af9-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="59af9-173">既定のルートで従来のルーティングを使用すると、各アクションの新しい URL パターンを使用しなくてもアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="59af9-174">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)スタイルのアクションを使用するアプリの場合、コントローラー間で url の一貫性を確保します。</span><span class="sxs-lookup"><span data-stu-id="59af9-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="59af9-175">コードを簡略化するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="59af9-175">Helps simplify the code.</span></span>
* <span data-ttu-id="59af9-176">UI の予測可能性を向上させます。</span><span class="sxs-lookup"><span data-stu-id="59af9-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="59af9-177">前のコードのは、 `id` ルートテンプレートによってオプションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="59af9-178">アクションは、URL の一部として指定された省略可能な ID なしで実行できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="59af9-179">通常、URL からを省略すると、 `id` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="59af9-180">`id` は `0` 、モデルバインドによってに設定されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="59af9-181">データベース照合でエンティティが見つかりません `id == 0` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="59af9-182">[属性ルーティング](#ar) を使用すると、他のアクションではなく、一部のアクションに必要な ID を作成できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="59af9-183">慣例により、ドキュメントには、 `id` 正しい使用状況で表示される可能性があるなどの省略可能なパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="59af9-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="59af9-184">ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="59af9-185">既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="59af9-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="59af9-186">基本的でわかりやすいルーティング スキームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="59af9-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="59af9-187">UI ベースのアプリの便利な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="59af9-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="59af9-188">は、多くの web UI アプリに必要な唯一のルートテンプレートです。</span><span class="sxs-lookup"><span data-stu-id="59af9-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="59af9-189">大規模な web UI アプリの場合は、 [領域](#areas) を使用するもう1つのルートが必要になることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-189">For larger web UI apps, another route using [Areas](#areas) is frequently all that's needed.</span></span>

<span data-ttu-id="59af9-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> および <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="59af9-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="59af9-191">呼び出された順序に基づいて、エンドポイントに **注文** 値を自動的に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="59af9-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="59af9-192">ASP.NET Core 3.0 以降でのエンドポイントのルーティング:</span><span class="sxs-lookup"><span data-stu-id="59af9-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="59af9-193">ルートの概念がありません。</span><span class="sxs-lookup"><span data-stu-id="59af9-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="59af9-194">は、拡張性の実行に対する順序の保証を提供しません。すべてのエンドポイントが一度に処理されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="59af9-195">[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="59af9-196">[属性ルーティング](#ar) については、このドキュメントで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="59af9-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="59af9-197">複数の従来のルート</span><span class="sxs-lookup"><span data-stu-id="59af9-197">Multiple conventional routes</span></span>

<span data-ttu-id="59af9-198">との呼び出しをさらに追加することで、複数の従来の [ルート](#cr) を内部に追加でき `UseEndpoints` <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="59af9-199">これにより、複数の規則を定義したり、次のような特定の [アクション](#action)専用の通常のルートを追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="59af9-200">前のコードのルートは、 `blog` 専用の従来の **ルート** です。</span><span class="sxs-lookup"><span data-stu-id="59af9-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="59af9-201">これは、次の理由で専用の従来のルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="59af9-202">従来の [ルーティング](#cr)を使用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="59af9-203">これは特定の [アクション](#action)専用です。</span><span class="sxs-lookup"><span data-stu-id="59af9-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="59af9-204">`controller`とは `action` ルートテンプレートにパラメーターとして表示されないため、 `"blog/{*article}"` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="59af9-205">既定値のみを持つことができ `{ controller = "Blog", action = "Article" }` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="59af9-206">このルートは常にアクションにマップさ `BlogController.Article` れます。</span><span class="sxs-lookup"><span data-stu-id="59af9-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="59af9-207">`/Blog`、 `/Blog/Article` 、および `/Blog/{any-string}` は、ブログルートに一致する唯一の URL パスです。</span><span class="sxs-lookup"><span data-stu-id="59af9-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="59af9-208">上記の例の場合:</span><span class="sxs-lookup"><span data-stu-id="59af9-208">The preceding example:</span></span>

* <span data-ttu-id="59af9-209">`blog` ルートは最初に追加されるため、ルートより優先順位が高くなり `default` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="59af9-210">は、URL の一部としてアーティクル名を持つことが一般的である、 [スラグ](https://developer.mozilla.org/docs/Glossary/Slug) スタイルのルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="59af9-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="59af9-211">ASP.NET Core 3.0 以降では、ルーティングは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="59af9-212">*ルート* と呼ばれる概念を定義します。</span><span class="sxs-lookup"><span data-stu-id="59af9-212">Define a concept called a *route*.</span></span> <span data-ttu-id="59af9-213">`UseRouting` では、ルートの照合がミドルウェア パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="59af9-214">ミドルウェアは、 `UseRouting` アプリで定義されているエンドポイントのセットを調べ、要求に基づいて最適なエンドポイント一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="59af9-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="59af9-215">やなどの拡張機能の実行順序についての保証を提供 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> します。</span><span class="sxs-lookup"><span data-stu-id="59af9-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="59af9-216">ルーティングのリファレンス資料については、「 [ルーティング](xref:fundamentals/routing) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="59af9-217">従来のルーティング順序</span><span class="sxs-lookup"><span data-stu-id="59af9-217">Conventional routing order</span></span>

<span data-ttu-id="59af9-218">従来のルーティングは、アプリによって定義されたアクションとコントローラーの組み合わせにのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="59af9-219">これは、通常のルートが重複するケースを簡略化することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="59af9-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="59af9-220">、、およびを使用してルートを追加する <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> と、 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 呼び出された順序に基づいて、そのエンドポイントに自動的に注文値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="59af9-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="59af9-221">以前に表示されたルートからの一致は、優先順位が高くなります。</span><span class="sxs-lookup"><span data-stu-id="59af9-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="59af9-222">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="59af9-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="59af9-223">一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="59af9-224">などのすべてのルートパラメーターを持つ[専用の従来のルート](#dcr)では、ルートの `{*article}` [最長](xref:fundamentals/routing#greedy)一致が実現されます。これは、他のルートと一致するように意図した url と一致することを意味します。</span><span class="sxs-lookup"><span data-stu-id="59af9-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="59af9-225">最短一致の一致を防ぐために、ルートテーブルの中で最長一致のルートを指定します。</span><span class="sxs-lookup"><span data-stu-id="59af9-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="59af9-226">あいまいなアクションの解決</span><span class="sxs-lookup"><span data-stu-id="59af9-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="59af9-227">ルーティングを通じて2つのエンドポイントが一致する場合、ルーティングでは次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="59af9-228">最適な候補を選択します。</span><span class="sxs-lookup"><span data-stu-id="59af9-228">Choose the best candidate.</span></span>
* <span data-ttu-id="59af9-229">例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="59af9-229">Throw an exception.</span></span>

<span data-ttu-id="59af9-230">例:</span><span class="sxs-lookup"><span data-stu-id="59af9-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="59af9-231">前のコントローラーは、次の2つのアクションに一致するアクションを定義します。</span><span class="sxs-lookup"><span data-stu-id="59af9-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="59af9-232">URL パス `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="59af9-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="59af9-233">データ `{ controller = Products33, action = Edit, id = 17 }` をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="59af9-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="59af9-234">これは、MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="59af9-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="59af9-235">`Edit(int)` 製品を編集するためのフォームを表示します。</span><span class="sxs-lookup"><span data-stu-id="59af9-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="59af9-236">`Edit(int, Product)` ポストされたフォームを処理します。</span><span class="sxs-lookup"><span data-stu-id="59af9-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="59af9-237">正しいルートを解決するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="59af9-237">To resolve the correct route:</span></span>

* <span data-ttu-id="59af9-238">`Edit(int, Product)` は、要求が HTTP の場合に選択され `POST` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="59af9-239">`Edit(int)`[HTTP 動詞](#verb)が他のものである場合は、が選択されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="59af9-240">`Edit(int)` は、一般に経由で呼び出され `GET` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="59af9-241">、 <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> 、 `[HttpPost]` は、要求の HTTP メソッドに基づいて選択できるように、ルーティングのために用意されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="59af9-242">は `HttpPostAttribute` `Edit(int, Product)` よりもより適切に一致し `Edit(int)` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="59af9-243">のような属性の役割を理解しておくことが重要です `HttpPostAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="59af9-244">同様の属性は、他の [HTTP 動詞](#verb)に対して定義されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="59af9-245">[従来のルーティング](#cr)では、表示フォーム、送信フォームワークフローの一部である場合、アクションは同じアクション名を使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="59af9-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="59af9-246">例については、「 [2 つの編集アクションメソッドの確認](xref:tutorials/first-mvc-app/controller-methods-views#get-post)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="59af9-247">ルーティングで最適な候補を選択できない場合は、 <xref:System.Reflection.AmbiguousMatchException> 一致する複数のエンドポイントを一覧表示するがスローされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="59af9-248">従来のルート名</span><span class="sxs-lookup"><span data-stu-id="59af9-248">Conventional route names</span></span>

<span data-ttu-id="59af9-249">次の例の文字列とは、  `"blog"` `"default"` 通常のルート名です。</span><span class="sxs-lookup"><span data-stu-id="59af9-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="59af9-250">ルート名によって、ルートに論理名が指定されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-250">The route names give the route a logical name.</span></span> <span data-ttu-id="59af9-251">名前付きルートは、URL の生成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="59af9-252">ルートの順序によって URL の生成が複雑になる可能性がある場合、名前付きルートを使用すると、URL の作成が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="59af9-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="59af9-253">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="59af9-254">ルート名:</span><span class="sxs-lookup"><span data-stu-id="59af9-254">Route names:</span></span>

* <span data-ttu-id="59af9-255">URL の照合や要求の処理には影響しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="59af9-256">は、URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-256">Are used only for URL generation.</span></span>

<span data-ttu-id="59af9-257">ルート名の概念は、ルーティングで [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)として表されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="59af9-258">**ルート名** と **エンドポイント名** の用語:</span><span class="sxs-lookup"><span data-stu-id="59af9-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="59af9-259">交換可能です。</span><span class="sxs-lookup"><span data-stu-id="59af9-259">Are interchangeable.</span></span>
* <span data-ttu-id="59af9-260">ドキュメントとコードで使用されるものは、記述されている API によって異なります。</span><span class="sxs-lookup"><span data-stu-id="59af9-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="59af9-261">REST Api の属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="59af9-262">REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が [HTTP 動詞](#verb)によって表されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="59af9-263">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="59af9-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="59af9-264">次の `StartUp.Configure` コードは、REST API の一般的なコードであり、次のサンプルで使用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="59af9-265">前のコードで <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> は、 `UseEndpoints` 属性ルーティングコントローラーをマップするために、内でが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="59af9-266">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="59af9-266">In the following example:</span></span>

* <span data-ttu-id="59af9-267">上記の `Configure` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="59af9-268">`HomeController` 既定の従来のルートと同様の Url のセットと一致 `{controller=Home}/{action=Index}/{id?}` します。</span><span class="sxs-lookup"><span data-stu-id="59af9-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="59af9-269">`HomeController.Index`アクションは、URL パス、、 `/` 、またはのいずれかに対して実行され `/Home` `/Home/Index` `/Home/Index/3` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="59af9-270">この例では、属性ルーティングと [従来のルーティング](#cr)の主なプログラミングの違いについて取り上げます。</span><span class="sxs-lookup"><span data-stu-id="59af9-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="59af9-271">属性ルーティングでは、ルートを指定するためにより多くの入力が必要です。</span><span class="sxs-lookup"><span data-stu-id="59af9-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="59af9-272">従来の既定のルートは、ルートをより簡潔に処理します。</span><span class="sxs-lookup"><span data-stu-id="59af9-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="59af9-273">ただし、属性ルーティングでは、各 [アクション](#action)に適用されるルートテンプレートを正確に制御する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="59af9-274">属性のルーティングでは、 [トークンの置換](#routing-token-replacement-templates-ref-label) が使用されていない限り、アクションが一致する部分はコントローラーとアクションの名前によって決まります。</span><span class="sxs-lookup"><span data-stu-id="59af9-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="59af9-275">次の例は、前の例と同じ Url に一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="59af9-276">次のコードでは、とのトークン置換を使用してい `action` `controller` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="59af9-277">コントローラーには、次のコードが適用され `[Route("[controller]/[action]")]` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="59af9-278">前のコードでは、 `Index` メソッドテンプレートをルートテンプレートに付加する必要があり `/` `~/` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="59af9-279">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="59af9-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="59af9-280">ルートテンプレートの選択の詳細については、 [ルートテンプレートの優先順位](xref:fundamentals/routing#rtp) に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="59af9-281">ルーティングの予約名</span><span class="sxs-lookup"><span data-stu-id="59af9-281">Reserved routing names</span></span>

<span data-ttu-id="59af9-282">次のキーワードは、コントローラーまたはページを使用する場合の予約ルートパラメーター名です Razor 。</span><span class="sxs-lookup"><span data-stu-id="59af9-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="59af9-283">`page`属性ルーティングでルートパラメーターとしてを使用することは、一般的なエラーです。</span><span class="sxs-lookup"><span data-stu-id="59af9-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="59af9-284">その結果、URL の生成によって一貫性のない動作がわかりにくくなります。</span><span class="sxs-lookup"><span data-stu-id="59af9-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="59af9-285">特別なパラメーター名は、url 生成操作がページまたはコントローラーのどちらを参照するかを判断するために、URL 生成によって使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="59af9-286">HTTP 動詞テンプレート</span><span class="sxs-lookup"><span data-stu-id="59af9-286">HTTP verb templates</span></span>

<span data-ttu-id="59af9-287">ASP.NET Core には、次の HTTP 動詞テンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="59af9-288">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="59af9-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="59af9-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="59af9-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="59af9-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="59af9-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="59af9-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="59af9-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="59af9-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="59af9-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="59af9-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="59af9-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="59af9-294">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="59af9-294">Route templates</span></span>

<span data-ttu-id="59af9-295">ASP.NET Core には、次のルートテンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="59af9-296">すべての [HTTP 動詞テンプレート](#verb) はルートテンプレートです。</span><span class="sxs-lookup"><span data-stu-id="59af9-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="59af9-297">[回送](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="59af9-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="59af9-298">Http 動詞属性を使用した属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="59af9-299">次のコントローラーを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="59af9-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="59af9-300">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-300">In the preceding code:</span></span>

* <span data-ttu-id="59af9-301">各アクションには属性が含まれてい `[HttpGet]` ます。これにより、一致する HTTP GET 要求のみが制限されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="59af9-302">アクションには `GetProduct` テンプレートが含まれ `"{id}"` ているため、 `id` コントローラーのテンプレートに追加され `"api/[controller]"` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="59af9-303">メソッドテンプレートは `"api/[controller]/"{id}""` です。</span><span class="sxs-lookup"><span data-stu-id="59af9-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="59af9-304">したがって、このアクションは、フォーム、、などの GET 要求のみと一致 `/api/test2/xyz` `/api/test2/123` `/api/test2/{any string}` します。</span><span class="sxs-lookup"><span data-stu-id="59af9-304">Therefore this action only matches GET requests for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="59af9-305">`GetIntProduct`アクションには、テンプレートが含まれてい `"int/{id:int}")` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="59af9-306">`:int`テンプレートの部分では、 `id` 整数に変換できる文字列にルート値を制限します。</span><span class="sxs-lookup"><span data-stu-id="59af9-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="59af9-307">GET 要求 `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="59af9-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="59af9-308">このアクションと一致しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="59af9-309">[404 が見つからないこと](https://developer.mozilla.org/docs/Web/HTTP/Status/404)を示すエラーを返します。</span><span class="sxs-lookup"><span data-stu-id="59af9-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="59af9-310">`GetInt2Product`アクションはテンプレートに含まれてい `{id}` ますが、 `id` 整数に変換できる値に制限されていません。</span><span class="sxs-lookup"><span data-stu-id="59af9-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="59af9-311">GET 要求 `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="59af9-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="59af9-312">このルートと一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-312">Matches this route.</span></span>
  * <span data-ttu-id="59af9-313">モデルバインドは整数への変換に失敗し `abc` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="59af9-314">`id`メソッドのパラメーターは integer です。</span><span class="sxs-lookup"><span data-stu-id="59af9-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="59af9-315">モデルバインドが整数に変換できなかったため、 [400 の無効な要求](https://developer.mozilla.org/docs/Web/HTTP/Status/400) が返さ `abc` れます。</span><span class="sxs-lookup"><span data-stu-id="59af9-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="59af9-316">属性ルーティングでは <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> 、、、などの属性を使用でき <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="59af9-317">すべての [HTTP 動詞](#verb) 属性は、ルートテンプレートを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="59af9-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="59af9-318">次の例は、同じルートテンプレートに一致する2つのアクションを示しています。</span><span class="sxs-lookup"><span data-stu-id="59af9-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="59af9-319">URL パスを使用する場合 `/products3` :</span><span class="sxs-lookup"><span data-stu-id="59af9-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="59af9-320">アクションは、 `MyProductsController.ListProducts` [HTTP 動詞](#verb) がの場合に実行され `GET` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="59af9-321">アクションは、 `MyProductsController.CreateProduct` [HTTP 動詞](#verb) がの場合に実行され `POST` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="59af9-322">REST API をビルドする場合、アクション `[Route(...)]` はすべての HTTP メソッドを受け入れるため、アクションメソッドでを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="59af9-323">API がサポートする内容については、より具体的な [HTTP 動詞属性](#verb) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="59af9-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="59af9-324">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="59af9-325">REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が HTTP 動詞によって表されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="59af9-326">これは、同じ論理リソースに対する GET や POST などの多くの操作で同じ URL が使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="59af9-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="59af9-327">属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="59af9-328">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="59af9-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="59af9-329">次の例で `id` は、URL パスの一部としてが必要です。</span><span class="sxs-lookup"><span data-stu-id="59af9-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="59af9-330">`Products2ApiController.GetProduct(int)`アクション:</span><span class="sxs-lookup"><span data-stu-id="59af9-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="59af9-331">次のように URL パスを使用して実行する `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="59af9-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="59af9-332">URL パスでは実行さ `/products2` れません。</span><span class="sxs-lookup"><span data-stu-id="59af9-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="59af9-333">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性を使用すると、アクションでサポートされる要求のコンテンツの種類を制限できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="59af9-334">詳細については、「 [利用属性を使用したサポートされる要求のコンテンツの種類の定義](xref:web-api/index#consumes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="59af9-335">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="59af9-336">の詳細につい `[ApiController]` ては、「 [ApiController 属性](xref:web-api/index##apicontroller-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="59af9-337">ルート名</span><span class="sxs-lookup"><span data-stu-id="59af9-337">Route name</span></span>

<span data-ttu-id="59af9-338">次のコードでは、 の "`Products_List`ルート名" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="59af9-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="59af9-339">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="59af9-340">ルート名:</span><span class="sxs-lookup"><span data-stu-id="59af9-340">Route names:</span></span>

* <span data-ttu-id="59af9-341">ルーティングの URL 照合動作には影響しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="59af9-342">は、URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-342">Are only used for URL generation.</span></span>

<span data-ttu-id="59af9-343">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="59af9-344">前のコードと従来の既定のルートを比較します。これにより、 `id` パラメーターが省略可能な () として定義され `{id?}` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="59af9-345">Api を正確に指定する機能には、 `/products` と `/products/5` を別のアクションにディスパッチできるなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="59af9-346">属性ルートの結合</span><span class="sxs-lookup"><span data-stu-id="59af9-346">Combining attribute routes</span></span>

<span data-ttu-id="59af9-347">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="59af9-348">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="59af9-349">ルート属性をコントローラーに配置すると、コントローラーの **すべて** のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="59af9-350">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="59af9-350">In the preceding example:</span></span>

* <span data-ttu-id="59af9-351">URL パスを `/products` 一致させることができます `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="59af9-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="59af9-352">URL パスを `/products/5` 一致させることができ `ProductsApi.GetProduct(int)` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="59af9-353">これらのアクションはどちらも `GET` 、属性でマークされているので、HTTP とのみ一致し `[HttpGet]` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="59af9-354">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="59af9-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="59af9-355">次の例は、既定のルートに似た URL パスのセットに一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="59af9-356">次の表では、上記のコードの属性について説明し `[Route]` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="59af9-357">属性</span><span class="sxs-lookup"><span data-stu-id="59af9-357">Attribute</span></span>               | <span data-ttu-id="59af9-358">との組み合わせ `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="59af9-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="59af9-359">ルートテンプレートを定義します</span><span class="sxs-lookup"><span data-stu-id="59af9-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="59af9-360">はい</span><span class="sxs-lookup"><span data-stu-id="59af9-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="59af9-361">はい</span><span class="sxs-lookup"><span data-stu-id="59af9-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="59af9-362">**いいえ**</span><span class="sxs-lookup"><span data-stu-id="59af9-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="59af9-363">はい</span><span class="sxs-lookup"><span data-stu-id="59af9-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="59af9-364">属性のルートの順序</span><span class="sxs-lookup"><span data-stu-id="59af9-364">Attribute route order</span></span>

<span data-ttu-id="59af9-365">ルーティングによってツリーが構築され、すべてのエンドポイントが同時に一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="59af9-366">ルートエントリは、最適な順序で配置されているかのように動作します。</span><span class="sxs-lookup"><span data-stu-id="59af9-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="59af9-367">最も具体的なルートは、より一般的なルートの前に実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="59af9-368">たとえば、のような属性ルート `blog/search/{topic}` は、のような属性ルートよりも具体的です `blog/{*article}` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="59af9-369">既定では、 `blog/search/{topic}` ルートの優先度は高くなります。</span><span class="sxs-lookup"><span data-stu-id="59af9-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="59af9-370">開発者は、 [従来のルーティング](#cr)を使用して、ルートを目的の順序で配置します。</span><span class="sxs-lookup"><span data-stu-id="59af9-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="59af9-371">属性ルートでは、プロパティを使用して注文を構成でき <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="59af9-372">フレームワークに用意されているすべての [ルート属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) には、が含まれ `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="59af9-373">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="59af9-374">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="59af9-374">The default order is `0`.</span></span> <span data-ttu-id="59af9-375">を使用してルートを設定すると `Order = -1` 、順序が設定されていないルートよりも前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="59af9-376">を使用したルートの設定は、 `Order = 1` 既定のルートの順序の後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="59af9-377">によっては **避け** て `Order` ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="59af9-378">アプリの URL 空間で、明示的な順序の値を正しくルーティングする必要がある場合は、クライアントにも混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="59af9-379">一般に、属性ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="59af9-380">URL の生成に使用される既定の順序が機能していない場合は、通常、プロパティを適用するよりも、ルート名をオーバーライドとして使用する方が簡単です `Order` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="59af9-381">次の2つのコントローラーで、両方がルート一致を定義しているとし `/home` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="59af9-382">`/home`前のコードでを要求すると、次のような例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="59af9-383">`Order`ルート属性のいずれかにを追加すると、あいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="59af9-384">上記のコードでは、によって `/home` エンドポイントが実行され `HomeController.Index` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="59af9-385">にアクセスするには `MyDemoController.MyIndex` 、を要求 `/home/MyIndex` します。</span><span class="sxs-lookup"><span data-stu-id="59af9-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="59af9-386">**注**:</span><span class="sxs-lookup"><span data-stu-id="59af9-386">**Note**:</span></span>

* <span data-ttu-id="59af9-387">上記のコードは、ルーティング設計の一例または不十分です。</span><span class="sxs-lookup"><span data-stu-id="59af9-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="59af9-388">これは、プロパティを示すために使用されていま `Order` した。</span><span class="sxs-lookup"><span data-stu-id="59af9-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="59af9-389">`Order`プロパティはあいまいさを解決するだけで、そのテンプレートは一致しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="59af9-390">テンプレートを削除することをお勧めし `[Route("Home")]` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="59af9-391">ページとルートの順序については[ Razor 、「ルートとアプリの規則](xref:razor-pages/razor-pages-conventions#route-order)」を参照してください Razor 。</span><span class="sxs-lookup"><span data-stu-id="59af9-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="59af9-392">場合によっては、あいまいなルートで HTTP 500 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="59af9-393">[ログ](xref:fundamentals/logging/index)を使用して、の原因となったエンドポイントを確認し `AmbiguousMatchException` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="59af9-394">ルートテンプレートでのトークンの置換 [controller]、[action]、[area]</span><span class="sxs-lookup"><span data-stu-id="59af9-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="59af9-395">便宜上、属性ルートは、トークンを角かっこ (,) で囲むことによって *トークンの置換* をサポートして `[` `]` います。</span><span class="sxs-lookup"><span data-stu-id="59af9-395">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="59af9-396">トークン `[action]` 、 `[area]` 、および `[controller]` は、ルートが定義されているアクションのアクション名、領域名、およびコントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="59af9-396">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="59af9-397">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-397">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="59af9-398">一致 `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="59af9-398">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="59af9-399">一致 `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="59af9-399">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="59af9-400">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="59af9-400">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="59af9-401">前の例では、次のコードと同じ動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-401">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="59af9-402">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="59af9-402">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="59af9-403">これは、トークンの置換と組み合わせた強力な機能です。</span><span class="sxs-lookup"><span data-stu-id="59af9-403">This is powerful combined with token replacement.</span></span> <span data-ttu-id="59af9-404">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-404">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="59af9-405">`[Route("[controller]/[action]", Name="[controller]_[action]")]`では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-405">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="59af9-406">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-406">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="59af9-407"> では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-407">generates a unique route name for each action.</span></span>

<span data-ttu-id="59af9-408">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="59af9-408">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="59af9-409">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="59af9-409">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="59af9-410">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="59af9-410">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="59af9-411">パラメーター トランスフォーマーは <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="59af9-411">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="59af9-412">たとえば、カスタム `SlugifyParameterTransformer` パラメータートランスフォーマーは、 `SubscriptionManagement` ルート値を次のように変更し `subscription-management` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-412">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="59af9-413"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="59af9-413">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="59af9-414">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-414">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="59af9-415">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="59af9-415">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="59af9-416">前の `ListAll` メソッドはと一致 `/subscription-management/list-all` します。</span><span class="sxs-lookup"><span data-stu-id="59af9-416">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="59af9-417">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-417">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="59af9-418">スラグの定義については、 [スラグの MDN web ドキュメント](https://developer.mozilla.org/docs/Glossary/Slug) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-418">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="59af9-419">複数の属性ルート</span><span class="sxs-lookup"><span data-stu-id="59af9-419">Multiple attribute routes</span></span>

<span data-ttu-id="59af9-420">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59af9-420">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="59af9-421">これの最も一般的な使用方法は、次の例で示すように、"既定の規則ルート" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="59af9-421">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="59af9-422">コントローラーに複数のルート属性を配置することは、それぞれがアクションメソッドの各ルート属性と結合することを意味します。</span><span class="sxs-lookup"><span data-stu-id="59af9-422">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="59af9-423">すべての [HTTP 動詞](#verb) ルート制約は、を実装 `IActionConstraint` します。</span><span class="sxs-lookup"><span data-stu-id="59af9-423">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="59af9-424">を実装する複数のルート属性 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> が1つのアクションに配置されている場合:</span><span class="sxs-lookup"><span data-stu-id="59af9-424">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="59af9-425">各アクション制約は、コントローラーに適用されたルートテンプレートと結合されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-425">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="59af9-426">アクションで複数のルートを使用すると便利で強力な場合があります。アプリの URL 空間の基本を明確に定義しておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="59af9-426">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="59af9-427">既存のクライアントをサポートするために必要な場合に **のみ** 、アクションで複数のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-427">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="59af9-428">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="59af9-428">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="59af9-429">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59af9-429">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="59af9-430">前のコードでは、は `[HttpPost("product14/{id:int}")]` ルート制約を適用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-430">In the preceding code, `[HttpPost("product14/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="59af9-431">`Products14Controller.ShowProduct`アクションは、のような URL パスによってのみ照合され `/product14/3` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-431">The `Products14Controller.ShowProduct` action is matched only by URL paths like `/product14/3`.</span></span> <span data-ttu-id="59af9-432">ルートテンプレートの部分は、 `{id:int}` そのセグメントを整数のみに制限します。</span><span class="sxs-lookup"><span data-stu-id="59af9-432">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="59af9-433">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-433">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="59af9-434">IRouteTemplateProvider を使用したカスタムルート属性</span><span class="sxs-lookup"><span data-stu-id="59af9-434">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="59af9-435">すべての [ルート属性](#rt) はを実装 <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> します。</span><span class="sxs-lookup"><span data-stu-id="59af9-435">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="59af9-436">ASP.NET Core ランタイム:</span><span class="sxs-lookup"><span data-stu-id="59af9-436">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="59af9-437">アプリの起動時に、コントローラークラスとアクションメソッドの属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="59af9-437">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="59af9-438">は、を実装する属性を使用して `IRouteTemplateProvider` 、ルートの初期セットを構築します。</span><span class="sxs-lookup"><span data-stu-id="59af9-438">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="59af9-439">を実装して `IRouteTemplateProvider` 、カスタムルート属性を定義します。</span><span class="sxs-lookup"><span data-stu-id="59af9-439">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="59af9-440">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-440">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="59af9-441">前の `Get` メソッドはを返し `Order = 2, Template = api/MyTestApi` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-441">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="59af9-442">アプリケーションモデルを使用して属性ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="59af9-442">Use application model to customize attribute routes</span></span>

<span data-ttu-id="59af9-443">アプリケーションモデル:</span><span class="sxs-lookup"><span data-stu-id="59af9-443">The application model:</span></span>

* <span data-ttu-id="59af9-444">は、起動時に作成されるオブジェクトモデルです。</span><span class="sxs-lookup"><span data-stu-id="59af9-444">Is an object model created at startup.</span></span>
* <span data-ttu-id="59af9-445">アプリでアクションをルーティングおよび実行するために ASP.NET Core によって使用されるすべてのメタデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-445">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="59af9-446">アプリケーションモデルには、ルート属性から収集されたすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-446">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="59af9-447">ルート属性からのデータは、実装によって提供され `IRouteTemplateProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-447">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="59af9-448">規定</span><span class="sxs-lookup"><span data-stu-id="59af9-448">Conventions:</span></span>

* <span data-ttu-id="59af9-449">アプリケーションモデルを変更して、ルーティングの動作をカスタマイズするために、を記述できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-449">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="59af9-450">アプリの起動時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-450">Are read at app startup.</span></span>

<span data-ttu-id="59af9-451">このセクションでは、アプリケーションモデルを使用してルーティングをカスタマイズする基本的な例を示します。</span><span class="sxs-lookup"><span data-stu-id="59af9-451">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="59af9-452">次のコードでは、ルートがプロジェクトのフォルダー構造とほぼ同じになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-452">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="59af9-453">次のコードは、 `namespace` 属性がルーティングされているコントローラーに規則が適用されないようにします。</span><span class="sxs-lookup"><span data-stu-id="59af9-453">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="59af9-454">たとえば、次のコントローラーではを使用しません `NamespaceRoutingConvention` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-454">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="59af9-455">`NamespaceRoutingConvention.Apply` メソッド:</span><span class="sxs-lookup"><span data-stu-id="59af9-455">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="59af9-456">コントローラーが属性ルーティングされている場合は、何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-456">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="59af9-457">に基づいてコントローラーテンプレートを設定し `namespace` `namespace` ます。ベースは削除されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-457">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="59af9-458">は、 `NamespaceRoutingConvention` 次の方法で適用でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-458">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="59af9-459">たとえば、次のコントローラーを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="59af9-459">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="59af9-460">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-460">In the preceding code:</span></span>

* <span data-ttu-id="59af9-461">ベース `namespace` が `My.Application` です。</span><span class="sxs-lookup"><span data-stu-id="59af9-461">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="59af9-462">前のコントローラーの完全な名前は `My.Application.Admin.Controllers.UsersController` です。</span><span class="sxs-lookup"><span data-stu-id="59af9-462">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="59af9-463">によって、 `NamespaceRoutingConvention` コントローラーテンプレートがに設定され `Admin/Controllers/Users/[action]/{id?` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-463">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="59af9-464">は、 `NamespaceRoutingConvention` コントローラーの属性として適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="59af9-464">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="59af9-465">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-465">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="59af9-466">ASP.NET Core アプリでは、従来のルーティングと属性ルーティングの使用を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-466">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="59af9-467">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-467">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="59af9-468">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-468">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="59af9-469">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-469">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="59af9-470">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="59af9-470">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="59af9-471">コントローラー **のルート属性では、コントローラー** 属性の **すべて** のアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-471">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="59af9-472">属性ルーティングと従来のルーティングでは、同じルーティングエンジンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-472">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="59af9-473">URL 生成とアンビエント値</span><span class="sxs-lookup"><span data-stu-id="59af9-473">URL Generation and ambient values</span></span>

<span data-ttu-id="59af9-474">アプリでは、ルーティング URL 生成機能を使用して、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-474">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="59af9-475">Url を生成することで、Url をハードコーディングすることがなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="59af9-475">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="59af9-476">このセクションでは、MVC によって提供される URL 生成機能について説明し、URL の生成のしくみの基本についてのみ説明します。</span><span class="sxs-lookup"><span data-stu-id="59af9-476">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="59af9-477">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-477">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="59af9-478"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>インターフェイスは、MVC と URL 生成のルーティングの間のインフラストラクチャの基になる要素です。</span><span class="sxs-lookup"><span data-stu-id="59af9-478">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="59af9-479">のインスタンス `IUrlHelper` は、 `Url` コントローラー、ビュー、およびビューコンポーネントのプロパティを介して使用できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-479">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="59af9-480">次の例では、 `IUrlHelper` プロパティを通じてインターフェイスを使用して、 `Controller.Url` 別のアクションへの URL を生成しています。</span><span class="sxs-lookup"><span data-stu-id="59af9-480">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="59af9-481">アプリで既定のルートを使用している場合、変数の値 `url` は URL パス文字列に `/UrlGeneration/Destination` なります。</span><span class="sxs-lookup"><span data-stu-id="59af9-481">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="59af9-482">この URL パスは、次の組み合わせによってルーティングによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-482">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="59af9-483">現在の要求からのルート値 ( **アンビエント値** と呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="59af9-483">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="59af9-484">に渡され、 `Url.Action` それらの値をルートテンプレートに置き換える値。</span><span class="sxs-lookup"><span data-stu-id="59af9-484">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="59af9-485">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-485">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="59af9-486">値を持たないルートパラメーターには、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-486">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="59af9-487">既定値がある場合は、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-487">Use a default value if it has one.</span></span>
* <span data-ttu-id="59af9-488">省略可能な場合はスキップします。</span><span class="sxs-lookup"><span data-stu-id="59af9-488">Be skipped if it's optional.</span></span> <span data-ttu-id="59af9-489">たとえば、ルートテンプレートのを使用し `id` `{controller}/{action}/{id?}` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-489">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="59af9-490">必要なルートパラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="59af9-490">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="59af9-491">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-491">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="59af9-492">前の例では、従来のルーティングが想定されてい `Url.Action` ます。 [](#cr)</span><span class="sxs-lookup"><span data-stu-id="59af9-492">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="59af9-493">URL の生成は、 [属性ルーティング](#ar)と同様に機能しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="59af9-493">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="59af9-494">従来のルーティングの場合:</span><span class="sxs-lookup"><span data-stu-id="59af9-494">With conventional routing:</span></span>

* <span data-ttu-id="59af9-495">ルート値は、テンプレートを展開するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-495">The route values are used to expand a template.</span></span>
* <span data-ttu-id="59af9-496">とのルート値は、 `controller` `action` 通常、そのテンプレートに表示されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-496">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="59af9-497">これは、ルーティングによって一致した Url が規則に準拠しているために機能します。</span><span class="sxs-lookup"><span data-stu-id="59af9-497">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="59af9-498">次の例では、属性ルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-498">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="59af9-499">前のコードのアクションによってが `Source` 生成さ `custom/url/to/destination` れます。</span><span class="sxs-lookup"><span data-stu-id="59af9-499">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="59af9-500"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> は、の代わりに ASP.NET Core 3.0 で追加されました `IUrlHelper` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-500"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="59af9-501">`LinkGenerator` は、同様に柔軟な機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="59af9-501">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="59af9-502">の各メソッドに `IUrlHelper` は、に対応するメソッドのファミリ `LinkGenerator` もあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-502">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="59af9-503">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="59af9-503">Generating URLs by action name</span></span>

<span data-ttu-id="59af9-504">[Url. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)、 [Linkgenerator、GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)、およびすべての関連するすべてのオーバーロードは、コントローラー名とアクション名を指定してターゲットエンドポイントを生成するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-504">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="59af9-505">を使用する場合 `Url.Action` 、との現在のルート値 `controller` は、 `action` ランタイムによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-505">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="59af9-506">との値 `controller` `action` は、 [アンビエント値](#ambient) と値の両方に含まれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-506">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="59af9-507">メソッドは、 `Url.Action` 常にとの現在の値を使用 `action` し、現在の `controller` アクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-507">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="59af9-508">ルーティングでは、アンビエント値の値を使用して、URL の生成時に提供されなかった情報を入力しようとします。</span><span class="sxs-lookup"><span data-stu-id="59af9-508">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="59af9-509">アンビエント値のようなルートを考えてみましょう `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-509">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="59af9-510">ルーティングには、URL を生成するための十分な情報が含まれており、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="59af9-510">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="59af9-511">すべてのルートパラメーターに値があるため、ルーティングに十分な情報が得られます。</span><span class="sxs-lookup"><span data-stu-id="59af9-511">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="59af9-512">値が追加された場合は、 `{ d = Donovan }` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-512">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="59af9-513">この値 `{ d = David }` は無視されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-513">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="59af9-514">生成された URL パスが `Alice/Bob/Carol/Donovan` です。</span><span class="sxs-lookup"><span data-stu-id="59af9-514">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="59af9-515">**警告**: URL パスは階層化されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-515">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="59af9-516">前の例で、値が追加されている場合は、 `{ c = Cheryl }` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-516">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="59af9-517">両方の値 `{ c = Carol, d = David }` が無視されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-517">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="59af9-518">の値はなく `d` 、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="59af9-518">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="59af9-519">URL を生成するには、との必要な値を `c` `d` 指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-519">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="59af9-520">既定のルートでは、この問題が発生する可能性があり `{controller}/{action}/{id?}` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-520">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="59af9-521">この問題は、常にとの値が明示的に指定されているため、実際にはめったにあり `Url.Action` `controller` `action` ません。</span><span class="sxs-lookup"><span data-stu-id="59af9-521">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="59af9-522">[Url](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)のいくつかのオーバーロードでは、ルート値オブジェクトを使用し `controller` て、および以外のルートパラメーターの値を指定します。 `action`</span><span class="sxs-lookup"><span data-stu-id="59af9-522">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="59af9-523">ルート値オブジェクトは、と共によく使用され `id` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-523">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="59af9-524">たとえば、「 `Url.Action("Buy", "Products", new { id = 17 })` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="59af9-524">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="59af9-525">ルート値オブジェクト:</span><span class="sxs-lookup"><span data-stu-id="59af9-525">The route values object:</span></span>

* <span data-ttu-id="59af9-526">慣例により、通常は匿名型のオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="59af9-526">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="59af9-527">には、 `IDictionary<>` または [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)を指定できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-527">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="59af9-528">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-528">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="59af9-529">前のコードによってが生成さ `/Products/Buy/17?color=red` れます。</span><span class="sxs-lookup"><span data-stu-id="59af9-529">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="59af9-530">次のコードでは、絶対 URL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-530">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="59af9-531">絶対 URL を作成するには、次のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-531">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="59af9-532">を受け入れるオーバーロード `protocol` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-532">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="59af9-533">たとえば、上記のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-533">For example, the preceding code.</span></span>
* <span data-ttu-id="59af9-534">[Linkgenerator. GetUriByAction。](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)既定で絶対 uri を生成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-534">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="59af9-535">ルートによる Url の生成</span><span class="sxs-lookup"><span data-stu-id="59af9-535">Generate URLs by route</span></span>

<span data-ttu-id="59af9-536">前のコードでは、コントローラーとアクション名を渡すことによって URL を生成することを示していました。</span><span class="sxs-lookup"><span data-stu-id="59af9-536">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="59af9-537">`IUrlHelper` には、メソッドの [Url routeurl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) ファミリも用意されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-537">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="59af9-538">これらのメソッドは、 [Url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)に似ていますが、との現在の値 `action` `controller` をルート値にコピーしません。</span><span class="sxs-lookup"><span data-stu-id="59af9-538">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="59af9-539">の最も一般的な使用方法は `Url.RouteUrl` 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="59af9-539">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="59af9-540">URL を生成するルート名を指定します。</span><span class="sxs-lookup"><span data-stu-id="59af9-540">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="59af9-541">通常、コントローラーまたはアクション名は指定しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-541">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="59af9-542">次の Razor ファイルでは、への HTML リンクが生成され `Destination_Route` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-542">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a><span data-ttu-id="59af9-543">HTML およびでの Url の生成 Razor</span><span class="sxs-lookup"><span data-stu-id="59af9-543">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="59af9-544"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>メソッドを使用して、 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> 要素と要素をそれぞれ生成するように[Html.actionlink と html](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*)を[提供します](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*)。 `<form>` `<a>`</span><span class="sxs-lookup"><span data-stu-id="59af9-544"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="59af9-545">これらのメソッドは、Url を生成するために [url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) メソッドを使用し、同様の引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="59af9-545">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="59af9-546">`HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="59af9-546">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="59af9-547">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-547">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="59af9-548">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-548">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="59af9-549">詳細については、「 [フォームのタグヘルパー](xref:mvc/views/working-with-forms) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-549">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="59af9-550">ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="59af9-550">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="59af9-551">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="59af9-551">URL generation in Action Results</span></span>

<span data-ttu-id="59af9-552">前の例では、コントローラーでを使用して示しました `IUrlHelper` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-552">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="59af9-553">コントローラーで最も一般的に使用されるのは、アクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="59af9-553">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="59af9-554">基底クラス <xref:Microsoft.AspNetCore.Mvc.ControllerBase> および <xref:Microsoft.AspNetCore.Mvc.Controller> では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-554">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="59af9-555">一般的な使用方法の1つは、ユーザー入力を受け入れた後にリダイレクトすることです。</span><span class="sxs-lookup"><span data-stu-id="59af9-555">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="59af9-556">アクションの結果ファクトリメソッド (やなど) は、 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> のメソッドと同様のパターンに従い `IUrlHelper` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-556">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="59af9-557">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="59af9-557">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="59af9-558">[従来のルーティング](#cr) では、 [専用の従来のルート](#dcr)と呼ばれる特別な種類のルート定義を使用できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-558">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="59af9-559">次の例では、という名前のルート `blog` が専用の従来のルートになっています。</span><span class="sxs-lookup"><span data-stu-id="59af9-559">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="59af9-560">前のルート定義を使用すると、は `Url.Action("Index", "Home")` ルートを使用して URL パスを生成し `/` `default` ますが、その理由は何でしょうか。</span><span class="sxs-lookup"><span data-stu-id="59af9-560">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="59af9-561">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-561">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="59af9-562">[専用の従来のルート](#dcr) では、ルートパラメーターが指定されていない既定値の特殊な動作に依存しています。これにより、ルートが URL 生成に [最長一致](xref:fundamentals/routing#greedy) するのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-562">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="59af9-563">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="59af9-563">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="59af9-564">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-564">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="59af9-565">値が一致しないため、を使用して URL を生成すること `blog` はでき `{ controller = Home, action = Index }` ません `{ controller = Blog, action = Article }` 。</span><span class="sxs-lookup"><span data-stu-id="59af9-565">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="59af9-566">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="59af9-566">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="59af9-567">Areas</span><span class="sxs-lookup"><span data-stu-id="59af9-567">Areas</span></span>

<span data-ttu-id="59af9-568">[区分](xref:mvc/controllers/areas) は、関連する機能を別のグループとしてグループにまとめるために使用される MVC 機能です。</span><span class="sxs-lookup"><span data-stu-id="59af9-568">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="59af9-569">コントローラーアクションのルーティング名前空間。</span><span class="sxs-lookup"><span data-stu-id="59af9-569">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="59af9-570">ビューのフォルダー構造。</span><span class="sxs-lookup"><span data-stu-id="59af9-570">Folder structure for views.</span></span>

<span data-ttu-id="59af9-571">区分を使用すると、異なる領域がある限り、同じ名前の複数のコントローラーをアプリに割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-571">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="59af9-572">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-572">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="59af9-573">ここでは、ルーティングと領域の相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="59af9-573">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="59af9-574">領域をビューで使用する方法の詳細については、「 [区分](xref:mvc/controllers/areas) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-574">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="59af9-575">次の例では、既定の従来のルートと、 `area` という名前ののルートを使用するように MVC を構成し `area` `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-575">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="59af9-576">上記のコードで <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> は、が呼び出され、が作成され `"blog_route"` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-576">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="59af9-577">2番目のパラメーターは、 `"Blog"` 領域名です。</span><span class="sxs-lookup"><span data-stu-id="59af9-577">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="59af9-578">のような URL パスを照合すると、ルート `/Manage/Users/AddUser` によっ `"blog_route"` てルート値が生成され `{ area = Blog, controller = Users, action = AddUser }` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-578">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="59af9-579">`area`ルート値は、の既定値によって生成され `area` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-579">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="59af9-580">によって作成されるルート `MapAreaControllerRoute` は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-580">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="59af9-581">`MapAreaControllerRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-581">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="59af9-582">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="59af9-582">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="59af9-583">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="59af9-583">Conventional routing is order-dependent.</span></span> <span data-ttu-id="59af9-584">一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-584">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="59af9-585">前の例を使用すると、ルート値は `{ area = Blog, controller = Users, action = AddUser }` 次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-585">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="59af9-586">[[区分]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性は、1つの領域の一部としてコントローラーを表します。</span><span class="sxs-lookup"><span data-stu-id="59af9-586">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="59af9-587">このコントローラーは、この `Blog` 領域にあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-587">This controller is in the `Blog` area.</span></span> <span data-ttu-id="59af9-588">属性を持たないコントローラー `[Area]` は、どの領域のメンバーでもなく、 `area` ルーティングによってルート値が提供されるときには一致しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-588">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="59af9-589">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-589">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="59af9-590">各コントローラーの名前空間は、完全を期すためにここに示されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-590">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="59af9-591">前のコントローラーで同じ名前空間が使用されている場合は、コンパイラエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-591">If the preceding controllers used the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="59af9-592">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="59af9-592">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="59af9-593">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-593">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="59af9-594">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-594">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="59af9-595">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="59af9-595">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="59af9-596">領域内でアクションを実行する場合、のルート値 `area` は、URL の生成に使用するルーティングの [アンビエント値](#ambient) として使用できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-596">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="59af9-597">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="59af9-597">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="59af9-598">次のコードでは、への URL が生成され `/Zebra/Users/AddUser` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-598">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="59af9-599">アクションの定義</span><span class="sxs-lookup"><span data-stu-id="59af9-599">Action definition</span></span>

<span data-ttu-id="59af9-600">[非 action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute)属性を持つパブリックメソッドは、アクションです。</span><span class="sxs-lookup"><span data-stu-id="59af9-600">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="59af9-601">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="59af9-601">Sample code</span></span>

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* <span data-ttu-id="59af9-602">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="59af9-602">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="59af9-603">ASP.NET Core MVC は、ルーティング [ミドルウェア](xref:fundamentals/middleware/index)を使って、受信した要求の URL を照合し、アクションにマップします。</span><span class="sxs-lookup"><span data-stu-id="59af9-603">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="59af9-604">ルートは、スタートアップ コードまたは属性で定義されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-604">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="59af9-605">ルートでは、URL パスとアクションの照合方法が記述されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-605">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="59af9-606">また、ルートは、応答で送信される (リンク用) の URL の生成にも使われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-606">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="59af9-607">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-607">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="59af9-608">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-608">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="59af9-609">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-609">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="59af9-610">このドキュメントでは、MVC とルーティングの間の相互作用、および標準的な MVC アプリでのルーティング機能の利用方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="59af9-610">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="59af9-611">高度なルーティングについて詳しくは、「[ASP.NET Core のルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-611">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="59af9-612">ルーティング ミドルウェアの設定</span><span class="sxs-lookup"><span data-stu-id="59af9-612">Setting up Routing Middleware</span></span>

<span data-ttu-id="59af9-613">*Configure* メソッドには、次のようなコードが含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-613">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="59af9-614">`UseMvc` の呼び出しの内部で、`MapRoute` は単一のルートの作成に使われます。これは、`default` ルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-614">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="59af9-615">ほとんどの MVC アプリは、`default` ルートのようなルートをテンプレートで使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-615">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="59af9-616">ルート テンプレート `"{controller=Home}/{action=Index}/{id?}"` は、`/Products/Details/5` のような URL パスと一致し、パスをトークン化することによってルート値 `{ controller = Products, action = Details, id = 5 }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="59af9-616">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="59af9-617">MVC は、`ProductsController` という名前のコントローラーを探して、アクション `Details` の実行を試みます。</span><span class="sxs-lookup"><span data-stu-id="59af9-617">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="59af9-618">この例では、このアクションを呼び出すときに、モデル バインドが `id = 5` という値を使って、`id` パラメーターを `5` に設定することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-618">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="59af9-619">詳しくは、「[モデル バインド](../models/model-binding.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-619">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="59af9-620">`default` ルートの使用:</span><span class="sxs-lookup"><span data-stu-id="59af9-620">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="59af9-621">ルート テンプレート:</span><span class="sxs-lookup"><span data-stu-id="59af9-621">The route template:</span></span>

* <span data-ttu-id="59af9-622">`{controller=Home}` は、`Home` を既定の `controller` として定義します</span><span class="sxs-lookup"><span data-stu-id="59af9-622">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="59af9-623">`{action=Index}` は、`Index` を既定の `action` として定義します</span><span class="sxs-lookup"><span data-stu-id="59af9-623">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="59af9-624">`{id?}` は、`id` を省略可能として定義します</span><span class="sxs-lookup"><span data-stu-id="59af9-624">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="59af9-625">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-625">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="59af9-626">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-626">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="59af9-627">`"{controller=Home}/{action=Index}/{id?}"` は URL パス `/` と一致でき、ルート値 `{ controller = Home, action = Index }` を生成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-627">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="59af9-628">`controller` と `action` の値は既定値を使い、`id` は URL パスに対応するセグメントが存在しないため値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="59af9-628">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="59af9-629">MVC はこれらのルート値を使って、`HomeController` および `Index` アクションを選びます。</span><span class="sxs-lookup"><span data-stu-id="59af9-629">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="59af9-630">このコントローラー定義とルート テンプレートを使うと、`HomeController.Index` アクションは次のいずれかの URL パスに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-630">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="59af9-631">便利なメソッド `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="59af9-631">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="59af9-632">これは、次の代わりに使うことができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-632">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="59af9-633">`UseMvc` および `UseMvcWithDefaultRoute` は、`RouterMiddleware` のインスタンスをミドルウェア パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="59af9-633">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="59af9-634">MVC は、ミドルウェアと直接相互作用せず、ルーティングを使って要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="59af9-634">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="59af9-635">MVC は、`MvcRouteHandler` のインスタンスによってルートに接続されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-635">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="59af9-636">`UseMvc` の内部のコードは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-636">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="59af9-637">`UseMvc` は、ルートを直接定義することはなく、`attribute` ルートのルート コレクションにプレースホルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="59af9-637">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="59af9-638">オーバーロード `UseMvc(Action<IRouteBuilder>)` は、独自ルートの追加を可能にし、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="59af9-638">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="59af9-639">`UseMvc` とそのすべてのバリエーションは、属性ルートのプレースホルダーを追加します。属性ルーティングは、`UseMvc` の構成方法に関係なく常に利用できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-639">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="59af9-640">`UseMvcWithDefaultRoute` は既定のルートを定義し、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="59af9-640">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="59af9-641">「[属性ルーティング](#attribute-routing-ref-label)」セクションでは、属性ルーティングについてさらに詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="59af9-641">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="59af9-642">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-642">Conventional routing</span></span>

<span data-ttu-id="59af9-643">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="59af9-643">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="59af9-644">前のコードは、従来のルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="59af9-644">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="59af9-645">このスタイルは、URL パスの *規則* を確立するため、従来のルーティングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-645">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="59af9-646">最初のパスセグメントは、コントローラー名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-646">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="59af9-647">2つ目は、アクション名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-647">The second maps to the action name.</span></span>
* <span data-ttu-id="59af9-648">3番目のセグメントは、省略可能なに使用され `id` ます。</span><span class="sxs-lookup"><span data-stu-id="59af9-648">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="59af9-649">`id` モデルエンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-649">`id` maps to a model entity.</span></span>

<span data-ttu-id="59af9-650">この `default` ルートを使うと、URL パス `/Products/List` は `ProductsController.List` アクションにマップし、`/Blog/Article/17` は `BlogController.Article` にマップします。</span><span class="sxs-lookup"><span data-stu-id="59af9-650">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="59af9-651">このマッピングは、コントローラーとアクションの名前に **のみ** 基づき、名前空間、ソース ファイルの場所、またはメソッドのパラメーターには基づきません。</span><span class="sxs-lookup"><span data-stu-id="59af9-651">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="59af9-652">既定のルートで規則ルーティングを使うと、定義するアクションごとに新しい URL パターンを考える必要がなく、アプリケーションをすばやく構築できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-652">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="59af9-653">CRUD スタイルのアクションを使うアプリケーションでは、コントローラー間で URL に一貫性を持たせると、コードが容易になり、UI の予測可能性が向上します。</span><span class="sxs-lookup"><span data-stu-id="59af9-653">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="59af9-654">`id` はルート テンプレートで省略可能と定義されており、これは URL の一部として ID を提供されなくてもアクションを実行できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="59af9-654">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="59af9-655">通常、`id` が URL から省略されると、ID はモデル バインドによって `0` に設定され、結果として、`id == 0` と一致するエンティティはデータベースで検索されません。</span><span class="sxs-lookup"><span data-stu-id="59af9-655">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="59af9-656">属性ルーティングを使うと、ID が必須のアクションと必須ではないアクションをきめ細かく制御できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-656">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="59af9-657">慣例に従って、ドキュメントでは `id` などの省略可能なパラメーターが正しい使用法で使われる可能性がある場合はパラメーターを記載してあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-657">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="59af9-658">複数のルート</span><span class="sxs-lookup"><span data-stu-id="59af9-658">Multiple routes</span></span>

<span data-ttu-id="59af9-659">`MapRoute` の呼び出しをさらに追加することで、`UseMvc` の内部に複数のルートを追加できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-659">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="59af9-660">これにより、次のように、複数の規則を定義すること、または特定のアクションに専用の規則ルートを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-660">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="59af9-661">`blog` ルートは "*専用の規則ルート*" です。これは、このルートは規則ルーティング システムを使いますが、特定のアクション専用であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="59af9-661">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="59af9-662">`controller` と `action` はパラメーターとしてルート テンプレートに表示されないので、既定値を持つことだけができ、したがってこのルートは常に `BlogController.Article` アクションにマップされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-662">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="59af9-663">ルート コレクション内のルートには順序があり、追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-663">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="59af9-664">そのため、この例では、`blog` ルートが `default` ルートより先に試されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-664">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="59af9-665">*専用の従来のルート* では、多くの場合、のような **キャッチオール** ルートパラメーターを使用して、 `{*article}` URL パスの残りの部分をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="59af9-665">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="59af9-666">このようにすると、ルートの "一致範囲が広くなりすぎて"、他のルートと一致させるつもりであった URL まで一致する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-666">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="59af9-667">この問題を解決するには、"一致範囲の広い" ルートはルート テーブルの後の方に配置します。</span><span class="sxs-lookup"><span data-stu-id="59af9-667">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="59af9-668">フォールバック</span><span class="sxs-lookup"><span data-stu-id="59af9-668">Fallback</span></span>

<span data-ttu-id="59af9-669">要求処理の一環として、MVC はルートの値を使ってアプリケーション内のコントローラーとアクションを検索できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="59af9-669">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="59af9-670">ルートの値がアクションと一致しない場合、そのルートは一致と見なされず、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-670">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="59af9-671">これは "*フォールバック*" と呼ばれ、規則ルートがオーバーラップしている場合の簡略化を意図したものです。</span><span class="sxs-lookup"><span data-stu-id="59af9-671">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="59af9-672">アクションの明確化</span><span class="sxs-lookup"><span data-stu-id="59af9-672">Disambiguating actions</span></span>

<span data-ttu-id="59af9-673">2 つのアクションがルーティングで一致する場合、MVC はあいまいさを解消して "最善の" 候補を選ぶか、または例外をスローする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-673">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="59af9-674">例:</span><span class="sxs-lookup"><span data-stu-id="59af9-674">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="59af9-675">このコントローラーでは、URL パス `/Products/Edit/17` およびルート データ `{ controller = Products, action = Edit, id = 17 }` と一致する 2 つのアクションが定義されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-675">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="59af9-676">これは、`Edit(int)` で製品を編集するためのフォームを表示し、`Edit(int, Product)` で送信されたフォームを処理する MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="59af9-676">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="59af9-677">これを MVC で可能にするには、要求が HTTP `POST` の場合は `Edit(int, Product)` を選び、それ以外の HTTP 動詞の場合は `Edit(int)` を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-677">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="59af9-678">`HttpPostAttribute` (`[HttpPost]`) は、HTTP 動詞が `POST` の場合にのみそのアクションの選択を許可する `IActionConstraint` の実装です。</span><span class="sxs-lookup"><span data-stu-id="59af9-678">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="59af9-679">`IActionConstraint` が存在すると、`Edit(int, Product)` の方が `Edit(int)` より "良い" 一致になるので、`Edit(int, Product)` が最初に試されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-679">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="59af9-680">カスタム `IActionConstraint` を作成する必要があるのは特殊なシナリオだけですが、`HttpPostAttribute` のような属性の役割を理解しておくことが重要です。似た属性が他の HTTP 動詞に対しても定義されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-680">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="59af9-681">規則ルーティングでは、アクションが `show form -> submit form` ワークフローの一部になっている場合、複数のアクションが同じアクション名を使うのはよくあることです。</span><span class="sxs-lookup"><span data-stu-id="59af9-681">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="59af9-682">「[IActionConstraint について](#understanding-iactionconstraint)」セクションを読むと、このパターンの便利さがいっそうよくわかります。</span><span class="sxs-lookup"><span data-stu-id="59af9-682">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="59af9-683">複数のルートが一致し、MVC が "最善の " ルートを発見できない場合、MVC は `AmbiguousActionException` をスローします。</span><span class="sxs-lookup"><span data-stu-id="59af9-683">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="59af9-684">ルート名</span><span class="sxs-lookup"><span data-stu-id="59af9-684">Route names</span></span>

<span data-ttu-id="59af9-685">次の例の文字列 `"blog"` と `"default"` は、ルート名です。</span><span class="sxs-lookup"><span data-stu-id="59af9-685">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="59af9-686">ルート名は、URL の生成に名前付きのルートを使うことができるよう、ルートに論理名を提供します。</span><span class="sxs-lookup"><span data-stu-id="59af9-686">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="59af9-687">これは、ルートの順序指定によって URL の生成が複雑になる場合に、URL の作成を大幅に合理化します。</span><span class="sxs-lookup"><span data-stu-id="59af9-687">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="59af9-688">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-688">Route names must be unique application-wide.</span></span>

<span data-ttu-id="59af9-689">ルート名が URL の照合や要求の処理に影響を与えることはありません。ルート名は URL の生成のみに使われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-689">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="59af9-690">MVC 固有のヘルパーでの URL の生成など、URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-690">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="59af9-691">属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-691">Attribute routing</span></span>

<span data-ttu-id="59af9-692">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="59af9-692">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="59af9-693">次の例では、`app.UseMvc();` が `Configure` メソッド内で使われており、ルートは渡されていません。</span><span class="sxs-lookup"><span data-stu-id="59af9-693">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="59af9-694">`HomeController` は、既定のルート `{controller=Home}/{action=Index}/{id?}` が一致するのと同じように、URL のセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-694">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="59af9-695">`HomeController.Index()` アクションは、URL パス `/`、`/Home`、または `/Home/Index` のいずれかに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-695">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="59af9-696">この例では、属性ルーティングと規則ルーティングでのプログラミングの大きな違いが強調して示されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-696">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="59af9-697">属性ルーティングの方が、ルートを指定するために多くの入力を必要とします。既定の規則ルートの方が簡潔にルートを処理します。</span><span class="sxs-lookup"><span data-stu-id="59af9-697">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="59af9-698">ただし、属性ルーティングでは、各アクションに適用するルート テンプレートを正確に制御できます (そして制御する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="59af9-698">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="59af9-699">属性ルーティングでは、コントローラー名とアクション名はアクションの選択において役割を **持ちません**。</span><span class="sxs-lookup"><span data-stu-id="59af9-699">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="59af9-700">この例では、前の例と同じ URL を照合します。</span><span class="sxs-lookup"><span data-stu-id="59af9-700">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="59af9-701">上のルート テンプレートでは、`action`、`area`、`controller` のルート パラメーターは定義されていません。</span><span class="sxs-lookup"><span data-stu-id="59af9-701">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="59af9-702">実際、これらのルート パラメーターは属性ルートでは許可されていません。</span><span class="sxs-lookup"><span data-stu-id="59af9-702">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="59af9-703">ルート テンプレートはアクションと既に関連付けられているため、URL からアクション名を解析しても意味はありません。</span><span class="sxs-lookup"><span data-stu-id="59af9-703">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="59af9-704">Http[Verb] 属性を使用する属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-704">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="59af9-705">属性ルーティングでは、`HttpPostAttribute` などの `Http[Verb]` 属性も使うことができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-705">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="59af9-706">これらの属性はすべて、ルート テンプレートを受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-706">All of these attributes can accept a route template.</span></span> <span data-ttu-id="59af9-707">次の例では、同じルート テンプレートと一致する 2 つのアクションが示されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-707">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="59af9-708">`/products` のような URL パスの場合、HTTP 動詞が `GET` のときは `ProductsApi.ListProducts` アクションが実行され、HTTP 動詞が `POST` のときは `ProductsApi.CreateProduct` が実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-708">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="59af9-709">属性ルーティングでは、最初に、ルート属性で定義されているルート テンプレートのセットに対して URL が照合されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-709">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="59af9-710">ルート テンプレートが一致すると、`IActionConstraint` 制約が適用されて、実行できるアクションが決定されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-710">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="59af9-711">REST API を構築する場合、アクションではすべての HTTP メソッドが受け入れられるので、アクション メソッドに対して `[Route(...)]` を使用することはまれです。</span><span class="sxs-lookup"><span data-stu-id="59af9-711">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="59af9-712">より固有の `Http*Verb*Attributes` を使って、API がサポートするものを正確に指定するのがよい方法です。</span><span class="sxs-lookup"><span data-stu-id="59af9-712">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="59af9-713">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-713">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="59af9-714">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="59af9-714">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="59af9-715">次の例では、`id` は URL パスの一部として必須です。</span><span class="sxs-lookup"><span data-stu-id="59af9-715">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="59af9-716">`ProductsApi.GetProduct(int)` アクションは、`/products/3` のような URL パスに対しては実行されますが、`/products` のような URL パスに対しては実行されません。</span><span class="sxs-lookup"><span data-stu-id="59af9-716">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="59af9-717">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-717">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="59af9-718">ルート名</span><span class="sxs-lookup"><span data-stu-id="59af9-718">Route Name</span></span>

<span data-ttu-id="59af9-719">次のコードでは、`Products_List` の "*ルート名*" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="59af9-719">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="59af9-720">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-720">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="59af9-721">ルート名は、ルーティングの URL 照合動作には影響を与えず、URL 生成に対してのみ使われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-721">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="59af9-722">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-722">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="59af9-723">これに対し、規則 "*既定ルート*" では、`id` パラメーターは省略可能 (`{id?}`) として定義されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-723">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="59af9-724">API を厳密に指定するこの機能には、`/products` と `/products/5` を異なるアクションに対してディスパッチできるといった利点があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-724">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="59af9-725">ルートの結合</span><span class="sxs-lookup"><span data-stu-id="59af9-725">Combining routes</span></span>

<span data-ttu-id="59af9-726">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-726">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="59af9-727">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-727">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="59af9-728">ルート属性をコントローラーに配置すると、コントローラーの **すべて** のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-728">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="59af9-729">次の例では、URL パス `/products` は `ProductsApi.ListProducts` と一致でき、URL パス `/products/5` は `ProductsApi.GetProduct(int)` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-729">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="59af9-730">どちらのアクションも、`HttpGetAttribute` でマークされているため、HTTP `GET` だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-730">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="59af9-731">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="59af9-731">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="59af9-732">次の例は、"*既定ルート*" と同様の URL パスのセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-732">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="59af9-733">属性ルートの順序の指定</span><span class="sxs-lookup"><span data-stu-id="59af9-733">Ordering attribute routes</span></span>

<span data-ttu-id="59af9-734">定義された順序で実行される従来のルートとは対照的に、属性ルーティングはツリーを構築し、すべてのルートを同時に照合します。</span><span class="sxs-lookup"><span data-stu-id="59af9-734">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="59af9-735">これは、ルート エントリが理想的な順序で配置されているかのように動作します。一般的なルートより前に、最も固有のルートが実行する機会があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-735">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="59af9-736">たとえば、`blog/search/{topic}` のようなルートは、`blog/{*article}` のようなルートより固有です。</span><span class="sxs-lookup"><span data-stu-id="59af9-736">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="59af9-737">論理的には、`blog/search/{topic}` ルートが唯一の意味のある順序なので、既定では、これが最初に "実行" されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-737">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="59af9-738">規則ルーティングを使うときは、開発者が目的の順序でルートを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-738">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="59af9-739">属性ルートでは、フレームワークが提供するすべてのルート属性の `Order` プロパティを使って、順序を構成できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-739">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="59af9-740">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-740">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="59af9-741">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="59af9-741">The default order is `0`.</span></span> <span data-ttu-id="59af9-742">`Order = -1` を使ってルートを設定すると、順序が設定されていないルートの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-742">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="59af9-743">`Order = 1` を使ってルートを設定すると、既定のルート順序の後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-743">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="59af9-744">`Order` には依存しないでください。</span><span class="sxs-lookup"><span data-stu-id="59af9-744">Avoid depending on `Order`.</span></span> <span data-ttu-id="59af9-745">URL 空間で正しくルーティングするために明示的な順序値が必要な場合、クライアントの混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-745">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="59af9-746">一般に、属性ルーティングは URL 照合で正しいルートを選びます。</span><span class="sxs-lookup"><span data-stu-id="59af9-746">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="59af9-747">URL の生成に使われる既定の順序がうまくいかない場合は、通常、オーバーライドとしてルート名を使う方が、`Order` プロパティを適用するより簡単です。</span><span class="sxs-lookup"><span data-stu-id="59af9-747">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="59af9-748">Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="59af9-748">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="59af9-749">「ページのルート Razor [ Razor とアプリの規則: ルートの順序](xref:razor-pages/razor-pages-conventions#route-order)」で、ルートの順序に関する情報を参照できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-749">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="59af9-750">ルート テンプレートでのトークンの置換 ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="59af9-750">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="59af9-751">便宜上、属性ルートは、トークンを角かっこ (,) で囲むことによって *トークンの置換* をサポートして `[` `]` います。</span><span class="sxs-lookup"><span data-stu-id="59af9-751">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="59af9-752">トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、区分名、コントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="59af9-752">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="59af9-753">次の例では、アクションはコメントで説明されているように URL パスと一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-753">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="59af9-754">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="59af9-754">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="59af9-755">上の例は、次のコードと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="59af9-755">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="59af9-756">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="59af9-756">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="59af9-757">これをトークンの置換と組み合わせると特に強力です。</span><span class="sxs-lookup"><span data-stu-id="59af9-757">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="59af9-758">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-758">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="59af9-759">`[Route("[controller]/[action]", Name="[controller]_[action]")]` では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-759">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="59af9-760">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="59af9-760">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="59af9-761">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="59af9-761">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="59af9-762">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="59af9-762">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="59af9-763">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="59af9-763">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="59af9-764">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-764">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="59af9-765">`RouteTokenTransformerConvention` は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="59af9-765">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="59af9-766">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="59af9-766">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="59af9-767">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="59af9-767">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="59af9-768">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-768">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="59af9-769">複数のルート</span><span class="sxs-lookup"><span data-stu-id="59af9-769">Multiple Routes</span></span>

<span data-ttu-id="59af9-770">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59af9-770">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="59af9-771">これの最も一般的な使用方法は、次の例で示すように、"*既定の規則ルート*" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="59af9-771">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="59af9-772">コントローラーに複数のルート属性を配置すると、それぞれが、アクション メソッドの各ルート属性と結合します。</span><span class="sxs-lookup"><span data-stu-id="59af9-772">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="59af9-773">アクションに (`IActionConstraint` を実装する) 複数のルート属性を配置すると、各アクション制約がそれを定義した属性のルート テンプレートと結合します。</span><span class="sxs-lookup"><span data-stu-id="59af9-773">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="59af9-774">アクションで複数のルートを使うと強力に見えますが、アプリケーションの URL 空間は簡潔で明確に定義された状態に保つことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="59af9-774">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="59af9-775">アクションで複数のルートを使うのは、必要な場合 (既存のクライアントをサポートする、など) だけにしてください。</span><span class="sxs-lookup"><span data-stu-id="59af9-775">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="59af9-776">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="59af9-776">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="59af9-777">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59af9-777">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="59af9-778">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-778">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="59af9-779">`IRouteTemplateProvider` を使用するカスタム ルート属性</span><span class="sxs-lookup"><span data-stu-id="59af9-779">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="59af9-780">フレームワークで提供されるすべてのルート属性 (`[Route(...)]`、`[HttpGet(...)]` など) は、`IRouteTemplateProvider` インターフェイスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="59af9-780">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="59af9-781">アプリが起動し、`IRouteTemplateProvider` を実装している属性を使ってルートの初期セットを作成すると、MVC はコントローラー クラスとアクション メソッドで属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="59af9-781">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="59af9-782">`IRouteTemplateProvider` を実装して、独自のルート属性を定義できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-782">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="59af9-783">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-783">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="59af9-784">上の例の属性は、`[MyApiController]` が適用されると、自動的に `Template` を `"api/[controller]"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="59af9-784">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="59af9-785">アプリケーション モデルを使用した属性ルートのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="59af9-785">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="59af9-786">"*アプリケーション モデル*" は、アクションをルーティングおよび実行するために MVC によって使われるすべてのメタデータで起動時に作成されるオブジェクト モデルです。</span><span class="sxs-lookup"><span data-stu-id="59af9-786">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="59af9-787">"*アプリケーション モデル*" には、(`IRouteTemplateProvider` によって) ルート属性から収集されるすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-787">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="59af9-788">起動時にアプリケーション モデルを変更してルーティングの動作方法をカスタマイズする "*規則*" を作成できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-788">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="59af9-789">このセクションでは、アプリケーション モデルを使ってルーティングをカスタマイズする簡単な例を示します。</span><span class="sxs-lookup"><span data-stu-id="59af9-789">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="59af9-790">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="59af9-790">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="59af9-791">MVC アプリケーションでは、規則ルーティングと属性ルーティングを併用できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-791">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="59af9-792">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-792">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="59af9-793">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-793">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="59af9-794">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="59af9-794">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="59af9-795">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="59af9-795">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="59af9-796">コントローラーの **任意** のルート属性により、コントローラー属性のすべてのアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-796">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="59af9-797">2 種類のルーティング システムを区別しているのは、URL がルート テンプレートと一致した後に適用されるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="59af9-797">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="59af9-798">規則ルーティングでは、一致のルート値を使って、規則ルーティングされるすべてのアクションの参照テーブルから、アクションとコントローラーが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-798">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="59af9-799">属性ルーティングでは、各テンプレートはアクションと既に関連付けられており、それ以上参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="59af9-799">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="59af9-800">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="59af9-800">Complex segments</span></span>

<span data-ttu-id="59af9-801">複雑なセグメント (たとえば、`[Route("/dog{token}cat")]`) は、右から左にリテラルを最短の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-801">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="59af9-802">説明については、[ソース コード](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-802">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="59af9-803">詳細については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8197)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59af9-803">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="59af9-804">URL の生成</span><span class="sxs-lookup"><span data-stu-id="59af9-804">URL Generation</span></span>

<span data-ttu-id="59af9-805">MVC アプリケーションでは、ルーティングの URL 生成機能を使って、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-805">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="59af9-806">URL を生成すると URL をハードコーディングする必要がなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="59af9-806">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="59af9-807">このセクションでは、MVC によって提供される URL 生成機能について説明します。URL 生成のしくみに関する基本だけを取り上げます。</span><span class="sxs-lookup"><span data-stu-id="59af9-807">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="59af9-808">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-808">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="59af9-809">`IUrlHelper` インターフェイスは、MVC と URL 生成のルーティングの間にあるインフラストラクチャの基盤部分です。</span><span class="sxs-lookup"><span data-stu-id="59af9-809">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="59af9-810">使用可能な `IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビュー コンポーネントの `Url` プロパティを使って探します。</span><span class="sxs-lookup"><span data-stu-id="59af9-810">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="59af9-811">この例の `IUrlHelper` インターフェイスは、別のアクションへの URL を生成するために `Controller.Url` プロパティを介して使われています。</span><span class="sxs-lookup"><span data-stu-id="59af9-811">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="59af9-812">アプリケーションが既定の規則ルートを使っている場合、`url` 変数の値は URL パス文字列 `/UrlGeneration/Destination` になります。</span><span class="sxs-lookup"><span data-stu-id="59af9-812">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="59af9-813">この URL パスは、ルーティングにより、現在の要求からのルート値 (アンビエント値) と、`Url.Action` に渡された値を結合し、これらの値でルート テンプレートを置換することによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-813">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="59af9-814">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-814">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="59af9-815">値を持たないルート パラメーターは、既定値がある場合はそれを使うことができ、省略可能な場合はスキップできます (この例の `id` の場合と同様)。</span><span class="sxs-lookup"><span data-stu-id="59af9-815">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="59af9-816">必須のルート パラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="59af9-816">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="59af9-817">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-817">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="59af9-818">上の `Url.Action` の例では規則ルーティングを想定しています。URL 生成は属性ルーティングでも同じように動作しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="59af9-818">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="59af9-819">規則ルーティングでは、ルート値を使ってテンプレートが展開され、通常、`controller` と `action` のルートがそのテンプレートに表示されます。これは、ルーティングによって一致した URL が "*規則*" に従うために動作します。</span><span class="sxs-lookup"><span data-stu-id="59af9-819">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="59af9-820">属性ルーティングでは、`controller` と `action` のルート値はテンプレートに表示できません。代わりに、使うテンプレートの検索に使われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-820">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="59af9-821">この例では、属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-821">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="59af9-822">MVC はすべての属性ルーティングされるアクションの参照テーブルを作成し、`controller` と `action` の値で照合して、URL 生成に使うルート テンプレートを選びます。</span><span class="sxs-lookup"><span data-stu-id="59af9-822">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="59af9-823">上の例では、`custom/url/to/destination` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-823">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="59af9-824">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="59af9-824">Generating URLs by action name</span></span>

<span data-ttu-id="59af9-825">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="59af9-825">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="59af9-826">`Action`) およびすべての関連するオーバーロードは、コントローラー名とアクション名を指定することによってリンク先を指定するアイデアに基づきます。</span><span class="sxs-lookup"><span data-stu-id="59af9-826">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="59af9-827">`Url.Action` を使うと、`controller` および `action` の現在のルート値が自動的に指定されます。`controller` および `action` の値は、"*アンビエント値"* **と "** *値*" 両方の一部です。</span><span class="sxs-lookup"><span data-stu-id="59af9-827">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="59af9-828">`Url.Action` メソッドは、常に `action` と `controller` の現在の値は使い、現在のアクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-828">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="59af9-829">ルーティングは、ユーザーが URL 生成時に指定しなかった情報を、アンビエント値を使って埋めようとします。</span><span class="sxs-lookup"><span data-stu-id="59af9-829">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="59af9-830">`{a}/{b}/{c}/{d}` やアンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }` のようなルートを使うと、すべてのルート パラメーターが値を持っているため、ルーティングには URL を生成するための十分な情報があり、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="59af9-830">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="59af9-831">値 `{ d = Donovan }` を追加した場合は、値 `{ d = David }` は無視されて、生成される URL パスは `Alice/Bob/Carol/Donovan` になります。</span><span class="sxs-lookup"><span data-stu-id="59af9-831">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="59af9-832">URL パスは階層的です。</span><span class="sxs-lookup"><span data-stu-id="59af9-832">URL paths are hierarchical.</span></span> <span data-ttu-id="59af9-833">上の例で、値 `{ c = Cheryl }` を追加した場合は、両方の値 `{ c = Carol, d = David }` が無視されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-833">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="59af9-834">この場合、`d` には値がなくなり、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="59af9-834">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="59af9-835">`c` および `d` の目的の値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-835">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="59af9-836">既定のルート (`{controller}/{action}/{id?}`) でもこの問題が発生すると思われるかもしれませんが、`Url.Action` が常に `controller` と `action` の値を明示的に指定するので、実際には、このような動作が発生することはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="59af9-836">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="59af9-837">`Url.Action` の長いオーバーロードも、追加の "*ルート値*" オブジェクトを受け取って、`controller` および `action` 以外のルート パラメーターの値を提供します。</span><span class="sxs-lookup"><span data-stu-id="59af9-837">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="59af9-838">これを最もよく目にするのは、`Url.Action("Buy", "Products", new { id = 17 })` のように `id` で使われる場合です。</span><span class="sxs-lookup"><span data-stu-id="59af9-838">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="59af9-839">慣例では、"*ルート値*" オブジェクトは通常は匿名型のオブジェクトですが、`IDictionary<>` または "*単純な従来の .NET オブジェクト*" を使うこともできます。</span><span class="sxs-lookup"><span data-stu-id="59af9-839">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="59af9-840">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-840">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="59af9-841">絶対 URL を作成するには、`protocol` を受け取るオーバーロードを使います。`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="59af9-841">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="59af9-842">ルートによる URL の生成</span><span class="sxs-lookup"><span data-stu-id="59af9-842">Generating URLs by route</span></span>

<span data-ttu-id="59af9-843">上記のコードでは、コントローラーとアクション名を渡すことによる URL の生成を示しました。</span><span class="sxs-lookup"><span data-stu-id="59af9-843">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="59af9-844">`IUrlHelper` では、`Url.RouteUrl` ファミリ メソッドも提供されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-844">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="59af9-845">これらのメソッドは、`Url.Action` と似ていますが、`action` および `controller` の現在の値をルート値にコピーしません。</span><span class="sxs-lookup"><span data-stu-id="59af9-845">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="59af9-846">最も一般的な使用方法は、ルート名を指定して特定のルートを使って URL を生成する場合です。通常、コントローラーまたはアクション名は指定 "*しません*"。</span><span class="sxs-lookup"><span data-stu-id="59af9-846">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="59af9-847">HTML での URL の生成</span><span class="sxs-lookup"><span data-stu-id="59af9-847">Generating URLs in HTML</span></span>

<span data-ttu-id="59af9-848">`IHtmlHelper` で提供されている `HtmlHelper` メソッドの `Html.BeginForm` および `Html.ActionLink` は、それぞれ `<form>` 要素と `<a>` 要素を生成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-848">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="59af9-849">これらのメソッドは `Url.Action` メソッドを使って URL を生成するので、同じような引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="59af9-849">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="59af9-850">`HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="59af9-850">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="59af9-851">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-851">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="59af9-852">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-852">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="59af9-853">詳しくは、「[フォームの操作](../views/working-with-forms.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-853">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="59af9-854">ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="59af9-854">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="59af9-855">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="59af9-855">Generating URLS in Action Results</span></span>

<span data-ttu-id="59af9-856">上の例ではコントローラーでの `IUrlHelper` の使用が示されていますが、コントローラーでの最も一般的な使用方法はアクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="59af9-856">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="59af9-857">基底クラス `ControllerBase` および `Controller` では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-857">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="59af9-858">一般的な使用法の 1 つは、ユーザー入力を受け付けた後でリダイレクトする場合です。</span><span class="sxs-lookup"><span data-stu-id="59af9-858">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="59af9-859">アクション結果ファクトリ メソッドは、`IUrlHelper` のメソッドに似たパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="59af9-859">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="59af9-860">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="59af9-860">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="59af9-861">規則ルーティングでは、"*専用規則ルート*" と呼ばれる特殊なルート定義を使うことができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-861">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="59af9-862">次の例の `blog` という名前のルートが専用規則ルートです。</span><span class="sxs-lookup"><span data-stu-id="59af9-862">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="59af9-863">これらのルート定義を使うと、`Url.Action("Index", "Home")` は `default` ルートで URL パス `/` を生成します。なぜでしょうか。</span><span class="sxs-lookup"><span data-stu-id="59af9-863">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="59af9-864">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-864">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="59af9-865">専用規則ルートは、URL の生成でルートの "一致範囲が広くなりすぎる" のを防ぐために対応するルート パラメーターを持たない既定値の特別な動作に依存します。</span><span class="sxs-lookup"><span data-stu-id="59af9-865">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="59af9-866">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="59af9-866">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="59af9-867">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-867">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="59af9-868">値 `{ controller = Home, action = Index }` は `{ controller = Blog, action = Article }` と一致しないため、`blog` を使った URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="59af9-868">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="59af9-869">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="59af9-869">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="59af9-870">Areas</span><span class="sxs-lookup"><span data-stu-id="59af9-870">Areas</span></span>

<span data-ttu-id="59af9-871">[区分](areas.md)は MVC の機能であり、関連する機能を独立したルーティング名前空間 (コントローラー アクションの場合) およびフォルダー構造 (ビューの場合) としてグループ化するために使われます。</span><span class="sxs-lookup"><span data-stu-id="59af9-871">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="59af9-872">区分を使うと、アプリケーションは同じ名前の複数のコントローラーを持つことができます (ただし、"*区分*" が異なる場合)。</span><span class="sxs-lookup"><span data-stu-id="59af9-872">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="59af9-873">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-873">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="59af9-874">このセクションでは、ルーティングと区分がどのように相互作用するのかについて説明します。ビューでの区分の使い方について詳しくは、「[区分](areas.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-874">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="59af9-875">次の例では、既定の規則ルートと、`Blog` という名前の区分に対する "*区分ルート*" を使うように、MVC を構成しています。</span><span class="sxs-lookup"><span data-stu-id="59af9-875">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="59af9-876">`/Manage/Users/AddUser` のような URL パスを照合すると、最初のルートではルート値 `{ area = Blog, controller = Users, action = AddUser }` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-876">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="59af9-877">`area` ルート値は、`area` の既定値によって生成されます。実際には、`MapAreaRoute` によって作成されるルートは以下と同等です。</span><span class="sxs-lookup"><span data-stu-id="59af9-877">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="59af9-878">`MapAreaRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="59af9-878">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="59af9-879">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="59af9-879">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="59af9-880">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="59af9-880">Conventional routing is order-dependent.</span></span> <span data-ttu-id="59af9-881">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-881">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="59af9-882">上の例を使うと、ルート値は次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-882">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="59af9-883">`AreaAttribute` はコントローラーが区分の一部であることを示すものであり、このコントローラーは `Blog` 区分に含まれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-883">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="59af9-884">`[Area]` 属性を持たないコントローラーはどの区域のメンバーでもなく、`area` ルート値がルーティングによって提供されたときは一致 **しません**。</span><span class="sxs-lookup"><span data-stu-id="59af9-884">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="59af9-885">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-885">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="59af9-886">ここでは完全を期すために各コントローラーの名前空間を示してあります。そうしないと、コントローラーの名前が競合し、コンパイラ エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="59af9-886">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="59af9-887">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="59af9-887">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="59af9-888">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-888">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="59af9-889">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-889">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="59af9-890">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="59af9-890">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="59af9-891">区分の内部でアクションを実行するとき、`area` のルート値は、URL の生成に使うルーティングの "*アンビエント値*" として利用できます。</span><span class="sxs-lookup"><span data-stu-id="59af9-891">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="59af9-892">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="59af9-892">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="59af9-893">IActionConstraint について</span><span class="sxs-lookup"><span data-stu-id="59af9-893">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="59af9-894">このセクションでは、フレームワークの内部構造と MVC が実行するアクションを選ぶ方法について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="59af9-894">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="59af9-895">一般的なアプリケーションでは、カスタム `IActionConstraint` は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="59af9-895">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="59af9-896">インターフェイスにあまり詳しくない場合でも、既に `IActionConstraint` を使ったことがあるはずです。</span><span class="sxs-lookup"><span data-stu-id="59af9-896">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="59af9-897">`[HttpGet]` 属性およびそれに似た `[Http-VERB]` 属性は、アクション メソッドの実行を制限するために `IActionConstraint` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="59af9-897">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="59af9-898">既定の規則ルートでは、URL パス `/Products/Edit` は値 `{ controller = Products, action = Edit }` を生成し、ここで示す **両方** のアクションと一致するものとします。</span><span class="sxs-lookup"><span data-stu-id="59af9-898">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="59af9-899">`IActionConstraint` ではこれを、両方のアクションが候補と見なされる、といいます。どちらもルート データと一致するからです。</span><span class="sxs-lookup"><span data-stu-id="59af9-899">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="59af9-900">`HttpGetAttribute` は実行すると、*Edit()* は *GET* に対して一致し、他の HTTP 動詞には一致しないことを示します。</span><span class="sxs-lookup"><span data-stu-id="59af9-900">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="59af9-901">`Edit(...)` アクションには制約が定義されていないので、すべての HTTP 動詞と一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-901">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="59af9-902">したがって、`POST` は `Edit(...)` のみと一致します。</span><span class="sxs-lookup"><span data-stu-id="59af9-902">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="59af9-903">一方、`GET` の場合は両方のアクションが一致します。しかし、`IActionConstraint` を指定されているアクションの方が常に、指定されていないアクション "*より適切*" であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="59af9-903">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="59af9-904">したがって、`Edit()` には `[HttpGet]` があるので、より具体的と見なされ、両方のアクションが一致する場合はこちらが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="59af9-904">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="59af9-905">概念的には、`IActionConstraint` は "*オーバーロード*" の形式ですが、同じ名前のメソッドをオーバーロードするのではなく、同じ URL に一致するアクション間でオーバーロードします。</span><span class="sxs-lookup"><span data-stu-id="59af9-905">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="59af9-906">属性ルーティングも `IActionConstraint` を使い、異なるコントローラーのアクションがどちらも候補と見なされる結果になることがあります。</span><span class="sxs-lookup"><span data-stu-id="59af9-906">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="59af9-907">IActionConstraint の実装</span><span class="sxs-lookup"><span data-stu-id="59af9-907">Implementing IActionConstraint</span></span>

<span data-ttu-id="59af9-908">`IActionConstraint` を実装する最も簡単な方法は、`System.Attribute` の派生クラスを作成し、アクションとコントローラーに配置する方法です。</span><span class="sxs-lookup"><span data-stu-id="59af9-908">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="59af9-909">MVC は、属性として適用された `IActionConstraint` を自動的に検出します。</span><span class="sxs-lookup"><span data-stu-id="59af9-909">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="59af9-910">アプリケーション モデルを使って制約を適用することができ、適用方法をメタプログラムできるので、おそらくこれが最も柔軟なアプローチです。</span><span class="sxs-lookup"><span data-stu-id="59af9-910">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="59af9-911">次の例では、制約は、ルートデータから *国コード* に基づいてアクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="59af9-911">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="59af9-912">[GitHub の完全なサンプルはこちら](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59af9-912">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="59af9-913">ユーザーは、`Accept` メソッドを実装し、実行する制約の "順序" を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="59af9-913">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="59af9-914">この例では、`country` ルート値が一致すると、`Accept` メソッドは `true` を返してアクションが一致することを示します。</span><span class="sxs-lookup"><span data-stu-id="59af9-914">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="59af9-915">この動作は、非属性アクションにフォールバックできる `RouteValueAttribute` とは異なります。</span><span class="sxs-lookup"><span data-stu-id="59af9-915">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="59af9-916">サンプルでは、`en-US` アクションを定義すると、`fr-FR` のような国コードは `[CountrySpecific(...)]` が適用されていない、より一般的なコントローラーにフォールバックすることが示されています。</span><span class="sxs-lookup"><span data-stu-id="59af9-916">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="59af9-917">`Order` プロパティは、制約が含まれる "*ステージ*" を決定します。</span><span class="sxs-lookup"><span data-stu-id="59af9-917">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="59af9-918">アクションの制約は、`Order` に基づいてグループで実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-918">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="59af9-919">たとえば、フレームワークで提供されるすべての HTTP メソッド属性は、同じステージで実行されるように、同じ `Order` 値を使います。</span><span class="sxs-lookup"><span data-stu-id="59af9-919">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="59af9-920">目的のポリシーを実装するため、必要なだけいくつでもステージを使うことができます。</span><span class="sxs-lookup"><span data-stu-id="59af9-920">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="59af9-921">`Order` の値を決めるときは、HTTP メソッドより前に制約を適用する必要があるかどうかを考えます。</span><span class="sxs-lookup"><span data-stu-id="59af9-921">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="59af9-922">小さい値が先に実行されます。</span><span class="sxs-lookup"><span data-stu-id="59af9-922">Lower numbers run first.</span></span>

::: moniker-end

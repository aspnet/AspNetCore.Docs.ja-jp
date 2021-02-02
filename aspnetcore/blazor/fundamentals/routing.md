---
title: ASP.NET Core Blazor のルーティング
author: guardrex
description: アプリで要求ルーティングを管理する方法と、Blazor アプリでナビゲーション用に NavLink コンポーネントを使用する方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 74886eae7431045e56de535b5221040bd56cdc2d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751569"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="c8205-103">ASP.NET Core Blazor のルーティング</span><span class="sxs-lookup"><span data-stu-id="c8205-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="c8205-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c8205-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c8205-105">この記事では、要求ルーティングを管理する方法と、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントを使用して Blazor アプリでナビゲーション リンクを作成する方法について学習します。</span><span class="sxs-lookup"><span data-stu-id="c8205-105">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="c8205-106">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="c8205-106">Route templates</span></span>

<span data-ttu-id="c8205-107"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用すると、Blazor アプリで Razor コンポーネントにルーティングできます。</span><span class="sxs-lookup"><span data-stu-id="c8205-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="c8205-108"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは Blazor アプリの `App` コンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-108">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="c8205-109">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-109">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

<span data-ttu-id="c8205-110">[`@page` ディレクティブ](xref:mvc/views/razor#page) を含む Razor コンポーネント (`.razor`) がコンパイルされると、生成されたコンポーネント クラスに、コンポーネントのルート テンプレートを指定する <xref:Microsoft.AspNetCore.Components.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-110">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="c8205-111">アプリが起動すると、Router の `AppAssembly` として指定されたアセンブリがスキャンされ、<xref:Microsoft.AspNetCore.Components.RouteAttribute> を持つアプリのコンポーネントのルート情報が収集されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-111">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="c8205-112">実行時に、<xref:Microsoft.AspNetCore.Components.RouteView> コンポーネントは、</span><span class="sxs-lookup"><span data-stu-id="c8205-112">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="c8205-113"><xref:Microsoft.AspNetCore.Components.Routing.Router> から、ルート パラメーターと共に <xref:Microsoft.AspNetCore.Components.RouteData> を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c8205-113">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="c8205-114">さらに入れ子になったレイアウトを含め、指定されたコンポーネントをその[レイアウト](xref:blazor/layouts)でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="c8205-114">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="c8205-115">必要に応じて、[`@layout` ディレクティブ](xref:blazor/layouts#specify-a-layout-in-a-component) を使用してレイアウトを指定しないコンポーネントに対して、レイアウト クラスで <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="c8205-115">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="c8205-116">フレームワークの Blazor プロジェクト テンプレートでは、アプリの既定のレイアウトとして `MainLayout` コンポーネント (`Shared/MainLayout.razor`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="c8205-116">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="c8205-117">レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8205-117">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="c8205-118">コンポーネントにより、複数の [`@page` ディレクティブ](xref:mvc/views/razor#page) を使用する複数のルート テンプレートがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-118">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="c8205-119">次のコンポーネントの例では、`/BlazorRoute` と `/DifferentBlazorRoute` に対する要求を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c8205-119">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="c8205-120">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-120">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="c8205-121">URL が正しく解決されるように、アプリでは、`href` 属性に指定されているアプリのベース パスを使用して、その `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) に `<base>` タグを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8205-121">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="c8205-122">詳細については、「<xref:blazor/host-and-deploy/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8205-122">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="c8205-123">コンテンツが見つからないときにカスタム コンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="c8205-123">Provide custom content when content isn't found</span></span>

<span data-ttu-id="c8205-124"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用すると、要求されたルートでコンテンツが見つからない場合に、アプリでカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c8205-124">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="c8205-125">`App` コンポーネントで、<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントの <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> テンプレートにカスタム コンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="c8205-125">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="c8205-126">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-126">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="c8205-127">他の対話型コンポーネントなど、`<NotFound>` タグのコンテンツとして任意の項目がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-127">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="c8205-128"><xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts#default-layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8205-128">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="c8205-129">複数のアセンブリからコンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="c8205-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="c8205-130"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> パラメーターを使用して、<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントで、ルーティング可能なコンポーネントを検索するときに考慮する追加のアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="c8205-130">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="c8205-131">`AppAssembly` に指定されたアセンブリに加え、追加のアセンブリがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-131">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="c8205-132">次の例では、`Component1` は、参照されている[コンポーネント クラス ライブラリ](xref:blazor/components/class-libraries)に定義されているルーティング可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="c8205-132">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="c8205-133">次の <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> の例は、`Component1` のルーティング サポートの結果を示しています。</span><span class="sxs-lookup"><span data-stu-id="c8205-133">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="c8205-134">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-134">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="c8205-135">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="c8205-135">Route parameters</span></span>

<span data-ttu-id="c8205-136">ルーターでルート パラメーターを使用すれば、同じ名前の対応する[コンポーネント パラメーター](xref:blazor/components/index#component-parameters)を設定できます。</span><span class="sxs-lookup"><span data-stu-id="c8205-136">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="c8205-137">ルート パラメーター名では大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="c8205-137">Route parameter names are case insensitive.</span></span> <span data-ttu-id="c8205-138">次の例では、`text` パラメーターを使用して、ルート セグメントの値をコンポーネントの `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c8205-138">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="c8205-139">`/RouteParameter/amazing` に対して要求が行われると、`<h1>` タグのコンテンツは `Blazor is amazing!` としてレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-139">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="c8205-140">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-140">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c8205-141">省略可能なパラメーターがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c8205-141">Optional parameters are supported.</span></span> <span data-ttu-id="c8205-142">次の例では、省略可能なパラメーター `text` を使用して、ルート セグメントの値をコンポーネントの `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c8205-142">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="c8205-143">セグメントが存在しない場合、`Text` の値は `fantastic` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-143">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="c8205-144">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-144">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c8205-145">省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c8205-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="c8205-146">以下の例では、2 つの [`@page` ディレクティブ](xref:mvc/views/razor#page)が適用されています。</span><span class="sxs-lookup"><span data-stu-id="c8205-146">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="c8205-147">1 つ目のディレクティブでは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="c8205-147">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c8205-148">2 つ目のディレクティブでは、`{text}` ルート パラメーターの値をコンポーネントの `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c8205-148">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="c8205-149">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-149">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="c8205-150">オプションのパラメーター値が異なる同じコンポーネントへのアプリの移動を許可するには、[`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) ではなく、[`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) を使用します。</span><span class="sxs-lookup"><span data-stu-id="c8205-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="c8205-151">前の例に基づいて、ユーザーが `/RouteParameter` から `/RouteParameter/amazing` に、または `/RouteParameter/amazing` から `/RouteParameter` に移動できる必要がある場合は、`OnParametersSet` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="c8205-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="c8205-152">ルート制約</span><span class="sxs-lookup"><span data-stu-id="c8205-152">Route constraints</span></span>

<span data-ttu-id="c8205-153">ルート制約は、コンポーネントへのルート セグメントに型の一致を適用します。</span><span class="sxs-lookup"><span data-stu-id="c8205-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="c8205-154">次の例で、`User` コンポーネントへのルートは、次の場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="c8205-154">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="c8205-155">要求 URL に `Id` ルート セグメントが存在する。</span><span class="sxs-lookup"><span data-stu-id="c8205-155">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="c8205-156">`Id` セグメントが整数 (`int`) 型である。</span><span class="sxs-lookup"><span data-stu-id="c8205-156">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="c8205-157">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-157">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="c8205-158">次の表に示すルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c8205-158">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="c8205-159">インバリアント カルチャと一致するルート制約については、表の下の警告で詳細をご確認ください。</span><span class="sxs-lookup"><span data-stu-id="c8205-159">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="c8205-160">制約</span><span class="sxs-lookup"><span data-stu-id="c8205-160">Constraint</span></span> | <span data-ttu-id="c8205-161">例</span><span class="sxs-lookup"><span data-stu-id="c8205-161">Example</span></span>           | <span data-ttu-id="c8205-162">一致の例</span><span class="sxs-lookup"><span data-stu-id="c8205-162">Example Matches</span></span>                                                                  | <span data-ttu-id="c8205-163">インバリアント</span><span class="sxs-lookup"><span data-stu-id="c8205-163">Invariant</span></span><br><span data-ttu-id="c8205-164">カルチャ</span><span class="sxs-lookup"><span data-stu-id="c8205-164">culture</span></span><br><span data-ttu-id="c8205-165">一致</span><span class="sxs-lookup"><span data-stu-id="c8205-165">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="c8205-166">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="c8205-166">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="c8205-167">いいえ</span><span class="sxs-lookup"><span data-stu-id="c8205-167">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="c8205-168">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="c8205-168">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="c8205-169">はい</span><span class="sxs-lookup"><span data-stu-id="c8205-169">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="c8205-170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="c8205-170">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="c8205-171">はい</span><span class="sxs-lookup"><span data-stu-id="c8205-171">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="c8205-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="c8205-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="c8205-173">はい</span><span class="sxs-lookup"><span data-stu-id="c8205-173">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="c8205-174">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="c8205-174">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="c8205-175">はい</span><span class="sxs-lookup"><span data-stu-id="c8205-175">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="c8205-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="c8205-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="c8205-177">いいえ</span><span class="sxs-lookup"><span data-stu-id="c8205-177">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="c8205-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="c8205-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="c8205-179">はい</span><span class="sxs-lookup"><span data-stu-id="c8205-179">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="c8205-180">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="c8205-180">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="c8205-181">はい</span><span class="sxs-lookup"><span data-stu-id="c8205-181">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="c8205-182">URL の妥当性を検証し、CLR 型 (`int` や <xref:System.DateTime> など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-182">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="c8205-183">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="c8205-183">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="c8205-184">ドットを含む URL によるルーティング</span><span class="sxs-lookup"><span data-stu-id="c8205-184">Routing with URLs that contain dots</span></span>

<span data-ttu-id="c8205-185">ホストされている Blazor WebAssembly および Blazor Server アプリの場合は、サーバー側の既定のルート テンプレートで、ファイルが要求されているドット (`.`) が要求 URL の最後のセグメントに含まれていると想定されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-185">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="c8205-186">たとえば、URL `https://localhost.com:5001/example/some.thing` は、ルーターによって `some.thing` という名前のファイルに対する要求として解釈されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-186">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="c8205-187">追加の構成がないと、`some.thing` が [`@page` ディレクティブ](xref:mvc/views/razor#page) を含むコンポーネントにルーティングすることを意図しており、`some.thing` がルート パラメーター値である場合に、アプリによって "*404 - 見つかりません*" という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-187">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="c8205-188">ドットが含まれる 1 つまたは複数のパラメーターを指定してルートを使用するには、アプリでカスタム テンプレートを使ってルートを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8205-188">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="c8205-189">URL の最後のセグメントからルート パラメーターを受け取ることができる次の `Example` コンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c8205-189">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="c8205-190">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-190">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="c8205-191">ホストされている Blazor WebAssembly ソリューションの *`Server`* アプリで、`param` ルート パラメーターのドットを使って要求をルーティングできるようにするには、`Startup.Configure` で省略可能なパラメーターを指定してフォールバック ファイル ルート テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8205-191">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="c8205-192">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="c8205-192">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="c8205-193">`param` ルート パラメーターのドットを使って要求をルーティングするように Blazor Server アプリを構成するには、`Startup.Configure` で省略可能なパラメーターを指定してフォールバック ページ ルート テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8205-193">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="c8205-194">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="c8205-194">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="c8205-195">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8205-195">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="c8205-196">キャッチオールのルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="c8205-196">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c8205-197">複数のフォルダー境界にまたがるパスをキャプチャするキャッチオール ルート パラメーターが、コンポーネントでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-197">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="c8205-198">キャッチオールのルート パラメーターは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c8205-198">Catch-all route parameters are:</span></span>

* <span data-ttu-id="c8205-199">ルート セグメント名と一致する名前が付けられている。</span><span class="sxs-lookup"><span data-stu-id="c8205-199">Named to match the route segment name.</span></span> <span data-ttu-id="c8205-200">名前付けで大文字と小文字は区別されない。</span><span class="sxs-lookup"><span data-stu-id="c8205-200">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="c8205-201">`string` 型。</span><span class="sxs-lookup"><span data-stu-id="c8205-201">A `string` type.</span></span> <span data-ttu-id="c8205-202">フレームワークによって自動キャストは提供されません。</span><span class="sxs-lookup"><span data-stu-id="c8205-202">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="c8205-203">URL の末尾。</span><span class="sxs-lookup"><span data-stu-id="c8205-203">At the end of the URL.</span></span>

<span data-ttu-id="c8205-204">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-204">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

<span data-ttu-id="c8205-205">ルート テンプレートが `/catch-all/{*pageRoute}` の URL `/catch-all/this/is/a/test` の場合、`PageRoute` の値は `this/is/a/test` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-205">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="c8205-206">キャプチャされたパスのスラッシュとセグメントはデコードされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-206">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="c8205-207">`/catch-all/{*pageRoute}` のルート テンプレートでは、URL `/catch-all/this/is/a%2Ftest%2A` から `this/is/a/test*` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-207">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c8205-208">キャッチオール ルート パラメーターは、ASP.NET Core 5.0 以降でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-208">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="c8205-209">詳細については、この記事の 5.0 バージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="c8205-209">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="c8205-210">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="c8205-210">URI and navigation state helpers</span></span>

<span data-ttu-id="c8205-211">C# コード内の URI とナビゲーションを管理するには、<xref:Microsoft.AspNetCore.Components.NavigationManager> を使用します。</span><span class="sxs-lookup"><span data-stu-id="c8205-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="c8205-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> には、次の表に示すイベントとメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="c8205-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="c8205-213">メンバー</span><span class="sxs-lookup"><span data-stu-id="c8205-213">Member</span></span> | <span data-ttu-id="c8205-214">説明</span><span class="sxs-lookup"><span data-stu-id="c8205-214">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="c8205-215">現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="c8205-215">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="c8205-216">絶対 URI を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="c8205-216">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="c8205-217">通常、<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> は `wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) 内のドキュメントの `<base>` 要素の `href` 属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="c8205-217">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="c8205-218">指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="c8205-218">Navigates to the specified URI.</span></span> <span data-ttu-id="c8205-219">`forceLoad` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="c8205-219">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="c8205-220">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-220">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="c8205-221">URI が通常クライアント側ルーターによって処理されるかどうかにかかわらず、ブラウザーでは、強制的にサーバーから新しいページが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c8205-221">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="c8205-222">ナビゲーションの場所が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="c8205-222">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="c8205-223">相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="c8205-223">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="c8205-224">ベース URI (たとえば、<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> によって以前に返された URI) が与えられると、絶対 URI を、ベース URI プレフィックスに相対的な URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="c8205-224">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="c8205-225"><xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> イベントの場合、<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> でナビゲーション イベントに関する次の情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-225">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="c8205-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>:新しい場所の URL。</span><span class="sxs-lookup"><span data-stu-id="c8205-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="c8205-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>:`true` の場合、Blazor によってブラウザーからナビゲーションがインターセプトされました。</span><span class="sxs-lookup"><span data-stu-id="c8205-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="c8205-228">`false` の場合、<xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> によってナビゲーションが発生しました。</span><span class="sxs-lookup"><span data-stu-id="c8205-228">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="c8205-229">次のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c8205-229">The following component:</span></span>

* <span data-ttu-id="c8205-230"><xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> を使用してボタンが選択されたときに、アプリの `Counter` コンポーネント (`Pages/Counter.razor`) に移動します。</span><span class="sxs-lookup"><span data-stu-id="c8205-230">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="c8205-231"><xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> をサブスクライブすることで、場所の変更イベントを処理します。</span><span class="sxs-lookup"><span data-stu-id="c8205-231">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="c8205-232">`HandleLocationChanged` メソッドは、`Dispose` がフレームワークによって呼び出されると、アンフックになります。</span><span class="sxs-lookup"><span data-stu-id="c8205-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="c8205-233">このメソッドをアンフックすることで、コンポーネントのガベージ コレクションが許可されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-233">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="c8205-234">ロガーの実装では、ボタンが選択されたときに次の情報をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="c8205-234">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="c8205-235">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-235">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

<span data-ttu-id="c8205-236">コンポーネントの破棄の詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8205-236">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="c8205-237">クエリ文字列とパラメーターの解析</span><span class="sxs-lookup"><span data-stu-id="c8205-237">Query string and parse parameters</span></span>

<span data-ttu-id="c8205-238">要求のクエリ文字列は、<xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-238">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="c8205-239">クエリ文字列のパラメーターを解析するには:</span><span class="sxs-lookup"><span data-stu-id="c8205-239">To parse a query string's parameters:</span></span>

* <span data-ttu-id="c8205-240">アプリでは <xref:Microsoft.AspNetCore.WebUtilities> API を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c8205-240">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="c8205-241">アプリで API を使用できない場合は、[Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) のアプリのプロジェクト ファイルにパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="c8205-241">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="c8205-242"><xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> を使用してクエリ文字列を解析した後に値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c8205-242">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="c8205-243">次の `ParseQueryString` コンポーネントの例では、`ship` という名前のクエリ文字列パラメーターのキーを解析します。</span><span class="sxs-lookup"><span data-stu-id="c8205-243">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="c8205-244">たとえば、URL クエリ文字列のキーと値のペアの `?ship=Tardis` の場合、`queryValue` の `Tardis` 値が取り込まれます。</span><span class="sxs-lookup"><span data-stu-id="c8205-244">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="c8205-245">次の例では、`https://localhost:5001/parse-query-string?ship=Tardis` という URL を使用してアプリに移動します。</span><span class="sxs-lookup"><span data-stu-id="c8205-245">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="c8205-246">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="c8205-246">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="c8205-247">`NavLink` および `NavMenu` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c8205-247">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="c8205-248">ナビゲーション リンクを作成するときは、HTML ハイパーリンク要素 (`<a>`) の代わりに <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="c8205-248">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="c8205-249"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="c8205-249">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="c8205-250">`active` クラスは、表示されているナビゲーション リンクの中でどのページがアクティブ ページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c8205-250">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="c8205-251">必要に応じて、CSS クラス名を <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> に割り当てて、現在のルートが `href` と一致したときに、レンダリングされるリンクにカスタム CSS クラスを適用します。</span><span class="sxs-lookup"><span data-stu-id="c8205-251">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="c8205-252">次の `NavMenu` コンポーネントでは、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントの使用方法を示す [`Bootstrap`](https://getbootstrap.com/docs/) ナビゲーション バーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="c8205-252">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="c8205-253">`NavMenu` コンポーネント (`NavMenu.razor`) は、Blazor プロジェクト テンプレートから生成されたアプリの `Shared` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="c8205-253">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="c8205-254">`<NavLink>` 要素の `Match` 属性に割り当てられる 2 つの <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="c8205-254">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="c8205-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>:<xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、現在の URL 全体に一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="c8205-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="c8205-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*既定値*):<xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、現在の URL の任意のプレフィックスに一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="c8205-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="c8205-257">前の例では、ホーム <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` はホーム URL と一致し、アプリの既定のベース パス URL (`https://localhost:5001/` など) でのみ `active` CSS クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c8205-257">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="c8205-258">2 番目の <xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、ユーザーが `component` プレフィックスを含む任意の URL (`https://localhost:5001/component` や `https://localhost:5001/component/another-segment` など) にアクセスしたときに、`active` クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c8205-258">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="c8205-259">追加の <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネント属性は、レンダリングされるアンカー タグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-259">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="c8205-260">次の例では、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントに `target` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c8205-260">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="c8205-261">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="c8205-261">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="c8205-262">Blazor による子コンテンツのレンダリング方法により、`for` ループ内の `NavLink` コンポーネントのレンダリングでは、インクリメントするループ変数が `NavLink` (子) コンポーネントのコンテンツ内で使用されている場合、ローカル インデックス変数が必要になります。</span><span class="sxs-lookup"><span data-stu-id="c8205-262">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="c8205-263">このシナリオでのインデックス変数の使用は、`NavLink` コンポーネントだけでなく、[子コンテンツ](xref:blazor/components/index#child-content)でループ変数を使用する **すべての** 子コンポーネントで必須です。</span><span class="sxs-lookup"><span data-stu-id="c8205-263">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="c8205-264">または、<xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> と共に `foreach` ループを使用します。</span><span class="sxs-lookup"><span data-stu-id="c8205-264">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="c8205-265">ASP.NET Core エンドポイントのルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="c8205-265">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="c8205-266">"*このセクションは Blazor Server アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="c8205-266">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="c8205-267">Blazor Server は [ASP.NET Core エンドポイントのルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="c8205-267">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="c8205-268">ASP.NET Core アプリは、`Startup.Configure` で <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> を使用して、対話型コンポーネントの着信接続を受け入れるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="c8205-268">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="c8205-269">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="c8205-269">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="c8205-270">一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor Server アプリのサーバー側部分のホストとして機能します。</span><span class="sxs-lookup"><span data-stu-id="c8205-270">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="c8205-271">慣例により、"*ホスト*" のページは通常、アプリの `Pages` フォルダーでは `_Host.cshtml` という名前になります。</span><span class="sxs-lookup"><span data-stu-id="c8205-271">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="c8205-272">ホスト ファイルに指定されるルートは、ルート照合で低い優先順位で動作するため、*フォールバック ルート* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c8205-272">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="c8205-273">フォールバック ルートは、他のルートが一致しない場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c8205-273">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="c8205-274">これにより、Blazor Server アプリのコンポーネント ルーティングに干渉することなく、他のコントローラーやページをアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c8205-274">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="c8205-275">ルート以外の URL のサーバー ホスト用に <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> を構成する方法の詳細については、<xref:blazor/host-and-deploy/index#app-base-path> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c8205-275">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

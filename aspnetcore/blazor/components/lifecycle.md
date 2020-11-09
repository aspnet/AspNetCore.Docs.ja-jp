---
title: 'ASP.NET Core Blazor ライフサイクル'
author: guardrex
description: 'ASP.NET Core Blazor アプリで Razor コンポーネント ライフサイクル メソッドを使用する方法について学習します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/14/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 30dfd9e821490d016d1d2be5c4cfd56818d46655
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056375"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="65556-103">ASP.NET Core Blazor ライフサイクル</span><span class="sxs-lookup"><span data-stu-id="65556-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="65556-104">著者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="65556-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="65556-105">Blazor フレームワークには、同期と非同期のライフサイクル メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="65556-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="65556-106">コンポーネントの初期化およびレンダリング中にコンポーネントで追加の操作を実行するには、ライフサイクル メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65556-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="65556-107">次の図は、Blazor のライフサイクルを示しています。</span><span class="sxs-lookup"><span data-stu-id="65556-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="65556-108">この記事の後続のセクションで、例を示しながらライフサイクル メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="65556-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="65556-109">コンポーネント ライフサイクル イベント:</span><span class="sxs-lookup"><span data-stu-id="65556-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="65556-110">要求に対してコンポーネントが初めてレンダリングされる場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="65556-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="65556-111">コンポーネントのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="65556-111">Create the component's instance.</span></span>
   * <span data-ttu-id="65556-112">プロパティの挿入を実行します。</span><span class="sxs-lookup"><span data-stu-id="65556-112">Perform property injection.</span></span> <span data-ttu-id="65556-113">[`SetParametersAsync`](#before-parameters-are-set) を実行します。</span><span class="sxs-lookup"><span data-stu-id="65556-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="65556-114">[`OnInitialized{Async}`](#component-initialization-methods) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65556-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="65556-115"><xref:System.Threading.Tasks.Task> が返された場合、<xref:System.Threading.Tasks.Task> を待機してから、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="65556-116"><xref:System.Threading.Tasks.Task> が返されない場合は、コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="65556-116">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>
1. <span data-ttu-id="65556-117">[`OnParametersSet{Async}`](#after-parameters-are-set) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65556-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set).</span></span> <span data-ttu-id="65556-118"><xref:System.Threading.Tasks.Task> が返された場合、<xref:System.Threading.Tasks.Task> を待機してから、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-118">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="65556-119"><xref:System.Threading.Tasks.Task> が返されない場合は、コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="65556-119">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>

![Blazor の Razor コンポーネントのコンポーネント ライフサイクル イベント](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="65556-121">ドキュメント オブジェクト モデル (DOM) イベント処理:</span><span class="sxs-lookup"><span data-stu-id="65556-121">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="65556-122">イベント ハンドラーが実行されます。</span><span class="sxs-lookup"><span data-stu-id="65556-122">The event handler is run.</span></span>
1. <span data-ttu-id="65556-123"><xref:System.Threading.Tasks.Task> が返された場合、<xref:System.Threading.Tasks.Task> を待機してから、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-123">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="65556-124"><xref:System.Threading.Tasks.Task> が返されない場合は、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-124">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![ドキュメント オブジェクト モデル (DOM) イベント処理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="65556-126">`Render` のライフサイクル:</span><span class="sxs-lookup"><span data-stu-id="65556-126">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="65556-127">これがコンポーネントの最初のレンダリングでない場合、または [`ShouldRender`](#suppress-ui-refreshing) が `false` として評価された場合は、コンポーネントに対して他の操作を実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="65556-127">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="65556-128">レンダリング ツリーの差分を作成し、コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="65556-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="65556-129">DOM が更新されるのを待機します。</span><span class="sxs-lookup"><span data-stu-id="65556-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="65556-130">[`OnAfterRender{Async}`](#after-component-render) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65556-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Render ライフサイクル](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="65556-132">Developer によって [`StateHasChanged`](#state-changes) の呼び出しが行われると、結果としてレンダリングが実行されます。</span><span class="sxs-lookup"><span data-stu-id="65556-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="65556-133">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="65556-133">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="65556-134">パラメーターが設定される前</span><span class="sxs-lookup"><span data-stu-id="65556-134">Before parameters are set</span></span>

<span data-ttu-id="65556-135"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> は、レンダリング ツリーのコンポーネントの親によって指定されたパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="65556-135"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="65556-136"><xref:Microsoft.AspNetCore.Components.ParameterView> には、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> が呼び出されるたびに、コンポーネントのパラメーター値のセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="65556-136"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="65556-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> の既定の実装では、対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にある [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) または [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 属性を使用して、各プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="65556-137">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="65556-138">対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にないパラメーターは、変更されないままになります。</span><span class="sxs-lookup"><span data-stu-id="65556-138">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="65556-139">[`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) が呼び出されない場合、カスタム コードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="65556-139">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="65556-140">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="65556-140">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="65556-141">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="65556-141">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="65556-142">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-142">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="65556-143">コンポーネントの初期化メソッド</span><span class="sxs-lookup"><span data-stu-id="65556-143">Component initialization methods</span></span>

<span data-ttu-id="65556-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> は、コンポーネントが、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> でその親コンポーネントから初期パラメーターを受け取った後で初期化されるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65556-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="65556-145">コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> を使用します。</span><span class="sxs-lookup"><span data-stu-id="65556-145">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="65556-146">同期操作の場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65556-146">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="65556-147">非同期操作を実行するには、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> をオーバーライドし、操作で [`await`](/dotnet/csharp/language-reference/operators/await) 演算子を使用します。</span><span class="sxs-lookup"><span data-stu-id="65556-147">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="65556-148">[コンテンツをプリレンダリングする](xref:blazor/fundamentals/additional-scenarios#render-mode)Blazor Server アプリは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> を " **_2 回_** " 呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65556-148">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_** :</span></span>

* <span data-ttu-id="65556-149">コンポーネントが最初にページの一部として静的にレンダリングされるときに 1 回。</span><span class="sxs-lookup"><span data-stu-id="65556-149">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="65556-150">ブラウザーがサーバーへの接続を確立するときに 2 回目。</span><span class="sxs-lookup"><span data-stu-id="65556-150">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="65556-151"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 内で開発者コードが 2 回実行されないようにするには、「[プリレンダリング後のステートフル再接続](#stateful-reconnection-after-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-151">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="65556-152">Blazor Server アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="65556-152">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="65556-153">コンポーネントは、プリレンダリング時に異なるレンダリングが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65556-153">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="65556-154">詳細については、「[アプリがプリレンダリングされていることを検出する](#detect-when-the-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-154">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="65556-155">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="65556-155">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="65556-156">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-156">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="65556-157">パラメーターが設定された後</span><span class="sxs-lookup"><span data-stu-id="65556-157">After parameters are set</span></span>

<span data-ttu-id="65556-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> または <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65556-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="65556-159">コンポーネントが <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> または <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> で初期化された後。</span><span class="sxs-lookup"><span data-stu-id="65556-159">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="65556-160">親コンポーネントが再レンダリングし、次のものを提供するとき:</span><span class="sxs-lookup"><span data-stu-id="65556-160">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="65556-161">少なくとも 1 つのパラメーターが変更された既知のプリミティブ不変型のみ。</span><span class="sxs-lookup"><span data-stu-id="65556-161">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="65556-162">任意の複合型のパラメーター。</span><span class="sxs-lookup"><span data-stu-id="65556-162">Any complex-typed parameters.</span></span> <span data-ttu-id="65556-163">フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーター セットは変更済みとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="65556-163">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="65556-164">パラメーターとプロパティ値を適用するときの非同期処理は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ライフサイクル イベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65556-164">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="65556-165">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="65556-165">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="65556-166">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-166">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="65556-167">コンポーネントのレンダリング後</span><span class="sxs-lookup"><span data-stu-id="65556-167">After component render</span></span>

<span data-ttu-id="65556-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65556-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="65556-169">この時点で、要素およびコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="65556-169">Element and component references are populated at this point.</span></span> <span data-ttu-id="65556-170">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を行うには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="65556-170">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="65556-171"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> の `firstRender` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="65556-171">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="65556-172">コンポーネント インスタンスを初めて表示するときに `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="65556-172">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="65556-173">初期化作業が確実に 1 回だけ実行されるように使用できます。</span><span class="sxs-lookup"><span data-stu-id="65556-173">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="65556-174"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ライフサイクル イベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65556-174">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="65556-175"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークでは、そのタスクが完了しても、コンポーネントに対してさらにレンダリング サイクルがスケジュールされることはありません。</span><span class="sxs-lookup"><span data-stu-id="65556-175">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="65556-176">これは、無限のレンダリング ループを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="65556-176">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="65556-177">返されたタスクが完了すると、さらにレンダリング サイクルをスケジュールする他のライフサイクル メソッドとは異なります。</span><span class="sxs-lookup"><span data-stu-id="65556-177">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="65556-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> " *はサーバーでのプリレンダリング プロセス中には呼び出されません* "。</span><span class="sxs-lookup"><span data-stu-id="65556-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="65556-179">メソッドは、プリレンダリングが完了した後にコンポーネントが対話形式でレンダリングされるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65556-179">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="65556-180">次の場合に、アプリによりプリレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-180">When the app prerenders:</span></span>

1. <span data-ttu-id="65556-181">コンポーネントがサーバー上で実行され、HTTP 応答でいくつかの静的 HTML マークアップが生成される。</span><span class="sxs-lookup"><span data-stu-id="65556-181">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="65556-182">このフェーズでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> は呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="65556-182">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="65556-183">ブラウザーで `blazor.server.js` または `blazor.webassembly.js` が起動すると、コンポーネントが対話型のレンダリング モードで再起動される。</span><span class="sxs-lookup"><span data-stu-id="65556-183">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="65556-184">コンポーネントが再起動されると、アプリはプリレンダリング フェーズでなくなるため、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **が呼び出されます** 。</span><span class="sxs-lookup"><span data-stu-id="65556-184">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="65556-185">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="65556-185">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="65556-186">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-186">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="65556-187">UI 更新の抑制</span><span class="sxs-lookup"><span data-stu-id="65556-187">Suppress UI refreshing</span></span>

<span data-ttu-id="65556-188"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> をオーバーライドして、UI の更新を抑制します。</span><span class="sxs-lookup"><span data-stu-id="65556-188">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="65556-189">実装によって `true` が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="65556-189">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="65556-190"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> は、コンポーネントがレンダリングされるたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65556-190"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="65556-191"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> がオーバーライドされる場合でも、コンポーネントは常に最初にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-191">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="65556-192">詳細については、「<xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-192">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="65556-193">状態変更</span><span class="sxs-lookup"><span data-stu-id="65556-193">State changes</span></span>

<span data-ttu-id="65556-194"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、状態が変更されたことをコンポーネントに通知します。</span><span class="sxs-lookup"><span data-stu-id="65556-194"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="65556-195">必要に応じて、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すと、コンポーネントが再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-195">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="65556-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、<xref:Microsoft.AspNetCore.Components.EventCallback> メソッドに対して自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65556-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="65556-197">詳細については、「<xref:blazor/components/event-handling#eventcallback>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-197">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="65556-198">レンダリング時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="65556-198">Handle incomplete async actions at render</span></span>

<span data-ttu-id="65556-199">ライフサイクル イベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65556-199">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="65556-200">ライフサイクル メソッドの実行中に、オブジェクトが `null` またはデータが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65556-200">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="65556-201">オブジェクトが初期化されていることを確認するレンダリング ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="65556-201">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="65556-202">オブジェクトが `null` の間、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="65556-202">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="65556-203">Blazor テンプレートの `FetchData` コンポーネントでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> はオーバーライドされ、予測データを非同期に受信します (`forecasts`)。</span><span class="sxs-lookup"><span data-stu-id="65556-203">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="65556-204">`forecasts` が `null` の場合、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="65556-204">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="65556-205"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> によって返された `Task` が完了すると、コンポーネントは更新された状態で再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-205">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="65556-206">Blazor Server テンプレートの `Pages/FetchData.razor` は以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="65556-206">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="65556-207">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="65556-207">Handle errors</span></span>

<span data-ttu-id="65556-208">ライフサイクル メソッド実行中のエラー処理の詳細については、「<xref:blazor/fundamentals/handle-errors#lifecycle-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-208">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="65556-209">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="65556-209">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="65556-210">Blazor Server アプリで <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> が <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> の場合、コンポーネントは最初にページの一部として静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="65556-210">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="65556-211">ブラウザーがサーバーへの接続を確立すると、コンポーネントが " *再度* " レンダリングされ、コンポーネントがやりとりできるようになります。</span><span class="sxs-lookup"><span data-stu-id="65556-211">Once the browser establishes a connection back to the server, the component is rendered *again* , and the component is now interactive.</span></span> <span data-ttu-id="65556-212">コンポーネントを初期化するための [`OnInitialized{Async}`](#component-initialization-methods) ライフサイクル メソッドが存在する場合、メソッドは " *2 回* " 実行されます。</span><span class="sxs-lookup"><span data-stu-id="65556-212">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice* :</span></span>

* <span data-ttu-id="65556-213">コンポーネントが静的にプリレンダリングされたとき。</span><span class="sxs-lookup"><span data-stu-id="65556-213">When the component is prerendered statically.</span></span>
* <span data-ttu-id="65556-214">サーバー接続が確立された後。</span><span class="sxs-lookup"><span data-stu-id="65556-214">After the server connection has been established.</span></span>

<span data-ttu-id="65556-215">これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変わる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65556-215">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="65556-216">Blazor Server アプリ内の二重レンダリングのシナリオを回避するには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="65556-216">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="65556-217">プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。</span><span class="sxs-lookup"><span data-stu-id="65556-217">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="65556-218">識別子をプリレンダリング中に使用して、コンポーネントの状態を保存します。</span><span class="sxs-lookup"><span data-stu-id="65556-218">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="65556-219">識別子をプリレンダリング後に使用して、キャッシュされた状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="65556-219">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="65556-220">次のコードは、二重レンダリングを回避するテンプレートベースの Blazor Server アプリ内で更新される `WeatherForecastService` を示しています。</span><span class="sxs-lookup"><span data-stu-id="65556-220">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="65556-221"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> の詳細については、「<xref:blazor/fundamentals/additional-scenarios#render-mode>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65556-221">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="65556-222">アプリがプリレンダリングされていることを検出する</span><span class="sxs-lookup"><span data-stu-id="65556-222">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="65556-223">IDisposable を使用したコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="65556-223">Component disposal with IDisposable</span></span>

<span data-ttu-id="65556-224">コンポーネントが <xref:System.IDisposable> を実装している場合は、コンポーネントが UI から削除されると、[`Dispose` メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65556-224">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="65556-225">破棄は、[コンポーネントの初期化](#component-initialization-methods)中など、いつでも実行できます。</span><span class="sxs-lookup"><span data-stu-id="65556-225">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="65556-226">次のコンポーネントでは、`@implements IDisposable` および `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="65556-226">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="65556-227">`Dispose` では、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="65556-227">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="65556-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="65556-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="65556-229">.NET イベントからイベント ハンドラーのサブスクライブを解除します。</span><span class="sxs-lookup"><span data-stu-id="65556-229">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="65556-230">次の [Blazor フォーム](xref:blazor/forms-validation)の例は、`Dispose` メソッドでイベント ハンドラーをアンフックする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="65556-230">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="65556-231">プライベート フィールドとラムダのアプローチ</span><span class="sxs-lookup"><span data-stu-id="65556-231">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="65556-232">プライベート メソッドのアプローチ</span><span class="sxs-lookup"><span data-stu-id="65556-232">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="65556-233">取り消し可能なバックグラウンド作業</span><span class="sxs-lookup"><span data-stu-id="65556-233">Cancelable background work</span></span>

<span data-ttu-id="65556-234">ネットワーク呼び出し (<xref:System.Net.Http.HttpClient>) の実行やデータベースとの対話など、コンポーネントによって実行時間の長いバックグラウンド作業が実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="65556-234">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="65556-235">いくつかの状況でシステム リソースを節約するために、バックグラウンド作業を停止することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="65556-235">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="65556-236">たとえば、ユーザーがコンポーネントの操作を止めても、バックグラウンドの非同期操作は自動的に停止しません。</span><span class="sxs-lookup"><span data-stu-id="65556-236">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="65556-237">バックグラウンド作業項目の取り消しが必要になるその他の理由には、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="65556-237">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="65556-238">実行中のバックグラウンド タスクは、不完全な入力データまたは処理パラメーターを使用して開始されました。</span><span class="sxs-lookup"><span data-stu-id="65556-238">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="65556-239">現在実行中のバックグラウンド作業項目のセットを、新しい作業項目のセットに置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="65556-239">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="65556-240">現在実行中のタスクの優先度を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65556-240">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="65556-241">アプリをサーバーに再展開するには、シャットダウンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="65556-241">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="65556-242">サーバー リソースが制限され、バックグラウンド作業項目の再スケジュールが必要になりました。</span><span class="sxs-lookup"><span data-stu-id="65556-242">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="65556-243">コンポーネントに取り消し可能なバックグラウンド作業パターンを実装するには:</span><span class="sxs-lookup"><span data-stu-id="65556-243">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="65556-244"><xref:System.Threading.CancellationTokenSource> と <xref:System.Threading.CancellationToken> を使用します。</span><span class="sxs-lookup"><span data-stu-id="65556-244">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="65556-245">[コンポーネントの破棄](#component-disposal-with-idisposable)時と、任意の時点で手動でトークンを取り消すことで取り消しが望まれた場合は、[`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) を呼び出して、バックグラウンド作業を取り消す必要があることを通知します。</span><span class="sxs-lookup"><span data-stu-id="65556-245">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="65556-246">非同期呼び出しが返された後、トークンに対して <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65556-246">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="65556-247">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="65556-247">In the following example:</span></span>

* <span data-ttu-id="65556-248">`await Task.Delay(5000, cts.Token);` は、実行時間が長い非同期のバックグラウンド作業を表します。</span><span class="sxs-lookup"><span data-stu-id="65556-248">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="65556-249">`BackgroundResourceMethod` は、メソッドが呼び出される前に `Resource` が破棄された場合に開始されない、実行時間が長いバックグラウンド メソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="65556-249">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

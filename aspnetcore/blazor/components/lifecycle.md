---
title: ASP.NET Core Blazor ライフサイクル
author: guardrex
description: ASP.NET Core Blazor アプリで Razor コンポーネント ライフサイクル メソッドを使用する方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: e5f9a07db742ce2e26f03c0b6e1caa1904e4e0d9
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058234"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="bbc77-103">ASP.NET Core Blazor ライフサイクル</span><span class="sxs-lookup"><span data-stu-id="bbc77-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="bbc77-104">著者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bbc77-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="bbc77-105">Blazor フレームワークには、同期と非同期のライフサイクル メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="bbc77-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="bbc77-106">コンポーネントの初期化およびレンダリング中にコンポーネントで追加の操作を実行するには、ライフサイクル メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="bbc77-107">次の図は、Blazor のライフサイクルを示しています。</span><span class="sxs-lookup"><span data-stu-id="bbc77-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="bbc77-108">この記事の後続のセクションで、例を示しながらライフサイクル メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="bbc77-109">コンポーネント ライフサイクル イベント:</span><span class="sxs-lookup"><span data-stu-id="bbc77-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="bbc77-110">要求に対してコンポーネントが初めてレンダリングされる場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="bbc77-111">コンポーネントのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-111">Create the component's instance.</span></span>
   * <span data-ttu-id="bbc77-112">プロパティの挿入を実行します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-112">Perform property injection.</span></span> <span data-ttu-id="bbc77-113">[`SetParametersAsync`](#before-parameters-are-set) を実行します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="bbc77-114">[`OnInitialized{Async}`](#component-initialization-methods) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="bbc77-115"><xref:System.Threading.Tasks.Task> が返された場合、<xref:System.Threading.Tasks.Task> が待機され、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="bbc77-116"><xref:System.Threading.Tasks.Task> が返されない場合は、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="bbc77-117">[`OnParametersSet{Async}`](#after-parameters-are-set) を呼び出し、コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="bbc77-118">`OnParametersSetAsync` から <xref:System.Threading.Tasks.Task> が返された場合は、<xref:System.Threading.Tasks.Task> を待機してから、コンポーネントが再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Blazor の Razor コンポーネントのコンポーネント ライフサイクル イベント](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="bbc77-120">ドキュメント オブジェクト モデル (DOM) イベント処理:</span><span class="sxs-lookup"><span data-stu-id="bbc77-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="bbc77-121">イベント ハンドラーが実行されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-121">The event handler is run.</span></span>
1. <span data-ttu-id="bbc77-122"><xref:System.Threading.Tasks.Task> が返された場合、<xref:System.Threading.Tasks.Task> を待機してから、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="bbc77-123"><xref:System.Threading.Tasks.Task> が返されない場合は、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![ドキュメント オブジェクト モデル (DOM) イベント処理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="bbc77-125">`Render` のライフサイクル:</span><span class="sxs-lookup"><span data-stu-id="bbc77-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="bbc77-126">コンポーネントでそれ以上のレンダリング操作を停止します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-126">Stop further rendering operations on the component:</span></span>
   * <span data-ttu-id="bbc77-127">最初のレンダリングの後。</span><span class="sxs-lookup"><span data-stu-id="bbc77-127">After the first render.</span></span>
   * <span data-ttu-id="bbc77-128">[`ShouldRender`](#suppress-ui-refreshing) が `false` の場合。</span><span class="sxs-lookup"><span data-stu-id="bbc77-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="bbc77-129">レンダリング ツリーの差分を作成し、コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="bbc77-130">DOM が更新されるのを待機します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="bbc77-131">[`OnAfterRender{Async}`](#after-component-render) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Render ライフサイクル](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="bbc77-133">Developer によって [`StateHasChanged`](#state-changes) の呼び出しが行われると、結果としてレンダリングが実行されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="bbc77-134">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="bbc77-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="bbc77-135">パラメーターが設定される前</span><span class="sxs-lookup"><span data-stu-id="bbc77-135">Before parameters are set</span></span>

<span data-ttu-id="bbc77-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> は、レンダリング ツリーのコンポーネントの親によって指定されたパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="bbc77-137"><xref:Microsoft.AspNetCore.Components.ParameterView> には、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> が呼び出されるたびに、コンポーネントのパラメーター値のセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-137"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="bbc77-138"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> の既定の実装では、対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にある [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) または [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 属性を使用して、各プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-138">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="bbc77-139">対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にないパラメーターは、変更されないままになります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-139">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="bbc77-140">[`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) が呼び出されない場合、カスタム コードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-140">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="bbc77-141">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="bbc77-141">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="bbc77-142">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-142">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="bbc77-143">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-143">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="bbc77-144">コンポーネントの初期化メソッド</span><span class="sxs-lookup"><span data-stu-id="bbc77-144">Component initialization methods</span></span>

<span data-ttu-id="bbc77-145"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> は、コンポーネントが、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> でその親コンポーネントから初期パラメーターを受け取った後で初期化されるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-145"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="bbc77-146">コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> を使用します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-146">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="bbc77-147">同期操作の場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-147">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="bbc77-148">非同期操作を実行するには、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> をオーバーライドし、操作で [`await`](/dotnet/csharp/language-reference/operators/await) 演算子を使用します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-148">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="bbc77-149">[コンテンツをプリレンダリングする](xref:blazor/fundamentals/additional-scenarios#render-mode) Blazor Server アプリによって、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> が "*2 回*" 呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-149">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="bbc77-150">コンポーネントが最初にページの一部として静的にレンダリングされるときに 1 回。</span><span class="sxs-lookup"><span data-stu-id="bbc77-150">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="bbc77-151">ブラウザーがサーバーへの接続を確立するときに 2 回目。</span><span class="sxs-lookup"><span data-stu-id="bbc77-151">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="bbc77-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 内で開発者コードが 2 回実行されないようにするには、「[プリレンダリング後のステートフル再接続](#stateful-reconnection-after-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-152">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="bbc77-153">Blazor Server アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="bbc77-153">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="bbc77-154">コンポーネントは、プリレンダリング時に異なるレンダリングが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-154">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="bbc77-155">詳細については、「[アプリがプリレンダリングされていることを検出する](#detect-when-the-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-155">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="bbc77-156">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-156">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="bbc77-157">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-157">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="bbc77-158">パラメーターが設定された後</span><span class="sxs-lookup"><span data-stu-id="bbc77-158">After parameters are set</span></span>

<span data-ttu-id="bbc77-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> または <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="bbc77-160">コンポーネントが <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> または <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> で初期化された後。</span><span class="sxs-lookup"><span data-stu-id="bbc77-160">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="bbc77-161">親コンポーネントが再レンダリングし、次のものを提供するとき:</span><span class="sxs-lookup"><span data-stu-id="bbc77-161">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="bbc77-162">少なくとも 1 つのパラメーターが変更された既知のプリミティブ不変型のみ。</span><span class="sxs-lookup"><span data-stu-id="bbc77-162">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="bbc77-163">任意の複合型のパラメーター。</span><span class="sxs-lookup"><span data-stu-id="bbc77-163">Any complex-typed parameters.</span></span> <span data-ttu-id="bbc77-164">フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーター セットは変更済みとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-164">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="bbc77-165">パラメーターとプロパティ値を適用するときの非同期処理は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ライフサイクル イベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-165">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="bbc77-166">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-166">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="bbc77-167">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-167">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="bbc77-168">コンポーネントのレンダリング後</span><span class="sxs-lookup"><span data-stu-id="bbc77-168">After component render</span></span>

<span data-ttu-id="bbc77-169"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-169"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="bbc77-170">この時点で、要素およびコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-170">Element and component references are populated at this point.</span></span> <span data-ttu-id="bbc77-171">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を行うには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-171">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="bbc77-172"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> の `firstRender` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="bbc77-172">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="bbc77-173">コンポーネント インスタンスを初めて表示するときに `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-173">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="bbc77-174">初期化作業が確実に 1 回だけ実行されるように使用できます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-174">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="bbc77-175"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ライフサイクル イベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-175">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="bbc77-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークでは、そのタスクが完了しても、コンポーネントに対してさらにレンダリング サイクルがスケジュールされることはありません。</span><span class="sxs-lookup"><span data-stu-id="bbc77-176">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="bbc77-177">これは、無限のレンダリング ループを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="bbc77-177">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="bbc77-178">返されたタスクが完了すると、さらにレンダリング サイクルをスケジュールする他のライフサイクル メソッドとは異なります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-178">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="bbc77-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> " *はサーバーでのプリレンダリング プロセス中には呼び出されません*"。</span><span class="sxs-lookup"><span data-stu-id="bbc77-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="bbc77-180">メソッドは、プリレンダリングが完了した後にコンポーネントが対話形式でレンダリングされるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-180">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="bbc77-181">次の場合に、アプリによりプリレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-181">When the app prerenders:</span></span>

1. <span data-ttu-id="bbc77-182">コンポーネントがサーバー上で実行され、HTTP 応答でいくつかの静的 HTML マークアップが生成される。</span><span class="sxs-lookup"><span data-stu-id="bbc77-182">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="bbc77-183">このフェーズでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> は呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="bbc77-183">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="bbc77-184">ブラウザーで `blazor.server.js` または `blazor.webassembly.js` が起動すると、コンポーネントが対話型のレンダリング モードで再起動される。</span><span class="sxs-lookup"><span data-stu-id="bbc77-184">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="bbc77-185">コンポーネントが再起動されると、アプリはプリレンダリング フェーズでなくなるため、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **が呼び出されます**。</span><span class="sxs-lookup"><span data-stu-id="bbc77-185">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="bbc77-186">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-186">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="bbc77-187">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-187">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="bbc77-188">UI 更新の抑制</span><span class="sxs-lookup"><span data-stu-id="bbc77-188">Suppress UI refreshing</span></span>

<span data-ttu-id="bbc77-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> をオーバーライドして、UI の更新を抑制します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-189">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="bbc77-190">実装によって `true` が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-190">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="bbc77-191"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> は、コンポーネントがレンダリングされるたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-191"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="bbc77-192"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> がオーバーライドされる場合でも、コンポーネントは常に最初にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-192">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="bbc77-193">詳細については、「<xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-193">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="bbc77-194">状態変更</span><span class="sxs-lookup"><span data-stu-id="bbc77-194">State changes</span></span>

<span data-ttu-id="bbc77-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、状態が変更されたことをコンポーネントに通知します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="bbc77-196">必要に応じて、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すと、コンポーネントが再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-196">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="bbc77-197"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、<xref:Microsoft.AspNetCore.Components.EventCallback> メソッドに対して自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-197"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="bbc77-198">詳細については、「<xref:blazor/components/event-handling#eventcallback>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-198">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="bbc77-199">レンダリング時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="bbc77-199">Handle incomplete async actions at render</span></span>

<span data-ttu-id="bbc77-200">ライフサイクル イベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-200">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="bbc77-201">ライフサイクル メソッドの実行中に、オブジェクトが `null` またはデータが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-201">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="bbc77-202">オブジェクトが初期化されていることを確認するレンダリング ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-202">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="bbc77-203">オブジェクトが `null` の間、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-203">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="bbc77-204">Blazor テンプレートの `FetchData` コンポーネントでは、予測データ (`forecasts`) を非同期に受信するように、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-204">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="bbc77-205">`forecasts` が `null` の場合、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-205">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="bbc77-206"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> によって返された `Task` が完了すると、コンポーネントは更新された状態で再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-206">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="bbc77-207">Blazor Server テンプレートの `Pages/FetchData.razor` は以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-207">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="bbc77-208">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="bbc77-208">Handle errors</span></span>

<span data-ttu-id="bbc77-209">ライフサイクル メソッド実行中のエラー処理の詳細については、「<xref:blazor/fundamentals/handle-errors#lifecycle-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-209">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="bbc77-210">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="bbc77-210">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="bbc77-211">Blazor Server アプリで <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> が <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> の場合、コンポーネントは最初にページの一部として静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-211">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="bbc77-212">ブラウザーがサーバーへの接続を確立すると、コンポーネントが "*再度*" レンダリングされ、コンポーネントがやりとりできるようになります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-212">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="bbc77-213">コンポーネントを初期化するための [`OnInitialized{Async}`](#component-initialization-methods) ライフサイクル メソッドが存在する場合、メソッドは "*2 回*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-213">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="bbc77-214">コンポーネントが静的にプリレンダリングされたとき。</span><span class="sxs-lookup"><span data-stu-id="bbc77-214">When the component is prerendered statically.</span></span>
* <span data-ttu-id="bbc77-215">サーバー接続が確立された後。</span><span class="sxs-lookup"><span data-stu-id="bbc77-215">After the server connection has been established.</span></span>

<span data-ttu-id="bbc77-216">これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変わる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-216">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="bbc77-217">Blazor Server アプリ内の二重レンダリングのシナリオを回避するには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="bbc77-217">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="bbc77-218">プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-218">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="bbc77-219">識別子をプリレンダリング中に使用して、コンポーネントの状態を保存します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-219">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="bbc77-220">識別子をプリレンダリング後に使用して、キャッシュされた状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-220">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="bbc77-221">次のコードは、二重レンダリングを回避するテンプレートベースの Blazor Server アプリ内で更新される `WeatherForecastService` を示しています。</span><span class="sxs-lookup"><span data-stu-id="bbc77-221">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="bbc77-222"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> の詳細については、「<xref:blazor/fundamentals/additional-scenarios#render-mode>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-222">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="bbc77-223">アプリがプリレンダリングされていることを検出する</span><span class="sxs-lookup"><span data-stu-id="bbc77-223">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="bbc77-224">IDisposable を使用したコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="bbc77-224">Component disposal with IDisposable</span></span>

<span data-ttu-id="bbc77-225">コンポーネントが <xref:System.IDisposable> を実装している場合は、コンポーネントが UI から削除されると、[`Dispose` メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-225">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="bbc77-226">破棄は、[コンポーネントの初期化](#component-initialization-methods)中など、いつでも実行できます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-226">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="bbc77-227">次のコンポーネントでは、`@implements IDisposable` および `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-227">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="bbc77-228">`Dispose` では、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="bbc77-228">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="bbc77-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="bbc77-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="bbc77-230">.NET イベントからイベント ハンドラーのサブスクライブを解除します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-230">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="bbc77-231">次の [Blazor フォーム](xref:blazor/forms-validation)の例は、`Dispose` メソッドでイベント ハンドラーをアンフックする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="bbc77-231">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="bbc77-232">プライベート フィールドとラムダのアプローチ</span><span class="sxs-lookup"><span data-stu-id="bbc77-232">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="bbc77-233">プライベート メソッドのアプローチ</span><span class="sxs-lookup"><span data-stu-id="bbc77-233">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="bbc77-234">取り消し可能なバックグラウンド作業</span><span class="sxs-lookup"><span data-stu-id="bbc77-234">Cancelable background work</span></span>

<span data-ttu-id="bbc77-235">ネットワーク呼び出し (<xref:System.Net.Http.HttpClient>) の実行やデータベースとの対話など、コンポーネントによって実行時間の長いバックグラウンド作業が実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-235">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="bbc77-236">いくつかの状況でシステム リソースを節約するために、バックグラウンド作業を停止することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bbc77-236">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="bbc77-237">たとえば、ユーザーがコンポーネントの操作を止めても、バックグラウンドの非同期操作は自動的に停止しません。</span><span class="sxs-lookup"><span data-stu-id="bbc77-237">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="bbc77-238">バックグラウンド作業項目の取り消しが必要になるその他の理由には、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-238">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="bbc77-239">実行中のバックグラウンド タスクは、不完全な入力データまたは処理パラメーターを使用して開始されました。</span><span class="sxs-lookup"><span data-stu-id="bbc77-239">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="bbc77-240">現在実行中のバックグラウンド作業項目のセットを、新しい作業項目のセットに置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-240">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="bbc77-241">現在実行中のタスクの優先度を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-241">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="bbc77-242">アプリをサーバーに再展開するには、シャットダウンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bbc77-242">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="bbc77-243">サーバー リソースが制限され、バックグラウンド作業項目の再スケジュールが必要になりました。</span><span class="sxs-lookup"><span data-stu-id="bbc77-243">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="bbc77-244">コンポーネントに取り消し可能なバックグラウンド作業パターンを実装するには:</span><span class="sxs-lookup"><span data-stu-id="bbc77-244">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="bbc77-245"><xref:System.Threading.CancellationTokenSource> と <xref:System.Threading.CancellationToken> を使用します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-245">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="bbc77-246">[コンポーネントの破棄](#component-disposal-with-idisposable)時と、任意の時点で手動でトークンを取り消すことで取り消しが望まれた場合は、[`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) を呼び出して、バックグラウンド作業を取り消す必要があることを通知します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-246">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="bbc77-247">非同期呼び出しが返された後、トークンに対して <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-247">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="bbc77-248">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-248">In the following example:</span></span>

* <span data-ttu-id="bbc77-249">`await Task.Delay(5000, cts.Token);` は、実行時間が長い非同期のバックグラウンド作業を表します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-249">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="bbc77-250">`BackgroundResourceMethod` は、メソッドが呼び出される前に `Resource` が破棄された場合に開始されない、実行時間が長いバックグラウンド メソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-250">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="bbc77-251">Blazor Server 再接続イベント</span><span class="sxs-lookup"><span data-stu-id="bbc77-251">Blazor Server reconnection events</span></span>

<span data-ttu-id="bbc77-252">この記事で説明するコンポーネント ライフサイクル イベントは、[Blazor Server の再接続イベント ハンドラー](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui)とは別々に動作します。</span><span class="sxs-lookup"><span data-stu-id="bbc77-252">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="bbc77-253">Blazor Server アプリとクライアントの SignalR 接続が失われた場合には、UI の更新だけが中断されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-253">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="bbc77-254">その接続が再確立されると、UI の更新が再開されます。</span><span class="sxs-lookup"><span data-stu-id="bbc77-254">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="bbc77-255">回線ハンドラーのイベントと構成の詳細については、「<xref:blazor/fundamentals/additional-scenarios>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc77-255">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>

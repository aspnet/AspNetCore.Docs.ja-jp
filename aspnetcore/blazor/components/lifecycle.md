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
ms.openlocfilehash: 03a49c827a1f70e6b721adf293857bb33475ed36
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107078"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="31701-103">ASP.NET Core Blazor ライフサイクル</span><span class="sxs-lookup"><span data-stu-id="31701-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="31701-104">Blazor フレームワークには、同期と非同期のライフサイクル メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="31701-104">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="31701-105">コンポーネントの初期化およびレンダリング中にコンポーネントで追加の操作を実行するには、ライフサイクル メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="31701-105">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="31701-106">次の図は、Blazor のライフサイクルを示しています。</span><span class="sxs-lookup"><span data-stu-id="31701-106">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="31701-107">この記事の後続のセクションで、例を示しながらライフサイクル メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="31701-107">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="31701-108">コンポーネント ライフサイクル イベント:</span><span class="sxs-lookup"><span data-stu-id="31701-108">Component lifecycle events:</span></span>

1. <span data-ttu-id="31701-109">要求に対してコンポーネントが初めてレンダリングされる場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="31701-109">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="31701-110">コンポーネントのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="31701-110">Create the component's instance.</span></span>
   * <span data-ttu-id="31701-111">プロパティの挿入を実行します。</span><span class="sxs-lookup"><span data-stu-id="31701-111">Perform property injection.</span></span> <span data-ttu-id="31701-112">[`SetParametersAsync`](#before-parameters-are-set) を実行します。</span><span class="sxs-lookup"><span data-stu-id="31701-112">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="31701-113">[`OnInitialized{Async}`](#component-initialization-methods) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="31701-113">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="31701-114"><xref:System.Threading.Tasks.Task> が返された場合、<xref:System.Threading.Tasks.Task> が待機され、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-114">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="31701-115"><xref:System.Threading.Tasks.Task> が返されない場合は、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-115">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="31701-116">[`OnParametersSet{Async}`](#after-parameters-are-set) を呼び出し、コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="31701-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="31701-117">`OnParametersSetAsync` から <xref:System.Threading.Tasks.Task> が返された場合は、<xref:System.Threading.Tasks.Task> を待機してから、コンポーネントが再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-117">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Blazor の Razor コンポーネントのコンポーネント ライフサイクル イベント](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="31701-119">ドキュメント オブジェクト モデル (DOM) イベント処理:</span><span class="sxs-lookup"><span data-stu-id="31701-119">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="31701-120">イベント ハンドラーが実行されます。</span><span class="sxs-lookup"><span data-stu-id="31701-120">The event handler is run.</span></span>
1. <span data-ttu-id="31701-121"><xref:System.Threading.Tasks.Task> が返された場合、<xref:System.Threading.Tasks.Task> を待機してから、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-121">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="31701-122"><xref:System.Threading.Tasks.Task> が返されない場合は、コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-122">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![ドキュメント オブジェクト モデル (DOM) イベント処理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="31701-124">`Render` のライフサイクル:</span><span class="sxs-lookup"><span data-stu-id="31701-124">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="31701-125">コンポーネントでそれ以上のレンダリング操作を行わないようにします。</span><span class="sxs-lookup"><span data-stu-id="31701-125">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="31701-126">最初のレンダリングの後。</span><span class="sxs-lookup"><span data-stu-id="31701-126">After the first render.</span></span>
   * <span data-ttu-id="31701-127">[`ShouldRender`](#suppress-ui-refreshing) が `false` の場合。</span><span class="sxs-lookup"><span data-stu-id="31701-127">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="31701-128">レンダリング ツリーの差分を作成し、コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="31701-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="31701-129">DOM が更新されるのを待機します。</span><span class="sxs-lookup"><span data-stu-id="31701-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="31701-130">[`OnAfterRender{Async}`](#after-component-render) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="31701-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Render ライフサイクル](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="31701-132">Developer によって [`StateHasChanged`](#state-changes) の呼び出しが行われると、結果としてレンダリングが実行されます。</span><span class="sxs-lookup"><span data-stu-id="31701-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="31701-133">詳細については、「<xref:blazor/components/rendering>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-133">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="31701-134">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="31701-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="31701-135">パラメーターが設定される前</span><span class="sxs-lookup"><span data-stu-id="31701-135">Before parameters are set</span></span>

<span data-ttu-id="31701-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> により、レンダリング ツリーのコンポーネントの親によって、またはルート パラメーターから指定されたパラメーターが設定されます。</span><span class="sxs-lookup"><span data-stu-id="31701-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="31701-137">開発者のコードでは、このメソッドをオーバーライドすることによって、<xref:Microsoft.AspNetCore.Components.ParameterView> のパラメーターと直接対話できます。</span><span class="sxs-lookup"><span data-stu-id="31701-137">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="31701-138">次の例では、`Param` のルート パラメーターの解析が成功した場合、<xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> によって `Param` パラメーターの値が `value` に代入されます。</span><span class="sxs-lookup"><span data-stu-id="31701-138">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="31701-139">`value` が `null` でなければ、コンポーネントによってその値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="31701-139">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="31701-140">[ルート パラメーターの照合では大文字と小文字が区別されません](xref:blazor/fundamentals/routing#route-parameters)が、ルート テンプレートでは <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> によってパラメーター名が大文字と小文字を区別して照合されます。</span><span class="sxs-lookup"><span data-stu-id="31701-140">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="31701-141">次の例では、値を取得するために、`/{param?}` ではなく、`/{Param?}` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31701-141">The following example is required to use `/{Param?}`, not `/{param?}`, in order to get the value.</span></span> <span data-ttu-id="31701-142">このシナリオで `/{param?}` が使用される場合、<xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> から `false` が返され、`message` はどちらの文字列にも設定されません。</span><span class="sxs-lookup"><span data-stu-id="31701-142">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either string.</span></span>

<span data-ttu-id="31701-143">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="31701-143">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="31701-144"><xref:Microsoft.AspNetCore.Components.ParameterView> には、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> が呼び出されるたびに、コンポーネントのパラメーター値のセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="31701-144"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="31701-145"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> の既定の実装では、対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にある [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) または [`[CascadingParameter]` 属性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)を使用して、各プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="31701-145">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="31701-146">対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にないパラメーターは、変更されないままになります。</span><span class="sxs-lookup"><span data-stu-id="31701-146">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="31701-147">[`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) が呼び出されない場合、カスタム コードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="31701-147">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="31701-148">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="31701-148">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="31701-149">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="31701-149">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="31701-150">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-150">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="31701-151">コンポーネントの初期化メソッド</span><span class="sxs-lookup"><span data-stu-id="31701-151">Component initialization methods</span></span>

<span data-ttu-id="31701-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> は、コンポーネントが、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> でその親コンポーネントから初期パラメーターを受け取った後で初期化されるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31701-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="31701-153">コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> を使用します。</span><span class="sxs-lookup"><span data-stu-id="31701-153">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="31701-154">同期操作の場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="31701-154">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="31701-155">非同期操作を実行するには、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> をオーバーライドし、操作で [`await`](/dotnet/csharp/language-reference/operators/await) 演算子を使用します。</span><span class="sxs-lookup"><span data-stu-id="31701-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="31701-156">[コンテンツをプリレンダリングする](xref:blazor/fundamentals/signalr#render-mode) Blazor Server アプリによって、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> が "*2 回*" 呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31701-156">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="31701-157">コンポーネントが最初にページの一部として静的にレンダリングされるときに 1 回。</span><span class="sxs-lookup"><span data-stu-id="31701-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="31701-158">ブラウザーがサーバーへの接続を確立するときに 2 回目。</span><span class="sxs-lookup"><span data-stu-id="31701-158">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="31701-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 内で開発者コードが 2 回実行されないようにするには、「[プリレンダリング後のステートフル再接続](#stateful-reconnection-after-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="31701-160">Blazor Server アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="31701-160">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="31701-161">コンポーネントは、プリレンダリング時に異なるレンダリングが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="31701-161">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="31701-162">詳細については、「[アプリがプリレンダリングされていることを検出する](#detect-when-the-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-162">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="31701-163">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="31701-163">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="31701-164">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-164">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="31701-165">パラメーターが設定された後</span><span class="sxs-lookup"><span data-stu-id="31701-165">After parameters are set</span></span>

<span data-ttu-id="31701-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> または <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31701-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="31701-167">コンポーネントが <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> または <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> で初期化された後。</span><span class="sxs-lookup"><span data-stu-id="31701-167">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="31701-168">親コンポーネントが再レンダリングし、次のものを提供するとき:</span><span class="sxs-lookup"><span data-stu-id="31701-168">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="31701-169">少なくとも 1 つのパラメーターが変更された既知のプリミティブ不変型のみ。</span><span class="sxs-lookup"><span data-stu-id="31701-169">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="31701-170">任意の複合型のパラメーター。</span><span class="sxs-lookup"><span data-stu-id="31701-170">Any complex-typed parameters.</span></span> <span data-ttu-id="31701-171">フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーター セットは変更済みとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="31701-171">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="31701-172">パラメーターとプロパティ値を適用するときの非同期処理は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ライフサイクル イベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31701-172">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="31701-173">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="31701-173">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="31701-174">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-174">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="31701-175">コンポーネントのレンダリング後</span><span class="sxs-lookup"><span data-stu-id="31701-175">After component render</span></span>

<span data-ttu-id="31701-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31701-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="31701-177">この時点で、要素およびコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="31701-177">Element and component references are populated at this point.</span></span> <span data-ttu-id="31701-178">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を行うには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="31701-178">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="31701-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> の `firstRender` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="31701-179">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="31701-180">コンポーネント インスタンスを初めて表示するときに `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="31701-180">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="31701-181">初期化作業が確実に 1 回だけ実行されるように使用できます。</span><span class="sxs-lookup"><span data-stu-id="31701-181">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="31701-182"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ライフサイクル イベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31701-182">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="31701-183"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークでは、そのタスクが完了しても、コンポーネントに対してさらにレンダリング サイクルがスケジュールされることはありません。</span><span class="sxs-lookup"><span data-stu-id="31701-183">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="31701-184">これは、無限のレンダリング ループを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="31701-184">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="31701-185">返されたタスクが完了すると、さらにレンダリング サイクルをスケジュールする他のライフサイクル メソッドとは異なります。</span><span class="sxs-lookup"><span data-stu-id="31701-185">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="31701-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> " *はサーバーでのプリレンダリング プロセス中には呼び出されません*"。</span><span class="sxs-lookup"><span data-stu-id="31701-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="31701-187">メソッドは、プリレンダリングが完了した後にコンポーネントが対話形式でレンダリングされるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31701-187">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="31701-188">次の場合に、アプリによりプリレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-188">When the app prerenders:</span></span>

1. <span data-ttu-id="31701-189">コンポーネントがサーバー上で実行され、HTTP 応答でいくつかの静的 HTML マークアップが生成される。</span><span class="sxs-lookup"><span data-stu-id="31701-189">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="31701-190">このフェーズでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> は呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="31701-190">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="31701-191">ブラウザーで `blazor.server.js` または `blazor.webassembly.js` が起動すると、コンポーネントが対話型のレンダリング モードで再起動される。</span><span class="sxs-lookup"><span data-stu-id="31701-191">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="31701-192">コンポーネントが再起動されると、アプリはプリレンダリング フェーズでなくなるため、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **が呼び出されます**。</span><span class="sxs-lookup"><span data-stu-id="31701-192">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="31701-193">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="31701-193">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="31701-194">詳細については、「[`IDisposable` を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-194">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="31701-195">UI 更新の抑制</span><span class="sxs-lookup"><span data-stu-id="31701-195">Suppress UI refreshing</span></span>

<span data-ttu-id="31701-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> をオーバーライドして、UI の更新を抑制します。</span><span class="sxs-lookup"><span data-stu-id="31701-196">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="31701-197">実装によって `true` が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="31701-197">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="31701-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> は、コンポーネントがレンダリングされるたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31701-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="31701-199"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> がオーバーライドされる場合でも、コンポーネントは常に最初にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-199">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="31701-200">詳細については、「<xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-200">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="31701-201">状態変更</span><span class="sxs-lookup"><span data-stu-id="31701-201">State changes</span></span>

<span data-ttu-id="31701-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、状態が変更されたことをコンポーネントに通知します。</span><span class="sxs-lookup"><span data-stu-id="31701-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="31701-203">必要に応じて、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すと、コンポーネントが再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-203">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="31701-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、<xref:Microsoft.AspNetCore.Components.EventCallback> メソッドに対して自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="31701-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="31701-205">詳細については、「<xref:blazor/components/event-handling#eventcallback>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-205">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="31701-206">詳細については、「<xref:blazor/components/rendering>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-206">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="31701-207">レンダリング時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="31701-207">Handle incomplete async actions at render</span></span>

<span data-ttu-id="31701-208">ライフサイクル イベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31701-208">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="31701-209">ライフサイクル メソッドの実行中に、オブジェクトが `null` またはデータが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31701-209">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="31701-210">オブジェクトが初期化されていることを確認するレンダリング ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="31701-210">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="31701-211">オブジェクトが `null` の間、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="31701-211">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="31701-212">Blazor テンプレートの `FetchData` コンポーネントでは、予測データ (`forecasts`) を非同期に受信するように、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="31701-212">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="31701-213">`forecasts` が `null` の場合、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="31701-213">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="31701-214"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> によって返された `Task` が完了すると、コンポーネントは更新された状態で再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-214">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="31701-215">Blazor Server テンプレートの `Pages/FetchData.razor` は以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="31701-215">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="31701-216">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="31701-216">Handle errors</span></span>

<span data-ttu-id="31701-217">ライフサイクル メソッド実行中のエラー処理の詳細については、「<xref:blazor/fundamentals/handle-errors#lifecycle-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-217">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="31701-218">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="31701-218">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="31701-219">Blazor Server アプリで <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> が <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> の場合、コンポーネントは最初にページの一部として静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="31701-219">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="31701-220">ブラウザーがサーバーへの接続を確立すると、コンポーネントが "*再度*" レンダリングされ、コンポーネントがやりとりできるようになります。</span><span class="sxs-lookup"><span data-stu-id="31701-220">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="31701-221">コンポーネントを初期化するための [`OnInitialized{Async}`](#component-initialization-methods) ライフサイクル メソッドが存在する場合、メソッドは "*2 回*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="31701-221">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="31701-222">コンポーネントが静的にプリレンダリングされたとき。</span><span class="sxs-lookup"><span data-stu-id="31701-222">When the component is prerendered statically.</span></span>
* <span data-ttu-id="31701-223">サーバー接続が確立された後。</span><span class="sxs-lookup"><span data-stu-id="31701-223">After the server connection has been established.</span></span>

<span data-ttu-id="31701-224">これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変わる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31701-224">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="31701-225">Blazor Server アプリ内の二重レンダリングのシナリオを回避するには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="31701-225">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="31701-226">プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。</span><span class="sxs-lookup"><span data-stu-id="31701-226">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="31701-227">識別子をプリレンダリング中に使用して、コンポーネントの状態を保存します。</span><span class="sxs-lookup"><span data-stu-id="31701-227">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="31701-228">識別子をプリレンダリング後に使用して、キャッシュされた状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="31701-228">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="31701-229">次のコードは、二重レンダリングを回避するテンプレートベースの Blazor Server アプリ内で更新される `WeatherForecastService` を示しています。</span><span class="sxs-lookup"><span data-stu-id="31701-229">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="31701-230"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> の詳細については、「<xref:blazor/fundamentals/signalr#render-mode>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-230">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="31701-231">アプリがプリレンダリングされていることを検出する</span><span class="sxs-lookup"><span data-stu-id="31701-231">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="31701-232">`IDisposable` を使用したコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="31701-232">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="31701-233">コンポーネントが <xref:System.IDisposable> を実装している場合、そのコンポーネントが UI から削除されるときにフレームワークで[破棄メソッド](/dotnet/standard/garbage-collection/implementing-dispose)を呼び出し、アンマネージ リソースを解放することができます。</span><span class="sxs-lookup"><span data-stu-id="31701-233">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="31701-234">破棄は、[コンポーネントの初期化](#component-initialization-methods)中など、いつでも実行できます。</span><span class="sxs-lookup"><span data-stu-id="31701-234">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="31701-235">次のコンポーネントでは、[`@implements`](xref:mvc/views/razor#implements) Razor ディレクティブを使用して <xref:System.IDisposable> が実装されています。</span><span class="sxs-lookup"><span data-stu-id="31701-235">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

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

<span data-ttu-id="31701-236">オブジェクトが破棄を必要とする場合、<xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> が呼び出されるきに、ラムダを使用してオブジェクトを破棄できます。</span><span class="sxs-lookup"><span data-stu-id="31701-236">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called:</span></span>

<span data-ttu-id="31701-237">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="31701-237">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="31701-238">上記の例は、「<xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>」に示されています。</span><span class="sxs-lookup"><span data-stu-id="31701-238">The preceding example appears in <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>.</span></span>

<span data-ttu-id="31701-239">非同期の破棄タスクの場合は、<xref:System.IDisposable.Dispose> の代わりに `DisposeAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="31701-239">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="31701-240">`Dispose` では、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="31701-240">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="31701-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="31701-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="31701-242">.NET イベントからイベント ハンドラーのサブスクライブを解除します。</span><span class="sxs-lookup"><span data-stu-id="31701-242">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="31701-243">次の [Blazor フォーム](xref:blazor/forms-validation)の例は、`Dispose` メソッドでイベント ハンドラーの登録を解除する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="31701-243">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="31701-244">プライベート フィールドとラムダのアプローチ</span><span class="sxs-lookup"><span data-stu-id="31701-244">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="31701-245">プライベート メソッドのアプローチ</span><span class="sxs-lookup"><span data-stu-id="31701-245">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

<span data-ttu-id="31701-246">[匿名関数](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)、メソッド、または式が使用されている場合、<xref:System.IDisposable> の実装やデリゲートの登録解除を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="31701-246">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="31701-247">しかし、デリゲートの登録解除に失敗することは、**イベントを公開するオブジェクトが、デリゲートを登録するコンポーネントの有効期間を超えている場合** に問題になります。</span><span class="sxs-lookup"><span data-stu-id="31701-247">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="31701-248">この場合、登録されたデリゲートによって元のオブジェクトが保持されているため、メモリ リークが発生します。</span><span class="sxs-lookup"><span data-stu-id="31701-248">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="31701-249">そのため、イベント デリゲートがすぐに破棄されることがわかっている場合にのみ、次のアプローチを使用してください。</span><span class="sxs-lookup"><span data-stu-id="31701-249">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="31701-250">破棄が必要なオブジェクトの有効期間が不明な場合は、前の例で示したように、デリゲート メソッドを登録し、そのデリゲートを適切に破棄します。</span><span class="sxs-lookup"><span data-stu-id="31701-250">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the preceding examples show.</span></span>

* <span data-ttu-id="31701-251">匿名のラムダ メソッドのアプローチ (明示的な破棄は不要)</span><span class="sxs-lookup"><span data-stu-id="31701-251">Anonymous lambda method approach (explicit disposal not required)</span></span>

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* <span data-ttu-id="31701-252">匿名ラムダ式のアプローチ (明示的な破棄は不要)</span><span class="sxs-lookup"><span data-stu-id="31701-252">Anonymous lambda expression approach (explicit disposal not required)</span></span>

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  <span data-ttu-id="31701-253">匿名ラムダ式を使用した上記のコードの完全な例は、「<xref:blazor/forms-validation#validator-components>」に示されています。</span><span class="sxs-lookup"><span data-stu-id="31701-253">The full example of the preceding code with anonymous lambda expressions appears in <xref:blazor/forms-validation#validator-components>.</span></span>

<span data-ttu-id="31701-254">詳細については、「[アンマネージ リソースのクリーンアップ](/dotnet/standard/garbage-collection/unmanaged)」と、その後に続く `Dispose` および `DisposeAsync` メソッドの実装に関するトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-254">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="31701-255">取り消し可能なバックグラウンド作業</span><span class="sxs-lookup"><span data-stu-id="31701-255">Cancelable background work</span></span>

<span data-ttu-id="31701-256">ネットワーク呼び出し (<xref:System.Net.Http.HttpClient>) の実行やデータベースとの対話など、コンポーネントによって実行時間の長いバックグラウンド作業が実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="31701-256">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="31701-257">いくつかの状況でシステム リソースを節約するために、バックグラウンド作業を停止することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="31701-257">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="31701-258">たとえば、ユーザーがコンポーネントの操作を止めても、バックグラウンドの非同期操作は自動的に停止しません。</span><span class="sxs-lookup"><span data-stu-id="31701-258">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="31701-259">バックグラウンド作業項目の取り消しが必要になるその他の理由には、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="31701-259">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="31701-260">実行中のバックグラウンド タスクは、不完全な入力データまたは処理パラメーターを使用して開始されました。</span><span class="sxs-lookup"><span data-stu-id="31701-260">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="31701-261">現在実行中のバックグラウンド作業項目のセットを、新しい作業項目のセットに置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="31701-261">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="31701-262">現在実行中のタスクの優先度を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31701-262">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="31701-263">アプリをサーバーに再展開するには、シャットダウンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="31701-263">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="31701-264">サーバー リソースが制限され、バックグラウンド作業項目の再スケジュールが必要になりました。</span><span class="sxs-lookup"><span data-stu-id="31701-264">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="31701-265">コンポーネントに取り消し可能なバックグラウンド作業パターンを実装するには:</span><span class="sxs-lookup"><span data-stu-id="31701-265">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="31701-266"><xref:System.Threading.CancellationTokenSource> と <xref:System.Threading.CancellationToken> を使用します。</span><span class="sxs-lookup"><span data-stu-id="31701-266">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="31701-267">[コンポーネントの破棄](#component-disposal-with-idisposable)時と、任意の時点で手動でトークンを取り消すことで取り消しが望まれた場合は、[`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) を呼び出して、バックグラウンド作業を取り消す必要があることを通知します。</span><span class="sxs-lookup"><span data-stu-id="31701-267">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="31701-268">非同期呼び出しが返された後、トークンに対して <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="31701-268">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="31701-269">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="31701-269">In the following example:</span></span>

* <span data-ttu-id="31701-270">`await Task.Delay(5000, cts.Token);` は、実行時間が長い非同期のバックグラウンド作業を表します。</span><span class="sxs-lookup"><span data-stu-id="31701-270">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="31701-271">`BackgroundResourceMethod` は、メソッドが呼び出される前に `Resource` が破棄された場合に開始されない、実行時間が長いバックグラウンド メソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="31701-271">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="31701-272">Blazor Server 再接続イベント</span><span class="sxs-lookup"><span data-stu-id="31701-272">Blazor Server reconnection events</span></span>

<span data-ttu-id="31701-273">この記事で説明するコンポーネント ライフサイクル イベントは、[Blazor Server の再接続イベント ハンドラー](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui)とは別々に動作します。</span><span class="sxs-lookup"><span data-stu-id="31701-273">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="31701-274">Blazor Server アプリとクライアントの SignalR 接続が失われた場合には、UI の更新だけが中断されます。</span><span class="sxs-lookup"><span data-stu-id="31701-274">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="31701-275">その接続が再確立されると、UI の更新が再開されます。</span><span class="sxs-lookup"><span data-stu-id="31701-275">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="31701-276">回線ハンドラーのイベントと構成の詳細については、「<xref:blazor/fundamentals/signalr>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31701-276">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>

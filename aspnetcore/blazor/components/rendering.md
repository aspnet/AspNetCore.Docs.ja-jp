---
title: ASP.NET Core Blazor コンポーネントのレンダリング
author: guardrex
description: StateHasChanged を呼び出すタイミングなど、ASP.NET Core Blazor アプリでの Razor コンポーネントのレンダリングについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: e1222981d4af3f4e233cdc0c57bb96a71972af15
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280049"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="b0d2b-103">ASP.NET Core Blazor コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="b0d2b-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="b0d2b-104">コンポーネントは、その親コンポーネントによってコンポーネント階層に最初に追加されるときにレンダリングされる "*必要*" があります。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-104">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="b0d2b-105">コンポーネントのレンダリングが厳密に必要なのは、このときだけです。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-105">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="b0d2b-106">コンポーネントでは、その独自のロジックと規則に従って、他の任意のタイミングでレンダリングすることを選択 "*できます*"。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-106">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="b0d2b-107">`ComponentBase` の規則</span><span class="sxs-lookup"><span data-stu-id="b0d2b-107">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="b0d2b-108">既定では、Razor コンポーネント (`.razor`) は <xref:Microsoft.AspNetCore.Components.ComponentBase> 基底クラスから継承されます。これには次のタイミングで再レンダリングをトリガーするロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-108">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="b0d2b-109">更新された一連のパラメーターを親コンポーネントから適用した後。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-109">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="b0d2b-110">カスケード パラメーターの更新された値を適用した後。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-110">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="b0d2b-111">イベントが通知され、その独自のイベント ハンドラーの 1 つを呼び出した後。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-111">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="b0d2b-112">その独自の <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> メソッドの呼び出しの後。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-112">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="b0d2b-113">次のいずれかに該当する場合、<xref:Microsoft.AspNetCore.Components.ComponentBase> から継承されるコンポーネントの再レンダリングは、パラメーターの更新が理由でスキップされます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-113">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="b0d2b-114">パラメーター値はすべて、既知の変更できないプリミティブ型 (`int`、`string`、`DateTime` など) であり、前にパラメーター セットが設定されてから変更されていない。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-114">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="b0d2b-115">コンポーネントの <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> メソッドから `false` が返される。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-115">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="b0d2b-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> の詳細については、「<xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-116">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="b0d2b-117">レンダリング フローを制御する</span><span class="sxs-lookup"><span data-stu-id="b0d2b-117">Control the rendering flow</span></span>

<span data-ttu-id="b0d2b-118">ほとんどの場合、イベントの発生後は <xref:Microsoft.AspNetCore.Components.ComponentBase> 規則に従って適切な一部のコンポーネント再レンダリングが行われます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-118">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="b0d2b-119">通常、開発者は、再レンダリングするコンポーネントと、それらを再レンダリングするタイミングをフレームワークに指示する手動のロジックを用意する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-119">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="b0d2b-120">フレームワークの規則の全体的な効果は、イベントを受信したコンポーネントが自動的に再レンダリングされることにあります。これにより、パラメーター値が変更された可能性のある子孫コンポーネントの再レンダリングが再帰的にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-120">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="b0d2b-121">フレームワークの規則がパフォーマンスに及ぼす影響と、アプリのコンポーネント階層を最適化する方法の詳細については、「<xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-121">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="b0d2b-122">`StateHasChanged` を呼び出すタイミング</span><span class="sxs-lookup"><span data-stu-id="b0d2b-122">When to call `StateHasChanged`</span></span>

<span data-ttu-id="b0d2b-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> メソッドを使用すると、いつでもレンダリングをトリガーできます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-123">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="b0d2b-124">ただし、よくある間違いですが、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を不必要に呼び出さないように注意してください。これは不要なレンダリング コストがかかるためです。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-124">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="b0d2b-125">次の場合に <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出す必要は "*ありません*"。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-125">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="b0d2b-126"><xref:Microsoft.AspNetCore.Components.ComponentBase> によってほとんどのルーチン イベント ハンドラーに対するレンダリングがトリガーされるため、同期的または非同期的にかかわらず、イベントを定期的に処理する。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-126">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="b0d2b-127"><xref:Microsoft.AspNetCore.Components.ComponentBase> によって典型的なライフサイクル イベントに対するレンダリングがトリガーされるため、同期的または非同期的にかかわらず、[`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) や [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) などの典型的なライフサイクル ロジックを実装する。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-127">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="b0d2b-128">ただし、次のセクションで説明するケースでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すことが理にかなっている場合があります。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-128">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="b0d2b-129">非同期ハンドラーに複数の非同期フェーズが含まれる</span><span class="sxs-lookup"><span data-stu-id="b0d2b-129">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="b0d2b-130">Blazor 再レンダリングおよびイベント処理システムの外部の何かからの呼び出しを受信する</span><span class="sxs-lookup"><span data-stu-id="b0d2b-130">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="b0d2b-131">特定のイベントによって再レンダリングされるサブツリーの外部でコンポーネントをレンダリングするには</span><span class="sxs-lookup"><span data-stu-id="b0d2b-131">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="b0d2b-132">非同期ハンドラーに複数の非同期フェーズが含まれる</span><span class="sxs-lookup"><span data-stu-id="b0d2b-132">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="b0d2b-133">クリックのたびにカウントを 4 回更新する以下の `Counter` コンポーネントについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-133">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="b0d2b-134">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b0d2b-134">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="b0d2b-135">.NET でのタスクの定義方法が理由で、<xref:System.Threading.Tasks.Task> の受信側で観察できるのは、中間の非同期状態ではなく、最終的な完了だけとなっています。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-135">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="b0d2b-136">したがって、<xref:Microsoft.AspNetCore.Components.ComponentBase> で再レンダリングをトリガーできるのは、<xref:System.Threading.Tasks.Task> が最初に返されたときと、<xref:System.Threading.Tasks.Task> が最終的に完了したときに限られます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-136">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="b0d2b-137">他の中間点での再レンダリングに対する認識はありません。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-137">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="b0d2b-138">中間点で再レンダリングを行いたい場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-138">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="b0d2b-139">Blazor 再レンダリングおよびイベント処理システムの外部の何かからの呼び出しを受信する</span><span class="sxs-lookup"><span data-stu-id="b0d2b-139">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="b0d2b-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> によって認識されるのは、その独自のライフサイクル メソッドと Blazor でトリガーされるイベントのみです。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="b0d2b-141">ご利用のコード内で発生する可能性がある他のイベントについては、<xref:Microsoft.AspNetCore.Components.ComponentBase> によって認識されません。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="b0d2b-142">たとえば、C# カスタム データ ストアによって発生したイベントは、Blazor によって認識されません。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-142">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="b0d2b-143">そのようなイベントで再レンダリングをトリガーして、更新された値を UI に表示するためには、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-143">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="b0d2b-144"><xref:System.Timers.Timer?displayProperty=fullName> を使用してカウントを一定の間隔で更新し、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出して UI を更新する次の `Counter` コンポーネントについて、別のユース ケースで考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-144">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="b0d2b-145">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="b0d2b-145">`Pages/CounterWithTimerDisposal.razor`:</span></span>

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

<span data-ttu-id="b0d2b-146">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="b0d2b-146">In the preceding example:</span></span>

* <span data-ttu-id="b0d2b-147">コールバックでの `currentCount` への変更は Blazor によって認識されないため、`OnTimerCallback` で <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-147">`OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="b0d2b-148">`OnTimerCallback` は、Blazor で管理される再レンダリング フローまたはイベント通知の外部で実行されます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>
* <span data-ttu-id="b0d2b-149">コンポーネントによって <xref:System.IDisposable> が実装されます。この場合、フレームワークによって `Dispose` メソッドが呼び出されると、<xref:System.Timers.Timer> が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-149">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="b0d2b-150">詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-150">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="b0d2b-151">同様に、コールバックは Blazor の同期コンテキストの外部で呼び出されるため、<xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 内のロジックをラップして、それをレンダラーの同期コンテキストに移動することが必要です。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-151">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="b0d2b-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すことができるのは、レンダラーの同期コンテキストからのみであり、それ以外の場合は例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="b0d2b-153">これは、他の UI フレームワーク内の UI スレッドへのマーシャリングに相当します。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-153">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="b0d2b-154">特定のイベントによって再レンダリングされるサブツリーの外部でコンポーネントをレンダリングするには</span><span class="sxs-lookup"><span data-stu-id="b0d2b-154">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="b0d2b-155">ご利用の UI には、1 つのコンポーネントへのイベントのディスパッチ、何らかの状態の変更、およびイベントを受け取るコンポーネントの子孫ではないまったく異なるコンポーネントの再レンダリング、が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-155">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="b0d2b-156">このシナリオに対処する方法の 1 つとして、"*状態管理*" クラス (たとえば、DI サービス) を複数のコンポーネントに挿入することが挙げられます。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-156">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="b0d2b-157">状態マネージャー上で 1 つのコンポーネントによってメソッドが呼び出されると、別個のコンポーネントによって受信される C# イベントがその状態マネージャーによって引き起こされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-157">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="b0d2b-158">これらの C# イベントは Blazor レンダリング パイプラインの外部にあるため、状態マネージャーのイベントに応答してレンダリングしたい他のコンポーネント上で <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出してください。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-158">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="b0d2b-159">これは、[前のセクション](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)での <xref:System.Timers.Timer?displayProperty=fullName> に関する前のケースと似ています。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-159">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="b0d2b-160">実行コール スタックは一般にレンダラーの同期コンテキスト上に残っているため、<xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> は通常必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-160">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="b0d2b-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> は、ロジックによって同期コンテキストがエスケープされる場合にのみ必要です (<xref:System.Threading.Tasks.Task> 上で <xref:System.Threading.Tasks.Task.ContinueWith%2A> が呼び出される場合や、[`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) を使用して <xref:System.Threading.Tasks.Task> が待機される場合など)。</span><span class="sxs-lookup"><span data-stu-id="b0d2b-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>

---
title: ASP.NET Core Blazor WebAssembly パフォーマンスに関するベスト プラクティス
author: pranavkm
description: ASP.NET Core Blazor WebAssembly アプリのパフォーマンスの向上、および一般的なパフォーマンスの問題回避のためのヒント。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 0753ef0f1cde7bbb45ecc09b97fecb5ce364811c
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024653"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="7c2c1-103">ASP.NET Core Blazor WebAssembly パフォーマンスに関するベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="7c2c1-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="7c2c1-104">作成者: [Pranav Krishnamoorthy](https://github.com/pranavkm)、[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="7c2c1-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="7c2c1-105">Blazor WebAssembly は、最も現実的なアプリケーションの UI シナリオでハイ パフォーマンスを実現できるように、慎重に設計および最適化されています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="7c2c1-106">ただし、最適な結果を得るには、開発者が適切なパターンと機能を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="7c2c1-107">次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-107">Consider the following aspects:</span></span>

* <span data-ttu-id="7c2c1-108">**ランタイムのスループット**:.NET コードは WebAssembly ランタイム内のインタープリター上で実行されるため、CPU スループットが制限されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="7c2c1-109">要求の厳しいシナリオでは、[レンダリング速度の最適化](#optimize-rendering-speed)によってアプリが恩恵を受けます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="7c2c1-110">**起動時間**:アプリによって .NET ランタイムがブラウザーに転送されるため、[アプリケーションのダウンロード サイズを最小化する](#minimize-app-download-size)機能を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="7c2c1-111">レンダリング速度を最適化する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-111">Optimize rendering speed</span></span>

<span data-ttu-id="7c2c1-112">以下のセクションでは、レンダリング ワークロードを最小化し、UI の応答性を向上させるための推奨事項を示します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="7c2c1-113">このアドバイスに従うことで、UI のレンダリング速度を容易に "*10 倍以上改善*" できる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="7c2c1-114">コンポーネントのサブツリーの不要なレンダリングを避ける</span><span class="sxs-lookup"><span data-stu-id="7c2c1-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="7c2c1-115">実行時に、コンポーネントは階層として存在します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="7c2c1-116">ルート コンポーネントには子コンポーネントがあります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-116">A root component has child components.</span></span> <span data-ttu-id="7c2c1-117">さらに、ルートの子にはそれぞれの子コンポーネントがあり、同様に続きます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="7c2c1-118">ユーザーがボタンを選択するなどのイベントが発生すると、Blazor によってどのコンポーネントがレンダリングされるかが、次のように決定されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

1. <span data-ttu-id="7c2c1-119">イベント自体は、イベントのハンドラーをレンダリングしたコンポーネントにディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="7c2c1-120">イベント ハンドラーの実行後、そのコンポーネントは再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-120">After executing the event handler, that component is rerendered.</span></span>
1. <span data-ttu-id="7c2c1-121">いずれかのコンポーネントが再レンダリングされるたびに、その各子コンポーネントに対してパラメーター値の新しいコピーが提供されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
1. <span data-ttu-id="7c2c1-122">各コンポーネントでは、新しいパラメーター値のセットを受け取ったときに、レンダリングするかどうかが選択されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="7c2c1-123">既定では、パラメーター値が変更されている可能性がある場合 (たとえば、変更可能なオブジェクトの場合など)、コンポーネントはレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="7c2c1-124">このシーケンスの最後の 2 つの手順は、コンポーネント階層を下って再帰的に繰り返されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="7c2c1-125">多くの場合、サブツリー全体が再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="7c2c1-126">これは、上位レベルのコンポーネントを対象とするイベントによって、コストがかかる再レンダリング プロセスが発生する可能性があることを意味します。それより下位のすべてを再レンダリングする必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="7c2c1-127">このプロセスを中断し、特定のサブツリーへの再帰的なレンダリングを防止するには、次のいずれかの操作を行います。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

* <span data-ttu-id="7c2c1-128">特定のコンポーネントに対する全パラメーターを、変更不可のプリミティブ型 (たとえば `string`、`int`、`bool`、`DateTime` など) にします。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="7c2c1-129">変更を検出するための組み込みロジックでは、これらのパラメーター値がどれも変更されていない場合、再レンダリングが自動的にスキップされます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="7c2c1-130">`<Customer CustomerId="@item.CustomerId" />` (`CustomerId` は `int` 値) を持つ子コンポーネントをレンダリングする場合、`item.CustomerId` が変更された場合を除き、再レンダリングされることはありません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
* <span data-ttu-id="7c2c1-131">カスタム モデル型、イベントのコールバック、または <xref:Microsoft.AspNetCore.Components.RenderFragment> 値など、非プリミティブ型のパラメーター値を受け入れる必要がある場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> をオーバーライドしてレンダリングするかどうかの決定を制御できます。これについては、「[`ShouldRender` の使用](#use-of-shouldrender)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="7c2c1-132">サブツリー全体の再レンダリングをスキップすることで、イベントが発生したときのレンダリング コストの大半を取り除くことができる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="7c2c1-133">子コンポーネントを具体的に排除して、UI のその部分の再レンダリングをスキップできるようにしたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="7c2c1-134">これは、親コンポーネントのレンダリング コストを削減するための有効な方法です。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="7c2c1-135">`ShouldRender` の使用</span><span class="sxs-lookup"><span data-stu-id="7c2c1-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="7c2c1-136">(パラメーター値に関係なく) 初回のレンダリング後に変更されることがない UI のみのコンポーネントを作成する場合は、`false` を返すように <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を構成します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="7c2c1-137">コンポーネントの再レンダリングが、そのパラメーター値が特定の方法で変更された場合にのみ必要な場合は、プライベート フィールドを使用して変更を検出するために必要な情報を追跡できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="7c2c1-138">次の例の場合、`shouldRender` は、再レンダリングを発生させる必要がある任意の変更のチェックに基づいています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="7c2c1-139">`prevOutboundFlightId` と `prevInboundFlightId` によって、次に発生する可能性のある更新についての情報を追跡します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="7c2c1-140">上のコードでは、イベント ハンドラーで `shouldRender` を `true` に設定して、イベント後にコンポーネントが再レンダリングされるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="7c2c1-141">ほとんどのコンポーネントでは、このレベルの手動制御は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="7c2c1-142">サブツリーのレンダリングに特にコストがかかり、UI の遅延の原因となっている場合にのみ、それらのサブツリーのレンダリングをスキップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="7c2c1-143">詳細については、<xref:blazor/components/lifecycle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="7c2c1-144">仮想化</span><span class="sxs-lookup"><span data-stu-id="7c2c1-144">Virtualization</span></span>

<span data-ttu-id="7c2c1-145">何千ものエントリが含まれたリストやグリッドなど、ループ内で大量の UI をレンダリングする場合、膨大な量のレンダリング操作によって UI のレンダリングに遅延が発生し、ユーザー エクスペリエンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="7c2c1-146">ユーザーがスクロールなしで一度に少数の要素しか表示できない場合、現在表示されていない要素のレンダリングに多くの時間を費やすことは無駄のように見えます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="7c2c1-147">この問題に対処するために、Blazor には `Virtualize` コンポーネントが用意されています。これを使用すると、任意のサイズを持つリストの外観とスクロール動作が作成されますが、現在のスクロール ビューポート内のリスト項目のみがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-147">To address this, Blazor provides the `Virtualize` component that creates the appearance and scroll behaviors of an arbitrarily-large list but only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="7c2c1-148">これはたとえば、アプリに 100,000 個のエントリを持つリストを含めて、一度に表示される 20 項目のレンダリング コストのみを負担することが可能であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="7c2c1-149">`Virtualize` コンポーネントを使用すると、UI パフォーマンスを大幅にスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-149">Use of the `Virtualize` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="7c2c1-150">詳細については、<xref:blazor/components/virtualization> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-150">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="7c2c1-151">軽量で最適化されたコンポーネントを作成する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-151">Create lightweight, optimized components</span></span>

<span data-ttu-id="7c2c1-152">ほとんどの Blazor コンポーネントでは、積極的な最適化を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-152">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="7c2c1-153">これは、ほとんどのコンポーネントは UI で繰り返し表示されず、高頻度で再レンダリングされることがないためです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-153">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="7c2c1-154">たとえば、`@page` コンポーネントや、ダイアログやフォームなどの高レベルの UI 部分を表すコンポーネントは、通常は一度に 1 つだけ表示され、ユーザー ジェスチャへの応答としてのみ再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-154">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="7c2c1-155">これらのコンポーネントによってレンダリングの高ワークロードが作成されることはないため、レンダリングのパフォーマンスについてあまり心配することなく、必要なフレームワーク機能を自由に組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-155">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="7c2c1-156">ただし、大規模に繰り返す必要のあるコンポーネントを構築する一般的なシナリオもあります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-156">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="7c2c1-157">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-157">For example:</span></span>

* <span data-ttu-id="7c2c1-158">入れ子になった大規模なフォームには、何百もの個別の入力、ラベル、その他の要素が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-158">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
* <span data-ttu-id="7c2c1-159">グリッドには何千ものセルが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-159">Grids may have thousands of cells.</span></span>
* <span data-ttu-id="7c2c1-160">散布図には何百万ものデータ ポイントが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-160">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="7c2c1-161">各ユニットを個別のコンポーネント インスタンスとしてモデリングする場合は、その数が多くなり、レンダリング パフォーマンスが重要になります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-161">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="7c2c1-162">このセクションでは、UI の速度と応答性を維持できるように、これらのコンポーネントを軽量にするためのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-162">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="7c2c1-163">何千ものコンポーネント インスタンスを避ける</span><span class="sxs-lookup"><span data-stu-id="7c2c1-163">Avoid thousands of component instances</span></span>

<span data-ttu-id="7c2c1-164">各コンポーネントは、その親と子とは無関係にレンダリングできる独立した島です。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-164">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="7c2c1-165">UI をコンポーネント階層に分割する方法を選択することで、UI レンダリングの粒度を制御できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-165">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="7c2c1-166">これはパフォーマンスにとって良い場合も悪い場合もあります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-166">This can be either good or bad for performance.</span></span>

* <span data-ttu-id="7c2c1-167">UI をより多くのコンポーネントに分割することで、イベントの発生時に再レンダリングする UI の部分を小さくすることができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-167">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="7c2c1-168">たとえば、ユーザーがテーブルの行のボタンをクリックしたときに、ページまたはテーブル全体ではなく、その 1 つの行だけを再レンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-168">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
* <span data-ttu-id="7c2c1-169">ただし、それぞれの追加コンポーネントには、その独立した状態とレンダリング ライフサイクルを処理するための、追加のメモリと CPU オーバーヘッドが伴います。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-169">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="7c2c1-170">.NET 5 で Blazor WebAssembly のパフォーマンスを調整する場合、コンポーネント インスタンスあたり約 0.06 ミリ秒のレンダリング オーバーヘッドが測定されました。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-170">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="7c2c1-171">これは、一般的なノート PC 上で実行される、3 つのパラメーターを受け取るシンプルなコンポーネントに基づいています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-171">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="7c2c1-172">内部的には、オーバーヘッドの大部分が、ディクショナリからのコンポーネントごとの状態の取得と、パラメーターの受け渡しに起因します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-172">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="7c2c1-173">乗算により、2,000 個のコンポーネント インスタンスを新たに追加するとレンダリング時間が 0.12 秒長くなり、ユーザーが UI を低速に感じ始めることがわかります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-173">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="7c2c1-174">コンポーネントをより軽量にしてその数を増やすこともできますが、多くの場合、より強力な手法はコンポーネントの数を増やすことではありません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-174">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="7c2c1-175">以下のセクションでは、2 つの方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-175">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="7c2c1-176">子コンポーネントをその親にインラインで挿入する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-176">Inline child components into their parents</span></span>

<span data-ttu-id="7c2c1-177">子コンポーネントのシーケンスをレンダリングする次のコンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-177">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="7c2c1-178">上のコード例では、`<ChatMessageDisplay>` コンポーネントは次のようなファイル `ChatMessageDisplay.razor` で定義されています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-178">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="7c2c1-179">何千ものメッセージが一度に表示されない限り、上の例は正常に動作し、パフォーマンスも良好です。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-179">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="7c2c1-180">何千ものメッセージを一度に表示するには、個別の `ChatMessageDisplay` コンポーネントを "*取り出さない*" ことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-180">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="7c2c1-181">代わりに、レンダリングを直接親にインラインで挿入します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-181">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="7c2c1-182">これにより、個別にレンダリングすることができなくなる代わりに、コンポーネントごとに多くの子コンポーネントをレンダリングするオーバーヘッドを回避できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-182">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="7c2c1-183">コード内で再利用可能な `RenderFragments` を定義する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-183">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="7c2c1-184">レンダリング ロジックを再利用するための方法として、子コンポーネントを純粋に取り出すことができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-184">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="7c2c1-185">その場合でも、実際のコンポーネントを宣言することなくレンダリング ロジックを再利用できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-185">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="7c2c1-186">コンポーネントの `@code` ブロックでは、UI を出力し、どこからでも呼び出すことができる <xref:Microsoft.AspNetCore.Components.RenderFragment> を定義できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-186">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="7c2c1-187">上の例で示されているように、コンポーネントでは `@code` ブロック内およびその外部のコードからマークアップを出力できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-187">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="7c2c1-188">この方法では、コンポーネントの通常のレンダリング出力内で、必要に応じて複数の場所でレンダリングできる <xref:Microsoft.AspNetCore.Components.RenderFragment> デリゲートを定義します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-188">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="7c2c1-189">デリゲートでは、<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 型の `__builder` というパラメーターを受け取り、Razor コンパイラがレンダリング指示を生成できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-189">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="7c2c1-190">これを複数のコンポーネント間で再利用できるようにするには、`public static` メンバーとして宣言することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-190">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="7c2c1-191">これで、関連付けられていないコンポーネントから呼び出すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-191">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="7c2c1-192">この手法は、コンポーネントごとのオーバーヘッドなしでレンダリングされる、再利用可能なマークアップ スニペットのライブラリを構築する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-192">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="7c2c1-193"><xref:Microsoft.AspNetCore.Components.RenderFragment> デリゲートでは、パラメーターを受け取ることもできます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-193"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="7c2c1-194">前の例の `ChatMessageDisplay` コンポーネントと同等のものを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-194">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="7c2c1-195">この方法には、コンポーネントごとのオーバーヘッドなしでレンダリング ロジックを再利用できるという利点があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-195">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="7c2c1-196">ただし、その UI のサブツリーを個別に更新することはできません。また、コンポーネント境界がないため、親がレンダリングされるときに UI のそのサブツリーのレンダリングをスキップすることもできません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-196">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="7c2c1-197">受け取るパラメーターの数が多すぎないようにする</span><span class="sxs-lookup"><span data-stu-id="7c2c1-197">Don't receive too many parameters</span></span>

<span data-ttu-id="7c2c1-198">1 つのコンポーネントが非常に頻繁に繰り返される場合 (数百回、数千回など) は、各パラメーターを受け渡しするオーバーヘッドが増大していくことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-198">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="7c2c1-199">多すぎるパラメーターによってパフォーマンスが著しく制限されることはまれですが、1 つの要因になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-199">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="7c2c1-200">グリッド内で 1,000 回レンダリングされる `<TableCell>` コンポーネントの場合、渡される追加パラメーターごとに、全体のレンダリング コストが約 15 ミリ秒増大する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-200">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="7c2c1-201">各セルが 10 個のパラメーターを受け取る場合、コンポーネントのレンダリングごとにパラメーターの受け渡しに 150 ミリ秒かかります。したがっておそらく 150,000 ミリ秒 (150 秒) かかり、それ自体が遅い UI の原因になります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-201">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="7c2c1-202">この負荷を軽減するために、カスタム クラスを使用して複数のパラメーターをバンドルすることができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-202">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="7c2c1-203">たとえば、`<TableCell>` コンポーネントで次を受け取るようにします。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-203">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="7c2c1-204">上の例では、`Data` はすべてのセルで異なりますが、`Options` はすべてのセルで共通です。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-204">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="7c2c1-205">もちろん、`<TableCell>` コンポーネントを使用せず、代わりにそのロジックを親コンポーネントにインラインで挿入した方が改善される場合もあります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-205">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="7c2c1-206">カスケード型パラメーターが固定されていることを確認する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-206">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="7c2c1-207">`<CascadingValue>` コンポーネントには、`IsFixed` という省略可能なパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-207">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

* <span data-ttu-id="7c2c1-208">`IsFixed` の値が `false` (既定値) の場合、カスケードされた値のすべての受信者は、変更通知を受信するためのサブスクリプションを設定します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-208">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="7c2c1-209">この場合、サブスクリプションの追跡により、各 `[CascadingParameter]` は通常の `[Parameter]` よりも **大幅にコストが高くなります**。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-209">In this case, each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
* <span data-ttu-id="7c2c1-210">`IsFixed` の値が `true` (`<CascadingValue Value="@someValue" IsFixed="true">` など) の場合、受信者は初期値を受け取りますが、更新を受信するためのサブスクリプションを設定 "*しません*"。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-210">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="7c2c1-211">この場合、各 `[CascadingParameter]` は軽量であり、通常の `[Parameter]` よりも **コストが高くなることはありません**。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-211">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="7c2c1-212">したがって、可能な限り、カスケードされた値には `IsFixed="true"` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-212">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="7c2c1-213">指定される値が時間の経過と共に変化しない場合は、常にこれを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-213">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="7c2c1-214">コンポーネントによって `this` がカスケードされた値として渡される一般的なパターンでは、`IsFixed="true"` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-214">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="7c2c1-215">これにより、カスケードされた値を受け取る他のコンポーネントが多数ある場合に大きな違いが生じます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-215">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="7c2c1-216">詳細については、<xref:blazor/components/cascading-values-and-parameters> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-216">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="7c2c1-217">`CaptureUnmatchedValues` で属性スプラッティングを避ける</span><span class="sxs-lookup"><span data-stu-id="7c2c1-217">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="7c2c1-218">コンポーネントでは、<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> フラグを使用して、"一致しない" パラメーター値を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-218">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="7c2c1-219">この方法では、任意の追加属性を要素に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-219">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="7c2c1-220">ただし、レンダラーで次を行う必要があるため、非常にコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-220">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="7c2c1-221">指定されたすべてのパラメーターを既知のパラメーターのセットと照合して、ディクショナリを構築する。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-221">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="7c2c1-222">同じ属性の複数のコピーが相互に上書きされているかどうかを追跡する。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-222">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="7c2c1-223">頻繁に繰り返されないコンポーネントなど、パフォーマンスが重要でないコンポーネントでは <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> を自由に使用できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-223">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="7c2c1-224">ただし、大規模なリスト内の項目やグリッド内のセルなど、大規模にレンダリングされるコンポーネントの場合は、属性スプラッティングを避けるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-224">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="7c2c1-225">詳細については、<xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-225">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="7c2c1-226">手動で `SetParametersAsync` を実装する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-226">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="7c2c1-227">コンポーネントごとのレンダリングにかかるオーバーヘッドの主な側面の 1 つは、入力パラメーター値を `[Parameter]` プロパティに書き込むことです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-227">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="7c2c1-228">レンダラーでは、リフレクションを使用してこれを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-228">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="7c2c1-229">これは多少最適化されていますが、WebAssembly ランタイムに対する JIT サポートの不在により限界があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-229">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="7c2c1-230">極端なケースでは、リフレクションを使用せずに、独自のパラメーター設定ロジックを手動で実装したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-230">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="7c2c1-231">これは、次の場合に該当します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-231">This may be applicable when:</span></span>

* <span data-ttu-id="7c2c1-232">非常に頻繁にレンダリングされるコンポーネントがある (たとえば、UI 内にそのコピーが何百または何千個ある)。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-232">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
* <span data-ttu-id="7c2c1-233">それが多くのパラメーターを受け取る。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-233">It accepts many parameters.</span></span>
* <span data-ttu-id="7c2c1-234">パラメーターを受け取るオーバーヘッドが UI の応答性に目に見える影響を与えていることがわかる。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-234">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="7c2c1-235">このような場合は、コンポーネントの仮想 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> メソッドをオーバーライドし、独自のコンポーネント固有ロジックを実装できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-235">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="7c2c1-236">次の例では、すべてのディクショナリ参照を意図的に回避しています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-236">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="7c2c1-237">上のコードでは、基底クラス <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> を返すと、パラメーターを再度割り当てずに通常のライフサイクル メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-237">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="7c2c1-238">上のコードでわかるように、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> のオーバーライドとカスタム ロジックの提供は複雑で手間がかかるため、この方法は一般にお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-238">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="7c2c1-239">極端なケースでは、これによってレンダリング パフォーマンスを 20-25% 向上させることができますが、この方法は前述のシナリオでのみ検討してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-239">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="7c2c1-240">イベントをすぐにトリガーしない</span><span class="sxs-lookup"><span data-stu-id="7c2c1-240">Don't trigger events too rapidly</span></span>

<span data-ttu-id="7c2c1-241">一部のブラウザー イベントは非常に頻繁に発生します。たとえば `onmousemove` や `onscroll` などは、1 秒間に数十回または数百回発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-241">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="7c2c1-242">ほとんどの場合、これほど頻繁に UI 更新を実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-242">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="7c2c1-243">これを行おうとすると、UI の応答性が損なわれたり、過剰な CPU 時間が消費されたりする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-243">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="7c2c1-244">ネイティブの `@onmousemove` や `@onscroll` イベントを使用するのではなく、JS 相互運用を使用して、発生する頻度の低いコールバックを登録することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-244">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="7c2c1-245">たとえば、次のコンポーネント (`MyComponent.razor`) にはマウスの位置が表示されますが、500 ミリ秒ごとに最大で 1 回しか更新されません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-245">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="7c2c1-246">対応する JavaScript コード (`index.html` ページに配置したり、ES6 モジュールとして読み込んだりできます) によって、実際の DOM イベント リスナーが登録されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-246">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="7c2c1-247">この例の場合、イベント リスナーでは [Lodash の `throttle` 関数](https://lodash.com/docs/4.17.15#throttle)を使用して、呼び出し率を制限しています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-247">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="7c2c1-248">この手法は Blazor Server ではさらに重要になる可能性があります。各イベントの呼び出しに、ネットワーク経由でのメッセージの配信が伴うためです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-248">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="7c2c1-249">これが Blazor WebAssembly において価値を持つのは、レンダリング作業の量を大幅に削減できるためです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-249">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="7c2c1-250">JavaScript 相互運用の速度を最適化する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-250">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="7c2c1-251">.NET と JavaScript の間の呼び出しには、次の理由から追加のオーバーヘッドが伴います。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-251">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

* <span data-ttu-id="7c2c1-252">既定では、呼び出しは非同期です。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-252">By default, calls are asynchronous.</span></span>
* <span data-ttu-id="7c2c1-253">既定では、パラメーターと戻り値は JSON でシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-253">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="7c2c1-254">これは .NET と JavaScript の型の間にわかりやすい変換メカニズムを提供するためです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-254">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="7c2c1-255">さらに Blazor Server では、これらの呼び出しはネットワーク経由で渡されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-255">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="7c2c1-256">過度に細かい呼び出しを避ける</span><span class="sxs-lookup"><span data-stu-id="7c2c1-256">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="7c2c1-257">各呼び出しにはある程度のオーバーヘッドが伴うため、呼び出しの回数を減らすことが有益な場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-257">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="7c2c1-258">ブラウザーの `localStorage` ストアに項目のコレクションを格納する次のコードについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-258">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="7c2c1-259">上の例では、各項目に対して個別の JS 相互運用呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-259">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="7c2c1-260">代わりに、次の方法では JS 相互運用を 1 回の呼び出しに減らしています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-260">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="7c2c1-261">対応する JavaScript 関数は次のように定義されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-261">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="7c2c1-262">Blazor WebAssembly の場合、これは通常、多数の JS 相互運用呼び出しを行う場合にのみ重要になります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-262">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="7c2c1-263">同期呼び出しを行うことを検討する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-263">Consider making synchronous calls</span></span>

<span data-ttu-id="7c2c1-264">JavaScript 相互運用呼び出しは、呼び出されているコードが同期であるか非同期であるかに関係なく、既定で非同期です。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-264">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="7c2c1-265">これは、コンポーネントが Blazor WebAssembly と Blazor Server の両方と互換性を持つようにするためです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-265">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="7c2c1-266">Blazor Server では、すべての JavaScript 相互運用呼び出しはネットワーク接続を介して送信されるため、非同期である必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-266">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="7c2c1-267">アプリが Blazor WebAssembly でのみ実行されることが確実にわかっている場合は、同期 JavaScript 相互運用呼び出しの実行を選択できます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-267">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="7c2c1-268">これにより、非同期呼び出しを行う場合よりもオーバーヘッドがわずかに減少し、レンダリング サイクルが少なくなる可能性があります。結果を待機する間の中間状態が存在しないためです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-268">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="7c2c1-269">.NET から JavaScript への同期呼び出しを行うには、<xref:Microsoft.JSInterop.IJSRuntime> を <xref:Microsoft.JSInterop.IJSInProcessRuntime> にキャストします。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-269">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7c2c1-270">`IJSObjectReference` を使用する場合は、`IJSInProcessObjectReference` にキャストすることによって同期呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-270">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="7c2c1-271">JavaScript から .NET への同期呼び出しを行うには、`DotNet.invokeMethodAsync` ではなく `DotNet.invokeMethod` を使用します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-271">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="7c2c1-272">同期呼び出しは、次の場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-272">Synchronous calls work if:</span></span>

* <span data-ttu-id="7c2c1-273">アプリが Blazor Server ではなく Blazor WebAssembly で実行されている。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-273">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="7c2c1-274">呼び出された関数から同期的に値が返される (`async` メソッドではなく、.NET <xref:System.Threading.Tasks.Task> や JavaScript `Promise` が返されない)。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-274">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="7c2c1-275">詳細については、<xref:blazor/call-javascript-from-dotnet> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-275">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="7c2c1-276">マーシャリング解除された呼び出しを行うことを検討する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-276">Consider making unmarshalled calls</span></span>

<span data-ttu-id="7c2c1-277">Blazor WebAssembly で実行する場合、.NET から JavaScript へのマーシャリング解除された呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-277">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="7c2c1-278">これらは、引数または戻り値の JSON シリアル化を実行しない同期呼び出しです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-278">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="7c2c1-279">.NET と JavaScript の表現の間のメモリ管理と翻訳のすべての側面は、開発者に任されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-279">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="7c2c1-280">`IJSUnmarshalledRuntime` を使用すると JS 相互運用アプローチのオーバーヘッドが最小限になりますが、これらの API とのやり取りに必要な JavaScript API は現在ドキュメントに記載されていません。また、今後のリリースでの破壊的変更の対象となることがあります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-280">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="7c2c1-281">アプリのダウンロード サイズを最小限にする</span><span class="sxs-lookup"><span data-stu-id="7c2c1-281">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="7c2c1-282">中間言語 (IL) のトリミング</span><span class="sxs-lookup"><span data-stu-id="7c2c1-282">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="7c2c1-283">[使用されていないアセンブリを Blazor WebAssembly アプリからトリミング](xref:blazor/host-and-deploy/configure-trimmer)すると、アプリのバイナリで使用されていないコードを削除して、アプリのサイズを縮小することができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-283">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="7c2c1-284">既定では、アプリケーションの発行時にトリマーが実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-284">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="7c2c1-285">トリミングを活用するには、[-c|--configuration](/dotnet/core/tools/dotnet-publish#options) オプションを `Release` に設定した状態で [`dotnet publish`](/dotnet/core/tools/dotnet-publish) コマンドを使用して、展開用にアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-285">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="7c2c1-286">中間言語 (IL) のリンク</span><span class="sxs-lookup"><span data-stu-id="7c2c1-286">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="7c2c1-287">[Blazor WebAssembly アプリをリンク](xref:blazor/host-and-deploy/configure-linker)すると、アプリのバイナリで使用されていないコードをトリミングすることで、アプリのサイズが縮小されます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-287">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="7c2c1-288">既定では、中間言語 (IL) リンカーは、`Release` 構成でビルドする場合にのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-288">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="7c2c1-289">これを活用するには、[-c|--configuration](/dotnet/core/tools/dotnet-publish#options) オプションを `Release` に設定した状態で [`dotnet publish`](/dotnet/core/tools/dotnet-publish) コマンドを使用して、展開用にアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-289">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="7c2c1-290">System.Text.Json を使用する</span><span class="sxs-lookup"><span data-stu-id="7c2c1-290">Use System.Text.Json</span></span>

<span data-ttu-id="7c2c1-291">Blazor の JS 相互運用の実装は <xref:System.Text.Json> に依存します。これは、メモリ割り当てが少ない高パフォーマンスの JSON シリアル化ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-291">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="7c2c1-292"><xref:System.Text.Json> を使用しても、1 つ以上の代替 JSON ライブラリを追加したときにアプリのペイロードのサイズは増加しません。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-292">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="7c2c1-293">移行のガイダンスについては、「[`Newtonsoft.Json` から `System.Text.Json` に移行する方法](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-293">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="7c2c1-294">アセンブリの遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="7c2c1-294">Lazy load assemblies</span></span>

<span data-ttu-id="7c2c1-295">アセンブリがルートによって要求されたときに、実行時にアセンブリを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-295">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="7c2c1-296">詳細については、「<xref:blazor/webassembly-lazy-load-assemblies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-296">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="7c2c1-297">[圧縮]</span><span class="sxs-lookup"><span data-stu-id="7c2c1-297">Compression</span></span>

<span data-ttu-id="7c2c1-298">Blazor WebAssembly アプリが公開されると、公開中に出力が静的に圧縮されてアプリのサイズが縮小され、実行時の圧縮に必要なオーバーヘッドがなくなります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-298">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="7c2c1-299">Blazor は、コンテンツ ネゴシエーションの実行および静的に圧縮されたファイルの提供に関して、サーバーに依存します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-299">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="7c2c1-300">アプリが展開されたら、アプリが圧縮ファイルを提供していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-300">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="7c2c1-301">ブラウザーの開発者ツールの [ネットワーク] タブを調べ、ファイルが `Content-Encoding: br` または `Content-Encoding: gz` で提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-301">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="7c2c1-302">ホストが圧縮ファイルを提供していない場合は、「<xref:blazor/host-and-deploy/webassembly#compression>」に記載されている手順に従ってください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-302">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="7c2c1-303">未使用の機能を無効にする</span><span class="sxs-lookup"><span data-stu-id="7c2c1-303">Disable unused features</span></span>

<span data-ttu-id="7c2c1-304">Blazor WebAssembly のランタイムには、次の .NET 機能が含まれています。これらの機能はアプリで必要がない場合、ペイロード サイズを小さくするために無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-304">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="7c2c1-305">データ ファイルは、タイムゾーン情報を正しく保つために含まれています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-305">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="7c2c1-306">アプリでこの機能を必要としない場合は、アプリのプロジェクト ファイル内の `BlazorEnableTimeZoneSupport` MSBuild プロパティを `false` に設定して無効にすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-306">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7c2c1-307">既定では、Blazor WebAssembly には、ユーザーのカルチャで日付や通貨などの値を表示するために必要なグローバリゼーション リソースがあります。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-307">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="7c2c1-308">アプリがローカライズを必要としない場合は、`en-US` カルチャに基づく[インバリアント カルチャをサポートするようにアプリを構成する](xref:blazor/globalization-localization)ことができます。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-308">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7c2c1-309"><xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> などの API を正常に動作させるために、照合順序情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-309">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="7c2c1-310">アプリで照合順序情報を必要としないことが確実な場合は、アプリのプロジェクト ファイル内の `BlazorWebAssemblyPreserveCollationData` MSBuild プロパティを `false` に設定して無効にすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="7c2c1-310">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end

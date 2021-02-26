---
title: ASP.NET Core Blazor コンポーネントの仮想化
author: guardrex
description: ASP.NET Core Blazor アプリでコンポーネントの仮想化を使用する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: d9fc767a4b5160c616053b075ba92194bcffa275
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280012"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="ab464-103">ASP.NET Core Blazor コンポーネントの仮想化</span><span class="sxs-lookup"><span data-stu-id="ab464-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="ab464-104">Blazor フレームワークに組み込まれている仮想化サポートを使用して、コンポーネント レンダリングの認識されるパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="ab464-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="ab464-105">仮想化は、UI レンダリングを現在表示されている部分のみに制限するための手法です。</span><span class="sxs-lookup"><span data-stu-id="ab464-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="ab464-106">たとえば、仮想化が有用なのは、アプリで項目の長いリストや項目の一部のみをレンダリングする必要があるが、表示する必要があるのはどんなときでも項目のサブセットのみである場合です。</span><span class="sxs-lookup"><span data-stu-id="ab464-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="ab464-107">Blazor には、仮想化をアプリのコンポーネントに追加するために使用できる [`Virtualize` コンポーネント](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)が用意されています。</span><span class="sxs-lookup"><span data-stu-id="ab464-107">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="ab464-108">`Virtualize` コンポーネントは、次の場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="ab464-108">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="ab464-109">ループ内の一連のデータ項目をレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="ab464-109">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="ab464-110">スクロールが原因でほとんどの項目が表示されない。</span><span class="sxs-lookup"><span data-stu-id="ab464-110">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="ab464-111">レンダリングされる項目のサイズはまったく同じ。</span><span class="sxs-lookup"><span data-stu-id="ab464-111">The rendered items are exactly the same size.</span></span> <span data-ttu-id="ab464-112">ユーザーが任意の点にスクロールすると、コンポーネントでは表示する項目を計算できます。</span><span class="sxs-lookup"><span data-stu-id="ab464-112">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="ab464-113">仮想化を使用しない場合は、一般的なリストで、C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループを使用してリストの各項目を表示できます。</span><span class="sxs-lookup"><span data-stu-id="ab464-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="ab464-114">リストに何千もの項目が含まれている場合は、リストの表示に時間がかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="ab464-114">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="ab464-115">UI の表示が明らかに遅いとユーザーが感じる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ab464-115">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="ab464-116">リスト内の各項目を一度に表示するのではなく、[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループを `Virtualize` コンポーネントに置き換え、<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> を使用して固定の項目ソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="ab464-116">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ab464-117">現在表示される項目のみがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="ab464-117">Only the items that are currently visible are rendered:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="ab464-118">`Context` を使用してコンポーネントにコンテキストを指定しない場合は、項目コンテンツ テンプレートで `context` 値を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab464-118">If not specifying a context to the component with `Context`, use the `context` value in the item content template:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="ab464-119">要素およびコンポーネントへのモデル オブジェクトのマッピング プロセスは、[`@key`](xref:mvc/views/razor#key) ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="ab464-119">The mapping process of model objects to elements and components can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="ab464-120">`@key` により、比較アルゴリズムで、キーの値に基づいて要素またはコンポーネントが確実に保持されます。</span><span class="sxs-lookup"><span data-stu-id="ab464-120">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="ab464-121">詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab464-121">For more information, see the following articles:</span></span>
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [<span data-ttu-id="ab464-122">ASP.NET Core の Razor 構文リファレンス</span><span class="sxs-lookup"><span data-stu-id="ab464-122">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor#key)

<span data-ttu-id="ab464-123">`Virtualize` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="ab464-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="ab464-124">コンテナーの高さとレンダリングする項目のサイズに基づいて、レンダリングする項目の数を計算します。</span><span class="sxs-lookup"><span data-stu-id="ab464-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="ab464-125">ユーザーがスクロールするときに項目を再計算し、再レンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ab464-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="ab464-126">コレクションからすべてのデータをダウンロードするのでなく、現在の表示領域に対応するレコードの一部だけを外部 API からフェッチします。</span><span class="sxs-lookup"><span data-stu-id="ab464-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="ab464-127">`Virtualize` コンポーネントの項目コンテンツには次を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ab464-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="ab464-128">前の例に含まれていたプレーン HTML および Razor コード。</span><span class="sxs-lookup"><span data-stu-id="ab464-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="ab464-129">1 つまたは複数の Razor コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="ab464-129">One or more Razor components.</span></span>
* <span data-ttu-id="ab464-130">HTML/Razor および Razor コンポーネントの混合。</span><span class="sxs-lookup"><span data-stu-id="ab464-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="ab464-131">項目プロバイダー デリゲート</span><span class="sxs-lookup"><span data-stu-id="ab464-131">Item provider delegate</span></span>

<span data-ttu-id="ab464-132">すべての項目をメモリに読み込みたいわけではない場合は、要求された項目をオンデマンドで非同期的に取得するコンポーネントの <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> パラメーターに項目プロバイダー デリゲート メソッドを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ab464-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="ab464-133">次の例では、`LoadEmployees` メソッドによって、項目が `Virtualize` コンポーネントに提供されます。</span><span class="sxs-lookup"><span data-stu-id="ab464-133">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="ab464-134">項目プロバイダーは、特定の開始インデックスを開始位置として、必要な項目数を指定する <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ab464-134">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="ab464-135">次に、項目プロバイダーにより、要求される項目がデータベースまたは他のサービスから取得され、それらが <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> として、合計項目数と共に返されます。</span><span class="sxs-lookup"><span data-stu-id="ab464-135">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="ab464-136">項目プロバイダーでは、項目を要求ごとに取得するか、すぐに使用できるようにキャッシュするかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="ab464-136">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="ab464-137">`Virtualize` コンポーネントでは、そのパラメーターから **1 つの項目ソース** のみを受け入れることができるので、項目プロバイダーを同時に使用して `Items` にコレクションを割り当てることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="ab464-137">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="ab464-138">両方が割り当てられている場合は、コンポーネントのパラメーターが実行時に設定されると <xref:System.InvalidOperationException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="ab464-138">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="ab464-139">次の `LoadEmployees` メソッドの例では、`EmployeeService` (表示されていません) から従業員が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ab464-139">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<span data-ttu-id="ab464-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> にデータを再要求するように、<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> からコンポーネントに指示が出されます。</span><span class="sxs-lookup"><span data-stu-id="ab464-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="ab464-141">これは、外部データが変更される場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ab464-141">This is useful when external data changes.</span></span> <span data-ttu-id="ab464-142"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> を使用する場合、これを呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ab464-142">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="ab464-143">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="ab464-143">Placeholder</span></span>

<span data-ttu-id="ab464-144">リモート データ ソースに項目を要求には時間がかかる場合があるため、項目のコンテンツを含むプレースホルダーをレンダリングするオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="ab464-144">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="ab464-145">項目データが使用可能になるまでコンテンツを表示するには、<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab464-145">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="ab464-146">リストの項目テンプレートを設定するには、<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab464-146">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="ab464-147">アイテムのサイズ</span><span class="sxs-lookup"><span data-stu-id="ab464-147">Item size</span></span>

<span data-ttu-id="ab464-148">各項目の高さ (ピクセル単位) は <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> で設定できます (既定値: 50):</span><span class="sxs-lookup"><span data-stu-id="ab464-148">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="ab464-149">既定では、初期レンダリングが行われた "*後*" に、`Virtualize` コンポーネントによって実際のレンダリング サイズが測定されます。</span><span class="sxs-lookup"><span data-stu-id="ab464-149">By default, the `Virtualize` component measures the actual rendering size *after* the initial render occurs.</span></span> <span data-ttu-id="ab464-150">正確な初期レンダリングのパフォーマンスを支援し、ページを再読み込みするための正しいスクロール位置を確保するために、<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> を使用して事前に正確な項目のサイズを提供します。</span><span class="sxs-lookup"><span data-stu-id="ab464-150">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="ab464-151">既定の <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> によって一部の項目が現在表示されているビューの外側にレンダーされる場合、2 回目の再レンダリングがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="ab464-151">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="ab464-152">仮想化されたリストでブラウザーのスクロール位置を正しく維持するには、最初のレンダリングが正しいことが必要です。</span><span class="sxs-lookup"><span data-stu-id="ab464-152">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="ab464-153">そうでない場合、間違った項目がユーザーに表示されるおそれがあります。</span><span class="sxs-lookup"><span data-stu-id="ab464-153">If not, users might view the wrong items.</span></span> 

## <a name="overscan-count"></a><span data-ttu-id="ab464-154">オーバースキャン数</span><span class="sxs-lookup"><span data-stu-id="ab464-154">Overscan count</span></span>

<span data-ttu-id="ab464-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> を使用して、表示領域の前後にレンダリングされる追加項目の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="ab464-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="ab464-156">この設定は、スクロール中のレンダリングの頻度を減らすのに利用できます。</span><span class="sxs-lookup"><span data-stu-id="ab464-156">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="ab464-157">ただし、値を大きくすると、ページにレンダリングされる要素が多くなります (既定値:3)。</span><span class="sxs-lookup"><span data-stu-id="ab464-157">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="ab464-158">状態変更</span><span class="sxs-lookup"><span data-stu-id="ab464-158">State changes</span></span>

<span data-ttu-id="ab464-159">`Virtualize` コンポーネントによってレンダリングされる項目を変更する場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出して、強制的にコンポーネントを再評価して再レンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ab464-159">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="ab464-160">詳細については、「<xref:blazor/components/rendering>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab464-160">For more information, see <xref:blazor/components/rendering>.</span></span>

---
title: ASP.NET Core Blazor の値とパラメーターのカスケード
author: guardrex
description: 先祖のコンポーネントから子孫のコンポーネントにデータをフローさせる方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280243"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="a5008-103">ASP.NET Core Blazor の値とパラメーターのカスケード</span><span class="sxs-lookup"><span data-stu-id="a5008-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="a5008-104">"*カスケード値とパラメーター*" の使用は、コンポーネント階層で先祖コンポーネントから下位の任意の数の子孫コンポーネントにデータをフローさせる便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="a5008-104">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="a5008-105">カスケード値およびパラメーターでは、[コンポーネント パラメーター](xref:blazor/components/index#component-parameters)とは異なり、データが使用される各子孫コンポーネントに属性を割り当てる必要がありません。</span><span class="sxs-lookup"><span data-stu-id="a5008-105">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="a5008-106">また、カスケード値とパラメーターを使用すると、コンポーネント階層全体でコンポーネントを相互連携させることができます。</span><span class="sxs-lookup"><span data-stu-id="a5008-106">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="a5008-107">`CascadingValue` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a5008-107">`CascadingValue` component</span></span>

<span data-ttu-id="a5008-108">先祖コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供する Blazor フレームワークの [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) コンポーネントを使用して、カスケード値を提供します。</span><span class="sxs-lookup"><span data-stu-id="a5008-108">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="a5008-109">次の例では、子コンポーネントのボタンに CSS 形式のクラスを提供する、レイアウト コンポーネントのコンポーネント階層におけるテーマ情報のフローを示しています。</span><span class="sxs-lookup"><span data-stu-id="a5008-109">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="a5008-110">次の `ThemeInfo` C# クラスは、`UIThemeClasses` という名前のフォルダーに配置され、テーマ情報を指定します。</span><span class="sxs-lookup"><span data-stu-id="a5008-110">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="a5008-111">このセクションの例では、アプリの名前空間は `BlazorSample` です。</span><span class="sxs-lookup"><span data-stu-id="a5008-111">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="a5008-112">自分独自のサンプル アプリでコードを試す場合は、アプリの名前空間をお使いのサンプル アプリの名前空間に変更します。</span><span class="sxs-lookup"><span data-stu-id="a5008-112">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="a5008-113">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="a5008-113">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="a5008-114">次の[レイアウト コンポーネント](xref:blazor/layouts)は、<xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> プロパティのレイアウト本体を構成するすべてのコンポーネントに、テーマ情報 (`ThemeInfo`) をカスケード値として指定しています。</span><span class="sxs-lookup"><span data-stu-id="a5008-114">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="a5008-115">`ButtonClass` には、Bootstrap ボタン形式の [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) 値が割り当てられています。</span><span class="sxs-lookup"><span data-stu-id="a5008-115">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="a5008-116">`ButtonClass` プロパティは、`ThemeInfo` カスケード値を介し、コンポーネント階層内のすべての子孫コンポーネントで使用できます。</span><span class="sxs-lookup"><span data-stu-id="a5008-116">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="a5008-117">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="a5008-117">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="a5008-118">`[CascadingParameter]` 属性</span><span class="sxs-lookup"><span data-stu-id="a5008-118">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="a5008-119">子孫コンポーネントでは、[`[CascadingParameter]` 属性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)を使用してカスケード型パラメーターを宣言し、カスケード値を使用します。</span><span class="sxs-lookup"><span data-stu-id="a5008-119">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="a5008-120">カスケード値は、**型で** カスケード型パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="a5008-120">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="a5008-121">同じ型の複数の値のカスケードについては、後でこの記事の「[複数の値のカスケード](#cascade-multiple-values)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="a5008-121">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="a5008-122">次のコンポーネントは、オプションで同じ `ThemeInfo` 名を使用してカスケード型パラメーターに `ThemeInfo` カスケード値をバインドします。</span><span class="sxs-lookup"><span data-stu-id="a5008-122">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="a5008-123">このパラメーターは、 **`Increment Counter (Themed)`** ボタンの CSS クラスを設定するのに使用されます。</span><span class="sxs-lookup"><span data-stu-id="a5008-123">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="a5008-124">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="a5008-124">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="a5008-125">複数の値のカスケード</span><span class="sxs-lookup"><span data-stu-id="a5008-125">Cascade multiple values</span></span>

<span data-ttu-id="a5008-126">同じサブツリー内で同じ型の値を複数カスケードするには、各 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) コンポーネントとそれに対応する [`[CascadingParameter]` 属性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)に一意の <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="a5008-126">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="a5008-127">次の例では、2 つの [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) コンポーネントが、`CascadingType` の異なるインスタンスをカスケードしています。</span><span class="sxs-lookup"><span data-stu-id="a5008-127">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="a5008-128">子孫コンポーネントで、カスケードされたパラメーターはそれらのカスケードされた値を、次のように <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> を使用して、先祖コンポーネントから受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a5008-128">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="a5008-129">コンポーネント階層に渡ってデータを渡す</span><span class="sxs-lookup"><span data-stu-id="a5008-129">Pass data across a component hierarchy</span></span>

<span data-ttu-id="a5008-130">カスケード型パラメーターにより、コンポーネントがコンポーネント階層間でデータを渡せるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="a5008-130">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="a5008-131">タブ セット コンポーネントによって一連の個別タブが維持される、次の UI タブ セットの例を考えてみてください。</span><span class="sxs-lookup"><span data-stu-id="a5008-131">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="a5008-132">このセクションの例では、アプリの名前空間は `BlazorSample` です。</span><span class="sxs-lookup"><span data-stu-id="a5008-132">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="a5008-133">自分独自のサンプル アプリでコードを試す場合は、名前空間をお使いのサンプル アプリの名前空間に変更します。</span><span class="sxs-lookup"><span data-stu-id="a5008-133">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="a5008-134">`UIInterfaces` という名前のフォルダーに、タブが実装する `ITab` インターフェイスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a5008-134">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="a5008-135">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="a5008-135">`UIInterfaces/ITab.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

<span data-ttu-id="a5008-136">一連のタブは、次の `TabSet` コンポーネントによって維持されます。</span><span class="sxs-lookup"><span data-stu-id="a5008-136">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="a5008-137">リスト (`<ul>...</ul>`) のリスト項目 (`<li>...</li>`) は、このセクションで後で作成するタブ セットの `Tab` コンポーネントによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a5008-137">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="a5008-138">子 `Tab` コンポーネントは、`TabSet` にパラメーターとして明示的に渡されません。</span><span class="sxs-lookup"><span data-stu-id="a5008-138">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="a5008-139">代わりに、子 `Tab` コンポーネントは、`TabSet` の子コンテンツに含まれます。</span><span class="sxs-lookup"><span data-stu-id="a5008-139">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="a5008-140">ただし、ヘッダーとアクティブなタブをレンダリングできるように、`TabSet` は、各 `Tab` コンポーネントをまだ参照する必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントでは、*それ自体をカスケード値として指定し*、その後に子孫 `Tab` コンポーネントによって取得できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a5008-140">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="a5008-141">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="a5008-141">`Shared/TabSet.razor`:</span></span>

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="a5008-142">子孫 `Tab` コンポーネントは、カスケード型パラメーターとして含まれる `TabSet` を取得します。</span><span class="sxs-lookup"><span data-stu-id="a5008-142">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="a5008-143">`Tab` コンポーネントは、アクティブなタブの設定のために自身を `TabSet` と座標に追加します。</span><span class="sxs-lookup"><span data-stu-id="a5008-143">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="a5008-144">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="a5008-144">`Shared/Tab.razor`:</span></span>

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

<span data-ttu-id="a5008-145">次の `ExampleTabSet` コンポーネントは、3 つの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用しています。</span><span class="sxs-lookup"><span data-stu-id="a5008-145">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="a5008-146">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="a5008-146">`Pages/ExampleTabSet.razor`:</span></span>

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```

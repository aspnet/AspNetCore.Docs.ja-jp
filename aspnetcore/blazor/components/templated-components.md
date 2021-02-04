---
title: ASP.NET Core Blazor テンプレート コンポーネント
author: guardrex
description: テンプレート コンポーネントで 1 つまたは複数の UI テンプレートをパラメーターとして受け取る方法について学習します。これは、コンポーネントのレンダリング ロジックの一部として使用できます。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: 28f98596046877672b2935cb1f0feb508548398d
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529931"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="d2a3f-103">ASP.NET Core Blazor テンプレート コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d2a3f-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="d2a3f-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d2a3f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d2a3f-105">テンプレート コンポーネントは、1 つまたは複数の UI テンプレートをパラメーターとして受け取るコンポーネントです。その後、コンポーネントのレンダリング ロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="d2a3f-106">テンプレート コンポーネントを使用すると、通常のコンポーネントよりも再利用しやすい上位レベルのコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="d2a3f-107">いくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-107">A couple of examples include:</span></span>

* <span data-ttu-id="d2a3f-108">テーブルのヘッダー、行、フッターのテンプレートをユーザーが指定できるようにするテーブル コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d2a3f-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="d2a3f-109">リスト内の項目をレンダリングするためのテンプレートをユーザーが指定できるようにするリスト コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d2a3f-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="d2a3f-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="d2a3f-111">テンプレート パラメーター</span><span class="sxs-lookup"><span data-stu-id="d2a3f-111">Template parameters</span></span>

<span data-ttu-id="d2a3f-112"><xref:Microsoft.AspNetCore.Components.RenderFragment> または <xref:Microsoft.AspNetCore.Components.RenderFragment%601> の型の 1 つまたは複数のコンポーネント パラメーターを指定することで、テンプレート コンポーネントが定義されます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="d2a3f-113">レンダー フラグメントは、レンダリングする UI のセグメントを表します。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="d2a3f-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> は、レンダー フラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="d2a3f-115">`TableTemplate` コンポーネント (`TableTemplate.razor`):</span><span class="sxs-lookup"><span data-stu-id="d2a3f-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="d2a3f-116">テンプレート コンポーネントを使用する場合、テンプレート パラメーターは、パラメーターの名前と一致する子要素を使用して指定できます (次の例では `TableHeader` と `RowTemplate`)。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="d2a3f-117">ジェネリック型の制約は、将来のリリースでサポートされる予定です。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="d2a3f-118">詳細については、[ジェネリック型の制約の許可 (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="d2a3f-119">テンプレート コンテキスト パラメーター</span><span class="sxs-lookup"><span data-stu-id="d2a3f-119">Template context parameters</span></span>

<span data-ttu-id="d2a3f-120">要素として渡される型 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> のコンポーネント引数には、`context` という名前の暗黙的なパラメーターがありますが (たとえば、上記のコード サンプルの `@context.PetId`)、子要素の `Context` 属性を使用してパラメーター名を変更できます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="d2a3f-121">次の例では、`RowTemplate` 要素の `Context` 属性で `pet` パラメーターを指定しています。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

<span data-ttu-id="d2a3f-122">または、コンポーネント要素の `Context` 属性を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="d2a3f-123">指定された `Context` 属性は、指定されたすべてのテンプレート パラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="d2a3f-124">これは、(ラップする子要素を持たない) 暗黙的な子コンテンツのコンテンツ パラメーター名を指定する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="d2a3f-125">次の例では、`Context` 属性が `TableTemplate` 要素に表示され、すべてのテンプレート パラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

## <a name="generic-typed-components"></a><span data-ttu-id="d2a3f-126">ジェネリック型のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="d2a3f-126">Generic-typed components</span></span>

<span data-ttu-id="d2a3f-127">多くの場合、テンプレート コンポーネントはジェネリック型です。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-127">Templated components are often generically typed.</span></span> <span data-ttu-id="d2a3f-128">たとえば、ジェエリックの `ListViewTemplate` コンポーネント (`ListViewTemplate.razor`) を使用して、`IEnumerable<T>` 値をレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="d2a3f-129">ジェネリック コンポーネントを定義するには、[`@typeparam`](xref:mvc/views/razor#typeparam) ディレクティブを使用して、型パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="d2a3f-130">ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="d2a3f-131">それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="d2a3f-132">次の例では、`TItem="Pet"` によって次の型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d2a3f-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```

## <a name="additional-resources"></a><span data-ttu-id="d2a3f-133">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d2a3f-133">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

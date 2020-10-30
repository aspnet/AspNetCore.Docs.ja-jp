---
title: ASP.NET Core のコンポーネントタグヘルパー
author: guardrex
ms.author: riande
description: 'ASP.NET Core コンポーネントタグヘルパーを使用して、 :::no-loc(Razor)::: ページおよびビューでコンポーネントを表示する方法について説明します。'
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: cddbca7f95e4d2143d4632aaa83133bc6210e251
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059157"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="1b4b9-103">ASP.NET Core のコンポーネントタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="1b4b9-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="1b4b9-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1b4b9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1b4b9-105">ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1b4b9-106">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="1b4b9-106">Prerequisites</span></span>

<span data-ttu-id="1b4b9-107">記事の「 *ページとビューでコンポーネントを使用するためにアプリを準備する* 」セクションのガイダンスに従って <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> ください。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="1b4b9-108">コンポーネントタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="1b4b9-108">Component Tag Helper</span></span>

<span data-ttu-id="1b4b9-109">次のコンポーネントタグヘルパーは、 `Counter` ページまたはビューでコンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="1b4b9-110">前の例では、 `Counter` コンポーネントがアプリの *Pages* フォルダー内にあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="1b4b9-111">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `@using :::no-loc(Blazor):::Sample.Pages`)。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages`).</span></span>

<span data-ttu-id="1b4b9-112">コンポーネントタグヘルパーは、コンポーネントにパラメーターを渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="1b4b9-113">`ColorfulCheckbox`チェックボックスのラベルの色とサイズを設定する次のコンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="1b4b9-114">コンポーネント `Size` `int` タグヘルパーでは、() および `Color` ( `string` ) [コンポーネントパラメーター](xref:blazor/components/index#component-parameters) を設定できます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="1b4b9-115">前の例では、 `ColorfulCheckbox` コンポーネントがアプリの *共有* フォルダーにあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="1b4b9-116">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `@using :::no-loc(Blazor):::Sample.Shared`)。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="1b4b9-117">ページまたはビューには、次の HTML が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="1b4b9-118">前の例のように、引用符で囲まれた文字列を渡すには、 [明示的な :::no-loc(Razor)::: 式](xref:mvc/views/razor#explicit-razor-expressions)が必要です `param-Color` 。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-118">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="1b4b9-119">:::no-loc(Razor):::型の値の解析動作は、属性 `string` が型であるため、属性には適用されません `param-*` `object` 。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-119">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="1b4b9-120">パラメーターの型は、JSON シリアル化可能である必要があります。これは通常、型が既定のコンストラクターと設定可能なプロパティを持つ必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="1b4b9-121">たとえば、前の例ではとの値を指定できます `Size` `Color` 。これは、 `Size` との型 `Color` がプリミティブ型 (と) であり、 `int` `string` JSON シリアライザーでサポートされているためです。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="1b4b9-122">次の例では、クラスオブジェクトがコンポーネントに渡されます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="1b4b9-123">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="1b4b9-123">*MyClass.cs* :</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="1b4b9-124">**クラスには、パラメーターなしのパブリックコンストラクターが必要です。**</span><span class="sxs-lookup"><span data-stu-id="1b4b9-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="1b4b9-125">*Shared/MyComponent* :</span><span class="sxs-lookup"><span data-stu-id="1b4b9-125">*Shared/MyComponent.razor* :</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="1b4b9-126">*Pages/MyPage* :</span><span class="sxs-lookup"><span data-stu-id="1b4b9-126">*Pages/MyPage.cshtml* :</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="1b4b9-127">前の例では、 `MyComponent` コンポーネントがアプリの *共有* フォルダーにあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="1b4b9-128">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名 ( `@using :::no-loc(Blazor):::Sample` やなど `@using :::no-loc(Blazor):::Sample.Shared` ) です。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="1b4b9-129">`MyClass` は、アプリの名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="1b4b9-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="1b4b9-131">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="1b4b9-132">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから :::no-loc(Blazor)::: アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

| <span data-ttu-id="1b4b9-133">表示モード</span><span class="sxs-lookup"><span data-stu-id="1b4b9-133">Render Mode</span></span> | <span data-ttu-id="1b4b9-134">説明</span><span class="sxs-lookup"><span data-stu-id="1b4b9-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="1b4b9-135">コンポーネントを静的 HTML にレンダリングし、:::no-loc(Blazor Server)::: アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-135">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="1b4b9-136">このマーカーは、ユーザー エージェントの起動時に :::no-loc(Blazor)::: アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-136">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="1b4b9-137">:::no-loc(Blazor Server)::: アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-137">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="1b4b9-138">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-138">Output from the component isn't included.</span></span> <span data-ttu-id="1b4b9-139">このマーカーは、ユーザー エージェントの起動時に :::no-loc(Blazor)::: アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-139">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="1b4b9-140">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1b4b9-141">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="1b4b9-142">コンポーネントでは、ビューおよびページ固有の機能 (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="1b4b9-143">コンポーネントの部分ビューのロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="1b4b9-144">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1b4b9-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b4b9-145">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1b4b9-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>

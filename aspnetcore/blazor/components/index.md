---
title: ASP.NET Core Razor コンポーネントの作成と使用
author: guardrex
description: データへのバインド、イベントの処理、コンポーネント ライフ サイクルの管理の方法など、Razor コンポーネントを作成および使用する方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
uid: blazor/components/index
ms.openlocfilehash: 7b4438b4003916488c17d389b9817b5e09d1086c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536221"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="267a2-103">ASP.NET Core Razor コンポーネントの作成と使用</span><span class="sxs-lookup"><span data-stu-id="267a2-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="267a2-104">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="267a2-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="267a2-105">Blazor アプリは *コンポーネント* を使用してビルドします。</span><span class="sxs-lookup"><span data-stu-id="267a2-105">Blazor apps are built using *components*.</span></span> <span data-ttu-id="267a2-106">コンポーネントは、ページ、ダイアログ、フォームなどのユーザー インターフェイス (UI) の自己完結型のチャンクです。</span><span class="sxs-lookup"><span data-stu-id="267a2-106">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="267a2-107">コンポーネントには、データの挿入や UI イベントへの応答に必要な HTML マークアップと、処理ロジックが含まれます。</span><span class="sxs-lookup"><span data-stu-id="267a2-107">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="267a2-108">コンポーネントは、柔軟性があり、軽量です。</span><span class="sxs-lookup"><span data-stu-id="267a2-108">Components are flexible and lightweight.</span></span> <span data-ttu-id="267a2-109">それらを入れ子にしたり、再利用したり、プロジェクト間で共有したりできます。</span><span class="sxs-lookup"><span data-stu-id="267a2-109">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="267a2-110">コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="267a2-110">Component classes</span></span>

<span data-ttu-id="267a2-111">コンポーネントは、C# と HTML マークアップの組み合わせを使用して、[Razor](xref:mvc/views/razor) コンポーネント ファイル (`.razor`) で実装します。</span><span class="sxs-lookup"><span data-stu-id="267a2-111">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="267a2-112">Blazor のコンポーネントは、正式には *Razor コンポーネント* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="267a2-112">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="267a2-113">Razor の構文</span><span class="sxs-lookup"><span data-stu-id="267a2-113">Razor syntax</span></span>

<span data-ttu-id="267a2-114">Blazor アプリの Razor コンポーネントでは、Razor 構文が多用されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-114">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="267a2-115">Razor マークアップ言語に慣れていない場合は、続行する前に、「[ASP.NET Core の Razor 構文のリファレンス](xref:mvc/views/razor)」を読むことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="267a2-115">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="267a2-116">Razor 構文でコンテンツにアクセスする場合は、次のセクションに特にご注意ください。</span><span class="sxs-lookup"><span data-stu-id="267a2-116">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="267a2-117">[ディレクティブ](xref:mvc/views/razor#directives): 通常はコンポーネント マークアップの解析方法や機能を変更する、`@` プレフィックス付きの予約キーワード。</span><span class="sxs-lookup"><span data-stu-id="267a2-117">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="267a2-118">[ディレクティブ属性](xref:mvc/views/razor#directive-attributes): 通常はコンポーネント要素の解析方法や機能を変更する、`@` プレフィックス付きの予約キーワード。</span><span class="sxs-lookup"><span data-stu-id="267a2-118">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="267a2-119">名前</span><span class="sxs-lookup"><span data-stu-id="267a2-119">Names</span></span>

<span data-ttu-id="267a2-120">コンポーネントの名前は、大文字で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-120">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="267a2-121">たとえば、`MyCoolComponent.razor` は有効で、`myCoolComponent.razor` は無効です。</span><span class="sxs-lookup"><span data-stu-id="267a2-121">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="267a2-122">ルーティング</span><span class="sxs-lookup"><span data-stu-id="267a2-122">Routing</span></span>

<span data-ttu-id="267a2-123">Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントへのルート テンプレートを提供することで実現します。</span><span class="sxs-lookup"><span data-stu-id="267a2-123">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="267a2-124">[`@page`][9] ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-124">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="267a2-125">実行時に、ルーターによって <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> を持つコンポーネント クラスが検索され、要求された URL に一致するルート テンプレートを使用するコンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-125">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="267a2-126">詳細については、「<xref:blazor/fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-126">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="267a2-127">マークアップ</span><span class="sxs-lookup"><span data-stu-id="267a2-127">Markup</span></span>

<span data-ttu-id="267a2-128">コンポーネントの UI は、HTML を使用して定義します。</span><span class="sxs-lookup"><span data-stu-id="267a2-128">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="267a2-129">動的なレンダリング ロジック (たとえばループ、条件、式) が、 *Razor* と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="267a2-129">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="267a2-130">アプリがコンパイルされると、HTML マークアップと C# のレンダリング ロジックはコンポーネント クラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-130">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="267a2-131">生成されたクラスの名前は、ファイルの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="267a2-131">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="267a2-132">コンポーネント クラスのメンバーは、[`@code`][1] ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="267a2-132">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="267a2-133">[`@code`][1] ブロックには、イベント処理のメソッド、またはその他のコンポーネント ロジックを定義するためのメソッドによって、コンポーネントの状態 (プロパティ、フィールド) を指定します。</span><span class="sxs-lookup"><span data-stu-id="267a2-133">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="267a2-134">複数の [`@code`][1] ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-134">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="267a2-135">コンポーネント メンバーは、`@` で始まる C# 式を使用して、コンポーネントのレンダリング ロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-135">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="267a2-136">たとえば、フィールド名の前に `@` を付けることによって、C# フィールドがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-136">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="267a2-137">次の例では、以下のように評価され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-137">The following example evaluates and renders:</span></span>

* <span data-ttu-id="267a2-138">`headingFontStyle` が `font-style` の CSS プロパティ値に。</span><span class="sxs-lookup"><span data-stu-id="267a2-138">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="267a2-139">`headingText` が `<h1>` 要素のコンテンツに。</span><span class="sxs-lookup"><span data-stu-id="267a2-139">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="267a2-140">コンポーネントが最初にレンダリングされた後に、コンポーネントがイベントに応答して、レンダリング ツリーを再生成します。</span><span class="sxs-lookup"><span data-stu-id="267a2-140">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="267a2-141">Blazor によって新旧のレンダリング ツリーが比較され、ブラウザーのドキュメント オブジェクト モデル (DOM) に変更が適用されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-141">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="267a2-142">詳細については、「<xref:blazor/components/rendering>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="267a2-142">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="267a2-143">コンポーネントは通常の C# クラスであり、プロジェクト内の任意の場所に配置できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="267a2-144">Web ページを生成するコンポーネントは、通常、`Pages` フォルダーに存在します。</span><span class="sxs-lookup"><span data-stu-id="267a2-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="267a2-145">ページ以外のコンポーネントは、多くの場合、`Shared` フォルダー、またはプロジェクトに追加されたカスタム フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="267a2-146">名前空間</span><span class="sxs-lookup"><span data-stu-id="267a2-146">Namespaces</span></span>

<span data-ttu-id="267a2-147">一般に、コンポーネントの名前空間は、アプリのルート名前空間と、アプリ内のコンポーネントの場所 (フォルダー) から派生します。</span><span class="sxs-lookup"><span data-stu-id="267a2-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="267a2-148">アプリのルート名前空間が `BlazorSample` で、`Counter` コンポーネントが `Pages` フォルダーに存在する場合:</span><span class="sxs-lookup"><span data-stu-id="267a2-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="267a2-149">`Counter` コンポーネントの名前空間は `BlazorSample.Pages` になります。</span><span class="sxs-lookup"><span data-stu-id="267a2-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="267a2-150">コンポーネントの完全修飾型名は `BlazorSample.Pages.Counter` になります。</span><span class="sxs-lookup"><span data-stu-id="267a2-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="267a2-151">コンポーネントを保持するカスタム フォルダーの場合は、[`@using`][2] ディレクティブを親コンポーネントまたはアプリの `_Imports.razor` ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="267a2-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="267a2-152">次の例では、`Components` フォルダー内のコンポーネントを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="267a2-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="267a2-153">コンポーネントは、完全修飾名を使用して参照することもできます。この場合、[`@using`][2] ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="267a2-154">Razor で作成されるコンポーネントの名前空間は、次に基づきます (優先順)。</span><span class="sxs-lookup"><span data-stu-id="267a2-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="267a2-155">Razor ファイル (`.razor`) マークアップ内の [`@namespace`][8] の指定 (`@namespace BlazorSample.MyNamespace`)。</span><span class="sxs-lookup"><span data-stu-id="267a2-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="267a2-156">プロジェクト ファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。</span><span class="sxs-lookup"><span data-stu-id="267a2-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="267a2-157">プロジェクト ファイルのファイル名 (`.csproj`) から取得されたプロジェクト名、およびプロジェクト ルートからコンポーネントへのパス。</span><span class="sxs-lookup"><span data-stu-id="267a2-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="267a2-158">たとえば、フレームワークでは `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) が名前空間 `BlazorSample.Pages` に解決されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="267a2-159">コンポーネントは C# の名前のバインド規則に従います。</span><span class="sxs-lookup"><span data-stu-id="267a2-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="267a2-160">この例の `Index` コンポーネントの場合、スコープ内のコンポーネントは、次のすべてのコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="267a2-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="267a2-161">同じフォルダー (`Pages`) に含まれるもの。</span><span class="sxs-lookup"><span data-stu-id="267a2-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="267a2-162">別の名前空間を明示的に指定しない、プロジェクトのルート内のコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="267a2-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="267a2-163">`global::` 修飾はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="267a2-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="267a2-164">別名が付けられた [`using`](/dotnet/csharp/language-reference/keywords/using-statement) ステートメント (`@using Foo = Bar` など) によるコンポーネントのインポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="267a2-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="267a2-165">部分修飾名はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="267a2-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="267a2-166">たとえば、`<Shared.NavMenu></Shared.NavMenu>` による `@using BlazorSample` の追加と `NavMenu` コンポーネント (`NavMenu.razor`) の参照はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="267a2-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="267a2-167">部分クラスのサポート</span><span class="sxs-lookup"><span data-stu-id="267a2-167">Partial class support</span></span>

<span data-ttu-id="267a2-168">Razor コンポーネントは、部分クラスとして生成されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="267a2-169">Razor コンポーネントは、次のいずれかの方法を使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="267a2-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="267a2-170">C# コードは、1 つのファイルに HTML マークアップと Razor コードを含む [`@code`][1] ブロックで定義します。</span><span class="sxs-lookup"><span data-stu-id="267a2-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="267a2-171">Blazor テンプレートでは、この方法を使用して Razor コンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="267a2-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="267a2-172">C# コードは、部分クラスとして定義されている分離コード ファイルに配置されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="267a2-173">次の例は、Blazor テンプレートから生成されたアプリ内の [`@code`][1] ブロックを含む既定の `Counter` コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="267a2-174">HTML マークアップ、Razor コード、C# コードは、同じファイル内にあります。</span><span class="sxs-lookup"><span data-stu-id="267a2-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="267a2-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-175">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="267a2-176">`Counter` コンポーネントは、部分クラスを含む分離コード ファイルを使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="267a2-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="267a2-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="267a2-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="267a2-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="267a2-179">必要に応じて、部分クラスファイルに必要な名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="267a2-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="267a2-180">Razor コンポーネントで使用される一般的な名前空間には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="267a2-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="267a2-181">`_Imports.razor` ファイルの [`@using`][2] ディレクティブは、C# ファイル (`.cs`) ではなく Razor ファイル (`.razor`) にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="267a2-182">基本クラスの指定</span><span class="sxs-lookup"><span data-stu-id="267a2-182">Specify a base class</span></span>

<span data-ttu-id="267a2-183">[`@inherits`][6] ディレクティブを使用して、コンポーネントの基本クラスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="267a2-184">次の例は、コンポーネントが基本クラス `BlazorRocksBase` を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="267a2-185">基本クラスは <xref:Microsoft.AspNetCore.Components.ComponentBase> から派生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="267a2-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="267a2-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="267a2-187">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="267a2-188">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="267a2-188">Use components</span></span>

<span data-ttu-id="267a2-189">コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="267a2-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="267a2-190">コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。</span><span class="sxs-lookup"><span data-stu-id="267a2-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="267a2-191">`Pages/Index.razor` の次のマークアップでは、`HeadingComponent` インスタンスがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="267a2-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="267a2-193">コンポーネント名と一致しない最初の文字が大文字の HTML 要素がコンポーネントに含まれている場合、要素に予期しない名前が付いていることを示す警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="267a2-194">コンポーネントの名前空間に [`@using`][2] ディレクティブを追加すると、コンポーネントを使用できるようになり、警告が解決されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="267a2-195">パラメーター</span><span class="sxs-lookup"><span data-stu-id="267a2-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="267a2-196">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="267a2-196">Route parameters</span></span>

<span data-ttu-id="267a2-197">コンポーネントでは、[`@page`][9] ディレクティブに指定されたルート テンプレートからルート パラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="267a2-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="267a2-198">ルーターでは、ルート パラメーターを使用して、対応するコンポーネント パラメーターが設定されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="267a2-199">省略可能なパラメーターがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="267a2-199">Optional parameters are supported.</span></span> <span data-ttu-id="267a2-200">次の例では、省略可能なパラメーター `text` を使用して、ルート セグメントの値をコンポーネントの `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="267a2-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="267a2-201">セグメントが存在しない場合、`Text` の値は `fantastic` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="267a2-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="267a2-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="267a2-204">オプションのパラメーターはサポートされていないため、前の例では 2 つの [`@page`][9] ディレクティブが適用されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="267a2-205">1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="267a2-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="267a2-206">2 番目の [`@page`][9] ディレクティブでは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="267a2-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="267a2-207">複数のフォルダーにわたりパスをキャプチャするキャッチオール ルート パラメーター (`{*pageRoute}`) の詳細については、「<xref:blazor/fundamentals/routing#catch-all-route-parameters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="267a2-208">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="267a2-208">Component parameters</span></span>

<span data-ttu-id="267a2-209">コンポーネントには、"*コンポーネント パラメーター*" を指定できます。これは、[`[Parameter]` 属性](xref:Microsoft.AspNetCore.Components.ParameterAttribute)を指定したコンポーネント クラス上で、単純な、または複雑なパブリック プロパティを使用して定義します。</span><span class="sxs-lookup"><span data-stu-id="267a2-209">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="267a2-210">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="267a2-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="267a2-211">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-211">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="267a2-212">コンポーネント パラメーターには、既定値を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="267a2-212">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="267a2-213">サンプル アプリの次の例では、`ParentComponent` によって `ChildComponent` の `Title` プロパティの値を設定しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-213">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="267a2-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-214">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="267a2-215">[Razor の予約済み `@` シンボル](xref:mvc/views/razor#razor-syntax)を使用して、C# のフィールド、プロパティ、およびメソッドを HTML 属性値としてコンポーネント パラメーターに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="267a2-215">Assign C# fields, properties, and methods to component parameters as HTML attribute values using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="267a2-216">親コンポーネントのフィールド、プロパティ、またはメソッドを子コンポーネントのパラメーターに割り当てる場合は、そのフィールド、プロパティ、またはメソッドの名前の先頭に `@` シンボルを付けます。</span><span class="sxs-lookup"><span data-stu-id="267a2-216">To assign a parent component's field, property, or method to a child component's parameter, prefix the field, property, or method name with the `@` symbol.</span></span> <span data-ttu-id="267a2-217">[暗黙的な C# の式](xref:mvc/views/razor#implicit-razor-expressions)の結果をパラメーターに割り当てる場合は、暗黙的な式の先頭に `@` シンボルを付けます。</span><span class="sxs-lookup"><span data-stu-id="267a2-217">To assign the result of an [implicit C# expression](xref:mvc/views/razor#implicit-razor-expressions) to a parameter, prefix the implicit expression with an `@` symbol.</span></span>

  <span data-ttu-id="267a2-218">次の親コンポーネントでは、上記の `ChildComponent` コンポーネントの 4 つのインスタンスが表示され、それらの `Title` パラメーター値が次のように設定されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-218">The following parent component displays four instances of the preceding `ChildComponent` component and sets their `Title` parameter values to:</span></span>

  * <span data-ttu-id="267a2-219">`title` フィールドの値。</span><span class="sxs-lookup"><span data-stu-id="267a2-219">The value of the `title` field.</span></span>
  * <span data-ttu-id="267a2-220">`GetTitle` C# メソッドの結果。</span><span class="sxs-lookup"><span data-stu-id="267a2-220">The result of the `GetTitle` C# method.</span></span>
  * <span data-ttu-id="267a2-221"><xref:System.DateTime.ToLongDateString%2A> を使用した長い形式での現在のローカル日付。</span><span class="sxs-lookup"><span data-stu-id="267a2-221">The current local date in long format with <xref:System.DateTime.ToLongDateString%2A>.</span></span>
  * <span data-ttu-id="267a2-222">`person` オブジェクトの `Name` プロパティ。</span><span class="sxs-lookup"><span data-stu-id="267a2-222">The `person` object's `Name` property.</span></span>

  <span data-ttu-id="267a2-223">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-223">`Pages/ParentComponent.razor`:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from field.
  </ChildComponent>
  
  <ChildComponent Title="@GetTitle()">
      Title from method.
  </ChildComponent>
  
  <ChildComponent Title="@DateTime.Now.ToLongDateString()">
      Title from implicit Razor expression.
  </ChildComponent>
  
  <ChildComponent Title="@person.Name">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title = "Panel Title from Parent";
      private Person person = new Person();
      
      private string GetTitle()
      {
          return "Panel Title from Parent";
      }
      
      private class Person
      {
          public string Name { get; set; } = "Dr. Who";
      }
  }
  ```
  
  <span data-ttu-id="267a2-224">Razor ページ (`.cshtml`) とは異なり、Blazor では、コンポーネントのレンダリング中に Razor 式で非同期処理を実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="267a2-224">Unlike in Razor pages (`.cshtml`), Blazor can't perform asynchronous work in a Razor expression while rendering a component.</span></span> <span data-ttu-id="267a2-225">これは、Blazor が対話型 UI をレンダリングするように設計されているためです。</span><span class="sxs-lookup"><span data-stu-id="267a2-225">This is because Blazor is designed for rendering interactive UIs.</span></span> <span data-ttu-id="267a2-226">対話型 UI の場合、画面には常に何かが表示されている必要があるため、レンダリング フローをブロックしても意味はありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-226">In an interactive UI, the screen must always display something, so it doesn't make sense to block the rendering flow.</span></span> <span data-ttu-id="267a2-227">代わりに、非同期処理は、いずれかの[非同期ライフサイクル イベント](xref:blazor/components/lifecycle)中に実行されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-227">Instead, asynchronous work is performed during one of the [asynchronous lifecycle events](xref:blazor/components/lifecycle).</span></span> <span data-ttu-id="267a2-228">非同期ライフサイクル イベントが発生するたびに、コンポーネントは再びレンダリングされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-228">After each asynchronous lifecycle event, the component may render again.</span></span> <span data-ttu-id="267a2-229">次の Razor 構文はサポートされて **いません**。</span><span class="sxs-lookup"><span data-stu-id="267a2-229">The following Razor syntax is **not** supported:</span></span>
  
  ```razor
  <ChildComponent Title="@await ...">
      ...
  </ChildComponent>
  ```
  
  <span data-ttu-id="267a2-230">上記の例に含まれるコードでは、アプリがビルドされると "*コンパイラ エラー*" が発生します。</span><span class="sxs-lookup"><span data-stu-id="267a2-230">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="267a2-231">'await' 演算子は、非同期メソッド内でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-231">The 'await' operator can only be used within an async method.</span></span> <span data-ttu-id="267a2-232">このメソッドを 'async' 修飾子でマークし、その戻り値の型を 'Task' に変更することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-232">Consider marking this method with the 'async' modifier and changing its return type to 'Task'.</span></span>

  <span data-ttu-id="267a2-233">上記の例で非同期的に `Title` パラメーターの値を取得するには、次の例に示すように、コンポーネントで [`OnInitializedAsync` ライフサイクル イベント](xref:blazor/components/lifecycle#component-initialization-methods)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-233">To obtain a value for the `Title` parameter in the preceding example asychronously, the component can use the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods), as the following example demonstrates:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title;
      
      protected override async Task OnInitializedAsync()
      {
          title = await ...;
      }
  }
  ```
  
* <span data-ttu-id="267a2-234">親コンポーネントの[明示的な C# の式](xref:mvc/views/razor#explicit-razor-expressions)の結果を子コンポーネントのパラメーターに割り当てるには、式をかっこで囲み、先頭に `@` シンボルを付けます。</span><span class="sxs-lookup"><span data-stu-id="267a2-234">To assign the result of an [explicit C# expression](xref:mvc/views/razor#explicit-razor-expressions) in the parent component to a child component's parameter, surround the expression in parentheses with an `@` symbol prefix.</span></span>

  <span data-ttu-id="267a2-235">次の子コンポーネントには、<xref:System.DateTime> コンポーネント パラメーターである `ShowItemsSinceDate` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="267a2-235">The following child component has a <xref:System.DateTime> component parameter, `ShowItemsSinceDate`.</span></span>
  
  <span data-ttu-id="267a2-236">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-236">`Shared/ChildComponent.razor`:</span></span>
  
  ```razor
  <div class="panel panel-default">
      <div class="panel-heading">Explicit DateTime Expression Example</div>
      <div class="panel-body">
          <p>@ChildContent</p>
          <p>One week ago date: @ShowItemsSinceDate</p>
      </div>
  </div>

  @code {
      [Parameter]
      public DateTime ShowItemsSinceDate { get; set; }

      [Parameter]
      public RenderFragment ChildContent { get; set; }
  }
  ```
  
  <span data-ttu-id="267a2-237">次の親コンポーネントでは、明示的な C# 式 (過去 1 週間を表します) を使用して、子の `ShowItemsSinceDate` パラメーターに割り当てるための日付が計算されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-237">The following parent component calculates a date with an explicit C# expression that's one week in the past for assignment to the child's `ShowItemsSinceDate` parameter.</span></span>
  
  <span data-ttu-id="267a2-238">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-238">`Pages/ParentComponent.razor`:</span></span>

  ```razor
  <ChildComponent ShowItemsSinceDate="@(DateTime.Now - TimeSpan.FromDays(7))">
      Title from explicit Razor expression.
  </ChildComponent>
  ```

  <span data-ttu-id="267a2-239">パラメーターに割り当てるために、明示的な式を使用してテキストを式の結果と連結することは、サポートされて **いません**。</span><span class="sxs-lookup"><span data-stu-id="267a2-239">Use of an explicit expression to concatenate text with an expression result for assignment to a parameter is **not** supported.</span></span> <span data-ttu-id="267a2-240">次の例では、"SKU-" というテキストを、親コンポーネントの `product` オブジェクトによって提供される製品在庫数 (`SKU` プロパティ、"Stock Keeping Unit") と連結しようとしています。</span><span class="sxs-lookup"><span data-stu-id="267a2-240">The following example seeks to concatenate the text "SKU-" with a product stock number (`SKU` property, "Stock Keeping Unit") provided by a parent component's `product` object.</span></span> <span data-ttu-id="267a2-241">この構文は Razor ページ (`.cshtml`) でサポートされていますが、子の `Title` パラメーターに割り当てる場合は無効です。</span><span class="sxs-lookup"><span data-stu-id="267a2-241">Although this syntax is supported in a Razor page (`.cshtml`), it isn't valid for assignment to the child's `Title` parameter.</span></span>
  
  ```razor
  <ChildComponent Title="SKU-@(product.SKU)">
      Title from composed Razor expression. This doesn't compile.
  </ChildComponent>
  ```
  
  <span data-ttu-id="267a2-242">上記の例に含まれるコードでは、アプリがビルドされると "*コンパイラ エラー*" が発生します。</span><span class="sxs-lookup"><span data-stu-id="267a2-242">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="267a2-243">コンポーネント属性では、複合コンテンツ (C# とマークアップの混合) はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="267a2-243">Component attributes do not support complex content (mixed C# and markup).</span></span>
  
  <span data-ttu-id="267a2-244">合成値の割り当てをサポートするには、メソッド、フィールド、またはプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="267a2-244">To support the assignment of a composed value, use a method, field, or property.</span></span> <span data-ttu-id="267a2-245">次の例では、C# メソッド `GetTitle` 内で "SKU-" と製品の在庫数の連結が実行されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-245">The following example performs the concatination of "SKU-" and a product's stock number in the C# method `GetTitle`:</span></span>
  
  ```razor
  <ChildComponent Title="@GetTitle()">
      Composed title from method.
  </ChildComponent>
  
  @code {
      private Product product = new Product();

      private string GetTitle() => $"SKU-{product.SKU}";
      
      private class Product
      {
          public string SKU { get; set; } = "12345";
      }
  }
  ```
  
<span data-ttu-id="267a2-246">詳細については、「[ASP.NET Coreの Razor 構文リファレンス](xref:mvc/views/razor)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-246">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="267a2-247">独自の "*コンポーネント パラメーター*" を書き込み先とするコンポーネントを作成する代わりに、プライベート フィールドを使用してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-247">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="267a2-248">詳細については、「[上書きされたパラメーター](#overwritten-parameters)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="267a2-248">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

#### <a name="component-parameters-should-be-auto-properties"></a><span data-ttu-id="267a2-249">コンポーネント パラメーターは自動プロパティである必要がある</span><span class="sxs-lookup"><span data-stu-id="267a2-249">Component parameters should be auto-properties</span></span>

<span data-ttu-id="267a2-250">コンポーネント パラメーターは "*自動プロパティ*" として宣言する必要があります。つまり、そのゲッターやセッターにカスタム ロジックを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="267a2-250">Component parameters should be declared as *auto-properties*, meaning that they shouldn't contain custom logic in their getters or setters.</span></span> <span data-ttu-id="267a2-251">たとえば、次の `StartData` プロパティは自動プロパティです。</span><span class="sxs-lookup"><span data-stu-id="267a2-251">For example, the following `StartData` property is an auto-property:</span></span>

```csharp
[Parameter]
public DateTime StartData { get; set; }
```

<span data-ttu-id="267a2-252">`get` または `set` アクセサーにカスタム ロジックを配置しないでください。なぜなら、コンポーネント パラメーターの目的は、親コンポーネントから子コンポーネントに情報をフローさせるためのチャネルとして使用することだけだからです。</span><span class="sxs-lookup"><span data-stu-id="267a2-252">Don't place custom logic in the `get` or `set` accessor because component parameters are purely intended for use as a channel for a parent component to flow information to a child component.</span></span> <span data-ttu-id="267a2-253">子コンポーネントのプロパティのセッターに、親コンポーネントの再レンダリングが発生する原因となるロジックが含まれている場合、レンダリングの無限ループが発生します。</span><span class="sxs-lookup"><span data-stu-id="267a2-253">If a setter of a child component property contains logic that causes rerendering of the parent component, an infinite rendering loop results.</span></span>

<span data-ttu-id="267a2-254">受け取ったパラメーター値を変換する必要がある場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="267a2-254">If you need to transform a received parameter value:</span></span>

* <span data-ttu-id="267a2-255">パラメーター プロパティは、指定された生データを表す純粋な自動プロパティのままにしておきます。</span><span class="sxs-lookup"><span data-stu-id="267a2-255">Leave the parameter property as a pure auto-property to represent the supplied raw data.</span></span>
* <span data-ttu-id="267a2-256">パラメーター プロパティに基づいて変換されたデータを指定する、その他のプロパティまたはメソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="267a2-256">Create some other property or method that supplies the transformed data based on the parameter property.</span></span>

<span data-ttu-id="267a2-257">新しいデータを受け取るたびに受け取ったパラメーターを変換したい場合は、`OnParametersSetAsync` をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="267a2-257">You can override `OnParametersSetAsync` if you want to transform a received parameter each time new data is received.</span></span>

## <a name="child-content"></a><span data-ttu-id="267a2-258">子コンテンツ</span><span class="sxs-lookup"><span data-stu-id="267a2-258">Child content</span></span>

<span data-ttu-id="267a2-259">コンポーネントでは、別のコンポーネントのコンテンツを設定できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-259">Components can set the content of another component.</span></span> <span data-ttu-id="267a2-260">割り当てコンポーネントでは、受信コンポーネントを指定するタグ間にコンテンツを指定します。</span><span class="sxs-lookup"><span data-stu-id="267a2-260">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="267a2-261">次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す <xref:Microsoft.AspNetCore.Components.RenderFragment> を表す `ChildContent` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="267a2-261">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="267a2-262">コンテンツをレンダリングする必要があるコンポーネントのマークアップに、`ChildContent` の値を配置します。</span><span class="sxs-lookup"><span data-stu-id="267a2-262">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="267a2-263">`ChildContent` の値は、親コンポーネントから受け取られ、ブートストラップ パネルの `panel-body` 内にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-263">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="267a2-264">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-264">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="267a2-265"><xref:Microsoft.AspNetCore.Components.RenderFragment> コンテンツを受け取るプロパティは、規則によって `ChildContent` という名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-265">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="267a2-266">サンプル アプリの `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` をレンダリングするためのコンテンツを提供できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-266">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="267a2-267">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-267">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="267a2-268">Blazor による子コンテンツのレンダリング方法により、`for` ループ内のコンポーネントのレンダリングでは、インクリメントするループ変数が子コンポーネントのコンテンツ内で使用されている場合、ローカル インデックス変数が必要になります。</span><span class="sxs-lookup"><span data-stu-id="267a2-268">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="267a2-269">または、<xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> と共に `foreach` ループを使用します。</span><span class="sxs-lookup"><span data-stu-id="267a2-269">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="267a2-270"><xref:Microsoft.AspNetCore.Components.RenderFragment> を Razor コンポーネント UI のテンプレートとして使用する方法については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-270">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="267a2-271">属性スプラッティングと任意のパラメーター</span><span class="sxs-lookup"><span data-stu-id="267a2-271">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="267a2-272">コンポーネントでは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャしてレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="267a2-272">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="267a2-273">追加の属性は、ディクショナリにキャプチャし、[`@attributes`][3] Razor ディレクティブを使用して、コンポーネントがレンダリングされるときに、要素に "*スプラッティング*" できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-273">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="267a2-274">このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="267a2-274">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="267a2-275">たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-275">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="267a2-276">次の例で、最初の `<input>` 要素 (`id="useIndividualParams"`) では、個々のコンポーネント パラメーターを使用していますが、2 番目の `<input>` 要素 (`id="useAttributesDict"`) では、属性スプラッティングを使用しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-276">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="267a2-277">パラメーターの型は、文字列キーで `IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-277">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="267a2-278">両方の方法を使用してレンダリングされる `<input>` 要素は同じです。</span><span class="sxs-lookup"><span data-stu-id="267a2-278">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="267a2-279">任意の属性を受け入れるには、<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> プロパティを `true` に設定した [`[Parameter]` 属性](xref:Microsoft.AspNetCore.Components.ParameterAttribute)を使用して、コンポーネント パラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="267a2-279">To accept arbitrary attributes, define a component parameter using the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="267a2-280">[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) の <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> プロパティにより、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。</span><span class="sxs-lookup"><span data-stu-id="267a2-280">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="267a2-281">1 つのコンポーネントで、<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> を持つパラメーターは 1 つだけ定義できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-281">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="267a2-282"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-282">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="267a2-283">このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` も使用できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-283">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="267a2-284">要素属性の位置を基準とした [`@attributes`][3] の位置は重要です。</span><span class="sxs-lookup"><span data-stu-id="267a2-284">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="267a2-285">[`@attributes`][3] が要素にスプラッティングされると、属性は右から左 (最後から最初) に処理されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-285">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="267a2-286">`Child` コンポーネントを使用する次のコンポーネントの例を考えます。</span><span class="sxs-lookup"><span data-stu-id="267a2-286">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="267a2-287">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-287">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="267a2-288">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-288">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="267a2-289">`Child` コンポーネントの `extra` 属性が [`@attributes`][3] の右側に設定されています。</span><span class="sxs-lookup"><span data-stu-id="267a2-289">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="267a2-290">属性は右から左 (最後から最初) に処理されるため、追加の属性によって渡された場合に、`Parent` コンポーネントのレンダリングされる `<div>` に、`extra="5"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="267a2-290">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="267a2-291">次の例では、`Child` コンポーネントの `<div>` で、`extra` と [`@attributes`][3] の順序が逆になります。</span><span class="sxs-lookup"><span data-stu-id="267a2-291">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="267a2-292">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-292">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="267a2-293">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="267a2-293">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="267a2-294">追加の属性によって渡された場合に、`Parent` コンポーネント内のレンダリングされる `<div>` には、`extra="10"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="267a2-294">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="267a2-295">コンポーネントへの参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="267a2-295">Capture references to components</span></span>

<span data-ttu-id="267a2-296">コンポーネント参照によって、コンポーネント インスタンスを参照する方法が得られるため、そのインスタンスに `Show` や `Reset` などのコマンドを発行できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="267a2-297">コンポーネント参照をキャプチャするには:</span><span class="sxs-lookup"><span data-stu-id="267a2-297">To capture a component reference:</span></span>

* <span data-ttu-id="267a2-298">子コンポーネントに [`@ref`][4] 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="267a2-298">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="267a2-299">子コンポーネントと同じ型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="267a2-299">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="267a2-300">コンポーネントがレンダリングされると、`loginDialog` フィールドに `CustomLoginDialog` 子コンポーネント インスタンスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-300">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="267a2-301">これにより、コンポーネント インスタンスに対し、.NET メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="267a2-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="267a2-302">`loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="267a2-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="267a2-303">コンポーネントがレンダリングされるまで、参照するものはありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-303">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="267a2-304">コンポーネントのレンダリングが完了した後にコンポーネント参照を操作するには、[`OnAfterRenderAsync`メソッドまたは `OnAfterRender` メソッド](xref:blazor/components/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="267a2-304">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="267a2-305">イベント ハンドラーで参照変数を使用するには、ラムダ式を使用するか、[`OnAfterRenderAsync` または `OnAfterRender` メソッド](xref:blazor/components/lifecycle#after-component-render)でイベント ハンドラー デリゲートを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="267a2-305">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="267a2-306">これにより、イベント ハンドラーが割り当てられる前に参照変数が確実に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="267a2-306">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="267a2-307">ループ内のコンポーネントを参照するには、「[Capture references to multiple similar child-components](https://github.com/dotnet/aspnetcore/issues/13358)」(複数の類似した子コンポーネントへの参照をキャプチャする) (dotnet/aspnetcore #13358) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-307">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="267a2-308">コンポーネント参照のキャプチャでは、[要素参照のキャプチャ](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)と類似の構文を使用しますが、それは JavaScript 相互運用機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-308">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="267a2-309">コンポーネント参照は、JavaScript コードに渡されません。</span><span class="sxs-lookup"><span data-stu-id="267a2-309">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="267a2-310">コンポーネント参照は、.NET コードでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-310">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="267a2-311">子コンポーネントの状態を変えるためにコンポーネント参照を使用 **しない** でください。</span><span class="sxs-lookup"><span data-stu-id="267a2-311">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="267a2-312">代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="267a2-312">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="267a2-313">通常の宣言型パラメーターを使用すると、子コンポーネントが正しいタイミングで自動的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-313">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="267a2-314">同期コンテキスト</span><span class="sxs-lookup"><span data-stu-id="267a2-314">Synchronization context</span></span>

<span data-ttu-id="267a2-315">Blazor では、同期コンテキスト (<xref:System.Threading.SynchronizationContext>) を使用して、1 つの実行の論理スレッドを強制します。</span><span class="sxs-lookup"><span data-stu-id="267a2-315">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="267a2-316">コンポーネントの[ライフサイクル メソッド](xref:blazor/components/lifecycle)と、Blazor によって発生するすべてのイベント コールバックは、同期コンテキストで実行されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-316">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="267a2-317">Blazor Server の同期コンテキストでは、ブラウザーの WebAssembly モデル (シングル スレッド) と厳密に一致するように、シングルスレッド環境のエミュレートが試行されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-317">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="267a2-318">どの時点でも、作業は 1 つのスレッドでのみ実行され、1 つの論理スレッドであるという印象になります。</span><span class="sxs-lookup"><span data-stu-id="267a2-318">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="267a2-319">2 つの操作が同時に実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-319">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="267a2-320">スレッドをブロックする呼び出しを避ける</span><span class="sxs-lookup"><span data-stu-id="267a2-320">Avoid thread-blocking calls</span></span>

<span data-ttu-id="267a2-321">一般に、次のメソッドは呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="267a2-321">Generally, don't call the following methods.</span></span> <span data-ttu-id="267a2-322">次のメソッドでは、スレッドがブロックされます。そのため、基になる <xref:System.Threading.Tasks.Task> が完了するまで、アプリの動作が再開されなくなります。</span><span class="sxs-lookup"><span data-stu-id="267a2-322">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="267a2-323">状態を更新するために外部でコンポーネント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="267a2-323">Invoke component methods externally to update state</span></span>

<span data-ttu-id="267a2-324">タイマーやその他の通知などの外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。これにより、Blazor の同期コンテキストにディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-324">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="267a2-325">たとえば、リッスンしているコンポーネントに、更新状態を通知できる *通知サービス* を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="267a2-325">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="267a2-326">`NotifierService` を登録します。</span><span class="sxs-lookup"><span data-stu-id="267a2-326">Register the `NotifierService`:</span></span>

* <span data-ttu-id="267a2-327">Blazor WebAssembly で、`Program.Main` のシングルトンとしてサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="267a2-327">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="267a2-328">Blazor Server で、`Startup.ConfigureServices` のスコープとしてサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="267a2-328">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="267a2-329">`NotifierService` を使用して、コンポーネントを更新します。</span><span class="sxs-lookup"><span data-stu-id="267a2-329">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="267a2-330">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="267a2-330">In the preceding example:</span></span>

* <span data-ttu-id="267a2-331">Blazor の同期コンテキスト外で `NotifierService` からコンポーネントの `OnNotify` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-331">`NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="267a2-332">`InvokeAsync` を使用して、正しいコンテキストに切り替え、レンダリングをキューに登録します。</span><span class="sxs-lookup"><span data-stu-id="267a2-332">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="267a2-333">詳細については、「<xref:blazor/components/rendering>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-333">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="267a2-334">コンポーネントは <xref:System.IDisposable> を実装し、`OnNotify` デリゲートは `Dispose` メソッドでサブスクライブを解除します。このメソッドは、コンポーネントが破棄されたときにフレームワークによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-334">The component implements <xref:System.IDisposable>, and the `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="267a2-335">詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-335">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="267a2-336">\@ キーを使用して要素とコンポーネントの保存を制御する</span><span class="sxs-lookup"><span data-stu-id="267a2-336">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="267a2-337">要素またはコンポーネントのリストをレンダリングし、その後に要素またはコンポーネントが変更された場合、Blazor の比較アルゴリズムでは、前のどの要素やコンポーネントを保持できるか、およびモデル オブジェクトをそれらにどのようにマップするかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-337">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="267a2-338">通常、このプロセスは自動で、無視できますが、プロセスの制御が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-338">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="267a2-339">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="267a2-339">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="267a2-340">`People` コレクションのコンテンツは、挿入、削除、または順序変更されたエントリによって変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-340">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="267a2-341">コンポーネントのレンダリング時に、`<DetailsEditor>` コンポーネントが変更され、異なる `Details` パラメーター値を受け取ることがあります。</span><span class="sxs-lookup"><span data-stu-id="267a2-341">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="267a2-342">これにより、予期したものよりも複雑な再レンダリングが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-342">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="267a2-343">場合によっては、再レンダリングによって、要素のフォーカスの喪失などの表示動作の違いが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-343">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="267a2-344">マッピング プロセスは、[`@key`][5] ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-344">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="267a2-345">[`@key`][5] により、比較アルゴリズムで、キーの値に基づいて要素やコンポーネントが確実に保持されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-345">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="267a2-346">`People` コレクションが変更されても、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンス間の関連付けが保持されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-346">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="267a2-347">`Person` が `People` リストから削除された場合、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-347">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="267a2-348">他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="267a2-348">Other instances are left unchanged.</span></span>
* <span data-ttu-id="267a2-349">リスト内の特定の位置に `Person` が挿入されると、その対応する位置に、1 つの新しい `<DetailsEditor>` インスタンスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-349">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="267a2-350">他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="267a2-350">Other instances are left unchanged.</span></span>
* <span data-ttu-id="267a2-351">`Person` エントリの順序が変更された場合、対応する `<DetailsEditor>` インスタンスは UI で保持され、順序が変更されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-351">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="267a2-352">シナリオによっては、[`@key`][5] を使用すると、レンダリングの複雑さが最小限に抑えられ、フォーカス位置など、DOM 変更のステートフルな部分の潜在的な問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-352">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="267a2-353">キーは、各コンテナー要素やコンポーネントに対してローカルです。</span><span class="sxs-lookup"><span data-stu-id="267a2-353">Keys are local to each container element or component.</span></span> <span data-ttu-id="267a2-354">キーはドキュメント全体でグローバルに比較されません。</span><span class="sxs-lookup"><span data-stu-id="267a2-354">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="267a2-355">\@ キーを使用する場面</span><span class="sxs-lookup"><span data-stu-id="267a2-355">When to use \@key</span></span>

<span data-ttu-id="267a2-356">一般に、リストがレンダリングされ (たとえば、[foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ブロックで)、[`@key`][5] を定義するための適切な値が存在する場合は常に、[`@key`][5] を使用することは意味があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-356">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="267a2-357">また、[`@key`][5] を使用して、オブジェクトが変更されたときに Blazor が要素やコンポーネントのサブツリーを保持しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="267a2-357">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="267a2-358">`@currentPerson` が変更された場合、[`@key`][5] 属性ディレクティブによって、Blazor に、`<div>` とその子孫全体を破棄させ、新しい要素とコンポーネントで UI 内のサブツリーをリビルドさせます。</span><span class="sxs-lookup"><span data-stu-id="267a2-358">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="267a2-359">これは、`@currentPerson` が変更されたときに、UI の状態が確実に保持されないようにする必要がある場合に役立つ可能性があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-359">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="267a2-360">\@ キーを使用しない場面</span><span class="sxs-lookup"><span data-stu-id="267a2-360">When not to use \@key</span></span>

<span data-ttu-id="267a2-361">[`@key`][5] で比較すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="267a2-361">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="267a2-362">パフォーマンスの低下は大きくありませんが、要素やコンポーネントの保存規則を制御することによって、アプリにメリットがある場合にのみ [`@key`][5] を指定してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-362">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="267a2-363">[`@key`][5] を使用しない場合でも、Blazor では可能な限り、子要素とコンポーネント インスタンスが保持されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-363">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="267a2-364">[`@key`][5] を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネント インスタンスにモデル インスタンスをマップする "*方法*" を制御することです。</span><span class="sxs-lookup"><span data-stu-id="267a2-364">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="267a2-365">\@ キーに使用する値</span><span class="sxs-lookup"><span data-stu-id="267a2-365">What values to use for \@key</span></span>

<span data-ttu-id="267a2-366">一般に、[`@key`][5] には、次のいずれかの種類の値を指定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="267a2-366">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="267a2-367">モデル オブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。</span><span class="sxs-lookup"><span data-stu-id="267a2-367">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="267a2-368">これにより、オブジェクト参照の等価性に基づいて保持されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-368">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="267a2-369">一意の識別子 (たとえば、`int` 型、`string` 型、`Guid` 型の主キー値)。</span><span class="sxs-lookup"><span data-stu-id="267a2-369">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="267a2-370">[`@key`][5] に使用される値は確実に競合しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="267a2-370">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="267a2-371">同じ親要素内で競合する値が検出された場合、Blazor では、古い要素やコンポーネントを新しい要素やコンポーネントに確定的にマップできないため、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-371">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="267a2-372">個別の値 (オブジェクト インスタンスや主キー値など) のみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-372">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="267a2-373">上書きされたパラメーター</span><span class="sxs-lookup"><span data-stu-id="267a2-373">Overwritten parameters</span></span>

<span data-ttu-id="267a2-374">Blazor フレームワークでは、一般に安全な親から子へのパラメーターの割り当てが行われます。</span><span class="sxs-lookup"><span data-stu-id="267a2-374">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="267a2-375">パラメーターが予期せずに上書きされることはありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-375">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="267a2-376">副作用は最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="267a2-376">Side-effects are minimized.</span></span> <span data-ttu-id="267a2-377">たとえば、追加のレンダリングは、無限のレンダリング ループが作成される可能性があるため、回避されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-377">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="267a2-378">子コンポーネントは、親コンポーネントのレンダリング時に既存の値を上書きする可能性がある新しいパラメーター値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="267a2-378">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="267a2-379">子コンポーネントでパラメーター値が誤って上書きされることは、1 つまたは複数のデータバインド パラメーターを使用してコンポーネントを開発しており、開発者が子のパラメーターに直接書き込む場合に多く発生します。</span><span class="sxs-lookup"><span data-stu-id="267a2-379">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="267a2-380">子コンポーネントは、親コンポーネントの 1 つまたは複数のパラメーター値を使用してレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-380">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="267a2-381">子によって、パラメーターの値が直接書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="267a2-381">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="267a2-382">親コンポーネントがレンダリングされ、子のパラメーターの値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-382">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="267a2-383">パラメーター値の上書きの可能性は、子コンポーネントのプロパティ セッターにも及びます。</span><span class="sxs-lookup"><span data-stu-id="267a2-383">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="267a2-384">**一般的なガイダンスとして、独自のパラメーターに直接書き込むコンポーネントを作成しないでください。**</span><span class="sxs-lookup"><span data-stu-id="267a2-384">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="267a2-385">次が実行される、問題のある `Expander` コンポーネントについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="267a2-385">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="267a2-386">子コンテンツのレンダリング。</span><span class="sxs-lookup"><span data-stu-id="267a2-386">Renders child content.</span></span>
* <span data-ttu-id="267a2-387">コンポーネント パラメーター (`Expanded`) を使用した、子コンテンツの表示の切り替え。</span><span class="sxs-lookup"><span data-stu-id="267a2-387">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="267a2-388">コンポーネントによって、`Expanded` パラメーターに直接書き込まれます。これは上書きされるパラメーターの問題を示しているため、回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-388">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="267a2-389">`Expander` コンポーネントは、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出す可能性のある親コンポーネントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-389">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="267a2-390">初期状態では、`Expanded` プロパティが切り替えられると、`Expander` コンポーネントはそれぞれ独立して動作します。</span><span class="sxs-lookup"><span data-stu-id="267a2-390">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="267a2-391">子コンポーネントの状態は、想定どおりのままです。</span><span class="sxs-lookup"><span data-stu-id="267a2-391">The child components maintain their states as expected.</span></span> <span data-ttu-id="267a2-392">親で <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> が呼び出されると、最初の子コンポーネントの `Expanded` パラメーターが初期値 (`true`) にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-392">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="267a2-393">2 つめの `Expander` コンポーネントの `Expanded` 値はリセットされません。これは、2 つめのコンポーネントでは子コンテンツがレンダリングされないためです。</span><span class="sxs-lookup"><span data-stu-id="267a2-393">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="267a2-394">前のシナリオでの状態を維持するには、`Expander` コンポーネントで "*プライベート フィールド*" を使用して、切り替え状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="267a2-394">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="267a2-395">次の変更された `Expander` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="267a2-395">The following revised `Expander` component:</span></span>

* <span data-ttu-id="267a2-396">親から `Expanded` コンポーネント パラメーター値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="267a2-396">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="267a2-397">コンポーネント パラメーター値を、[OnInitialized イベント](xref:blazor/components/lifecycle#component-initialization-methods) の "*プライベート フィールド*" (`expanded`) に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="267a2-397">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="267a2-398">プライベート フィールドを使用して、その内部のトグル状態を維持します。これは、パラメーターに直接書き込まれないようにする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-398">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

<span data-ttu-id="267a2-399">詳細については、[Blazor両方向のバインド エラー (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-399">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="267a2-400">属性を適用する</span><span class="sxs-lookup"><span data-stu-id="267a2-400">Apply an attribute</span></span>

<span data-ttu-id="267a2-401">属性は、[`@attribute`][7] ディレクティブを使用して Razor コンポーネントに適用できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-401">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="267a2-402">次の例では、[`[Authorize]` 属性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)をコンポーネント クラスに適用しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-402">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="267a2-403">条件付き HTML 要素属性</span><span class="sxs-lookup"><span data-stu-id="267a2-403">Conditional HTML element attributes</span></span>

<span data-ttu-id="267a2-404">HTML 要素属性は、.NET 値に基づいて条件付きでレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-404">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="267a2-405">値が `false` または `null` の場合、属性はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="267a2-405">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="267a2-406">値が `true` の場合、属性が最小化されてレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-406">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="267a2-407">次の例では、`IsCompleted` によって `checked` が要素のマークアップにレンダリングされるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="267a2-407">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="267a2-408">`IsCompleted` が `true` の場合、このチェック ボックスは次のようにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-408">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="267a2-409">`IsCompleted` が `false` の場合、このチェック ボックスは次のようにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-409">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="267a2-410">詳細については、「[ASP.NET Coreの Razor 構文リファレンス](xref:mvc/views/razor)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-410">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="267a2-411">.NET 型が `bool` の場合、[`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) などの一部の HTML 属性が正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="267a2-411">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="267a2-412">そのような場合は、`bool` ではなく `string` 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="267a2-412">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="267a2-413">生 HTML</span><span class="sxs-lookup"><span data-stu-id="267a2-413">Raw HTML</span></span>

<span data-ttu-id="267a2-414">通常、文字列は DOM テキスト ノードを使用してレンダリングされます。つまり、それらに含まれている可能性のあるすべてのマークアップが無視され、リテラル テキストとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="267a2-414">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="267a2-415">生 HTML をレンダリングするには、HTML コンテンツを `MarkupString` 値にラップします。</span><span class="sxs-lookup"><span data-stu-id="267a2-415">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="267a2-416">値は HTML または SVG として解析され、DOM に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-416">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="267a2-417">信頼されていないソースから構築された生 HTML をレンダリングすることは、**セキュリティ リスク** であるため、避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="267a2-417">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="267a2-418">次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-418">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="267a2-419">Razor テンプレート</span><span class="sxs-lookup"><span data-stu-id="267a2-419">Razor templates</span></span>

<span data-ttu-id="267a2-420">レンダリング フラグメントは、Razor テンプレート構文を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="267a2-420">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="267a2-421">Razor テンプレートは、UI スニペットを定義する 1 つの方法であり、次の形式を想定しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-421">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="267a2-422">次の例では、<xref:Microsoft.AspNetCore.Components.RenderFragment> と <xref:Microsoft.AspNetCore.Components.RenderFragment%601> の値を指定し、コンポーネント内にテンプレートを直接レンダリングする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="267a2-422">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="267a2-423">レンダリング フラグメントは、引数として[テンプレート コンポーネント](xref:blazor/components/templated-components)に渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="267a2-423">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="267a2-424">前のコードのレンダリングされた結果:</span><span class="sxs-lookup"><span data-stu-id="267a2-424">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="267a2-425">静的な資産</span><span class="sxs-lookup"><span data-stu-id="267a2-425">Static assets</span></span>

<span data-ttu-id="267a2-426">Blazor は、プロジェクトの [`web root (wwwroot)` フォルダー](xref:fundamentals/index#web-root)に静的アセットを配置する ASP.NET Core アプリの規則に従います。</span><span class="sxs-lookup"><span data-stu-id="267a2-426">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="267a2-427">静的アセットの Web ルートを参照するには、ベース相対パス (`/`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="267a2-427">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="267a2-428">次の例では、`logo.png` が物理的に `{PROJECT ROOT}/wwwroot/images` フォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="267a2-428">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="267a2-429">Razor コンポーネントでは、チルダ スラッシュ表記 (`~/`) はサポートされて **いません**。</span><span class="sxs-lookup"><span data-stu-id="267a2-429">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="267a2-430">アプリのベース パスの設定の詳細については、「<xref:blazor/host-and-deploy/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-430">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="267a2-431">タグ ヘルパーはコンポーネントでサポートされない</span><span class="sxs-lookup"><span data-stu-id="267a2-431">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="267a2-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) は、Razor コンポーネント (`.razor` ファイル) ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="267a2-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="267a2-433">Blazor にタグ ヘルパーのような機能を提供するには、タグ ヘルパーと同じ機能を持つコンポーネントを作成し、代わりにそのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="267a2-433">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="267a2-434">スケーラブル ベクター グラフィックス (SVG) イメージ</span><span class="sxs-lookup"><span data-stu-id="267a2-434">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="267a2-435">Blazor では HTML がレンダリングされるため、スケーラブル ベクター グラフィックス (SVG) 画像 (`.svg`) などのブラウザーでサポートされている画像は、`<img>` タグを介してサポートされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-435">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="267a2-436">同様に、SVG 画像は、スタイルシート ファイル (`.css`) の CSS 規則でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="267a2-436">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="267a2-437">ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-437">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="267a2-438">`<svg>` タグをコンポーネント ファイル (`.razor`) に直接配置した場合、基本的な画像レンダリングはサポートされますが、多くの高度なシナリオはまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="267a2-438">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="267a2-439">たとえば、`<use>` タグは現在考慮されないため、一部の SVG タグで [`@bind`][10] を使用できません。</span><span class="sxs-lookup"><span data-stu-id="267a2-439">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="267a2-440">詳細については、[Blazor の SVG サポート (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="267a2-440">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="267a2-441">空白文字のレンダリング動作</span><span class="sxs-lookup"><span data-stu-id="267a2-441">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="267a2-442">[`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) ディレクティブが `true`の値と共に使用されている場合を除き、既定では、次の場合に余分な空白が削除されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-442">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="267a2-443">要素内の先頭または末尾。</span><span class="sxs-lookup"><span data-stu-id="267a2-443">Leading or trailing within an element.</span></span>
* <span data-ttu-id="267a2-444">`RenderFragment` パラメーター内の先頭または末尾。</span><span class="sxs-lookup"><span data-stu-id="267a2-444">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="267a2-445">別のコンポーネントに渡される子コンテンツなどです。</span><span class="sxs-lookup"><span data-stu-id="267a2-445">For example, child content passed to another component.</span></span>
* <span data-ttu-id="267a2-446">`@if` または `@foreach` のような、C# コード ブロックの前か後にある。</span><span class="sxs-lookup"><span data-stu-id="267a2-446">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="267a2-447">空白文字の削除は、`white-space: pre` などの CSS ルールを使用するときに、レンダリングされた出力に影響を与えることがあります。</span><span class="sxs-lookup"><span data-stu-id="267a2-447">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="267a2-448">このパフォーマンスの最適化を無効にして、空白を保持するには、次のいずれかの操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="267a2-448">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="267a2-449">特定のコンポーネントに設定を適用するには、 `.razor` ファイルの先頭に `@preservewhitespace true` ディレクティブを追加する。</span><span class="sxs-lookup"><span data-stu-id="267a2-449">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="267a2-450">サブディレクトリ全体またはプロジェクト全体に設定を適用するには、 `_Imports.razor` ファイル内に `@preservewhitespace true` ディレクティブを追加する。</span><span class="sxs-lookup"><span data-stu-id="267a2-450">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="267a2-451">ほとんどの場合、アプリでは一般的に通常の動作が続行されるため (ただし、速くなります)、何の措置も必要ありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-451">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="267a2-452">空白文字の削除で特定のコンポーネントに問題が発生する場合、そのコンポーネントで `@preservewhitespace true` を使用し、この最適化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="267a2-452">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="267a2-453">空白は、コンポーネントのソース コードに保持されます。</span><span class="sxs-lookup"><span data-stu-id="267a2-453">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="267a2-454">空白文字のみのテキストは、視覚効果がないときでも、ブラウザーのドキュメント オブジェクト モデル (DOM) にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-454">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="267a2-455">次の Razor コンポーネント コードについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="267a2-455">Consider the following Razor component code:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="267a2-456">前の例では、次の不要な空白文字がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="267a2-456">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="267a2-457">`@foreach` コード ブロックの外側。</span><span class="sxs-lookup"><span data-stu-id="267a2-457">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="267a2-458">`<li>` 要素の前後。</span><span class="sxs-lookup"><span data-stu-id="267a2-458">Around the `<li>` element.</span></span>
* <span data-ttu-id="267a2-459">`@item.Text` 出力の前後。</span><span class="sxs-lookup"><span data-stu-id="267a2-459">Around the `@item.Text` output.</span></span>

<span data-ttu-id="267a2-460">100 項目を含むリストでは、402 の空白領域になり、余分な空白が、レンダリングされる出力に視覚的に影響を及ぼすことはありません。</span><span class="sxs-lookup"><span data-stu-id="267a2-460">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="267a2-461">コンポーネントの静的 HTML をレンダリングする場合、タグ内の空白文字は保持されません。</span><span class="sxs-lookup"><span data-stu-id="267a2-461">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="267a2-462">たとえば、レンダリングされた出力の次のコンポーネントのソースをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="267a2-462">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="267a2-463">前の Razor マークアップからの、空白は保持されません。</span><span class="sxs-lookup"><span data-stu-id="267a2-463">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="267a2-464">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="267a2-464">Additional resources</span></span>

* <span data-ttu-id="267a2-465"><xref:blazor/security/server/threat-mitigation>:リソース不足に対処する必要がある Blazor Server アプリの構築に関するガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="267a2-465"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "ASP.NET Core の Razor 構文リファレンス"
[2]: <xref:mvc/views/razor#using> "ASP.NET Core の Razor 構文リファレンス"
[3]: <xref:mvc/views/razor#attributes> "ASP.NET Core の Razor 構文リファレンス"
[4]: <xref:mvc/views/razor#ref> "ASP.NET Core の Razor 構文リファレンス"
[5]: <xref:mvc/views/razor#key> "ASP.NET Core の Razor 構文リファレンス"
[6]: <xref:mvc/views/razor#inherits> "ASP.NET Core の Razor 構文リファレンス"
[7]: <xref:mvc/views/razor#attribute> "ASP.NET Core の Razor 構文リファレンス"
[8]: <xref:mvc/views/razor#namespace> "ASP.NET Core の Razor 構文リファレンス"
[9]: <xref:mvc/views/razor#page> "ASP.NET Core の Razor 構文リファレンス"
[10]: <xref:mvc/views/razor#bind> "ASP.NET Core の Razor 構文リファレンス"

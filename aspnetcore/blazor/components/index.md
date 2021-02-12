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
ms.openlocfilehash: 111512916cb7f0a4fc1f17648e2f9c69e366dff3
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107052"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="8dfb7-103">ASP.NET Core Razor コンポーネントの作成と使用</span><span class="sxs-lookup"><span data-stu-id="8dfb7-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="8dfb7-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Scott Addie](https://github.com/scottaddie)、[Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="8dfb7-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Scott Addie](https://github.com/scottaddie), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="8dfb7-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8dfb7-106">Blazor アプリは *コンポーネント* を使用してビルドします。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="8dfb7-107">コンポーネントは、ページ、ダイアログ、フォームなどのユーザー インターフェイス (UI) の自己完結型のチャンクです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="8dfb7-108">コンポーネントには、データの挿入や UI イベントへの応答に必要な HTML マークアップと、処理ロジックが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="8dfb7-109">コンポーネントは、柔軟性があり、軽量です。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="8dfb7-110">それらを入れ子にしたり、再利用したり、プロジェクト間で共有したりできます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="8dfb7-111">コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="8dfb7-111">Component classes</span></span>

<span data-ttu-id="8dfb7-112">コンポーネントは、C# と HTML マークアップの組み合わせを使用して、[Razor](xref:mvc/views/razor) コンポーネント ファイル (`.razor`) で実装します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="8dfb7-113">Blazor のコンポーネントは、正式には *Razor コンポーネント* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="8dfb7-114">Razor の構文</span><span class="sxs-lookup"><span data-stu-id="8dfb7-114">Razor syntax</span></span>

<span data-ttu-id="8dfb7-115">Blazor アプリの Razor コンポーネントでは、Razor 構文が多用されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="8dfb7-116">Razor マークアップ言語に慣れていない場合は、続行する前に、「[ASP.NET Core の Razor 構文のリファレンス](xref:mvc/views/razor)」を読むことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-116">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="8dfb7-117">Razor 構文でコンテンツにアクセスする場合は、次のセクションに特にご注意ください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="8dfb7-118">[ディレクティブ](xref:mvc/views/razor#directives): 通常はコンポーネント マークアップの解析方法や機能を変更する、`@` プレフィックス付きの予約キーワード。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="8dfb7-119">[ディレクティブ属性](xref:mvc/views/razor#directive-attributes): 通常はコンポーネント要素の解析方法や機能を変更する、`@` プレフィックス付きの予約キーワード。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="8dfb7-120">名前</span><span class="sxs-lookup"><span data-stu-id="8dfb7-120">Names</span></span>

<span data-ttu-id="8dfb7-121">コンポーネントの名前は、大文字で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="8dfb7-122">たとえば、`MyCoolComponent.razor` は有効で、`myCoolComponent.razor` は無効です。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="8dfb7-123">ルーティング</span><span class="sxs-lookup"><span data-stu-id="8dfb7-123">Routing</span></span>

<span data-ttu-id="8dfb7-124">Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントへのルート テンプレートを提供することで実現します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="8dfb7-125">[`@page`][9] ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="8dfb7-126">実行時に、ルーターによって <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> を持つコンポーネント クラスが検索され、要求された URL に一致するルート テンプレートを使用するコンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="8dfb7-127">詳細については、「<xref:blazor/fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="8dfb7-128">マークアップ</span><span class="sxs-lookup"><span data-stu-id="8dfb7-128">Markup</span></span>

<span data-ttu-id="8dfb7-129">コンポーネントの UI は、HTML を使用して定義します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="8dfb7-130">動的なレンダリング ロジック (たとえばループ、条件、式) が、 *Razor* と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="8dfb7-131">アプリがコンパイルされると、HTML マークアップと C# のレンダリング ロジックはコンポーネント クラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="8dfb7-132">生成されたクラスの名前は、ファイルの名前と一致します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="8dfb7-133">コンポーネント クラスのメンバーは、[`@code`][1] ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="8dfb7-134">[`@code`][1] ブロックには、イベント処理のメソッド、またはその他のコンポーネント ロジックを定義するためのメソッドによって、コンポーネントの状態 (プロパティ、フィールド) を指定します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="8dfb7-135">複数の [`@code`][1] ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="8dfb7-136">コンポーネント メンバーは、`@` で始まる C# 式を使用して、コンポーネントのレンダリング ロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="8dfb7-137">たとえば、フィールド名の前に `@` を付けることによって、C# フィールドがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="8dfb7-138">次の例では、以下のように評価され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="8dfb7-139">`headingFontStyle` が `font-style` の CSS プロパティ値に。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="8dfb7-140">`headingText` が `<h1>` 要素のコンテンツに。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="8dfb7-141">コンポーネントが最初にレンダリングされた後に、コンポーネントがイベントに応答して、レンダリング ツリーを再生成します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="8dfb7-142">Blazor によって新旧のレンダリング ツリーが比較され、ブラウザーのドキュメント オブジェクト モデル (DOM) に変更が適用されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="8dfb7-143">詳細については、「<xref:blazor/components/rendering>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-143">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="8dfb7-144">コンポーネントは通常の C# クラスであり、プロジェクト内の任意の場所に配置できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-144">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="8dfb7-145">Web ページを生成するコンポーネントは、通常、`Pages` フォルダーに存在します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-145">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="8dfb7-146">ページ以外のコンポーネントは、多くの場合、`Shared` フォルダー、またはプロジェクトに追加されたカスタム フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-146">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="8dfb7-147">名前空間</span><span class="sxs-lookup"><span data-stu-id="8dfb7-147">Namespaces</span></span>

<span data-ttu-id="8dfb7-148">一般に、コンポーネントの名前空間は、アプリのルート名前空間と、アプリ内のコンポーネントの場所 (フォルダー) から派生します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-148">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="8dfb7-149">アプリのルート名前空間が `BlazorSample` で、`Counter` コンポーネントが `Pages` フォルダーに存在する場合:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-149">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="8dfb7-150">`Counter` コンポーネントの名前空間は `BlazorSample.Pages` になります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-150">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="8dfb7-151">コンポーネントの完全修飾型名は `BlazorSample.Pages.Counter` になります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-151">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="8dfb7-152">コンポーネントを保持するカスタム フォルダーの場合は、[`@using`][2] ディレクティブを親コンポーネントまたはアプリの `_Imports.razor` ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-152">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="8dfb7-153">次の例では、`Components` フォルダー内のコンポーネントを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-153">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="8dfb7-154">コンポーネントは、完全修飾名を使用して参照することもできます。この場合、[`@using`][2] ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-154">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="8dfb7-155">Razor で作成されるコンポーネントの名前空間は、次に基づきます (優先順)。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-155">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="8dfb7-156">Razor ファイル (`.razor`) マークアップ内の [`@namespace`][8] の指定 (`@namespace BlazorSample.MyNamespace`)。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-156">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="8dfb7-157">プロジェクト ファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-157">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="8dfb7-158">プロジェクト ファイルのファイル名 (`.csproj`) から取得されたプロジェクト名、およびプロジェクト ルートからコンポーネントへのパス。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-158">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="8dfb7-159">たとえば、フレームワークでは `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) が名前空間 `BlazorSample.Pages` に解決されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-159">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="8dfb7-160">コンポーネントは C# の名前のバインド規則に従います。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-160">Components follow C# name binding rules.</span></span> <span data-ttu-id="8dfb7-161">この例の `Index` コンポーネントの場合、スコープ内のコンポーネントは、次のすべてのコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-161">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="8dfb7-162">同じフォルダー (`Pages`) に含まれるもの。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-162">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="8dfb7-163">別の名前空間を明示的に指定しない、プロジェクトのルート内のコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-163">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="8dfb7-164">`global::` 修飾はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-164">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="8dfb7-165">別名が付けられた [`using`](/dotnet/csharp/language-reference/keywords/using-statement) ステートメント (`@using Foo = Bar` など) によるコンポーネントのインポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-165">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="8dfb7-166">部分修飾名はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-166">Partially qualified names aren't supported.</span></span> <span data-ttu-id="8dfb7-167">たとえば、`<Shared.NavMenu></Shared.NavMenu>` による `@using BlazorSample` の追加と `NavMenu` コンポーネント (`NavMenu.razor`) の参照はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-167">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="8dfb7-168">部分クラスのサポート</span><span class="sxs-lookup"><span data-stu-id="8dfb7-168">Partial class support</span></span>

<span data-ttu-id="8dfb7-169">Razor コンポーネントは、部分クラスとして生成されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-169">Razor components are generated as partial classes.</span></span> <span data-ttu-id="8dfb7-170">Razor コンポーネントは、次のいずれかの方法を使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-170">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="8dfb7-171">C# コードは、1 つのファイルに HTML マークアップと Razor コードを含む [`@code`][1] ブロックで定義します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-171">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="8dfb7-172">Blazor テンプレートでは、この方法を使用して Razor コンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-172">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="8dfb7-173">C# コードは、部分クラスとして定義されている分離コード ファイルに配置されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-173">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="8dfb7-174">次の例は、Blazor テンプレートから生成されたアプリ内の [`@code`][1] ブロックを含む既定の `Counter` コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-174">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="8dfb7-175">HTML マークアップ、Razor コード、C# コードは、同じファイル内にあります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-175">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="8dfb7-176">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-176">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="8dfb7-177">`Counter` コンポーネントは、部分クラスを含む分離コード ファイルを使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-177">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="8dfb7-178">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-178">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="8dfb7-179">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-179">`Counter.razor.cs`:</span></span>

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

<span data-ttu-id="8dfb7-180">必要に応じて、部分クラスファイルに必要な名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-180">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="8dfb7-181">Razor コンポーネントで使用される一般的な名前空間には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-181">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="8dfb7-182">`_Imports.razor` ファイルの [`@using`][2] ディレクティブは、C# ファイル (`.cs`) ではなく Razor ファイル (`.razor`) にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-182">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="8dfb7-183">基本クラスの指定</span><span class="sxs-lookup"><span data-stu-id="8dfb7-183">Specify a base class</span></span>

<span data-ttu-id="8dfb7-184">[`@inherits`][6] ディレクティブを使用して、コンポーネントの基本クラスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-184">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="8dfb7-185">次の例は、コンポーネントが基本クラス `BlazorRocksBase` を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-185">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="8dfb7-186">基本クラスは <xref:Microsoft.AspNetCore.Components.ComponentBase> から派生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-186">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="8dfb7-187">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-187">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="8dfb7-188">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-188">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="8dfb7-189">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="8dfb7-189">Use components</span></span>

<span data-ttu-id="8dfb7-190">コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-190">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="8dfb7-191">コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-191">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="8dfb7-192">`Pages/Index.razor` の次のマークアップでは、`HeadingComponent` インスタンスがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-192">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="8dfb7-193">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-193">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="8dfb7-194">コンポーネント名と一致しない最初の文字が大文字の HTML 要素がコンポーネントに含まれている場合、要素に予期しない名前が付いていることを示す警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-194">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="8dfb7-195">コンポーネントの名前空間に [`@using`][2] ディレクティブを追加すると、コンポーネントを使用できるようになり、警告が解決されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-195">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="8dfb7-196">パラメーター</span><span class="sxs-lookup"><span data-stu-id="8dfb7-196">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="8dfb7-197">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="8dfb7-197">Route parameters</span></span>

<span data-ttu-id="8dfb7-198">コンポーネントでは、[`@page`][9] ディレクティブに指定されたルート テンプレートからルート パラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-198">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="8dfb7-199">ルーターでは、ルート パラメーターを使用して、対応するコンポーネント パラメーターが設定されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-199">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8dfb7-200">省略可能なパラメーターがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-200">Optional parameters are supported.</span></span> <span data-ttu-id="8dfb7-201">次の例では、省略可能なパラメーター `text` を使用して、ルート セグメントの値をコンポーネントの `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-201">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="8dfb7-202">セグメントが存在しない場合、`Text` の値は `fantastic` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-202">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="8dfb7-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8dfb7-204">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-204">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="8dfb7-205">オプションのパラメーターはサポートされていないため、前の例では 2 つの [`@page`][9] ディレクティブが適用されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-205">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="8dfb7-206">1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-206">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="8dfb7-207">2 番目の [`@page`][9] ディレクティブでは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-207">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="8dfb7-208">複数のフォルダーにわたりパスをキャプチャするキャッチオール ルート パラメーター (`{*pageRoute}`) の詳細については、「<xref:blazor/fundamentals/routing#catch-all-route-parameters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-208">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="8dfb7-209">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="8dfb7-209">Component parameters</span></span>

<span data-ttu-id="8dfb7-210">コンポーネントには、"*コンポーネント パラメーター*" を指定できます。これは、[`[Parameter]` 属性](xref:Microsoft.AspNetCore.Components.ParameterAttribute)を指定したコンポーネント クラス上で、単純な、または複雑なパブリック プロパティを使用して定義します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-210">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="8dfb7-211">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-211">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="8dfb7-212">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-212">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="8dfb7-213">コンポーネント パラメーターには、既定値を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-213">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="8dfb7-214">サンプル アプリの次の例では、`ParentComponent` によって `ChildComponent` の `Title` プロパティの値を設定しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-214">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="8dfb7-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-215">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="8dfb7-216">慣例により、C# コードで構成される属性値は、[Razor の予約済み `@` シンボル](xref:mvc/views/razor#razor-syntax)を使用してパラメーターに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-216">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="8dfb7-217">親フィールドまたはプロパティ: `Title="@{FIELD OR PROPERTY}`。プレースホルダー `{FIELD OR PROPERTY}` は親コンポーネントの C# フィールドまたはプロパティです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-217">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="8dfb7-218">メソッドの結果: `Title="@{METHOD}"`。プレースホルダー `{METHOD}` は親コンポーネントの C# メソッドです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-218">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="8dfb7-219">[暗黙的または明示的な式](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`。プレースホルダー `{EXPRESSION}` は C# 式です。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-219">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="8dfb7-220">詳細については、「[ASP.NET Coreの Razor 構文リファレンス](xref:mvc/views/razor)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-220">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="8dfb7-221">独自の "*コンポーネント パラメーター*" を書き込み先とするコンポーネントを作成する代わりに、プライベート フィールドを使用してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-221">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="8dfb7-222">詳細については、「[上書きされたパラメーター](#overwritten-parameters)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-222">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="8dfb7-223">子コンテンツ</span><span class="sxs-lookup"><span data-stu-id="8dfb7-223">Child content</span></span>

<span data-ttu-id="8dfb7-224">コンポーネントでは、別のコンポーネントのコンテンツを設定できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-224">Components can set the content of another component.</span></span> <span data-ttu-id="8dfb7-225">割り当てコンポーネントでは、受信コンポーネントを指定するタグ間にコンテンツを指定します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-225">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="8dfb7-226">次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す <xref:Microsoft.AspNetCore.Components.RenderFragment> を表す `ChildContent` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-226">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="8dfb7-227">コンテンツをレンダリングする必要があるコンポーネントのマークアップに、`ChildContent` の値を配置します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-227">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="8dfb7-228">`ChildContent` の値は、親コンポーネントから受け取られ、ブートストラップ パネルの `panel-body` 内にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-228">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="8dfb7-229">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-229">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="8dfb7-230"><xref:Microsoft.AspNetCore.Components.RenderFragment> コンテンツを受け取るプロパティは、規則によって `ChildContent` という名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-230">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="8dfb7-231">サンプル アプリの `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` をレンダリングするためのコンテンツを提供できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-231">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="8dfb7-232">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-232">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="8dfb7-233">Blazor による子コンテンツのレンダリング方法により、`for` ループ内のコンポーネントのレンダリングでは、インクリメントするループ変数が子コンポーネントのコンテンツ内で使用されている場合、ローカル インデックス変数が必要になります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-233">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
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
> <span data-ttu-id="8dfb7-234">または、<xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> と共に `foreach` ループを使用します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-234">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="8dfb7-235"><xref:Microsoft.AspNetCore.Components.RenderFragment> を Razor コンポーネント UI のテンプレートとして使用する方法については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-235">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="8dfb7-236">属性スプラッティングと任意のパラメーター</span><span class="sxs-lookup"><span data-stu-id="8dfb7-236">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="8dfb7-237">コンポーネントでは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャしてレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-237">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="8dfb7-238">追加の属性は、ディクショナリにキャプチャし、[`@attributes`][3] Razor ディレクティブを使用して、コンポーネントがレンダリングされるときに、要素に "*スプラッティング*" できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-238">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="8dfb7-239">このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-239">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="8dfb7-240">たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-240">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="8dfb7-241">次の例で、最初の `<input>` 要素 (`id="useIndividualParams"`) では、個々のコンポーネント パラメーターを使用していますが、2 番目の `<input>` 要素 (`id="useAttributesDict"`) では、属性スプラッティングを使用しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-241">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="8dfb7-242">パラメーターの型は、文字列キーで `IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-242">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="8dfb7-243">両方の方法を使用してレンダリングされる `<input>` 要素は同じです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-243">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="8dfb7-244">任意の属性を受け入れるには、<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> プロパティを `true` に設定した [`[Parameter]` 属性](xref:Microsoft.AspNetCore.Components.ParameterAttribute)を使用して、コンポーネント パラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-244">To accept arbitrary attributes, define a component parameter using the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="8dfb7-245">[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) の <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> プロパティにより、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-245">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="8dfb7-246">1 つのコンポーネントで、<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> を持つパラメーターは 1 つだけ定義できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-246">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="8dfb7-247"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-247">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="8dfb7-248">このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` も使用できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-248">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="8dfb7-249">要素属性の位置を基準とした [`@attributes`][3] の位置は重要です。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-249">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="8dfb7-250">[`@attributes`][3] が要素にスプラッティングされると、属性は右から左 (最後から最初) に処理されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-250">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="8dfb7-251">`Child` コンポーネントを使用する次のコンポーネントの例を考えます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-251">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="8dfb7-252">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-252">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="8dfb7-253">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-253">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="8dfb7-254">`Child` コンポーネントの `extra` 属性が [`@attributes`][3] の右側に設定されています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-254">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="8dfb7-255">属性は右から左 (最後から最初) に処理されるため、追加の属性によって渡された場合に、`Parent` コンポーネントのレンダリングされる `<div>` に、`extra="5"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-255">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="8dfb7-256">次の例では、`Child` コンポーネントの `<div>` で、`extra` と [`@attributes`][3] の順序が逆になります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-256">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="8dfb7-257">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-257">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="8dfb7-258">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-258">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="8dfb7-259">追加の属性によって渡された場合に、`Parent` コンポーネント内のレンダリングされる `<div>` には、`extra="10"` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-259">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="8dfb7-260">コンポーネントへの参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="8dfb7-260">Capture references to components</span></span>

<span data-ttu-id="8dfb7-261">コンポーネント参照によって、コンポーネント インスタンスを参照する方法が得られるため、そのインスタンスに `Show` や `Reset` などのコマンドを発行できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-261">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="8dfb7-262">コンポーネント参照をキャプチャするには:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-262">To capture a component reference:</span></span>

* <span data-ttu-id="8dfb7-263">子コンポーネントに [`@ref`][4] 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-263">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="8dfb7-264">子コンポーネントと同じ型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-264">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="8dfb7-265">コンポーネントがレンダリングされると、`loginDialog` フィールドに `CustomLoginDialog` 子コンポーネント インスタンスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-265">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="8dfb7-266">これにより、コンポーネント インスタンスに対し、.NET メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-266">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8dfb7-267">`loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-267">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="8dfb7-268">コンポーネントがレンダリングされるまで、参照するものはありません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-268">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="8dfb7-269">コンポーネントのレンダリングが完了した後にコンポーネント参照を操作するには、[`OnAfterRenderAsync`メソッドまたは `OnAfterRender` メソッド](xref:blazor/components/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-269">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="8dfb7-270">イベント ハンドラーで参照変数を使用するには、ラムダ式を使用するか、[`OnAfterRenderAsync` または `OnAfterRender` メソッド](xref:blazor/components/lifecycle#after-component-render)でイベント ハンドラー デリゲートを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-270">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="8dfb7-271">これにより、イベント ハンドラーが割り当てられる前に参照変数が確実に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-271">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="8dfb7-272">ループ内のコンポーネントを参照するには、「[Capture references to multiple similar child-components](https://github.com/dotnet/aspnetcore/issues/13358)」(複数の類似した子コンポーネントへの参照をキャプチャする) (dotnet/aspnetcore #13358) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-272">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="8dfb7-273">コンポーネント参照のキャプチャでは、[要素参照のキャプチャ](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)と類似の構文を使用しますが、それは JavaScript 相互運用機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-273">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="8dfb7-274">コンポーネント参照は、JavaScript コードに渡されません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-274">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="8dfb7-275">コンポーネント参照は、.NET コードでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-275">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="8dfb7-276">子コンポーネントの状態を変えるためにコンポーネント参照を使用 **しない** でください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-276">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="8dfb7-277">代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-277">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="8dfb7-278">通常の宣言型パラメーターを使用すると、子コンポーネントが正しいタイミングで自動的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-278">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="8dfb7-279">同期コンテキスト</span><span class="sxs-lookup"><span data-stu-id="8dfb7-279">Synchronization context</span></span>

<span data-ttu-id="8dfb7-280">Blazor では、同期コンテキスト (<xref:System.Threading.SynchronizationContext>) を使用して、1 つの実行の論理スレッドを強制します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-280">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="8dfb7-281">コンポーネントの[ライフサイクル メソッド](xref:blazor/components/lifecycle)と、Blazor によって発生するすべてのイベント コールバックは、同期コンテキストで実行されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-281">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="8dfb7-282">Blazor Server の同期コンテキストでは、ブラウザーの WebAssembly モデル (シングル スレッド) と厳密に一致するように、シングルスレッド環境のエミュレートが試行されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-282">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="8dfb7-283">どの時点でも、作業は 1 つのスレッドでのみ実行され、1 つの論理スレッドであるという印象になります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-283">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="8dfb7-284">2 つの操作が同時に実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-284">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="8dfb7-285">スレッドをブロックする呼び出しを避ける</span><span class="sxs-lookup"><span data-stu-id="8dfb7-285">Avoid thread-blocking calls</span></span>

<span data-ttu-id="8dfb7-286">一般に、次のメソッドは呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-286">Generally, don't call the following methods.</span></span> <span data-ttu-id="8dfb7-287">次のメソッドでは、スレッドがブロックされます。そのため、基になる <xref:System.Threading.Tasks.Task> が完了するまで、アプリの動作が再開されなくなります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-287">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="8dfb7-288">状態を更新するために外部でコンポーネント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="8dfb7-288">Invoke component methods externally to update state</span></span>

<span data-ttu-id="8dfb7-289">タイマーやその他の通知などの外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。これにより、Blazor の同期コンテキストにディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-289">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="8dfb7-290">たとえば、リッスンしているコンポーネントに、更新状態を通知できる *通知サービス* を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-290">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="8dfb7-291">`NotifierService` を登録します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-291">Register the `NotifierService`:</span></span>

* <span data-ttu-id="8dfb7-292">Blazor WebAssembly で、`Program.Main` のシングルトンとしてサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-292">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="8dfb7-293">Blazor Server で、`Startup.ConfigureServices` のスコープとしてサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-293">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="8dfb7-294">`NotifierService` を使用して、コンポーネントを更新します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-294">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="8dfb7-295">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-295">In the preceding example:</span></span>

* <span data-ttu-id="8dfb7-296">Blazor の同期コンテキスト外で `NotifierService` からコンポーネントの `OnNotify` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-296">`NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="8dfb7-297">`InvokeAsync` を使用して、正しいコンテキストに切り替え、レンダリングをキューに登録します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-297">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="8dfb7-298">詳細については、「<xref:blazor/components/rendering>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-298">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="8dfb7-299">コンポーネントは <xref:System.IDisposable> を実装し、`OnNotify` デリゲートは `Dispose` メソッドでサブスクライブを解除します。このメソッドは、コンポーネントが破棄されたときにフレームワークによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-299">The component implements <xref:System.IDisposable>, and the `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="8dfb7-300">詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-300">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="8dfb7-301">\@ キーを使用して要素とコンポーネントの保存を制御する</span><span class="sxs-lookup"><span data-stu-id="8dfb7-301">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="8dfb7-302">要素またはコンポーネントのリストをレンダリングし、その後に要素またはコンポーネントが変更された場合、Blazor の比較アルゴリズムでは、前のどの要素やコンポーネントを保持できるか、およびモデル オブジェクトをそれらにどのようにマップするかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-302">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="8dfb7-303">通常、このプロセスは自動で、無視できますが、プロセスの制御が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-303">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="8dfb7-304">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-304">Consider the following example:</span></span>

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

<span data-ttu-id="8dfb7-305">`People` コレクションのコンテンツは、挿入、削除、または順序変更されたエントリによって変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-305">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="8dfb7-306">コンポーネントのレンダリング時に、`<DetailsEditor>` コンポーネントが変更され、異なる `Details` パラメーター値を受け取ることがあります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-306">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="8dfb7-307">これにより、予期したものよりも複雑な再レンダリングが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-307">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="8dfb7-308">場合によっては、再レンダリングによって、要素のフォーカスの喪失などの表示動作の違いが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-308">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="8dfb7-309">マッピング プロセスは、[`@key`][5] ディレクティブ属性を使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-309">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="8dfb7-310">[`@key`][5] により、比較アルゴリズムで、キーの値に基づいて要素やコンポーネントが確実に保持されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-310">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="8dfb7-311">`People` コレクションが変更されても、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンス間の関連付けが保持されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-311">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="8dfb7-312">`Person` が `People` リストから削除された場合、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-312">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="8dfb7-313">他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-313">Other instances are left unchanged.</span></span>
* <span data-ttu-id="8dfb7-314">リスト内の特定の位置に `Person` が挿入されると、その対応する位置に、1 つの新しい `<DetailsEditor>` インスタンスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-314">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="8dfb7-315">他のインスタンスは変更されません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-315">Other instances are left unchanged.</span></span>
* <span data-ttu-id="8dfb7-316">`Person` エントリの順序が変更された場合、対応する `<DetailsEditor>` インスタンスは UI で保持され、順序が変更されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-316">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="8dfb7-317">シナリオによっては、[`@key`][5] を使用すると、レンダリングの複雑さが最小限に抑えられ、フォーカス位置など、DOM 変更のステートフルな部分の潜在的な問題を回避できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-317">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8dfb7-318">キーは、各コンテナー要素やコンポーネントに対してローカルです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-318">Keys are local to each container element or component.</span></span> <span data-ttu-id="8dfb7-319">キーはドキュメント全体でグローバルに比較されません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-319">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="8dfb7-320">\@ キーを使用する場面</span><span class="sxs-lookup"><span data-stu-id="8dfb7-320">When to use \@key</span></span>

<span data-ttu-id="8dfb7-321">一般に、リストがレンダリングされ (たとえば、[foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ブロックで)、[`@key`][5] を定義するための適切な値が存在する場合は常に、[`@key`][5] を使用することは意味があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-321">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="8dfb7-322">また、[`@key`][5] を使用して、オブジェクトが変更されたときに Blazor が要素やコンポーネントのサブツリーを保持しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-322">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="8dfb7-323">`@currentPerson` が変更された場合、[`@key`][5] 属性ディレクティブによって、Blazor に、`<div>` とその子孫全体を破棄させ、新しい要素とコンポーネントで UI 内のサブツリーをリビルドさせます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-323">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="8dfb7-324">これは、`@currentPerson` が変更されたときに、UI の状態が確実に保持されないようにする必要がある場合に役立つ可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-324">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="8dfb7-325">\@ キーを使用しない場面</span><span class="sxs-lookup"><span data-stu-id="8dfb7-325">When not to use \@key</span></span>

<span data-ttu-id="8dfb7-326">[`@key`][5] で比較すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-326">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="8dfb7-327">パフォーマンスの低下は大きくありませんが、要素やコンポーネントの保存規則を制御することによって、アプリにメリットがある場合にのみ [`@key`][5] を指定してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-327">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="8dfb7-328">[`@key`][5] を使用しない場合でも、Blazor では可能な限り、子要素とコンポーネント インスタンスが保持されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-328">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="8dfb7-329">[`@key`][5] を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネント インスタンスにモデル インスタンスをマップする "*方法*" を制御することです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-329">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="8dfb7-330">\@ キーに使用する値</span><span class="sxs-lookup"><span data-stu-id="8dfb7-330">What values to use for \@key</span></span>

<span data-ttu-id="8dfb7-331">一般に、[`@key`][5] には、次のいずれかの種類の値を指定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-331">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="8dfb7-332">モデル オブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-332">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="8dfb7-333">これにより、オブジェクト参照の等価性に基づいて保持されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-333">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="8dfb7-334">一意の識別子 (たとえば、`int` 型、`string` 型、`Guid` 型の主キー値)。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-334">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="8dfb7-335">[`@key`][5] に使用される値は確実に競合しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-335">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="8dfb7-336">同じ親要素内で競合する値が検出された場合、Blazor では、古い要素やコンポーネントを新しい要素やコンポーネントに確定的にマップできないため、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-336">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="8dfb7-337">個別の値 (オブジェクト インスタンスや主キー値など) のみを使用してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-337">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="8dfb7-338">上書きされたパラメーター</span><span class="sxs-lookup"><span data-stu-id="8dfb7-338">Overwritten parameters</span></span>

<span data-ttu-id="8dfb7-339">Blazor フレームワークでは、一般に安全な親から子へのパラメーターの割り当てが行われます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-339">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="8dfb7-340">パラメーターが予期せずに上書きされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-340">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="8dfb7-341">副作用は最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-341">Side-effects are minimized.</span></span> <span data-ttu-id="8dfb7-342">たとえば、追加のレンダリングは、無限のレンダリング ループが作成される可能性があるため、回避されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-342">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="8dfb7-343">子コンポーネントは、親コンポーネントのレンダリング時に既存の値を上書きする可能性がある新しいパラメーター値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-343">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="8dfb7-344">子コンポーネントでパラメーター値が誤って上書きされることは、1 つまたは複数のデータバインド パラメーターを使用してコンポーネントを開発しており、開発者が子のパラメーターに直接書き込む場合に多く発生します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-344">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="8dfb7-345">子コンポーネントは、親コンポーネントの 1 つまたは複数のパラメーター値を使用してレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-345">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="8dfb7-346">子によって、パラメーターの値が直接書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-346">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="8dfb7-347">親コンポーネントがレンダリングされ、子のパラメーターの値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-347">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="8dfb7-348">パラメーター値の上書きの可能性は、子コンポーネントのプロパティ セッターにも及びます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-348">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="8dfb7-349">**一般的なガイダンスとして、独自のパラメーターに直接書き込むコンポーネントを作成しないでください。**</span><span class="sxs-lookup"><span data-stu-id="8dfb7-349">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="8dfb7-350">次が実行される、問題のある `Expander` コンポーネントについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-350">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="8dfb7-351">子コンテンツのレンダリング。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-351">Renders child content.</span></span>
* <span data-ttu-id="8dfb7-352">コンポーネント パラメーター (`Expanded`) を使用した、子コンテンツの表示の切り替え。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-352">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="8dfb7-353">コンポーネントによって、`Expanded` パラメーターに直接書き込まれます。これは上書きされるパラメーターの問題を示しているため、回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-353">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

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

<span data-ttu-id="8dfb7-354">`Expander` コンポーネントは、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出す可能性のある親コンポーネントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-354">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

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

<span data-ttu-id="8dfb7-355">初期状態では、`Expanded` プロパティが切り替えられると、`Expander` コンポーネントはそれぞれ独立して動作します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-355">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="8dfb7-356">子コンポーネントの状態は、想定どおりのままです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-356">The child components maintain their states as expected.</span></span> <span data-ttu-id="8dfb7-357">親で <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> が呼び出されると、最初の子コンポーネントの `Expanded` パラメーターが初期値 (`true`) にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-357">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="8dfb7-358">2 つめの `Expander` コンポーネントの `Expanded` 値はリセットされません。これは、2 つめのコンポーネントでは子コンテンツがレンダリングされないためです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-358">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="8dfb7-359">前のシナリオでの状態を維持するには、`Expander` コンポーネントで "*プライベート フィールド*" を使用して、切り替え状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-359">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="8dfb7-360">次の変更された `Expander` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-360">The following revised `Expander` component:</span></span>

* <span data-ttu-id="8dfb7-361">親から `Expanded` コンポーネント パラメーター値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-361">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="8dfb7-362">コンポーネント パラメーター値を、[OnInitialized イベント](xref:blazor/components/lifecycle#component-initialization-methods) の "*プライベート フィールド*" (`expanded`) に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-362">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="8dfb7-363">プライベート フィールドを使用して、その内部のトグル状態を維持します。これは、パラメーターに直接書き込まれないようにする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-363">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

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

<span data-ttu-id="8dfb7-364">詳細については、[Blazor両方向のバインド エラー (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-364">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="8dfb7-365">属性を適用する</span><span class="sxs-lookup"><span data-stu-id="8dfb7-365">Apply an attribute</span></span>

<span data-ttu-id="8dfb7-366">属性は、[`@attribute`][7] ディレクティブを使用して Razor コンポーネントに適用できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-366">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="8dfb7-367">次の例では、[`[Authorize]` 属性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)をコンポーネント クラスに適用しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-367">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="8dfb7-368">条件付き HTML 要素属性</span><span class="sxs-lookup"><span data-stu-id="8dfb7-368">Conditional HTML element attributes</span></span>

<span data-ttu-id="8dfb7-369">HTML 要素属性は、.NET 値に基づいて条件付きでレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-369">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="8dfb7-370">値が `false` または `null` の場合、属性はレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-370">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="8dfb7-371">値が `true` の場合、属性が最小化されてレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-371">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="8dfb7-372">次の例では、`IsCompleted` によって `checked` が要素のマークアップにレンダリングされるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-372">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="8dfb7-373">`IsCompleted` が `true` の場合、このチェック ボックスは次のようにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-373">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="8dfb7-374">`IsCompleted` が `false` の場合、このチェック ボックスは次のようにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-374">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="8dfb7-375">詳細については、「[ASP.NET Coreの Razor 構文リファレンス](xref:mvc/views/razor)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-375">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="8dfb7-376">.NET 型が `bool` の場合、[`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) などの一部の HTML 属性が正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-376">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="8dfb7-377">そのような場合は、`bool` ではなく `string` 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-377">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="8dfb7-378">生 HTML</span><span class="sxs-lookup"><span data-stu-id="8dfb7-378">Raw HTML</span></span>

<span data-ttu-id="8dfb7-379">通常、文字列は DOM テキスト ノードを使用してレンダリングされます。つまり、それらに含まれている可能性のあるすべてのマークアップが無視され、リテラル テキストとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-379">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="8dfb7-380">生 HTML をレンダリングするには、HTML コンテンツを `MarkupString` 値にラップします。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-380">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="8dfb7-381">値は HTML または SVG として解析され、DOM に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-381">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="8dfb7-382">信頼されていないソースから構築された生 HTML をレンダリングすることは、**セキュリティ リスク** であるため、避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-382">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="8dfb7-383">次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-383">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="8dfb7-384">Razor テンプレート</span><span class="sxs-lookup"><span data-stu-id="8dfb7-384">Razor templates</span></span>

<span data-ttu-id="8dfb7-385">レンダリング フラグメントは、Razor テンプレート構文を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-385">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="8dfb7-386">Razor テンプレートは、UI スニペットを定義する 1 つの方法であり、次の形式を想定しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-386">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="8dfb7-387">次の例では、<xref:Microsoft.AspNetCore.Components.RenderFragment> と <xref:Microsoft.AspNetCore.Components.RenderFragment%601> の値を指定し、コンポーネント内にテンプレートを直接レンダリングする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-387">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="8dfb7-388">レンダリング フラグメントは、引数として[テンプレート コンポーネント](xref:blazor/components/templated-components)に渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-388">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="8dfb7-389">前のコードのレンダリングされた結果:</span><span class="sxs-lookup"><span data-stu-id="8dfb7-389">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="8dfb7-390">静的な資産</span><span class="sxs-lookup"><span data-stu-id="8dfb7-390">Static assets</span></span>

<span data-ttu-id="8dfb7-391">Blazor は、プロジェクトの [`web root (wwwroot)` フォルダー](xref:fundamentals/index#web-root)に静的アセットを配置する ASP.NET Core アプリの規則に従います。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-391">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="8dfb7-392">静的アセットの Web ルートを参照するには、ベース相対パス (`/`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-392">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="8dfb7-393">次の例では、`logo.png` が物理的に `{PROJECT ROOT}/wwwroot/images` フォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-393">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="8dfb7-394">Razor コンポーネントでは、チルダ スラッシュ表記 (`~/`) はサポートされて **いません**。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-394">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="8dfb7-395">アプリのベース パスの設定の詳細については、「<xref:blazor/host-and-deploy/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-395">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="8dfb7-396">タグ ヘルパーはコンポーネントでサポートされない</span><span class="sxs-lookup"><span data-stu-id="8dfb7-396">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="8dfb7-397">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) は、Razor コンポーネント (`.razor` ファイル) ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-397">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="8dfb7-398">Blazor にタグ ヘルパーのような機能を提供するには、タグ ヘルパーと同じ機能を持つコンポーネントを作成し、代わりにそのコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-398">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="8dfb7-399">スケーラブル ベクター グラフィックス (SVG) イメージ</span><span class="sxs-lookup"><span data-stu-id="8dfb7-399">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="8dfb7-400">Blazor では HTML がレンダリングされるため、スケーラブル ベクター グラフィックス (SVG) 画像 (`.svg`) などのブラウザーでサポートされている画像は、`<img>` タグを介してサポートされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-400">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="8dfb7-401">同様に、SVG 画像は、スタイルシート ファイル (`.css`) の CSS 規則でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-401">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="8dfb7-402">ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-402">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="8dfb7-403">`<svg>` タグをコンポーネント ファイル (`.razor`) に直接配置した場合、基本的な画像レンダリングはサポートされますが、多くの高度なシナリオはまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-403">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="8dfb7-404">たとえば、`<use>` タグは現在考慮されないため、一部の SVG タグで [`@bind`][10] を使用できません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-404">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="8dfb7-405">詳細については、[Blazor の SVG サポート (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-405">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="8dfb7-406">空白文字のレンダリング動作</span><span class="sxs-lookup"><span data-stu-id="8dfb7-406">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8dfb7-407">[`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) ディレクティブが `true`の値と共に使用されている場合を除き、既定では、次の場合に余分な空白が削除されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-407">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="8dfb7-408">要素内の先頭または末尾。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-408">Leading or trailing within an element.</span></span>
* <span data-ttu-id="8dfb7-409">`RenderFragment` パラメーター内の先頭または末尾。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-409">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="8dfb7-410">別のコンポーネントに渡される子コンテンツなどです。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-410">For example, child content passed to another component.</span></span>
* <span data-ttu-id="8dfb7-411">`@if` または `@foreach` のような、C# コード ブロックの前か後にある。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-411">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="8dfb7-412">空白文字の削除は、`white-space: pre` などの CSS ルールを使用するときに、レンダリングされた出力に影響を与えることがあります。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-412">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="8dfb7-413">このパフォーマンスの最適化を無効にして、空白を保持するには、次のいずれかの操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-413">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="8dfb7-414">特定のコンポーネントに設定を適用するには、 `.razor` ファイルの先頭に `@preservewhitespace true` ディレクティブを追加する。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-414">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="8dfb7-415">サブディレクトリ全体またはプロジェクト全体に設定を適用するには、 `_Imports.razor` ファイル内に `@preservewhitespace true` ディレクティブを追加する。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-415">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="8dfb7-416">ほとんどの場合、アプリでは一般的に通常の動作が続行されるため (ただし、速くなります)、何の措置も必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-416">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="8dfb7-417">空白文字の削除で特定のコンポーネントに問題が発生する場合、そのコンポーネントで `@preservewhitespace true` を使用し、この最適化を無効にします。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-417">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8dfb7-418">空白は、コンポーネントのソース コードに保持されます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-418">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="8dfb7-419">空白文字のみのテキストは、視覚効果がないときでも、ブラウザーのドキュメント オブジェクト モデル (DOM) にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-419">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="8dfb7-420">次の Razor コンポーネント コードについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-420">Consider the following Razor component code:</span></span>

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

<span data-ttu-id="8dfb7-421">前の例では、次の不要な空白文字がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-421">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="8dfb7-422">`@foreach` コード ブロックの外側。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-422">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="8dfb7-423">`<li>` 要素の前後。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-423">Around the `<li>` element.</span></span>
* <span data-ttu-id="8dfb7-424">`@item.Text` 出力の前後。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-424">Around the `@item.Text` output.</span></span>

<span data-ttu-id="8dfb7-425">100 項目を含むリストでは、402 の空白領域になり、余分な空白が、レンダリングされる出力に視覚的に影響を及ぼすことはありません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-425">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="8dfb7-426">コンポーネントの静的 HTML をレンダリングする場合、タグ内の空白文字は保持されません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-426">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="8dfb7-427">たとえば、レンダリングされた出力の次のコンポーネントのソースをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-427">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="8dfb7-428">前の Razor マークアップからの、空白は保持されません。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-428">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8dfb7-429">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8dfb7-429">Additional resources</span></span>

* <span data-ttu-id="8dfb7-430"><xref:blazor/security/server/threat-mitigation>:リソース不足に対処する必要がある Blazor Server アプリの構築に関するガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8dfb7-430"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

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

---
title: ASP.NET Core Blazor で .NET メソッドから JavaScript 関数を呼び出す
author: guardrex
description: Blazor アプリで .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: ca42b611a61fc394655e396f914e8e050c578e6a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530087"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="d9b0b-103">ASP.NET Core Blazor で .NET メソッドから JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="d9b0b-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="d9b0b-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Pranav Krishnamoorthy](https://github.com/pranavkm)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d9b0b-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d9b0b-105">Blazor アプリでは、.NET メソッドから JavaScript 関数を呼び出すことも、JavaScript 関数から .NET メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="d9b0b-106">これらのシナリオは、"*JavaScript 相互運用*" ("*JS 相互運用*") と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="d9b0b-107">この記事では、.NET から JavaScript 関数を呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="d9b0b-108">JavaScript から .NET メソッドを呼び出す方法については、「<xref:blazor/call-dotnet-from-javascript>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="d9b0b-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="d9b0b-110">`wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) では、終了タグ `</body>` の前に JS ファイル (`<script>` タグ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-110">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="d9b0b-111">JS 相互運用メソッドを含む JS ファイルは、Blazor フレームワークの JS ファイルよりも先に含めるようにします。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-111">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

<span data-ttu-id="d9b0b-112">.NET から JavaScript を呼び出すには、<xref:Microsoft.JSInterop.IJSRuntime> 抽象化を使用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-112">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="d9b0b-113">JS 相互運用呼び出しを発行するには、コンポーネントに <xref:Microsoft.JSInterop.IJSRuntime> 抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-113">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="d9b0b-114"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> は、JSON シリアル化可能な任意の数の引数と共に呼び出す JavaScript 関数の識別子を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-114"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="d9b0b-115">関数の識別子は、グローバル スコープ (`window`) に関連しています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-115">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="d9b0b-116">`window.someScope.someFunction` を呼び出す場合、識別子は `someScope.someFunction` です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-116">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="d9b0b-117">関数は、呼び出す前に登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-117">There's no need to register the function before it's called.</span></span> <span data-ttu-id="d9b0b-118">また、戻り値の型 `T` も JSON シリアル化可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-118">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="d9b0b-119">`T` は、返される JSON 型に最適にマップされる .NET 型と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-119">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="d9b0b-120">[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) を返す JavaScript 関数は、<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> と共に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-120">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="d9b0b-121">`InvokeAsync` は Promise のラップを解除し、Promise によって待機された値を返します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-121">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="d9b0b-122">Blazor Server アプリでプリレンダリングが有効になっている場合、最初のプリレンダリング中に JavaScript を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-122">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="d9b0b-123">JavaScript 相互運用呼び出しは、ブラウザーとの接続が確立されるまで遅延させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-123">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="d9b0b-124">詳細については、「[Blazor Server アプリがプリレンダリングされていることを検出する](#detect-when-a-blazor-server-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-124">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="d9b0b-125">次の例は、JavaScript ベースのデコーダーである [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-125">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="d9b0b-126">この例では、開発者コードから既存の JavaScript API に要件をオフロードする C# メソッドから JavaScript 関数を呼び出す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-126">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="d9b0b-127">JavaScript 関数は、C# メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-127">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="d9b0b-128">`wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) の `<head>` 要素内で、`TextDecoder` を使用して、渡された配列をデコードし、デコードした値を返す JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-128">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="d9b0b-129">JavaScript コードでは、前の例で示したコードのように、スクリプト ファイルへの参照を使用して JavaScript ファイル (`.js`) から読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-129">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="d9b0b-130">次のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="d9b0b-130">The following component:</span></span>

* <span data-ttu-id="d9b0b-131">コンポーネント ボタン ( **`Convert Array`** ) が選択された場合、`JS` を使用して `convertArray` JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-131">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="d9b0b-132">JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-132">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="d9b0b-133">文字列は、表示できるようにコンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-133">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="d9b0b-134">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="d9b0b-134">IJSRuntime</span></span>

<span data-ttu-id="d9b0b-135"><xref:Microsoft.JSInterop.IJSRuntime> 抽象化を使用するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-135">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="d9b0b-136">Razor コンポーネント (`.razor`) に <xref:Microsoft.JSInterop.IJSRuntime> 抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-136">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="d9b0b-137">`wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-137">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="d9b0b-138">関数は <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> を指定して呼び出され、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-138">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="d9b0b-139"><xref:Microsoft.JSInterop.IJSRuntime> 抽象化をクラス (`.cs`) に挿入します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-139">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="d9b0b-140">`wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-140">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="d9b0b-141">関数は `JS.InvokeAsync` を指定して呼び出され、次の値を返します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-141">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="d9b0b-142">[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) でコンテンツを動的に生成するには、`[Inject]` 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-142">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="d9b0b-143">このトピックに添付されているクライアント側のサンプル アプリでは、ユーザー入力を受け取り、ウェルカム メッセージを表示するために、DOM とやりとりする 2 つの JavaScript 関数をアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-143">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="d9b0b-144">`showPrompt`:ユーザー入力 (ユーザーの名前) を受け入れるプロンプトを生成し、名前を呼び出し元に返します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-144">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="d9b0b-145">`displayWelcome`:`welcome` の `id` を持つ DOM オブジェクトに、呼び出し元からのウェルカム メッセージを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-145">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="d9b0b-146">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="d9b0b-146">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="d9b0b-147">JavaScript ファイルを参照する `<script>` タグを `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) に配置します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-147">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="d9b0b-148">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="d9b0b-148">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="d9b0b-149">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="d9b0b-149">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="d9b0b-150">`<script>` タグを動的に更新できないため、`<script>` タグをコンポーネント ファイル内に配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-150">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="d9b0b-151">.NET メソッドは、<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> を呼び出して、`exampleJsInterop.js` ファイル内の JavaScript 関数と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-151">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="d9b0b-152"><xref:Microsoft.JSInterop.IJSRuntime> 抽象化は、Blazor Server のシナリオを可能にするために非同期です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-152">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="d9b0b-153">アプリが Blazor WebAssembly アプリであり、JavaScript 関数を同期的に呼び出す必要がある場合は、<xref:Microsoft.JSInterop.IJSInProcessRuntime> にダウンキャストし、代わりに <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-153">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="d9b0b-154">ほとんどの JS 相互運用ライブラリでは、確実にすべてのシナリオでライブラリを使用できるように、非同期 API を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-154">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d9b0b-155">標準 [JavaScript モジュール](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)で JavaScript の分離を有効にするには、「[Blazor JavaScript の分離とオブジェクト参照](#blazor-javascript-isolation-and-object-references)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-155">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="d9b0b-156">サンプル アプリには、JS 相互運用を示すコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-156">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="d9b0b-157">コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="d9b0b-157">The component:</span></span>

* <span data-ttu-id="d9b0b-158">JavaScript プロンプトを介してユーザー入力を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-158">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="d9b0b-159">テキストをコンポーネントに返して処理します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-159">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="d9b0b-160">DOM とやりとりしてウェルカム メッセージを表示する 2 番目の JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-160">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="d9b0b-161">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="d9b0b-161">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="d9b0b-162">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-162">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="d9b0b-163">コンポーネントの **`Trigger JavaScript Prompt`** ボタンを選択して `TriggerJsPrompt` を実行すると、`wwwroot/exampleJsInterop.js` ファイル内に指定した JavaScript `showPrompt` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-163">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="d9b0b-164">`showPrompt` 関数は、ユーザー入力 (ユーザーの名前) を受け取ります。これは、HTML エンコードされ、コンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-164">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="d9b0b-165">コンポーネントにより、ユーザーの名前がローカル変数 `name` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-165">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="d9b0b-166">`name` に格納された文字列は、ウェルカム メッセージに組み込まれます。このメッセージが JavaScript 関数 `displayWelcome` に渡され、ウェルカム メッセージが見出しタグにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-166">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="d9b0b-167">void JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="d9b0b-167">Call a void JavaScript function</span></span>

<span data-ttu-id="d9b0b-168">次の場合に <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> を使用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-168">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="d9b0b-169">[void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) または [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) を返す JavaScript 関数。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-169">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="d9b0b-170">JavaScript 呼び出しの結果を読み取るために .NET が不要な場合。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-170">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="d9b0b-171">Blazor Server アプリがプリレンダリングされていることを検出する</span><span class="sxs-lookup"><span data-stu-id="d9b0b-171">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="d9b0b-172">要素への参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="d9b0b-172">Capture references to elements</span></span>

<span data-ttu-id="d9b0b-173">一部の JS 相互運用シナリオでは、HTML 要素への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-173">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="d9b0b-174">たとえば、UI ライブラリで初期化のための要素参照が必要な場合、`focus` や `play` などの要素でコマンドのような API の呼び出し必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-174">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="d9b0b-175">次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-175">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="d9b0b-176">`@ref` 属性を HTML 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-176">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="d9b0b-177">名前が `@ref` 属性の値に一致する <xref:Microsoft.AspNetCore.Components.ElementReference> 型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-177">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="d9b0b-178">次の例は、`username` `<input>` 要素への参照をキャプチャする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-178">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="d9b0b-179">Blazor とやりとりしない空の要素のコンテンツを変化させるには、要素参照のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-179">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="d9b0b-180">このシナリオは、サードパーティの API から要素にコンテンツが提供される場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-180">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="d9b0b-181">Blazor は要素とやりとりしないため、Blazor の要素表現と DOM との間に競合が発生する可能性がありません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-181">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="d9b0b-182">次の例では、Blazor が DOM とやりとりしてこの要素のリスト項目 (`<li>`) を設定するため、順序なしリスト (`ul`) のコンテンツを変化させるのは "*危険*" です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-182">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="d9b0b-183">JS 相互運用により要素 `MyList` のコンテンツが変更され、Blazor でその要素に差分を適用しようとした場合、差分は DOM と一致しません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-183">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="d9b0b-184"><xref:Microsoft.AspNetCore.Components.ElementReference> は JS Interop 経由で JavaScript コードに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-184">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="d9b0b-185">JavaScript コードが `HTMLElement` インスタンスを受け取り、通常の DOM API で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-185">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="d9b0b-186">たとえば、次のコードでは、要素にマウス クリックを送信できるようにする .NET 拡張メソッドを定義しています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-186">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="d9b0b-187">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="d9b0b-187">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d9b0b-188">C# コードで [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) を使用し、Blazor フレームワークに組み込まれ、要素参照と連動する要素にフォーカスを合わせます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-188">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="d9b0b-189">値を返さない JavaScript 関数を呼び出すには、<xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> を使用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-189">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d9b0b-190">次のコードは、キャプチャされた <xref:Microsoft.AspNetCore.Components.ElementReference> で前述の JavaScript 関数を呼び出し、クライアント側の `Click` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-190">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="d9b0b-191">拡張メソッドを使用するには、<xref:Microsoft.JSInterop.IJSRuntime> インスタンスを受け取る静的拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-191">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="d9b0b-192">`clickElement` メソッドは、オブジェクトで直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-192">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="d9b0b-193">次の例では、`TriggerClickEvent` メソッドが `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-193">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="d9b0b-194">`exampleButton` 変数は、コンポーネントがレンダリングされた後にのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-194">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="d9b0b-195">未入力の <xref:Microsoft.AspNetCore.Components.ElementReference> が JavaScript コードに渡された場合、JavaScript コードは `null` の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-195">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="d9b0b-196">コンポーネントのレンダリングが完了した後に要素参照を操作するには、[`OnAfterRenderAsync` または `OnAfterRender` コンポーネント ライフサイクル メソッド](xref:blazor/components/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-196">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="d9b0b-197">ジェネリック型を操作して値を返す場合は、<xref:System.Threading.Tasks.ValueTask%601> を使用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-197">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="d9b0b-198">`GenericMethod` は、型を持つオブジェクトで直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-198">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="d9b0b-199">次の例では、`GenericMethod` が `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-199">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="d9b0b-200">コンポーネント間で要素を参照する</span><span class="sxs-lookup"><span data-stu-id="d9b0b-200">Reference elements across components</span></span>

<span data-ttu-id="d9b0b-201"><xref:Microsoft.AspNetCore.Components.ElementReference> は次の理由からコンポーネントで渡すことができません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="d9b0b-202">インスタンスはコンポーネントのレンダリング後にのみ存在することが保証されます。それはコンポーネントの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> メソッドの実行中か実行後になります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-202">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="d9b0b-203"><xref:Microsoft.AspNetCore.Components.ElementReference> は [`struct`](/csharp/language-reference/builtin-types/struct) であり、[コンポーネント パラメーター](xref:blazor/components/index#component-parameters)として渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-203">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="d9b0b-204">親コンポーネントが要素参照を他のコンポーネントで使用できるようにするために、親コンポーネントは次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-204">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="d9b0b-205">子コンポーネントがコールバックを登録できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-205">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="d9b0b-206">渡された要素参照を使用して、登録されたコールバックを<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> イベント中に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-206">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="d9b0b-207">間接的には、この方法により、子コンポーネントが親の要素参照とやりとりできるようになります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-207">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="d9b0b-208">次の Blazor WebAssembly の例でこのアプローチを示します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-208">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="d9b0b-209">`wwwroot/index.html` の `<head>` では、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-209">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="d9b0b-210">`wwwroot/index.html` の `<body>` では、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-210">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="d9b0b-211">`Pages/Index.razor` (親コンポーネント):</span><span class="sxs-lookup"><span data-stu-id="d9b0b-211">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="d9b0b-212">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="d9b0b-212">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="d9b0b-213">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-213">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="d9b0b-214">`Shared/SurveyPrompt.razor` (子コンポーネント):</span><span class="sxs-lookup"><span data-stu-id="d9b0b-214">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="d9b0b-215">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="d9b0b-215">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="d9b0b-216">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-216">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="d9b0b-217">JS 相互運用呼び出しの強化</span><span class="sxs-lookup"><span data-stu-id="d9b0b-217">Harden JS interop calls</span></span>

<span data-ttu-id="d9b0b-218">JS 相互運用は、ネットワーク エラーにより失敗する可能性があるため、信頼性の低いものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-218">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="d9b0b-219">既定では、Blazor Server アプリでは、サーバー上の JS 相互運用の呼び出しは 1 分後にタイムアウトします。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-219">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="d9b0b-220">アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-220">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="d9b0b-221">`Startup.ConfigureServices` でグローバルに、タイムアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-221">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="d9b0b-222">コンポーネント コードでの呼び出しごとに、1 回の呼び出しでタイムアウトを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-222">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="d9b0b-223">リソース枯渇の詳細については、「<xref:blazor/security/server/threat-mitigation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-223">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="d9b0b-224">循環オブジェクト参照の回避</span><span class="sxs-lookup"><span data-stu-id="d9b0b-224">Avoid circular object references</span></span>

<span data-ttu-id="d9b0b-225">循環参照を含むオブジェクトは、次のいずれに対しても、クライアントでシリアル化することはできません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-225">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="d9b0b-226">.NET メソッドの呼び出し。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-226">.NET method calls.</span></span>
* <span data-ttu-id="d9b0b-227">戻り値の型に循環参照がある場合の、C# からの JavaScript メソッドの呼び出し。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-227">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="d9b0b-228">詳細については、[「循環参照はサポートされていません、テイク 2」(dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-228">For more information, see [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="blazor-javascript-isolation-and-object-references"></a><span data-ttu-id="d9b0b-229">Blazor JavaScript の分離とオブジェクト参照</span><span class="sxs-lookup"><span data-stu-id="d9b0b-229">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="d9b0b-230">Blazor により、標準 [JavaScript モジュール](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)で JavaScript の分離が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-230">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="d9b0b-231">JavaScript の分離には、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="d9b0b-232">インポートされる JavaScript によって、グローバル名前空間が汚染されなくなります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="d9b0b-233">ライブラリおよびコンポーネントのコンシューマーは、関連する JavaScript をインポートする必要がありません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="d9b0b-234">たとえば、次の JavaScript モジュールにより、ブラウザー プロンプトを表示する JavaScript 関数がエクスポートされます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="d9b0b-235">前の JavaScript モジュールを静的 Web アセット (`wwwroot/exampleJsInterop.js`) として .NET ライブラリに追加し、<xref:Microsoft.JSInterop.IJSRuntime> サービスの <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> を呼び出して、そのモジュールを .NET コードにインポートします。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> on the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="d9b0b-236">サービスは、次の例では `js` (表示はなし) として挿入されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-236">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="d9b0b-237">前の例の `import` 識別子は、JavaScript モジュールをインポートするために特別に使用される特殊な識別子です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="d9b0b-238">安定した静的な Web アセット パスを使用してモジュールを指定します: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-238">Specify the module using its stable static web asset path: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="d9b0b-239">JavaScript ファイルへの正しい静的アセット パスを作成するためには、現在のディレクトリ (`./`) に対するパス セグメントが必要です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-239">The path segment for the current directory (`./`) is required in order to create the correct static asset path to the JavaScript file.</span></span> <span data-ttu-id="d9b0b-240">モジュールを動的にインポートするにはネットワーク要求が必要であるため、これは <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> を呼び出すことによってのみ、非同期的に実現できます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-240">Dynamically importing a module requires a network request, so it can only be achieved asynchronously by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="d9b0b-241">`{LIBRARY NAME}` プレースホルダーは、ライブラリの名前です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-241">The `{LIBRARY NAME}` placeholder is the library name.</span></span> <span data-ttu-id="d9b0b-242">`{PATH UNDER WWWROOT}` プレースホルダーは、`wwwroot` の下にあるスクリプトへのパスです。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-242">The `{PATH UNDER WWWROOT}` placeholder is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="d9b0b-243"><xref:Microsoft.JSInterop.IJSRuntime> により、モジュールが `IJSObjectReference` としてインポートされます。これは、.NET コードから JavaScript オブジェクトへの参照を表します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-243"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="d9b0b-244">モジュールからエクスポートされた JavaScript 関数を呼び出すには、`IJSObjectReference` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-244">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="d9b0b-245">`IJSInProcessObjectReference` は、関数を同期的に呼び出すことができる JavaScript オブジェクトへの参照を表します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-245">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="d9b0b-246">UI をレンダリングする JavaScript ライブラリの使用 (DOM 要素)</span><span class="sxs-lookup"><span data-stu-id="d9b0b-246">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="d9b0b-247">ブラウザー DOM 内に表示可能なユーザー インターフェイス要素を生成する JavaScript ライブラリを使用することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-247">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="d9b0b-248">Blazor の差分システムは、DOM 要素のツリーに対する制御に依存しており、外部コードによって DOM ツリーが変更されて、差分を適用するためのメカニズムが無効になるとエラーが発生するため、一見すると、これは困難に思えるかもしれません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-248">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="d9b0b-249">これは、Blazor に固有の制限ではありません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-249">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="d9b0b-250">差分ベースの UI フレームワークでは同じ課題が発生します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-250">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="d9b0b-251">幸い、外部で生成された UI を Blazor コンポーネントの UI に確実に埋め込むのは簡単です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-251">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="d9b0b-252">推奨される方法は、コンポーネントのコード (`.razor` ファイル) で空の要素を生成することです。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-252">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="d9b0b-253">Blazor の差分システムに関する限り、要素は常に空であるため、レンダラーによって要素は再帰されず、代わりにその内容はそのままの状態になります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-253">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="d9b0b-254">これにより、外部で管理されている任意の内容を要素に設定しても安全になります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-254">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="d9b0b-255">次の例はこの概念を示したものです。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-255">The following example demonstrates the concept.</span></span> <span data-ttu-id="d9b0b-256">`if` ステートメント内で、`firstRender` が `true` の場合は、`myElement` に対する何らかの処理を行います。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-256">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="d9b0b-257">たとえば、外部の JavaScript ライブラリを呼び出してそれを設定します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-257">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="d9b0b-258">このコンポーネント自体が削除されるまで、要素の内容が Blazor によって操作されることはありません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-258">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="d9b0b-259">コンポーネントが削除されると、コンポーネントの DOM サブツリー全体も削除されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-259">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="d9b0b-260">さらに詳細な例として、[オープンソースの Mapbox API](https://www.mapbox.com/) を使用して対話型マップをレンダリングする次のようなコンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-260">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="d9b0b-261">`wwwroot/mapComponent.js` に配置する必要のある、対応する JavaScript モジュールは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-261">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="d9b0b-262">前の例で、文字列 `{ACCESS TOKEN}` は、 https://account.mapbox.com から取得できる有効なアクセス トークンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-262">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="d9b0b-263">正しいスタイルを生成するには、ホストの HTML ページ (`index.html` または `_Host.cshtml`) に次のスタイル シート タグを追加します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-263">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="d9b0b-264">前の例で生成される対話型のマップ UI で、ユーザーは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-264">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="d9b0b-265">ドラッグしてスクロールまたはズームできます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-265">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="d9b0b-266">ボタンをクリックして、あらかじめ定義されている場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-266">Click buttons to jump to predefined locations.</span></span>

![Mapbox による東京の市街地図。英国のブリストルと日本の東京を選択するためのボタンがあります](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="d9b0b-268">理解しておくべき重要な点は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-268">The key points to understand are:</span></span>

 * <span data-ttu-id="d9b0b-269">`@ref="mapElement"` が含まれる `<div>` は、Blazor に関する限り空のままになります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-269">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="d9b0b-270">したがって、やがて `mapbox-gl.js` によって設定されたり、内容が変更されたりしても安全です。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-270">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="d9b0b-271">この手法は、UI をレンダリングする任意の JavaScript ライブラリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-271">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="d9b0b-272">ページの他の部分に手を伸ばして変更しようとしない限り、サードパーティの JavaScript SPA フレームワークのコンポーネントを Blazor コンポーネントの内部に埋め込むことさえできます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-272">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="d9b0b-273">Blazor によって空と見なされない要素を、外部の JavaScript コードで変更することは、安全では "*ありません*"。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-273">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="d9b0b-274">このアプローチを使用する場合は、Blazor によって DOM 要素が保持または破棄される方法に関する規則に留意してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-274">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="d9b0b-275">前の例で、既定では DOM 要素が可能な限り保持されるため、コンポーネントにより安全にボタン クリック イベントが処理され、既存のマップ インスタンスが更新されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-275">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="d9b0b-276">`@foreach` ループの内側からマップ要素のリストをレンダリングしていた場合は、`@key` を使用して、コンポーネントのインスタンスを確実に保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-276">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="d9b0b-277">そうしないと、リスト データを変更した場合、コンポーネントのインスタンスによって前のインスタンスの状態が望ましくない状態で保持される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-277">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="d9b0b-278">詳細については、[@key を使用した要素とコンポーネントの保持](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-278">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="d9b0b-279">また、前の例では、JavaScript のロジックと依存関係を ES6 モジュール内にカプセル化し、`import` 識別子を使用して動的に読み込むことができる方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-279">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="d9b0b-280">詳細については、[JavaScript の分離とオブジェクト参照](#blazor-javascript-isolation-and-object-references)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-280">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="d9b0b-281">JS 相互運用呼び出しのサイズ制限</span><span class="sxs-lookup"><span data-stu-id="d9b0b-281">Size limits on JS interop calls</span></span>

<span data-ttu-id="d9b0b-282">Blazor WebAssembly では、フレームワークによって JS 相互運用の入力と出力のサイズが制限されることはありません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-282">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="d9b0b-283">Blazor Server では、ハブ メソッドで許可される SignalR 受信メッセージの最大サイズによって、JS 相互運用呼び出しのサイズが制限されます。これは、<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> によって適用されます (既定値: 32 KB)。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-283">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="d9b0b-284">JS から .NET への SignalR メッセージが <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> より大きい場合は、エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-284">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="d9b0b-285">このフレームワークでは、ハブからクライアントへの SignalR メッセージのサイズが制限されることはありません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-285">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span> <span data-ttu-id="d9b0b-286">詳細については、「<xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-286">For more information, see <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="d9b0b-287">JS モジュール</span><span class="sxs-lookup"><span data-stu-id="d9b0b-287">JS modules</span></span>

<span data-ttu-id="d9b0b-288">JS の分離では、JS 相互運用は、ブラウザーで [EcmaScript モジュール (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript 仕様](https://tc39.es/ecma262/#sec-modules)) が既定でサポートされている場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-288">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="d9b0b-289">JS 相互運用のマーシャリングを解除する</span><span class="sxs-lookup"><span data-stu-id="d9b0b-289">Unmarshalled JS interop</span></span>

<span data-ttu-id="d9b0b-290">.NET オブジェクトが JS 相互運用のためにシリアル化され、次のいずれかに該当する場合、Blazor WebAssembly コンポーネントのパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-290">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="d9b0b-291">大量の .NET オブジェクトが短時間でシリアル化される。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-291">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="d9b0b-292">例:JS 相互運用の呼び出しは、マウス ホイールの回転など、入力デバイスの動きに基づいて行われます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-292">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="d9b0b-293">大きな .NET オブジェクトまたは多数の .NET オブジェクトを JS 相互運用のためにシリアル化する必要がある。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-293">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="d9b0b-294">例:JS 相互運用の呼び出しでは、数十個のファイルをシリアル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-294">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="d9b0b-295"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> は、.NET データをシリアル化するオーバーヘッドなしで関数を呼び出すことができる JavaScript オブジェクトへの参照を表します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-295"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="d9b0b-296">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-296">In the following example:</span></span>

* <span data-ttu-id="d9b0b-297">文字列と整数を含む [struct](/dotnet/csharp/language-reference/builtin-types/struct) は、シリアル化されずに JavaScript に渡されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-297">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="d9b0b-298">JavaScript 関数によってデータが処理され、ブール値または文字列が呼び出し元に返されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-298">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="d9b0b-299">JavaScript 文字列を .NET `string` オブジェクトに直接変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-299">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="d9b0b-300">`unmarshalledFunctionReturnString` 関数によって `BINDING.js_string_to_mono_string` が呼び出され、Javascript 文字列の変換が管理されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-300">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="d9b0b-301">次の例は、JavaScript に渡された [struct](/dotnet/csharp/language-reference/builtin-types/struct) によってコンポーネントのパフォーマンスは低下しないので、このシナリオの一般的な使用例ではありません。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-301">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="d9b0b-302">この例では、シリアル化されていない .NET データを渡すための概念を示すためだけに、小さなオブジェクトを使用しています。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-302">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="d9b0b-303">`wwwroot/index.html` の `<script>` ブロックまたは `wwwroot/index.html` から参照される外部 Javascript ファイルの内容:</span><span class="sxs-lookup"><span data-stu-id="d9b0b-303">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> <span data-ttu-id="d9b0b-304">`js_string_to_mono_string` 関数の名前、動作、および存在は、.NET の今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-304">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="d9b0b-305">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-305">For example:</span></span>
>
> * <span data-ttu-id="d9b0b-306">関数の名前が変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-306">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="d9b0b-307">フレームワークによる文字列の自動変換を優先して、関数自体が削除される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-307">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="d9b0b-308">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span><span class="sxs-lookup"><span data-stu-id="d9b0b-308">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

<span data-ttu-id="d9b0b-309">`IJSUnmarshalledObjectReference` インスタンスが C# コードで破棄されない場合は、JavaScript で破棄することができます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-309">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="d9b0b-310">次の `dispose` 関数を使用すると、JavaScript から呼び出されたときにオブジェクト参照が破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-310">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

<span data-ttu-id="d9b0b-311">配列型は、`js_typed_array_to_array` を使用して JavaScript オブジェクトから .NET オブジェクトに変換できますが、JavaScript 配列は型指定された配列である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-311">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="d9b0b-312">JavaScript の配列は、C# コードで .NET オブジェクト配列 (`object[]`) として読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-312">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="d9b0b-313">文字列配列などの他のデータ型は変換できますが、新しい Mono 配列オブジェクト (`mono_obj_array_new`) を作成し、その値 (`mono_obj_array_set`) を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-313">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="d9b0b-314">`js_typed_array_to_array`、`mono_obj_array_new`、`mono_obj_array_set` など、Blazor フレームワークによって提供される JavaScript 関数は、.NET の今後のリリースで、名前の変更、動作の変更、または削除の対象となる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9b0b-314">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9b0b-315">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d9b0b-315">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="d9b0b-316">InteropComponent.razor の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)</span><span class="sxs-lookup"><span data-stu-id="d9b0b-316">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)

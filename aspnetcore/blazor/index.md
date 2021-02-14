---
title: ASP.NET Core Blazor の概要
author: guardrex
description: ASP.NET Core アプリ内に .NET を使った対話型のクライアント側 Web UI を構築する方法である、ASP.NET Core Blazor について調べます。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: 560fead9868bab9888c0d6a69cf09f135bbf39cc
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279738"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="26569-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="26569-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="26569-104">*Blazor へようこそ。*</span><span class="sxs-lookup"><span data-stu-id="26569-104">*Welcome to Blazor!*</span></span>

<span data-ttu-id="26569-105">Blazor は、[.NET](/dotnet/standard/tour) を使って対話型のクライアント側 Web UI を構築するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="26569-105">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="26569-106">[JavaScript](https://www.javascript.com) の代わりに [C#](/dotnet/csharp/) を使って、優れた対話型 UI を作成します。</span><span class="sxs-lookup"><span data-stu-id="26569-106">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="26569-107">.NET で記述された、サーバー側とクライアント側のアプリのロジックを共有します。</span><span class="sxs-lookup"><span data-stu-id="26569-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="26569-108">モバイル ブラウザーを含めた広範なブラウザーのサポートのために、HTML および CSS として UI をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="26569-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="26569-109">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index) などの最新のホスティング プラットフォームと統合します。</span><span class="sxs-lookup"><span data-stu-id="26569-109">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="26569-110">クライアント側の Web 開発に .NET を使用すると、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="26569-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="26569-111">JavaScript ではなく C# でコードを記述します。</span><span class="sxs-lookup"><span data-stu-id="26569-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="26569-112">[.NET ライブラリ](/dotnet/standard/class-libraries)の既存の .NET エコシステムを活用します。</span><span class="sxs-lookup"><span data-stu-id="26569-112">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="26569-113">サーバーとクライアント全体でアプリ ロジックを共有します。</span><span class="sxs-lookup"><span data-stu-id="26569-113">Share app logic across server and client.</span></span>
* <span data-ttu-id="26569-114">.NET のパフォーマンス、信頼性、およびセキュリティから利点が得られます。</span><span class="sxs-lookup"><span data-stu-id="26569-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="26569-115">Windows、Linux、macOS 上の [Visual Studio](https://visualstudio.microsoft.com) を使って生産性を維持します。</span><span class="sxs-lookup"><span data-stu-id="26569-115">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="26569-116">多機能で使いやすい安定した言語、フレームワーク、およびツールの共通セットに基づいて構築します。</span><span class="sxs-lookup"><span data-stu-id="26569-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="26569-117">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="26569-117">Components</span></span>

<span data-ttu-id="26569-118">Blazor アプリは、"*コンポーネント*" に基づいています。</span><span class="sxs-lookup"><span data-stu-id="26569-118">Blazor apps are based on *components*.</span></span> <span data-ttu-id="26569-119">Blazor のコンポーネントとは、ページ、ダイアログ、データ エントリ フォームなどの UI の要素です。</span><span class="sxs-lookup"><span data-stu-id="26569-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="26569-120">コンポーネントは、[.NET アセンブリ](/dotnet/standard/assembly/)に組み込まれている次のような .NET C# クラスです。</span><span class="sxs-lookup"><span data-stu-id="26569-120">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="26569-121">柔軟な UI のレンダリング ロジックを定義する。</span><span class="sxs-lookup"><span data-stu-id="26569-121">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="26569-122">ユーザー イベントを処理する。</span><span class="sxs-lookup"><span data-stu-id="26569-122">Handle user events.</span></span>
* <span data-ttu-id="26569-123">入れ子にしたり再利用したりできる。</span><span class="sxs-lookup"><span data-stu-id="26569-123">Can be nested and reused.</span></span>
* <span data-ttu-id="26569-124">[Razor クラス ライブラリまたは ](xref:razor-pages/ui-class)[NuGet パッケージ](/nuget/what-is-nuget)として共有および配布できる。</span><span class="sxs-lookup"><span data-stu-id="26569-124">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="26569-125">通常、コンポーネント クラスは、`.razor` ファイル拡張子を持つ [Razor](xref:mvc/views/razor) マークアップ ページの形式で記述されます。</span><span class="sxs-lookup"><span data-stu-id="26569-125">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="26569-126">Blazor のコンポーネントは、正式には " *Razor コンポーネント*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="26569-126">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="26569-127">Razor とは、開発者の生産性のために設計された、C# コードに HTML マークアップを結合するための構文です。</span><span class="sxs-lookup"><span data-stu-id="26569-127">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="26569-128">Razor を使用すると、Visual Studio の [IntelliSense](/visualstudio/ide/using-intellisense) によるプログラミング サポートを利用して、同一ファイル内で HTML マークアップと C# を切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="26569-128">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="26569-129">また、Razor Pages と MVC でも Razor が使用されます。</span><span class="sxs-lookup"><span data-stu-id="26569-129">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="26569-130">要求/応答モデルを中心に構築される Razor Pages や MVC とは異なり、コンポーネントは特にクライアント側の UI ロジックとコンポジションに対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="26569-130">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="26569-131">Blazor では、UI コンポジションにとって自然な HTML タグを使用します。</span><span class="sxs-lookup"><span data-stu-id="26569-131">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="26569-132">次の Razor マークアップは、ダイアログを表示し、ユーザーがボタンを選択したときにイベントを処理するコンポーネント (`Dialog.razor`) を示しています。</span><span class="sxs-lookup"><span data-stu-id="26569-132">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="26569-133">前の例の `OnYes` は、ボタンの `onclick` イベントによってトリガーされる C# メソッドです。</span><span class="sxs-lookup"><span data-stu-id="26569-133">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="26569-134">ダイアログのテキスト (`ChildContent`) とタイトル (`Title`) は、UI にこのコンポーネントを使用する次のコンポーネントによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="26569-134">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="26569-135">`Dialog` コンポーネントは、HTML タグを使用して、別のコンポーネント内に入れ子にされます。</span><span class="sxs-lookup"><span data-stu-id="26569-135">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="26569-136">次の例では、`Index` コンポーネント (`Pages/Index.razor`) で前の `Dialog` コンポーネントが使われています。</span><span class="sxs-lookup"><span data-stu-id="26569-136">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="26569-137">タグの `Title` 属性は、タイトルの値を `Dialog` コンポーネントの `Title` プロパティに渡します。</span><span class="sxs-lookup"><span data-stu-id="26569-137">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="26569-138">`Dialog` コンポーネントのテキスト (`ChildContent`) は、`<Dialog>` 要素の内容によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="26569-138">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="26569-139">`Dialog` コンポーネントを `Index` に追加すると、[Visual Studio の IntelliSense](/visualstudio/ide/using-intellisense) の構文およびパラメーター補完により開発を迅速化できます。</span><span class="sxs-lookup"><span data-stu-id="26569-139">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="26569-140">ブラウザー内で `Index` コンポーネントにアクセスすると、ダイアログがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="26569-140">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="26569-141">ユーザーがボタンを選択すると、ブラウザーの開発者ツール コンソールに `OnYes` メソッドによって書き込まれたメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="26569-141">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Index コンポーネントの内部に入れ子になっているブラウザーでレンダリングされたダイアログ コンポーネント。](index/_static/dialog.png)

<span data-ttu-id="26569-145">コンポーネントはレンダリングされると、"*レンダリング ツリー*" と呼ばれる、ブラウザーの [ドキュメント オブジェクト モデル (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) のメモリ内表現になります。これは、柔軟かつ効率的な方法で UI を更新するために使われます。</span><span class="sxs-lookup"><span data-stu-id="26569-145">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="26569-146">Blazor WebAssembly は、.NET を使って対話型のクライアント側 Web アプリを構築するための[シングルページ アプリ (SPA) フレームワーク](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps)です。</span><span class="sxs-lookup"><span data-stu-id="26569-146">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="26569-147">Blazor WebAssembly は、プラグインを使用せずに、または他の言語へのコードの再コンパイルなしに、オープンな Web 標準を使用します。</span><span class="sxs-lookup"><span data-stu-id="26569-147">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="26569-148">Blazor WebAssembly はモバイル ブラウザーも含めて、すべての最新の Web ブラウザーで動作します。</span><span class="sxs-lookup"><span data-stu-id="26569-148">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="26569-149">[WebAssembly](https://webassembly.org) (略称 `wasm`) によって、Web ブラウザー内で .NET コードを実行することが可能になります。</span><span class="sxs-lookup"><span data-stu-id="26569-149">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="26569-150">WebAssembly は、ダウンロードを高速化し実行速度を最大限に高めるために最適化されたコンパクトなバイトコード形式です。</span><span class="sxs-lookup"><span data-stu-id="26569-150">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="26569-151">WebAssembly はオープンな Web 標準であり、プラグインを使わずに Web ブラウザー内でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="26569-151">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="26569-152">WebAssembly コードを使用すると、JavaScript を介してブラウザーの全機能にアクセスできます。これは、"*JavaScript の相互運用性*" と呼ばれますが、"*JavaScript 相互運用*" や "*JS 相互運用*" とよく省略されます。</span><span class="sxs-lookup"><span data-stu-id="26569-152">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="26569-153">ブラウザーの WebAssembly 経由で実行される .NET コードは、ブラウザーの JavaScript サンドボックス内で実行されます。その際、クライアント コンピューター上での悪意のある操作に対して、サンドボックスに備わった保護が適用されます。</span><span class="sxs-lookup"><span data-stu-id="26569-153">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![Blazor WebAssembly は WebAssembly を使用してブラウザーで .NET コードを実行します。](index/_static/blazor-webassembly.png)

<span data-ttu-id="26569-155">Blazor WebAssembly アプリをビルドしてブラウザーで実行する場合:</span><span class="sxs-lookup"><span data-stu-id="26569-155">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="26569-156">C# コード ファイルと Razor ファイルが .NET アセンブリにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="26569-156">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="26569-157">そのアセンブリと [.NET ランタイム](/dotnet/framework/get-started/overview)がブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="26569-157">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="26569-158">Blazor WebAssembly により .NET ランタイムがブートストラップされ、アプリのアセンブリを読み込むようにそのランタイムが構成されます。</span><span class="sxs-lookup"><span data-stu-id="26569-158">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="26569-159">Blazor WebAssembly ランタイムでは、JavaScript 相互運用を使って DOM 操作とブラウザー API の呼び出しが処理されます。</span><span class="sxs-lookup"><span data-stu-id="26569-159">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="26569-160">公開されているアプリのサイズである "*ペイロードのサイズ*" は、アプリの使用性に関する重要なパフォーマンス要因になります。</span><span class="sxs-lookup"><span data-stu-id="26569-160">The size of the published app, its *payload size*, is a critical performance factor for an app's usability.</span></span> <span data-ttu-id="26569-161">大規模なアプリは、ブラウザーへのダウンロードに比較的長い時間がかかり、ユーザー エクスペリエンスが損なわれます。</span><span class="sxs-lookup"><span data-stu-id="26569-161">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="26569-162">Blazor WebAssembly では、ダウンロード時間を短縮するためにペイロードのサイズが最適化されます。</span><span class="sxs-lookup"><span data-stu-id="26569-162">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="26569-163">アプリが[中間言語 (IL) トリマー](xref:blazor/host-and-deploy/configure-trimmer)によって公開される場合、アプリから未使用コードが除去されます。</span><span class="sxs-lookup"><span data-stu-id="26569-163">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="26569-164">HTTP 応答が圧縮されます。</span><span class="sxs-lookup"><span data-stu-id="26569-164">HTTP responses are compressed.</span></span>
* <span data-ttu-id="26569-165">.NET ランタイムとアセンブリがブラウザーにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="26569-165">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="26569-166">アプリが[中間言語 (IL) リンカー](xref:blazor/host-and-deploy/configure-linker)によって公開される場合、アプリから未使用コードが除去されます。</span><span class="sxs-lookup"><span data-stu-id="26569-166">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="26569-167">HTTP 応答が圧縮されます。</span><span class="sxs-lookup"><span data-stu-id="26569-167">HTTP responses are compressed.</span></span>
* <span data-ttu-id="26569-168">.NET ランタイムとアセンブリがブラウザーにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="26569-168">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="26569-169">Blazor では、UI の更新プログラムを適用する方法からコンポーネントのレンダリング ロジックが分離されます。</span><span class="sxs-lookup"><span data-stu-id="26569-169">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="26569-170">*Blazor Server* では、ASP.NET Core アプリでサーバー上の Razor コンポーネントをホストするためのサポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="26569-170">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="26569-171">UI の更新は [SignalR](xref:signalr/introduction) 接続を介して処理されます。</span><span class="sxs-lookup"><span data-stu-id="26569-171">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="26569-172">ランタイムはサーバー上にとどまり、次の処理を行います。</span><span class="sxs-lookup"><span data-stu-id="26569-172">The runtime stays on the server and handles:</span></span>

* <span data-ttu-id="26569-173">アプリの C# コードを実行しています。</span><span class="sxs-lookup"><span data-stu-id="26569-173">Executing the app's C# code.</span></span>
* <span data-ttu-id="26569-174">ブラウザーからサーバーへの UI イベントの送信。</span><span class="sxs-lookup"><span data-stu-id="26569-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="26569-175">サーバーによって返送される、レンダリングされたコンポーネントへの UI の更新の適用。</span><span class="sxs-lookup"><span data-stu-id="26569-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="26569-176">ブラウザーと通信するために Blazor Server で使われる接続は、JavaScript 相互運用の呼び出しを処理するためにも使われます。</span><span class="sxs-lookup"><span data-stu-id="26569-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor Server では、サーバー上で .NET コードが実行され、SignalR 接続を介してクライアント上のドキュメント オブジェクト モデルとのやりとりが行われます](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="26569-178">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="26569-178">JavaScript interop</span></span>

<span data-ttu-id="26569-179">サード パーティ製の JavaScript ライブラリや、ブラウザーの API へのアクセスを必要とするアプリのために、コンポーネントは JavaScript と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="26569-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="26569-180">コンポーネントでは、JavaScript で使用できるライブラリまたは API はいずれも使用することができます。</span><span class="sxs-lookup"><span data-stu-id="26569-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="26569-181">C# コードによる [JavaScript コードの呼び出し](xref:blazor/call-javascript-from-dotnet)、および JavaScript コードによる [C# コードの呼び出し](xref:blazor/call-dotnet-from-javascript)を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="26569-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="26569-182">コードの共有と .NET Standard</span><span class="sxs-lookup"><span data-stu-id="26569-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="26569-183">Blazor は [.NET Standard](/dotnet/standard/net-standard) を実装します。これにより、Blazor プロジェクトは .NET Standard の仕様に準拠するライブラリを参照できます。</span><span class="sxs-lookup"><span data-stu-id="26569-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="26569-184">.NET Standard は、.NET 実装全体で共通した .NET API の標準仕様です。</span><span class="sxs-lookup"><span data-stu-id="26569-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="26569-185">.NET Standard のクラス ライブラリは、Blazor、.NET Framework、.NET Core、Xamarin、Mono、Unity など、さまざまな .NET プラットフォーム全体で共有することができます。</span><span class="sxs-lookup"><span data-stu-id="26569-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="26569-186">Web ブラウザー内で使用できない API (たとえば、ファイル システムへのアクセス、ソケットを開く機能、スレッドなど) からは、<xref:System.PlatformNotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="26569-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26569-187">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="26569-187">Additional resources</span></span>

* [<span data-ttu-id="26569-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="26569-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="26569-189">C# のガイド</span><span class="sxs-lookup"><span data-stu-id="26569-189">C# Guide</span></span>](/dotnet/csharp/)
* [<span data-ttu-id="26569-190">ASP.NET Core の Razor 構文リファレンス</span><span class="sxs-lookup"><span data-stu-id="26569-190">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* [<span data-ttu-id="26569-191">HTML</span><span class="sxs-lookup"><span data-stu-id="26569-191">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="26569-192">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) コミュニティへのリンク</span><span class="sxs-lookup"><span data-stu-id="26569-192">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>

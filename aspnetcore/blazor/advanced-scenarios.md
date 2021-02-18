---
title: ASP.NET Core Blazor の高度なシナリオ
author: guardrex
description: Blazor の高度なシナリオについて説明します。これには、手動の RenderTreeBuilder ロジックをアプリに組み込む方法などが含まれます。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: ba2bf91f3318225383ec9d164c34be9124aa311b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280853"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="3a999-103">ASP.NET Core Blazor の高度なシナリオ</span><span class="sxs-lookup"><span data-stu-id="3a999-103">ASP.NET Core Blazor advanced scenarios</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="3a999-104">Blazor Server 回線ハンドラー</span><span class="sxs-lookup"><span data-stu-id="3a999-104">Blazor Server circuit handler</span></span>

<span data-ttu-id="3a999-105">Blazor Server を使用すると、コードで "*回線ハンドラー*" を定義できます。これにより、ユーザーの回線の状態の変更時にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="3a999-105">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="3a999-106">回線ハンドラーは、`CircuitHandler` から派生させ、そのクラスをアプリのサービス コンテナーに登録することで実装します。</span><span class="sxs-lookup"><span data-stu-id="3a999-106">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="3a999-107">次の回線ハンドラーの例では、開いている SignalR 接続を追跡します。</span><span class="sxs-lookup"><span data-stu-id="3a999-107">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="3a999-108">回線ハンドラーは DI を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-108">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="3a999-109">スコープを持つインスタンスは、回線のインスタンスごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-109">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="3a999-110">前の例の `TrackingCircuitHandler` を使用すると、すべての回線の状態を追跡する必要があるため、シングルトン サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-110">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="3a999-111">カスタム回線ハンドラーのメソッドでハンドルされない例外がスローされる場合は、その例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="3a999-111">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="3a999-112">ハンドラーのコードまたはメソッドで例外が許容されるようにするには、エラー処理とログを含む 1 つ以上の [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントでコードをラップします。</span><span class="sxs-lookup"><span data-stu-id="3a999-112">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="3a999-113">ユーザーが切断し、フレームワークで回線の状態がクリーンアップされていることが原因で回線が終了すると、フレームワークによって回線の DI スコープが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-113">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="3a999-114">スコープが破棄されると、<xref:System.IDisposable?displayProperty=fullName> を実装するサーキットスコープの DI サービスはすべて破棄されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-114">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="3a999-115">破棄中にいずれかの DI サービスでハンドルされない例外がスローされると、フレームワークによって例外がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-115">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="3a999-116">手動の RenderTreeBuilder ロジック</span><span class="sxs-lookup"><span data-stu-id="3a999-116">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="3a999-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> には、コンポーネントと要素を操作するためのメソッドが用意されています。これには、C# コードでコンポーネントを手動で作成することも含まれます。</span><span class="sxs-lookup"><span data-stu-id="3a999-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="3a999-118">コンポーネントの作成に <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> を使用することは、高度なシナリオです。</span><span class="sxs-lookup"><span data-stu-id="3a999-118">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="3a999-119">形式が正しくないコンポーネント (閉じられていないマークアップ タグなど) により、定義されていない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3a999-119">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="3a999-120">次の `PetDetails` コンポーネントを考えてみましょう。これは手動で別のコンポーネントに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="3a999-120">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="3a999-121">次の例では、`CreateComponent` メソッド内のループによって、3 つの `PetDetails` コンポーネントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-121">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="3a999-122">シーケンス番号のある <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> メソッドでは、シーケンス番号はソース コードの行番号です。</span><span class="sxs-lookup"><span data-stu-id="3a999-122">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="3a999-123">Blazor の差分アルゴリズムは、個別の呼び出しではなく、個別のコード行に対応するシーケンス番号に依存しています。</span><span class="sxs-lookup"><span data-stu-id="3a999-123">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="3a999-124"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> メソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコードします。</span><span class="sxs-lookup"><span data-stu-id="3a999-124">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="3a999-125">**計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。**</span><span class="sxs-lookup"><span data-stu-id="3a999-125">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="3a999-126">詳細については、「[シーケンス番号は実行順序ではなくコード行番号に関係する](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a999-126">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="3a999-127">`BuiltContent` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="3a999-127">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="3a999-128"><xref:Microsoft.AspNetCore.Components.RenderTree> の型により、レンダリング操作の "*結果*" の処理が許可されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-128">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="3a999-129">これらは、Blazor フレームワーク実装の内部的な詳細です。</span><span class="sxs-lookup"><span data-stu-id="3a999-129">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="3a999-130">これらの型は "*不安定*" と考えるべきで、今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3a999-130">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="3a999-131">シーケンス番号は実行順序ではなくコード行番号に関係する</span><span class="sxs-lookup"><span data-stu-id="3a999-131">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="3a999-132">Razor コンポーネント ファイル (`.razor`) は常にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="3a999-132">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="3a999-133">コンパイル ステップは、実行時にアプリのパフォーマンスを向上させる情報を挿入するために使用できるため、コンパイルの方がコードを解釈するよりも潜在的な利点があります。</span><span class="sxs-lookup"><span data-stu-id="3a999-133">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="3a999-134">これらの機能強化の主な例として、"*シーケンス番号*" があります。</span><span class="sxs-lookup"><span data-stu-id="3a999-134">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="3a999-135">シーケンス番号は、出力がコードの個別の順序付けられたどの行からのものかをランタイムに示します。</span><span class="sxs-lookup"><span data-stu-id="3a999-135">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="3a999-136">ランタイムでは、この情報を使用して、効率的なツリーの差分を線形時間で生成します。これは、一般的なツリーの差分アルゴリズムで通常にできるよりもかなり高速です。</span><span class="sxs-lookup"><span data-stu-id="3a999-136">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="3a999-137">次の Razor コンポーネント (`.razor`) ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="3a999-137">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="3a999-138">上記のコードは、次のようにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="3a999-138">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="3a999-139">コードを初めて実行するときに、`someFlag` が `true` の場合、ビルダーは以下を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="3a999-139">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="3a999-140">シーケンス</span><span class="sxs-lookup"><span data-stu-id="3a999-140">Sequence</span></span> | <span data-ttu-id="3a999-141">種類</span><span class="sxs-lookup"><span data-stu-id="3a999-141">Type</span></span>      | <span data-ttu-id="3a999-142">データ</span><span class="sxs-lookup"><span data-stu-id="3a999-142">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="3a999-143">0</span><span class="sxs-lookup"><span data-stu-id="3a999-143">0</span></span>        | <span data-ttu-id="3a999-144">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="3a999-144">Text node</span></span> | <span data-ttu-id="3a999-145">First</span><span class="sxs-lookup"><span data-stu-id="3a999-145">First</span></span>  |
| <span data-ttu-id="3a999-146">1</span><span class="sxs-lookup"><span data-stu-id="3a999-146">1</span></span>        | <span data-ttu-id="3a999-147">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="3a999-147">Text node</span></span> | <span data-ttu-id="3a999-148">Second</span><span class="sxs-lookup"><span data-stu-id="3a999-148">Second</span></span> |

<span data-ttu-id="3a999-149">`someFlag` が `false` になり、マークアップが再びレンダリングされるとします。</span><span class="sxs-lookup"><span data-stu-id="3a999-149">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="3a999-150">今度は、ビルダーは以下を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="3a999-150">This time, the builder receives:</span></span>

| <span data-ttu-id="3a999-151">シーケンス</span><span class="sxs-lookup"><span data-stu-id="3a999-151">Sequence</span></span> | <span data-ttu-id="3a999-152">種類</span><span class="sxs-lookup"><span data-stu-id="3a999-152">Type</span></span>       | <span data-ttu-id="3a999-153">データ</span><span class="sxs-lookup"><span data-stu-id="3a999-153">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="3a999-154">1</span><span class="sxs-lookup"><span data-stu-id="3a999-154">1</span></span>        | <span data-ttu-id="3a999-155">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="3a999-155">Text node</span></span>  | <span data-ttu-id="3a999-156">Second</span><span class="sxs-lookup"><span data-stu-id="3a999-156">Second</span></span> |

<span data-ttu-id="3a999-157">ランタイムで差分を実行すると、シーケンス `0` の項目が削除されたことが認識されるため、次のような単純な "*編集スクリプト*" が生成されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-157">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="3a999-158">Remove the first text node. (最初のテキスト ノードを削除します。)</span><span class="sxs-lookup"><span data-stu-id="3a999-158">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="3a999-159">プログラムによってシーケンス番号を生成する場合の問題</span><span class="sxs-lookup"><span data-stu-id="3a999-159">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="3a999-160">代わりに、次のレンダリング ツリー ビルダー ロジックを記述したとします。</span><span class="sxs-lookup"><span data-stu-id="3a999-160">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="3a999-161">最初の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3a999-161">Now, the first output is:</span></span>

| <span data-ttu-id="3a999-162">シーケンス</span><span class="sxs-lookup"><span data-stu-id="3a999-162">Sequence</span></span> | <span data-ttu-id="3a999-163">種類</span><span class="sxs-lookup"><span data-stu-id="3a999-163">Type</span></span>      | <span data-ttu-id="3a999-164">データ</span><span class="sxs-lookup"><span data-stu-id="3a999-164">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="3a999-165">0</span><span class="sxs-lookup"><span data-stu-id="3a999-165">0</span></span>        | <span data-ttu-id="3a999-166">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="3a999-166">Text node</span></span> | <span data-ttu-id="3a999-167">First</span><span class="sxs-lookup"><span data-stu-id="3a999-167">First</span></span>  |
| <span data-ttu-id="3a999-168">1</span><span class="sxs-lookup"><span data-stu-id="3a999-168">1</span></span>        | <span data-ttu-id="3a999-169">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="3a999-169">Text node</span></span> | <span data-ttu-id="3a999-170">Second</span><span class="sxs-lookup"><span data-stu-id="3a999-170">Second</span></span> |

<span data-ttu-id="3a999-171">この結果は前のケースと同じであるため、否定的な問題は存在しません。</span><span class="sxs-lookup"><span data-stu-id="3a999-171">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="3a999-172">`someFlag` は 2 番目のレンダリングでは `false` で、出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3a999-172">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="3a999-173">シーケンス</span><span class="sxs-lookup"><span data-stu-id="3a999-173">Sequence</span></span> | <span data-ttu-id="3a999-174">種類</span><span class="sxs-lookup"><span data-stu-id="3a999-174">Type</span></span>      | <span data-ttu-id="3a999-175">データ</span><span class="sxs-lookup"><span data-stu-id="3a999-175">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="3a999-176">0</span><span class="sxs-lookup"><span data-stu-id="3a999-176">0</span></span>        | <span data-ttu-id="3a999-177">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="3a999-177">Text node</span></span> | <span data-ttu-id="3a999-178">Second</span><span class="sxs-lookup"><span data-stu-id="3a999-178">Second</span></span> |

<span data-ttu-id="3a999-179">今度は、差分アルゴリズムによって、"*2 つ*" の変更が発生したことが認識され、アルゴリズムによって次の編集スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-179">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="3a999-180">最初のテキスト ノードの値を `Second` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3a999-180">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="3a999-181">2 番目のテキスト ノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="3a999-181">Remove the second text node.</span></span>

<span data-ttu-id="3a999-182">シーケンス番号を生成すると、`if/else` 分岐とループが元のコードのどこにあったかに関する有用な情報がすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="3a999-182">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="3a999-183">これにより、差分が以前の **2 倍の長さ** なります。</span><span class="sxs-lookup"><span data-stu-id="3a999-183">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="3a999-184">これは簡単な例です。</span><span class="sxs-lookup"><span data-stu-id="3a999-184">This is a trivial example.</span></span> <span data-ttu-id="3a999-185">複雑で深く入れ子になった構造体で、特にループがある、より現実的なケースでは、通常、パフォーマンス コストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="3a999-185">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="3a999-186">どのループ ブロックまたは分岐が挿入または削除されたかを即座に特定する代わりに、差分アルゴリズムではレンダリング ツリー内を深く再帰処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a999-186">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="3a999-187">これにより、古い構造と新しい構造体が相互にどのように関連しているかについて差分アルゴリズムが誤って通知されるため、通常、より長い編集スクリプトを作成することが必要になります。</span><span class="sxs-lookup"><span data-stu-id="3a999-187">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="3a999-188">ガイダンスと結論</span><span class="sxs-lookup"><span data-stu-id="3a999-188">Guidance and conclusions</span></span>

* <span data-ttu-id="3a999-189">シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="3a999-189">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="3a999-190">コンパイル時にキャプチャされない限り、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="3a999-190">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="3a999-191">手動で実装された <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> ロジックの長いブロックは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="3a999-191">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="3a999-192">`.razor` ファイルを優先し、コンパイラがシーケンス番号を処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="3a999-192">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="3a999-193">手動の <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> ロジックを回避できない場合は、長いブロックのコードを <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 呼び出しでラップされたより小さな部分に分割します。</span><span class="sxs-lookup"><span data-stu-id="3a999-193">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="3a999-194">各リージョンには独自のシーケンス番号の個別のスペースがあるため、各リージョン内でゼロ (または他の任意の数) から再開できます。</span><span class="sxs-lookup"><span data-stu-id="3a999-194">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="3a999-195">シーケンス番号がハードコードされている場合、差分アルゴリズムでは、シーケンス番号の値が増えることだけが要求されます。</span><span class="sxs-lookup"><span data-stu-id="3a999-195">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="3a999-196">初期値とギャップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="3a999-196">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="3a999-197">合理的な選択肢の 1 つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1 つずつまたは 100 ずつ (または任意の間隔で) 増やすことです。</span><span class="sxs-lookup"><span data-stu-id="3a999-197">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="3a999-198">Blazor ではシーケンス番号が使用されていますが、他のツリー差分 UI フレームワークでは使用されていません。</span><span class="sxs-lookup"><span data-stu-id="3a999-198">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="3a999-199">シーケンス番号を使用すると、差分がはるかに高速になります。また、Blazor には、`.razor` ファイルを作成する開発者に対して、シーケンス番号を自動的に処理するコンパイル ステップの利点があります。</span><span class="sxs-lookup"><span data-stu-id="3a999-199">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

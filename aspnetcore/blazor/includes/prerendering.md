<span data-ttu-id="13b4d-101">Blazor サーバー アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="13b4d-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="13b4d-102">コンポーネントは、プリレンダリング時に異なるレンダリングが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="13b4d-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="13b4d-103">ブラウザーとの接続が確立されるまで JavaScript の相互運用呼び出しを遅らせるために、[OnAfterRenderAsync コンポーネント ライフサイクル イベント](xref:blazor/components/lifecycle#after-component-render)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="13b4d-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="13b4d-104">このイベントは、アプリが完全にレンダリングされ、クライアント接続が確立された後にのみ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="13b4d-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="13b4d-105">前のコード例では、`wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) の `<head>` 要素内で、`setElementText` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="13b4d-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="13b4d-106">関数は <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> を指定して呼び出され、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="13b4d-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="13b4d-107">前の例では、デモンストレーションのみを目的として、ドキュメント オブジェクト モデル (DOM) を直接変更しています。</span><span class="sxs-lookup"><span data-stu-id="13b4d-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="13b4d-108">ほとんどのシナリオにおいて、JavaScript で DOM を直接変更することはお勧めできません。JavaScript が Blazor の変更追跡に影響する可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="13b4d-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="13b4d-109">次のコンポーネントは、プリレンダリングと互換性のある方法で、コンポーネントの初期化ロジックの一部として JavaScript の相互運用を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="13b4d-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="13b4d-110">コンポーネントには、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 内からレンダリングの更新をトリガーできることが示されています。</span><span class="sxs-lookup"><span data-stu-id="13b4d-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="13b4d-111">このシナリオでは、開発者が無限ループを作成しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13b4d-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="13b4d-112"><xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> が呼び出されるとき、`ElementRef` は、以前のライフサイクル メソッドではなく <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> でのみ使用されます。コンポーネントがレンダリングされるまで JavaScript 要素が存在しないためです。</span><span class="sxs-lookup"><span data-stu-id="13b4d-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="13b4d-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) は、JavaScript の相互運用呼び出しから取得された新しい状態でコンポーネントを再度レンダリングするために呼び出されます (詳細については、「<xref:blazor/components/rendering>」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="13b4d-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call (for more information, see <xref:blazor/components/rendering>).</span></span> <span data-ttu-id="13b4d-114">`StateHasChanged` は `infoFromJs`が `null` である場合にのみ呼び出されるため、このコードで無限ループが作成されることはありません。</span><span class="sxs-lookup"><span data-stu-id="13b4d-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="13b4d-115">前のコード例では、`wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) の `<head>` 要素内で、`setElementText` JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="13b4d-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="13b4d-116">関数は <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> を指定して呼び出され、次の値を返します。</span><span class="sxs-lookup"><span data-stu-id="13b4d-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="13b4d-117">前の例では、デモンストレーションのみを目的として、ドキュメント オブジェクト モデル (DOM) を直接変更しています。</span><span class="sxs-lookup"><span data-stu-id="13b4d-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="13b4d-118">ほとんどのシナリオにおいて、JavaScript で DOM を直接変更することはお勧めできません。JavaScript が Blazor の変更追跡に影響する可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="13b4d-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

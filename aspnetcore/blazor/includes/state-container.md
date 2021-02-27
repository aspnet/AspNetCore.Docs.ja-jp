---
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
ms.openlocfilehash: 76dbf3cae1c264fa474101bc4398da28f45a1c10
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100254390"
---
<span data-ttu-id="165bc-101">入れ子になったコンポーネントは通常、「ASP.NET Core Blazor データ バインディング<xref:blazor/components/data-binding>」で説明されているように、"*チェーン バインド*" を使用してデータをバインドします。</span><span class="sxs-lookup"><span data-stu-id="165bc-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="165bc-102">入れ子になったコンポーネントと入れ子になっていないコンポーネントは、登録済みのメモリ内状態コンテナーを使用してデータへのアクセスを共有できます。</span><span class="sxs-lookup"><span data-stu-id="165bc-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="165bc-103">カスタムの状態コンテナー クラスでは、割り当て可能な <xref:System.Action> を使用して、状態変更のアプリのさまざまな部分でコンポーネントに通知できます。</span><span class="sxs-lookup"><span data-stu-id="165bc-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="165bc-104">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="165bc-104">In the following example:</span></span>

* <span data-ttu-id="165bc-105">コンポーネントのペアでは、状態コンテナーを使用してプロパティを追跡します。</span><span class="sxs-lookup"><span data-stu-id="165bc-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="165bc-106">この例のコンポーネントは入れ子になっていますが、この方法を使用する際に入れ子は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="165bc-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="165bc-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="165bc-107">`StateContainer.cs`:</span></span>

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

<span data-ttu-id="165bc-108">`Program.Main` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="165bc-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="165bc-109">`Startup.ConfigureServices` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="165bc-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="165bc-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="165bc-110">`Pages/Component1.razor`:</span></span>

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1: {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="165bc-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="165bc-111">`Shared/Component2.razor`:</span></span>

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2: {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="165bc-112">前のコンポーネントによって <xref:System.IDisposable> が実装され、`Dispose` メソッドで `OnChange` デリゲートがサブスクライブ解除されます。このメソッドは、コンポーネントが破棄されるときにフレームワークによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="165bc-112">The preceding components implement <xref:System.IDisposable>, and the `OnChange` delegates are unsubscribed in the `Dispose` methods, which are called by the framework when the components are disposed.</span></span> <span data-ttu-id="165bc-113">詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="165bc-113">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

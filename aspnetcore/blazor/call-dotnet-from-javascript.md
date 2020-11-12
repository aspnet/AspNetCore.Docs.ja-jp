---
title: 'ASP.NET Core :::no-loc(Blazor)::: で JavaScript 関数から .NET メソッドを呼び出す'
author: guardrex
description: ':::no-loc(Blazor)::: アプリで JavaScript 関数から .NET メソッドを呼び出す方法について学習します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 1de4996b18642b7a17c696a51a0d7f909179d5f1
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507786"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="ea4ab-103">ASP.NET Core :::no-loc(Blazor)::: で JavaScript 関数から .NET メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="ea4ab-103">Call .NET methods from JavaScript functions in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="ea4ab-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Shashikant Rudrawadi](http://wisne.co)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ea4ab-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ea4ab-105">:::no-loc(Blazor)::: アプリでは、.NET メソッドから JavaScript 関数を呼び出すことも、JavaScript 関数から .NET メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="ea4ab-106">これらのシナリオは、" *JavaScript 相互運用* " (" *JS 相互運用* ") と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="ea4ab-107">この記事では、JavaScript から .NET メソッドを呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="ea4ab-108">.NET から JavaScript 関数を呼び出す方法については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="ea4ab-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="ea4ab-110">静的 .NET メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="ea4ab-110">Static .NET method call</span></span>

<span data-ttu-id="ea4ab-111">JavaScript から静的 .NET メソッドを呼び出すには、`DotNet.invokeMethod` 関数または `DotNet.invokeMethodAsync` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="ea4ab-112">呼び出す静的メソッドの識別子、関数を含むアセンブリの名前、任意の引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="ea4ab-113">:::no-loc(Blazor Server)::: のシナリオをサポートするには、非同期バージョンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-113">The asynchronous version is preferred to support :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="ea4ab-114">.NET メソッドはパブリックかつ静的であり、[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 属性を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="ea4ab-115">オープン ジェネリック メソッドを呼び出すことは、現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="ea4ab-116">サンプル アプリには、`int` 配列を返す C# メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="ea4ab-117">[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 属性がメソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="ea4ab-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-118">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="ea4ab-119">クライアントに提供される JavaScript は、C# .NET メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="ea4ab-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="ea4ab-121">**`Trigger .NET static method ReturnArrayAsync`** ボタンが選択されている場合は、ブラウザーの Web 開発者ツールでコンソール出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="ea4ab-122">コンソール出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="ea4ab-123">4 番目の配列値は、`ReturnArrayAsync` によって返される配列 (`data.push(4);`) にプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="ea4ab-124">既定では、メソッド識別子はメソッド名ですが、[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 属性コンストラクターを使用して別の識別子を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="ea4ab-125">クライアント側の JavaScript ファイル</span><span class="sxs-lookup"><span data-stu-id="ea4ab-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="ea4ab-126">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `:::no-loc(Blazor):::Sample`)。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="ea4ab-127">インスタンス メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="ea4ab-127">Instance method call</span></span>

<span data-ttu-id="ea4ab-128">JavaScript から .NET インスタンス メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="ea4ab-129">JavaScript から .NET インスタンス メソッドを呼び出すには</span><span class="sxs-lookup"><span data-stu-id="ea4ab-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="ea4ab-130">参照渡しで .NET インスタンスを JavaScript に渡します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="ea4ab-131">静的呼び出しを <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> にします。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="ea4ab-132">インスタンスを <xref:Microsoft.JSInterop.DotNetObjectReference> インスタンスにラップし、<xref:Microsoft.JSInterop.DotNetObjectReference> インスタンスで <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="ea4ab-133"><xref:Microsoft.JSInterop.DotNetObjectReference> オブジェクトを破棄します (このセクションの後半で例を示します)。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="ea4ab-134">`invokeMethod` 関数または `invokeMethodAsync` 関数を使用して、インスタンスで .NET インスタンス メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="ea4ab-135">.NET インスタンスは、JavaScript から他の .NET メソッドを呼び出すときに引数として渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="ea4ab-136">サンプル アプリでは、メッセージがクライアント側のコンソールにログ出力されます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="ea4ab-137">サンプル アプリで示される以下の例については、ブラウザーの開発者ツールでブラウザーのコンソール出力を確認してください。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="ea4ab-138">**`Trigger .NET instance method HelloHelper.SayHello`** ボタンを選択すると、`ExampleJsInterop.CallHelloHelperSayHello` が呼び出され、メソッドに名前 `:::no-loc(Blazor):::` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `:::no-loc(Blazor):::`, to the method.</span></span>

<span data-ttu-id="ea4ab-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello(":::no-loc(Blazor):::");
    }
}
```

<span data-ttu-id="ea4ab-140">`CallHelloHelperSayHello` では、`HelloHelper` の新しいインスタンスを使用して JavaScript 関数 `sayHello` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="ea4ab-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="ea4ab-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="ea4ab-143">名前は `HelloHelper` のコンストラクターに渡されます。これにより、`HelloHelper.Name` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="ea4ab-144">JavaScript 関数 `sayHello` が実行されると、`HelloHelper.SayHello` によって `Hello, {Name}!` メッセージが返されます。これは、JavaScript 関数によってコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="ea4ab-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="ea4ab-146">ブラウザーの Web 開発者ツールでのコンソール出力</span><span class="sxs-lookup"><span data-stu-id="ea4ab-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, :::no-loc(Blazor):::!
```

<span data-ttu-id="ea4ab-147">メモリ リークを回避し、<xref:Microsoft.JSInterop.DotNetObjectReference> を作成するコンポーネントでガベージ コレクションを許可するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="ea4ab-148"><xref:Microsoft.JSInterop.DotNetObjectReference> インスタンスを作成したクラスのオブジェクトを破棄します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="ea4ab-149">`ExampleJsInterop` クラスに示されている上記のパターンは、コンポーネントに実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(":::no-loc(Blazor):::"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="ea4ab-150">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `:::no-loc(Blazor):::Sample`)。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="ea4ab-151">コンポーネントまたはクラスによって <xref:Microsoft.JSInterop.DotNetObjectReference> が破棄されない場合は、`.dispose()` を呼び出すことによって、クライアント上のオブジェクトを破棄します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="ea4ab-152">コンポーネント インスタンス メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="ea4ab-152">Component instance method call</span></span>

<span data-ttu-id="ea4ab-153">コンポーネントの .NET メソッドを呼び出すには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="ea4ab-154">コンポーネントに対して静的メソッド呼び出しを行うには、`invokeMethod` または `invokeMethodAsync` 関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="ea4ab-155">コンポーネントの静的メソッドにより、そのインスタンス メソッドへの呼び出しが、呼び出された <xref:System.Action> としてラップされます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="ea4ab-156">複数のユーザーが同じコンポーネントを同時に使用している可能性がある :::no-loc(Blazor Server)::: アプリの場合、ヘルパー クラスを使用してインスタンス メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-156">For :::no-loc(Blazor Server)::: apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="ea4ab-157">詳細については、「[コンポーネント インスタンス メソッド ヘルパー クラス](#component-instance-method-helper-class)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="ea4ab-158">クライアント側の JavaScript:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="ea4ab-159">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `:::no-loc(Blazor):::Sample`)。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="ea4ab-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-160">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="ea4ab-161">インスタンス メソッドに引数を渡すには:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="ea4ab-162">JS メソッドの呼び出しにパラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="ea4ab-163">次の例では、名前がメソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="ea4ab-164">必要に応じて、追加のパラメーターを一覧に追加できます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="ea4ab-165">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `:::no-loc(Blazor):::Sample`)。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="ea4ab-166">パラメーターの <xref:System.Action> に適切な型を指定します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="ea4ab-167">C# メソッドにパラメーター一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="ea4ab-168"><xref:System.Action> (`UpdateMessage`) をパラメーター (`action.Invoke(name)`) を使用して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="ea4ab-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-169">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="ea4ab-170">**[Call JS Method]\(JS メソッドの呼び出し\)** ボタンを選択したときに `message` を出力します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="ea4ab-171">コンポーネント インスタンス メソッド ヘルパー クラス</span><span class="sxs-lookup"><span data-stu-id="ea4ab-171">Component instance method helper class</span></span>

<span data-ttu-id="ea4ab-172">ヘルパー クラスは、<xref:System.Action> としてインスタンス メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="ea4ab-173">ヘルパー クラスは、次の場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="ea4ab-174">同じ種類の複数のコンポーネントが同じページにレンダリングされる。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="ea4ab-175">:::no-loc(Blazor Server)::: アプリが使用され、複数のユーザーがコンポーネントを同時に使用している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-175">A :::no-loc(Blazor Server)::: app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="ea4ab-176">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-176">In the following example:</span></span>

* <span data-ttu-id="ea4ab-177">`JSInteropExample` コンポーネントには、複数の `ListItem` コンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="ea4ab-178">各 `ListItem` コンポーネントは、メッセージとボタンで構成されます。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="ea4ab-179">`ListItem` コンポーネント ボタンが選択されると、その `ListItem` の `UpdateMessage` メソッドによってリスト項目のテキストが変更され、ボタンが非表示になります。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="ea4ab-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-180">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="ea4ab-181">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `:::no-loc(Blazor):::Sample`)。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="ea4ab-182">クライアント側の JavaScript:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="ea4ab-183">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `:::no-loc(Blazor):::Sample`)。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="ea4ab-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-184">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="ea4ab-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea4ab-185">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="ea4ab-186">循環オブジェクト参照の回避</span><span class="sxs-lookup"><span data-stu-id="ea4ab-186">Avoid circular object references</span></span>

<span data-ttu-id="ea4ab-187">循環参照を含むオブジェクトは、次のいずれに対しても、クライアントでシリアル化することはできません。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="ea4ab-188">.NET メソッドの呼び出し。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-188">.NET method calls.</span></span>
* <span data-ttu-id="ea4ab-189">戻り値の型に循環参照がある場合の、C# からの JavaScript メソッドの呼び出し。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="ea4ab-190">詳細については、次のイシューを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea4ab-190">For more information, see the following issues:</span></span>

* <span data-ttu-id="ea4ab-191">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525) (循環参照はサポートされていません、テイク 2 (dotnet/aspnetcore #20525))</span><span class="sxs-lookup"><span data-stu-id="ea4ab-191">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)</span></span>
* <span data-ttu-id="ea4ab-192">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820) (提案: シリアル化するときに循環参照を処理するメカニズムを追加する (dotnet/runtime #30820))</span><span class="sxs-lookup"><span data-stu-id="ea4ab-192">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea4ab-193">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ea4ab-193">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="ea4ab-194">`InteropComponent.razor` の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)</span><span class="sxs-lookup"><span data-stu-id="ea4ab-194">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)

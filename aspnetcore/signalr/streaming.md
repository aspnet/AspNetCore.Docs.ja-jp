---
title: 'ASP.NET Core でストリーミングを使用する :::no-loc(SignalR):::'
author: bradygaster
description: クライアントとサーバーの間でデータをストリーミングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
no-loc:
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
uid: signalr/streaming
ms.openlocfilehash: 732198cf07392bda008c9cc1c9768df2500852c0
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690641"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="b39c5-103">ASP.NET Core でストリーミングを使用する :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="b39c5-103">Use streaming in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="b39c5-104">[Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="b39c5-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b39c5-105">ASP.NET Core :::no-loc(SignalR)::: は、クライアントからサーバー、およびサーバーからクライアントへのストリーミングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="b39c5-105">ASP.NET Core :::no-loc(SignalR)::: supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="b39c5-106">これは、時間の経過と共にデータのフラグメントが到着するシナリオに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="b39c5-107">ストリーミング時には、すべてのデータが使用可能になるのを待機するのではなく、使用可能になるとすぐに各フラグメントがクライアントまたはサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b39c5-108">ASP.NET Core :::no-loc(SignalR)::: は、サーバーメソッドのストリーミング戻り値をサポートします。</span><span class="sxs-lookup"><span data-stu-id="b39c5-108">ASP.NET Core :::no-loc(SignalR)::: supports streaming return values of server methods.</span></span> <span data-ttu-id="b39c5-109">これは、時間の経過と共にデータのフラグメントが到着するシナリオに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="b39c5-110">戻り値がクライアントにストリーミングされると、すべてのデータが使用可能になるまで待機するのではなく、使用可能になるとすぐに各フラグメントがクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="b39c5-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b39c5-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="b39c5-112">ストリーミング用のハブを設定する</span><span class="sxs-lookup"><span data-stu-id="b39c5-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b39c5-113">ハブメソッドは、、、 <xref:System.Collections.Generic.IAsyncEnumerable`1> 、またはを返すと、自動的にストリーミングハブメソッドになり <xref:System.Threading.Channels.ChannelReader%601> `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b39c5-114">ハブメソッドは、またはを返すと、自動的にストリーミングハブメソッドになり <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="b39c5-115">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="b39c5-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b39c5-116">ストリーミングハブのメソッドは `IAsyncEnumerable<T>` 、に加えてを返すことができ `ChannelReader<T>` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="b39c5-117">を返す最も簡単 `IAsyncEnumerable<T>` な方法は、次の例に示すように、ハブメソッドを非同期反復子メソッドにすることです。</span><span class="sxs-lookup"><span data-stu-id="b39c5-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="b39c5-118">ハブの非同期反復子メソッドは、 `CancellationToken` クライアントがストリームからアンサブスクライブしたときにトリガーされるパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="b39c5-119">非同期反復子メソッドは、チャネルに共通する問題を回避します。たとえば、を完了しないうちに十分な初期状態を返さない場合 `ChannelReader` や、メソッドを終了する場合など <xref:System.Threading.Channels.ChannelWriter`1> です。</span><span class="sxs-lookup"><span data-stu-id="b39c5-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="b39c5-120">次のサンプルは、チャネルを使用してクライアントにデータをストリーミングする方法の基本を示しています。</span><span class="sxs-lookup"><span data-stu-id="b39c5-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="b39c5-121">オブジェクトがに書き込まれるたびに、 <xref:System.Threading.Channels.ChannelWriter%601> オブジェクトは直ちにクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="b39c5-122">最後に、は、 `ChannelWriter` ストリームが閉じられていることをクライアントに通知するために完了します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="b39c5-123">`ChannelWriter<T>`バックグラウンドスレッドでに書き込み、できるだけ早くを返し `ChannelReader` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="b39c5-124">が返されるまで、他のハブ呼び出しはブロックされ `ChannelReader` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="b39c5-125">[ `try ... catch` ステートメント](/dotnet/csharp/language-reference/keywords/try-catch)でロジックをラップします。</span><span class="sxs-lookup"><span data-stu-id="b39c5-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="b39c5-126">`Channel` [ `finally` ブロック](/dotnet/csharp/language-reference/keywords/try-catch-finally)内のを完了します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="b39c5-127">エラーをフローさせる場合は、ブロック内でキャプチャ `catch` し、ブロックに書き込み `finally` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b39c5-128">サーバーからクライアントへのストリーミングハブメソッドは、 `CancellationToken` クライアントがストリームからアンサブスクライブしたときにトリガーされるパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="b39c5-129">ストリームの終了前にクライアントが切断された場合は、このトークンを使用してサーバー操作を停止し、リソースを解放します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="b39c5-130">クライアントとサーバー間のストリーミング</span><span class="sxs-lookup"><span data-stu-id="b39c5-130">Client-to-server streaming</span></span>

<span data-ttu-id="b39c5-131">ハブメソッドは、型または型の1つ以上のオブジェクトを受け入れると、クライアントとサーバー間のストリーミングハブメソッドに自動的になり <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601> ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="b39c5-132">次のサンプルは、クライアントから送信されたストリーミングデータの読み取りの基本を示しています。</span><span class="sxs-lookup"><span data-stu-id="b39c5-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="b39c5-133">クライアントがに書き込むたびに、 <xref:System.Threading.Channels.ChannelWriter%601> データは `ChannelReader` ハブメソッドの読み取り元のサーバー上のに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="b39c5-134"><xref:System.Collections.Generic.IAsyncEnumerable%601>メソッドのバージョンは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b39c5-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="b39c5-135">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="b39c5-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="b39c5-136">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="b39c5-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b39c5-137">`StreamAsync` `StreamAsChannelAsync` のメソッドとメソッド `HubConnection` は、サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="b39c5-138">ハブメソッドで定義されているハブメソッド名と引数を `StreamAsync` またはに渡し `StreamAsChannelAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="b39c5-139">およびのジェネリックパラメーターは、 `StreamAsync<T>` `StreamAsChannelAsync<T>` ストリーミングメソッドによって返されるオブジェクトの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="b39c5-140">または型の `IAsyncEnumerable<T>` オブジェクト `ChannelReader<T>` がストリーム呼び出しから返され、クライアントのストリームを表します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="b39c5-141">`StreamAsync`を返す例を `IAsyncEnumerable<int>` 次に示します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="b39c5-142">`StreamAsChannelAsync`を返す対応する例を `ChannelReader<int>` 次に示します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b39c5-143">`StreamAsChannelAsync`のメソッドは、 `HubConnection` サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="b39c5-144">ハブメソッドで定義されているハブメソッド名と引数をに渡し `StreamAsChannelAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="b39c5-145">のジェネリックパラメーターは、 `StreamAsChannelAsync<T>` ストリーミングメソッドによって返されるオブジェクトの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="b39c5-146">は `ChannelReader<T>` ストリーム呼び出しから返され、クライアントのストリームを表します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b39c5-147">`StreamAsChannelAsync`のメソッドは、 `HubConnection` サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="b39c5-148">ハブメソッドで定義されているハブメソッド名と引数をに渡し `StreamAsChannelAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="b39c5-149">のジェネリックパラメーターは、 `StreamAsChannelAsync<T>` ストリーミングメソッドによって返されるオブジェクトの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="b39c5-150">は `ChannelReader<T>` ストリーム呼び出しから返され、クライアントのストリームを表します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="b39c5-151">クライアントとサーバー間のストリーミング</span><span class="sxs-lookup"><span data-stu-id="b39c5-151">Client-to-server streaming</span></span>

<span data-ttu-id="b39c5-152">.NET クライアントからクライアントとサーバー間のストリーミングハブメソッドを呼び出すには、2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="b39c5-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="b39c5-153">`IAsyncEnumerable<T>` `ChannelReader` `SendAsync` `InvokeAsync` `StreamAsChannelAsync` 呼び出されたハブメソッドに応じて、またはを引数として、、またはに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="b39c5-154">オブジェクトまたはオブジェクトにデータが書き込まれるたびに `IAsyncEnumerable` `ChannelWriter` 、サーバーのハブメソッドは、クライアントからのデータを使用して新しい項目を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b39c5-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="b39c5-155">オブジェクトを使用している場合 `IAsyncEnumerable` 、ストリームは、ストリーム項目を返すメソッドが終了した後に終了します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="b39c5-156">または、を使用している場合は、 `ChannelWriter` 次のようにチャネルを完成させ `channel.Writer.Complete()` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="b39c5-157">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="b39c5-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="b39c5-158">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="b39c5-158">Server-to-client streaming</span></span>

<span data-ttu-id="b39c5-159">JavaScript クライアントは、を使用してハブでサーバーからクライアントへのストリーミングメソッドを呼び出し `connection.stream` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="b39c5-160">メソッドは、 `stream` 次の2つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b39c5-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="b39c5-161">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="b39c5-161">The name of the hub method.</span></span> <span data-ttu-id="b39c5-162">次の例では、ハブメソッド名は `Counter` です。</span><span class="sxs-lookup"><span data-stu-id="b39c5-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="b39c5-163">ハブメソッドで定義されている引数。</span><span class="sxs-lookup"><span data-stu-id="b39c5-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="b39c5-164">次の例では、引数は、受信するストリーム項目数とストリーム項目間の遅延をカウントします。</span><span class="sxs-lookup"><span data-stu-id="b39c5-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="b39c5-165">`connection.stream``IStreamResult`メソッドを含むを返し `subscribe` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="b39c5-166">をに渡し、、、およびの各 `IStreamSubscriber` `subscribe` コールバックを設定して、 `next` `error` `complete` 呼び出しから通知を受信し `stream` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="b39c5-167">クライアントからストリームを終了するには、 `dispose` メソッドから返されたに対してメソッドを呼び出し `ISubscription` `subscribe` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="b39c5-168">このメソッドを呼び出すと、 `CancellationToken` ハブメソッドのパラメーターがキャンセルされます (指定した場合)。</span><span class="sxs-lookup"><span data-stu-id="b39c5-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="b39c5-169">クライアントからストリームを終了するには、 `dispose` メソッドから返されたに対してメソッドを呼び出し `ISubscription` `subscribe` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="b39c5-170">クライアントとサーバー間のストリーミング</span><span class="sxs-lookup"><span data-stu-id="b39c5-170">Client-to-server streaming</span></span>

<span data-ttu-id="b39c5-171">JavaScript クライアントは、 `Subject` `send` `invoke` `stream` 呼び出されたハブメソッドに応じて、を引数として、、またはに渡すことによって、ハブでクライアントからサーバーへのストリーミングメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="b39c5-172">は、の `Subject` ように見えるクラスです `Subject` 。</span><span class="sxs-lookup"><span data-stu-id="b39c5-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="b39c5-173">たとえば、RxJS では、そのライブラリの [サブジェクト](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="b39c5-174">項目を使用してを呼び出す `subject.next(item)` と、項目がストリームに書き込まれ、ハブメソッドはサーバー上の項目を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b39c5-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="b39c5-175">ストリームを終了するには、を呼び出し `subject.complete()` ます。</span><span class="sxs-lookup"><span data-stu-id="b39c5-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="b39c5-176">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="b39c5-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="b39c5-177">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="b39c5-177">Server-to-client streaming</span></span>

<span data-ttu-id="b39c5-178">Java クライアントは、メソッドを使用して :::no-loc(SignalR)::: `stream` ストリーミングメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-178">The :::no-loc(SignalR)::: Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="b39c5-179">`stream` 3つ以上の引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b39c5-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="b39c5-180">ストリーム項目の予期される型。</span><span class="sxs-lookup"><span data-stu-id="b39c5-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="b39c5-181">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="b39c5-181">The name of the hub method.</span></span>
* <span data-ttu-id="b39c5-182">ハブメソッドで定義されている引数。</span><span class="sxs-lookup"><span data-stu-id="b39c5-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="b39c5-183">`stream`のメソッドは、 `HubConnection` ストリーム項目の型の観測可能なを返します。</span><span class="sxs-lookup"><span data-stu-id="b39c5-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="b39c5-184">観測可能な型の `subscribe` メソッドは `onNext` 、、 `onError` および `onCompleted` ハンドラーが定義されている場所です。</span><span class="sxs-lookup"><span data-stu-id="b39c5-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b39c5-185">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b39c5-185">Additional resources</span></span>

* [<span data-ttu-id="b39c5-186">ハブ</span><span class="sxs-lookup"><span data-stu-id="b39c5-186">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b39c5-187">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="b39c5-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b39c5-188">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="b39c5-188">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="b39c5-189">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="b39c5-189">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

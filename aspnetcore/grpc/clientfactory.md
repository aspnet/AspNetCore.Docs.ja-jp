---
title: .NET Core での gRPC クライアント ファクトリの統合
author: jamesnk
description: クライアント ファクトリを使用して gRPC クライアントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060951"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="49661-103">.NET Core での gRPC クライアント ファクトリの統合</span><span class="sxs-lookup"><span data-stu-id="49661-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="49661-104">gRPC と `HttpClientFactory` の統合により、gRPC クライアントを一元的に作成する方法が提供されています。</span><span class="sxs-lookup"><span data-stu-id="49661-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="49661-105">これは、[スタンドアロンの gRPC クライアント インスタンスを構成する](xref:grpc/client)ための代替手段として使用できます。</span><span class="sxs-lookup"><span data-stu-id="49661-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="49661-106">ファクトリの統合は、[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet パッケージで提供されています。</span><span class="sxs-lookup"><span data-stu-id="49661-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="49661-107">ファクトリには以下のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="49661-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="49661-108">論理 gRPC クライアント インスタンスの構成を一元管理する場所となります</span><span class="sxs-lookup"><span data-stu-id="49661-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="49661-109">基になる `HttpClientMessageHandler` の存続期間を管理します</span><span class="sxs-lookup"><span data-stu-id="49661-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="49661-110">ASP.NET Core gRPC サービスで期限とキャンセルを自動伝達</span><span class="sxs-lookup"><span data-stu-id="49661-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="49661-111">gRPC クライアントを登録する</span><span class="sxs-lookup"><span data-stu-id="49661-111">Register gRPC clients</span></span>

<span data-ttu-id="49661-112">gRPC クライアントを登録するには、ジェネリック `AddGrpcClient` 拡張メソッドを `Startup.ConfigureServices` 内で使用して、gRPC の型指定されたクライアント クラスとサービス アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="49661-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="49661-113">GRPC クライアントの種類は、依存関係の挿入 (DI) がある一時的なものとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="49661-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="49661-114">これで、DI によって作成される種類でクライアントを直接挿入し、使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="49661-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="49661-115">ASP.NET Core MVC コントローラー、SignalR ハブ、および gRPC サービスは、gRPC クライアントを自動的に挿入できる場所です。</span><span class="sxs-lookup"><span data-stu-id="49661-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="49661-116">HttpClient を構成する</span><span class="sxs-lookup"><span data-stu-id="49661-116">Configure HttpClient</span></span>

<span data-ttu-id="49661-117">`HttpClientFactory` では、gRPC クライアントによって使用される `HttpClient` が作成されます。</span><span class="sxs-lookup"><span data-stu-id="49661-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="49661-118">標準の `HttpClientFactory` メソッドを使用して、送信要求ミドルウェアを追加したり、`HttpClient` の基になる `HttpClientHandler` を構成したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="49661-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="49661-119">詳細については、[IHttpClientFactory を使用した HTTP 要求の作成](xref:fundamentals/http-requests)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="49661-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="49661-120">チャネルとインターセプターを構成する</span><span class="sxs-lookup"><span data-stu-id="49661-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="49661-121">gRPC 固有のメソッドは、以下のために使用できます。</span><span class="sxs-lookup"><span data-stu-id="49661-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="49661-122">gRPC クライアントの基になるチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="49661-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="49661-123">gRPC 呼び出しを行うときにクライアントが使用する `Interceptor` インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="49661-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="49661-124">期限とキャンセルの伝達</span><span class="sxs-lookup"><span data-stu-id="49661-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="49661-125">gRPC サービスでファクトリによって作成された gRPC クライアントは、`EnableCallContextPropagation()` を使用して、期限とキャンセル トークンが子の呼び出しに自動伝達されるように構成できます。</span><span class="sxs-lookup"><span data-stu-id="49661-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="49661-126">`EnableCallContextPropagation()` 拡張メソッドは [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet パッケージで提供されています。</span><span class="sxs-lookup"><span data-stu-id="49661-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="49661-127">呼び出しコンテキストの伝達は、現在の gRPC 要求コンテキストから期限とキャンセル トークンを読み取り、それらを、gRPC クライアントによって行われた送信呼び出しに自動的に伝達することによって機能します。</span><span class="sxs-lookup"><span data-stu-id="49661-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="49661-128">呼び出しコンテキストの伝達は、複雑で入れ子になった gRPC のシナリオで、常に期限とキャンセルが確実に伝達されるようにする優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="49661-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="49661-129">既定では、クライアントが gRPC 呼び出しのコンテキスト外で使用されると、`EnableCallContextPropagation` によってエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="49661-129">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="49661-130">このエラーは、伝達する呼び出しコンテキストが存在しないことを警告するために設計されています。</span><span class="sxs-lookup"><span data-stu-id="49661-130">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="49661-131">クライアントを呼び出しコンテキストの外部で使用する場合は、クライアントが `SuppressContextNotFoundErrors` で構成されていると、エラーが抑制されます。</span><span class="sxs-lookup"><span data-stu-id="49661-131">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="49661-132">期限と RPC のキャンセルの詳細については、「[RPC ライフサイクル](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49661-132">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49661-133">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="49661-133">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>

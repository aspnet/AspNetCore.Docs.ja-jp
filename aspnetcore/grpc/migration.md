---
title: gRPC サービスの C-core から ASP.NET Core への移行
author: juntaoluo
description: 既存の C-core ベースの gRPC アプリを移動して ASP.NET Core スタック上で実行する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 27c53dd4b41d6c99e45fccb5af79bab1ed5dc1b9
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253151"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="71668-103">gRPC サービスの C-core から ASP.NET Core への移行</span><span class="sxs-lookup"><span data-stu-id="71668-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="71668-104">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="71668-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="71668-105">基になるスタックの実装のため、[C-core ベースの gRPC](https://grpc.io/blog/grpc-stacks) アプリと ASP.NET Core ベースのアプリ間で、すべての機能が同じように動作するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="71668-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="71668-106">このドキュメントでは、2 つのスタック間で移行する場合の主な違いについて説明します。</span><span class="sxs-lookup"><span data-stu-id="71668-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="71668-107">gRPC サービス実装の有効期間</span><span class="sxs-lookup"><span data-stu-id="71668-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="71668-108">ASP.NET Core スタックでは、既定で gRPC サービスは、[スコープが設定された有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で作成されます。</span><span class="sxs-lookup"><span data-stu-id="71668-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="71668-109">これに対し、gRPC C-core は、既定で、[シングルトン有効期間](xref:fundamentals/dependency-injection#service-lifetimes)でサービスにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="71668-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="71668-110">スコープが設定された有効期間により、サービス実装では、スコープが設定された有効期間を持つその他のサービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="71668-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="71668-111">たとえば、スコープが設定された有効期間では、コンストラクター インジェクションによって DI コンテナーから `DbContext` を解決することもできます。</span><span class="sxs-lookup"><span data-stu-id="71668-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="71668-112">スコープが設定された有効期間の使用:</span><span class="sxs-lookup"><span data-stu-id="71668-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="71668-113">サービス実装の新しいインスタンスが、要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="71668-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="71668-114">実装型のインスタンス メンバーを介して、要求間で状態を共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="71668-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="71668-115">シングルトン サービスでの共有状態は、DI コンテナーに格納することが期待されます。</span><span class="sxs-lookup"><span data-stu-id="71668-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="71668-116">格納された共有状態は、gRPC サービス実装のコンストラクターで解決されます。</span><span class="sxs-lookup"><span data-stu-id="71668-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="71668-117">サービス有効期間の詳細については、「<xref:fundamentals/dependency-injection#service-lifetimes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71668-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="71668-118">シングルトン サービスの追加</span><span class="sxs-lookup"><span data-stu-id="71668-118">Add a singleton service</span></span>

<span data-ttu-id="71668-119">gRPC C-core 実装から ASP.NET Core への移行を容易にするために、サービス実装のサービス有効期間をスコープからシングルトンに変更することができます。</span><span class="sxs-lookup"><span data-stu-id="71668-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="71668-120">これには、サービス実装のインスタンスを DI コンテナーに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71668-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="71668-121">ただし、シングルトン有効期間を持つサービス実装では、コンストラクター インジェクションによって、スコープ設定されたサービスを解決できなくなります。</span><span class="sxs-lookup"><span data-stu-id="71668-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="71668-122">gRPC サービス オプションの構成</span><span class="sxs-lookup"><span data-stu-id="71668-122">Configure gRPC services options</span></span>

<span data-ttu-id="71668-123">C-core ベースのアプリでは、`grpc.max_receive_message_length` や `grpc.max_send_message_length` などの設定は、[サーバー インスタンスの構築](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)時に `ChannelOption` で構成します。</span><span class="sxs-lookup"><span data-stu-id="71668-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="71668-124">ASP.NET Core では、gRPC で `GrpcServiceOptions` 型による構成を提供しています。</span><span class="sxs-lookup"><span data-stu-id="71668-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="71668-125">たとえば、gRPC サービスの最大受信メッセージ サイズは、`AddGrpc` を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="71668-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="71668-126">次の例では、既定の `MaxReceiveMessageSize` を 4 MB から 16 MB に変更しています。</span><span class="sxs-lookup"><span data-stu-id="71668-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="71668-127">構成の詳細については、「<xref:grpc/configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71668-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="71668-128">ログの記録</span><span class="sxs-lookup"><span data-stu-id="71668-128">Logging</span></span>

<span data-ttu-id="71668-129">C-core ベースのアプリでは、デバッグ目的で[ロガーを構成する](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)ために `GrpcEnvironment` に依存しています。</span><span class="sxs-lookup"><span data-stu-id="71668-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="71668-130">ASP.NET Core スタックでは、[ロギング API](xref:fundamentals/logging/index) によってこの機能を提供しています。</span><span class="sxs-lookup"><span data-stu-id="71668-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="71668-131">たとえば、コンストラクター インジェクションを使用して、gRPC サービスにロガーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="71668-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="71668-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="71668-132">HTTPS</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="71668-133">C-core ベースのアプリでは、[Server. Ports プロパティ](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)を使用して HTTPS を構成します。</span><span class="sxs-lookup"><span data-stu-id="71668-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="71668-134">同様の概念は、ASP.NET Core でサーバーを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="71668-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="71668-135">たとえば、Kestrel では、この機能のために[エンドポイント構成](xref:fundamentals/servers/kestrel/endpoints)を使用します。</span><span class="sxs-lookup"><span data-stu-id="71668-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) for this functionality.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="71668-136">C-core ベースのアプリでは、[Server. Ports プロパティ](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)を使用して HTTPS を構成します。</span><span class="sxs-lookup"><span data-stu-id="71668-136">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="71668-137">同様の概念は、ASP.NET Core でサーバーを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="71668-137">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="71668-138">たとえば、Kestrel では、この機能のために[エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration)を使用します。</span><span class="sxs-lookup"><span data-stu-id="71668-138">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>
::: moniker-end

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="71668-139">gRPC インターセプターとミドルウェア</span><span class="sxs-lookup"><span data-stu-id="71668-139">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="71668-140">ASP.NET Core [ミドルウェア](xref:fundamentals/middleware/index)は、C-core ベースの gRPC アプリのインターセプターと比較して、同様の機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="71668-140">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="71668-141">ASP.NET Core ミドルウェアとインターセプターは概念的に似ています。</span><span class="sxs-lookup"><span data-stu-id="71668-141">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="71668-142">両方:</span><span class="sxs-lookup"><span data-stu-id="71668-142">Both:</span></span>

* <span data-ttu-id="71668-143">gRPC 要求を処理するパイプラインを構築するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="71668-143">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="71668-144">パイプライン内の次のコンポーネントの前または後で作業が実行されます。</span><span class="sxs-lookup"><span data-stu-id="71668-144">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="71668-145">`HttpContext` へのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="71668-145">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="71668-146">ミドルウェアでは、`HttpContext` はパラメーターです。</span><span class="sxs-lookup"><span data-stu-id="71668-146">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="71668-147">インターセプターでは、`ServerCallContext.GetHttpContext` 拡張メソッドで `ServerCallContext` パラメーターを使用して、`HttpContext` にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="71668-147">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="71668-148">この機能は、ASP.NET Core で実行されているインターセプターに固有です。</span><span class="sxs-lookup"><span data-stu-id="71668-148">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="71668-149">gRPC インターセプターと ASP.NET Core ミドルウェアの違い:</span><span class="sxs-lookup"><span data-stu-id="71668-149">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="71668-150">インターセプター:</span><span class="sxs-lookup"><span data-stu-id="71668-150">Interceptors:</span></span>
  * <span data-ttu-id="71668-151">[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html) を使用して、抽象化の gRPC レイヤーで動作します。</span><span class="sxs-lookup"><span data-stu-id="71668-151">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="71668-152">次にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="71668-152">Provide access to:</span></span>
    * <span data-ttu-id="71668-153">呼び出しに送信された逆シリアル化されたメッセージ。</span><span class="sxs-lookup"><span data-stu-id="71668-153">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="71668-154">シリアル化される前に、呼び出しから返されたメッセージ。</span><span class="sxs-lookup"><span data-stu-id="71668-154">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="71668-155">GRPC サービスからスローされた例外をキャッチして処理できます。</span><span class="sxs-lookup"><span data-stu-id="71668-155">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="71668-156">ミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="71668-156">Middleware:</span></span>
  * <span data-ttu-id="71668-157">gRPC インターセプターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="71668-157">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="71668-158">基になる HTTP/2 メッセージを操作します。</span><span class="sxs-lookup"><span data-stu-id="71668-158">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="71668-159">要求ストリームと応答ストリームからのバイトのみにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="71668-159">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71668-160">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="71668-160">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

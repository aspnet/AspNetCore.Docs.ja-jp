---
title: ASP.NET Core を使用した gRPC サービス
author: juntaoluo
description: ASP.NET Core で gRPC サービスを作成するときの基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/14/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 44a6f1d2a25314460fa4bce469f697a2fa4c0825
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252852"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="69f0d-103">ASP.NET Core を使用した gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="69f0d-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="69f0d-104">このドキュメントでは、ASP.NET Core を使用して gRPC サービスを開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="69f0d-105">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="69f0d-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69f0d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69f0d-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="69f0d-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="69f0d-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="69f0d-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="69f0d-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="69f0d-109">ASP.NET Core の gRPC サービスの概要</span><span class="sxs-lookup"><span data-stu-id="69f0d-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="69f0d-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="69f0d-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69f0d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69f0d-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="69f0d-112">gRPC プロジェクトを作成する詳細な手順については、[gRPC サービスの概要](xref:tutorials/grpc/grpc-start)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69f0d-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="69f0d-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="69f0d-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="69f0d-114">コマンド ラインから `dotnet new grpc -o GrpcGreeter` を実行します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="69f0d-115">ASP.NET Core アプリに gRPC サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="69f0d-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="69f0d-116">gRPC には [Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="69f0d-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="69f0d-117">gRPC を構成する</span><span class="sxs-lookup"><span data-stu-id="69f0d-117">Configure gRPC</span></span>

<span data-ttu-id="69f0d-118">*Startup.cs* の場合:</span><span class="sxs-lookup"><span data-stu-id="69f0d-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="69f0d-119">gRPC を有効にするには、`AddGrpc` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="69f0d-120">各 gRPC サービスをルーティング パイプラインに追加するには、`MapGrpcService` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="69f0d-121">ルーティング パイプラインは ASP.NET Core のミドルウェアと機能で共有されるため、追加の要求ハンドラーを提供するようにアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="69f0d-122">MVC コントローラーなどの追加の要求ハンドラーは、構成されている gRPC サービスと並行して動作します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="configure-kestrel"></a><span data-ttu-id="69f0d-123">Kestrel を構成する</span><span class="sxs-lookup"><span data-stu-id="69f0d-123">Configure Kestrel</span></span>

<span data-ttu-id="69f0d-124">Kestrel gRPC エンドポイントは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="69f0d-125">HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="69f0d-125">Require HTTP/2.</span></span>
* <span data-ttu-id="69f0d-126">[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="69f0d-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="69f0d-127">HTTP/2</span></span>

<span data-ttu-id="69f0d-128">gRPC には HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="69f0d-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="69f0d-129">ASP.NET Core 用 gRPC は、[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) が `HTTP/2` であるかを検証します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="69f0d-130">Kestrel は、最新のオペレーティング システムで [HTTP/2 をサポート](xref:fundamentals/servers/kestrel/http2)しています。</span><span class="sxs-lookup"><span data-stu-id="69f0d-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="69f0d-131">Kestrel エンドポイントは、既定で HTTP/1.1 接続と HTTP/2 接続をサポートするように構成されています。</span><span class="sxs-lookup"><span data-stu-id="69f0d-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="69f0d-132">TLS</span><span class="sxs-lookup"><span data-stu-id="69f0d-132">TLS</span></span>

<span data-ttu-id="69f0d-133">gRPC に使用される Kestrel エンドポイントは、TLS で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="69f0d-134">開発環境では、ASP.NET Core 開発証明書が存在する場合に、TLS で保護されたエンドポイントが `https://localhost:5001` に自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="69f0d-135">構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="69f0d-135">No configuration is required.</span></span> <span data-ttu-id="69f0d-136">`https` プレフィックスによって、Kestrel エンドポイントが TLS を使用していることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="69f0d-137">運用環境では、TLS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="69f0d-138">次の *appsettings.json* の例では、TLS で保護された HTTP/2 エンドポイントが指定されています。</span><span class="sxs-lookup"><span data-stu-id="69f0d-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="69f0d-139">または、*Program.cs* 内に Kestrel エンドポイントを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="69f0d-140">プロトコル ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="69f0d-140">Protocol negotiation</span></span>

<span data-ttu-id="69f0d-141">TLS は、通信のセキュリティ保護以外にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="69f0d-142">エンドポイントで複数のプロトコルがサポートされている場合、クライアントとサーバー間の接続プロトコルをネゴシエートするために TLS [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクが使用されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="69f0d-143">このネゴシエーションでは、HTTP/1.1 または HTTP/2 のどちらが接続に使用されるかを判断します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="69f0d-144">HTTP/2 エンドポイントが TLS を使用せずに構成されている場合は、エンドポイントの [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) を `HttpProtocols.Http2` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="69f0d-145">複数のプロトコル (`HttpProtocols.Http1AndHttp2` など) を持つエンドポイントは、ネゴシエーションがないため、TLS なしで使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="69f0d-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="69f0d-146">セキュリティで保護されていないエンドポイントへの接続はすべて、既定で HTTP/1.1 に設定されるため、gRPC の呼び出しは失敗します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="69f0d-147">Kestrel で HTTP/2 および TLS を有効にする方法について詳しくは、[Kestrel のエンドポイント構成](xref:fundamentals/servers/kestrel/endpoints)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69f0d-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="69f0d-148">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="69f0d-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="69f0d-149">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="69f0d-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="69f0d-150">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69f0d-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="configure-kestrel"></a><span data-ttu-id="69f0d-151">Kestrel を構成する</span><span class="sxs-lookup"><span data-stu-id="69f0d-151">Configure Kestrel</span></span>

<span data-ttu-id="69f0d-152">Kestrel gRPC エンドポイントは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-152">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="69f0d-153">HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="69f0d-153">Require HTTP/2.</span></span>
* <span data-ttu-id="69f0d-154">[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-154">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="69f0d-155">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="69f0d-155">HTTP/2</span></span>

<span data-ttu-id="69f0d-156">gRPC には HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="69f0d-156">gRPC requires HTTP/2.</span></span> <span data-ttu-id="69f0d-157">ASP.NET Core 用 gRPC は、[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) が `HTTP/2` であるかを検証します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-157">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="69f0d-158">Kestrel は、最新のオペレーティング システムで [HTTP/2 をサポート](xref:fundamentals/servers/kestrel#http2-support)しています。</span><span class="sxs-lookup"><span data-stu-id="69f0d-158">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="69f0d-159">Kestrel エンドポイントは、既定で HTTP/1.1 接続と HTTP/2 接続をサポートするように構成されています。</span><span class="sxs-lookup"><span data-stu-id="69f0d-159">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="69f0d-160">TLS</span><span class="sxs-lookup"><span data-stu-id="69f0d-160">TLS</span></span>

<span data-ttu-id="69f0d-161">gRPC に使用される Kestrel エンドポイントは、TLS で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-161">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="69f0d-162">開発環境では、ASP.NET Core 開発証明書が存在する場合に、TLS で保護されたエンドポイントが `https://localhost:5001` に自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-162">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="69f0d-163">構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="69f0d-163">No configuration is required.</span></span> <span data-ttu-id="69f0d-164">`https` プレフィックスによって、Kestrel エンドポイントが TLS を使用していることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-164">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="69f0d-165">運用環境では、TLS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-165">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="69f0d-166">次の *appsettings.json* の例では、TLS で保護された HTTP/2 エンドポイントが指定されています。</span><span class="sxs-lookup"><span data-stu-id="69f0d-166">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="69f0d-167">または、*Program.cs* 内に Kestrel エンドポイントを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-167">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="69f0d-168">プロトコル ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="69f0d-168">Protocol negotiation</span></span>

<span data-ttu-id="69f0d-169">TLS は、通信のセキュリティ保護以外にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-169">TLS is used for more than securing communication.</span></span> <span data-ttu-id="69f0d-170">エンドポイントで複数のプロトコルがサポートされている場合、クライアントとサーバー間の接続プロトコルをネゴシエートするために TLS [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクが使用されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-170">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="69f0d-171">このネゴシエーションでは、HTTP/1.1 または HTTP/2 のどちらが接続に使用されるかを判断します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-171">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="69f0d-172">HTTP/2 エンドポイントが TLS を使用せずに構成されている場合は、エンドポイントの [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) を `HttpProtocols.Http2` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-172">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="69f0d-173">複数のプロトコル (`HttpProtocols.Http1AndHttp2` など) を持つエンドポイントは、ネゴシエーションがないため、TLS なしで使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="69f0d-173">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="69f0d-174">セキュリティで保護されていないエンドポイントへの接続はすべて、既定で HTTP/1.1 に設定されるため、gRPC の呼び出しは失敗します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-174">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="69f0d-175">Kestrel で HTTP/2 および TLS を有効にする方法について詳しくは、[Kestrel のエンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69f0d-175">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="69f0d-176">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="69f0d-176">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="69f0d-177">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="69f0d-177">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="69f0d-178">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69f0d-178">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="69f0d-179">ASP.NET Core API との統合</span><span class="sxs-lookup"><span data-stu-id="69f0d-179">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="69f0d-180">gRPC サービスには、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) や[ログ記録](xref:fundamentals/logging/index)などの ASP.NET Core 機能へのフル アクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="69f0d-180">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="69f0d-181">たとえば、サービスの実装では、コンストラクターを使用して DI コンテナーから logger サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-181">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="69f0d-182">既定では、gRPC サービスの実装では、任意の有効期間 (シングルトン、スコープ、または一時的) を持つ他の DI サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-182">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="69f0d-183">gRPC メソッドで HttpContext を解決する</span><span class="sxs-lookup"><span data-stu-id="69f0d-183">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="69f0d-184">gRPC API は、メソッド、ホスト、ヘッダー、トレーラーなど、一部の HTTP/2 メッセージ データへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="69f0d-184">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="69f0d-185">アクセスには、各 gRPC メソッドに渡される `ServerCallContext` 引数が使用されます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-185">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="69f0d-186">`ServerCallContext` を使用しても、すべての ASP.NET API で `HttpContext` へのフル アクセスが提供されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="69f0d-186">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="69f0d-187">`GetHttpContext` 拡張メソッドを使用すると、ASP.NET API で基となる HTTP/2 メッセージを表す `HttpContext` にフル アクセスできます。</span><span class="sxs-lookup"><span data-stu-id="69f0d-187">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="69f0d-188">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="69f0d-188">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>

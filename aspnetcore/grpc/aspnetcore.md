---
title: ASP.NET Core を使用した gRPC サービス
author: juntaoluo
description: ASP.NET Core で gRPC サービスを作成するときの基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
ms.openlocfilehash: f17ba247747f906cf026fc0f7bc04d51f4c8cb2a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530204"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="708d8-103">ASP.NET Core を使用した gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="708d8-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="708d8-104">このドキュメントでは、ASP.NET Core を使用して gRPC サービスを開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="708d8-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="708d8-105">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="708d8-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="708d8-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="708d8-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="708d8-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="708d8-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="708d8-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="708d8-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="708d8-109">ASP.NET Core の gRPC サービスの概要</span><span class="sxs-lookup"><span data-stu-id="708d8-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="708d8-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="708d8-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="708d8-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="708d8-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="708d8-112">gRPC プロジェクトを作成する詳細な手順については、[gRPC サービスの概要](xref:tutorials/grpc/grpc-start)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="708d8-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="708d8-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="708d8-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="708d8-114">コマンド ラインから `dotnet new grpc -o GrpcGreeter` を実行します。</span><span class="sxs-lookup"><span data-stu-id="708d8-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="708d8-115">ASP.NET Core アプリに gRPC サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="708d8-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="708d8-116">gRPC には [Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="708d8-117">gRPC を構成する</span><span class="sxs-lookup"><span data-stu-id="708d8-117">Configure gRPC</span></span>

<span data-ttu-id="708d8-118">*Startup.cs* の場合:</span><span class="sxs-lookup"><span data-stu-id="708d8-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="708d8-119">gRPC を有効にするには、`AddGrpc` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="708d8-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="708d8-120">各 gRPC サービスをルーティング パイプラインに追加するには、`MapGrpcService` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="708d8-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="708d8-121">ルーティング パイプラインは ASP.NET Core のミドルウェアと機能で共有されるため、追加の要求ハンドラーを提供するようにアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="708d8-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="708d8-122">MVC コントローラーなどの追加の要求ハンドラーは、構成されている gRPC サービスと並行して動作します。</span><span class="sxs-lookup"><span data-stu-id="708d8-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="708d8-123">サーバー オプション</span><span class="sxs-lookup"><span data-stu-id="708d8-123">Server options</span></span>

<span data-ttu-id="708d8-124">gRPC サービスは、すべての組み込み ASP.NET Core サーバーによってホストできます。</span><span class="sxs-lookup"><span data-stu-id="708d8-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="708d8-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="708d8-125">Kestrel</span></span>
> * <span data-ttu-id="708d8-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="708d8-126">TestServer</span></span>
> * <span data-ttu-id="708d8-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="708d8-127">IIS&dagger;</span></span>
> * <span data-ttu-id="708d8-128">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="708d8-128">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="708d8-129">&dagger;IIS では、.NET 5 および Windows 10 ビルド 20241 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-129">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="708d8-130">&Dagger;HTTP.sys では、.NET 5 および Windows 10 ビルド 19529 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-130">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="708d8-131">ASP.NET Core アプリに適切なサーバーを選択する方法の詳細については、「<xref:fundamentals/servers/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="708d8-131">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="708d8-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="708d8-132">Kestrel</span></span>

<span data-ttu-id="708d8-133">[Kestrel](xref:fundamentals/servers/kestrel) は、ASP.NET Core 向けのクロスプラットフォーム Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="708d8-133">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="708d8-134">Kestrel を使用すると、最高のパフォーマンスとメモリ使用率が提供されますが、ポート共有など、HTTP.sys の高度な機能の一部は提供されません。</span><span class="sxs-lookup"><span data-stu-id="708d8-134">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="708d8-135">Kestrel gRPC エンドポイントは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="708d8-135">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="708d8-136">HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-136">Require HTTP/2.</span></span>
* <span data-ttu-id="708d8-137">[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-137">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="708d8-138">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="708d8-138">HTTP/2</span></span>

<span data-ttu-id="708d8-139">gRPC には HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-139">gRPC requires HTTP/2.</span></span> <span data-ttu-id="708d8-140">ASP.NET Core 用 gRPC は、[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) が `HTTP/2` であるかを検証します。</span><span class="sxs-lookup"><span data-stu-id="708d8-140">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="708d8-141">Kestrel は、最新のオペレーティング システムで [HTTP/2 をサポート](xref:fundamentals/servers/kestrel/http2)しています。</span><span class="sxs-lookup"><span data-stu-id="708d8-141">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="708d8-142">Kestrel エンドポイントは、既定で HTTP/1.1 接続と HTTP/2 接続をサポートするように構成されています。</span><span class="sxs-lookup"><span data-stu-id="708d8-142">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="708d8-143">TLS</span><span class="sxs-lookup"><span data-stu-id="708d8-143">TLS</span></span>

<span data-ttu-id="708d8-144">gRPC に使用される Kestrel エンドポイントは、TLS で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-144">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="708d8-145">開発環境では、ASP.NET Core 開発証明書が存在する場合に、TLS で保護されたエンドポイントが `https://localhost:5001` に自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-145">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="708d8-146">構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="708d8-146">No configuration is required.</span></span> <span data-ttu-id="708d8-147">`https` プレフィックスによって、Kestrel エンドポイントが TLS を使用していることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-147">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="708d8-148">運用環境では、TLS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-148">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="708d8-149">次の *appsettings.json* の例では、TLS で保護された HTTP/2 エンドポイントが指定されています。</span><span class="sxs-lookup"><span data-stu-id="708d8-149">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="708d8-150">または、*Program.cs* 内に Kestrel エンドポイントを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="708d8-150">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="708d8-151">プロトコル ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="708d8-151">Protocol negotiation</span></span>

<span data-ttu-id="708d8-152">TLS は、通信のセキュリティ保護以外にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-152">TLS is used for more than securing communication.</span></span> <span data-ttu-id="708d8-153">エンドポイントで複数のプロトコルがサポートされている場合、クライアントとサーバー間の接続プロトコルをネゴシエートするために TLS [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクが使用されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-153">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="708d8-154">このネゴシエーションでは、HTTP/1.1 または HTTP/2 のどちらが接続に使用されるかを判断します。</span><span class="sxs-lookup"><span data-stu-id="708d8-154">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="708d8-155">HTTP/2 エンドポイントが TLS を使用せずに構成されている場合は、エンドポイントの [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) を `HttpProtocols.Http2` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-155">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="708d8-156">複数のプロトコル (`HttpProtocols.Http1AndHttp2` など) を持つエンドポイントは、ネゴシエーションがないため、TLS なしで使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="708d8-156">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="708d8-157">セキュリティで保護されていないエンドポイントへの接続はすべて、既定で HTTP/1.1 に設定されるため、gRPC の呼び出しは失敗します。</span><span class="sxs-lookup"><span data-stu-id="708d8-157">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="708d8-158">Kestrel で HTTP/2 および TLS を有効にする方法について詳しくは、[Kestrel のエンドポイント構成](xref:fundamentals/servers/kestrel/endpoints)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="708d8-158">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="708d8-159">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="708d8-159">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="708d8-160">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-160">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="708d8-161">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="708d8-161">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="708d8-162">IIS</span><span class="sxs-lookup"><span data-stu-id="708d8-162">IIS</span></span>

<span data-ttu-id="708d8-163">[インターネット インフォメーション サービス (IIS)](xref:host-and-deploy/iis/index) は、Web アプリ (ASP.NET Core を含む) をホストするための、柔軟かつ安全で管理しやすい Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="708d8-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="708d8-164">IIS で gRPC サービスをホストするには、.NET 5 および Windows 10 ビルド 20241 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-164">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="708d8-165">IIS は、TLS と HTTP/2 を使用するように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-165">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="708d8-166">詳細については、「<xref:host-and-deploy/iis/protocols>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="708d8-166">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="708d8-167">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="708d8-167">HTTP.sys</span></span>

<span data-ttu-id="708d8-168">[HTTP.sys](xref:fundamentals/servers/httpsys) は、Windows 上でのみ動作する ASP.NET Core 用 Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="708d8-168">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="708d8-169">HTTP.sys で gRPC サービスをホストするには、.NET 5 および Windows 10 ビルド 20241 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-169">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="708d8-170">HTTP.sys は、TLS と HTTP/2 を使用するように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-170">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="708d8-171">詳細については、[HTTP.sys Web サーバーの HTTP/2 サポート](xref:fundamentals/servers/httpsys#http2-support)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="708d8-171">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="708d8-172">Kestrel</span><span class="sxs-lookup"><span data-stu-id="708d8-172">Kestrel</span></span>

<span data-ttu-id="708d8-173">[Kestrel](xref:fundamentals/servers/kestrel) は、ASP.NET Core 向けのクロスプラットフォーム Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="708d8-173">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="708d8-174">Kestrel を使用すると、最高のパフォーマンスとメモリ使用率が提供されますが、ポート共有など、HTTP.sys の高度な機能の一部は提供されません。</span><span class="sxs-lookup"><span data-stu-id="708d8-174">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="708d8-175">Kestrel gRPC エンドポイントは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="708d8-175">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="708d8-176">HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-176">Require HTTP/2.</span></span>
* <span data-ttu-id="708d8-177">[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-177">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="708d8-178">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="708d8-178">HTTP/2</span></span>

<span data-ttu-id="708d8-179">gRPC には HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-179">gRPC requires HTTP/2.</span></span> <span data-ttu-id="708d8-180">ASP.NET Core 用 gRPC は、[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) が `HTTP/2` であるかを検証します。</span><span class="sxs-lookup"><span data-stu-id="708d8-180">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="708d8-181">Kestrel は、最新のオペレーティング システムで [HTTP/2 をサポート](xref:fundamentals/servers/kestrel#http2-support)しています。</span><span class="sxs-lookup"><span data-stu-id="708d8-181">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="708d8-182">Kestrel エンドポイントは、既定で HTTP/1.1 接続と HTTP/2 接続をサポートするように構成されています。</span><span class="sxs-lookup"><span data-stu-id="708d8-182">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="708d8-183">TLS</span><span class="sxs-lookup"><span data-stu-id="708d8-183">TLS</span></span>

<span data-ttu-id="708d8-184">gRPC に使用される Kestrel エンドポイントは、TLS で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-184">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="708d8-185">開発環境では、ASP.NET Core 開発証明書が存在する場合に、TLS で保護されたエンドポイントが `https://localhost:5001` に自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-185">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="708d8-186">構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="708d8-186">No configuration is required.</span></span> <span data-ttu-id="708d8-187">`https` プレフィックスによって、Kestrel エンドポイントが TLS を使用していることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-187">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="708d8-188">運用環境では、TLS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-188">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="708d8-189">次の *appsettings.json* の例では、TLS で保護された HTTP/2 エンドポイントが指定されています。</span><span class="sxs-lookup"><span data-stu-id="708d8-189">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="708d8-190">または、*Program.cs* 内に Kestrel エンドポイントを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="708d8-190">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="708d8-191">プロトコル ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="708d8-191">Protocol negotiation</span></span>

<span data-ttu-id="708d8-192">TLS は、通信のセキュリティ保護以外にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-192">TLS is used for more than securing communication.</span></span> <span data-ttu-id="708d8-193">エンドポイントで複数のプロトコルがサポートされている場合、クライアントとサーバー間の接続プロトコルをネゴシエートするために TLS [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクが使用されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-193">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="708d8-194">このネゴシエーションでは、HTTP/1.1 または HTTP/2 のどちらが接続に使用されるかを判断します。</span><span class="sxs-lookup"><span data-stu-id="708d8-194">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="708d8-195">HTTP/2 エンドポイントが TLS を使用せずに構成されている場合は、エンドポイントの [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) を `HttpProtocols.Http2` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="708d8-195">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="708d8-196">複数のプロトコル (`HttpProtocols.Http1AndHttp2` など) を持つエンドポイントは、ネゴシエーションがないため、TLS なしで使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="708d8-196">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="708d8-197">セキュリティで保護されていないエンドポイントへの接続はすべて、既定で HTTP/1.1 に設定されるため、gRPC の呼び出しは失敗します。</span><span class="sxs-lookup"><span data-stu-id="708d8-197">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="708d8-198">Kestrel で HTTP/2 および TLS を有効にする方法について詳しくは、[Kestrel のエンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="708d8-198">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="708d8-199">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="708d8-199">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="708d8-200">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="708d8-200">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="708d8-201">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="708d8-201">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="708d8-202">ASP.NET Core API との統合</span><span class="sxs-lookup"><span data-stu-id="708d8-202">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="708d8-203">gRPC サービスには、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) や[ログ記録](xref:fundamentals/logging/index)などの ASP.NET Core 機能へのフル アクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="708d8-203">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="708d8-204">たとえば、サービスの実装では、コンストラクターを使用して DI コンテナーから logger サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="708d8-204">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="708d8-205">既定では、gRPC サービスの実装では、任意の有効期間 (シングルトン、スコープ、または一時的) を持つ他の DI サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="708d8-205">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="708d8-206">gRPC メソッドで HttpContext を解決する</span><span class="sxs-lookup"><span data-stu-id="708d8-206">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="708d8-207">gRPC API は、メソッド、ホスト、ヘッダー、トレーラーなど、一部の HTTP/2 メッセージ データへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="708d8-207">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="708d8-208">アクセスには、各 gRPC メソッドに渡される `ServerCallContext` 引数が使用されます。</span><span class="sxs-lookup"><span data-stu-id="708d8-208">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="708d8-209">`ServerCallContext` を使用しても、すべての ASP.NET API で `HttpContext` へのフル アクセスが提供されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="708d8-209">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="708d8-210">`GetHttpContext` 拡張メソッドを使用すると、ASP.NET API で基となる HTTP/2 メッセージを表す `HttpContext` にフル アクセスできます。</span><span class="sxs-lookup"><span data-stu-id="708d8-210">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="708d8-211">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="708d8-211">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>

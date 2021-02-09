---
title: ブラウザー アプリでの gRPC の使用
author: jamesnk
description: ASP.NET Core で、GRPC-Web を使用してブラウザー アプリから呼び出しできるように、gRPC サービスを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 0967a70b498156d9c4ea8818ee1c80b37d9f2d87
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217480"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="a6cea-103">ブラウザー アプリでの gRPC の使用</span><span class="sxs-lookup"><span data-stu-id="a6cea-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="a6cea-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a6cea-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="a6cea-105">既存の ASP.NET Core の gRPC サービスを、[gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) プロトコルを使用してブラウザー アプリから呼び出せるように構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="a6cea-106">gRPC-Web を使用すると、ブラウザーの JavaScript および Blazor アプリで gRPC サービスを呼び出せます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="a6cea-107">ブラウザーベースのアプリから HTTP/2 gRPC サービスを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="a6cea-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="a6cea-108">ASP.NET Core でホストされている gRPC サービスは、HTTP/2 gRPC と共に gRPC-Web をサポートするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="a6cea-109">既存の ASP.NET Core アプリに gRPC サービスを追加する手順については、「[ASP.NET Core アプリに gRPC サービスを追加する](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6cea-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="a6cea-110">gRPC プロジェクトを作成する手順については、「<xref:tutorials/grpc/grpc-start>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6cea-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="a6cea-111">ASP.NET Core の gRPC-Web と Envoy</span><span class="sxs-lookup"><span data-stu-id="a6cea-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="a6cea-112">gRPC-Web を ASP.NET Core アプリに追加する方法には、次の 2 つの選択肢があります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="a6cea-113">ASP.NET Core の gRPC HTTP/2 と共に、gRPC-Web をサポートします。</span><span class="sxs-lookup"><span data-stu-id="a6cea-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="a6cea-114">このオプションでは、`Grpc.AspNetCore.Web` パッケージによって提供されるミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="a6cea-115">[Envoy プロキシ](https://www.envoyproxy.io/)の gRPC-Web サポートを使用して、gRPC-Web を gRPC HTTP/2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="a6cea-116">変換された呼び出しが、ASP.NET Core アプリに転送されます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="a6cea-117">それぞれの方法には、長所と短所があります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="a6cea-118">アプリの環境で既にプロキシとして Envoy を使用している場合は、gRPC-Web サポートを提供するために Envoy を使用するのも、妥当かもしれません。</span><span class="sxs-lookup"><span data-stu-id="a6cea-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="a6cea-119">ASP.NET Core のみを必要とする gRPC-Web の基本的なソリューションについては、`Grpc.AspNetCore.Web` を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a6cea-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="a6cea-120">ASP.NET Core での gRPC-Web の構成</span><span class="sxs-lookup"><span data-stu-id="a6cea-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="a6cea-121">ASP.NET Core でホストされている gRPC サービスは、HTTP/2 gRPC と共に gRPC-Web をサポートするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="a6cea-122">gRPC-Web では、サービスを変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a6cea-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="a6cea-123">唯一の変更点はスタートアップ構成です。</span><span class="sxs-lookup"><span data-stu-id="a6cea-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="a6cea-124">ASP.NET Core gRPC サービスで gRPC-Web を有効にするには:</span><span class="sxs-lookup"><span data-stu-id="a6cea-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="a6cea-125">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="a6cea-126">アプリで gRPC-Web を使用するように構成するには、`UseGrpcWeb` と `EnableGrpcWeb` を *Startup.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="a6cea-127">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-127">The preceding code:</span></span>

* <span data-ttu-id="a6cea-128">GRPC-Web ミドルウェアの `UseGrpcWeb` を、ルーティングの後かつエンドポイントの前に追加します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="a6cea-129">`endpoints.MapGrpcService<GreeterService>()` メソッドで、`EnableGrpcWeb` を使用して gRPC-Web をサポートすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="a6cea-130">または、gRPC-Web ミドルウェアを構成して、すべてのサービスで既定で gRPC-Web がサポートされ、`EnableGrpcWeb` が不要になるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="a6cea-131">ミドルウェアを追加するときに `new GrpcWebOptions { DefaultEnabled = true }` を指定します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="a6cea-132">.NET Core 3.x で [HTTP.sys によりホストされている](xref:fundamentals/servers/httpsys)場合は、gRPC-Web が失敗する原因となる既知の問題があります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-132">There is a known issue that causes gRPC-Web to fail when [hosted by HTTP.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="a6cea-133">HTTP.sys で gRPC-Web を機能させるには、[こちら](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)で回避策を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6cea-133">A workaround to get gRPC-Web working on HTTP.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="a6cea-134">gRPC-Web と CORS</span><span class="sxs-lookup"><span data-stu-id="a6cea-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="a6cea-135">ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="a6cea-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a6cea-136">この制限は、ブラウザー アプリで gRPC-Web 呼び出しを行う場合に適用されます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="a6cea-137">たとえば、`https://www.contoso.com` によって提供されるブラウザー アプリでは、`https://services.contoso.com` でホストされている gRPC-Web サービスの呼び出しがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="a6cea-138">この制限を緩和するには、クロス オリジン リソース共有 (CORS) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="a6cea-139">ブラウザー アプリでクロス オリジン gRPC-Web 呼び出しを行えるようにするには、[ASP.NET Core で CORS](xref:security/cors) を設定します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="a6cea-140">組み込みの CORS サポートを使用し、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> で gRPC 固有のヘッダーを公開します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="a6cea-141">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-141">The preceding code:</span></span>

* <span data-ttu-id="a6cea-142">`AddCors` を呼び出して CORS サービスを追加し、gRPC 固有のヘッダーを公開する CORS ポリシーを構成します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="a6cea-143">`UseCors` を呼び出して、ルーティングの後かつエンドポイントの前に CORS ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="a6cea-144">`endpoints.MapGrpcService<GreeterService>()` メソッドが `RequiresCors` で CORS をサポートすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="a6cea-145">gRPC-Web とストリーミング</span><span class="sxs-lookup"><span data-stu-id="a6cea-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="a6cea-146">従来の HTTP/2 による gRPC では、すべての方向でストリーミングがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="a6cea-147">gRPC-Web では、ストリーミングのサポートが制限されています。</span><span class="sxs-lookup"><span data-stu-id="a6cea-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="a6cea-148">gRPC-Web ブラウザー クライアントでは、クライアント ストリーミング メソッドと双方向ストリーミング メソッドの呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a6cea-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="a6cea-149">Azure App Service および IIS でホストされている ASP.NET Core gRPC サービスでは、双方向ストリーミングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a6cea-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="a6cea-150">gRPC-Web を使用するときは、単項メソッドとサーバー ストリーミング メソッドのみを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a6cea-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="a6cea-151">ブラウザーから gRPC-Web を呼び出す</span><span class="sxs-lookup"><span data-stu-id="a6cea-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="a6cea-152">ブラウザー アプリでは gRPC-Web を使用して gRPC サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="a6cea-153">ブラウザーから gRPC-Web を使用して gRPC サービスを呼び出す場合、いくつかの要件と制限があります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="a6cea-154">サーバーは、gRPC-Web をサポートするように構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="a6cea-155">クライアント ストリーミングと双方向ストリーミングの呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a6cea-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="a6cea-156">サーバー ストリーミングはサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a6cea-156">Server streaming is supported.</span></span>
* <span data-ttu-id="a6cea-157">別のドメインで gRPC サービスを呼び出すには、サーバーで [CORS](xref:security/cors) を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="a6cea-158">JavaScript gRPC-Web クライアント</span><span class="sxs-lookup"><span data-stu-id="a6cea-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="a6cea-159">JavaScript gRPC-Web クライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="a6cea-160">JavaScript から gRPC-Web を使用する方法については、[gRPC-Web を使用した JavaScript クライアント コードの作成](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6cea-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="a6cea-161">.NET gRPC クライアントを使用して gRPC-Web を構成する</span><span class="sxs-lookup"><span data-stu-id="a6cea-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="a6cea-162">gRPC-Web 呼び出しを行うように .NET gRPC クライアントを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="a6cea-163">これは、ブラウザーでホストされ、JavaScript コードの同じ HTTP 制限がある [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) アプリに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="a6cea-164">.NET クライアントによる gRPC-Web の呼び出しは、[HTTP/2 gRPC と同じ](xref:grpc/client)です。</span><span class="sxs-lookup"><span data-stu-id="a6cea-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="a6cea-165">唯一の変更点は、チャネルの作成方法です。</span><span class="sxs-lookup"><span data-stu-id="a6cea-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="a6cea-166">gRPC-Web を使用するには:</span><span class="sxs-lookup"><span data-stu-id="a6cea-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="a6cea-167">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="a6cea-168">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) パッケージへの参照が確実に 2.29.0 以上であるようにします。</span><span class="sxs-lookup"><span data-stu-id="a6cea-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="a6cea-169">`GrpcWebHandler` を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="a6cea-170">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-170">The preceding code:</span></span>

* <span data-ttu-id="a6cea-171">gRPC-Web を使用するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="a6cea-172">クライアントを作成し、チャネルを使用して呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="a6cea-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="a6cea-173">`GrpcWebHandler` には次の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="a6cea-174">**InnerHandler**:gRPC HTTP 要求を行う基になる <xref:System.Net.Http.HttpMessageHandler> (`HttpClientHandler` など)。</span><span class="sxs-lookup"><span data-stu-id="a6cea-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="a6cea-175">**GrpcWebMode**:gRPC HTTP 要求の `Content-Type` が `application/grpc-web` または `application/grpc-web-text` であるかどうかを指定する列挙型。</span><span class="sxs-lookup"><span data-stu-id="a6cea-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="a6cea-176">`GrpcWebMode.GrpcWeb` は、コンテンツをエンコードせずに送信するように構成します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="a6cea-177">既定値です。</span><span class="sxs-lookup"><span data-stu-id="a6cea-177">Default value.</span></span>
    * <span data-ttu-id="a6cea-178">`GrpcWebMode.GrpcWebText` は、コンテンツを base64 でエンコードするように構成します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="a6cea-179">ブラウザーでのサーバー ストリーム呼び出しに必要です。</span><span class="sxs-lookup"><span data-stu-id="a6cea-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="a6cea-180">**HttpVersion**:基になる gRPC HTTP 要求で、[HttpRequestMessage](xref:System.Net.Http.HttpRequestMessage.Version) を設定するために使用される HTTP プロトコル `Version`。</span><span class="sxs-lookup"><span data-stu-id="a6cea-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="a6cea-181">gRPC-Web では特定のバージョンを必要とせず、指定しない限り、既定値はオーバーライドされません。</span><span class="sxs-lookup"><span data-stu-id="a6cea-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a6cea-182">生成された gRPC クライアントには、単項メソッドを呼び出すための同期メソッドと非同期メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="a6cea-183">たとえば、`SayHello` は同期であり、`SayHelloAsync` は非同期です。</span><span class="sxs-lookup"><span data-stu-id="a6cea-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="a6cea-184">Blazor WebAssembly アプリで同期メソッドを呼び出すと、アプリが応答しなくなります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="a6cea-185">Blazor WebAssembly では、常に非同期メソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a6cea-185">Async methods must always be used in Blazor WebAssembly.</span></span>

### <a name="use-grpc-client-factory-with-grpc-web"></a><span data-ttu-id="a6cea-186">gRPC-Web で gRPC クライアント ファクトリを使用する</span><span class="sxs-lookup"><span data-stu-id="a6cea-186">Use gRPC client factory with gRPC-Web</span></span>

<span data-ttu-id="a6cea-187">gRPC-Web と互換性のある .NET クライアントは、gRPC と [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory) の統合を使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-187">A gRPC-Web compatible .NET client can be created using gRPC's integration with [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span></span>

<span data-ttu-id="a6cea-188">gRPC-Web とクライアント ファクトリを使用するには:</span><span class="sxs-lookup"><span data-stu-id="a6cea-188">To use gRPC-Web with client factory:</span></span>

* <span data-ttu-id="a6cea-189">次のパッケージのプロジェクト ファイルにパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-189">Add package references to the project file for the following packages:</span></span>
  * [<span data-ttu-id="a6cea-190">Grpc.Net.Client.Web</span><span class="sxs-lookup"><span data-stu-id="a6cea-190">Grpc.Net.Client.Web</span></span>](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [<span data-ttu-id="a6cea-191">Grpc.Net.ClientFactory</span><span class="sxs-lookup"><span data-stu-id="a6cea-191">Grpc.Net.ClientFactory</span></span>](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* <span data-ttu-id="a6cea-192">汎用の `AddGrpcClient` 拡張メソッドを使用して、依存関係の注入 (DI) に gRPC クライアントを登録します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-192">Register a gRPC client with dependency injection (DI) using the generic `AddGrpcClient` extension method.</span></span> <span data-ttu-id="a6cea-193">Blazor WebAssembly アプリでは、サービスは `Program.cs` で DI に登録されます。</span><span class="sxs-lookup"><span data-stu-id="a6cea-193">In a Blazor WebAssembly app, services are registered with DI in `Program.cs`.</span></span>
* <span data-ttu-id="a6cea-194"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 拡張メソッドを使用して `GrpcWebHandler` を構成します。</span><span class="sxs-lookup"><span data-stu-id="a6cea-194">Configure `GrpcWebHandler` using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

<span data-ttu-id="a6cea-195">詳細については、「<xref:grpc/clientfactory>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a6cea-195">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6cea-196">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a6cea-196">Additional resources</span></span>

* [<span data-ttu-id="a6cea-197">Web クライアント用 gRPC の GitHub プロジェクト</span><span class="sxs-lookup"><span data-stu-id="a6cea-197">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>

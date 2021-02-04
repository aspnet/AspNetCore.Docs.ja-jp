---
title: プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する
author: rick-anderson
description: プロキシ サーバーとロード バランサーの背後にホストされているアプリの構成について説明します。このような構成では、要求の重要な情報がわからなくなることがよくあります。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e63821743dad565b51d5c2360dcc2fbf0632754f
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530048"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="0087c-103">プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する</span><span class="sxs-lookup"><span data-stu-id="0087c-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="0087c-104">作成者: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="0087c-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0087c-105">ASP.NET Core の推奨される構成では、アプリは IIS/ASP.NET Core モジュール、Nginx、または Apache を使ってホストされます。</span><span class="sxs-lookup"><span data-stu-id="0087c-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="0087c-106">プロキシ サーバー、ロード バランサー、および他のネットワーク アプライアンスにより、要求に関する情報が、アプリに到達する前にわからなくなることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="0087c-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="0087c-107">HTTPS 要求が HTTP によってプロキシされると、元のスキーム (HTTPS) は失われ、ヘッダーで転送される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="0087c-108">アプリは、インターネット上または社内ネットワーク上の本来の送信元ではなく、プロキシから要求を受信するため、送信元クライアントの IP アドレスもヘッダーで転送される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="0087c-109">この情報は、リダイレクト、認証、リンクの生成、ポリシーの評価、クライアントの位置情報など、要求の処理で重要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="0087c-110">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="0087c-110">Forwarded headers</span></span>

<span data-ttu-id="0087c-111">慣例によりは、プロキシは HTTP ヘッダーで情報を転送します。</span><span class="sxs-lookup"><span data-stu-id="0087c-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="0087c-112">Header</span><span class="sxs-lookup"><span data-stu-id="0087c-112">Header</span></span> | <span data-ttu-id="0087c-113">説明</span><span class="sxs-lookup"><span data-stu-id="0087c-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="0087c-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="0087c-114">X-Forwarded-For</span></span> | <span data-ttu-id="0087c-115">要求を開始したクライアントと、プロキシ チェーン内の後続のプロキシに関する情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="0087c-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="0087c-116">このパラメーターは、IP アドレス (および、必要に応じてポート番号) を含むことがあります。</span><span class="sxs-lookup"><span data-stu-id="0087c-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="0087c-117">プロキシ サーバーのチェーンにおいては、最初のパラメーターが、要求を最初に行ったクライアントを示します。</span><span class="sxs-lookup"><span data-stu-id="0087c-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="0087c-118">その後に、後続のプロキシの識別子が指定されています。</span><span class="sxs-lookup"><span data-stu-id="0087c-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="0087c-119">チェーン内の最後のプロキシは、パラメーターのリストには含まれません。</span><span class="sxs-lookup"><span data-stu-id="0087c-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="0087c-120">最後のプロキシの IP アドレスとオプションのポート番号は、トランスポート層のリモート IP アドレスとして入手できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="0087c-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="0087c-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="0087c-122">開始スキーム (HTTP/HTTPS) の値です。</span><span class="sxs-lookup"><span data-stu-id="0087c-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="0087c-123">要求が複数のプロキシを通過している場合、値はスキームのリストである場合もあります。</span><span class="sxs-lookup"><span data-stu-id="0087c-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="0087c-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="0087c-124">X-Forwarded-Host</span></span> | <span data-ttu-id="0087c-125">ホスト ヘッダー フィールドの元の値です。</span><span class="sxs-lookup"><span data-stu-id="0087c-125">The original value of the Host header field.</span></span> <span data-ttu-id="0087c-126">通常、プロキシはホスト ヘッダーを変更しません。</span><span class="sxs-lookup"><span data-stu-id="0087c-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="0087c-127">プロキシにおいてホスト ヘッダーが既知の適切な値であることが検証されない、または既知の適切な値に制限されないシステムに影響を与える、特権の昇格脆弱性については、[マイクロソフト セキュリティ アドバイザリ CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="0087c-128">Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>) によって、これらのヘッダーが読み取られ、<xref:Microsoft.AspNetCore.Http.HttpContext> 上の関連するフィールドに入力されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="0087c-129">ミドルウェアの更新:</span><span class="sxs-lookup"><span data-stu-id="0087c-129">The middleware updates:</span></span>

* <span data-ttu-id="0087c-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress):`X-Forwarded-For` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="0087c-131">追加の設定は、ミドルウェアが `RemoteIpAddress` を設定する方法に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="0087c-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="0087c-132">詳しくは、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="0087c-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme):`X-Forwarded-Proto` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="0087c-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host):`X-Forwarded-Host` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="0087c-135">Forwarded Headers Middleware の[既定の設定](#forwarded-headers-middleware-options)は構成できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="0087c-136">既定の設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0087c-136">The default settings are:</span></span>

* <span data-ttu-id="0087c-137">アプリと要求のソースの間には、"*1 つのプロキシ*" だけが存在します。</span><span class="sxs-lookup"><span data-stu-id="0087c-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="0087c-138">既知のプロキシと既知のネットワークに対しては、ループバック アドレスのみが構成されています。</span><span class="sxs-lookup"><span data-stu-id="0087c-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="0087c-139">転送されるヘッダーには `X-Forwarded-For` と `X-Forwarded-Proto` の名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="0087c-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="0087c-140">すべてのネットワーク アプライアンスが追加構成なしで `X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを追加するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="0087c-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="0087c-141">プロキシされた要求がアプリに届いたときにこれらのヘッダーが含まれていない場合は、アプライアンスの製造元のガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="0087c-142">アプライアンスが `X-Forwarded-For` と `X-Forwarded-Proto` 以外の名前を使用している場合は、アプライアンスで使用されるヘッダー名と一致するように <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> と <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> のオプションを設定してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="0087c-143">詳細については、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」と「[別のヘッダー名を使用するプロキシの構成](#configuration-for-a-proxy-that-uses-different-header-names)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="0087c-144">IIS/IIS Express と ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="0087c-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="0087c-145">アプリが IIS と ASP.NET Core モジュールの背後で[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホストされている場合、[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) によって Forwarded Headers Middleware が既定で有効にされます。</span><span class="sxs-lookup"><span data-stu-id="0087c-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="0087c-146">転送されるヘッダーの信頼に関する問題のため (たとえば、[IP スプーフィング](https://www.iplocation.net/ip-spoofing))、Forwarded Headers Middleware はアクティブ化されると最初に、ASP.NET Core モジュールに固有の制限された構成を使って、ミドルウェア パイプライン内で実行します。</span><span class="sxs-lookup"><span data-stu-id="0087c-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="0087c-147">ミドルウェアは、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送するように構成され、単一の localhost プロキシに制限されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="0087c-148">追加の構成が必要な場合は、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0087c-149">プロキシ サーバーとロード バランサーの他のシナリオ</span><span class="sxs-lookup"><span data-stu-id="0087c-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0087c-150">[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホストされている場合に [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) が使われていない場合は、Forwarded Headers Middleware は既定で有効になりません。</span><span class="sxs-lookup"><span data-stu-id="0087c-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="0087c-151"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を含む転送されたヘッダーをアプリで処理するためには、Forwarded Headers Middleware を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="0087c-152">ミドルウェアを有効にした後、ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合の既定の [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) は [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="0087c-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="0087c-153"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> でミドルウェアを構成して、`Startup.ConfigureServices` で `X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。</span><span class="sxs-lookup"><span data-stu-id="0087c-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="0087c-154">Forwarded Headers Middleware の順序</span><span class="sxs-lookup"><span data-stu-id="0087c-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="0087c-155">Forwarded Headers Middleware は、他のミドルウェアの前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="0087c-156">この順序により、転送されるヘッダー情報に依存するミドルウェアが処理にヘッダー値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="0087c-157">Forwarded Headers Middleware は、診断とエラー処理の後に実行できますが、`UseHsts` を呼び出す前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="0087c-158">または、診断の前に `UseForwardedHeaders` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0087c-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="0087c-159"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が `Startup.ConfigureServices` において指定されていない場合、または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を使って拡張メソッドに直接渡されない場合、転送される既定のヘッダーは [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="0087c-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="0087c-160">転送するヘッダーで <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> プロパティが構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="0087c-161">Nginx の構成</span><span class="sxs-lookup"><span data-stu-id="0087c-161">Nginx configuration</span></span>

<span data-ttu-id="0087c-162">`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送する場合は、<xref:host-and-deploy/linux-nginx#configure-nginx> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="0087c-163">詳細については、「[NGINX:Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)」 (NGINX: 転送されるヘッダーの使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="0087c-164">Apache の構成</span><span class="sxs-lookup"><span data-stu-id="0087c-164">Apache configuration</span></span>

<span data-ttu-id="0087c-165">`X-Forwarded-For` は自動的に追加されます (「[Apache Module mod_proxy:Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)」 (Apache モジュール mod_proxy: リバース プロキシがヘッダーを要求する) を参照)。</span><span class="sxs-lookup"><span data-stu-id="0087c-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="0087c-166">`X-Forwarded-Proto` ヘッダーを転送する方法については、<xref:host-and-deploy/linux-apache#configure-apache> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="0087c-167">Forwarded Headers Middleware のオプション</span><span class="sxs-lookup"><span data-stu-id="0087c-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="0087c-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> は、Forwarded Headers Middleware の動作を制御します。</span><span class="sxs-lookup"><span data-stu-id="0087c-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="0087c-169">既定の値の変更例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0087c-169">The following example changes the default values:</span></span>

* <span data-ttu-id="0087c-170">転送されるヘッダーのエントリ数を `2` に制限します。</span><span class="sxs-lookup"><span data-stu-id="0087c-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="0087c-171">`127.0.10.1` の既知のプロキシ アドレスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0087c-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="0087c-172">転送されるヘッダーの名前を既定の `X-Forwarded-For` から `X-Forwarded-For-My-Custom-Header-Name` に変更します。</span><span class="sxs-lookup"><span data-stu-id="0087c-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="0087c-173">オプション</span><span class="sxs-lookup"><span data-stu-id="0087c-173">Option</span></span> | <span data-ttu-id="0087c-174">説明</span><span class="sxs-lookup"><span data-stu-id="0087c-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="0087c-175">`X-Forwarded-Host` ヘッダーによるホストを指定した値に制限します。</span><span class="sxs-lookup"><span data-stu-id="0087c-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="0087c-176">値は、大文字と小文字を無視して序数で比較されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="0087c-177">ポート番号は除外されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="0087c-178">リストが空の場合、すべてのホストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="0087c-179">最上位のワイルドカード `*` は、すべての空でないホストを許可します。</span><span class="sxs-lookup"><span data-stu-id="0087c-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="0087c-180">サブドメインのワイルドカードは許可されますが、ルート ドメインとは一致しません。</span><span class="sxs-lookup"><span data-stu-id="0087c-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="0087c-181">たとえば、`*.contoso.com` はサブドメイン `foo.contoso.com` と一致しますが、ルート ドメイン `contoso.com` とは一致しません。</span><span class="sxs-lookup"><span data-stu-id="0087c-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="0087c-182">Unicode のホスト名は許可されますが、一致のために [Punycode](https://tools.ietf.org/html/rfc3492) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="0087c-183">[IPv6 アドレス](https://tools.ietf.org/html/rfc4291)は境界の角かっこを含む必要があり、[従来の形式](https://tools.ietf.org/html/rfc4291#section-2.2) (例: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`) になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="0087c-184">IPv6 アドレスは、異なる形式間で論理的な等価性を調べるために特別な大文字小文字にはされず、正規化は行われません。</span><span class="sxs-lookup"><span data-stu-id="0087c-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="0087c-185">許可されるホストの制限に失敗すると、サービスによって生成されたリンクを攻撃者が偽装する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="0087c-186">既定値は空の `IList<string>` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="0087c-187">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="0087c-188">このオプションは、プロキシ/フォワーダーが `X-Forwarded-For` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0087c-189">既定値は、`X-Forwarded-For` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="0087c-190">処理する必要があるフォワーダーを識別します。</span><span class="sxs-lookup"><span data-stu-id="0087c-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="0087c-191">適用されるフィールドの一覧については、「[ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)」(ForwardedHeaders 列挙型) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="0087c-192">このプロパティに割り当てられる標準値は、`ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="0087c-193">既定値は [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="0087c-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="0087c-194">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="0087c-195">このオプションは、プロキシ/フォワーダーが `X-Forwarded-Host` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0087c-196">既定値は、`X-Forwarded-Host` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="0087c-197">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="0087c-198">このオプションは、プロキシ/フォワーダーが `X-Forwarded-Proto` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0087c-199">既定値は、`X-Forwarded-Proto` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="0087c-200">処理されるヘッダー内のエントリの数を制限します。</span><span class="sxs-lookup"><span data-stu-id="0087c-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="0087c-201">制限を無効にするには `null` に設定しますが、これは `KnownProxies` または `KnownNetworks` が構成されている場合にのみ行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="0087c-202">`null` 以外の値を設定することは、正しく構成されていないプロキシや、ネットワーク上のサイドチャネルから届く悪意のある要求から保護するための予防策となります (ただし保証はできません)。</span><span class="sxs-lookup"><span data-stu-id="0087c-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="0087c-203">Forwarded Headers Middleware では、ヘッダーが右から左の逆の順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="0087c-204">規定値 (`1`) を使う場合は、`ForwardLimit` の値を増やさない限りヘッダーの右端にある値のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="0087c-205">既定値は、`1` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="0087c-206">受け付けるヘッダーの転送元である既知のネットワークのアドレス範囲です。</span><span class="sxs-lookup"><span data-stu-id="0087c-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="0087c-207">クラスレス ドメイン間ルーティング (CIDR) の表記を使って、IP の範囲を指定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="0087c-208">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="0087c-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="0087c-209">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0087c-210">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="0087c-211">既定値は、`IPAddress.Loopback` の単一エントリを含む `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> です。</span><span class="sxs-lookup"><span data-stu-id="0087c-211">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="0087c-212">受け付けるヘッダーの転送元である既知のプロキシのアドレスです。</span><span class="sxs-lookup"><span data-stu-id="0087c-212">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="0087c-213">IP アドレスの正確な一致を指定するには、`KnownProxies` を使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-213">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="0087c-214">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="0087c-214">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="0087c-215">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-215">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0087c-216">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-216">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="0087c-217">既定値は、`IPAddress.IPv6Loopback` の単一エントリを含む `IList`\<<xref:System.Net.IPAddress>> です。</span><span class="sxs-lookup"><span data-stu-id="0087c-217">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="0087c-218">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="0087c-219">既定値は、`X-Original-For` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-219">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="0087c-220">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="0087c-221">既定値は、`X-Original-Host` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-221">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="0087c-222">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="0087c-223">既定値は、`X-Original-Proto` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-223">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="0087c-224">処理対象の [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) の間でヘッダー値の数が同期していることを要求します。</span><span class="sxs-lookup"><span data-stu-id="0087c-224">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="0087c-225">ASP.NET Core 1.x での既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-225">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="0087c-226">ASP.NET Core 2.0 以降での既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-226">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="0087c-227">シナリオとユース ケース</span><span class="sxs-lookup"><span data-stu-id="0087c-227">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="0087c-228">転送されるヘッダーを追加することができず、すべての要求が安全な場合</span><span class="sxs-lookup"><span data-stu-id="0087c-228">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="0087c-229">アプリにプロキシされる要求に、転送されるヘッダーを追加できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-229">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="0087c-230">すべてのパブリック外部要求が HTTPS を使うことをプロキシが強制している場合、すべての種類のミドルウェアを使う前に、`Startup.Configure` でスキームを手動で設定できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-230">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="0087c-231">このコードは、開発環境またはステージング環境の環境変数または他の構成設定によって、無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="0087c-231">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="0087c-232">要求のパスを変更するパス ベースとプロキシを処理する</span><span class="sxs-lookup"><span data-stu-id="0087c-232">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="0087c-233">一部のプロキシでは、パスはそのまま渡されますが、ルーティングが正しく機能するためには削除する必要のあるアプリ ベース パスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0087c-233">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="0087c-234">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ミドルウェアは、パスを [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) と [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) へのアプリ ベース パスに分割します。</span><span class="sxs-lookup"><span data-stu-id="0087c-234">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="0087c-235">`/foo` が `/foo/api/1` として渡されるプロキシ パスのアプリ ベース パスである場合、ミドルウェアは次のコマンドで `Request.PathBase` を `/foo`に、`Request.Path` を `/api/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-235">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="0087c-236">ミドルウェアが逆方向にもう一度呼び出されると、元のパスとパス ベースが再度適用されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-236">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="0087c-237">ミドルウェアの処理の順序について詳しくは、<xref:fundamentals/middleware/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-237">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="0087c-238">プロキシがパスをトリミングする場合は (たとえば、`/foo/api/1` を `/api/1` に転送)、要求の [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) プロパティを設定することによって、リダイレクトとリンクを修正します。</span><span class="sxs-lookup"><span data-stu-id="0087c-238">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="0087c-239">プロキシがパス データを追加している場合は、<xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> を使用して <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> プロパティに割り当てることで、パスの一部を破棄してリダイレクトとリンクを修正します。</span><span class="sxs-lookup"><span data-stu-id="0087c-239">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="0087c-240">別のヘッダー名を使用するプロキシの構成</span><span class="sxs-lookup"><span data-stu-id="0087c-240">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="0087c-241">プロキシがプロキシ アドレス/ポートおよび開始スキーム情報の転送に名前が `X-Forwarded-For` と `X-Forwarded-Proto` のヘッダーを使用しない場合、プロキシで使用されるヘッダー名と一致するように、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> と <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> のオプションを設定してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-241">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="0087c-242">Linux および非 IIS のリバース プロキシのスキームを転送する</span><span class="sxs-lookup"><span data-stu-id="0087c-242">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="0087c-243"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> および <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> を呼び出すアプリが、Azure Linux App Service、Azure Linux 仮想マシン (VM)、または IIS 以外の他のリバース プロキシの背後に展開されている場合、サイトは無限ループに陥ります。</span><span class="sxs-lookup"><span data-stu-id="0087c-243">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="0087c-244">TLS はリバース プロキシによって終了され、Kestrel では正しい要求スキームが認識されません。</span><span class="sxs-lookup"><span data-stu-id="0087c-244">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="0087c-245">OAuth と OIDC もこの構成では正しくないリダイレクトを生成するため、失敗します。</span><span class="sxs-lookup"><span data-stu-id="0087c-245">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="0087c-246"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> は IIS の背後で実行される場合、Forwarded Headers Middleware を追加して構成しますが、Linux 用の対応する自動設定 (Apache または Nginx 統合) はありません。</span><span class="sxs-lookup"><span data-stu-id="0087c-246"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="0087c-247">非 IIS シナリオでプロキシからスキームを転送するには、Forwarded Headers Middleware を追加して構成します。</span><span class="sxs-lookup"><span data-stu-id="0087c-247">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="0087c-248">`Startup.ConfigureServices` で、次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="0087c-248">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="0087c-249">証明書の転送</span><span class="sxs-lookup"><span data-stu-id="0087c-249">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="0087c-250">Azure</span><span class="sxs-lookup"><span data-stu-id="0087c-250">Azure</span></span>

<span data-ttu-id="0087c-251">証明書の転送用に Azure App Service を構成するには、「[Azure App Service に対する TLS 相互認証の構成](/azure/app-service/app-service-web-configure-tls-mutual-auth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-251">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="0087c-252">次のガイダンスは、ASP.NET Core アプリの構成に関するものです。</span><span class="sxs-lookup"><span data-stu-id="0087c-252">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="0087c-253">`Startup.Configure` で、`app.UseAuthentication();` の呼び出しの前に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0087c-253">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="0087c-254">Azure で使用されるヘッダー名を指定するように、証明書転送ミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="0087c-254">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="0087c-255">`Startup.ConfigureServices` に次のコードを追加し、ミドルウェアによる証明書作成の基になるヘッダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="0087c-255">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="0087c-256">他の Web プロキシ</span><span class="sxs-lookup"><span data-stu-id="0087c-256">Other web proxies</span></span>

<span data-ttu-id="0087c-257">使用されているプロキシが、IIS でも、Azure App Service のアプリケーション要求ルーティング処理 (ARR) でもない場合は、HTTP ヘッダーで受け取った証明書を転送するように、プロキシを構成します。</span><span class="sxs-lookup"><span data-stu-id="0087c-257">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="0087c-258">`Startup.Configure` で、`app.UseAuthentication();` の呼び出しの前に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0087c-258">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="0087c-259">ヘッダー名を指定するように証明書の転送ミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="0087c-259">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="0087c-260">`Startup.ConfigureServices` に次のコードを追加し、ミドルウェアによる証明書作成の基になるヘッダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="0087c-260">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="0087c-261">プロキシで証明書が base64 エンコードではない場合は (Nginx の場合と同様)、`HeaderConverter` オプションを設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-261">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="0087c-262">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-262">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="0087c-263">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0087c-263">Troubleshoot</span></span>

<span data-ttu-id="0087c-264">ヘッダーが意図したとおりに転送されない場合は、[ログ](xref:fundamentals/logging/index) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="0087c-264">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0087c-265">ログで問題のトラブルシューティングに十分な情報が提供されない場合は、サーバーが受信した要求ヘッダーを列挙します。</span><span class="sxs-lookup"><span data-stu-id="0087c-265">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="0087c-266">インライン ミドルウェアを使用し、アプリ応答に要求ヘッダーを書き込んだり、ヘッダーをログに記録したりします。</span><span class="sxs-lookup"><span data-stu-id="0087c-266">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="0087c-267">アプリの応答にヘッダーを書き込むには、`Startup.Configure` 内の <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> の呼び出しの直後に、次の端末のインライン ミドルウェアを配置します。</span><span class="sxs-lookup"><span data-stu-id="0087c-267">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="0087c-268">応答本文ではなく、ログに書き込むことができます。</span><span class="sxs-lookup"><span data-stu-id="0087c-268">You can write to logs instead of the response body.</span></span> <span data-ttu-id="0087c-269">ログに書き込むことで、デバッグしている間、サイトは正常に機能できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-269">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="0087c-270">応答本文ではなく、ログに書き込むには:</span><span class="sxs-lookup"><span data-stu-id="0087c-270">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="0087c-271">「[Startup でログを作成する](xref:fundamentals/logging/index#create-logs-in-startup)」に説明されているように、`ILogger<Startup>` を `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0087c-271">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="0087c-272">`Startup.Configure` 内で <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を呼び出した直後に次のインライン ミドルウェアを配置します。</span><span class="sxs-lookup"><span data-stu-id="0087c-272">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="0087c-273">処理時、`X-Forwarded-{For|Proto|Host}` 値は `X-Original-{For|Proto|Host}` に移動されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-273">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="0087c-274">指定されたヘッダーに複数の値がある場合、Forwarded Headers Middleware では右から左の逆の順序でヘッダーが処理されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-274">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="0087c-275">既定の `ForwardLimit` は `1` です。そのため、`ForwardLimit` の値を増やさない限り、ヘッダーの右端にある値のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-275">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="0087c-276">Forwarded Headers が処理される前に、要求の元のリモート IP アドレスが `KnownProxies` リストまたは `KnownNetworks` リストのエントリと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-276">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="0087c-277">これにより、信頼されないプロキシからの転送を受け付けないことで、ヘッダーのスプーフィングを制限します。</span><span class="sxs-lookup"><span data-stu-id="0087c-277">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="0087c-278">不明なプロキシが検出されると、ログにプロキシのアドレスが示されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-278">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="0087c-279">前の例では、10.0.0.100 がプロキシ サーバーです。</span><span class="sxs-lookup"><span data-stu-id="0087c-279">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="0087c-280">サーバーが信頼されているプロキシであれば、`Startup.ConfigureServices` で `KnownProxies` にサーバーの IP アドレスを追加します (あるいは、信頼されているネットワークを `KnownNetworks` に追加します)。</span><span class="sxs-lookup"><span data-stu-id="0087c-280">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0087c-281">詳細については、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-281">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="0087c-282">信頼されているプロキシとネットワークにのみ、ヘッダーの転送を許可します。</span><span class="sxs-lookup"><span data-stu-id="0087c-282">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="0087c-283">それ以外に許可すると、[IP なりすまし](https://www.iplocation.net/ip-spoofing)攻撃が可能になります。</span><span class="sxs-lookup"><span data-stu-id="0087c-283">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0087c-284">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0087c-284">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="0087c-285">Microsoft セキュリティ アドバイザリ CVE-2018-0787:ASP.NET Core の特権の昇格脆弱性</span><span class="sxs-lookup"><span data-stu-id="0087c-285">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0087c-286">ASP.NET Core の推奨される構成では、アプリは IIS/ASP.NET Core モジュール、Nginx、または Apache を使ってホストされます。</span><span class="sxs-lookup"><span data-stu-id="0087c-286">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="0087c-287">プロキシ サーバー、ロード バランサー、および他のネットワーク アプライアンスにより、要求に関する情報が、アプリに到達する前にわからなくなることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="0087c-287">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="0087c-288">HTTPS 要求が HTTP によってプロキシされると、元のスキーム (HTTPS) は失われ、ヘッダーで転送される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-288">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="0087c-289">アプリは、インターネット上または社内ネットワーク上の本来の送信元ではなく、プロキシから要求を受信するため、送信元クライアントの IP アドレスもヘッダーで転送される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-289">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="0087c-290">この情報は、リダイレクト、認証、リンクの生成、ポリシーの評価、クライアントの位置情報など、要求の処理で重要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-290">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="0087c-291">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="0087c-291">Forwarded headers</span></span>

<span data-ttu-id="0087c-292">慣例によりは、プロキシは HTTP ヘッダーで情報を転送します。</span><span class="sxs-lookup"><span data-stu-id="0087c-292">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="0087c-293">Header</span><span class="sxs-lookup"><span data-stu-id="0087c-293">Header</span></span> | <span data-ttu-id="0087c-294">説明</span><span class="sxs-lookup"><span data-stu-id="0087c-294">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="0087c-295">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="0087c-295">X-Forwarded-For</span></span> | <span data-ttu-id="0087c-296">要求を開始したクライアントと、プロキシ チェーン内の後続のプロキシに関する情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="0087c-296">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="0087c-297">このパラメーターは、IP アドレス (および、必要に応じてポート番号) を含むことがあります。</span><span class="sxs-lookup"><span data-stu-id="0087c-297">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="0087c-298">プロキシ サーバーのチェーンにおいては、最初のパラメーターが、要求を最初に行ったクライアントを示します。</span><span class="sxs-lookup"><span data-stu-id="0087c-298">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="0087c-299">その後に、後続のプロキシの識別子が指定されています。</span><span class="sxs-lookup"><span data-stu-id="0087c-299">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="0087c-300">チェーン内の最後のプロキシは、パラメーターのリストには含まれません。</span><span class="sxs-lookup"><span data-stu-id="0087c-300">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="0087c-301">最後のプロキシの IP アドレスとオプションのポート番号は、トランスポート層のリモート IP アドレスとして入手できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-301">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="0087c-302">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="0087c-302">X-Forwarded-Proto</span></span> | <span data-ttu-id="0087c-303">開始スキーム (HTTP/HTTPS) の値です。</span><span class="sxs-lookup"><span data-stu-id="0087c-303">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="0087c-304">要求が複数のプロキシを通過している場合、値はスキームのリストである場合もあります。</span><span class="sxs-lookup"><span data-stu-id="0087c-304">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="0087c-305">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="0087c-305">X-Forwarded-Host</span></span> | <span data-ttu-id="0087c-306">ホスト ヘッダー フィールドの元の値です。</span><span class="sxs-lookup"><span data-stu-id="0087c-306">The original value of the Host header field.</span></span> <span data-ttu-id="0087c-307">通常、プロキシはホスト ヘッダーを変更しません。</span><span class="sxs-lookup"><span data-stu-id="0087c-307">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="0087c-308">プロキシにおいてホスト ヘッダーが既知の適切な値であることが検証されない、または既知の適切な値に制限されないシステムに影響を与える、特権の昇格脆弱性については、[マイクロソフト セキュリティ アドバイザリ CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-308">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="0087c-309">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) パッケージの Forwarded Headers Middleware は、これらのヘッダーを読み取り、<xref:Microsoft.AspNetCore.Http.HttpContext> の関連するフィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="0087c-309">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="0087c-310">ミドルウェアの更新:</span><span class="sxs-lookup"><span data-stu-id="0087c-310">The middleware updates:</span></span>

* <span data-ttu-id="0087c-311">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress):`X-Forwarded-For` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-311">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="0087c-312">追加の設定は、ミドルウェアが `RemoteIpAddress` を設定する方法に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="0087c-312">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="0087c-313">詳しくは、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-313">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="0087c-314">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme):`X-Forwarded-Proto` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-314">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="0087c-315">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host):`X-Forwarded-Host` ヘッダー値を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-315">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="0087c-316">Forwarded Headers Middleware の[既定の設定](#forwarded-headers-middleware-options)は構成できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-316">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="0087c-317">既定の設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0087c-317">The default settings are:</span></span>

* <span data-ttu-id="0087c-318">アプリと要求のソースの間には、"*1 つのプロキシ*" だけが存在します。</span><span class="sxs-lookup"><span data-stu-id="0087c-318">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="0087c-319">既知のプロキシと既知のネットワークに対しては、ループバック アドレスのみが構成されています。</span><span class="sxs-lookup"><span data-stu-id="0087c-319">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="0087c-320">転送されるヘッダーには `X-Forwarded-For` と `X-Forwarded-Proto` の名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="0087c-320">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="0087c-321">すべてのネットワーク アプライアンスが追加構成なしで `X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを追加するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="0087c-321">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="0087c-322">プロキシされた要求がアプリに届いたときにこれらのヘッダーが含まれていない場合は、アプライアンスの製造元のガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-322">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="0087c-323">アプライアンスが `X-Forwarded-For` と `X-Forwarded-Proto` 以外の名前を使用している場合は、アプライアンスで使用されるヘッダー名と一致するように <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> と <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> のオプションを設定してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-323">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="0087c-324">詳細については、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」と「[別のヘッダー名を使用するプロキシの構成](#configuration-for-a-proxy-that-uses-different-header-names)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-324">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="0087c-325">IIS/IIS Express と ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="0087c-325">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="0087c-326">アプリが IIS と ASP.NET Core モジュールの背後で[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホストされている場合、[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) によって Forwarded Headers Middleware が既定で有効にされます。</span><span class="sxs-lookup"><span data-stu-id="0087c-326">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="0087c-327">転送されるヘッダーの信頼に関する問題のため (たとえば、[IP スプーフィング](https://www.iplocation.net/ip-spoofing))、Forwarded Headers Middleware はアクティブ化されると最初に、ASP.NET Core モジュールに固有の制限された構成を使って、ミドルウェア パイプライン内で実行します。</span><span class="sxs-lookup"><span data-stu-id="0087c-327">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="0087c-328">ミドルウェアは、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送するように構成され、単一の localhost プロキシに制限されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-328">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="0087c-329">追加の構成が必要な場合は、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-329">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0087c-330">プロキシ サーバーとロード バランサーの他のシナリオ</span><span class="sxs-lookup"><span data-stu-id="0087c-330">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0087c-331">[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホストされている場合に [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) が使われていない場合は、Forwarded Headers Middleware は既定で有効になりません。</span><span class="sxs-lookup"><span data-stu-id="0087c-331">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="0087c-332"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を含む転送されたヘッダーをアプリで処理するためには、Forwarded Headers Middleware を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-332">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="0087c-333">ミドルウェアを有効にした後、ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合の既定の [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) は [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="0087c-333">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="0087c-334"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> でミドルウェアを構成して、`Startup.ConfigureServices` で `X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。</span><span class="sxs-lookup"><span data-stu-id="0087c-334">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0087c-335">他のミドルウェアを呼び出す前に、`Startup.Configure` 内で <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0087c-335">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="0087c-336"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が `Startup.ConfigureServices` において指定されていない場合、または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を使って拡張メソッドに直接渡されない場合、転送される既定のヘッダーは [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="0087c-336">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="0087c-337">転送するヘッダーで <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> プロパティが構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-337">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="0087c-338">Nginx の構成</span><span class="sxs-lookup"><span data-stu-id="0087c-338">Nginx configuration</span></span>

<span data-ttu-id="0087c-339">`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送する場合は、<xref:host-and-deploy/linux-nginx#configure-nginx> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-339">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="0087c-340">詳細については、「[NGINX:Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)」 (NGINX: 転送されるヘッダーの使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-340">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="0087c-341">Apache の構成</span><span class="sxs-lookup"><span data-stu-id="0087c-341">Apache configuration</span></span>

<span data-ttu-id="0087c-342">`X-Forwarded-For` は自動的に追加されます (「[Apache Module mod_proxy:Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)」 (Apache モジュール mod_proxy: リバース プロキシがヘッダーを要求する) を参照)。</span><span class="sxs-lookup"><span data-stu-id="0087c-342">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="0087c-343">`X-Forwarded-Proto` ヘッダーを転送する方法については、<xref:host-and-deploy/linux-apache#configure-apache> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-343">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="0087c-344">Forwarded Headers Middleware のオプション</span><span class="sxs-lookup"><span data-stu-id="0087c-344">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="0087c-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> は、Forwarded Headers Middleware の動作を制御します。</span><span class="sxs-lookup"><span data-stu-id="0087c-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="0087c-346">既定の値の変更例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0087c-346">The following example changes the default values:</span></span>

* <span data-ttu-id="0087c-347">転送されるヘッダーのエントリ数を `2` に制限します。</span><span class="sxs-lookup"><span data-stu-id="0087c-347">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="0087c-348">`127.0.10.1` の既知のプロキシ アドレスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0087c-348">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="0087c-349">転送されるヘッダーの名前を既定の `X-Forwarded-For` から `X-Forwarded-For-My-Custom-Header-Name` に変更します。</span><span class="sxs-lookup"><span data-stu-id="0087c-349">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="0087c-350">オプション</span><span class="sxs-lookup"><span data-stu-id="0087c-350">Option</span></span> | <span data-ttu-id="0087c-351">説明</span><span class="sxs-lookup"><span data-stu-id="0087c-351">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="0087c-352">`X-Forwarded-Host` ヘッダーによるホストを指定した値に制限します。</span><span class="sxs-lookup"><span data-stu-id="0087c-352">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="0087c-353">値は、大文字と小文字を無視して序数で比較されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-353">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="0087c-354">ポート番号は除外されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-354">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="0087c-355">リストが空の場合、すべてのホストが許可されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-355">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="0087c-356">最上位のワイルドカード `*` は、すべての空でないホストを許可します。</span><span class="sxs-lookup"><span data-stu-id="0087c-356">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="0087c-357">サブドメインのワイルドカードは許可されますが、ルート ドメインとは一致しません。</span><span class="sxs-lookup"><span data-stu-id="0087c-357">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="0087c-358">たとえば、`*.contoso.com` はサブドメイン `foo.contoso.com` と一致しますが、ルート ドメイン `contoso.com` とは一致しません。</span><span class="sxs-lookup"><span data-stu-id="0087c-358">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="0087c-359">Unicode のホスト名は許可されますが、一致のために [Punycode](https://tools.ietf.org/html/rfc3492) に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-359">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="0087c-360">[IPv6 アドレス](https://tools.ietf.org/html/rfc4291)は境界の角かっこを含む必要があり、[従来の形式](https://tools.ietf.org/html/rfc4291#section-2.2) (例: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`) になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-360">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="0087c-361">IPv6 アドレスは、異なる形式間で論理的な等価性を調べるために特別な大文字小文字にはされず、正規化は行われません。</span><span class="sxs-lookup"><span data-stu-id="0087c-361">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="0087c-362">許可されるホストの制限に失敗すると、サービスによって生成されたリンクを攻撃者が偽装する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-362">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="0087c-363">既定値は空の `IList<string>` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-363">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="0087c-364">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-364">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="0087c-365">このオプションは、プロキシ/フォワーダーが `X-Forwarded-For` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-365">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0087c-366">既定値は、`X-Forwarded-For` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-366">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="0087c-367">処理する必要があるフォワーダーを識別します。</span><span class="sxs-lookup"><span data-stu-id="0087c-367">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="0087c-368">適用されるフィールドの一覧については、「[ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)」(ForwardedHeaders 列挙型) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-368">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="0087c-369">このプロパティに割り当てられる標準値は、`ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-369">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="0087c-370">既定値は [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) です。</span><span class="sxs-lookup"><span data-stu-id="0087c-370">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="0087c-371">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-371">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="0087c-372">このオプションは、プロキシ/フォワーダーが `X-Forwarded-Host` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-372">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0087c-373">既定値は、`X-Forwarded-Host` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-373">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="0087c-374">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-374">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="0087c-375">このオプションは、プロキシ/フォワーダーが `X-Forwarded-Proto` ヘッダーを使用していないが、情報の転送のためにその他のヘッダーを使用している場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-375">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="0087c-376">既定値は、`X-Forwarded-Proto` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-376">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="0087c-377">処理されるヘッダー内のエントリの数を制限します。</span><span class="sxs-lookup"><span data-stu-id="0087c-377">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="0087c-378">制限を無効にするには `null` に設定しますが、これは `KnownProxies` または `KnownNetworks` が構成されている場合にのみ行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-378">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="0087c-379">`null` 以外の値を設定することは、正しく構成されていないプロキシや、ネットワーク上のサイドチャネルから届く悪意のある要求から保護するための予防策となります (ただし保証はできません)。</span><span class="sxs-lookup"><span data-stu-id="0087c-379">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="0087c-380">Forwarded Headers Middleware では、ヘッダーが右から左の逆の順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-380">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="0087c-381">規定値 (`1`) を使う場合は、`ForwardLimit` の値を増やさない限りヘッダーの右端にある値のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-381">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="0087c-382">既定値は、`1` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-382">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="0087c-383">受け付けるヘッダーの転送元である既知のネットワークのアドレス範囲です。</span><span class="sxs-lookup"><span data-stu-id="0087c-383">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="0087c-384">クラスレス ドメイン間ルーティング (CIDR) の表記を使って、IP の範囲を指定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-384">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="0087c-385">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="0087c-385">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="0087c-386">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-386">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0087c-387">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-387">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="0087c-388">詳細については、「[IPv6 アドレスとして表される IPv4 アドレスの構成](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-388">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="0087c-389">既定値は、`IPAddress.Loopback` の単一エントリを含む `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> です。</span><span class="sxs-lookup"><span data-stu-id="0087c-389">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="0087c-390">受け付けるヘッダーの転送元である既知のプロキシのアドレスです。</span><span class="sxs-lookup"><span data-stu-id="0087c-390">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="0087c-391">IP アドレスの正確な一致を指定するには、`KnownProxies` を使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-391">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="0087c-392">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="0087c-392">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="0087c-393">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-393">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0087c-394">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-394">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="0087c-395">詳細については、「[IPv6 アドレスとして表される IPv4 アドレスの構成](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-395">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="0087c-396">既定値は、`IPAddress.IPv6Loopback` の単一エントリを含む `IList`\<<xref:System.Net.IPAddress>> です。</span><span class="sxs-lookup"><span data-stu-id="0087c-396">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="0087c-397">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-397">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="0087c-398">既定値は、`X-Original-For` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-398">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="0087c-399">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-399">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="0087c-400">既定値は、`X-Original-Host` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-400">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="0087c-401">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) によって指定されているヘッダーではなく、このプロパティによって指定されているヘッダーを使います。</span><span class="sxs-lookup"><span data-stu-id="0087c-401">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="0087c-402">既定値は、`X-Original-Proto` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-402">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="0087c-403">処理対象の [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) の間でヘッダー値の数が同期していることを要求します。</span><span class="sxs-lookup"><span data-stu-id="0087c-403">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="0087c-404">ASP.NET Core 1.x での既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-404">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="0087c-405">ASP.NET Core 2.0 以降での既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="0087c-405">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="0087c-406">シナリオとユース ケース</span><span class="sxs-lookup"><span data-stu-id="0087c-406">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="0087c-407">転送されるヘッダーを追加することができず、すべての要求が安全な場合</span><span class="sxs-lookup"><span data-stu-id="0087c-407">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="0087c-408">アプリにプロキシされる要求に、転送されるヘッダーを追加できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-408">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="0087c-409">すべてのパブリック外部要求が HTTPS を使うことをプロキシが強制している場合、すべての種類のミドルウェアを使う前に、`Startup.Configure` でスキームを手動で設定できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-409">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="0087c-410">このコードは、開発環境またはステージング環境の環境変数または他の構成設定によって、無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="0087c-410">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="0087c-411">要求のパスを変更するパス ベースとプロキシを処理する</span><span class="sxs-lookup"><span data-stu-id="0087c-411">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="0087c-412">一部のプロキシでは、パスはそのまま渡されますが、ルーティングが正しく機能するためには削除する必要のあるアプリ ベース パスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0087c-412">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="0087c-413">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ミドルウェアは、パスを [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) と [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) へのアプリ ベース パスに分割します。</span><span class="sxs-lookup"><span data-stu-id="0087c-413">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="0087c-414">`/foo` が `/foo/api/1` として渡されるプロキシ パスのアプリ ベース パスである場合、ミドルウェアは次のコマンドで `Request.PathBase` を `/foo`に、`Request.Path` を `/api/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0087c-414">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="0087c-415">ミドルウェアが逆方向にもう一度呼び出されると、元のパスとパス ベースが再度適用されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-415">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="0087c-416">ミドルウェアの処理の順序について詳しくは、<xref:fundamentals/middleware/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-416">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="0087c-417">プロキシがパスをトリミングする場合は (たとえば、`/foo/api/1` を `/api/1` に転送)、要求の [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) プロパティを設定することによって、リダイレクトとリンクを修正します。</span><span class="sxs-lookup"><span data-stu-id="0087c-417">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="0087c-418">プロキシがパス データを追加している場合は、<xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> を使用して <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> プロパティに割り当てることで、パスの一部を破棄してリダイレクトとリンクを修正します。</span><span class="sxs-lookup"><span data-stu-id="0087c-418">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="0087c-419">別のヘッダー名を使用するプロキシの構成</span><span class="sxs-lookup"><span data-stu-id="0087c-419">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="0087c-420">プロキシがプロキシ アドレス/ポートおよび開始スキーム情報の転送に名前が `X-Forwarded-For` と `X-Forwarded-Proto` のヘッダーを使用しない場合、プロキシで使用されるヘッダー名と一致するように、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> と <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> のオプションを設定してください。</span><span class="sxs-lookup"><span data-stu-id="0087c-420">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="0087c-421">IPv6 アドレスとして表される IPv4 アドレスの構成</span><span class="sxs-lookup"><span data-stu-id="0087c-421">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="0087c-422">サーバーがデュアル モードのソケットを使用している場合、IPv4 アドレスは IPv6 形式で提供されます (たとえば、IPv4 での `10.0.0.1` は IPv6 で `::ffff:10.0.0.1` または `::ffff:a00:1` と表現されます)。</span><span class="sxs-lookup"><span data-stu-id="0087c-422">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="0087c-423">「[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-423">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="0087c-424">この形式が必要かどうかを判断するには、「[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-424">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="0087c-425">次の例では、転送されるヘッダーを提供するネットワーク アドレスを、IPv6 形式の `KnownNetworks` リストに追加します。</span><span class="sxs-lookup"><span data-stu-id="0087c-425">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="0087c-426">IPv4 アドレス: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="0087c-426">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="0087c-427">変換された IPv6 アドレス: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="0087c-427">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="0087c-428">変換されたプレフィックス長:104</span><span class="sxs-lookup"><span data-stu-id="0087c-428">Converted prefix length: 104</span></span>

<span data-ttu-id="0087c-429">16 進数形式でアドレスを指定することもできます (`10.11.12.1` は IPv6 で `::ffff:0a0b:0c01` として表されます)。</span><span class="sxs-lookup"><span data-stu-id="0087c-429">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="0087c-430">IPv4 アドレスを IPv6 に変換するときは、CIDR プレフィックス長 (例では `8`) に 96 を足して、追加の `::ffff:` IPv6 プレフィックスを構成します (8 + 96 = 104)。</span><span class="sxs-lookup"><span data-stu-id="0087c-430">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="0087c-431">Linux および非 IIS のリバース プロキシのスキームを転送する</span><span class="sxs-lookup"><span data-stu-id="0087c-431">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="0087c-432"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> および <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> を呼び出すアプリが、Azure Linux App Service、Azure Linux 仮想マシン (VM)、または IIS 以外の他のリバース プロキシの背後に展開されている場合、サイトは無限ループに陥ります。</span><span class="sxs-lookup"><span data-stu-id="0087c-432">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="0087c-433">TLS はリバース プロキシによって終了され、Kestrel では正しい要求スキームが認識されません。</span><span class="sxs-lookup"><span data-stu-id="0087c-433">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="0087c-434">OAuth と OIDC もこの構成では正しくないリダイレクトを生成するため、失敗します。</span><span class="sxs-lookup"><span data-stu-id="0087c-434">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="0087c-435"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> は IIS の背後で実行される場合、Forwarded Headers Middleware を追加して構成しますが、Linux 用の対応する自動設定 (Apache または Nginx 統合) はありません。</span><span class="sxs-lookup"><span data-stu-id="0087c-435"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="0087c-436">非 IIS シナリオでプロキシからスキームを転送するには、Forwarded Headers Middleware を追加して構成します。</span><span class="sxs-lookup"><span data-stu-id="0087c-436">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="0087c-437">`Startup.ConfigureServices` で、次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="0087c-437">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="0087c-438">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0087c-438">Troubleshoot</span></span>

<span data-ttu-id="0087c-439">ヘッダーが意図したとおりに転送されない場合は、[ログ](xref:fundamentals/logging/index) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="0087c-439">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0087c-440">ログで問題のトラブルシューティングに十分な情報が提供されない場合は、サーバーが受信した要求ヘッダーを列挙します。</span><span class="sxs-lookup"><span data-stu-id="0087c-440">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="0087c-441">インライン ミドルウェアを使用し、アプリ応答に要求ヘッダーを書き込んだり、ヘッダーをログに記録したりします。</span><span class="sxs-lookup"><span data-stu-id="0087c-441">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="0087c-442">アプリの応答にヘッダーを書き込むには、`Startup.Configure` 内の <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> の呼び出しの直後に、次の端末のインライン ミドルウェアを配置します。</span><span class="sxs-lookup"><span data-stu-id="0087c-442">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="0087c-443">応答本文ではなく、ログに書き込むことができます。</span><span class="sxs-lookup"><span data-stu-id="0087c-443">You can write to logs instead of the response body.</span></span> <span data-ttu-id="0087c-444">ログに書き込むことで、デバッグしている間、サイトは正常に機能できます。</span><span class="sxs-lookup"><span data-stu-id="0087c-444">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="0087c-445">応答本文ではなく、ログに書き込むには:</span><span class="sxs-lookup"><span data-stu-id="0087c-445">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="0087c-446">「[Startup でログを作成する](xref:fundamentals/logging/index#create-logs-in-startup)」に説明されているように、`ILogger<Startup>` を `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0087c-446">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="0087c-447">`Startup.Configure` 内で <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> を呼び出した直後に次のインライン ミドルウェアを配置します。</span><span class="sxs-lookup"><span data-stu-id="0087c-447">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="0087c-448">処理時、`X-Forwarded-{For|Proto|Host}` 値は `X-Original-{For|Proto|Host}` に移動されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-448">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="0087c-449">指定されたヘッダーに複数の値がある場合、Forwarded Headers Middleware では右から左の逆の順序でヘッダーが処理されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-449">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="0087c-450">既定の `ForwardLimit` は `1` です。そのため、`ForwardLimit` の値を増やさない限り、ヘッダーの右端にある値のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-450">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="0087c-451">Forwarded Headers が処理される前に、要求の元のリモート IP アドレスが `KnownProxies` リストまたは `KnownNetworks` リストのエントリと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0087c-451">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="0087c-452">これにより、信頼されないプロキシからの転送を受け付けないことで、ヘッダーのスプーフィングを制限します。</span><span class="sxs-lookup"><span data-stu-id="0087c-452">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="0087c-453">不明なプロキシが検出されると、ログにプロキシのアドレスが示されます。</span><span class="sxs-lookup"><span data-stu-id="0087c-453">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="0087c-454">前の例では、10.0.0.100 がプロキシ サーバーです。</span><span class="sxs-lookup"><span data-stu-id="0087c-454">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="0087c-455">サーバーが信頼されているプロキシであれば、`Startup.ConfigureServices` で `KnownProxies` にサーバーの IP アドレスを追加します (あるいは、信頼されているネットワークを `KnownNetworks` に追加します)。</span><span class="sxs-lookup"><span data-stu-id="0087c-455">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0087c-456">詳細については、「[Forwarded Headers Middleware のオプション](#forwarded-headers-middleware-options)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0087c-456">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="0087c-457">信頼されているプロキシとネットワークにのみ、ヘッダーの転送を許可します。</span><span class="sxs-lookup"><span data-stu-id="0087c-457">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="0087c-458">それ以外に許可すると、[IP なりすまし](https://www.iplocation.net/ip-spoofing)攻撃が可能になります。</span><span class="sxs-lookup"><span data-stu-id="0087c-458">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0087c-459">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0087c-459">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="0087c-460">Microsoft セキュリティ アドバイザリ CVE-2018-0787:ASP.NET Core の特権の昇格脆弱性</span><span class="sxs-lookup"><span data-stu-id="0087c-460">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

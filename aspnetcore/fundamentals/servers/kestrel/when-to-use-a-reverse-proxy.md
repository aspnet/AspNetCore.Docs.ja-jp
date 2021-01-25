---
title: ASP.NET Core Kestrel Web サーバーでリバース プロキシを使用するタイミング
author: rick-anderson
description: ASP.NET Core 向けのクロスプラットフォーム Web サーバーである Kestrel の前で、リバース プロキシを使用するタイミングについて学習します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253840"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="21e12-103">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="21e12-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="21e12-104">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの *リバース プロキシ サーバー* と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="21e12-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="21e12-105">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="21e12-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="21e12-106">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="21e12-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](_static/kestrel-to-internet2.png)

<span data-ttu-id="21e12-108">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="21e12-108">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](_static/kestrel-to-internet.png)

<span data-ttu-id="21e12-110">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="21e12-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="21e12-111">リバース プロキシ サーバーのないエッジ サーバーとして Kestrel を使用する場合、複数のプロセス間で同じ IP アドレスとポートを共有することはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="21e12-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="21e12-112">あるポートをリッスンするように Kestrel を構成する場合は、要求の `Host` ヘッダーに関係なく、Kestrel によってそのポートに対するすべてのトラフィックが処理されます。</span><span class="sxs-lookup"><span data-stu-id="21e12-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="21e12-113">ポートを共有できるリバース プロキシを使用すると、一意の IP とポート上で Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="21e12-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="21e12-114">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="21e12-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="21e12-115">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="21e12-115">A reverse proxy:</span></span>

* <span data-ttu-id="21e12-116">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="21e12-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="21e12-117">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="21e12-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="21e12-118">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="21e12-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="21e12-119">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="21e12-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="21e12-120">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="21e12-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="21e12-121">リバース プロキシ構成でのホストには[ホストのフィルター処理](xref:fundamentals/servers/kestrel/host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="21e12-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21e12-122">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="21e12-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>


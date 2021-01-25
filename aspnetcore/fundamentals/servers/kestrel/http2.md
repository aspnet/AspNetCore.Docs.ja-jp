---
title: ASP.NET Core Kestrel Web サーバーで HTTP/2 を使用する
author: rick-anderson
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel での HTTP/2 の使用について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253872"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="66abc-103">ASP.NET Core Kestrel Web サーバーで HTTP/2 を使用する</span><span class="sxs-lookup"><span data-stu-id="66abc-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="66abc-104">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="66abc-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="66abc-105">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="66abc-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="66abc-106">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="66abc-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="66abc-107">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="66abc-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="66abc-108">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="66abc-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="66abc-109">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="66abc-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="66abc-110">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="66abc-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="66abc-111">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="66abc-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="66abc-112">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="66abc-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="66abc-113">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="66abc-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="66abc-114">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="66abc-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="66abc-115">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="66abc-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="66abc-116">.NET Core 3.0 以降では、HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="66abc-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="66abc-117">構成の詳細については、[Kestrel HTTP/2 の制限](xref:fundamentals/servers/kestrel/options#http2-limits)に関するセクションと、「[ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="66abc-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="66abc-118">高度な HTTP/2 機能</span><span class="sxs-lookup"><span data-stu-id="66abc-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="66abc-119">Kestrel に追加された HTTP/2 機能により、gRPC がサポートされています。これには、応答トレーラーのサポートや、リセット フレームの送信のサポートが含まれます。</span><span class="sxs-lookup"><span data-stu-id="66abc-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="66abc-120">予告編</span><span class="sxs-lookup"><span data-stu-id="66abc-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="66abc-121">Reset</span><span class="sxs-lookup"><span data-stu-id="66abc-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

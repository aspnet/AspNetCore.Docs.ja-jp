---
title: ASP.NET Core での Web サーバーの実装
author: rick-anderson
description: ASP.NET Core の Web サーバー Kestrel と HTTP.sys を検出します。 サーバーを選択する方法と、リバース プロキシ サーバーを使用するタイミングについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: a49388c73f2ec0ea03b35dbba1575ce70a4e1512
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238335"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="c21b0-104">ASP.NET Core での Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="c21b0-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="c21b0-105">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)、[Stephen Halter](https://twitter.com/halter73)、[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="c21b0-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="c21b0-106">ASP.NET Core アプリは、インプロセス HTTP サーバー実装を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="c21b0-107">サーバーの実装では、HTTP 要求がリッスンされ、<xref:Microsoft.AspNetCore.Http.HttpContext> に構成された[要求機能](xref:fundamentals/request-features)のセットとしてアプリに公開されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="c21b0-108">Windows</span><span class="sxs-lookup"><span data-stu-id="c21b0-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="c21b0-109">ASP.NET Core には次のものが付属しています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="c21b0-110">[Kestrel サーバー](xref:fundamentals/servers/kestrel)は、クロスプラットフォーム HTTP サーバーの既定の実装です。</span><span class="sxs-lookup"><span data-stu-id="c21b0-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="c21b0-111">Kestrel を使用すると、最高のパフォーマンスとメモリ使用率が提供されますが、HTTP.sys の高度な機能の一部は提供されません。</span><span class="sxs-lookup"><span data-stu-id="c21b0-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="c21b0-112">詳細については、このドキュメントの「[Kestrel と HTTP.sys](#korh)」(次のセクション) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-112">For more information, see [Kestrel vs. HTTP.sys](#korh) in the next section.</span></span>
* <span data-ttu-id="c21b0-113">IIS HTTP サーバーは、IIS 用の[インプロセス サーバー](#hosting-models)です。</span><span class="sxs-lookup"><span data-stu-id="c21b0-113">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="c21b0-114">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys)は、[HTTP.sys カーネル ドライバーおよび HTTP サーバー API](/windows/desktop/Http/http-api-start-page) に基づく Windows 専用の HTTP サーバーです。</span><span class="sxs-lookup"><span data-stu-id="c21b0-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="c21b0-115">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは次のどちらかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-115">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="c21b0-116">IIS HTTP サーバーを使用して IIS ワーカー プロセスと同じプロセス内 ([インプロセス ホスティング モデル](#hosting-models))。</span><span class="sxs-lookup"><span data-stu-id="c21b0-116">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="c21b0-117">"*インプロセス*" が推奨される構成です。</span><span class="sxs-lookup"><span data-stu-id="c21b0-117">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="c21b0-118">[Kestrel サーバー](#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内 ([プロセス外ホスティング モデル](#hosting-models))。</span><span class="sxs-lookup"><span data-stu-id="c21b0-118">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="c21b0-119">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はネイティブの IIS モジュールであり、IIS とインプロセス IIS HTTP サーバーまたは Kestrel の間のネイティブ IIS 要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-119">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="c21b0-120">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-120">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a><span data-ttu-id="c21b0-121">Kestrel と HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c21b0-121">Kestrel vs. HTTP.sys</span></span>

<span data-ttu-id="c21b0-122">Kestrel は、次のような点が HTTP.sys より優れています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-122">Kestrel has the following advantages over HTTP.sys:</span></span>

  * <span data-ttu-id="c21b0-123">よりよいパフォーマンスとメモリ使用率</span><span class="sxs-lookup"><span data-stu-id="c21b0-123">Better performance and memory utilization.</span></span>
  * <span data-ttu-id="c21b0-124">クロス プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="c21b0-124">Cross platform</span></span>
  * <span data-ttu-id="c21b0-125">機敏性。OS から独立して開発され、パッチが適用されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-125">Agility, it's developed and patched independent of the OS.</span></span>
  * <span data-ttu-id="c21b0-126">プログラムによるポートと TLS の構成</span><span class="sxs-lookup"><span data-stu-id="c21b0-126">Programmatic port and TLS configuration</span></span>
  * <span data-ttu-id="c21b0-127">[PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) や代替トランスポートなどのプロトコルを使用できる拡張性。</span><span class="sxs-lookup"><span data-stu-id="c21b0-127">Extensibility that allows for protocols like [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) and alternate transports.</span></span>

<span data-ttu-id="c21b0-128">HTTP.sys は、kestrel にはない次の機能を備えた共有カーネル モード コンポーネントとして動作します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-128">Http.Sys operates as a shared kernel mode component with the following features that kestrel does not have:</span></span>

  * <span data-ttu-id="c21b0-129">ポート共有</span><span class="sxs-lookup"><span data-stu-id="c21b0-129">Port sharing</span></span>
  * <span data-ttu-id="c21b0-130">カーネル モードの Windows 認証。</span><span class="sxs-lookup"><span data-stu-id="c21b0-130">Kernel mode windows authentication.</span></span> <span data-ttu-id="c21b0-131">[Kestrel でサポートされているのは、ユーザー モード認証のみです](xref:security/authentication/windowsauth#kestrel)。</span><span class="sxs-lookup"><span data-stu-id="c21b0-131">[Kestrel supports only user-mode authentication](xref:security/authentication/windowsauth#kestrel).</span></span>
  * <span data-ttu-id="c21b0-132">キュー転送による高速プロキシ</span><span class="sxs-lookup"><span data-stu-id="c21b0-132">Fast proxying via queue transfers</span></span>
  * <span data-ttu-id="c21b0-133">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="c21b0-133">Direct file transmission</span></span>
  * <span data-ttu-id="c21b0-134">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="c21b0-134">Response caching</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c21b0-135">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="c21b0-135">Hosting models</span></span>

<span data-ttu-id="c21b0-136">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-136">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="c21b0-137">インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。</span><span class="sxs-lookup"><span data-stu-id="c21b0-137">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="c21b0-138">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-138">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c21b0-139">アウト プロセス ホスティングでは ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行され、プロセス管理はモジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-139">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="c21b0-140">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-140">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c21b0-141">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="c21b0-141">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c21b0-142">詳細および構成のガイダンスについては、次のトピックを参照してください:</span><span class="sxs-lookup"><span data-stu-id="c21b0-142">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="c21b0-143">macOS</span><span class="sxs-lookup"><span data-stu-id="c21b0-143">macOS</span></span>](#tab/macos)

<span data-ttu-id="c21b0-144">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-144">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="c21b0-145">Linux</span><span class="sxs-lookup"><span data-stu-id="c21b0-145">Linux</span></span>](#tab/linux)

<span data-ttu-id="c21b0-146">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-146">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="c21b0-147">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c21b0-147">Kestrel</span></span>

 <span data-ttu-id="c21b0-148">[Kestrel サーバー](xref:fundamentals/servers/kestrel)は、クロスプラットフォーム HTTP サーバーの既定の実装です。</span><span class="sxs-lookup"><span data-stu-id="c21b0-148">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="c21b0-149">Kestrel を使用すると、最高のパフォーマンスとメモリ使用率が提供されますが、HTTP.sys の高度な機能の一部は提供されません。</span><span class="sxs-lookup"><span data-stu-id="c21b0-149">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="c21b0-150">詳細については、このドキュメントの「[Kestrel と HTTP.sys](#korh)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-150">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>

<span data-ttu-id="c21b0-151">Kestrel を使用する:</span><span class="sxs-lookup"><span data-stu-id="c21b0-151">Use Kestrel:</span></span>

* <span data-ttu-id="c21b0-152">これ自体で、インターネットを含むネットワークから直接要求を処理するエッジ サーバーとして。</span><span class="sxs-lookup"><span data-stu-id="c21b0-152">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="c21b0-154">[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの *リバース プロキシ サーバー* と共に。</span><span class="sxs-lookup"><span data-stu-id="c21b0-154">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c21b0-155">リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-155">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c21b0-157">&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、いずれのホスティング構成もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-157">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="c21b0-158">Kestrel の構成ガイダンスおよびリバース プロキシ構成で Kestrel を使用するときの情報については、「<xref:fundamentals/servers/kestrel>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-158">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="c21b0-159">Windows</span><span class="sxs-lookup"><span data-stu-id="c21b0-159">Windows</span></span>](#tab/windows)

<span data-ttu-id="c21b0-160">ASP.NET Core には次のものが付属しています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-160">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="c21b0-161">[Kestrel サーバー](xref:fundamentals/servers/kestrel)は、既定のクロスプラットフォーム HTTP サーバーです。</span><span class="sxs-lookup"><span data-stu-id="c21b0-161">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="c21b0-162">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys)は、[HTTP.sys カーネル ドライバーおよび HTTP サーバー API](/windows/desktop/Http/http-api-start-page) に基づく Windows 専用の HTTP サーバーです。</span><span class="sxs-lookup"><span data-stu-id="c21b0-162">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="c21b0-163">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは [Kestrel サーバー](#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内で実行されます ("*プロセス外*")。</span><span class="sxs-lookup"><span data-stu-id="c21b0-163">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="c21b0-164">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理はモジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-164">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="c21b0-165">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-165">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c21b0-166">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="c21b0-166">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c21b0-167">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-167">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core モジュール](_static/ancm-outofprocess.png)

<span data-ttu-id="c21b0-169">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-169">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c21b0-170">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="c21b0-170">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c21b0-171">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-171">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="c21b0-172">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-172">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="c21b0-173">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-173">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c21b0-174">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-174">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c21b0-175">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-175">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c21b0-176">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-176">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c21b0-177">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-177">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c21b0-178">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-178">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c21b0-179">IIS と ASP.NET Core モジュールの構成のガイダンスについては、次のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-179">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="c21b0-180">macOS</span><span class="sxs-lookup"><span data-stu-id="c21b0-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="c21b0-181">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-181">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="c21b0-182">Linux</span><span class="sxs-lookup"><span data-stu-id="c21b0-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="c21b0-183">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="c21b0-184">Nginx と Kestrel</span><span class="sxs-lookup"><span data-stu-id="c21b0-184">Nginx with Kestrel</span></span>

<span data-ttu-id="c21b0-185">Kestrel のリバース プロキシ サーバーとして Linux で Nginx を使用する方法については、<xref:host-and-deploy/linux-nginx> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-185">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="c21b0-186">Apache と Kestrel</span><span class="sxs-lookup"><span data-stu-id="c21b0-186">Apache with Kestrel</span></span>

<span data-ttu-id="c21b0-187">Kestrel のリバース プロキシ サーバーとして Linux で Apache を使用する方法については、<xref:host-and-deploy/linux-apache> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-187">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="c21b0-188">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c21b0-188">HTTP.sys</span></span>

<span data-ttu-id="c21b0-189">Windows で ASP.NET Core アプリを実行する場合は、HTTP.sys を Kestrel の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-189">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="c21b0-190">Kestrel で使用できない機能がアプリに必要な場合を除き、HTTP.sys より Kestrel をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c21b0-190">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="c21b0-191">詳細については、「<xref:fundamentals/servers/httpsys>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-191">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="c21b0-193">HTTP.sys は、内部ネットワークにのみ公開されるアプリにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-193">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="c21b0-195">HTTP.sys の構成のガイダンスについては、「<xref:fundamentals/servers/httpsys>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-195">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="c21b0-196">ASP.NET Core サーバー インフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="c21b0-196">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="c21b0-197">`Startup.Configure` メソッドで使用可能な <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> は、<xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 型の <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-197">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="c21b0-198">Kestrel および HTTP.sys は、それぞれ単独の機能 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> のみを公開しますが、サーバーの実装が異なると追加機能が公開される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c21b0-198">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="c21b0-199">`IServerAddressesFeature` を使用すれば、実行時にサーバー実装がバインドされたポートを見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-199">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="c21b0-200">カスタム サーバー</span><span class="sxs-lookup"><span data-stu-id="c21b0-200">Custom servers</span></span>

<span data-ttu-id="c21b0-201">組み込みサーバーがアプリの要件に合わない場合は、カスタム サーバー実装を作成できます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-201">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="c21b0-202">[Nowin](https://github.com/Bobris/Nowin) ベースの <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 実装の作成方法については、[Open Web Interface for .NET (OWIN) のガイド](xref:fundamentals/owin)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-202">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="c21b0-203">実装を必要とするのは、アプリで使用される機能のインターフェイスのみです。ただし、少なくとも <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> と <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> はサポートされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c21b0-203">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="c21b0-204">サーバーの起動</span><span class="sxs-lookup"><span data-stu-id="c21b0-204">Server startup</span></span>

<span data-ttu-id="c21b0-205">統合開発環境 (IDE) またはエディターでアプリが開始されると、サーバーが起動されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-205">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="c21b0-206">[Visual Studio](https://visualstudio.microsoft.com):起動プロファイルを使用して、[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)またはコンソールで、アプリとサーバーを開始できます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-206">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="c21b0-207">[Visual Studio Code](https://code.visualstudio.com/):CoreCLR デバッガーをアクティブにする [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) によって、アプリとサーバーが開始されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-207">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="c21b0-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): アプリとサーバーは、[Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) によって開始されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="c21b0-209">コマンド プロンプトからプロジェクトのフォルダーでアプリを起動すると、[dotnet run](/dotnet/core/tools/dotnet-run) によってアプリとサーバーが起動されます (Kestrel および HTTP.sys のみ)。</span><span class="sxs-lookup"><span data-stu-id="c21b0-209">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="c21b0-210">この構成は、`Debug` (既定) または `Release` のどちらかに設定された `-c|--configuration` オプションによって指定されます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-210">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="c21b0-211">*launchSettings. json* ファイルは、`dotnet run` または Visual Studio などのツールに組み込まれたデバッガーを使用してアプリを起動するときの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-211">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="c21b0-212">起動プロファイルが *launchSettings. json* ファイルに存在する場合は、`dotnet run` コマンドで `--launch-profile {PROFILE NAME}` オプションを使用するか、Visual Studio でプロファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-212">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="c21b0-213">詳しくは、「[dotnet run](/dotnet/core/tools/dotnet-run)」および「[.NET Core の配布パッケージ](/dotnet/core/build/distribution-packaging)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-213">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="c21b0-214">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="c21b0-214">HTTP/2 support</span></span>

<span data-ttu-id="c21b0-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c21b0-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="c21b0-216">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c21b0-216">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="c21b0-217">オペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="c21b0-217">Operating system</span></span>
    * <span data-ttu-id="c21b0-218">Windows Server 2016/Windows 10 以降&dagger;</span><span class="sxs-lookup"><span data-stu-id="c21b0-218">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="c21b0-219">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="c21b0-219">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="c21b0-220">将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="c21b0-220">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="c21b0-221">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-221">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="c21b0-222">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c21b0-222">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="c21b0-223">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-223">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="c21b0-224">ターゲット フレームワーク:HTTP.sys の展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="c21b0-224">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="c21b0-225">IIS (インプロセス)</span><span class="sxs-lookup"><span data-stu-id="c21b0-225">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c21b0-226">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-226">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c21b0-227">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-227">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="c21b0-228">IIS (アウトプロセス)</span><span class="sxs-lookup"><span data-stu-id="c21b0-228">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c21b0-229">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c21b0-230">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、Kestrel へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-230">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c21b0-231">ターゲット フレームワーク:IIS アウトプロセスの展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="c21b0-231">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="c21b0-232">&dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-232">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c21b0-233">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="c21b0-233">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c21b0-234">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c21b0-234">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="c21b0-235">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c21b0-235">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="c21b0-236">オペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="c21b0-236">Operating system</span></span>
    * <span data-ttu-id="c21b0-237">Windows Server 2016/Windows 10 以降&dagger;</span><span class="sxs-lookup"><span data-stu-id="c21b0-237">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="c21b0-238">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="c21b0-238">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="c21b0-239">将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="c21b0-239">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="c21b0-240">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-240">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="c21b0-241">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c21b0-241">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="c21b0-242">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-242">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="c21b0-243">ターゲット フレームワーク:HTTP.sys の展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="c21b0-243">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="c21b0-244">IIS (インプロセス)</span><span class="sxs-lookup"><span data-stu-id="c21b0-244">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c21b0-245">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-245">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c21b0-246">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="c21b0-247">IIS (アウトプロセス)</span><span class="sxs-lookup"><span data-stu-id="c21b0-247">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c21b0-248">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-248">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c21b0-249">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、Kestrel へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-249">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c21b0-250">ターゲット フレームワーク:IIS アウトプロセスの展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="c21b0-250">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="c21b0-251">&dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="c21b0-251">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c21b0-252">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="c21b0-252">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c21b0-253">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c21b0-253">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="c21b0-254">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c21b0-254">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="c21b0-255">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-255">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="c21b0-256">ターゲット フレームワーク:HTTP.sys の展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="c21b0-256">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="c21b0-257">IIS (アウトプロセス)</span><span class="sxs-lookup"><span data-stu-id="c21b0-257">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="c21b0-258">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="c21b0-258">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c21b0-259">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、Kestrel へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="c21b0-259">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c21b0-260">ターゲット フレームワーク:IIS アウトプロセスの展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="c21b0-260">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="c21b0-261">HTTP/2 接続では、[アプリケーション レイヤー プロトコルのネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) および TLS 1.2 以降を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c21b0-261">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="c21b0-262">詳細については、ご利用のサーバーの展開シナリオに関連するトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c21b0-262">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c21b0-263">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c21b0-263">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>

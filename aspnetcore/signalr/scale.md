---
title: SignalR運用環境のホスティングとスケーリングの ASP.NET Core
author: bradygaster
description: ASP.NET Core を使用するアプリのパフォーマンスとスケーリングに関する問題を回避する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564004"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="69cd2-103">ASP.NET Core SignalR のホストとスケーリング</span><span class="sxs-lookup"><span data-stu-id="69cd2-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="69cd2-104">( [Andrew Stanton](https://twitter.com/anurse))、 [Brady](https://twitter.com/bradygaster)氏、 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="69cd2-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="69cd2-105">この記事では、ASP.NET Core を使用する高トラフィックアプリのホストとスケーリングに関する考慮事項について説明 SignalR します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="69cd2-106">固定セッション</span><span class="sxs-lookup"><span data-stu-id="69cd2-106">Sticky Sessions</span></span>

<span data-ttu-id="69cd2-107">SignalR では、特定の接続に対するすべての HTTP 要求を同じサーバープロセスで処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="69cd2-108">SignalRサーバーファーム (複数のサーバー) でが実行されている場合は、"固定セッション" を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="69cd2-109">"固定セッション" は、一部のロードバランサーによってセッションアフィニティとも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="69cd2-110">Azure App Service は、 [アプリケーション要求ルーティング](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) 処理 (ARR) を使用して要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="69cd2-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="69cd2-111">Azure App Service で "ARR Affinity" 設定を有効にすると、"固定セッション" が有効になります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="69cd2-112">固定セッションが不要な状況は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="69cd2-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="69cd2-113">単一のサーバーでホストする場合、1つのプロセスで実行します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="69cd2-114">Azure サービスを使用する場合 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="69cd2-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="69cd2-115">すべてのクライアントが Websocket **のみ** を使用するように構成され、 [skipnegotiation 設定](xref:signalr/configuration#configure-additional-options)がクライアント構成で有効に **なっている** 場合。</span><span class="sxs-lookup"><span data-stu-id="69cd2-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="69cd2-116">その他のすべての状況 (Redis バックプレーンを使用する場合を含む) では、サーバー環境を固定セッション用に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="69cd2-117">の Azure App Service の構成に関するガイダンスについ SignalR ては、「」を参照してください <xref:signalr/publish-to-azure-web-app> 。</span><span class="sxs-lookup"><span data-stu-id="69cd2-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="69cd2-118">TCP 接続リソース</span><span class="sxs-lookup"><span data-stu-id="69cd2-118">TCP connection resources</span></span>

<span data-ttu-id="69cd2-119">Web サーバーがサポートできる同時 TCP 接続の数は制限されています。</span><span class="sxs-lookup"><span data-stu-id="69cd2-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="69cd2-120">標準 HTTP クライアントは、 *一時的* な接続を使用します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="69cd2-121">これらの接続は、クライアントがアイドル状態になったときに終了し、後で再度開くことができます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="69cd2-122">一方、 SignalR 接続は *永続的* です。</span><span class="sxs-lookup"><span data-stu-id="69cd2-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="69cd2-123">SignalR クライアントがアイドル状態になった場合でも、接続は開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="69cd2-124">多数のクライアントにサービスを提供する高トラフィックアプリでは、これらの永続的な接続により、サーバーが最大接続数に達する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="69cd2-125">また、固定接続では、各接続を追跡するために、追加のメモリがいくつか消費されます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="69cd2-126">によって接続関連のリソースが頻繁に使用 SignalR される場合、同じサーバー上でホストされている他の web アプリに影響を与えることがあります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="69cd2-127">SignalRが開いていて、最後に使用可能な TCP 接続が保持されている場合、同じサーバー上の他の web アプリにも使用できる接続がありません。</span><span class="sxs-lookup"><span data-stu-id="69cd2-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="69cd2-128">サーバーの接続が不足している場合は、ランダムソケットエラーと接続リセットエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="69cd2-129">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="69cd2-130">他の web SignalR アプリでリソースの使用状況が発生しないようにするには、 SignalR 他の web アプリとは異なるサーバー上でを実行します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="69cd2-131">リソースの SignalR 使用状況によってアプリでエラーが発生しないようにするには SignalR 、スケールアウトして、サーバーが処理する接続の数を制限します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="69cd2-132">スケール アウト</span><span class="sxs-lookup"><span data-stu-id="69cd2-132">Scale out</span></span>

<span data-ttu-id="69cd2-133">を使用するアプリでは、 SignalR すべての接続を追跡する必要があります。これにより、サーバーファームに関する問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="69cd2-134">サーバーを追加すると、他のサーバーが認識していない新しい接続が取得されます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="69cd2-135">たとえば、 SignalR 次の図の各サーバーでは、他のサーバー上の接続が認識されません。</span><span class="sxs-lookup"><span data-stu-id="69cd2-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="69cd2-136">SignalRいずれかのサーバーですべてのクライアントにメッセージを送信する場合、メッセージは、そのサーバーに接続されているクライアントのみに送信されます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![スケーリング::: なし (SignalR)::: バックプレーンなし](scale/_static/scale-no-backplane.png)

<span data-ttu-id="69cd2-138">この問題を解決するためのオプションは、 [Azure SignalR サービス](#azure-signalr-service) と [Redis バックプレーン](#redis-backplane)です。</span><span class="sxs-lookup"><span data-stu-id="69cd2-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="69cd2-139">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="69cd2-139">Azure SignalR Service</span></span>

<span data-ttu-id="69cd2-140">Azure SignalR サービスは、バックプレーンではなくプロキシです。</span><span class="sxs-lookup"><span data-stu-id="69cd2-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="69cd2-141">クライアントがサーバーへの接続を開始するたびに、クライアントはサービスに接続するためにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="69cd2-142">このプロセスを次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-142">That process is illustrated in the following diagram:</span></span>

![Azure::: no loc (SignalR)::: Service への接続を確立しています](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="69cd2-144">その結果、次の図に示すように、サービスはすべてのクライアント接続を管理しますが、各サーバーにはサービスへの接続数をごくわずかにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![サービスに接続されているクライアント、サービスに接続されているサーバー](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="69cd2-146">このスケールアウトのアプローチには、Redis バックプレーンよりもいくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="69cd2-147">クライアント [アフィニティ](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)とも呼ばれる固定セッションは、接続時にクライアントが Azure サービスに直ちにリダイレクトされるため、必須ではありません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="69cd2-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="69cd2-148">アプリは、 SignalR 送信されたメッセージの数に基づいてスケールアウトできます。一方、Azure サービスは、 SignalR 任意の数の接続を処理するようにスケーリングします。</span><span class="sxs-lookup"><span data-stu-id="69cd2-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="69cd2-149">たとえば、クライアントが何千も存在する可能性はありますが、1秒あたり数個のメッセージしか送信されない場合、 SignalR アプリは接続自体を処理するためだけに複数のサーバーにスケールアウトする必要がありません。</span><span class="sxs-lookup"><span data-stu-id="69cd2-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="69cd2-150">アプリでは SignalR 、を使用しないと、web アプリよりもはるかに多くの接続リソースが使用されません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="69cd2-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="69cd2-151">これらの理由から、 SignalR SignalR App Service、vm、コンテナーなど、azure でホストされているすべての ASP.NET Core アプリに対して azure サービスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="69cd2-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="69cd2-152">詳細については、 [Azure SignalR サービスのドキュメント](/azure/azure-signalr/signalr-overview)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69cd2-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="69cd2-153">Redis バックプレーン</span><span class="sxs-lookup"><span data-stu-id="69cd2-153">Redis backplane</span></span>

<span data-ttu-id="69cd2-154">[Redis](https://redis.io/) は、パブリッシュ/サブスクライブモデルを持つメッセージングシステムをサポートするメモリ内キー値ストアです。</span><span class="sxs-lookup"><span data-stu-id="69cd2-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="69cd2-155">SignalRRedis バックプレーンは、pub/sub 機能を使用して、メッセージを他のサーバーに転送します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="69cd2-156">クライアントが接続を確立すると、接続情報がバックプレーンに渡されます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="69cd2-157">サーバーは、すべてのクライアントにメッセージを送信するときに、バックプレーンに送信します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="69cd2-158">バックプレーンは、接続されているすべてのクライアントと、それらがあるサーバーを認識します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="69cd2-159">このメッセージは、各サーバーを介してすべてのクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="69cd2-160">このプロセスを次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-160">This process is illustrated in the following diagram:</span></span>

![Redis バックプレーン、1つのサーバーからすべてのクライアントに送信されたメッセージ](scale/_static/redis-backplane.png)

<span data-ttu-id="69cd2-162">Redis バックプレーンは、お客様のインフラストラクチャでホストされているアプリに推奨されるスケールアウトアプローチです。</span><span class="sxs-lookup"><span data-stu-id="69cd2-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="69cd2-163">データセンターと Azure データセンターの間に大きな接続の待機時間がある場合、 SignalR 低待機時間または高いスループットの要件を持つオンプレミスのアプリでは、azure サービスが実用的な選択肢ではない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="69cd2-164">前述した Azure SignalR サービスの利点は、Redis バックプレーンの欠点です。</span><span class="sxs-lookup"><span data-stu-id="69cd2-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="69cd2-165">次の **両方** が当てはまる場合を除き、固定セッション ([クライアントアフィニティ](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)とも呼ばれます) が必要です。</span><span class="sxs-lookup"><span data-stu-id="69cd2-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="69cd2-166">すべてのクライアントは、Websocket **のみ** を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="69cd2-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="69cd2-167">クライアント構成で [Skipnegotiation 設定](xref:signalr/configuration#configure-additional-options) が有効になっています。</span><span class="sxs-lookup"><span data-stu-id="69cd2-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="69cd2-168">サーバーで接続が開始されると、接続はそのサーバー上にとどまります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="69cd2-169">アプリは、 SignalR 送信されるメッセージが少ない場合でも、クライアントの数に基づいてスケールアウトする必要があります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="69cd2-170">アプリは、を SignalR 使用しない場合、web アプリよりもはるかに多くの接続リソースを使用 SignalR します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="69cd2-171">Windows クライアント OS での IIS の制限事項</span><span class="sxs-lookup"><span data-stu-id="69cd2-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="69cd2-172">Windows 10 と Windows 8.x はクライアントオペレーティングシステムです。</span><span class="sxs-lookup"><span data-stu-id="69cd2-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="69cd2-173">クライアントオペレーティングシステムの IIS では、同時接続数が10個に制限されています。</span><span class="sxs-lookup"><span data-stu-id="69cd2-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="69cd2-174">SignalRの接続は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="69cd2-174">SignalR's connections are:</span></span>

* <span data-ttu-id="69cd2-175">一時的で、頻繁に再確立されます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="69cd2-176">使用されなくなった場合、すぐに **は破棄されません**。</span><span class="sxs-lookup"><span data-stu-id="69cd2-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="69cd2-177">上記の条件を満たすと、クライアント OS で10個の接続制限に達する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="69cd2-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="69cd2-178">クライアント OS を開発に使用する場合は、次のことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="69cd2-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="69cd2-179">IIS を避けます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-179">Avoid IIS.</span></span>
* <span data-ttu-id="69cd2-180">配置ターゲットとして Kestrel または IIS Express を使用します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="69cd2-181">Nginx を使用した Linux</span><span class="sxs-lookup"><span data-stu-id="69cd2-181">Linux with Nginx</span></span>

<span data-ttu-id="69cd2-182">次の表に、の Websocket、Serverのイベント、および LongPolling を有効にするために最低限必要な設定を示し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-182">The following contains the minimum required settings to enable WebSockets, ServerSentEvents, and LongPolling for SignalR:</span></span>

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

<span data-ttu-id="69cd2-183">複数のバックエンドサーバーを使用する場合は、接続時に接続がサーバーを切り替えるのを防ぐために、固定セッションを追加する必要があり SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-183">When multiple backend servers are used, sticky sessions must be added to prevent SignalR connections from switching servers when connecting.</span></span> <span data-ttu-id="69cd2-184">Nginx では、複数の方法で固定セッションを追加できます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-184">There are multiple ways to add sticky sessions in Nginx.</span></span> <span data-ttu-id="69cd2-185">次に、使用している内容に応じて2つの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="69cd2-185">Two approaches are shown below depending on what you have available.</span></span>

<span data-ttu-id="69cd2-186">前の構成に加えて、次のものも追加されています。</span><span class="sxs-lookup"><span data-stu-id="69cd2-186">The following is added in addition to the previous configuration.</span></span> <span data-ttu-id="69cd2-187">次の例で `backend` は、はサーバーのグループの名前です。</span><span class="sxs-lookup"><span data-stu-id="69cd2-187">In the following examples, `backend` is the name of the group of servers.</span></span>

<span data-ttu-id="69cd2-188">[Nginx オープンソース](https://nginx.org/en/)では、を使用して、 `ip_hash` クライアントの IP アドレスに基づいてサーバーへの接続をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="69cd2-188">With [Nginx Open Source](https://nginx.org/en/), use `ip_hash` to route connections to a server based on the client's IP address:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

<span data-ttu-id="69cd2-189">[Nginx Plus](https://www.nginx.com/products/nginx)では、を使用し `sticky` てを cookie 要求に追加し、ユーザーの要求をサーバーにピン留めします。</span><span class="sxs-lookup"><span data-stu-id="69cd2-189">With [Nginx Plus](https://www.nginx.com/products/nginx), use `sticky` to add a cookie to requests and pin the user's requests to a server:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

<span data-ttu-id="69cd2-190">最後に、 `proxy_pass http://localhost:5000` セクションの `server` をに変更し `proxy_pass http://backend` ます。</span><span class="sxs-lookup"><span data-stu-id="69cd2-190">Finally, change `proxy_pass http://localhost:5000` in the `server` section to `proxy_pass http://backend`.</span></span>

<span data-ttu-id="69cd2-191">Websocket over Nginx の詳細については、「 [Nginx as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69cd2-191">For more information on WebSockets over Nginx, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx).</span></span>

<span data-ttu-id="69cd2-192">負荷分散と固定セッションの詳細については、「 [NGINX load balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69cd2-192">For more information on load balancing and sticky sessions, see [NGINX load balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span></span>

<span data-ttu-id="69cd2-193">Nginx の ASP.NET Core の詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69cd2-193">For more information about ASP.NET Core with Nginx see the following article:</span></span>
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a><span data-ttu-id="69cd2-194">サードパーティ製 SignalR バックプレーンプロバイダー</span><span class="sxs-lookup"><span data-stu-id="69cd2-194">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="69cd2-195">NCache</span><span class="sxs-lookup"><span data-stu-id="69cd2-195">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="69cd2-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="69cd2-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>
* <span data-ttu-id="69cd2-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span><span class="sxs-lookup"><span data-stu-id="69cd2-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span></span>

## <a name="next-steps"></a><span data-ttu-id="69cd2-198">次のステップ</span><span class="sxs-lookup"><span data-stu-id="69cd2-198">Next steps</span></span>

<span data-ttu-id="69cd2-199">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69cd2-199">For more information, see the following resources:</span></span>

* [<span data-ttu-id="69cd2-200">Azure SignalR サービスのドキュメント</span><span class="sxs-lookup"><span data-stu-id="69cd2-200">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="69cd2-201">Redis バックプレーンを設定する</span><span class="sxs-lookup"><span data-stu-id="69cd2-201">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)

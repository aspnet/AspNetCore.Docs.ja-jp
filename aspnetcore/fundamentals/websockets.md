---
title: ASP.NET Core での Websocket のサポート
author: rick-anderson
description: ASP.NET Core で Websocket の使用を開始する方法を説明します。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 6edf2017cc889321cfb484e643b75711fd66004d
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058351"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="e6f88-103">ASP.NET Core での Websocket のサポート</span><span class="sxs-lookup"><span data-stu-id="e6f88-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="e6f88-104">作成者: [Tom Dykstra](https://github.com/tdykstra) および [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e6f88-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="e6f88-105">この記事では、ASP.NET Core で Websocket の使用を開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="e6f88-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) は、TCP 接続を使用した双方向の永続的通信チャネルを有効にするプロトコルです。</span><span class="sxs-lookup"><span data-stu-id="e6f88-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="e6f88-107">このプロトコルは、チャット、ダッシュボード、ゲーム アプリなど、高速かつリアルタイムのコミュニケーションを活用するアプリで使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="e6f88-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e6f88-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="e6f88-109">[実行方法](#sample-app)。</span><span class="sxs-lookup"><span data-stu-id="e6f88-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="e6f88-110">[ASP.NET Core SignalR](xref:signalr/introduction) は、アプリへのリアルタイム Web 機能の追加を簡単にするライブラリです。</span><span class="sxs-lookup"><span data-stu-id="e6f88-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="e6f88-111">可能なかぎり、WebSocket が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="e6f88-112">ほとんどのアプリケーションでは、生の WebSocket よりも SignalR が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="e6f88-113">SignalR には、WebSocket を使用できない環境の場合にトランスポートのフォールバックが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e6f88-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="e6f88-114">基本的なリモート プロシージャ呼び出しアプリ モデルも用意されています。</span><span class="sxs-lookup"><span data-stu-id="e6f88-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="e6f88-115">また、ほとんどのシナリオで、SignalR には生の WebSocket を使用した場合と比較してパフォーマンス上の大きなデメリットがありません。</span><span class="sxs-lookup"><span data-stu-id="e6f88-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="e6f88-116">一部のアプリでは、[.NET の gRPC](xref:grpc/index) に Websocket の代替手段が用意されています。</span><span class="sxs-lookup"><span data-stu-id="e6f88-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6f88-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="e6f88-117">Prerequisites</span></span>

* <span data-ttu-id="e6f88-118">ASP.NET Core をサポートする任意の OS:</span><span class="sxs-lookup"><span data-stu-id="e6f88-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="e6f88-119">Windows 7 / Windows Server 2008 以降</span><span class="sxs-lookup"><span data-stu-id="e6f88-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="e6f88-120">Linux</span><span class="sxs-lookup"><span data-stu-id="e6f88-120">Linux</span></span>
  * <span data-ttu-id="e6f88-121">macOS</span><span class="sxs-lookup"><span data-stu-id="e6f88-121">macOS</span></span>  
* <span data-ttu-id="e6f88-122">アプリが IIS を含む Windows 上で実行されている場合:</span><span class="sxs-lookup"><span data-stu-id="e6f88-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="e6f88-123">Windows 8 / Windows Server 2012 以降</span><span class="sxs-lookup"><span data-stu-id="e6f88-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="e6f88-124">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="e6f88-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="e6f88-125">WebSockets を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6f88-125">WebSockets must be enabled.</span></span> <span data-ttu-id="e6f88-126">「[IIS/IIS Express のサポート](#iisiis-express-support)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6f88-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="e6f88-127">アプリが [HTTP.sys](xref:fundamentals/servers/httpsys) で実行されている場合:</span><span class="sxs-lookup"><span data-stu-id="e6f88-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="e6f88-128">Windows 8 / Windows Server 2012 以降</span><span class="sxs-lookup"><span data-stu-id="e6f88-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="e6f88-129">サポートされているブラウザーについては、 https://caniuse.com/#feat=websockets を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6f88-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="e6f88-130">ミドルウェアの構成</span><span class="sxs-lookup"><span data-stu-id="e6f88-130">Configure the middleware</span></span>

<span data-ttu-id="e6f88-131">`Startup` クラスの `Configure` メソッドに、Websocket ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e6f88-132">次の設定を構成できます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-132">The following settings can be configured:</span></span>

* <span data-ttu-id="e6f88-133">`KeepAliveInterval`: プロキシの接続の維持を保証する、クライアントに "ping" フレームを送信する頻度。</span><span class="sxs-lookup"><span data-stu-id="e6f88-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="e6f88-134">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="e6f88-134">The default is two minutes.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e6f88-135">次の設定を構成できます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-135">The following settings can be configured:</span></span>

* <span data-ttu-id="e6f88-136">`KeepAliveInterval`: プロキシの接続の維持を保証する、クライアントに "ping" フレームを送信する頻度。</span><span class="sxs-lookup"><span data-stu-id="e6f88-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="e6f88-137">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="e6f88-137">The default is two minutes.</span></span>
* <span data-ttu-id="e6f88-138">`AllowedOrigins` - WebSocket 要求で許可される配信元ヘッダー値の一覧。</span><span class="sxs-lookup"><span data-stu-id="e6f88-138">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="e6f88-139">既定では、すべての配信元が許可されています。</span><span class="sxs-lookup"><span data-stu-id="e6f88-139">By default, all origins are allowed.</span></span> <span data-ttu-id="e6f88-140">詳細については、下記の "WebSocket の配信元の制限" を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6f88-140">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="e6f88-141">WebSocket の要求の受け入れ</span><span class="sxs-lookup"><span data-stu-id="e6f88-141">Accept WebSocket requests</span></span>

<span data-ttu-id="e6f88-142">以降の要求ライフサイクルのどこかで (たとえば、以降の `Configure` メソッドまたはアクション メソッド)、それが WebSocket 要求であるかを確認し、WebSocket 要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-142">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="e6f88-143">次の例は、以降の `Configure` メソッドから抜粋したものです。</span><span class="sxs-lookup"><span data-stu-id="e6f88-143">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="e6f88-144">WebSocket 要求はどの URL からも受け取る場合がありますが、このサンプル コードでは `/ws` の要求のみを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="e6f88-144">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="e6f88-145">WebSocket を使用するとき、接続中、ミドルウェア パイプラインの実行を維持する **必要があります**。</span><span class="sxs-lookup"><span data-stu-id="e6f88-145">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="e6f88-146">ミドルウェア パイプラインの修了後に WebSocket メッセージを送信するか、受信する場合、次のような例外を受け取ることがあります。</span><span class="sxs-lookup"><span data-stu-id="e6f88-146">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="e6f88-147">バックグラウンド サービスを利用してデータを WebSocket に書き込む場合、ミドルウェア パイプラインの実行を維持します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-147">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="e6f88-148">これは <xref:System.Threading.Tasks.TaskCompletionSource%601> を使用して行います。</span><span class="sxs-lookup"><span data-stu-id="e6f88-148">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="e6f88-149">`TaskCompletionSource` をバックグラウンド サービスに渡し、WebSocket が終わったとき、それに <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> を呼び出させます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-149">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="e6f88-150">次の例に示すように、要求中に <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> プロパティの `await` を行います。</span><span class="sxs-lookup"><span data-stu-id="e6f88-150">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="e6f88-151">WebSocket の終了例外は、アクション メソッドから早く戻りすぎた場合にも発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e6f88-151">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="e6f88-152">アクション メソッドでソケットを受け入れる場合は、そのソケットを使用するコードが完了するまで待ち、アクション メソッドから戻ってください。</span><span class="sxs-lookup"><span data-stu-id="e6f88-152">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="e6f88-153">重大なスレッドの問題を引き起こす可能性があるので、ソケットの完了を待つために `Task.Wait`、`Task.Result`、または同様のブロック呼び出しを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="e6f88-153">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="e6f88-154">常に `await` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-154">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="e6f88-155">メッセージの送受信</span><span class="sxs-lookup"><span data-stu-id="e6f88-155">Send and receive messages</span></span>

<span data-ttu-id="e6f88-156">`AcceptWebSocketAsync` メソッドは、TCP 接続を WebSocket 接続にアップグレードし、[WebSocket](/dotnet/core/api/system.net.websockets.websocket) オブジェクトを提供します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-156">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="e6f88-157">メッセージの送受信に、`WebSocket` オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-157">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="e6f88-158">前に示した、WebSocket 要求を受け入れるコードが、`WebSocket` オブジェクトを `Echo` メソッドに渡します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-158">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="e6f88-159">このコードは、メッセージを受信し、同じメッセージをすぐに送信します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-159">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="e6f88-160">クライアントが接続を閉じるまで、メッセージがループで送受信されます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-160">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="e6f88-161">このループを開始する前に、WebSocket 接続を受け入れた場合、ミドルウェア パイプラインは終了します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-161">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="e6f88-162">ソケットを閉じると、パイプラインはアンワインドされます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-162">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="e6f88-163">つまり、WebSocket を受け入れると、要求はパイプラインでの先への移動を中止します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-163">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="e6f88-164">ループを終了し、ソケットを閉じた場合、要求はパイプラインのバックアップを続けます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-164">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="e6f88-165">クライアントの切断の処理</span><span class="sxs-lookup"><span data-stu-id="e6f88-165">Handle client disconnects</span></span>

<span data-ttu-id="e6f88-166">接続の損失によってクライアントが切断されても、サーバーに自動的に通知されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="e6f88-166">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="e6f88-167">サーバーが切断メッセージを受信するのは、クライアントがそれを送信した場合のみです。インターネット接続が失われた場合、これを実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6f88-167">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="e6f88-168">これが発生した場合に何らかのアクションを実行したい場合は、特定の時間枠内でクライアントからの受信を待つタイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-168">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="e6f88-169">クライアントが常にメッセージを送信するとは限らず、その接続がアイドル状態になっただけでタイムアウトしたくない場合は、X 秒ごとに ping メッセージを送信するタイマーをクライアントに使用させます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-169">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="e6f88-170">サーバー上では、前のものから 2\*X 秒以内にメッセージが到着しなかった場合に、接続を終了してクライアントが切断されたことをレポートします。</span><span class="sxs-lookup"><span data-stu-id="e6f88-170">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="e6f88-171">予想される 2 倍の期間を待機することで、ping メッセージを遅らせる可能性のあるネットワークの遅延のために余分な時間を残します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-171">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="e6f88-172">WebSocket の配信元の制限</span><span class="sxs-lookup"><span data-stu-id="e6f88-172">WebSocket origin restriction</span></span>

<span data-ttu-id="e6f88-173">CORS で提供される保護は、WebSocket には適用されません。</span><span class="sxs-lookup"><span data-stu-id="e6f88-173">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="e6f88-174">ブラウザーでは以下を実行 **しません**。</span><span class="sxs-lookup"><span data-stu-id="e6f88-174">Browsers do **not**:</span></span>

* <span data-ttu-id="e6f88-175">CORS の事前要求を実行する。</span><span class="sxs-lookup"><span data-stu-id="e6f88-175">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="e6f88-176">WebSocket 要求を行うときに `Access-Control` ヘッダーに指定された制限を考慮する。</span><span class="sxs-lookup"><span data-stu-id="e6f88-176">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="e6f88-177">ただし、WebSocket 要求を発行するときにはブラウザーから `Origin` ヘッダーが送信されます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-177">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="e6f88-178">予期した配信元からの WebSocket のみが許可されるように、アプリケーションでこれらのヘッダーが検証されるように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6f88-178">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="e6f88-179">"https://server.com" でサーバーを、"https://client.com" でクライアントをホスティングしている場合は、検証のために "https://client.com" を WebSocket の `AllowedOrigins` 一覧に追加します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-179">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="e6f88-180">`Origin` ヘッダーは、クライアントによって制御され、`Referer` のように偽装することができます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-180">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="e6f88-181">これらのヘッダーを認証メカニズムとして使用 **しないでください**。</span><span class="sxs-lookup"><span data-stu-id="e6f88-181">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="e6f88-182">IIS/IIS Express のサポート</span><span class="sxs-lookup"><span data-stu-id="e6f88-182">IIS/IIS Express support</span></span>

<span data-ttu-id="e6f88-183">IIS/IIS Express 8 以降を含む、Windows Server 2012 以降および Windows 8 以降では、WebSocket プロトコルをサポートします。</span><span class="sxs-lookup"><span data-stu-id="e6f88-183">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="e6f88-184">IIS Express を使用する場合、WebSockets は常に有効になります。</span><span class="sxs-lookup"><span data-stu-id="e6f88-184">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="e6f88-185">IIS での WebSockets の有効化</span><span class="sxs-lookup"><span data-stu-id="e6f88-185">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="e6f88-186">Windows Server 2012 以降で WebSocket プロトコルのサポートを有効にするには</span><span class="sxs-lookup"><span data-stu-id="e6f88-186">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="e6f88-187">これらの手順は、IIS Express を使用する場合は必要ありません</span><span class="sxs-lookup"><span data-stu-id="e6f88-187">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="e6f88-188">**[管理]** メニューから **役割と機能の追加** ウィザードを使用するか、**サーバー マネージャー** にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-188">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="e6f88-189">**[役割ベースまたは機能ベースのインストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-189">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="e6f88-190">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-190">Select **Next**.</span></span>
1. <span data-ttu-id="e6f88-191">適切なサーバーを選択します (既定では、ローカル サーバーが選択されます)。</span><span class="sxs-lookup"><span data-stu-id="e6f88-191">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="e6f88-192">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-192">Select **Next**.</span></span>
1. <span data-ttu-id="e6f88-193">**[役割]** ツリーで **[Web サーバー (IIS)]** を展開し、 **[Web サーバー]** 、 **[アプリケーション開発]** の順に展開します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-193">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="e6f88-194">**[WebSocket プロトコル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-194">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="e6f88-195">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-195">Select **Next**.</span></span>
1. <span data-ttu-id="e6f88-196">追加機能が不要な場合は、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-196">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="e6f88-197">**[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-197">Select **Install**.</span></span>
1. <span data-ttu-id="e6f88-198">インストールが完了したら、 **[閉じる]** を選択してウィザードを終了します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-198">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="e6f88-199">Windows 8 以降で WebSocket プロトコルのサポートを有効にするには</span><span class="sxs-lookup"><span data-stu-id="e6f88-199">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="e6f88-200">これらの手順は、IIS Express を使用する場合は必要ありません</span><span class="sxs-lookup"><span data-stu-id="e6f88-200">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="e6f88-201">**[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]**  >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-201">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="e6f88-202">次のノード: **[インターネット インフォメーション サービス]**  >  **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-202">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="e6f88-203">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-203">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e6f88-204">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-204">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="e6f88-205">Node.js で socket.io を使用する場合に WebSocket を無効にする</span><span class="sxs-lookup"><span data-stu-id="e6f88-205">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="e6f88-206">[Node.js](https://nodejs.org/) の [socket.io](https://socket.io/) で WebSocket サポートを使用する場合、`webSocket` 要素を使用する既定の WebSocket モジュールを *web.config* または *applicationHost.config* で無効にします。この手順が実行されない場合、IIS WebSocket モジュールは Node.js とアプリ以外の WebSocket コミュニケーションを処理しようとします。</span><span class="sxs-lookup"><span data-stu-id="e6f88-206">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="e6f88-207">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="e6f88-207">Sample app</span></span>

<span data-ttu-id="e6f88-208">この記事に添えられている[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples)は、エコー アプリです。</span><span class="sxs-lookup"><span data-stu-id="e6f88-208">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="e6f88-209">これには、WebSocket 接続を作成する Web ページがあり、サーバーが受け取るすべてのメッセージをクライアントに再送信します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-209">It has a webpage that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="e6f88-210">このサンプル アプリは、IIS Express を使用して Visual Studio から実行するように構成されていないため、コマンド シェルで [`dotnet run`](/dotnet/core/tools/dotnet-run) を使用してアプリを実行し、ブラウザーで `http://localhost:5000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-210">The sample app isn't configured to run from Visual Studio with IIS Express, so run the app in a command shell with [`dotnet run`](/dotnet/core/tools/dotnet-run) and navigate in a browser to `http://localhost:5000`.</span></span> <span data-ttu-id="e6f88-211">Web ページに接続状態が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-211">The webpage shows the connection status:</span></span>

![WebSocket 接続前の Web ページの初期状態](websockets/_static/start.png)

<span data-ttu-id="e6f88-213">**[接続]** を選択し、表示されている URL に WebSocket 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-213">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="e6f88-214">テスト メッセージを入力し、 **[送信]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-214">Enter a test message and select **Send**.</span></span> <span data-ttu-id="e6f88-215">完了したら、 **[Close Socket]\(ソケットを閉じる\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6f88-215">When done, select **Close Socket**.</span></span> <span data-ttu-id="e6f88-216">**[Communication Log]\(コミュニケーション ログ\)** セクションに、発生した各オープン、送信、クローズのアクションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e6f88-216">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![WebSocket 接続とテスト メッセージの送受信が行われた後の Web ページの最終的な状態](websockets/_static/end.png)

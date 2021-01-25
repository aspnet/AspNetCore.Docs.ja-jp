---
title: ASP.NET Core Kestrel Web サーバーでの要求のドレイン
author: rick-anderson
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel での要求のドレインについて説明します。
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253866"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="90b41-103">ASP.NET Core Kestrel Web サーバーでの要求のドレイン</span><span class="sxs-lookup"><span data-stu-id="90b41-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="90b41-104">HTTP 接続を開くには時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="90b41-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="90b41-105">HTTPS の場合も、リソースが大量に消費されます。</span><span class="sxs-lookup"><span data-stu-id="90b41-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="90b41-106">そのため、Kestrel は、HTTP/1.1 プロトコルごとに接続を再利用しようとします。</span><span class="sxs-lookup"><span data-stu-id="90b41-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="90b41-107">接続を再利用できるようにするには、要求本文を完全に使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90b41-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="90b41-108">サーバーがリダイレクトや 404 応答を返す HTTP POST 要求のように、アプリによって常に要求本文が使用されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="90b41-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="90b41-109">HTTP POST リダイレクトの場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="90b41-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="90b41-110">クライアントによって、POST データの一部が既に送信されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="90b41-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="90b41-111">サーバーは 301 応答を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="90b41-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="90b41-112">以前の要求本文の POST データが完全に読み取られるまで、新しい要求に対して接続を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="90b41-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="90b41-113">Kestrel は、要求本文のドレインを試行します。</span><span class="sxs-lookup"><span data-stu-id="90b41-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="90b41-114">要求本文のドレインは、データを処理せずに読み取りおよび破棄を行うことを意味します。</span><span class="sxs-lookup"><span data-stu-id="90b41-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="90b41-115">ドレイン プロセスでは、接続が再利用できるようになる代わりに、残りのデータをドレインするのに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="90b41-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="90b41-116">ドレインのタイムアウトは 5 秒であり、この設定は構成できません。</span><span class="sxs-lookup"><span data-stu-id="90b41-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="90b41-117">`Content-Length` または `Transfer-Encoding` ヘッダーで指定されたすべてのデータがタイムアウト前に読み取られていない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="90b41-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="90b41-118">場合によっては、応答の書き込みの前後に要求をすぐに終了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90b41-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="90b41-119">たとえば、クライアントのデータ キャップが制限されている場合があります。</span><span class="sxs-lookup"><span data-stu-id="90b41-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="90b41-120">アップロードされたデータを制限することが優先される場合があります。</span><span class="sxs-lookup"><span data-stu-id="90b41-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="90b41-121">このような場合、要求を終了するには、コントローラー、Razor ページ、またはミドルウェアから [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90b41-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="90b41-122">`Abort` を呼び出す際には、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90b41-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="90b41-123">新しい接続の作成には、時間とコストがかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="90b41-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="90b41-124">接続が閉じる前にクライアントが応答を読み取ることは保証されません。</span><span class="sxs-lookup"><span data-stu-id="90b41-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="90b41-125">`Abort` の呼び出しは最小限に抑え、一般的なエラーではなく重大なエラーが発生した場合のために予約する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90b41-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="90b41-126">`Abort` は特定の問題を解決する必要がある場合にのみ、呼び出してください。</span><span class="sxs-lookup"><span data-stu-id="90b41-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="90b41-127">たとえば、悪意のあるクライアントによってデータの POST が試行されている場合や、クライアント コードに大量の、または複数の要求を引き起こすバグがある場合に `Abort` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90b41-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="90b41-128">HTTP 404 (Not Found) などの一般的なエラー状況では、`Abort` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="90b41-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="90b41-129">`Abort` を呼び出す前に [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) を呼び出すと、サーバーが応答の書き込みを完了したことが保証されます。</span><span class="sxs-lookup"><span data-stu-id="90b41-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="90b41-130">ただし、クライアントの動作は予測できないため、接続が中止される前に応答が読み取られない場合があります。</span><span class="sxs-lookup"><span data-stu-id="90b41-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="90b41-131">プロトコルでは、接続を閉じずに個々の要求ストリームを中止することがサポートされているため、HTTP/2 では、このプロセスが異なります。</span><span class="sxs-lookup"><span data-stu-id="90b41-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="90b41-132">5 秒のドレイン タイムアウトは適用されません。</span><span class="sxs-lookup"><span data-stu-id="90b41-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="90b41-133">応答の完了後に未読の要求本文データがある場合、サーバーは HTTP/2 RST フレームを送信します。</span><span class="sxs-lookup"><span data-stu-id="90b41-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="90b41-134">追加の要求本文データ フレームは無視されます。</span><span class="sxs-lookup"><span data-stu-id="90b41-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="90b41-135">可能なかぎり、クライアントが [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) の要求ヘッダーを利用し、要求本文の送信を開始する前にサーバーの応答を待機することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="90b41-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="90b41-136">これにより、クライアントは、不要なデータを送信する前に、応答を調べて中止することができます。</span><span class="sxs-lookup"><span data-stu-id="90b41-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

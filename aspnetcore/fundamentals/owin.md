---
title: Open Web Interface for .NET (OWIN) と ASP.NET Core
author: ardalis
description: ASP.NET Core が Open Web Interface for .NET (OWIN) をどのようにサポートしているかを確認します。確認することで、Web アプリケーションを Web サーバーから切り離すことができるようになります。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
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
uid: fundamentals/owin
ms.openlocfilehash: c44803c6f67d8e03759a01f7aa71d82088be9a11
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975275"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="54e5f-103">Open Web Interface for .NET (OWIN) と ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54e5f-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="54e5f-104">作成者: [Steve Smith](https://ardalis.com/)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="54e5f-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="54e5f-105">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="54e5f-105">ASP.NET Core:</span></span>

* <span data-ttu-id="54e5f-106">Open Web Interface for .NET (OWIN) をサポートします。</span><span class="sxs-lookup"><span data-stu-id="54e5f-106">Supports the Open Web Interface for .NET (OWIN).</span></span>
* <span data-ttu-id="54e5f-107">`Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) ライブラリの .NET Core と互換性のある置換があります。</span><span class="sxs-lookup"><span data-stu-id="54e5f-107">Has .NET Core compatible replacements for the `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) libraries.</span></span>

<span data-ttu-id="54e5f-108">OWIN により、Web アプリを Web サーバーから切り離すことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="54e5f-108">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="54e5f-109">ミドルウェアをパイプラインで使用し、要求と関連する応答を処理するための標準的な方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="54e5f-109">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="54e5f-110">ASP.NET Core アプリケーションとミドルウェアは、OWIN ベースのアプリケーション、サーバー、およびミドルウェアと相互運用できます。</span><span class="sxs-lookup"><span data-stu-id="54e5f-110">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="54e5f-111">OWIN には、さまざまなオブジェクト モデルを使用する 2 つのフレームワークを併用できる分離レイヤーがあります。</span><span class="sxs-lookup"><span data-stu-id="54e5f-111">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="54e5f-112">`Microsoft.AspNetCore.Owin` パッケージには 2 つのアダプター実装が用意されています。</span><span class="sxs-lookup"><span data-stu-id="54e5f-112">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="54e5f-113">ASP.NET Core から OWIN へ</span><span class="sxs-lookup"><span data-stu-id="54e5f-113">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="54e5f-114">OWIN から ASP.NET Core へ</span><span class="sxs-lookup"><span data-stu-id="54e5f-114">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="54e5f-115">これにより、ASP.NET Core を OWIN 互換のサーバー/ホスト上でホストするか、他の OWIN 互換コンポーネントを ASP.NET Core 上で実行することができます。</span><span class="sxs-lookup"><span data-stu-id="54e5f-115">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="54e5f-116">これらのアダプターを使用すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="54e5f-116">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="54e5f-117">ASP.NET Core コンポーネントのみを使用するアプリケーションでは、`Microsoft.AspNetCore.Owin` パッケージまたはアダプターを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-117">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="54e5f-118">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="54e5f-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="54e5f-119">ASP.NET Core パイプラインで OWIN ミドルウェアを実行する</span><span class="sxs-lookup"><span data-stu-id="54e5f-119">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="54e5f-120">ASP.NET Core の OWIN のサポートは、`Microsoft.AspNetCore.Owin` パッケージの一部として展開されます。</span><span class="sxs-lookup"><span data-stu-id="54e5f-120">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="54e5f-121">このパッケージをインストールすることで、OWIN のサポートをプロジェクトにインポートできます。</span><span class="sxs-lookup"><span data-stu-id="54e5f-121">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="54e5f-122">OWIN ミドルウェアは、`Func<IDictionary<string, object>, Task>` インターフェイスと特定のキー (`owin.ResponseBody` など) の設定を必須とする [OWIN 仕様](https://owin.org/spec/spec/owin-1.0.0.html)に準拠しています。</span><span class="sxs-lookup"><span data-stu-id="54e5f-122">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="54e5f-123">次の単純な OWIN ミドルウェアを実行すると "Hello World" が表示されます。</span><span class="sxs-lookup"><span data-stu-id="54e5f-123">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="54e5f-124">サンプル署名は `Task` を返し、OWIN で必要な場合に `IDictionary<string, object>` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="54e5f-124">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="54e5f-125">次のコードは、`UseOwin` 拡張メソッドを使用して ASP.NET Core パイプラインに `OwinHello` ミドルウェア (上の図) を追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="54e5f-125">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="54e5f-126">OWIN パイプライン内で実行する他のアクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="54e5f-126">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="54e5f-127">応答ヘッダーは、応答ストリームへの最初の書き込み前にのみ変更してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-127">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="54e5f-128">パフォーマンス上の理由から、`UseOwin` を複数回、呼び出すことは避けてください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-128">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="54e5f-129">OWIN コンポーネントは、グループ化されている場合に最適に動作します。</span><span class="sxs-lookup"><span data-stu-id="54e5f-129">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="54e5f-130">OWIN ベースのサーバー上で ASP.NET Core を実行し、その WebSockets のサポートを利用する</span><span class="sxs-lookup"><span data-stu-id="54e5f-130">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="54e5f-131">OWIN ベースのサーバーの機能を ASP.NET Core で利用する方法のもう 1 つの例として、WebSockets などの機能へのアクセスが挙げられます。</span><span class="sxs-lookup"><span data-stu-id="54e5f-131">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="54e5f-132">前の例で使用していた .NET OWIN Web サーバーは、ASP.NET Core アプリケーションから利用できる組み込みの Web Sockets をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="54e5f-132">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="54e5f-133">Web Sockets をサポートし、WebSockets を介してサーバーに送信されたすべてをエコー バックする単純な Web アプリケーションの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="54e5f-133">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="54e5f-134">OWIN 環境</span><span class="sxs-lookup"><span data-stu-id="54e5f-134">OWIN environment</span></span>

<span data-ttu-id="54e5f-135">`HttpContext` を使用して OWIN 環境を構築できます。</span><span class="sxs-lookup"><span data-stu-id="54e5f-135">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="54e5f-136">OWIN キー</span><span class="sxs-lookup"><span data-stu-id="54e5f-136">OWIN keys</span></span>

<span data-ttu-id="54e5f-137">OWIN は、HTTP 要求/応答の交換を通じて情報を伝達するために `IDictionary<string,object>` オブジェクトに依存しています。</span><span class="sxs-lookup"><span data-stu-id="54e5f-137">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="54e5f-138">ASP.NET Core は次のキーを実装しています。</span><span class="sxs-lookup"><span data-stu-id="54e5f-138">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="54e5f-139">[主な仕様、拡張機能](https://owin.org/#spec)に関するセクションと、「[OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html)」(OWIN キーのガイドラインと共通キー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-139">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="54e5f-140">要求データ (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="54e5f-140">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="54e5f-141">Key</span><span class="sxs-lookup"><span data-stu-id="54e5f-141">Key</span></span>               | <span data-ttu-id="54e5f-142">値 (型)</span><span class="sxs-lookup"><span data-stu-id="54e5f-142">Value (type)</span></span> | <span data-ttu-id="54e5f-143">説明</span><span class="sxs-lookup"><span data-stu-id="54e5f-143">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="54e5f-144">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="54e5f-144">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="54e5f-145">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="54e5f-145">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="54e5f-146">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="54e5f-146">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="54e5f-147">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="54e5f-147">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="54e5f-148">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="54e5f-148">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="54e5f-149">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="54e5f-149">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="54e5f-150">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="54e5f-150">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="54e5f-151">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="54e5f-151">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="54e5f-152">要求データ (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="54e5f-152">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="54e5f-153">Key</span><span class="sxs-lookup"><span data-stu-id="54e5f-153">Key</span></span>               | <span data-ttu-id="54e5f-154">値 (型)</span><span class="sxs-lookup"><span data-stu-id="54e5f-154">Value (type)</span></span> | <span data-ttu-id="54e5f-155">説明</span><span class="sxs-lookup"><span data-stu-id="54e5f-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="54e5f-156">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="54e5f-156">owin.RequestId</span></span> | `String` | <span data-ttu-id="54e5f-157">Optional</span><span class="sxs-lookup"><span data-stu-id="54e5f-157">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="54e5f-158">応答データ (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="54e5f-158">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="54e5f-159">Key</span><span class="sxs-lookup"><span data-stu-id="54e5f-159">Key</span></span>               | <span data-ttu-id="54e5f-160">値 (型)</span><span class="sxs-lookup"><span data-stu-id="54e5f-160">Value (type)</span></span> | <span data-ttu-id="54e5f-161">説明</span><span class="sxs-lookup"><span data-stu-id="54e5f-161">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="54e5f-162">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="54e5f-162">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="54e5f-163">Optional</span><span class="sxs-lookup"><span data-stu-id="54e5f-163">Optional</span></span> |
| <span data-ttu-id="54e5f-164">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="54e5f-164">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="54e5f-165">Optional</span><span class="sxs-lookup"><span data-stu-id="54e5f-165">Optional</span></span> |
| <span data-ttu-id="54e5f-166">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="54e5f-166">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="54e5f-167">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="54e5f-167">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="54e5f-168">その他のデータ (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="54e5f-168">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="54e5f-169">Key</span><span class="sxs-lookup"><span data-stu-id="54e5f-169">Key</span></span>               | <span data-ttu-id="54e5f-170">値 (型)</span><span class="sxs-lookup"><span data-stu-id="54e5f-170">Value (type)</span></span> | <span data-ttu-id="54e5f-171">説明</span><span class="sxs-lookup"><span data-stu-id="54e5f-171">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="54e5f-172">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="54e5f-172">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="54e5f-173">owin.Version</span><span class="sxs-lookup"><span data-stu-id="54e5f-173">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="54e5f-174">共通キー</span><span class="sxs-lookup"><span data-stu-id="54e5f-174">Common keys</span></span>

| <span data-ttu-id="54e5f-175">Key</span><span class="sxs-lookup"><span data-stu-id="54e5f-175">Key</span></span>               | <span data-ttu-id="54e5f-176">値 (型)</span><span class="sxs-lookup"><span data-stu-id="54e5f-176">Value (type)</span></span> | <span data-ttu-id="54e5f-177">説明</span><span class="sxs-lookup"><span data-stu-id="54e5f-177">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="54e5f-178">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="54e5f-178">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="54e5f-179">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="54e5f-179">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="54e5f-180">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="54e5f-180">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="54e5f-181">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="54e5f-181">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="54e5f-182">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="54e5f-182">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="54e5f-183">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="54e5f-183">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="54e5f-184">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="54e5f-184">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="54e5f-185">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="54e5f-185">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="54e5f-186">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="54e5f-186">SendFiles v0.3.0</span></span>

| <span data-ttu-id="54e5f-187">Key</span><span class="sxs-lookup"><span data-stu-id="54e5f-187">Key</span></span>               | <span data-ttu-id="54e5f-188">値 (型)</span><span class="sxs-lookup"><span data-stu-id="54e5f-188">Value (type)</span></span> | <span data-ttu-id="54e5f-189">説明</span><span class="sxs-lookup"><span data-stu-id="54e5f-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="54e5f-190">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="54e5f-190">sendfile.SendAsync</span></span> | <span data-ttu-id="54e5f-191">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-191">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="54e5f-192">要求ごと</span><span class="sxs-lookup"><span data-stu-id="54e5f-192">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="54e5f-193">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="54e5f-193">Opaque v0.3.0</span></span>

| <span data-ttu-id="54e5f-194">Key</span><span class="sxs-lookup"><span data-stu-id="54e5f-194">Key</span></span>               | <span data-ttu-id="54e5f-195">値 (型)</span><span class="sxs-lookup"><span data-stu-id="54e5f-195">Value (type)</span></span> | <span data-ttu-id="54e5f-196">説明</span><span class="sxs-lookup"><span data-stu-id="54e5f-196">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="54e5f-197">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="54e5f-197">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="54e5f-198">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="54e5f-198">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="54e5f-199">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-199">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="54e5f-200">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="54e5f-200">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="54e5f-201">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="54e5f-201">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="54e5f-202">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="54e5f-202">WebSocket v0.3.0</span></span>

| <span data-ttu-id="54e5f-203">Key</span><span class="sxs-lookup"><span data-stu-id="54e5f-203">Key</span></span>               | <span data-ttu-id="54e5f-204">値 (型)</span><span class="sxs-lookup"><span data-stu-id="54e5f-204">Value (type)</span></span> | <span data-ttu-id="54e5f-205">説明</span><span class="sxs-lookup"><span data-stu-id="54e5f-205">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="54e5f-206">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="54e5f-206">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="54e5f-207">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="54e5f-207">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="54e5f-208">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-208">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="54e5f-209">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="54e5f-209">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="54e5f-210">記述なし</span><span class="sxs-lookup"><span data-stu-id="54e5f-210">Non-spec</span></span> |
| <span data-ttu-id="54e5f-211">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="54e5f-211">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="54e5f-212">[RFC6455 のセクション 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) の手順 5.5 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-212">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="54e5f-213">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="54e5f-213">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="54e5f-214">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="54e5f-215">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="54e5f-215">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="54e5f-216">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="54e5f-217">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="54e5f-217">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="54e5f-218">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54e5f-218">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="54e5f-219">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="54e5f-219">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="54e5f-220">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="54e5f-220">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="54e5f-221">Optional</span><span class="sxs-lookup"><span data-stu-id="54e5f-221">Optional</span></span> |
| <span data-ttu-id="54e5f-222">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="54e5f-222">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="54e5f-223">Optional</span><span class="sxs-lookup"><span data-stu-id="54e5f-223">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="54e5f-224">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="54e5f-224">Additional resources</span></span>

* [<span data-ttu-id="54e5f-225">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="54e5f-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="54e5f-226">サーバー</span><span class="sxs-lookup"><span data-stu-id="54e5f-226">Servers</span></span>](xref:fundamentals/servers/index)

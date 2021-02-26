---
title: ASP.NET Core Blazor SignalR ガイダンス
author: guardrex
description: Blazor SignalR の接続を構成および管理する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/signalr
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279861"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="9d28b-103">ASP.NET Core Blazor SignalR ガイダンス</span><span class="sxs-lookup"><span data-stu-id="9d28b-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="9d28b-104">ASP.NET Core SignalR の構成の一般的なガイダンスについては、ドキュメントの「<xref:signalr/introduction>」領域のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d28b-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="9d28b-105">[ホステッド Blazor WebAssembly ソリューションに追加された](xref:tutorials/signalr-blazor) SignalR を構成する方法については、「<xref:signalr/configuration#configure-server-options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d28b-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="9d28b-106">回線ハンドラーのオプション</span><span class="sxs-lookup"><span data-stu-id="9d28b-106">Circuit handler options</span></span>

<span data-ttu-id="9d28b-107">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="9d28b-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="9d28b-108">次の表に示す <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> を使用して Blazor Server の回線を構成してください。</span><span class="sxs-lookup"><span data-stu-id="9d28b-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="9d28b-109">オプション</span><span class="sxs-lookup"><span data-stu-id="9d28b-109">Option</span></span> | <span data-ttu-id="9d28b-110">Default</span><span class="sxs-lookup"><span data-stu-id="9d28b-110">Default</span></span> | <span data-ttu-id="9d28b-111">説明</span><span class="sxs-lookup"><span data-stu-id="9d28b-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="9d28b-112">回線でハンドルされない例外が発生した場合、または JS 相互運用機能を介した .NET メソッドの呼び出しの結果として例外が発生した場合に、詳細な例外メッセージを JavaScript に送信します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="9d28b-113">100</span><span class="sxs-lookup"><span data-stu-id="9d28b-113">100</span></span> | <span data-ttu-id="9d28b-114">特定のサーバーが一度にメモリに保持する切断された回線の最大数。</span><span class="sxs-lookup"><span data-stu-id="9d28b-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="9d28b-115">3 分</span><span class="sxs-lookup"><span data-stu-id="9d28b-115">3 minutes</span></span> | <span data-ttu-id="9d28b-116">切断された回線が破棄されるまでにメモリに保持される最大時間。</span><span class="sxs-lookup"><span data-stu-id="9d28b-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="9d28b-117">1 分</span><span class="sxs-lookup"><span data-stu-id="9d28b-117">1 minute</span></span> | <span data-ttu-id="9d28b-118">非同期の JavaScript 関数呼び出しがタイムアウトするまでにサーバーが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="9d28b-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="9d28b-119">10</span><span class="sxs-lookup"><span data-stu-id="9d28b-119">10</span></span> | <span data-ttu-id="9d28b-120">堅牢な再接続をサポートするために、サーバーが一定期間、メモリに保持する回線あたりの未確認のレンダリング バッチの最大数。</span><span class="sxs-lookup"><span data-stu-id="9d28b-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="9d28b-121">制限に達すると、クライアントによって 1 つ以上のバッチが確認されるまで、サーバーは新しいレンダリング バッチの生成を停止します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="9d28b-122">オプションの <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> へのデリゲートを使用して `Startup.ConfigureServices` のオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="9d28b-123">次の例では、前の表に示した既定のオプション値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-123">The following example assigns the default option values shown in the preceding table:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="9d28b-124"><xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> を構成するには、<xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> と共に <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> を使用します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="9d28b-125">オプションの説明については、「<xref:signalr/configuration#configure-server-options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d28b-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="9d28b-126">次の例では、既定のオプション値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-126">The following example assigns the default option values:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="9d28b-127">認証のための SignalR のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="9d28b-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="9d28b-128">*このセクションは Blazor WebAssembly に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="9d28b-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="9d28b-129">cookie や HTTP 認証ヘッダーなどの資格情報を送信するように SignalR の基となるクライアントを構成するには:</span><span class="sxs-lookup"><span data-stu-id="9d28b-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="9d28b-130"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> を使用して、クロスオリジン [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求に <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> を設定します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="9d28b-131"><xref:System.Net.Http.HttpMessageHandler> を <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> オプションに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="9d28b-132">詳細については、「<xref:signalr/configuration#configure-additional-options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d28b-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="9d28b-133">UI に接続状態を反映する</span><span class="sxs-lookup"><span data-stu-id="9d28b-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="9d28b-134">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="9d28b-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="9d28b-135">接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="9d28b-136">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="9d28b-137">UI をカスタマイズするには、`_Host.cshtml` Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="9d28b-138">アプリのスタイルシート (`wwwroot/css/app.css` または `wwwroot/css/site.css`) に以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="9d28b-139">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="9d28b-140">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="9d28b-140">CSS class</span></span>                       | <span data-ttu-id="9d28b-141">示す内容&hellip;</span><span class="sxs-lookup"><span data-stu-id="9d28b-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="9d28b-142">接続が失われました。</span><span class="sxs-lookup"><span data-stu-id="9d28b-142">A lost connection.</span></span> <span data-ttu-id="9d28b-143">クライアントによって再接続が試行されています。</span><span class="sxs-lookup"><span data-stu-id="9d28b-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="9d28b-144">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="9d28b-145">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="9d28b-146">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="9d28b-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="9d28b-147">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9d28b-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="9d28b-148">再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="9d28b-149">再接続が拒否されました。</span><span class="sxs-lookup"><span data-stu-id="9d28b-149">Reconnection rejected.</span></span> <span data-ttu-id="9d28b-150">サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。</span><span class="sxs-lookup"><span data-stu-id="9d28b-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="9d28b-151">アプリを再度読み込むには、`location.reload()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="9d28b-152">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9d28b-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="9d28b-153">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="9d28b-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="9d28b-154">クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。</span><span class="sxs-lookup"><span data-stu-id="9d28b-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="9d28b-155">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="9d28b-156">サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</span><span class="sxs-lookup"><span data-stu-id="9d28b-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="9d28b-157">表示モード</span><span class="sxs-lookup"><span data-stu-id="9d28b-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9d28b-158">*このセクションは、ホストされている Blazor WebAssembly および Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="9d28b-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="9d28b-159">Blazor アプリは既定では、サーバー上の UI をプリレンダリングするように設定されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="9d28b-160">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d28b-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9d28b-161">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="9d28b-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="9d28b-162">Blazor Server アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="9d28b-163">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d28b-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="9d28b-164">Blazor 回線を初期化する</span><span class="sxs-lookup"><span data-stu-id="9d28b-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="9d28b-165">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="9d28b-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="9d28b-166">`Pages/_Host.cshtml` ファイル内にある Blazor Server アプリの [SignalR 回線](xref:blazor/hosting-models#circuits)の手動での起動を構成します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="9d28b-167">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="9d28b-168">`Blazor.start` を呼び出すスクリプトを、`blazor.server.js` スクリプトのタグの後の終了 `</body>` タグ内に配置します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="9d28b-169">`autostart` が無効になっている場合、回線に依存しないアプリのすべての側面が正常に動作します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="9d28b-170">たとえば、クライアント側のルーティングは動作します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="9d28b-171">ただし、回線に依存する側面はすべて、`Blazor.start` が呼び出されるまで動作しません。</span><span class="sxs-lookup"><span data-stu-id="9d28b-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="9d28b-172">回線が確立されていなければ、アプリの動作は予測不可能です。</span><span class="sxs-lookup"><span data-stu-id="9d28b-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="9d28b-173">たとえば、回線が切断されている間、コンポーネント メソッドは実行できません。</span><span class="sxs-lookup"><span data-stu-id="9d28b-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="9d28b-174">ドキュメントの準備完了時に Blazor を初期化する</span><span class="sxs-lookup"><span data-stu-id="9d28b-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="9d28b-175">ドキュメントの準備完了時に Blazor アプリを初期化するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9d28b-175">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="9d28b-176">手動で起動した結果として得た `Promise` に連結する</span><span class="sxs-lookup"><span data-stu-id="9d28b-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="9d28b-177">JS 相互運用機能の初期化など、追加のタスクを実行するには、`then` を使用して、手動で Blazor アプリを起動した結果として得た `Promise` に連結します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-signalr-client"></a><span data-ttu-id="9d28b-178">SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="9d28b-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="9d28b-179">ログの記録</span><span class="sxs-lookup"><span data-stu-id="9d28b-179">Logging</span></span>

<span data-ttu-id="9d28b-180">SignalR クライアント ログを構成するには、クライアント ビルダーでログ レベルを指定して `configureLogging` を呼び出す構成オブジェクト (`configureSignalR`) を渡します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="9d28b-181">前の例で、`information` はログ レベル <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> と同じです。</span><span class="sxs-lookup"><span data-stu-id="9d28b-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="9d28b-182">再接続ハンドラーを変更する</span><span class="sxs-lookup"><span data-stu-id="9d28b-182">Modify the reconnection handler</span></span>

<span data-ttu-id="9d28b-183">再接続ハンドラーの回線接続イベントは、次のようなカスタム動作を行うように変更できます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="9d28b-184">接続が切断された場合にユーザーに通知する。</span><span class="sxs-lookup"><span data-stu-id="9d28b-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="9d28b-185">回線が接続されているときに (クライアントから) ログ記録を実行する。</span><span class="sxs-lookup"><span data-stu-id="9d28b-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="9d28b-186">接続イベントを変更するには、次の接続の変更に対してコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="9d28b-187">切断された接続では、`onConnectionDown` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="9d28b-188">確立または再確立された接続では、`onConnectionUp` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="9d28b-189">`onConnectionDown` と `onConnectionUp` の **両方** を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d28b-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="9d28b-190">再接続の再試行回数と間隔を調整する</span><span class="sxs-lookup"><span data-stu-id="9d28b-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="9d28b-191">再接続の再試行の回数と間隔を調整するには、再試行の回数 (`maxRetries`) と、各再試行で許可されるミリ秒単位の期間 (`retryIntervalMilliseconds`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="9d28b-192">再接続の表示を非表示にする、または置き換える</span><span class="sxs-lookup"><span data-stu-id="9d28b-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="9d28b-193">再接続の表示を非表示にするには、再接続ハンドラーの `_reconnectionDisplay` を空のオブジェクト (`{}` または `new Object()`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="9d28b-194">再接続の表示を置き換えるには、前の例の `_reconnectionDisplay` を表示する要素に設定します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="9d28b-195">プレースホルダー `{ELEMENT ID}` は、表示する HTML 要素の ID です。</span><span class="sxs-lookup"><span data-stu-id="9d28b-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9d28b-196">モーダル要素に対して、アプリの CSS (`wwwroot/css/site.css`) の `transition-delay` プロパティを設定して、再接続表示が表示されるまでの遅延時間をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="9d28b-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="9d28b-197">次の例では、移行遅延時間を 500 ミリ秒 (既定値) から 1,000 ミリ秒 (1 秒) に設定しています。</span><span class="sxs-lookup"><span data-stu-id="9d28b-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="9d28b-198">クライアントから Blazor 回線を切断する</span><span class="sxs-lookup"><span data-stu-id="9d28b-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="9d28b-199">既定では、[`unload` ページ イベント](https://developer.mozilla.org/docs/Web/API/Window/unload_event)がトリガーされると、Blazor 回線が切断されます。</span><span class="sxs-lookup"><span data-stu-id="9d28b-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="9d28b-200">クライアント上の他のシナリオで回線を切断するには、適切なイベント ハンドラーで `Blazor.disconnect` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d28b-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="9d28b-201">次の例では、ページが非表示になると、回線が切断されます ([`pagehide` イベント](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event))。</span><span class="sxs-lookup"><span data-stu-id="9d28b-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9d28b-202">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="9d28b-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="9d28b-203">Blazor Server 再接続イベントとコンポーネント ライフサイクル イベント</span><span class="sxs-lookup"><span data-stu-id="9d28b-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)

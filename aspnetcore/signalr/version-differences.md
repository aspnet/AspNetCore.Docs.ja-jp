---
title: SignalRと ASP.NET Core の違いSignalR
author: bradygaster
description: SignalRと ASP.NET Core の違いSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: c37f0a3c528b6285fbf35f41eed781fb76c68de4
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854601"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="e7f0c-103">ASP.NET SignalR と ASP.NET Core の違い SignalR</span><span class="sxs-lookup"><span data-stu-id="e7f0c-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="e7f0c-104">ASP.NET Core SignalR は、ASP.NET のクライアントまたはサーバーと互換性がありません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="e7f0c-105">この記事では、ASP.NET Core で削除または変更された機能について詳しく説明 SignalR します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="e7f0c-106">バージョンを識別する方法 SignalR</span><span class="sxs-lookup"><span data-stu-id="e7f0c-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="e7f0c-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="e7f0c-107">ASP.NET SignalR</span></span> | <span data-ttu-id="e7f0c-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="e7f0c-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="e7f0c-109">**サーバー NuGet パッケージ**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-109">**Server NuGet package**</span></span> | <span data-ttu-id="e7f0c-110">[Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="e7f0c-111">[なし] :</span><span class="sxs-lookup"><span data-stu-id="e7f0c-111">None.</span></span> <span data-ttu-id="e7f0c-112">[AspNetCore](xref:fundamentals/metapackage-app)共有フレームワークに含まれています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="e7f0c-113">**クライアント NuGet パッケージ**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-113">**Client NuGet packages**</span></span> | <span data-ttu-id="e7f0c-114">[Microsoft SignalR . AspNet.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="e7f0c-115">[Microsoft SignalR . AspNet.NODE.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="e7f0c-116">[AspNetCore SignalR .Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="e7f0c-117">**JavaScript クライアント npm パッケージ**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="e7f0c-118">signalr</span><span class="sxs-lookup"><span data-stu-id="e7f0c-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="e7f0c-119">**Java クライアント**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-119">**Java client**</span></span> | <span data-ttu-id="e7f0c-120">[GitHub リポジトリ](https://github.com/SignalR/java-client) (非推奨)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="e7f0c-121">Maven パッケージ [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="e7f0c-122">**サーバーアプリの種類**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-122">**Server app type**</span></span> | <span data-ttu-id="e7f0c-123">ASP.NET (System.web) または OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="e7f0c-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="e7f0c-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7f0c-124">ASP.NET Core</span></span> |
| <span data-ttu-id="e7f0c-125">**サポートされているサーバープラットフォーム**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-125">**Supported server platforms**</span></span> | <span data-ttu-id="e7f0c-126">.NET Framework 4.5 以降</span><span class="sxs-lookup"><span data-stu-id="e7f0c-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="e7f0c-127">.NET Core 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="e7f0c-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="e7f0c-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="e7f0c-128">ASP.NET SignalR</span></span> | <span data-ttu-id="e7f0c-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="e7f0c-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="e7f0c-130">**サーバー NuGet パッケージ**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-130">**Server NuGet package**</span></span> | <span data-ttu-id="e7f0c-131">[Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="e7f0c-132">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="e7f0c-133">[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="e7f0c-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-134">(.NET Framework)</span></span> |
| <span data-ttu-id="e7f0c-135">**クライアント NuGet パッケージ**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-135">**Client NuGet packages**</span></span> | <span data-ttu-id="e7f0c-136">[Microsoft SignalR . AspNet.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="e7f0c-137">[Microsoft SignalR . AspNet.NODE.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="e7f0c-138">[AspNetCore SignalR .Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="e7f0c-139">**JavaScript クライアント npm パッケージ**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="e7f0c-140">signalr</span><span class="sxs-lookup"><span data-stu-id="e7f0c-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="e7f0c-141">**Java クライアント**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-141">**Java client**</span></span> | <span data-ttu-id="e7f0c-142">[GitHub リポジトリ](https://github.com/SignalR/java-client) (非推奨)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="e7f0c-143">Maven パッケージ [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="e7f0c-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="e7f0c-144">**サーバーアプリの種類**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-144">**Server app type**</span></span> | <span data-ttu-id="e7f0c-145">ASP.NET (System.web) または OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="e7f0c-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="e7f0c-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7f0c-146">ASP.NET Core</span></span> |
| <span data-ttu-id="e7f0c-147">**サポートされているサーバープラットフォーム**</span><span class="sxs-lookup"><span data-stu-id="e7f0c-147">**Supported server platforms**</span></span> | <span data-ttu-id="e7f0c-148">.NET Framework 4.5 以降</span><span class="sxs-lookup"><span data-stu-id="e7f0c-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="e7f0c-149">.NET Framework 4.6.1 以降</span><span class="sxs-lookup"><span data-stu-id="e7f0c-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="e7f0c-150">.NET Core 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="e7f0c-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="e7f0c-151">機能の違い</span><span class="sxs-lookup"><span data-stu-id="e7f0c-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="e7f0c-152">自動再接続</span><span class="sxs-lookup"><span data-stu-id="e7f0c-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7f0c-153">ASP.NET の場合 SignalR :</span><span class="sxs-lookup"><span data-stu-id="e7f0c-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="e7f0c-154">既定では、は、 SignalR 接続が切断された場合にサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="e7f0c-155">ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="e7f0c-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="e7f0c-156">自動再接続は、 [.net クライアント](xref:signalr/dotnet-client#automatically-reconnect) と [JavaScript クライアント](xref:signalr/javascript-client#automatically-reconnect)の両方にオプトインされています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e7f0c-157">ASP.NET Core 3.0 より前では、は自動再接続をサポートして SignalR いません。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="e7f0c-158">クライアントが切断されている場合、ユーザーは新しい接続を明示的に開始して再接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="e7f0c-159">ASP.NET では SignalR 、 SignalR 接続が切断された場合、はサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="e7f0c-160">プロトコルのサポート</span><span class="sxs-lookup"><span data-stu-id="e7f0c-160">Protocol support</span></span>

<span data-ttu-id="e7f0c-161">ASP.NET Core SignalR は、 [messagepack](xref:signalr/messagepackhubprotocol)に基づく新しいバイナリプロトコルだけでなく、JSON もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="e7f0c-162">さらに、カスタムプロトコルを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="e7f0c-163">トランスポート</span><span class="sxs-lookup"><span data-stu-id="e7f0c-163">Transports</span></span>

<span data-ttu-id="e7f0c-164">永続的フレーム転送は ASP.NET Core ではサポートされていません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="e7f0c-165">サーバーの相違点</span><span class="sxs-lookup"><span data-stu-id="e7f0c-165">Differences on the server</span></span>

<span data-ttu-id="e7f0c-166">ASP.NET Core SignalR サーバー側ライブラリは、 [AspNetCore](xref:fundamentals/metapackage-app)に含まれています。これは、と MVC プロジェクトの両方の **ASP.NET Core Web アプリケーション** テンプレートで使用されます。 Razor</span><span class="sxs-lookup"><span data-stu-id="e7f0c-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="e7f0c-167">ASP.NET Core SignalR は ASP.NET Core ミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="e7f0c-168">のを呼び出すことによって構成する必要があり <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7f0c-169">ルーティングを構成するには、メソッド内のメソッド呼び出し内のハブにルートをマップし <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e7f0c-170">ルーティングを構成するには、メソッド内のメソッド呼び出し内のハブにルートをマップし <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="e7f0c-171">固定セッション</span><span class="sxs-lookup"><span data-stu-id="e7f0c-171">Sticky sessions</span></span>

<span data-ttu-id="e7f0c-172">ASP.NET のスケールアウトモデルを SignalR 使用すると、クライアントは再接続し、ファーム内の任意のサーバーにメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="e7f0c-173">ASP.NET Core では、 SignalR クライアントは接続中に同じサーバーと通信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="e7f0c-174">Redis を使用したスケールアウトの場合、これは固定セッションが必要であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="e7f0c-175">[Azure SignalR サービス](/azure/azure-signalr/)を使用したスケールアウトでは、サービスがクライアントへの接続を処理するため、固定セッションは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="e7f0c-176">接続ごとに1つのハブ</span><span class="sxs-lookup"><span data-stu-id="e7f0c-176">Single hub per connection</span></span>

<span data-ttu-id="e7f0c-177">ASP.NET Core では SignalR 、接続モデルが単純化されています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="e7f0c-178">複数のハブへのアクセスを共有するために使用される単一の接続ではなく、1つのハブに直接接続されます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="e7f0c-179">ストリーミング</span><span class="sxs-lookup"><span data-stu-id="e7f0c-179">Streaming</span></span>

<span data-ttu-id="e7f0c-180">ASP.NET Core は、 SignalR ハブからクライアントへの [データのストリーミング](xref:signalr/streaming) をサポートするようになりました。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="e7f0c-181">State</span><span class="sxs-lookup"><span data-stu-id="e7f0c-181">State</span></span>

<span data-ttu-id="e7f0c-182">クライアントとハブ (多くの場合はと呼ばれます) の間で任意の状態を渡す機能は `HubState` 削除されており、進行状況メッセージもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="e7f0c-183">現時点では、対応するハブプロキシはありません。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="e7f0c-184">PersistentConnection の削除</span><span class="sxs-lookup"><span data-stu-id="e7f0c-184">PersistentConnection removal</span></span>

<span data-ttu-id="e7f0c-185">ASP.NET Core では SignalR 、 [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) クラスは削除されています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-185">In ASP.NET Core SignalR, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="e7f0c-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="e7f0c-186">GlobalHost</span></span>

<span data-ttu-id="e7f0c-187">ASP.NET Core には、フレームワークに依存関係の挿入 (DI) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="e7f0c-188">サービスは DI を使用して [HubContext](xref:signalr/hubcontext)にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="e7f0c-189">`GlobalHost`を取得するために ASP.NET で使用されるオブジェクトが SignalR `HubContext` ASP.NET Core に存在しません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="e7f0c-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="e7f0c-190">HubPipeline</span></span>

<span data-ttu-id="e7f0c-191">ASP.NET Core SignalR はモジュールをサポートしていません `HubPipeline` 。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="e7f0c-192">クライアントの相違点</span><span class="sxs-lookup"><span data-stu-id="e7f0c-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="e7f0c-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="e7f0c-193">TypeScript</span></span>

<span data-ttu-id="e7f0c-194">ASP.NET Core SignalR クライアントは [TypeScript](https://www.typescriptlang.org/)で記述されています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="e7f0c-195">Javascript [クライアント](xref:signalr/javascript-client)を使用する場合は、javascript または TypeScript で記述できます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="e7f0c-196">JavaScript クライアントは npm でホストされます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7f0c-197">ASP.NET のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを使用して取得されました。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="e7f0c-198">ASP.NET Core のバージョンでは、 [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm パッケージに JavaScript ライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="e7f0c-199">このパッケージは、 **ASP.NET Core Web アプリケーション** テンプレートには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="e7f0c-200">Npm パッケージを取得してインストールするには、npm を使用し `@microsoft/signalr` ます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e7f0c-201">ASP.NET のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを使用して取得されました。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="e7f0c-202">ASP.NET Core のバージョンでは、 [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm パッケージに JavaScript ライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="e7f0c-203">このパッケージは、 **ASP.NET Core Web アプリケーション** テンプレートには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="e7f0c-204">Npm パッケージを取得してインストールするには、npm を使用し `@aspnet/signalr` ます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="e7f0c-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="e7f0c-205">jQuery</span></span>

<span data-ttu-id="e7f0c-206">JQuery への依存関係は削除されましたが、プロジェクトは引き続き jQuery を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="e7f0c-207">Internet Explorer のサポート</span><span class="sxs-lookup"><span data-stu-id="e7f0c-207">Internet Explorer support</span></span>

<span data-ttu-id="e7f0c-208">ASP.NET Core SignalR は Microsoft internet explorer をサポートしていませんが、ASP.NET で SignalR は Microsoft internet explorer 8 以降がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-208">ASP.NET Core SignalR doesn't support Microsoft Internet Explorer, whereas ASP.NET SignalR supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="e7f0c-209">詳細については、「<xref:signalr/supported-platforms#javascript-client>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-209">For more information, see <xref:signalr/supported-platforms#javascript-client>.</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="e7f0c-210">JavaScript クライアントメソッドの構文</span><span class="sxs-lookup"><span data-stu-id="e7f0c-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7f0c-211">JavaScript の構文は、ASP.NET バージョンのから変更されました SignalR 。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-211">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="e7f0c-212">オブジェクトを使用するのではなく `$connection` 、 [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API を使用して接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="e7f0c-213">[On](/javascript/api/@microsoft/signalr/HubConnection#on)メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e7f0c-214">JavaScript の構文は、ASP.NET バージョンのから変更されました SignalR 。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-214">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="e7f0c-215">オブジェクトを使用するのではなく `$connection` 、 [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API を使用して接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="e7f0c-216">[On](/javascript/api/@aspnet/signalr/HubConnection#on)メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="e7f0c-217">クライアントメソッドを作成したら、ハブ接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="e7f0c-218">[Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)メソッドをチェーンして、エラーをログに記録または処理します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="e7f0c-219">ハブプロキシ</span><span class="sxs-lookup"><span data-stu-id="e7f0c-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7f0c-220">ハブプロキシが自動的に生成されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="e7f0c-221">代わりに、メソッド名が文字列として [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API に渡されます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e7f0c-222">ハブプロキシが自動的に生成されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="e7f0c-223">代わりに、メソッド名が文字列として [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API に渡されます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="e7f0c-224">.NET とその他のクライアント</span><span class="sxs-lookup"><span data-stu-id="e7f0c-224">.NET and other clients</span></span>

<span data-ttu-id="e7f0c-225">[AspNetCore. SignalR .クライアント](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet パッケージには、ASP.NET Core 用の .net クライアントライブラリが含まれてい SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-225">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="e7f0c-226"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>ハブへの接続のインスタンスを作成して構築するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-226">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="e7f0c-227">スケールアウトの相違点</span><span class="sxs-lookup"><span data-stu-id="e7f0c-227">Scaleout differences</span></span>

<span data-ttu-id="e7f0c-228">ASP.NET は SignalR SQL Server と Redis をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-228">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="e7f0c-229">ASP.NET Core SignalR では SignalR 、Azure サービスと Redis がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e7f0c-229">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="e7f0c-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e7f0c-230">ASP.NET</span></span>

* [<span data-ttu-id="e7f0c-231">SignalR Azure Service Bus によるスケールアウト</span><span class="sxs-lookup"><span data-stu-id="e7f0c-231">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="e7f0c-232">SignalR Redis によるスケールアウト</span><span class="sxs-lookup"><span data-stu-id="e7f0c-232">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="e7f0c-233">SignalR SQL Server によるスケールアウト</span><span class="sxs-lookup"><span data-stu-id="e7f0c-233">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="e7f0c-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7f0c-234">ASP.NET Core</span></span>

* [<span data-ttu-id="e7f0c-235">Azure SignalR サービス</span><span class="sxs-lookup"><span data-stu-id="e7f0c-235">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="e7f0c-236">Redis バックプレーン</span><span class="sxs-lookup"><span data-stu-id="e7f0c-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="e7f0c-237">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e7f0c-237">Additional resources</span></span>

* [<span data-ttu-id="e7f0c-238">ハブ</span><span class="sxs-lookup"><span data-stu-id="e7f0c-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e7f0c-239">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="e7f0c-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="e7f0c-240">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="e7f0c-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e7f0c-241">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="e7f0c-241">Supported platforms</span></span>](xref:signalr/supported-platforms)

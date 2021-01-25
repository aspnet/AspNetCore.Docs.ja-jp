---
title: ASP.NET Core Kestrel Web サーバーのオプションを構成する
author: rick-anderson
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel のオプションを構成する方法について説明します。
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253869"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="42f04-103">ASP.NET Core Kestrel Web サーバーのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="42f04-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="42f04-104">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="42f04-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="42f04-105">`ConfigureWebHostDefaults` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="42f04-105">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="42f04-106"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="42f04-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="42f04-107">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="42f04-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="42f04-108"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="42f04-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="42f04-109">この記事の後半で示す例では、Kestrel オプションが C# コードで構成されています。</span><span class="sxs-lookup"><span data-stu-id="42f04-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="42f04-110">Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="42f04-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="42f04-111">たとえば、[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)によって、 *appsettings.json* または *appsettings.{Environment}.json* ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="42f04-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="42f04-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> および[エンドポイント構成](xref:fundamentals/servers/kestrel/endpoints)は、構成プロバイダーから構成できます。</span><span class="sxs-lookup"><span data-stu-id="42f04-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="42f04-113">残りの Kestrel 構成は、C# コードで構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42f04-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="42f04-114">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="42f04-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="42f04-115">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="42f04-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="42f04-116">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="42f04-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="42f04-117">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="42f04-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="42f04-118">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="42f04-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="42f04-119">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="42f04-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="42f04-120">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="42f04-120">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="42f04-121">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="42f04-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="42f04-122">全般的な制限</span><span class="sxs-lookup"><span data-stu-id="42f04-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="42f04-123">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="42f04-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="42f04-124">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="42f04-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="42f04-125">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="42f04-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="42f04-126">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="42f04-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="42f04-127">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="42f04-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="42f04-128">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="42f04-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="42f04-129">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="42f04-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="42f04-130">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="42f04-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="42f04-131">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="42f04-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="42f04-132">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="42f04-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="42f04-133">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="42f04-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="42f04-134">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="42f04-134">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="42f04-135">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="42f04-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="42f04-136">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="42f04-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="42f04-137">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="42f04-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="42f04-138">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後で、アプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行される場合は、Kestrel の要求本文サイズの上限は無効になります。</span><span class="sxs-lookup"><span data-stu-id="42f04-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="42f04-139">IIS によって、上限が既に設定されています。</span><span class="sxs-lookup"><span data-stu-id="42f04-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="42f04-140">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="42f04-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="42f04-141">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="42f04-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="42f04-142">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信レートを最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。</span><span class="sxs-lookup"><span data-stu-id="42f04-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="42f04-143">この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="42f04-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="42f04-144">猶予期間を設定すると、TCP のスロースタートが原因で最初のデータ送信が低速レートで行われる接続の切断を回避することができます。</span><span class="sxs-lookup"><span data-stu-id="42f04-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="42f04-145">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="42f04-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="42f04-146">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="42f04-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="42f04-147">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="42f04-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="42f04-148">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="42f04-148">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="42f04-149">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="42f04-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="42f04-150">前のサンプルで参照した <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> は、HTTP/2 要求の `HttpContext.Features` には存在しません。</span><span class="sxs-lookup"><span data-stu-id="42f04-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in `HttpContext.Features` for HTTP/2 requests.</span></span> <span data-ttu-id="42f04-151">要求の多重化がプロトコルでサポートされているため、要求ごとにレート制限を変更することは、一般的に HTTP/2 ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="42f04-151">Modifying rate limits on a per-request basis is generally not supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="42f04-152">ただし、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> は引き続き現在の HTTP/2 要求の `HttpContext.Features` です。これは、HTTP/2 要求に対してであっても、`IHttpMinRequestBodyDataRateFeature.MinDataRate` を `null` に設定すれば、読み取りのレート制限を要求ごとに "*すべて無効*" にできるためです。</span><span class="sxs-lookup"><span data-stu-id="42f04-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="42f04-153">`IHttpMinRequestBodyDataRateFeature.MinDataRate` を読み取ろうとしたり、`null` 以外の値に設定しようとしたりすると、HTTP/2 要求を指定した `NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="42f04-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="42f04-154">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="42f04-154">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="42f04-155">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="42f04-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="42f04-156">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="42f04-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="42f04-157">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="42f04-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="42f04-158">HTTP/2 制限</span><span class="sxs-lookup"><span data-stu-id="42f04-158">HTTP/2 limits</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="42f04-159">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="42f04-159">Maximum streams per connection</span></span>

<span data-ttu-id="42f04-160">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="42f04-160">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="42f04-161">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="42f04-161">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="42f04-162">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="42f04-162">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="42f04-163">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="42f04-163">Header table size</span></span>

<span data-ttu-id="42f04-164">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="42f04-164">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="42f04-165">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="42f04-165">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="42f04-166">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="42f04-166">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="42f04-167">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="42f04-167">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="42f04-168">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="42f04-168">Maximum frame size</span></span>

<span data-ttu-id="42f04-169">`Http2.MaxFrameSize` は、サーバーによって受信または送信された HTTP/2 接続フレーム ペイロードの最大許容サイズを示しています。</span><span class="sxs-lookup"><span data-stu-id="42f04-169">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="42f04-170">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="42f04-170">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="42f04-171">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="42f04-171">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="42f04-172">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="42f04-172">Maximum request header size</span></span>

<span data-ttu-id="42f04-173">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="42f04-173">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="42f04-174">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="42f04-174">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="42f04-175">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="42f04-175">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="42f04-176">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="42f04-176">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="42f04-177">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="42f04-177">Initial connection window size</span></span>

<span data-ttu-id="42f04-178">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="42f04-178">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="42f04-179">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="42f04-179">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="42f04-180">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="42f04-180">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="42f04-181">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="42f04-181">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="42f04-182">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="42f04-182">Initial stream window size</span></span>

<span data-ttu-id="42f04-183">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="42f04-183">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="42f04-184">要求は `Http2.InitialConnectionWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="42f04-184">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="42f04-185">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="42f04-185">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="42f04-186">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="42f04-186">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="42f04-187">HTTP/2 キープ アライブ ping 構成</span><span class="sxs-lookup"><span data-stu-id="42f04-187">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="42f04-188">Kestrel は、接続されているクライアントに HTTP/2 ping を送信するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="42f04-188">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="42f04-189">HTTP/2 ping は複数の目的で機能します。</span><span class="sxs-lookup"><span data-stu-id="42f04-189">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="42f04-190">アイドル状態の接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="42f04-190">Keep idle connections alive.</span></span> <span data-ttu-id="42f04-191">一部のクライアントとプロキシ サーバーでは、アイドル状態の接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="42f04-191">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="42f04-192">HTTP/2 ping は接続におけるアクティビティと見なされ、接続がアイドル状態として閉じられるのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="42f04-192">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="42f04-193">異常な接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="42f04-193">Close unhealthy connections.</span></span> <span data-ttu-id="42f04-194">構成された時間でキープ アライブ ping にクライアントが応答しない接続はサーバーによって閉じられます。</span><span class="sxs-lookup"><span data-stu-id="42f04-194">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="42f04-195">HTTP/2 キープ アライブ ping に関連する 2 つの構成オプション:</span><span class="sxs-lookup"><span data-stu-id="42f04-195">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="42f04-196">`Http2.KeepAlivePingInterval` は、ping 間隔を構成する `TimeSpan` です。</span><span class="sxs-lookup"><span data-stu-id="42f04-196">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping interval.</span></span> <span data-ttu-id="42f04-197">この時間内にフレームが受信されない場合、サーバーからクライアントにキープ アライブ ping が送信されます。</span><span class="sxs-lookup"><span data-stu-id="42f04-197">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="42f04-198">このオプションが `TimeSpan.MaxValue` に設定されているとき、キープ アライブ ping は無効になります。</span><span class="sxs-lookup"><span data-stu-id="42f04-198">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="42f04-199">既定値は `TimeSpan.MaxValue` です。</span><span class="sxs-lookup"><span data-stu-id="42f04-199">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="42f04-200">`Http2.KeepAlivePingTimeout` は、ping タイムアウトを構成する `TimeSpan` です。</span><span class="sxs-lookup"><span data-stu-id="42f04-200">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="42f04-201">このタイムアウト内でサーバーが応答 ping など、いかなるフレームも受信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="42f04-201">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="42f04-202">このオプションが `TimeSpan.MaxValue` に設定されているとき、キープ アライブ タイムアウトは無効になります。</span><span class="sxs-lookup"><span data-stu-id="42f04-202">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="42f04-203">既定値は 20 秒です。</span><span class="sxs-lookup"><span data-stu-id="42f04-203">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="42f04-204">その他のオプション</span><span class="sxs-lookup"><span data-stu-id="42f04-204">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="42f04-205">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="42f04-205">Synchronous I/O</span></span>

<span data-ttu-id="42f04-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="42f04-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="42f04-207">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="42f04-207">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="42f04-208">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42f04-208">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="42f04-209">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="42f04-209">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="42f04-210">同期 I/O を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="42f04-210">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="42f04-211">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="42f04-211">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

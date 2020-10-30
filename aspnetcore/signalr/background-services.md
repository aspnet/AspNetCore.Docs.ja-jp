---
title: :::no-loc(SignalR):::バックグラウンドサービスでのホスト ASP.NET Core
author: bradygaster
description: '.NET Core BackgroundService クラスからクライアントにメッセージを送信する方法について説明 :::no-loc(SignalR)::: します。'
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/background-services
ms.openlocfilehash: 810eff7ccb08ecc22ea255bf0a9fe3d22637179f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060106"
---
# <a name="host-aspnet-core-no-locsignalr-in-background-services"></a><span data-ttu-id="a5f02-103">:::no-loc(SignalR):::バックグラウンドサービスでのホスト ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5f02-103">Host ASP.NET Core :::no-loc(SignalR)::: in background services</span></span>

<span data-ttu-id="a5f02-104">[Brady](https://twitter.com/bradygaster)による</span><span class="sxs-lookup"><span data-stu-id="a5f02-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="a5f02-105">この記事では、次のガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a5f02-105">This article provides guidance for:</span></span>

* <span data-ttu-id="a5f02-106">:::no-loc(SignalR):::ASP.NET Core でホストされたバックグラウンドワーカープロセスを使用してハブをホストする。</span><span class="sxs-lookup"><span data-stu-id="a5f02-106">Hosting :::no-loc(SignalR)::: Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="a5f02-107">.NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)内から接続されたクライアントにメッセージを送信する。</span><span class="sxs-lookup"><span data-stu-id="a5f02-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a5f02-108">[サンプル コードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) ([ダウンロード方法](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a5f02-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="a5f02-109">[サンプル コードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) ([ダウンロード方法](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a5f02-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-no-locsignalr-in-startup"></a><span data-ttu-id="a5f02-110">スタートアップで有効にする :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="a5f02-110">Enable :::no-loc(SignalR)::: in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a5f02-111">:::no-loc(SignalR):::バックグラウンドワーカープロセスのコンテキストで ASP.NET Core ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。</span><span class="sxs-lookup"><span data-stu-id="a5f02-111">Hosting ASP.NET Core :::no-loc(SignalR)::: Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="a5f02-112">メソッドで `Startup.ConfigureServices` を呼び出すと、を `services.Add:::no-loc(SignalR):::` サポートするために必要なサービスが ASP.NET Core 依存関係挿入 (DI) 層に追加され :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-112">In the `Startup.ConfigureServices` method, calling `services.Add:::no-loc(SignalR):::` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support :::no-loc(SignalR):::.</span></span> <span data-ttu-id="a5f02-113">では、 `Startup.Configure` `MapHub` メソッドがコールバックで呼び出され、 `UseEndpoints` ASP.NET Core 要求パイプラインのハブエンドポイントに接続します。</span><span class="sxs-lookup"><span data-stu-id="a5f02-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="a5f02-114">:::no-loc(SignalR):::バックグラウンドワーカープロセスのコンテキストで ASP.NET Core ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。</span><span class="sxs-lookup"><span data-stu-id="a5f02-114">Hosting ASP.NET Core :::no-loc(SignalR)::: Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="a5f02-115">メソッドで `Startup.ConfigureServices` を呼び出すと、を `services.Add:::no-loc(SignalR):::` サポートするために必要なサービスが ASP.NET Core 依存関係挿入 (DI) 層に追加され :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-115">In the `Startup.ConfigureServices` method, calling `services.Add:::no-loc(SignalR):::` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support :::no-loc(SignalR):::.</span></span> <span data-ttu-id="a5f02-116">で `Startup.Configure` は、 `Use:::no-loc(SignalR):::` メソッドを呼び出して、ASP.NET Core 要求パイプラインのハブエンドポイントを接続します。</span><span class="sxs-lookup"><span data-stu-id="a5f02-116">In `Startup.Configure`, the `Use:::no-loc(SignalR):::` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="a5f02-117">前の例では、 `ClockHub` クラスは、 `Hub<T>` 厳密に型指定されたハブを作成するクラスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="a5f02-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="a5f02-118">は、 `ClockHub` `Startup` エンドポイントでの要求に応答するようにクラスで構成されてい `/hubs/clock` ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="a5f02-119">厳密に型指定されたハブの詳細については、「 [ :::no-loc(SignalR)::: のハブを使用](xref:signalr/hubs#strongly-typed-hubs)した ASP.NET Core」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5f02-119">For more information on strongly typed Hubs, see [Use hubs in :::no-loc(SignalR)::: for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="a5f02-120">この機能は[ハブ \<T> ](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub`1)クラスに限定されません。</span><span class="sxs-lookup"><span data-stu-id="a5f02-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub`1) class.</span></span> <span data-ttu-id="a5f02-121">[Dynamichub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.DynamicHub)など、[ハブ](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub)から継承するクラスはすべて機能します。</span><span class="sxs-lookup"><span data-stu-id="a5f02-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="a5f02-122">厳密に型指定されたによって使用されるインターフェイスは、 `ClockHub` `IClock` インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="a5f02-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-no-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="a5f02-123">:::no-loc(SignalR):::バックグラウンドサービスからハブを呼び出す</span><span class="sxs-lookup"><span data-stu-id="a5f02-123">Call a :::no-loc(SignalR)::: Hub from a background service</span></span>

<span data-ttu-id="a5f02-124">起動時に、クラスはを使用して `Worker` `BackgroundService` 有効になり `AddHostedService` ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="a5f02-125">:::no-loc(SignalR):::はフェーズ中にも有効に `Startup` なるため、各ハブは ASP.NET CORE の HTTP 要求パイプライン内の個々のエンドポイントにアタッチされます。各ハブは、サーバー上のによって表され `IHubContext<T>` ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-125">Since :::no-loc(SignalR)::: is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="a5f02-126">ASP.NET Core の DI 機能を使用して、クラス、MVC コントローラークラス、ページモデルなどのホスト層によってインスタンス化された他のクラスは、 `BackgroundService` :::no-loc(Razor)::: 構築中にのインスタンスを受け入れることによって、サーバー側ハブへの参照を取得でき `IHubContext<ClockHub, IClock>` ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or :::no-loc(Razor)::: page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="a5f02-127">`ExecuteAsync`バックグラウンドサービスでメソッドが繰り返し呼び出されると、サーバーの現在の日付と時刻が、を使用して接続されたクライアントに送信され `ClockHub` ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-no-locsignalr-events-with-background-services"></a><span data-ttu-id="a5f02-128">:::no-loc(SignalR):::バックグラウンドサービスを使用したイベントへの対応</span><span class="sxs-lookup"><span data-stu-id="a5f02-128">React to :::no-loc(SignalR)::: events with background services</span></span>

<span data-ttu-id="a5f02-129">用の JavaScript クライアントを使用するシングルページアプリの場合と同様に、.NET デスクトップアプリではを使用してを使用できますが、またはの実装を使用して :::no-loc(SignalR)::: <xref:signalr/dotnet-client> `BackgroundService` `IHostedService` ハブに接続し、イベントに応答することもでき :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-129">Like a Single Page App using the JavaScript client for :::no-loc(SignalR)::: or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to :::no-loc(SignalR)::: Hubs and respond to events.</span></span>

<span data-ttu-id="a5f02-130">クラスは、 `ClockHubClient` インターフェイスとインターフェイスの両方を実装し `IClock` `IHostedService` ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="a5f02-131">このようにして、の実行中にを有効に `Startup` し、サーバーからハブイベントに応答することができます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="a5f02-132">初期化中に、によっ `ClockHubClient` てのインスタンスが作成され、その `HubConnection` `IClock.ShowTime` メソッドがハブのイベントのハンドラーとして有効になり `ShowTime` ます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="a5f02-133">実装では `IHostedService.StartAsync` 、 `HubConnection` が非同期的に開始されます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="a5f02-134">メソッドの実行中 `IHostedService.StopAsync` 、は `HubConnection` 非同期的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="a5f02-135">実装では `IHostedService.StartAsync` 、 `HubConnection` が非同期的に開始されます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="a5f02-136">メソッドの実行中 `IHostedService.StopAsync` 、は `HubConnection` 非同期的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="a5f02-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a5f02-137">その他の資料</span><span class="sxs-lookup"><span data-stu-id="a5f02-137">Additional resources</span></span>

* [<span data-ttu-id="a5f02-138">開始するには</span><span class="sxs-lookup"><span data-stu-id="a5f02-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a5f02-139">ハブ</span><span class="sxs-lookup"><span data-stu-id="a5f02-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a5f02-140">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="a5f02-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="a5f02-141">厳密に型指定されたハブ</span><span class="sxs-lookup"><span data-stu-id="a5f02-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)

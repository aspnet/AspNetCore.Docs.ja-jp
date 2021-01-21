---
title: ASP.NET Core の .NET 汎用ホスト
author: rick-anderson
description: ASP.NET Core アプリで .NET Core 汎用ホストを使用します。  汎用ホストが担当するのは、アプリの起動および有効期間の管理です。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: b99b0f0ab6e67ac84bf1232ff6681c5edd54ffb9
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253177"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="e95d9-104">ASP.NET Core の .NET 汎用ホスト</span><span class="sxs-lookup"><span data-stu-id="e95d9-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e95d9-105">ASP.NET Core テンプレートでは、.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="e95d9-106">このトピックでは、ASP.NET Core での .NET 汎用ホストの使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="e95d9-107">コンソール アプリで .NET 汎用ホストを使用する方法の詳細については、「[.NET 汎用ホスト](/dotnet/core/extensions/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="e95d9-108">ホストの定義</span><span class="sxs-lookup"><span data-stu-id="e95d9-108">Host definition</span></span>

<span data-ttu-id="e95d9-109">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="e95d9-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="e95d9-110">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="e95d9-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="e95d9-111">ログの記録</span><span class="sxs-lookup"><span data-stu-id="e95d9-111">Logging</span></span>
* <span data-ttu-id="e95d9-112">構成</span><span class="sxs-lookup"><span data-stu-id="e95d9-112">Configuration</span></span>
* <span data-ttu-id="e95d9-113">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="e95d9-113">`IHostedService` implementations</span></span>

<span data-ttu-id="e95d9-114">ホストが起動すると、サービス コンテナーのホステッド サービスのコレクションに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装で <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="e95d9-115">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="e95d9-116">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e95d9-117">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="e95d9-117">Set up a host</span></span>

<span data-ttu-id="e95d9-118">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="e95d9-119">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="e95d9-119">The `Main` method:</span></span>

* <span data-ttu-id="e95d9-120">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="e95d9-121">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="e95d9-122">ASP.NET Core の web テンプレートでは、ホストを作成するために、次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="e95d9-123">次のコードを実行すると、DI コンテナーに追加された `IHostedService` の実装を使用して、非 HTTP ワークロードが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="e95d9-124">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="e95d9-125">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="e95d9-126">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="e95d9-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="e95d9-127">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="e95d9-128">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="e95d9-128">Default builder settings</span></span>

<span data-ttu-id="e95d9-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="e95d9-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="e95d9-130">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory*> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="e95d9-131">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="e95d9-132">プレフィックス `DOTNET_` が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="e95d9-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="e95d9-133">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="e95d9-133">Command-line arguments.</span></span>
* <span data-ttu-id="e95d9-134">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="e95d9-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e95d9-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="e95d9-136">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="e95d9-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="e95d9-137">`Development` 環境でアプリが実行される場合に使用される[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="e95d9-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="e95d9-138">環境変数。</span><span class="sxs-lookup"><span data-stu-id="e95d9-138">Environment variables.</span></span>
  * <span data-ttu-id="e95d9-139">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="e95d9-139">Command-line arguments.</span></span>
* <span data-ttu-id="e95d9-140">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="e95d9-141">コンソール</span><span class="sxs-lookup"><span data-stu-id="e95d9-141">Console</span></span>
  * <span data-ttu-id="e95d9-142">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e95d9-142">Debug</span></span>
  * <span data-ttu-id="e95d9-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="e95d9-143">EventSource</span></span>
  * <span data-ttu-id="e95d9-144">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="e95d9-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="e95d9-145">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="e95d9-146"><xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="e95d9-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="e95d9-147">プレフィックス `ASPNETCORE_` が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="e95d9-148">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="e95d9-149">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel/options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
* <span data-ttu-id="e95d9-150">[Host Filtering middleware](xref:fundamentals/servers/kestrel/host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel/host-filtering).</span></span>
* <span data-ttu-id="e95d9-151">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` が `true` の場合、[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="e95d9-152">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-152">Enables IIS integration.</span></span> <span data-ttu-id="e95d9-153">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="e95d9-154">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e95d9-155">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="e95d9-155">Framework-provided services</span></span>

<span data-ttu-id="e95d9-156">以下のサービスは、自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="e95d9-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="e95d9-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="e95d9-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e95d9-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="e95d9-160">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="e95d9-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="e95d9-162">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="e95d9-163">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="e95d9-164">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="e95d9-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="e95d9-165">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="e95d9-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-166">IHostLifetime</span></span>

<span data-ttu-id="e95d9-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="e95d9-168">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-168">The last implementation registered is used.</span></span>

<span data-ttu-id="e95d9-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="e95d9-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e95d9-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="e95d9-171"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="e95d9-172">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="e95d9-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e95d9-173">IHostEnvironment</span></span>

<span data-ttu-id="e95d9-174">次の設定に関する情報を取得するため、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="e95d9-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e95d9-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="e95d9-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e95d9-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="e95d9-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e95d9-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="e95d9-178">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e95d9-179">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="e95d9-179">Host configuration</span></span>

<span data-ttu-id="e95d9-180">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="e95d9-181">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="e95d9-182">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="e95d9-183">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e95d9-184">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e95d9-185">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e95d9-186">プレフィックス `DOTNET_` を持つ環境変数プロバイダーとコマンドライン引数が、`CreateDefaultBuilder` によって組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e95d9-187">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="e95d9-188">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e95d9-189">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e95d9-190">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="e95d9-191">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="e95d9-191">App configuration</span></span>

<span data-ttu-id="e95d9-192">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e95d9-193">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e95d9-194">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="e95d9-195">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="e95d9-196">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="e95d9-197">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="e95d9-198">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="e95d9-198">Settings for all app types</span></span>

<span data-ttu-id="e95d9-199">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="e95d9-200">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="e95d9-201">詳細については、「[既定の builder 設定](#default-builder-settings)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="e95d9-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e95d9-202">ApplicationName</span></span>

<span data-ttu-id="e95d9-203">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="e95d9-204">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="e95d9-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e95d9-205">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-205">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-206">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="e95d9-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="e95d9-207">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="e95d9-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="e95d9-208">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="e95d9-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e95d9-209">ContentRoot</span></span>

<span data-ttu-id="e95d9-210">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="e95d9-211">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="e95d9-212">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e95d9-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e95d9-213">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-213">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-214">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="e95d9-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="e95d9-215">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="e95d9-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="e95d9-216">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="e95d9-217">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="e95d9-217">For more information, see:</span></span>

* [<span data-ttu-id="e95d9-218">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="e95d9-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="e95d9-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e95d9-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="e95d9-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e95d9-220">EnvironmentName</span></span>

<span data-ttu-id="e95d9-221">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="e95d9-222">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e95d9-223">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="e95d9-224">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="e95d9-224">**Key**: `environment`</span></span>  
<span data-ttu-id="e95d9-225">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-225">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-226">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="e95d9-226">**Default**: `Production`</span></span>  
<span data-ttu-id="e95d9-227">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="e95d9-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="e95d9-228">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="e95d9-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="e95d9-229">ShutdownTimeout</span></span>

<span data-ttu-id="e95d9-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e95d9-231">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-231">The default value is five seconds.</span></span>  <span data-ttu-id="e95d9-232">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="e95d9-233">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="e95d9-234">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="e95d9-235">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="e95d9-236">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="e95d9-237">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="e95d9-238">**キー**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="e95d9-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="e95d9-239">**型**: `int`</span><span class="sxs-lookup"><span data-stu-id="e95d9-239">**Type**: `int`</span></span>  
<span data-ttu-id="e95d9-240">**既定**:5 秒</span><span class="sxs-lookup"><span data-stu-id="e95d9-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="e95d9-241">**環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="e95d9-242">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="e95d9-243">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="e95d9-244">変更時にアプリ構成の再度読み込みを無効にする</span><span class="sxs-lookup"><span data-stu-id="e95d9-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="e95d9-245">[既定](xref:fundamentals/configuration/index#default)では、 *appsettings.json* と *appsettings.{Environment}.json* は、ファイルの変更時に再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="e95d9-246">ASP.NET Core 5.0 以降でこの再度読み込み動作を無効にするには、`hostBuilder:reloadConfigOnChange` キーを `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="e95d9-247">**キー**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e95d9-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="e95d9-248">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-249">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="e95d9-249">**Default**: `true`</span></span>  
<span data-ttu-id="e95d9-250">**コマンドライン引数**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e95d9-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="e95d9-251">**環境変数**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e95d9-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="e95d9-252">コロン (`:`) の区切り記号は、すべてのプラットフォームの環境変数階層キーには対応していません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="e95d9-253">詳細については、「[環境変数](xref:fundamentals/configuration/index#environment-variables)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="e95d9-254">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="e95d9-254">Settings for web apps</span></span>

<span data-ttu-id="e95d9-255">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="e95d9-256">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="e95d9-257">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="e95d9-258">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="e95d9-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="e95d9-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="e95d9-259">CaptureStartupErrors</span></span>

<span data-ttu-id="e95d9-260">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="e95d9-261">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="e95d9-262">**キー**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="e95d9-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="e95d9-263">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-264">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="e95d9-265">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="e95d9-266">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="e95d9-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="e95d9-267">DetailedErrors</span></span>

<span data-ttu-id="e95d9-268">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="e95d9-269">**キー**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="e95d9-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="e95d9-270">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-271">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="e95d9-271">**Default**: `false`</span></span>  
<span data-ttu-id="e95d9-272">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="e95d9-273">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="e95d9-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="e95d9-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="e95d9-275">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="e95d9-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="e95d9-276">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="e95d9-277">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="e95d9-278">**キー**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e95d9-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="e95d9-279">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-279">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-280">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="e95d9-280">**Default**: Empty string</span></span>  
<span data-ttu-id="e95d9-281">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e95d9-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="e95d9-282">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="e95d9-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="e95d9-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="e95d9-284">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="e95d9-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="e95d9-285">**キー**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e95d9-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="e95d9-286">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-286">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-287">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="e95d9-287">**Default**: Empty string</span></span>  
<span data-ttu-id="e95d9-288">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e95d9-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="e95d9-289">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="e95d9-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="e95d9-290">HTTPS_Port</span></span>

<span data-ttu-id="e95d9-291">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="e95d9-291">The HTTPS redirect port.</span></span> <span data-ttu-id="e95d9-292">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e95d9-293">**キー**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="e95d9-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="e95d9-294">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-294">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-295">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="e95d9-296">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="e95d9-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="e95d9-297">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="e95d9-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="e95d9-298">PreferHostingUrls</span></span>

<span data-ttu-id="e95d9-299">`IServer` の実装で構成されている URL ではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="e95d9-300">**キー**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="e95d9-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="e95d9-301">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-302">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="e95d9-302">**Default**: `true`</span></span>  
<span data-ttu-id="e95d9-303">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="e95d9-304">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="e95d9-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="e95d9-305">PreventHostingStartup</span></span>

<span data-ttu-id="e95d9-306">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="e95d9-307">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="e95d9-308">**キー**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e95d9-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="e95d9-309">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-310">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="e95d9-310">**Default**: `false`</span></span>  
<span data-ttu-id="e95d9-311">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e95d9-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="e95d9-312">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="e95d9-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="e95d9-313">StartupAssembly</span></span>

<span data-ttu-id="e95d9-314">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="e95d9-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="e95d9-315">**キー**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="e95d9-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="e95d9-316">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-316">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-317">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="e95d9-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="e95d9-318">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="e95d9-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="e95d9-319">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="e95d9-320">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="e95d9-321">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="e95d9-322">URL</span><span class="sxs-lookup"><span data-stu-id="e95d9-322">URLs</span></span>

<span data-ttu-id="e95d9-323">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="e95d9-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="e95d9-324">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="e95d9-325">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="e95d9-326">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="e95d9-327">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="e95d9-328">**キー**: `urls`</span><span class="sxs-lookup"><span data-stu-id="e95d9-328">**Key**: `urls`</span></span>  
<span data-ttu-id="e95d9-329">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-329">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-330">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e95d9-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="e95d9-331">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="e95d9-332">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="e95d9-333">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="e95d9-334">詳細については、「<xref:fundamentals/servers/kestrel/endpoints>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-334">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>

### <a name="webroot"></a><span data-ttu-id="e95d9-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e95d9-335">WebRoot</span></span>

<span data-ttu-id="e95d9-336">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) プロパティでは、アプリの静的アセットへの相対パスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="e95d9-337">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="e95d9-338">**キー**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="e95d9-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="e95d9-339">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-339">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-340">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="e95d9-341">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="e95d9-342">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="e95d9-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="e95d9-343">この値を設定するには、環境変数を使用するか、または `IWebHostBuilder` 上で `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="e95d9-344">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="e95d9-344">For more information, see:</span></span>

* [<span data-ttu-id="e95d9-345">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="e95d9-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="e95d9-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e95d9-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="e95d9-347">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="e95d9-347">Manage the host lifetime</span></span>

<span data-ttu-id="e95d9-348">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="e95d9-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="e95d9-349">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e95d9-350">実行</span><span class="sxs-lookup"><span data-stu-id="e95d9-350">Run</span></span>

<span data-ttu-id="e95d9-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="e95d9-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-352">RunAsync</span></span>

<span data-ttu-id="e95d9-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="e95d9-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-354">RunConsoleAsync</span></span>

<span data-ttu-id="e95d9-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="e95d9-356">[開始]</span><span class="sxs-lookup"><span data-stu-id="e95d9-356">Start</span></span>

<span data-ttu-id="e95d9-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="e95d9-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-358">StartAsync</span></span>

<span data-ttu-id="e95d9-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="e95d9-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e95d9-361">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="e95d9-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-362">StopAsync</span></span>

<span data-ttu-id="e95d9-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="e95d9-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e95d9-364">WaitForShutdown</span></span>

<span data-ttu-id="e95d9-365"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガーされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="e95d9-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="e95d9-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="e95d9-368">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="e95d9-368">External control</span></span>

<span data-ttu-id="e95d9-369">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e95d9-370">ASP.NET Core テンプレートでは、.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="e95d9-371">このトピックでは、ASP.NET Core での .NET 汎用ホストの使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="e95d9-372">コンソール アプリで .NET 汎用ホストを使用する方法の詳細については、「[.NET 汎用ホスト](/dotnet/core/extensions/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="e95d9-373">ホストの定義</span><span class="sxs-lookup"><span data-stu-id="e95d9-373">Host definition</span></span>

<span data-ttu-id="e95d9-374">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="e95d9-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="e95d9-375">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="e95d9-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="e95d9-376">ログの記録</span><span class="sxs-lookup"><span data-stu-id="e95d9-376">Logging</span></span>
* <span data-ttu-id="e95d9-377">構成</span><span class="sxs-lookup"><span data-stu-id="e95d9-377">Configuration</span></span>
* <span data-ttu-id="e95d9-378">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="e95d9-378">`IHostedService` implementations</span></span>

<span data-ttu-id="e95d9-379">ホストが起動すると、サービス コンテナーのホステッド サービスのコレクションに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装で <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="e95d9-380">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="e95d9-381">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e95d9-382">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="e95d9-382">Set up a host</span></span>

<span data-ttu-id="e95d9-383">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="e95d9-384">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="e95d9-384">The `Main` method:</span></span>

* <span data-ttu-id="e95d9-385">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="e95d9-386">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="e95d9-387">ASP.NET Core の web テンプレートでは、汎用ホストを作成するために次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="e95d9-388">次のコードでは、HTTP 以外のワークロードを使用して、汎用ホストを作成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="e95d9-389">次のコードでは、`IHostedService` の実装が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-389">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="e95d9-390">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="e95d9-391">上記のコードは、ASP.NET Core テンプレートによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="e95d9-392">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="e95d9-393">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="e95d9-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="e95d9-394">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="e95d9-395">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="e95d9-395">Default builder settings</span></span>

<span data-ttu-id="e95d9-396"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> メソッド:</span><span class="sxs-lookup"><span data-stu-id="e95d9-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="e95d9-397">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory%2A> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="e95d9-398">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="e95d9-399">プレフィックス `DOTNET_` が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="e95d9-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="e95d9-400">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="e95d9-400">Command-line arguments.</span></span>
* <span data-ttu-id="e95d9-401">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="e95d9-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e95d9-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="e95d9-403">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="e95d9-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="e95d9-404">`Development` 環境でアプリが実行される場合に使用される[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="e95d9-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="e95d9-405">環境変数。</span><span class="sxs-lookup"><span data-stu-id="e95d9-405">Environment variables.</span></span>
  * <span data-ttu-id="e95d9-406">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="e95d9-406">Command-line arguments.</span></span>
* <span data-ttu-id="e95d9-407">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="e95d9-408">コンソール</span><span class="sxs-lookup"><span data-stu-id="e95d9-408">Console</span></span>
  * <span data-ttu-id="e95d9-409">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e95d9-409">Debug</span></span>
  * <span data-ttu-id="e95d9-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="e95d9-410">EventSource</span></span>
  * <span data-ttu-id="e95d9-411">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="e95d9-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="e95d9-412">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="e95d9-413">`ConfigureWebHostDefaults` メソッド:</span><span class="sxs-lookup"><span data-stu-id="e95d9-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="e95d9-414">プレフィックス `ASPNETCORE_` が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="e95d9-415">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="e95d9-416">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="e95d9-417">[Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="e95d9-418">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` が `true` の場合、[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="e95d9-419">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-419">Enables IIS integration.</span></span> <span data-ttu-id="e95d9-420">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="e95d9-421">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e95d9-422">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="e95d9-422">Framework-provided services</span></span>

<span data-ttu-id="e95d9-423">以下のサービスは、自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="e95d9-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="e95d9-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="e95d9-426">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e95d9-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="e95d9-427">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="e95d9-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="e95d9-429">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="e95d9-430">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="e95d9-431">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="e95d9-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="e95d9-432">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="e95d9-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-433">IHostLifetime</span></span>

<span data-ttu-id="e95d9-434"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="e95d9-435">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-435">The last implementation registered is used.</span></span>

<span data-ttu-id="e95d9-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="e95d9-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e95d9-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="e95d9-438"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="e95d9-439">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="e95d9-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e95d9-440">IHostEnvironment</span></span>

<span data-ttu-id="e95d9-441">次の設定に関する情報を取得するため、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="e95d9-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e95d9-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="e95d9-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e95d9-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="e95d9-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e95d9-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="e95d9-445">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e95d9-446">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="e95d9-446">Host configuration</span></span>

<span data-ttu-id="e95d9-447">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="e95d9-448">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="e95d9-449">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="e95d9-450">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="e95d9-451">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e95d9-452">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e95d9-453">プレフィックス `DOTNET_` を持つ環境変数プロバイダーとコマンドライン引数が、`CreateDefaultBuilder` によって組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e95d9-454">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="e95d9-455">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e95d9-456">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e95d9-457">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="e95d9-458">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="e95d9-458">App configuration</span></span>

<span data-ttu-id="e95d9-459">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e95d9-460">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e95d9-461">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="e95d9-462">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="e95d9-463">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="e95d9-464">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="e95d9-465">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="e95d9-465">Settings for all app types</span></span>

<span data-ttu-id="e95d9-466">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="e95d9-467">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="e95d9-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e95d9-468">ApplicationName</span></span>

<span data-ttu-id="e95d9-469">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="e95d9-470">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="e95d9-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e95d9-471">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-471">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-472">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="e95d9-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="e95d9-473">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="e95d9-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="e95d9-474">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="e95d9-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e95d9-475">ContentRoot</span></span>

<span data-ttu-id="e95d9-476">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="e95d9-477">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="e95d9-478">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e95d9-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e95d9-479">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-479">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-480">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="e95d9-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="e95d9-481">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="e95d9-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="e95d9-482">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="e95d9-483">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="e95d9-483">For more information, see:</span></span>

* [<span data-ttu-id="e95d9-484">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="e95d9-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="e95d9-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e95d9-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="e95d9-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e95d9-486">EnvironmentName</span></span>

<span data-ttu-id="e95d9-487">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="e95d9-488">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e95d9-489">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="e95d9-490">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="e95d9-490">**Key**: `environment`</span></span>  
<span data-ttu-id="e95d9-491">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-491">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-492">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="e95d9-492">**Default**: `Production`</span></span>  
<span data-ttu-id="e95d9-493">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="e95d9-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="e95d9-494">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="e95d9-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="e95d9-495">ShutdownTimeout</span></span>

<span data-ttu-id="e95d9-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e95d9-497">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-497">The default value is five seconds.</span></span>  <span data-ttu-id="e95d9-498">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="e95d9-499">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="e95d9-500">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="e95d9-501">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="e95d9-502">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="e95d9-503">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="e95d9-504">**キー**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="e95d9-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="e95d9-505">**型**: `int`</span><span class="sxs-lookup"><span data-stu-id="e95d9-505">**Type**: `int`</span></span>  
<span data-ttu-id="e95d9-506">**既定**:5 秒</span><span class="sxs-lookup"><span data-stu-id="e95d9-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="e95d9-507">**環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="e95d9-508">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="e95d9-509">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="e95d9-510">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="e95d9-510">Settings for web apps</span></span>

<span data-ttu-id="e95d9-511">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="e95d9-512">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="e95d9-513">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="e95d9-514">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="e95d9-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="e95d9-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="e95d9-515">CaptureStartupErrors</span></span>

<span data-ttu-id="e95d9-516">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="e95d9-517">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="e95d9-518">**キー**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="e95d9-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="e95d9-519">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-520">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="e95d9-521">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="e95d9-522">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="e95d9-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="e95d9-523">DetailedErrors</span></span>

<span data-ttu-id="e95d9-524">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="e95d9-525">**キー**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="e95d9-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="e95d9-526">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-527">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="e95d9-527">**Default**: `false`</span></span>  
<span data-ttu-id="e95d9-528">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="e95d9-529">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="e95d9-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="e95d9-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="e95d9-531">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="e95d9-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="e95d9-532">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="e95d9-533">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="e95d9-534">**キー**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e95d9-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="e95d9-535">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-535">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-536">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="e95d9-536">**Default**: Empty string</span></span>  
<span data-ttu-id="e95d9-537">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e95d9-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="e95d9-538">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="e95d9-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="e95d9-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="e95d9-540">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="e95d9-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="e95d9-541">**キー**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e95d9-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="e95d9-542">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-542">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-543">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="e95d9-543">**Default**: Empty string</span></span>  
<span data-ttu-id="e95d9-544">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e95d9-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="e95d9-545">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="e95d9-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="e95d9-546">HTTPS_Port</span></span>

<span data-ttu-id="e95d9-547">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="e95d9-547">The HTTPS redirect port.</span></span> <span data-ttu-id="e95d9-548">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e95d9-549">**キー**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="e95d9-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="e95d9-550">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-550">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-551">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="e95d9-552">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="e95d9-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="e95d9-553">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="e95d9-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="e95d9-554">PreferHostingUrls</span></span>

<span data-ttu-id="e95d9-555">`IServer` の実装で構成されている URL ではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="e95d9-556">**キー**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="e95d9-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="e95d9-557">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-558">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="e95d9-558">**Default**: `true`</span></span>  
<span data-ttu-id="e95d9-559">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="e95d9-560">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="e95d9-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="e95d9-561">PreventHostingStartup</span></span>

<span data-ttu-id="e95d9-562">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="e95d9-563">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="e95d9-564">**キー**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e95d9-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="e95d9-565">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e95d9-566">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="e95d9-566">**Default**: `false`</span></span>  
<span data-ttu-id="e95d9-567">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e95d9-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="e95d9-568">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="e95d9-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="e95d9-569">StartupAssembly</span></span>

<span data-ttu-id="e95d9-570">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="e95d9-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="e95d9-571">**キー**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="e95d9-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="e95d9-572">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-572">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-573">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="e95d9-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="e95d9-574">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="e95d9-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="e95d9-575">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="e95d9-576">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="e95d9-577">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="e95d9-578">URL</span><span class="sxs-lookup"><span data-stu-id="e95d9-578">URLs</span></span>

<span data-ttu-id="e95d9-579">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="e95d9-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="e95d9-580">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="e95d9-581">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="e95d9-582">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="e95d9-583">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="e95d9-584">**キー**: `urls`</span><span class="sxs-lookup"><span data-stu-id="e95d9-584">**Key**: `urls`</span></span>  
<span data-ttu-id="e95d9-585">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-585">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-586">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e95d9-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="e95d9-587">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="e95d9-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="e95d9-588">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="e95d9-589">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="e95d9-590">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="e95d9-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e95d9-591">WebRoot</span></span>

<span data-ttu-id="e95d9-592">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) プロパティでは、アプリの静的アセットへの相対パスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="e95d9-593">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="e95d9-594">**キー**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="e95d9-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="e95d9-595">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-595">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-596">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="e95d9-597">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="e95d9-598">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="e95d9-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="e95d9-599">この値を設定するには、環境変数を使用するか、または `IWebHostBuilder` 上で `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="e95d9-600">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="e95d9-600">For more information, see:</span></span>

* [<span data-ttu-id="e95d9-601">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="e95d9-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="e95d9-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e95d9-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="e95d9-603">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="e95d9-603">Manage the host lifetime</span></span>

<span data-ttu-id="e95d9-604">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="e95d9-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="e95d9-605">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e95d9-606">実行</span><span class="sxs-lookup"><span data-stu-id="e95d9-606">Run</span></span>

<span data-ttu-id="e95d9-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="e95d9-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-608">RunAsync</span></span>

<span data-ttu-id="e95d9-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="e95d9-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-610">RunConsoleAsync</span></span>

<span data-ttu-id="e95d9-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="e95d9-612">[開始]</span><span class="sxs-lookup"><span data-stu-id="e95d9-612">Start</span></span>

<span data-ttu-id="e95d9-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="e95d9-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-614">StartAsync</span></span>

<span data-ttu-id="e95d9-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="e95d9-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e95d9-617">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="e95d9-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-618">StopAsync</span></span>

<span data-ttu-id="e95d9-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="e95d9-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e95d9-620">WaitForShutdown</span></span>

<span data-ttu-id="e95d9-621"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガーされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="e95d9-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="e95d9-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="e95d9-624">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="e95d9-624">External control</span></span>

<span data-ttu-id="e95d9-625">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e95d9-626">ASP.NET Core アプリはホストを構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="e95d9-627">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="e95d9-628">この記事では、HTTP 要求を処理しないアプリに使用される、ASP.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) について説明します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="e95d9-629">汎用ホストの目的は、Web ホスト API から HTTP パイプラインを切り離して、多様なホスト シナリオを有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="e95d9-630">メッセージング、バックグラウンド タスク、汎用ホストに基づくその他の HTTP ワークロードに対して、構成、依存関係の挿入 (DI)、ログなどの横断的機能によるメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="e95d9-631">汎用ホストは ASP.NET Core 2.1 の新機能であり、Web ホスティングのシナリオには適していません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="e95d9-632">Web ホスティングのシナリオの場合は、[Web ホスト](xref:fundamentals/host/web-host)を使ってください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="e95d9-633">汎用ホストは将来のリリースで Web ホストに代わるものであり、HTTP と非 HTTP 両方のシナリオのプライマリ ホスト API として機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="e95d9-634">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e95d9-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e95d9-635">[Visual Studio Code](https://code.visualstudio.com/) でサンプル アプリを実行するときは、"*外部ターミナルまたは統合ターミナル*" を使います。</span><span class="sxs-lookup"><span data-stu-id="e95d9-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="e95d9-636">`internalConsole` ではサンプルを実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="e95d9-637">Visual Studio Code でコンソールを設定するには:</span><span class="sxs-lookup"><span data-stu-id="e95d9-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="e95d9-638">*.vscode/launch.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="e95d9-639">**.NET Core Launch (console)** の構成で、**console** エントリを探します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="e95d9-640">値を `externalTerminal` または `integratedTerminal` に設定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="e95d9-641">はじめに</span><span class="sxs-lookup"><span data-stu-id="e95d9-641">Introduction</span></span>

<span data-ttu-id="e95d9-642">汎用ホスト ライブラリは、<xref:Microsoft.Extensions.Hosting> 名前空間で使用でき、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) パッケージで提供されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="e95d9-643">`Microsoft.Extensions.Hosting` パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます (ASP.NET Core 2.1 以降)。</span><span class="sxs-lookup"><span data-stu-id="e95d9-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="e95d9-644"><xref:Microsoft.Extensions.Hosting.IHostedService> がコード実行のエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="e95d9-645"><xref:Microsoft.Extensions.Hosting.IHostedService> の各実装は、[ConfigureServices でのサービス登録](#configureservices)の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="e95d9-646">ホストが開始するときは <xref:Microsoft.Extensions.Hosting.IHostedService> ごとに <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> が呼び出され、ホストが正常に終了するときは登録と逆の順序で <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e95d9-647">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="e95d9-647">Set up a host</span></span>

<span data-ttu-id="e95d9-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> は、ライブラリとアプリがホストの初期化、ビルド、実行に使用するメイン コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="e95d9-649">オプション</span><span class="sxs-lookup"><span data-stu-id="e95d9-649">Options</span></span>

<span data-ttu-id="e95d9-650"><xref:Microsoft.Extensions.Hosting.IHost> の <xref:Microsoft.Extensions.Hosting.HostOptions> 構成オプションです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="e95d9-651">シャットダウン タイムアウト</span><span class="sxs-lookup"><span data-stu-id="e95d9-651">Shutdown timeout</span></span>

<span data-ttu-id="e95d9-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> によって <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e95d9-653">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="e95d9-653">The default value is five seconds.</span></span>

<span data-ttu-id="e95d9-654">次に示す `Program.Main` のオプション構成では、既定の 5 秒のシャットダウン タイムアウトが 20 秒に延長されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="e95d9-655">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="e95d9-655">Default services</span></span>

<span data-ttu-id="e95d9-656">次のサービスは、ホストの初期化中に登録されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="e95d9-657">[環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="e95d9-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="e95d9-658">[構成](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="e95d9-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="e95d9-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="e95d9-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="e95d9-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="e95d9-661">[オプション](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="e95d9-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="e95d9-662">[ログ](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="e95d9-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e95d9-663">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="e95d9-663">Host configuration</span></span>

<span data-ttu-id="e95d9-664">ホストの構成は、次のようにして作成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-664">Host configuration is created by:</span></span>

* <span data-ttu-id="e95d9-665"><xref:Microsoft.Extensions.Hosting.IHostBuilder> で拡張メソッドを呼び出して、[コンテンツ ルート](#content-root)と[環境](#environment)を設定する。</span><span class="sxs-lookup"><span data-stu-id="e95d9-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="e95d9-666"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> の構成プロバイダーから構成を読み取る。</span><span class="sxs-lookup"><span data-stu-id="e95d9-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="e95d9-667">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="e95d9-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="e95d9-668">アプリケーション キー (名前)</span><span class="sxs-lookup"><span data-stu-id="e95d9-668">Application key (name)</span></span>

<span data-ttu-id="e95d9-669">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="e95d9-670">値を明示的に設定するには、[HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="e95d9-671">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="e95d9-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e95d9-672">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-672">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-673">**既定**:アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="e95d9-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="e95d9-674">**次を使用して設定**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="e95d9-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="e95d9-675">**環境変数**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` は [オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e95d9-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="e95d9-676">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="e95d9-676">Content root</span></span>

<span data-ttu-id="e95d9-677">この設定では、ホストがコンテンツ ファイルの検索を開始する場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="e95d9-678">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e95d9-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e95d9-679">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-679">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-680">**既定**:既定でアプリ アセンブリが存在するフォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="e95d9-681">**次を使用して設定**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="e95d9-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="e95d9-682">**環境変数**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` は [オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e95d9-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="e95d9-683">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="e95d9-684">詳細については、[基礎: コンテンツ ルート](xref:fundamentals/index#content-root)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="e95d9-685">環境</span><span class="sxs-lookup"><span data-stu-id="e95d9-685">Environment</span></span>

<span data-ttu-id="e95d9-686">アプリの[環境](xref:fundamentals/environments)を設定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e95d9-687">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="e95d9-687">**Key**: `environment`</span></span>  
<span data-ttu-id="e95d9-688">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="e95d9-688">**Type**: `string`</span></span>  
<span data-ttu-id="e95d9-689">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="e95d9-689">**Default**: `Production`</span></span>  
<span data-ttu-id="e95d9-690">**次を使用して設定**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="e95d9-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="e95d9-691">**環境変数**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` は [オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e95d9-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="e95d9-692">環境は任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-692">The environment can be set to any value.</span></span> <span data-ttu-id="e95d9-693">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e95d9-694">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="e95d9-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="e95d9-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="e95d9-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、ホストの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="e95d9-697">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> をアプリのビルド プロセスで使用するための初期化に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="e95d9-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="e95d9-699">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e95d9-700">既定ではプロバイダーが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="e95d9-700">No providers are included by default.</span></span> <span data-ttu-id="e95d9-701">次のような、アプリが <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> で必要とする構成プロバイダーを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="e95d9-702">ファイルの構成 (*hostsettings.json* ファイルからなど)。</span><span class="sxs-lookup"><span data-stu-id="e95d9-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="e95d9-703">環境変数の構成。</span><span class="sxs-lookup"><span data-stu-id="e95d9-703">Environment variable configuration.</span></span>
* <span data-ttu-id="e95d9-704">コマンドライン引数の構成。</span><span class="sxs-lookup"><span data-stu-id="e95d9-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="e95d9-705">その他に必要なすべての構成プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="e95d9-705">Any other required configuration providers.</span></span>

<span data-ttu-id="e95d9-706">ホストのファイル構成は、いずれかの[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)に対する呼び出しの前に `SetBasePath` のアプリのベース パスを指定することで有効になります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="e95d9-707">サンプル アプリは、JSON ファイル (*hostsettings.json*) を使用し、<xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> を呼び出して、ファイルのホスト構成設定を使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="e95d9-708">ホストの[環境変数の構成](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を追加するには、ホスト ビルダーで <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="e95d9-709">`AddEnvironmentVariables` は、オプションのユーザー定義プレフィックスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="e95d9-710">サンプル アプリは、`PREFIX_` のプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="e95d9-711">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e95d9-712">サンプル アプリのホストが構成されると、`PREFIX_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e95d9-713">開発中に [Visual Studio](https://visualstudio.microsoft.com) を使用している、または `dotnet run` を使用してアプリを実行している場合は、環境変数を *Properties/launchSettings.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="e95d9-714">[Visual Studio Code](https://code.visualstudio.com/) では、開発中に環境変数を *.vscode/launch.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="e95d9-715">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e95d9-716">[コマンドラインの構成](xref:fundamentals/configuration/index#command-line-configuration-provider)は、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>を呼び出すことで追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="e95d9-717">コマンドラインの構成は、コマンドライン引数を許可して以前の構成プロバイダーから提供された構成をオーバーライドするために最後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="e95d9-718">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e95d9-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="e95d9-719">追加の構成は、[applicationName](#application-key-name) と [contentRoot](#content-root) キーで指定することができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="e95d9-720"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を使う `HostBuilder` の構成の例:</span><span class="sxs-lookup"><span data-stu-id="e95d9-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="e95d9-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="e95d9-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="e95d9-722">アプリの構成は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="e95d9-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、アプリの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="e95d9-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="e95d9-725">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="e95d9-726"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> によって作成された構成は、以降の操作に対する [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) において、および <xref:Microsoft.Extensions.Hosting.IHost.Services*>サービス内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="e95d9-727">アプリの構成は、[ConfigureHostConfiguration](#configurehostconfiguration) によって提供されたホストの構成を自動的に受信します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="e95d9-728"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を使うアプリ構成の例:</span><span class="sxs-lookup"><span data-stu-id="e95d9-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="e95d9-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e95d9-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="e95d9-730">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="e95d9-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="e95d9-731">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="e95d9-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="e95d9-732">設定ファイルを出力ディレクトリに移動するには、プロジェクト ファイルの設定ファイルを [MSBuild プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items) として指定します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="e95d9-733">サンプル アプリはその JSON アプリ設定ファイルと、次の `<Content>` 項目を含む *hostsettings.json* を移動します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="e95d9-734"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> や <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> などの構成拡張メソッドでは、[Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) や [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) など、追加の NuGet パッケージを必要とします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="e95d9-735">アプリが [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) を使用しない限り、中心となる [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) パッケージに加えて、これらのパッケージがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="e95d9-736">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="e95d9-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="e95d9-737">ConfigureServices</span></span>

<span data-ttu-id="e95d9-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> は、アプリの[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーにサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e95d9-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="e95d9-740">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e95d9-741">詳細については、「<xref:fundamentals/host/hosted-services>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="e95d9-742">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)では、`AddHostedService` 拡張メソッドを使って、有効期間イベント `LifetimeEventsHostedService` と時刻指定付きバックグラウンド タスク `TimedHostedService` のサービスをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="e95d9-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="e95d9-743">ConfigureLogging</span></span>

<span data-ttu-id="e95d9-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> は、指定された <xref:Microsoft.Extensions.Logging.ILoggingBuilder> を構成するためのデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="e95d9-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="e95d9-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="e95d9-746">UseConsoleLifetime</span></span>

<span data-ttu-id="e95d9-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> では、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="e95d9-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> は、[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="e95d9-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の有効期間の実装として事前に登録されています。</span><span class="sxs-lookup"><span data-stu-id="e95d9-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="e95d9-750">最後に登録された有効期間が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="e95d9-751">コンテナーの構成</span><span class="sxs-lookup"><span data-stu-id="e95d9-751">Container configuration</span></span>

<span data-ttu-id="e95d9-752">他のコンテナーの接続をサポートするため、ホストは <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="e95d9-753">ファクトリの提供は、DI コンテナーの登録の一部ではなく、具象 DI コンテナーの作成に使用されるホストの組み込みです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="e95d9-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) は、アプリのサービス プロバイダーの作成に使われる既定のファクトリをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="e95d9-755">カスタム コンテナーの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> メソッドによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="e95d9-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> は、基盤のホスト API を基にしてコンテナーを構成するための、厳密に型指定されたエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="e95d9-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="e95d9-758">アプリのサービス コンテナーを作成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="e95d9-759">サービス コンテナーのファクトリを提供します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="e95d9-760">ファクトリを使って、アプリのカスタム サービス コンテナーを構成します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="e95d9-761">機能拡張</span><span class="sxs-lookup"><span data-stu-id="e95d9-761">Extensibility</span></span>

<span data-ttu-id="e95d9-762">ホスト拡張機能は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> 上の拡張メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="e95d9-763">次の例では、拡張メソッドが <xref:fundamentals/host/hosted-services> で示される [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) の例を使って <xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装を拡張する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="e95d9-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="e95d9-764">アプリは `UseHostedService` 拡張メソッドを確率して `T` に渡されたホステッド サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="e95d9-765">ホストを管理する</span><span class="sxs-lookup"><span data-stu-id="e95d9-765">Manage the host</span></span>

<span data-ttu-id="e95d9-766"><xref:Microsoft.Extensions.Hosting.IHost> の実装は、サービス コンテナーに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の実装の開始と停止を行います。</span><span class="sxs-lookup"><span data-stu-id="e95d9-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e95d9-767">実行</span><span class="sxs-lookup"><span data-stu-id="e95d9-767">Run</span></span>

<span data-ttu-id="e95d9-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="e95d9-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-769">RunAsync</span></span>

<span data-ttu-id="e95d9-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="e95d9-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-771">RunConsoleAsync</span></span>

<span data-ttu-id="e95d9-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="e95d9-773">Start と StopAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-773">Start and StopAsync</span></span>

<span data-ttu-id="e95d9-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="e95d9-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="e95d9-776">StartAsync と StopAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="e95d9-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> がアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="e95d9-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> がアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="e95d9-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e95d9-779">WaitForShutdown</span></span>

<span data-ttu-id="e95d9-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は、`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンする) などの <xref:Microsoft.Extensions.Hosting.IHostLifetime> を介してトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="e95d9-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="e95d9-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e95d9-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="e95d9-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="e95d9-784">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="e95d9-784">External control</span></span>

<span data-ttu-id="e95d9-785">ホストの外部コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-785">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="e95d9-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e95d9-787">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="e95d9-788">IHostingEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="e95d9-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="e95d9-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> は、アプリのホスティング環境に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="e95d9-790">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> を取得します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="e95d9-791">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e95d9-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="e95d9-792">IApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="e95d9-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="e95d9-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> は、正常なシャットダウンの要求など、起動後とシャットダウンのアクティビティに対応します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="e95d9-794">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する <xref:System.Action> メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="e95d9-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="e95d9-795">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="e95d9-795">Cancellation Token</span></span> | <span data-ttu-id="e95d9-796">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="e95d9-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="e95d9-797">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="e95d9-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="e95d9-798">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="e95d9-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="e95d9-799">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e95d9-799">All requests should be processed.</span></span> <span data-ttu-id="e95d9-800">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="e95d9-801">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="e95d9-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="e95d9-802">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="e95d9-802">Requests may still be processing.</span></span> <span data-ttu-id="e95d9-803">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="e95d9-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="e95d9-804">コンストラクターは任意のクラスに <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="e95d9-805">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)は、`LifetimeEventsHostedService` クラス (<xref:Microsoft.Extensions.Hosting.IHostedService> の実装) へのコンストラクターの挿入を使って、イベントを登録します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="e95d9-806">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="e95d9-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="e95d9-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="e95d9-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="e95d9-808">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="e95d9-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e95d9-809">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e95d9-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>

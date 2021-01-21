---
title: ASP.NET Core の Web ホスト
author: rick-anderson
description: ASP.NET Core アプリの Web ホスト (アプリの起動と有効期間の管理を担当する) について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 98be96bf60441cf09a315dbd1c60e109a7a08afe
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253112"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="6eab8-103">ASP.NET Core の Web ホスト</span><span class="sxs-lookup"><span data-stu-id="6eab8-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="6eab8-104">ASP.NET Core アプリは *ホスト* を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="6eab8-105">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="6eab8-106">少なくとも、ホストはサーバーおよび要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="6eab8-107">ホストでは、ログ記録、依存関係挿入、構成を設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6eab8-108">この記事では、Web ホストについて説明します。これは、下位互換性のためだけに引き続き使用可能となっています。</span><span class="sxs-lookup"><span data-stu-id="6eab8-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="6eab8-109">すべての種類のアプリに対して、[汎用ホスト](xref:fundamentals/host/generic-host)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6eab8-110">この記事では Web ホストについて説明します。これは Web アプリをホストするためのものです。</span><span class="sxs-lookup"><span data-stu-id="6eab8-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="6eab8-111">その他の種類のアプリでは、[汎用ホスト](xref:fundamentals/host/generic-host)をご使用ください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="6eab8-112">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="6eab8-112">Set up a host</span></span>

<span data-ttu-id="6eab8-113">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) のインスタンスを使用して、ホストを作成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="6eab8-114">通常、これはアプリのエントリ ポイントの `Main` メソッドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="6eab8-115">プロジェクト テンプレートでは、`Main` は *Program.cs* にあります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="6eab8-116">一般的なアプリでは、[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) を呼び出してホストの設定を開始します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="6eab8-117">`CreateDefaultBuilder` を呼び出すコードは、`CreateWebHostBuilder` という名前のメソッドに含まれ、ビルダー オブジェクトで `Run` を呼び出す `Main` のコードから分離されています。</span><span class="sxs-lookup"><span data-stu-id="6eab8-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="6eab8-118">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/)を使用する場合は、このような分離が必要です。</span><span class="sxs-lookup"><span data-stu-id="6eab8-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="6eab8-119">ツールでは、アプリを実行することなくホストを構成するために、デザイン時に呼び出すことができる `CreateWebHostBuilder` メソッドの検出が想定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="6eab8-120">別の方法は、`IDesignTimeDbContextFactory` を実装することです。</span><span class="sxs-lookup"><span data-stu-id="6eab8-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="6eab8-121">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="6eab8-122">`CreateDefaultBuilder` では次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="6eab8-123">アプリのホスティング構成プロバイダーを使用して [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="6eab8-124">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel/options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* <span data-ttu-id="6eab8-125">アプリのホスティング構成プロバイダーを使用して [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-125">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="6eab8-126">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-126">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
::: moniker-end
* <span data-ttu-id="6eab8-127">[コンテンツ ルート](xref:fundamentals/index#content-root)を、[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory) によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="6eab8-128">次から[ ホスト構成](#host-configuration-values)を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-128">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="6eab8-129">`ASPNETCORE_` のプレフィックスが付いた環境変数 (たとえば、`ASPNETCORE_ENVIRONMENT`)。</span><span class="sxs-lookup"><span data-stu-id="6eab8-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="6eab8-130">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="6eab8-130">Command-line arguments.</span></span>
* <span data-ttu-id="6eab8-131">次の順序でアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-131">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="6eab8-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6eab8-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="6eab8-133">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="6eab8-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="6eab8-134">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="6eab8-134">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="6eab8-135">環境変数。</span><span class="sxs-lookup"><span data-stu-id="6eab8-135">Environment variables.</span></span>
  * <span data-ttu-id="6eab8-136">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="6eab8-136">Command-line arguments.</span></span>
* <span data-ttu-id="6eab8-137">コンソールとデバッグ出力の[ログ](xref:fundamentals/logging/index)を構成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-137">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="6eab8-138">ログには、 *appsettings.json* または *appsettings.{Environment}.json* ファイルのログ構成セクションで指定される [ログ フィルター](xref:fundamentals/logging/index#log-filtering)規則が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-138">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="6eab8-139">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して IIS の背後で実行されている場合は、`CreateDefaultBuilder` によってアプリのベース アドレスとポートが構成される [IIS の統合](xref:host-and-deploy/iis/index)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-139">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="6eab8-140">IIS の統合により、[スタートアップ エラーをキャプチャする](#capture-startup-errors)アプリも構成されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-140">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="6eab8-141">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-141">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="6eab8-142">アプリの環境が開発の場合、[ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-142">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="6eab8-143">詳しくは、「[スコープの検証](#scope-validation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-143">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="6eab8-144">`CreateDefaultBuilder` によって定義された構成は、[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)、[ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)、そして [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) のその他のメソッドと拡張メソッドによってオーバーライドされ、拡張されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-144">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="6eab8-145">以下に、いくつかの例を示します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-145">A few examples follow:</span></span>

* <span data-ttu-id="6eab8-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) はアプリの追加の `IConfiguration` を指定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="6eab8-147">次の `ConfigureAppConfiguration` の呼び出しによりデリゲートが追加され、*appsettings.xml* ファイルにアプリの構成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-147">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="6eab8-148">`ConfigureAppConfiguration` は複数回呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-148">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="6eab8-149">この構成はホスト (たとえば、サーバーの URL や環境など) には適用されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-149">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="6eab8-150">「[ホストの構成値](#host-configuration-values)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-150">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="6eab8-151">次の `ConfigureLogging` の呼び出しによりデリゲートが追加され、最小ログ記録レベル ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) が [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel) に構成されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-151">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="6eab8-152">この設定により、`CreateDefaultBuilder` で構成された *appsettings.Development.json* (`LogLevel.Debug`) および *appsettings.Production.json* (`LogLevel.Error`) の設定がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-152">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6eab8-153">`ConfigureLogging` は複数回呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-153">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="6eab8-154">次の `ConfigureKestrel` の呼び出しにより、Kestrel が `CreateDefaultBuilder` によって構成されたときに確立された既定の [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) サイズである 30,000,000 バイトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-154">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="6eab8-155">次の [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) の呼び出しにより、Kestrel が `CreateDefaultBuilder` によって構成されたときに確立された既定の [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) サイズである 30,000,000 バイトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-155">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="6eab8-156">[コンテンツ ルート](xref:fundamentals/index#content-root)で、ホストが MVC ビュー ファイルなどのコンテンツ ファイルを検索する場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-156">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="6eab8-157">プロジェクトのルート フォルダーからアプリが開始された場合は、そのプロジェクトのルート フォルダーがコンテンツ ルートとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-157">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="6eab8-158">これは、[Visual Studio](https://visualstudio.microsoft.com) と [dotnet の新しいテンプレート](/dotnet/core/tools/dotnet-new)で使用される既定です。</span><span class="sxs-lookup"><span data-stu-id="6eab8-158">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="6eab8-159">アプリの構成の詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-159">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="6eab8-160">静的な `CreateDefaultBuilder` メソッドを使用する代わりに、[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) からホストを作成する方法が ASP.NET Core 2.x でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6eab8-160">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="6eab8-161">ホストの構成時に、[Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) および [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) メソッドを指摘することができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-161">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="6eab8-162">`Startup` クラスが指定されている場合は、`Configure` メソッドを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-162">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="6eab8-163">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-163">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="6eab8-164">`ConfigureServices` の複数回の呼び出しでは、互いに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-164">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="6eab8-165">`WebHostBuilder` での `Configure` または `UseStartup` の複数回の呼び出しでは、以前の設定が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-165">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="6eab8-166">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="6eab8-166">Host configuration values</span></span>

<span data-ttu-id="6eab8-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) では、ホストの構成値を設定する場合に以下の方法に依存します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="6eab8-168">`ASPNETCORE_{configurationKey}` 形式の環境変数を含む、ホスト ビルダー構成。</span><span class="sxs-lookup"><span data-stu-id="6eab8-168">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="6eab8-169">たとえば、`ASPNETCORE_ENVIRONMENT` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-169">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="6eab8-170">[UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) や [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) などの拡張機能 (「[構成をオーバーライドする](#override-configuration)」のセクションを参照)。</span><span class="sxs-lookup"><span data-stu-id="6eab8-170">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="6eab8-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) と関連するキー。</span><span class="sxs-lookup"><span data-stu-id="6eab8-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="6eab8-172">`UseSetting` で値を設定すると、値はその型に関係なく、文字列として設定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-172">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="6eab8-173">ホストは、最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-173">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="6eab8-174">詳しくは、次のセクション「[構成をオーバーライドする](#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-174">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="6eab8-175">アプリケーション キー (名前)</span><span class="sxs-lookup"><span data-stu-id="6eab8-175">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6eab8-176">ホストの構築時に [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) または [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) を呼び出すと、`IWebHostEnvironment.ApplicationName` プロパティが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-176">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="6eab8-177">この値はアプリのエントリ ポイントを含むアセンブリの名前に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-177">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="6eab8-178">値を明示的に設定するには、[WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-178">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6eab8-179">ホストの構築時に [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) または [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) が呼び出されると、[IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) プロパティが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-179">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="6eab8-180">この値はアプリのエントリ ポイントを含むアセンブリの名前に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-180">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="6eab8-181">値を明示的に設定するには、[WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-181">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="6eab8-182">**キー**: applicationName</span><span class="sxs-lookup"><span data-stu-id="6eab8-182">**Key**: applicationName</span></span>  
<span data-ttu-id="6eab8-183">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="6eab8-183">**Type**: *string*</span></span>  
<span data-ttu-id="6eab8-184">**既定値**:アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="6eab8-184">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="6eab8-185">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6eab8-185">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6eab8-186">**環境変数**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="6eab8-186">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="6eab8-187">スタートアップ エラーをキャプチャする</span><span class="sxs-lookup"><span data-stu-id="6eab8-187">Capture Startup Errors</span></span>

<span data-ttu-id="6eab8-188">この設定では、スタートアップ エラーのキャプチャを制御します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-188">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="6eab8-189">**キー**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="6eab8-189">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="6eab8-190">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="6eab8-190">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6eab8-191">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-191">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="6eab8-192">**次を使用して設定**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="6eab8-192">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="6eab8-193">**環境変数**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="6eab8-193">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="6eab8-194">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-194">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="6eab8-195">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-195">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="6eab8-196">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="6eab8-196">Content root</span></span>

<span data-ttu-id="6eab8-197">この設定により、ASP.NET Core でコンテンツ ファイルの検索が開始される場所が決まります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-197">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="6eab8-198">**キー**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="6eab8-198">**Key**: contentRoot</span></span>  
<span data-ttu-id="6eab8-199">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="6eab8-199">**Type**: *string*</span></span>  
<span data-ttu-id="6eab8-200">**既定**:既定でアプリ アセンブリが存在するフォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-200">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="6eab8-201">**次を使用して設定**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="6eab8-201">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="6eab8-202">**環境変数**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="6eab8-202">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="6eab8-203">コンテンツ ルートは、[Web ルート](xref:fundamentals/index#web-root)の基本パスとしても使用されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-203">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="6eab8-204">コンテンツ ルート パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="6eab8-204">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="6eab8-205">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="6eab8-205">For more information, see:</span></span>

* [<span data-ttu-id="6eab8-206">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="6eab8-206">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="6eab8-207">Web ルート</span><span class="sxs-lookup"><span data-stu-id="6eab8-207">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="6eab8-208">詳細なエラー</span><span class="sxs-lookup"><span data-stu-id="6eab8-208">Detailed Errors</span></span>

<span data-ttu-id="6eab8-209">詳細なエラーをキャプチャするかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-209">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="6eab8-210">**キー**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="6eab8-210">**Key**: detailedErrors</span></span>  
<span data-ttu-id="6eab8-211">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="6eab8-211">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6eab8-212">**既定値**: false</span><span class="sxs-lookup"><span data-stu-id="6eab8-212">**Default**: false</span></span>  
<span data-ttu-id="6eab8-213">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6eab8-213">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6eab8-214">**環境変数**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="6eab8-214">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="6eab8-215">有効な場合 (または<a href="#environment">環境</a>が `Development` に設定されている場合)、アプリは詳細な例外をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-215">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="6eab8-216">環境</span><span class="sxs-lookup"><span data-stu-id="6eab8-216">Environment</span></span>

<span data-ttu-id="6eab8-217">アプリの環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-217">Sets the app's environment.</span></span>

<span data-ttu-id="6eab8-218">**キー**: 環境</span><span class="sxs-lookup"><span data-stu-id="6eab8-218">**Key**: environment</span></span>  
<span data-ttu-id="6eab8-219">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="6eab8-219">**Type**: *string*</span></span>  
<span data-ttu-id="6eab8-220">**既定**:実稼働</span><span class="sxs-lookup"><span data-stu-id="6eab8-220">**Default**: Production</span></span>  
<span data-ttu-id="6eab8-221">**次を使用して設定**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="6eab8-221">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="6eab8-222">**環境変数**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="6eab8-222">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="6eab8-223">環境は任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-223">The environment can be set to any value.</span></span> <span data-ttu-id="6eab8-224">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-224">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="6eab8-225">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="6eab8-225">Values aren't case sensitive.</span></span> <span data-ttu-id="6eab8-226">既定では、*環境* は `ASPNETCORE_ENVIRONMENT` 環境変数から読み取られます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-226">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="6eab8-227">[Visual Studio](https://visualstudio.microsoft.com) を使用する場合、環境変数は *launchSettings.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-227">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="6eab8-228">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-228">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="6eab8-229">ホスティング スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="6eab8-229">Hosting Startup Assemblies</span></span>

<span data-ttu-id="6eab8-230">アプリのホスティング スタートアップ アセンブリを設定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-230">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="6eab8-231">**キー**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="6eab8-231">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="6eab8-232">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="6eab8-232">**Type**: *string*</span></span>  
<span data-ttu-id="6eab8-233">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="6eab8-233">**Default**: Empty string</span></span>  
<span data-ttu-id="6eab8-234">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6eab8-234">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6eab8-235">**環境変数**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="6eab8-235">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="6eab8-236">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="6eab8-236">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="6eab8-237">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-237">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="6eab8-238">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-238">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="6eab8-239">HTTPS Port</span><span class="sxs-lookup"><span data-stu-id="6eab8-239">HTTPS Port</span></span>

<span data-ttu-id="6eab8-240">HTTPS リダイレクト ポートを設定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-240">Set the HTTPS redirect port.</span></span> <span data-ttu-id="6eab8-241">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-241">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6eab8-242">**キー**: https_port **型**: *文字列*
**既定値**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="6eab8-242">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="6eab8-243">**次を使用して設定**:`UseSetting`
**環境変数**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="6eab8-243">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="6eab8-244">除外するホスティング スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="6eab8-244">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="6eab8-245">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="6eab8-245">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="6eab8-246">**キー**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="6eab8-246">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="6eab8-247">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="6eab8-247">**Type**: *string*</span></span>  
<span data-ttu-id="6eab8-248">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="6eab8-248">**Default**: Empty string</span></span>  
<span data-ttu-id="6eab8-249">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6eab8-249">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6eab8-250">**環境変数**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="6eab8-250">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="6eab8-251">ホスティング URL を優先する</span><span class="sxs-lookup"><span data-stu-id="6eab8-251">Prefer Hosting URLs</span></span>

<span data-ttu-id="6eab8-252">`IServer` 実装で構成されているものではなく、`WebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-252">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="6eab8-253">**キー**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="6eab8-253">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="6eab8-254">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="6eab8-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6eab8-255">**既定値**: true</span><span class="sxs-lookup"><span data-stu-id="6eab8-255">**Default**: true</span></span>  
<span data-ttu-id="6eab8-256">**次を使用して設定**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="6eab8-256">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="6eab8-257">**環境変数**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="6eab8-257">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="6eab8-258">ホスティング スタートアップを回避する</span><span class="sxs-lookup"><span data-stu-id="6eab8-258">Prevent Hosting Startup</span></span>

<span data-ttu-id="6eab8-259">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-259">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="6eab8-260">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-260">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="6eab8-261">**キー**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="6eab8-261">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="6eab8-262">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="6eab8-262">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6eab8-263">**既定値**: false</span><span class="sxs-lookup"><span data-stu-id="6eab8-263">**Default**: false</span></span>  
<span data-ttu-id="6eab8-264">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6eab8-264">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6eab8-265">**環境変数**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="6eab8-265">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="6eab8-266">サーバーの URL</span><span class="sxs-lookup"><span data-stu-id="6eab8-266">Server URLs</span></span>

<span data-ttu-id="6eab8-267">サーバーが要求をリッスンする必要があるポートとプロトコルを使用して、IP アドレスまたはホスト アドレスを示します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-267">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="6eab8-268">**キー**: urls</span><span class="sxs-lookup"><span data-stu-id="6eab8-268">**Key**: urls</span></span>  
<span data-ttu-id="6eab8-269">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="6eab8-269">**Type**: *string*</span></span>  
<span data-ttu-id="6eab8-270">**既定値**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="6eab8-270">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="6eab8-271">**次を使用して設定**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="6eab8-271">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="6eab8-272">**環境変数**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="6eab8-272">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="6eab8-273">サーバーが応答する必要がある URL プレフィックスのセミコロン (;) で区切られたリストに設定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-273">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="6eab8-274">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-274">For example, `http://localhost:123`.</span></span> <span data-ttu-id="6eab8-275">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-275">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="6eab8-276">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-276">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="6eab8-277">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-277">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="6eab8-278">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-278">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="6eab8-279">詳細については、「<xref:fundamentals/servers/kestrel/endpoints>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-279">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="6eab8-280">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-280">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="6eab8-281">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-281">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>
::: moniker-end

### <a name="shutdown-timeout"></a><span data-ttu-id="6eab8-282">シャットダウン タイムアウト</span><span class="sxs-lookup"><span data-stu-id="6eab8-282">Shutdown Timeout</span></span>

<span data-ttu-id="6eab8-283">Web ホストがシャットダウンするまで待機する時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-283">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="6eab8-284">**キー**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="6eab8-284">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="6eab8-285">**型**: *int*</span><span class="sxs-lookup"><span data-stu-id="6eab8-285">**Type**: *int*</span></span>  
<span data-ttu-id="6eab8-286">**既定**:5</span><span class="sxs-lookup"><span data-stu-id="6eab8-286">**Default**: 5</span></span>  
<span data-ttu-id="6eab8-287">**次を使用して設定**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="6eab8-287">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="6eab8-288">**環境変数**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="6eab8-288">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="6eab8-289">キーは `UseSetting` で *int* を受け取りますが (`.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")` など)、[UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) 拡張メソッドは [TimeSpan](/dotnet/api/system.timespan) を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-289">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="6eab8-290">タイムアウト期間中、ホスティングは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="6eab8-290">During the timeout period, hosting:</span></span>

* <span data-ttu-id="6eab8-291">[IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-291">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="6eab8-292">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-292">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="6eab8-293">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-293">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="6eab8-294">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-294">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="6eab8-295">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-295">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="6eab8-296">スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="6eab8-296">Startup Assembly</span></span>

<span data-ttu-id="6eab8-297">`Startup` クラスを検索するアセンブリを決定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-297">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="6eab8-298">**キー**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="6eab8-298">**Key**: startupAssembly</span></span>  
<span data-ttu-id="6eab8-299">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="6eab8-299">**Type**: *string*</span></span>  
<span data-ttu-id="6eab8-300">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="6eab8-300">**Default**: The app's assembly</span></span>  
<span data-ttu-id="6eab8-301">**次を使用して設定**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="6eab8-301">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="6eab8-302">**環境変数**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="6eab8-302">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="6eab8-303">名前 (`string`) または型 (`TStartup`) 別のアセンブリを参照できます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-303">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="6eab8-304">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-304">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="6eab8-305">Web ルート</span><span class="sxs-lookup"><span data-stu-id="6eab8-305">Web root</span></span>

<span data-ttu-id="6eab8-306">アプリの静的資産への相対パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-306">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="6eab8-307">**キー**: webroot</span><span class="sxs-lookup"><span data-stu-id="6eab8-307">**Key**: webroot</span></span>  
<span data-ttu-id="6eab8-308">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="6eab8-308">**Type**: *string*</span></span>  
<span data-ttu-id="6eab8-309">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="6eab8-309">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="6eab8-310">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-310">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="6eab8-311">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-311">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="6eab8-312">**次を使用して設定**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="6eab8-312">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="6eab8-313">**環境変数**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="6eab8-313">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="6eab8-314">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="6eab8-314">For more information, see:</span></span>

* [<span data-ttu-id="6eab8-315">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="6eab8-315">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="6eab8-316">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="6eab8-316">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="6eab8-317">構成をオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="6eab8-317">Override configuration</span></span>

<span data-ttu-id="6eab8-318">[Configuration](xref:fundamentals/configuration/index) を使用して、Web ホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-318">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="6eab8-319">次の例では、ホスト構成はオプションで *hostsettings.json* ファイルに指定されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-319">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="6eab8-320">*hostsettings.json* ファイルから読み込まれた構成は、コマンド ライン引数でオーバーライドされる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-320">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="6eab8-321">(`config` の) ビルド構成は、[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) でホストを構成する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-321">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="6eab8-322">`IWebHostBuilder` 構成はアプリの構成に追加されますが、その逆は当てはまりません&mdash;`ConfigureAppConfiguration` は `IWebHostBuilder` の構成に影響しません。</span><span class="sxs-lookup"><span data-stu-id="6eab8-322">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="6eab8-323">次のように、`UseUrls` で指定された構成をオーバーライドします (最初の構成は *hostsettings.json*、2 番目の構成はコマンドライン引数です)。</span><span class="sxs-lookup"><span data-stu-id="6eab8-323">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="6eab8-324">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6eab8-324">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="6eab8-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) は、指定された `IConfiguration` からホスト ビルダーの構成へのキーのみをコピーします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="6eab8-326">そのため、JSON、INI、XML 設定のファイルに `reloadOnChange: true` を設定しても影響はありません。</span><span class="sxs-lookup"><span data-stu-id="6eab8-326">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="6eab8-327">特定の URL でホストを実行するように指定する場合、[dotnet run](/dotnet/core/tools/dotnet-run) の実行時にコマンド プロンプトから必要な値を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-327">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="6eab8-328">コマンドライン引数は *hostsettings.json* ファイルからの `urls` 値をオーバーライドし、サーバーはポート 8080 でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-328">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="6eab8-329">ホストを管理する</span><span class="sxs-lookup"><span data-stu-id="6eab8-329">Manage the host</span></span>

<span data-ttu-id="6eab8-330">**実行**</span><span class="sxs-lookup"><span data-stu-id="6eab8-330">**Run**</span></span>

<span data-ttu-id="6eab8-331">`Run` メソッドは Web アプリを起動し、ホストがシャットダウンするまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-331">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="6eab8-332">**[開始]**</span><span class="sxs-lookup"><span data-stu-id="6eab8-332">**Start**</span></span>

<span data-ttu-id="6eab8-333">`Start` メソッドを呼び出して、ブロックせずにホストを実行します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-333">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="6eab8-334">URL のリストが `Start` メソッドに渡された場合、指定された URL でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-334">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="6eab8-335">アプリは、便利な静的メソッドを使用し、`CreateDefaultBuilder` の構成済みの既定値を使用して、新しいホストを初期化して起動できます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-335">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="6eab8-336">これらのメソッドは、コンソール出力なしでサーバーを起動します。その場合、[WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) を指定し、中断される (Ctrl-C/SIGINT または SIGTERM) まで待機します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-336">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="6eab8-337">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="6eab8-337">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="6eab8-338">次のように `RequestDelegate` で始まります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-338">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="6eab8-339">"Hello World!" という応答を受け取るには、ブラウザで `http://localhost:5000` に対する要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6eab8-339">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="6eab8-340">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-340">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="6eab8-341">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-341">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="6eab8-342">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="6eab8-342">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="6eab8-343">次のように、URL と `RequestDelegate` で始まります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-343">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="6eab8-344">アプリが `http://localhost:8080` で応答する場合を除き、**Start(RequestDelegate app)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-344">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="6eab8-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="6eab8-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="6eab8-346">次のように、`IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) のインスタンスを使用してルーティング ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-346">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="6eab8-347">この例では、以下のブラウザー要求を使用します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-347">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="6eab8-348">要求</span><span class="sxs-lookup"><span data-stu-id="6eab8-348">Request</span></span>                                    | <span data-ttu-id="6eab8-349">応答</span><span class="sxs-lookup"><span data-stu-id="6eab8-349">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="6eab8-350">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="6eab8-350">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="6eab8-351">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="6eab8-351">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="6eab8-352">"ooops!" という文字列がある場合は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-352">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="6eab8-353">"Uh oh!" という文字列がある場合は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-353">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="6eab8-354">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="6eab8-354">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="6eab8-355">Hello World!</span><span class="sxs-lookup"><span data-stu-id="6eab8-355">Hello World!</span></span>                             |

<span data-ttu-id="6eab8-356">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-356">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="6eab8-357">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-357">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="6eab8-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="6eab8-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="6eab8-359">次のように、URL と `IRouteBuilder` のインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-359">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="6eab8-360">アプリが `http://localhost:8080` で応答する場合を除き、**Start(Action\<IRouteBuilder> routeBuilder)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-360">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="6eab8-361">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="6eab8-361">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="6eab8-362">次のように、デリゲートを指定して `IApplicationBuilder` を構成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-362">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="6eab8-363">"Hello World!" という応答を受け取るには、ブラウザで `http://localhost:5000` に対する要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6eab8-363">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="6eab8-364">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-364">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="6eab8-365">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-365">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="6eab8-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="6eab8-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="6eab8-367">次のように、URL とデリゲートを指定して `IApplicationBuilder` を構成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-367">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="6eab8-368">アプリが `http://localhost:8080` で応答する場合を除き、**StartWith(Action\<IApplicationBuilder> app)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-368">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="6eab8-369">IWebHostEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="6eab8-369">IWebHostEnvironment interface</span></span>

<span data-ttu-id="6eab8-370">`IWebHostEnvironment` インターフェイスでは、アプリの Web ホスティング環境に関する情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-370">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="6eab8-371">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して `IWebHostEnvironment` を取得します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-371">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="6eab8-372">[規則ベースのアプローチ](xref:fundamentals/environments#environment-based-startup-class-and-methods)を使用して、環境に基づいて起動時にアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-372">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="6eab8-373">あるいは、次のように `ConfigureServices` で使用するために `IWebHostEnvironment` を `Startup` コンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-373">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="6eab8-374">`IsDevelopment` 拡張メソッドに加え、`IWebHostEnvironment` は `IsStaging`、`IsProduction`、`IsEnvironment(string environmentName)` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-374">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="6eab8-375">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-375">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6eab8-376">処理パイプラインを設定するために、次のように `IWebHostEnvironment` サービスを `Configure` メソッドに直接挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-376">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="6eab8-377">カスタムの[ミドルウェア](xref:fundamentals/middleware/write)を作成する際に、次のように `IWebHostEnvironment` を `Invoke` メソッドに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-377">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="6eab8-378">IHostingEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="6eab8-378">IHostingEnvironment interface</span></span>

<span data-ttu-id="6eab8-379">[IHostingEnvironment インターフェイス](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment)では、アプリの Web ホスティング環境に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-379">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="6eab8-380">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して `IHostingEnvironment` を取得します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-380">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="6eab8-381">[規則ベースのアプローチ](xref:fundamentals/environments#environment-based-startup-class-and-methods)を使用して、環境に基づいて起動時にアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-381">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="6eab8-382">あるいは、次のように `ConfigureServices` で使用するために `IHostingEnvironment` を `Startup` コンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-382">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="6eab8-383">`IsDevelopment` 拡張メソッドに加え、`IHostingEnvironment` は `IsStaging`、`IsProduction`、`IsEnvironment(string environmentName)` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-383">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="6eab8-384">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6eab8-384">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6eab8-385">処理パイプラインを設定するために、次のように `IHostingEnvironment` サービスを `Configure` メソッドに直接挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-385">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="6eab8-386">カスタムの[ミドルウェア](xref:fundamentals/middleware/write)を作成する際に、次のように `IHostingEnvironment` を `Invoke` メソッドに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-386">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="6eab8-387">IHostApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="6eab8-387">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="6eab8-388">`IHostApplicationLifetime` では、起動後とシャットダウンのアクティビティを実行できます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-388">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="6eab8-389">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する `Action` メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="6eab8-389">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="6eab8-390">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="6eab8-390">Cancellation Token</span></span>    | <span data-ttu-id="6eab8-391">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="6eab8-391">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="6eab8-392">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="6eab8-392">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="6eab8-393">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="6eab8-393">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="6eab8-394">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-394">All requests should be processed.</span></span> <span data-ttu-id="6eab8-395">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-395">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="6eab8-396">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="6eab8-396">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="6eab8-397">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="6eab8-397">Requests may still be processing.</span></span> <span data-ttu-id="6eab8-398">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-398">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="6eab8-399">`StopApplication` は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-399">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="6eab8-400">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、`StopApplication` を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-400">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="6eab8-401">IApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="6eab8-401">IApplicationLifetime interface</span></span>

<span data-ttu-id="6eab8-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) は、起動後とシャットダウンのアクティビティに適用できます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="6eab8-403">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する `Action` メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="6eab8-403">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="6eab8-404">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="6eab8-404">Cancellation Token</span></span>    | <span data-ttu-id="6eab8-405">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="6eab8-405">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="6eab8-406">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="6eab8-406">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="6eab8-407">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="6eab8-407">The host has fully started.</span></span> |
| [<span data-ttu-id="6eab8-408">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="6eab8-408">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="6eab8-409">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="6eab8-409">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="6eab8-410">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6eab8-410">All requests should be processed.</span></span> <span data-ttu-id="6eab8-411">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-411">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="6eab8-412">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="6eab8-412">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="6eab8-413">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="6eab8-413">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="6eab8-414">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="6eab8-414">Requests may still be processing.</span></span> <span data-ttu-id="6eab8-415">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-415">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="6eab8-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="6eab8-417">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、`StopApplication` を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-417">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="6eab8-418">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="6eab8-418">Scope validation</span></span>

<span data-ttu-id="6eab8-419">アプリの環境が開発の場合、[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) は [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="6eab8-420">`ValidateScopes` が `true` に設定されていると、既定のサービス プロバイダーはチェックを実行して次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-420">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="6eab8-421">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="6eab8-421">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="6eab8-422">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="6eab8-422">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="6eab8-423">[BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-423">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="6eab8-424">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-424">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="6eab8-425">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-425">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="6eab8-426">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="6eab8-426">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="6eab8-427">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="6eab8-427">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="6eab8-428">運用環境も含めて、常にスコープを検証するには、ホスト ビルダー上の [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) で [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) を構成します。</span><span class="sxs-lookup"><span data-stu-id="6eab8-428">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="6eab8-429">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6eab8-429">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>

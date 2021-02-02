---
title: ASP.NET Core でのアプリケーションのスタートアップ
author: rick-anderson
description: ASP.NET Core の Startup クラスがサービスとアプリケーションの要求パイプラインをどのように構成しているかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/startup
ms.openlocfilehash: e2825476c5fe36571b365ac5ee3c57ff4db61b87
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658678"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="dec76-103">ASP.NET Core でのアプリケーションのスタートアップ</span><span class="sxs-lookup"><span data-stu-id="dec76-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="dec76-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="dec76-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="dec76-105">`Startup` クラスはサービスとアプリケーションの要求パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="dec76-105">The `Startup` class configures services and the app's request pipeline.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="dec76-106">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="dec76-106">The Startup class</span></span>

<span data-ttu-id="dec76-107">ASP.NET Core アプリケーションでは `Startup` クラスが使用されています。このクラスは規約に従って `Startup` と名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="dec76-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="dec76-108">`Startup` クラス:</span><span class="sxs-lookup"><span data-stu-id="dec76-108">The `Startup` class:</span></span>

* <span data-ttu-id="dec76-109">必要に応じて <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> メソッドを含め、アプリの *サービス* を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="dec76-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="dec76-110">サービスとは、アプリ機能を提供する再利用可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="dec76-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="dec76-111">サービスは `ConfigureServices` に *登録され*、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) または <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> を介してアプリ全体で利用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="dec76-112">アプリの要求処理パイプラインを作成するために <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> メソッドを含めます。</span><span class="sxs-lookup"><span data-stu-id="dec76-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="dec76-113">`ConfigureServices` と `Configure` はアプリの起動時に ASP.NET Core ランタイムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="dec76-114">上記のサンプルは [Razor Pages](xref:razor-pages/index) 用です。MVC バージョンは似ています。</span><span class="sxs-lookup"><span data-stu-id="dec76-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>


<span data-ttu-id="dec76-115">アプリの[ホスト](xref:fundamentals/index#host)がビルドされるときに、`Startup` クラスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="dec76-116">`Startup` クラスは通常、ホスト ビルダーで [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) メソッドを呼び出すことで指定されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-116">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="dec76-117">ホストには、`Startup` クラス コンストラクターで使用できるサービスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="dec76-118">アプリケーションから `ConfigureServices` 経由でサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="dec76-119">ホスト サービスとアプリ サービスの両方が `Configure` 内とアプリ全体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="dec76-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="dec76-120">[汎用ホスト](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスの種類のみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="dec76-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="dec76-121">ほとんどのサービスは、`Configure` メソッドが呼び出されるまで使用できません。</span><span class="sxs-lookup"><span data-stu-id="dec76-121">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="dec76-122">マルチ スタートアップ</span><span class="sxs-lookup"><span data-stu-id="dec76-122">Multiple Startup</span></span>

<span data-ttu-id="dec76-123">アプリケーションの環境 (たとえば `StartupDevelopment`) ごとに個別の `Startup` クラスが定義されると、実行時に適切な `Startup` クラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-123">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="dec76-124">名前のサフィックスが現在の環境と一致するクラスが優先されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-124">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="dec76-125">アプリケーションが Development 環境で実行され、`Startup` クラスと `StartupDevelopment` クラスの両方が含まれている場合は、`StartupDevelopment` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-125">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="dec76-126">詳細については、「[Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods)」(複数の環境の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-126">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="dec76-127">ホストの詳細については、「[ホスト](xref:fundamentals/index#host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-127">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="dec76-128">スタートアップ時のエラー処理については、「[Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling)」(スタートアップ例外処理) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-128">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="dec76-129">ConfigureServices メソッド</span><span class="sxs-lookup"><span data-stu-id="dec76-129">The ConfigureServices method</span></span>

<span data-ttu-id="dec76-130"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="dec76-130">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="dec76-131">任意。</span><span class="sxs-lookup"><span data-stu-id="dec76-131">Optional.</span></span>
* <span data-ttu-id="dec76-132">アプリケーションのサービスを構成する `Configure` メソッドの前にホストによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-132">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="dec76-133">規約によって[構成オプション](xref:fundamentals/configuration/index)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-133">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="dec76-134">ホストでは、`Startup` メソッドが呼び出される前にいくつかのサービスを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="dec76-134">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="dec76-135">詳細については、「[ホスト](xref:fundamentals/index#host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-135">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="dec76-136">多くの設定が必要な機能には、<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 上の `Add{Service}` 拡張メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="dec76-136">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="dec76-137">たとえば、**Add** DbContext、**Add** DefaultIdentity、**Add** EntityFrameworkStores、**Add**RazorPages です。</span><span class="sxs-lookup"><span data-stu-id="dec76-137">For example, **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

<span data-ttu-id="dec76-138">サービス コンテナーにサービスを追加すると、アプリケーション内と `Configure` メソッド内でサービスを利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="dec76-138">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="dec76-139">サービスは[依存関係の挿入](xref:fundamentals/dependency-injection)または <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-139">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="dec76-140">Configure メソッド</span><span class="sxs-lookup"><span data-stu-id="dec76-140">The Configure method</span></span>

<span data-ttu-id="dec76-141"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> メソッドは、アプリケーションが HTTP 要求にどのように応答するかを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-141">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="dec76-142">要求パイプラインは、[ミドルウェア](xref:fundamentals/middleware/index) コンポーネントを <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> インスタンスに追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-142">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="dec76-143">`IApplicationBuilder` は `Configure` メソッドから使用できますが、サービス コンテナーに登録されていません。</span><span class="sxs-lookup"><span data-stu-id="dec76-143">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="dec76-144">ホスティングによって `IApplicationBuilder` が作成され、`Configure` に直接渡されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-144">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="dec76-145">[ASP.NET Core テンプレート](/dotnet/core/tools/dotnet-new)では、次をサポートするパイプラインが構成されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-145">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="dec76-146">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="dec76-146">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="dec76-147">例外ハンドラー</span><span class="sxs-lookup"><span data-stu-id="dec76-147">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="dec76-148">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="dec76-148">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="dec76-149">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="dec76-149">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="dec76-150">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="dec76-150">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="dec76-151">ASP.NET Core [MVC](xref:mvc/overview) と [Razor ページ](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="dec76-151">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="dec76-152">上記のサンプルは [Razor Pages](xref:razor-pages/index) 用です。MVC バージョンは似ています。</span><span class="sxs-lookup"><span data-stu-id="dec76-152">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

<span data-ttu-id="dec76-153">各 `Use` 拡張メソッドによって、要求パイプラインに 1 つまたは複数のミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-153">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="dec76-154">たとえば、<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> では、[静的ファイル](xref:fundamentals/static-files)を提供するように、[ミドルウェア](xref:fundamentals/middleware/index)を構成します。</span><span class="sxs-lookup"><span data-stu-id="dec76-154">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="dec76-155">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、妥当な場合はチェーンをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="dec76-155">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="dec76-156">`IWebHostEnvironment`、`ILoggerFactory`、または `ConfigureServices` で定義された任意のものなどの追加サービスを `Configure` メソッド シグネチャで指定できます。</span><span class="sxs-lookup"><span data-stu-id="dec76-156">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="dec76-157">使用可能な場合、これらのサービスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-157">These services are injected if they're available.</span></span>

<span data-ttu-id="dec76-158">`IApplicationBuilder` の使用方法およびミドルウェアの処理の順序については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-158">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="dec76-159">スタートアップを使用せずにサービスを構成する</span><span class="sxs-lookup"><span data-stu-id="dec76-159">Configure services without Startup</span></span>

<span data-ttu-id="dec76-160">`Startup` クラスを使用せず、サービスと要求処理パイプラインを構成するには、ホスト ビルダーで便利なメソッド、`ConfigureServices` と `Configure` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dec76-160">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="dec76-161">`ConfigureServices` の複数回の呼び出しでは、互いに追加されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-161">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="dec76-162">`Configure` メソッドが複数回呼び出された場合、最後の `Configure` 呼び出しが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-162">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="dec76-163">スタートアップ フィルターを使用した Startup の拡張</span><span class="sxs-lookup"><span data-stu-id="dec76-163">Extend Startup with startup filters</span></span>

<span data-ttu-id="dec76-164"><xref:Microsoft.AspNetCore.Hosting.IStartupFilter> を使用して次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="dec76-164">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="dec76-165">`Use{Middleware}` を明示的に呼び出さずに、アプリの [Configure](#the-configure-method) ミドルウェア パイプラインの先頭または末尾でミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="dec76-165">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="dec76-166">`IStartupFilter` は、アプリの作成者が既定のミドルウェアを明示的に登録する必要がないよう、パイプラインの先頭に既定値を追加するために、ASP.NET Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-166">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="dec76-167">`IStartupFilter` により、アプリの作成者に代わって別のコンポーネントが `Use{Middleware}` を呼び出すことを許可します。</span><span class="sxs-lookup"><span data-stu-id="dec76-167">`IStartupFilter` allows a different component to call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="dec76-168">`Configure` メソッドのパイプラインを作成します。</span><span class="sxs-lookup"><span data-stu-id="dec76-168">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="dec76-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) では、ライブラリによって追加されたミドルウェアの前後に実行するように、ミドルウェアを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="dec76-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="dec76-170">`IStartupFilter` には、`Action<IApplicationBuilder>` を受け取って返す <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> が実装されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-170">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="dec76-171"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で、アプリケーションの要求パイプラインを構成するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="dec76-171">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="dec76-172">詳細については、「[Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)」(IApplicationBuilder を使用したミドルウェア パイプラインの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-172">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="dec76-173">各 `IStartupFilter` によって、要求パイプラインで 1 つまたは複数のミドルウェアを追加できます。</span><span class="sxs-lookup"><span data-stu-id="dec76-173">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="dec76-174">フィルターは、サービス コンテナーに追加された順に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-174">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="dec76-175">フィルターは、コントロールを次のフィルターに渡す前または後にミドルウェアを追加できるため、アプリケーション パイプラインの先頭または末尾に追加されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-175">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="dec76-176">`IStartupFilter` でミドルウェアを登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="dec76-176">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="dec76-177">`RequestSetOptionsMiddleware` ミドルウェアによって、クエリ文字列パラメーターからオプション値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-177">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="dec76-178">`RequestSetOptionsMiddleware` は `RequestSetOptionsStartupFilter` クラスで構成されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-178">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="dec76-179">`IStartupFilter` は <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> のサービス コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-179">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

<span data-ttu-id="dec76-180">`option` のクエリ文字列パラメーターを指定すると、ミドルウェアでは ASP.NET Core ミドルウェアによって応答がレンダリングされる前に値の割り当てを処理します。</span><span class="sxs-lookup"><span data-stu-id="dec76-180">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="dec76-181">ミドルウェアの実行順序は、`IStartupFilter` の登録順に設定されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-181">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="dec76-182">複数の `IStartupFilter` の実装が、同じオブジェクトとやり取りする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dec76-182">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="dec76-183">順序が重要な場合は、ミドルウェアの実行順序に合わせて `IStartupFilter` サービスの登録順序を指定してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-183">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="dec76-184">`IStartupFilter` に登録された他のアプリケーション ミドルウェアの前または後に実行される `IStartupFilter` の実装が 1 つまたは複数あるミドルウェアを、ライブラリで追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dec76-184">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="dec76-185">ライブラリの `IStartupFilter` よって追加されたミドルウェアの前に `IStartupFilter` ミドルウェアを呼び出すには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="dec76-185">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="dec76-186">サービス コンテナーにライブラリを追加する前に、サービス登録を配置する</span><span class="sxs-lookup"><span data-stu-id="dec76-186">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="dec76-187">後で呼び出すために、ライブラリが追加された後にサービス登録を配置する</span><span class="sxs-lookup"><span data-stu-id="dec76-187">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="dec76-188">外部アセンブリからの起動時に構成を追加する</span><span class="sxs-lookup"><span data-stu-id="dec76-188">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="dec76-189"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="dec76-189">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="dec76-190">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-190">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dec76-191">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dec76-191">Additional resources</span></span>

* [<span data-ttu-id="dec76-192">ホスト</span><span class="sxs-lookup"><span data-stu-id="dec76-192">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="dec76-193">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="dec76-193">The Startup class</span></span>

<span data-ttu-id="dec76-194">ASP.NET Core アプリケーションでは `Startup` クラスが使用されています。このクラスは規約に従って `Startup` と名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="dec76-194">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="dec76-195">`Startup` クラス:</span><span class="sxs-lookup"><span data-stu-id="dec76-195">The `Startup` class:</span></span>

* <span data-ttu-id="dec76-196">必要に応じて <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> メソッドを含め、アプリの *サービス* を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="dec76-196">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="dec76-197">サービスとは、アプリ機能を提供する再利用可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="dec76-197">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="dec76-198">サービスは `ConfigureServices` に *登録され*、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) または <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> を介してアプリ全体で利用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-198">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="dec76-199">アプリの要求処理パイプラインを作成するために <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> メソッドを含めます。</span><span class="sxs-lookup"><span data-stu-id="dec76-199">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="dec76-200">`ConfigureServices` と `Configure` はアプリの起動時に ASP.NET Core ランタイムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-200">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

<span data-ttu-id="dec76-201">アプリの[ホスト](xref:fundamentals/index#host)がビルドされるときに、`Startup` クラスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-201">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="dec76-202">`Startup` クラスは通常、ホスト ビルダーで [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) メソッドを呼び出すことで指定されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-202">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="dec76-203">ホストには、`Startup` クラス コンストラクターで使用できるサービスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-203">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="dec76-204">アプリケーションから `ConfigureServices` 経由でサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-204">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="dec76-205">これで、ホスト サービスとアプリケーション サービスの両方が `Configure` 内とアプリ全体で使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="dec76-205">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="dec76-206">`Startup` クラスへの[依存関係挿入](xref:fundamentals/dependency-injection)の一般的な用途は、以下を挿入する場合です。</span><span class="sxs-lookup"><span data-stu-id="dec76-206">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="dec76-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> (環境別にサービスを構成するため)。</span><span class="sxs-lookup"><span data-stu-id="dec76-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="dec76-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> (構成を読み取るため)。</span><span class="sxs-lookup"><span data-stu-id="dec76-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="dec76-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> (`Startup.ConfigureServices` でロガーを作成するため)。</span><span class="sxs-lookup"><span data-stu-id="dec76-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="dec76-210">ほとんどのサービスは、`Configure` メソッドが呼び出されるまで使用できません。</span><span class="sxs-lookup"><span data-stu-id="dec76-210">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="dec76-211">マルチ スタートアップ</span><span class="sxs-lookup"><span data-stu-id="dec76-211">Multiple Startup</span></span>

<span data-ttu-id="dec76-212">アプリケーションの環境 (たとえば `StartupDevelopment`) ごとに個別の `Startup` クラスが定義されると、実行時に適切な `Startup` クラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-212">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="dec76-213">名前のサフィックスが現在の環境と一致するクラスが優先されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-213">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="dec76-214">アプリケーションが Development 環境で実行され、`Startup` クラスと `StartupDevelopment` クラスの両方が含まれている場合は、`StartupDevelopment` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-214">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="dec76-215">詳細については、「[Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods)」(複数の環境の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-215">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="dec76-216">ホストの詳細については、「[ホスト](xref:fundamentals/index#host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-216">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="dec76-217">スタートアップ時のエラー処理については、「[Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling)」(スタートアップ例外処理) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-217">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="dec76-218">ConfigureServices メソッド</span><span class="sxs-lookup"><span data-stu-id="dec76-218">The ConfigureServices method</span></span>

<span data-ttu-id="dec76-219"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="dec76-219">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="dec76-220">任意。</span><span class="sxs-lookup"><span data-stu-id="dec76-220">Optional.</span></span>
* <span data-ttu-id="dec76-221">アプリケーションのサービスを構成する `Configure` メソッドの前にホストによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-221">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="dec76-222">規約によって[構成オプション](xref:fundamentals/configuration/index)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-222">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="dec76-223">ホストでは、`Startup` メソッドが呼び出される前にいくつかのサービスを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="dec76-223">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="dec76-224">詳細については、「[ホスト](xref:fundamentals/index#host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-224">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="dec76-225">多くの設定が必要な機能には、<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 上の `Add{Service}` 拡張メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="dec76-225">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="dec76-226">たとえば、**Add** DbContext、**Add** DefaultIdentity、**Add** EntityFrameworkStores、**Add**RazorPages です。</span><span class="sxs-lookup"><span data-stu-id="dec76-226">For example, **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

<span data-ttu-id="dec76-227">サービス コンテナーにサービスを追加すると、アプリケーション内と `Configure` メソッド内でサービスを利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="dec76-227">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="dec76-228">サービスは[依存関係の挿入](xref:fundamentals/dependency-injection)または <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-228">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

<span data-ttu-id="dec76-229">`SetCompatibilityVersion` について詳しくは、「[SetCompatibilityVersion](xref:mvc/compatibility-version)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dec76-229">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="dec76-230">Configure メソッド</span><span class="sxs-lookup"><span data-stu-id="dec76-230">The Configure method</span></span>

<span data-ttu-id="dec76-231"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> メソッドは、アプリケーションが HTTP 要求にどのように応答するかを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-231">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="dec76-232">要求パイプラインは、[ミドルウェア](xref:fundamentals/middleware/index) コンポーネントを <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> インスタンスに追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-232">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="dec76-233">`IApplicationBuilder` は `Configure` メソッドから使用できますが、サービス コンテナーに登録されていません。</span><span class="sxs-lookup"><span data-stu-id="dec76-233">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="dec76-234">ホスティングによって `IApplicationBuilder` が作成され、`Configure` に直接渡されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-234">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="dec76-235">[ASP.NET Core テンプレート](/dotnet/core/tools/dotnet-new)では、次をサポートするパイプラインが構成されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-235">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="dec76-236">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="dec76-236">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="dec76-237">例外ハンドラー</span><span class="sxs-lookup"><span data-stu-id="dec76-237">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="dec76-238">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="dec76-238">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="dec76-239">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="dec76-239">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="dec76-240">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="dec76-240">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="dec76-241">ASP.NET Core [MVC](xref:mvc/overview) と [Razor ページ](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="dec76-241">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>
* [<span data-ttu-id="dec76-242">一般データ保護規制 (GDPR)</span><span class="sxs-lookup"><span data-stu-id="dec76-242">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

<span data-ttu-id="dec76-243">各 `Use` 拡張メソッドによって、要求パイプラインに 1 つまたは複数のミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-243">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="dec76-244">たとえば、<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> では、[静的ファイル](xref:fundamentals/static-files)を提供するように、[ミドルウェア](xref:fundamentals/middleware/index)を構成します。</span><span class="sxs-lookup"><span data-stu-id="dec76-244">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="dec76-245">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、妥当な場合はチェーンをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="dec76-245">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="dec76-246">`IHostingEnvironment` や `ILoggerFactory`、または `ConfigureServices` で定義された任意のものなどの追加サービスを `Configure` メソッド シグネチャで指定できます。</span><span class="sxs-lookup"><span data-stu-id="dec76-246">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="dec76-247">使用可能な場合、これらのサービスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-247">These services are injected if they're available.</span></span>

<span data-ttu-id="dec76-248">`IApplicationBuilder` の使用方法およびミドルウェアの処理の順序については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-248">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="dec76-249">スタートアップを使用せずにサービスを構成する</span><span class="sxs-lookup"><span data-stu-id="dec76-249">Configure services without Startup</span></span>

<span data-ttu-id="dec76-250">`Startup` クラスを使用せず、サービスと要求処理パイプラインを構成するには、ホスト ビルダーで便利なメソッド、`ConfigureServices` と `Configure` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dec76-250">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="dec76-251">`ConfigureServices` の複数回の呼び出しでは、互いに追加されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-251">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="dec76-252">`Configure` メソッドが複数回呼び出された場合、最後の `Configure` 呼び出しが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-252">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="dec76-253">スタートアップ フィルターを使用した Startup の拡張</span><span class="sxs-lookup"><span data-stu-id="dec76-253">Extend Startup with startup filters</span></span>

<span data-ttu-id="dec76-254"><xref:Microsoft.AspNetCore.Hosting.IStartupFilter> を使用して次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="dec76-254">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="dec76-255">`Use{Middleware}` を明示的に呼び出さずに、アプリの [Configure](#the-configure-method) ミドルウェア パイプラインの先頭または末尾でミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="dec76-255">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="dec76-256">`IStartupFilter` は、アプリの作成者が既定のミドルウェアを明示的に登録する必要がないよう、パイプラインの先頭に既定値を追加するために、ASP.NET Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-256">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="dec76-257">`IStartupFilter` は、アプリの作成者に代わって、別のコンポーネント呼び出し `Use{Middleware}` を許可します。</span><span class="sxs-lookup"><span data-stu-id="dec76-257">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="dec76-258">`Configure` メソッドのパイプラインを作成します。</span><span class="sxs-lookup"><span data-stu-id="dec76-258">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="dec76-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) では、ライブラリによって追加されたミドルウェアの前後に実行するように、ミドルウェアを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="dec76-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="dec76-260">`IStartupFilter` には、`Action<IApplicationBuilder>` を受け取って返す <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> が実装されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-260">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="dec76-261"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で、アプリケーションの要求パイプラインを構成するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="dec76-261">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="dec76-262">詳細については、「[Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)」(IApplicationBuilder を使用したミドルウェア パイプラインの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-262">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="dec76-263">各 `IStartupFilter` によって、要求パイプラインで 1 つまたは複数のミドルウェアを追加できます。</span><span class="sxs-lookup"><span data-stu-id="dec76-263">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="dec76-264">フィルターは、サービス コンテナーに追加された順に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-264">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="dec76-265">フィルターは、コントロールを次のフィルターに渡す前または後にミドルウェアを追加できるため、アプリケーション パイプラインの先頭または末尾に追加されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-265">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="dec76-266">`IStartupFilter` でミドルウェアを登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="dec76-266">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="dec76-267">`RequestSetOptionsMiddleware` ミドルウェアによって、クエリ文字列パラメーターからオプション値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="dec76-267">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="dec76-268">`RequestSetOptionsMiddleware` は `RequestSetOptionsStartupFilter` クラスで構成されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-268">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="dec76-269">`IStartupFilter` は <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> のサービス コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-269">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

<span data-ttu-id="dec76-270">`option` のクエリ文字列パラメーターを指定すると、ミドルウェアでは ASP.NET Core ミドルウェアによって応答がレンダリングされる前に値の割り当てを処理します。</span><span class="sxs-lookup"><span data-stu-id="dec76-270">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="dec76-271">ミドルウェアの実行順序は、`IStartupFilter` の登録順に設定されています。</span><span class="sxs-lookup"><span data-stu-id="dec76-271">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="dec76-272">複数の `IStartupFilter` の実装が、同じオブジェクトとやり取りする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dec76-272">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="dec76-273">順序が重要な場合は、ミドルウェアの実行順序に合わせて `IStartupFilter` サービスの登録順序を指定してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-273">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="dec76-274">`IStartupFilter` に登録された他のアプリケーション ミドルウェアの前または後に実行される `IStartupFilter` の実装が 1 つまたは複数あるミドルウェアを、ライブラリで追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dec76-274">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="dec76-275">ライブラリの `IStartupFilter` よって追加されたミドルウェアの前に `IStartupFilter` ミドルウェアを呼び出すには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="dec76-275">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="dec76-276">サービス コンテナーにライブラリを追加する前に、サービス登録を配置する</span><span class="sxs-lookup"><span data-stu-id="dec76-276">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="dec76-277">後で呼び出すために、ライブラリが追加された後にサービス登録を配置する</span><span class="sxs-lookup"><span data-stu-id="dec76-277">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="dec76-278">外部アセンブリからの起動時に構成を追加する</span><span class="sxs-lookup"><span data-stu-id="dec76-278">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="dec76-279"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="dec76-279">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="dec76-280">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dec76-280">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dec76-281">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dec76-281">Additional resources</span></span>

* [<span data-ttu-id="dec76-282">ホスト</span><span class="sxs-lookup"><span data-stu-id="dec76-282">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

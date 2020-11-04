---
title: ASP.NET Core での依存関係の挿入
author: rick-anderson
description: ASP.NET Core で依存関係の挿入を実装する方法とそれを使う方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678565"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="18586-103">ASP.NET Core での依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="18586-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18586-104">作成者: [Kirk Larkin](https://twitter.com/serpent5)、[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com)、[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="18586-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="18586-105">ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。</span><span class="sxs-lookup"><span data-stu-id="18586-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="18586-106">MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="18586-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="18586-107">コンソール アプリで依存関係の挿入を使用する方法の詳細については、「[Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)」 (.NET.NET での依存関係の挿入) を参照してください</span><span class="sxs-lookup"><span data-stu-id="18586-107">For information on using dependency injection in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="18586-108">オプションとしての依存関係挿入の詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="18586-109">このトピックでは、ASP.NET Core での依存関係の挿入について説明します。</span><span class="sxs-lookup"><span data-stu-id="18586-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="18586-110">コンソール アプリで依存関係の挿入を使用することの詳細については、「[.NET での依存関係の挿入](/dotnet/core/extensions/dependency-injection)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-110">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="18586-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="18586-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="18586-112">依存関係の挿入の概要</span><span class="sxs-lookup"><span data-stu-id="18586-112">Overview of dependency injection</span></span>

<span data-ttu-id="18586-113">" *依存関係* " とは、他のオブジェクトが依存するオブジェクトのことです。</span><span class="sxs-lookup"><span data-stu-id="18586-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="18586-114">他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="18586-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="18586-115">クラスは、`MyDependency` クラスのインスタンスを作成して、その `WriteMessage` メソッドを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="18586-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="18586-116">次の例で、`MyDependency` クラスは `IndexModel` クラスの依存関係です。</span><span class="sxs-lookup"><span data-stu-id="18586-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="18586-117">このクラスは `MyDependency` クラスを作成し、これに直接依存しています。</span><span class="sxs-lookup"><span data-stu-id="18586-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="18586-118">コードの依存関係には、前の例のような問題が含まれ、次の理由から回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="18586-119">`MyDependency` を別の実装で置き換えるには、`IndexModel` クラスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="18586-120">`MyDependency` が依存関係を含んでいる場合、これらは `IndexModel` クラスによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="18586-121">複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。</span><span class="sxs-lookup"><span data-stu-id="18586-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="18586-122">このような実装では、単体テストを行うことが困難です。</span><span class="sxs-lookup"><span data-stu-id="18586-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="18586-123">アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。</span><span class="sxs-lookup"><span data-stu-id="18586-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="18586-124">依存関係の挿入は、次の方法によってこれらの問題に対応します。</span><span class="sxs-lookup"><span data-stu-id="18586-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="18586-125">依存関係の実装を抽象化するための、インターフェイスまたは基底クラスの使用。</span><span class="sxs-lookup"><span data-stu-id="18586-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="18586-126">サービス コンテナー内の依存関係の登録。</span><span class="sxs-lookup"><span data-stu-id="18586-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="18586-127">ASP.NET Core には、組み込みのサービス コンテナー <xref:System.IServiceProvider> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="18586-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="18586-128">サービスは通常、アプリの `Startup.ConfigureServices` メソッドに登録されています。</span><span class="sxs-lookup"><span data-stu-id="18586-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="18586-129">サービスを使用するクラスのコンストラクターへの、サービスの " *挿入* "。</span><span class="sxs-lookup"><span data-stu-id="18586-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="18586-130">依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。</span><span class="sxs-lookup"><span data-stu-id="18586-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="18586-131">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、`IMyDependency` インターフェイスは、`WriteMessage` メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="18586-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="18586-132">このインターフェイスは、具象型 `MyDependency` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="18586-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="18586-133">サンプル アプリにおいて、`IMyDependency` サービスは具象型 `MyDependency` を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="18586-134"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> メソッドによって、サービスは、1 つの要求の有効期間であるスコープ付き有効期間で登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="18586-135">[サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="18586-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="18586-136">サンプル アプリでは、`IMyDependency` サービスが要求され、`WriteMessage` メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="18586-137">DI パターンを使用すると、コントローラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="18586-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="18586-138">具象型 `MyDependency` は使用されず、実装される `IMyDependency` インターフェイスのみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="18586-139">これにより、コントローラーが使用する実装は、コントローラーを変更することなく、簡単に変更できるようになります。</span><span class="sxs-lookup"><span data-stu-id="18586-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="18586-140">`MyDependency` のインスタンスは作成されず、DI コンテナーによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="18586-141">組み込みのログ API を使用すると、`IMyDependency` インターフェイスの実装を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="18586-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="18586-142">更新された `ConfigureServices` メソッドでは、新しい `IMyDependency` の実装が登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="18586-143">`MyDependency2` は、コンストラクターで要求される <xref:Microsoft.Extensions.Logging.ILogger%601> によって異なります。</span><span class="sxs-lookup"><span data-stu-id="18586-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="18586-144">`ILogger<TCategoryName>` は、[フレームワークで提供されるサービス](#framework-provided-services)です。</span><span class="sxs-lookup"><span data-stu-id="18586-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="18586-145">依存関係の挿入をチェーン形式で使用することはよくあります。</span><span class="sxs-lookup"><span data-stu-id="18586-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="18586-146">次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="18586-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="18586-147">コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。</span><span class="sxs-lookup"><span data-stu-id="18586-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="18586-148">解決する必要がある依存関係の集合的なセットは、通常、" *依存関係ツリー* "、" *依存関係グラフ* "、または " *オブジェクト グラフ* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="18586-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="18586-149">コンテナーでは、[(ジェネリック) オープン型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)を活用し、すべての [(ジェネリック) 構築型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)を登録する必要をなくすことで、`ILogger<TCategoryName>` を解決します。</span><span class="sxs-lookup"><span data-stu-id="18586-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="18586-150">依存関係の挿入に関する用語では、サービスは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="18586-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="18586-151">通常、他のオブジェクト (`IMyDependency` サービスなど) にサービスを提供するオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="18586-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="18586-152">Web サービスを使用する場合はありますが、サービスは Web サービスに関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="18586-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="18586-153">フレームワークは、堅牢な [ログ](xref:fundamentals/logging/index) システムを備えています。</span><span class="sxs-lookup"><span data-stu-id="18586-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="18586-154">前の例に示されている `IMyDependency` の実装は、ログを実装するのではなく、基本的な DI を実演するために記述されています。</span><span class="sxs-lookup"><span data-stu-id="18586-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="18586-155">ほとんどのアプリでは、ロガーを記述する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="18586-156">次のコードでは、既定のログを使用する方法が示されます。この場合、`ConfigureServices` にサービスを登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="18586-157">前のコードを使用すると、[ログ](xref:fundamentals/logging/index) がフレームワークによって提供されるため、`ConfigureServices` を更新する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="18586-158">Startup に挿入されるサービス</span><span class="sxs-lookup"><span data-stu-id="18586-158">Services injected into Startup</span></span>

<span data-ttu-id="18586-159">サービスは、`Startup` コンストラクターと `Startup.Configure` メソッドに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="18586-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="18586-160">汎用ホスト (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスのみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="18586-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="18586-161">DI コンテナーに登録されているすべてのサービスを、`Startup.Configure` メソッドに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="18586-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="18586-162">詳細については、「<xref:fundamentals/startup>」および「[起動時の構成へのアクセス](xref:fundamentals/configuration/index#access-configuration-in-startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="18586-163">拡張メソッドを使用したサービスのグループを登録する</span><span class="sxs-lookup"><span data-stu-id="18586-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="18586-164">ASP.NET Core フレームワークは、関連するサービスのグループを登録するための規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="18586-165">規則は、単一の `Add{GROUP_NAME}` 拡張メソッドを使用して、フレームワーク機能に必要なすべてのサービスを登録するというものです。</span><span class="sxs-lookup"><span data-stu-id="18586-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="18586-166">たとえば、<Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> 拡張メソッドによって、MVC コントローラーに必要なサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="18586-167">次のコードは、個々のユーザー アカウントを使用して :::no-loc(Razor)::: ページ テンプレートに基づいて生成されており、拡張メソッド <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> および <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A> を使用してコンテナーにさらにサービスを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="18586-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="18586-168">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="18586-168">Service lifetimes</span></span>

<span data-ttu-id="18586-169">サービスは、次のいずれかの有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="18586-169">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="18586-170">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-170">Transient</span></span>
* <span data-ttu-id="18586-171">スコープ</span><span class="sxs-lookup"><span data-stu-id="18586-171">Scoped</span></span>
* <span data-ttu-id="18586-172">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-172">Singleton</span></span>

<span data-ttu-id="18586-173">次のセクションでは、前の有効期間について個別に説明します。</span><span class="sxs-lookup"><span data-stu-id="18586-173">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="18586-174">登録される各サービスの適切な有効期間を選択します。</span><span class="sxs-lookup"><span data-stu-id="18586-174">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="18586-175">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-175">Transient</span></span>

<span data-ttu-id="18586-176">有効期間が一時的なサービスは、サービス コンテナーから要求されるたびに作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-176">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="18586-177">この有効期間は、軽量でステートレスのサービスに最適です。</span><span class="sxs-lookup"><span data-stu-id="18586-177">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="18586-178"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> で一時的なサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="18586-178">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="18586-179">要求を処理するアプリでは、一時的なサービスが要求の最後に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-179">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="18586-180">スコープ</span><span class="sxs-lookup"><span data-stu-id="18586-180">Scoped</span></span>

<span data-ttu-id="18586-181">有効期間がスコープのサービスは、クライアント要求 (接続) ごとに 1 回作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-181">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="18586-182"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> でスコープ付きサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="18586-182">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="18586-183">要求を処理するアプリでは、スコープ付きサービスは要求の最後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-183">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="18586-184">Entity Framework Core を使用する場合、既定では <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張メソッドによって、スコープ付き有効期間を持つ `DbContext` 型が登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-184">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="18586-185">シングルトンからスコープ付きサービスを解決 **しない** でください。また、たとえば一時的なサービスにより、間接的に解決しないように注意してください。</span><span class="sxs-lookup"><span data-stu-id="18586-185">Do \* **not** _ resolve a scoped service from a singleton and be careful not to do so indirectly, for example, through a transient service.</span></span> <span data-ttu-id="18586-186">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18586-186">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="18586-187">次の場合は問題ありません。</span><span class="sxs-lookup"><span data-stu-id="18586-187">It's fine to:</span></span>

<span data-ttu-id="18586-188">スコープ付きまたは一時的なサービスからシングルトン サービスを解決する。</span><span class="sxs-lookup"><span data-stu-id="18586-188">_ Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="18586-189">スコープ付きサービスを、別のスコープ付きまたは一時的なサービスから解決する。</span><span class="sxs-lookup"><span data-stu-id="18586-189">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="18586-190">既定では、開発環境で、より長い有効期間を持つ別のサービスからサービスを解決すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="18586-190">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="18586-191">詳しくは、「[スコープの検証](#sv)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="18586-191">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="18586-192">ミドルウェアでスコープ付きサービスを使用するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-192">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="18586-193">ミドルウェアの `Invoke` または `InvokeAsync` メソッドにサービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="18586-193">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="18586-194">[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用すると、スコープ付きサービスがシングルトンのように動作するように強制されるため、実行時の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="18586-194">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="18586-195">「[有効期間と登録のオプション](#lifetime-and-registration-options)」セクションにあるサンプルは、`InvokeAsync` による方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="18586-195">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="18586-196">[ファクトリ ベースのミドルウェア](xref:fundamentals/middleware/extensibility)を使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-196">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="18586-197">この方法を使用して登録されたミドルウェアは、クライアント要求 (接続) ごとにアクティブ化されます。これにより、スコープ付きサービスをミドルウェアの `InvokeAsync` メソッドに挿入できるようになります。</span><span class="sxs-lookup"><span data-stu-id="18586-197">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="18586-198">詳細については、「<xref:fundamentals/middleware/write#per-request-middleware-dependencies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-198">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="18586-199">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-199">Singleton</span></span>

<span data-ttu-id="18586-200">シングルトン有効期間サービスが作成されるのは、次のいずれかの場合です。</span><span class="sxs-lookup"><span data-stu-id="18586-200">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="18586-201">それらが初めて要求された場合。</span><span class="sxs-lookup"><span data-stu-id="18586-201">The first time they're requested.</span></span>
* <span data-ttu-id="18586-202">開発者によって、実装インスタンスがコンテナーに直接提供される場合。</span><span class="sxs-lookup"><span data-stu-id="18586-202">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="18586-203">このアプローチはほとんど必要ありません。</span><span class="sxs-lookup"><span data-stu-id="18586-203">This approach is rarely needed.</span></span>

<span data-ttu-id="18586-204">以降の要求は、すべて同じインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-204">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="18586-205">アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可してください。</span><span class="sxs-lookup"><span data-stu-id="18586-205">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="18586-206">シングルトン デザイン パターンを実装したり、シングルトンを破棄するコードを提供したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-206">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="18586-207">コンテナーからサービスを解決したコードによって、サービスが破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="18586-207">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="18586-208">型またはファクトリがシングルトンとして登録されている場合、コンテナーによってシングルトンが自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-208">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="18586-209">シングルトン サービスを <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> で登録します。</span><span class="sxs-lookup"><span data-stu-id="18586-209">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="18586-210">シングルトン サービスはスレッド セーフである必要があり、ほとんどの場合、ステートレス サービスで使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-210">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="18586-211">要求を処理するアプリでは、アプリのシャットダウン時に <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> が破棄されるとき、シングルトン サービスが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-211">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="18586-212">アプリがシャットダウンされるまでメモリは解放されないため、シングルトン サービスでのメモリ使用を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="18586-212">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="18586-213">シングルトンからスコープ付きサービスを解決 **しない** でください。</span><span class="sxs-lookup"><span data-stu-id="18586-213">Do \* **not** _ resolve a scoped service from a singleton.</span></span> <span data-ttu-id="18586-214">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18586-214">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="18586-215">スコープ付きまたは一時的なサービスからシングルトン サービスを解決することに問題はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-215">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="18586-216">サービス登録メソッド</span><span class="sxs-lookup"><span data-stu-id="18586-216">Service registration methods</span></span>

<span data-ttu-id="18586-217">このフレームワークでは、特定のシナリオで役立つサービス登録拡張メソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="18586-217">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="18586-218">メソッド</span><span class="sxs-lookup"><span data-stu-id="18586-218">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="18586-219">自動</span><span class="sxs-lookup"><span data-stu-id="18586-219">Automatic</span></span><br><span data-ttu-id="18586-220">object</span><span class="sxs-lookup"><span data-stu-id="18586-220">object</span></span><br><span data-ttu-id="18586-221">破棄</span><span class="sxs-lookup"><span data-stu-id="18586-221">disposal</span></span> | <span data-ttu-id="18586-222">複数</span><span class="sxs-lookup"><span data-stu-id="18586-222">Multiple</span></span><br><span data-ttu-id="18586-223">実装</span><span class="sxs-lookup"><span data-stu-id="18586-223">implementations</span></span> | <span data-ttu-id="18586-224">引数を渡す</span><span class="sxs-lookup"><span data-stu-id="18586-224">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="18586-225">例:</span><span class="sxs-lookup"><span data-stu-id="18586-225">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="18586-226">はい</span><span class="sxs-lookup"><span data-stu-id="18586-226">Yes</span></span>                             | <span data-ttu-id="18586-227">はい</span><span class="sxs-lookup"><span data-stu-id="18586-227">Yes</span></span>                         | <span data-ttu-id="18586-228">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-228">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="18586-229">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-229">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="18586-230">はい</span><span class="sxs-lookup"><span data-stu-id="18586-230">Yes</span></span>                             | <span data-ttu-id="18586-231">はい</span><span class="sxs-lookup"><span data-stu-id="18586-231">Yes</span></span>                         | <span data-ttu-id="18586-232">はい</span><span class="sxs-lookup"><span data-stu-id="18586-232">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="18586-233">例:</span><span class="sxs-lookup"><span data-stu-id="18586-233">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="18586-234">はい</span><span class="sxs-lookup"><span data-stu-id="18586-234">Yes</span></span>                             | <span data-ttu-id="18586-235">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-235">No</span></span>                          | <span data-ttu-id="18586-236">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-236">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="18586-237">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-237">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="18586-238">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-238">No</span></span>                              | <span data-ttu-id="18586-239">はい</span><span class="sxs-lookup"><span data-stu-id="18586-239">Yes</span></span>                         | <span data-ttu-id="18586-240">はい</span><span class="sxs-lookup"><span data-stu-id="18586-240">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="18586-241">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-241">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="18586-242">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-242">No</span></span>                              | <span data-ttu-id="18586-243">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-243">No</span></span>                          | <span data-ttu-id="18586-244">はい</span><span class="sxs-lookup"><span data-stu-id="18586-244">Yes</span></span>       |

<span data-ttu-id="18586-245">型の廃棄の詳細については、「[サービスの破棄](#disposal-of-services)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-245">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="18586-246">[テスト用に型のモックを作成](xref:test/integration-tests#inject-mock-services)する場合に、複数の実装を使用することは一般的です。</span><span class="sxs-lookup"><span data-stu-id="18586-246">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="18586-247">実装型のみでサービスを登録することは、同じ実装とサービスの型でそのサービスを登録することと同じです。</span><span class="sxs-lookup"><span data-stu-id="18586-247">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="18586-248">明示的なサービス型を使用しないメソッドを使用してサービスの複数の実装を登録できないのは、このためです。</span><span class="sxs-lookup"><span data-stu-id="18586-248">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="18586-249">これらのメソッドでは、サービスの複数のインスタンスを登録できますが、すべて同じ " *実装* " 型になります。</span><span class="sxs-lookup"><span data-stu-id="18586-249">These methods can register multiple _instances\* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="18586-250">上記のサービス登録メソッドのずれかを使用して、同じサービス型の複数のサービス インスタンスを登録できます。</span><span class="sxs-lookup"><span data-stu-id="18586-250">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="18586-251">次の例では、`IMyDependency` をサービス型として使用して、`AddSingleton` を 2 回呼び出します。</span><span class="sxs-lookup"><span data-stu-id="18586-251">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="18586-252">2 回目の `AddSingleton` の呼び出しにより、`IMyDependency` として解決された場合は前のものがオーバーライドされ、`IEnumerable<IMyDependency>` を介して複数のサービスが解決された場合は前のものに追加されます。</span><span class="sxs-lookup"><span data-stu-id="18586-252">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="18586-253">`IEnumerable<{SERVICE}>` を介して解決された場合、サービスは登録された順に表示されます。</span><span class="sxs-lookup"><span data-stu-id="18586-253">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="18586-254">フレームワークには `TryAdd{LIFETIME}` 拡張メソッドも用意されており、実装がまだ登録されていない場合にのみ、サービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-254">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="18586-255">次の例では、`AddSingleton` の呼び出しによって、`IMyDependency` の実装として `MyDependency` が登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-255">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="18586-256">`IMyDependency` には登録された実装が既に含まれているため、`TryAddSingleton` の呼び出しでは何も行われません。</span><span class="sxs-lookup"><span data-stu-id="18586-256">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="18586-257">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-257">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="18586-258">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) メソッドでは、" *同じ型* " の実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-258">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="18586-259">複数のサービスは、`IEnumerable<{SERVICE}>` によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="18586-259">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="18586-260">サービスを登録するとき、開発者は同じ型のものがまだ追加されていない場合にインスタンスを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-260">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="18586-261">一般に、ライブラリの作成者は、コンテナー内の実装の複数のコピーを登録しないようにするため `TryAddEnumerable` を使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-261">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="18586-262">次の例では、`TryAddEnumerable` の最初の呼び出しで、`IMyDependency1` の実装として `MyDependency` が登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-262">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="18586-263">2 番目の呼び出しでは `IMyDependency2` に `MyDependency` が登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-263">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="18586-264">3 番目の呼び出しでは何も行われません。`IMyDependency1` には `MyDependency` の登録済みの実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="18586-264">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="18586-265">サービスの登録は、通常、同じ種類の複数の実装を登録する場合を除き、順序に依存しません。</span><span class="sxs-lookup"><span data-stu-id="18586-265">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="18586-266">`IServiceCollection` は <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> オブジェクトのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="18586-266">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="18586-267">次の例は、`ServiceDescriptor` の作成と追加によってサービスを登録する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="18586-267">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="18586-268">組み込みの `Add{LIFETIME}` メソッドでも同じ方法が使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-268">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="18586-269">たとえば、[AddScoped のソース コード](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="18586-269">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="18586-270">コンストラクターの挿入の動作</span><span class="sxs-lookup"><span data-stu-id="18586-270">Constructor injection behavior</span></span>

<span data-ttu-id="18586-271">サービスは次を使用することによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="18586-271">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="18586-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="18586-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="18586-273">コンテナーに登録されていないオブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-273">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="18586-274">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、MVC コントローラー、[モデル バインダー](xref:mvc/models/model-binding)などのフレームワーク機能で使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-274">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="18586-275">コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-275">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="18586-276">`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、 [コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、" *パブリック* " コンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="18586-276">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="18586-277">`ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、該当するコンストラクターが 1 つだけ存在することが必要です。</span><span class="sxs-lookup"><span data-stu-id="18586-277">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="18586-278">コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。</span><span class="sxs-lookup"><span data-stu-id="18586-278">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="18586-279">Entity Framework コンテキスト</span><span class="sxs-lookup"><span data-stu-id="18586-279">Entity Framework contexts</span></span>

<span data-ttu-id="18586-280">既定の Entity Framework コンテキストは、[スコープ付き有効期間](#service-lifetimes)を使用してサービス コンテナーに追加されます。これは、Web アプリ データベース操作は通常、そのスコープがクライアント要求に設定されるためです。</span><span class="sxs-lookup"><span data-stu-id="18586-280">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="18586-281">異なる有効期間を使用するには、<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> のオーバーロードを使用して有効期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="18586-281">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="18586-282">有効期間が与えられたサービスの場合、そのサービスより有効期間が短いデータベース コンテキストを使用できません。</span><span class="sxs-lookup"><span data-stu-id="18586-282">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="18586-283">有効期間と登録のオプション</span><span class="sxs-lookup"><span data-stu-id="18586-283">Lifetime and registration options</span></span>

<span data-ttu-id="18586-284">サービスの有効期間とその登録のオプションの違いを示すために、タスクを識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。</span><span class="sxs-lookup"><span data-stu-id="18586-284">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="18586-285">次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーからは、クラスによって要求されたときに、サービスの同じインスタンスか別のインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="18586-285">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="18586-286">次の `Operation` クラスでは、上記のすべてのインターフェイスが実装されます。</span><span class="sxs-lookup"><span data-stu-id="18586-286">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="18586-287">`Operation` コンストラクターによって GUID が生成され、`OperationId` プロパティの最後の 4 文字が格納されます。</span><span class="sxs-lookup"><span data-stu-id="18586-287">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="18586-288">`Startup.ConfigureServices` メソッドでは、指定された有効期間に従って、`Operation` クラスの複数の登録が作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-288">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="18586-289">サンプル アプリでは、それぞれの要求内、およびそれぞれの要求間におけるオブジェクトの有効期間が示されます。</span><span class="sxs-lookup"><span data-stu-id="18586-289">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="18586-290">`IndexModel` とミドルウェアでは、すべての種類の `IOperation` 型が要求され、それぞれの `OperationId` がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-290">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="18586-291">ミドルウェアは `IndexModel` に類似していて、同じサービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="18586-291">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="18586-292">スコープ付きのサービスは `InvokeAsync` メソッドで解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-292">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="18586-293">ロガーの出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="18586-293">The logger output shows:</span></span>

* <span data-ttu-id="18586-294">" *一時的な* " オブジェクトは常に異なります。</span><span class="sxs-lookup"><span data-stu-id="18586-294">*Transient* objects are always different.</span></span> <span data-ttu-id="18586-295">一時的な `OperationId` 値は、`IndexModel` とミドルウェアでは異なります。</span><span class="sxs-lookup"><span data-stu-id="18586-295">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="18586-296">" *スコープ付きの* " オブジェクトは、それぞれの要求内では同じですが、それぞれの要求間では異なります。</span><span class="sxs-lookup"><span data-stu-id="18586-296">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="18586-297">" *シングルトン* " オブジェクトは、すべての要求において同じです。</span><span class="sxs-lookup"><span data-stu-id="18586-297">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="18586-298">ログ出力を減らすには、 *appsettings.Development.json* ファイル内で "Logging:LogLevel:Microsoft:Error" を設定します。</span><span class="sxs-lookup"><span data-stu-id="18586-298">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="18586-299">main からサービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="18586-299">Call services from main</span></span>

<span data-ttu-id="18586-300">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) を使用して <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成し、アプリのスコープ内のスコープ サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="18586-300">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="18586-301">この方法は、起動時に初期化タスクを実行するために、スコープ サービスにアクセスするのに便利です。</span><span class="sxs-lookup"><span data-stu-id="18586-301">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="18586-302">次の例は、スコープ付きの `IMyDependency` サービスにアクセスし、`Program.Main` でその `WriteMessage` メソッドを呼び出す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-302">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="18586-303">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="18586-303">Scope validation</span></span>

<span data-ttu-id="18586-304">アプリが[開発環境](xref:fundamentals/environments)で実行されていて、ホストを構築するために [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) が呼び出される場合、既定のサービス プロバイダーによって次を確認するためのチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="18586-304">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="18586-305">スコープ付きサービスが、ルート サービス プロバイダーによって解決されない。</span><span class="sxs-lookup"><span data-stu-id="18586-305">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="18586-306">スコープ付きサービスが、シングルトンに挿入されない。</span><span class="sxs-lookup"><span data-stu-id="18586-306">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="18586-307"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-307">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="18586-308">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリの有効期間に対応し、アプリのシャットダウン時には破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-308">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="18586-309">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-309">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="18586-310">ルート コンテナーに作成されたスコープ付きサービスは、アプリのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="18586-310">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="18586-311">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="18586-311">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="18586-312">詳しくは、「[スコープの検証](xref:fundamentals/host/web-host#scope-validation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="18586-312">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="18586-313">要求サービス</span><span class="sxs-lookup"><span data-stu-id="18586-313">Request Services</span></span>

<span data-ttu-id="18586-314">ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) コレクションを通じて公開されます。</span><span class="sxs-lookup"><span data-stu-id="18586-314">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="18586-315">要求内からサービスが要求されると、サービスとその依存関係は `RequestServices` コレクションから解決されます。</span><span class="sxs-lookup"><span data-stu-id="18586-315">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="18586-316">フレームワークでは要求ごとにスコープが作成され、`RequestServices` によってスコープ付きサービス プロバイダーが公開されます。</span><span class="sxs-lookup"><span data-stu-id="18586-316">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="18586-317">すべてのスコープ サービスは、要求がアクティブである限り有効です。</span><span class="sxs-lookup"><span data-stu-id="18586-317">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="18586-318">`RequestServices` コレクションからサービスを解決するよりも、コンストラクターのパラメーターとして依存関係を要求するようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-318">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="18586-319">これにより、テストしやすいクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-319">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="18586-320">依存関係の挿入のためのサービスの設計</span><span class="sxs-lookup"><span data-stu-id="18586-320">Design services for dependency injection</span></span>

<span data-ttu-id="18586-321">依存関係の挿入のためのサービスの設計時には:</span><span class="sxs-lookup"><span data-stu-id="18586-321">When designing services for dependency injection:</span></span>

* <span data-ttu-id="18586-322">ステートフル、静的クラス、およびメンバーは避けてください。</span><span class="sxs-lookup"><span data-stu-id="18586-322">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="18586-323">代わりにシングルトン サービスを使用するようにアプリを設計し、グローバルな状態を作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-323">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="18586-324">サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。</span><span class="sxs-lookup"><span data-stu-id="18586-324">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="18586-325">直接のインスタンス化は、コードの固有の実装につながります。</span><span class="sxs-lookup"><span data-stu-id="18586-325">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="18586-326">サービスを、小さく、十分に要素に分割された、テストしやすいものにします。</span><span class="sxs-lookup"><span data-stu-id="18586-326">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="18586-327">クラスに含まれる挿入される依存関係が多すぎる場合、それは、クラスの責任が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反しているサインである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18586-327">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="18586-328">責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。</span><span class="sxs-lookup"><span data-stu-id="18586-328">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="18586-329">:::no-loc(Razor)::: Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。</span><span class="sxs-lookup"><span data-stu-id="18586-329">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="18586-330">サービスの破棄</span><span class="sxs-lookup"><span data-stu-id="18586-330">Disposal of services</span></span>

<span data-ttu-id="18586-331">コンテナーは、作成する <xref:System.IDisposable> 型の <xref:System.IDisposable.Dispose%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="18586-331">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="18586-332">コンテナーから解決されたサービスが、開発者によって破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="18586-332">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="18586-333">型またはファクトリがシングルトンとして登録されている場合、コンテナーによってシングルトンが自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-333">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="18586-334">次の例では、サービスがサービス コンテナーによって作成され、自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-334">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="18586-335">デバッグ コンソールでは、インデックス ページを更新するたびに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="18586-335">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="18586-336">サービス コンテナーによって作成されていないサービス</span><span class="sxs-lookup"><span data-stu-id="18586-336">Services not created by the service container</span></span>

<span data-ttu-id="18586-337">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="18586-337">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="18586-338">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="18586-338">In the preceding code:</span></span>

* <span data-ttu-id="18586-339">サービス インスタンスは、サービス コンテナーによって作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="18586-339">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="18586-340">フレームワークでは、サービスが自動的に破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="18586-340">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="18586-341">サービスを破棄する責任は開発者にあります。</span><span class="sxs-lookup"><span data-stu-id="18586-341">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="18586-342">一時的なインスタンスと共有インスタンスのための IDisposable ガイダンス</span><span class="sxs-lookup"><span data-stu-id="18586-342">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="18586-343">一時的で限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="18586-343">Transient, limited lifetime</span></span>

<span data-ttu-id="18586-344">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="18586-344">**Scenario**</span></span>

<span data-ttu-id="18586-345">アプリには、次のいずれかのシナリオに対して一時的な有効期間を持つ <xref:System.IDisposable> インスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="18586-345">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="18586-346">インスタンスは、ルート スコープ (ルート コンテナー) で解決されます。</span><span class="sxs-lookup"><span data-stu-id="18586-346">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="18586-347">インスタンスは、スコープが終了する前に破棄する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-347">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="18586-348">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="18586-348">**Solution**</span></span>

<span data-ttu-id="18586-349">ファクトリ パターンを使用して、親スコープの外部にインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="18586-349">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="18586-350">このような状況では、通常、アプリケーションには、最終的な型のコンストラクターを直接呼び出す `Create` メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="18586-350">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="18586-351">最終的な型に他の依存関係がある場合、ファクトリは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="18586-351">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="18586-352">そのコンストラクターで <xref:System.IServiceProvider> を受け取る。</span><span class="sxs-lookup"><span data-stu-id="18586-352">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="18586-353"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> を使用してコンテナー外部でインスタンスをインスタンス化し、同時にコンテナーを依存関係のために使用する。</span><span class="sxs-lookup"><span data-stu-id="18586-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="18586-354">共有インスタンス、限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="18586-354">Shared instance, limited lifetime</span></span>

<span data-ttu-id="18586-355">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="18586-355">**Scenario**</span></span>

<span data-ttu-id="18586-356">アプリでは、複数のサービスにわたって共有 <xref:System.IDisposable> インスタンスが必要ですが、<xref:System.IDisposable> インスタンスの有効期間は限られています。</span><span class="sxs-lookup"><span data-stu-id="18586-356">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="18586-357">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="18586-357">**Solution**</span></span>

<span data-ttu-id="18586-358">インスタンスをスコープ付きの有効期間で登録します。</span><span class="sxs-lookup"><span data-stu-id="18586-358">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="18586-359"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> を使用して新しい <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>を作成します。</span><span class="sxs-lookup"><span data-stu-id="18586-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="18586-360">スコープの <xref:System.IServiceProvider> を使用して必要なサービスを取得します。</span><span class="sxs-lookup"><span data-stu-id="18586-360">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="18586-361">不要になったスコープを破棄します。</span><span class="sxs-lookup"><span data-stu-id="18586-361">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="18586-362">IDisposable の一般的なガイドライン</span><span class="sxs-lookup"><span data-stu-id="18586-362">General IDisposable guidelines</span></span>

* <span data-ttu-id="18586-363"><xref:System.IDisposable> インスタンスは、一時的な有効期間で登録しないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-363">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="18586-364">代わりに、ファクトリ パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-364">Use the factory pattern instead.</span></span>
* <span data-ttu-id="18586-365">一時的またはスコープ付きの有効期間の <xref:System.IDisposable> インスタンスをルート スコープ内で解決しないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-365">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="18586-366">唯一の例外は、アプリが <xref:System.IServiceProvider> を作成/再作成し、破棄する場合ですが、これは理想的なパターンではありません。</span><span class="sxs-lookup"><span data-stu-id="18586-366">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="18586-367">DI を使用して <xref:System.IDisposable> 依存関係を受け取る場合、受信側が <xref:System.IDisposable> 自体を実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-367">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="18586-368"><xref:System.IDisposable> 依存関係の受信側は、その依存関係で <xref:System.IDisposable.Dispose%2A> を呼び出してはなりません。</span><span class="sxs-lookup"><span data-stu-id="18586-368">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="18586-369">サービスの有効期間を制御するには、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-369">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="18586-370">スコープは階層構造ではなく、スコープ間に特別な接続はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-370">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="18586-371">既定のサービス コンテナーの置換</span><span class="sxs-lookup"><span data-stu-id="18586-371">Default service container replacement</span></span>

<span data-ttu-id="18586-372">組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを目的としたものです。</span><span class="sxs-lookup"><span data-stu-id="18586-372">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="18586-373">次のようなサポートされない特定の機能が必要な場合でなければ、組み込みのコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="18586-373">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="18586-374">プロパティの挿入</span><span class="sxs-lookup"><span data-stu-id="18586-374">Property injection</span></span>
* <span data-ttu-id="18586-375">名前に基づく挿入</span><span class="sxs-lookup"><span data-stu-id="18586-375">Injection based on name</span></span>
* <span data-ttu-id="18586-376">子コンテナー</span><span class="sxs-lookup"><span data-stu-id="18586-376">Child containers</span></span>
* <span data-ttu-id="18586-377">有効期間のカスタム管理</span><span class="sxs-lookup"><span data-stu-id="18586-377">Custom lifetime management</span></span>
* <span data-ttu-id="18586-378">遅延初期化に対する `Func<T>` のサポート</span><span class="sxs-lookup"><span data-stu-id="18586-378">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="18586-379">規則に基づく登録</span><span class="sxs-lookup"><span data-stu-id="18586-379">Convention-based registration</span></span>

<span data-ttu-id="18586-380">次のサードパーティ コンテナーは ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="18586-380">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="18586-381">Autofac</span><span class="sxs-lookup"><span data-stu-id="18586-381">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="18586-382">DryIoc</span><span class="sxs-lookup"><span data-stu-id="18586-382">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="18586-383">Grace</span><span class="sxs-lookup"><span data-stu-id="18586-383">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="18586-384">LightInject</span><span class="sxs-lookup"><span data-stu-id="18586-384">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="18586-385">Lamar</span><span class="sxs-lookup"><span data-stu-id="18586-385">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="18586-386">Stashbox</span><span class="sxs-lookup"><span data-stu-id="18586-386">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="18586-387">Unity</span><span class="sxs-lookup"><span data-stu-id="18586-387">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="18586-388">スレッド セーフ</span><span class="sxs-lookup"><span data-stu-id="18586-388">Thread safety</span></span>

<span data-ttu-id="18586-389">スレッド セーフのシングルトン サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="18586-389">Create thread-safe singleton services.</span></span> <span data-ttu-id="18586-390">シングルトン サービスに一時的なサービスへの依存関係がある場合、シングルトンによる使い方によっては、一時的なサービスもスレッド セーフであることが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="18586-390">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="18586-391">1 つのサービスのファクトリ メソッド (例: [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A) に対する 2 番目の引数) をスレッド セーフにする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-391">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="18586-392">型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回のみ呼び出されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="18586-392">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="18586-393">Recommendations</span><span class="sxs-lookup"><span data-stu-id="18586-393">Recommendations</span></span>

* <span data-ttu-id="18586-394">`async/await` および `Task` ベースのサービスの解決はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="18586-394">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="18586-395">C# では非同期コンストラクターがサポートされていないため、非同期メソッドはサービスを同期的に解決した後に使用してください。</span><span class="sxs-lookup"><span data-stu-id="18586-395">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="18586-396">データと構成をサービス コンテナーに直接格納しないようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-396">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="18586-397">たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。</span><span class="sxs-lookup"><span data-stu-id="18586-397">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="18586-398">構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-398">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="18586-399">同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-399">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="18586-400">実際のアイテムを DI 経由で要求することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="18586-400">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="18586-401">サービスへの静的なアクセスを行わないようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-401">Avoid static access to services.</span></span> <span data-ttu-id="18586-402">たとえば、他の場所で使用するために [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) を静的フィールドまたはプロパティとしてキャプチャしないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="18586-402">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="18586-403">DI ファクトリを高速かつ同期的に維持します。</span><span class="sxs-lookup"><span data-stu-id="18586-403">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="18586-404">*サービス ロケーター パターン* の使用は避けてください。</span><span class="sxs-lookup"><span data-stu-id="18586-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="18586-405">たとえば、サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService%2A> を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-405">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="18586-406">**正しくない:**</span><span class="sxs-lookup"><span data-stu-id="18586-406">**Incorrect:**</span></span>

    ![正しくないコード](dependency-injection/_static/bad.png)

  <span data-ttu-id="18586-408">**正しい** :</span><span class="sxs-lookup"><span data-stu-id="18586-408">**Correct** :</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="18586-409">回避すべき別のサービス ロケーターのバリエーションは、実行時に依存関係を解決するファクトリを挿入することです。</span><span class="sxs-lookup"><span data-stu-id="18586-409">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="18586-410">この両方のプラクティスによって、複数の[制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)方式が組み合わせられます。</span><span class="sxs-lookup"><span data-stu-id="18586-410">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="18586-411">`HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="18586-411">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="18586-412">`ConfigureServices` で <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> を呼び出すことは避けてください。</span><span class="sxs-lookup"><span data-stu-id="18586-412">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="18586-413">`BuildServiceProvider` の呼び出しは、通常、開発者が `ConfigureServices` でサービスを解決する必要がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="18586-413">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="18586-414">たとえば、構成から `LoginPath` が読み込まれる場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="18586-414">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="18586-415">次の方法は避けてください。</span><span class="sxs-lookup"><span data-stu-id="18586-415">Avoid the following approach:</span></span>

  ![BuildServiceProvider を呼び出すコードが正しくありません](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="18586-417">上の図では、`services.BuildServiceProvider` の下の緑の波線を選択すると、次の ASP0000 警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="18586-417">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="18586-418">アプリケーション コードから ASP0000 呼び出し 'BuildServiceProvider' を行うと、シングルトン サービスの追加のコピーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-418">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="18586-419">'Configure' のパラメーターとして依存関係挿入サービスなどの代替手段を検討してください。</span><span class="sxs-lookup"><span data-stu-id="18586-419">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="18586-420">`BuildServiceProvider` を呼び出すと、2 つ目のコンテナーが作成されます。これを使用すれば、破損したシングルトンが作成され、複数のコンテナーにまたがるオブジェクト グラフへの参照を生じさせることができます。</span><span class="sxs-lookup"><span data-stu-id="18586-420">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="18586-421">`LoginPath` を取得する正しい方法は、DI のオプション パターンの組み込みサポートを使用することです。</span><span class="sxs-lookup"><span data-stu-id="18586-421">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="18586-422">破棄可能な一時的なサービスは、破棄のためにコンテナーによってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="18586-422">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="18586-423">これにより、最上位のコンテナーから解決された場合、メモリ リークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18586-423">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="18586-424">スコープの検証を有効にすることで、アプリにスコープ付きサービスをキャプチャするシングルトンがないようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-424">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="18586-425">詳しくは、「[スコープの検証](#scope-validation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="18586-425">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="18586-426">どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18586-426">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="18586-427">例外はまれです。ほとんどがフレームワーク自体の内の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="18586-427">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="18586-428">DI は静的/グローバル オブジェクト アクセス パターンの *代替機能* です。</span><span class="sxs-lookup"><span data-stu-id="18586-428">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="18586-429">静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。</span><span class="sxs-lookup"><span data-stu-id="18586-429">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="18586-430">DI でのマルチテナントの推奨パターン</span><span class="sxs-lookup"><span data-stu-id="18586-430">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="18586-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) は、ASP.NET Core でモジュール型のマルチテナント アプリケーションを構築するためのアプリケーション フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="18586-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="18586-432">詳細については、[Orchard Core のドキュメント](https://docs.orchardcore.net/en/dev/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-432">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="18586-433">CMS 固有の機能を使用せずに Orchard Core Framework のみを使用してモジュール型のマルチテナント アプリを構築する方法の例については、[Orchard Core のサンプル](https://github.com/OrchardCMS/OrchardCore.Samples)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-433">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="18586-434">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="18586-434">Framework-provided services</span></span>

<span data-ttu-id="18586-435">`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-435">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="18586-436">最初に、`ConfigureServices` に提供される `IServiceCollection` には、フレームワークによって定義されたサービスがあります ([ホストの構成方法](xref:fundamentals/index#host)によって異なります)。</span><span class="sxs-lookup"><span data-stu-id="18586-436">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="18586-437">ASP.NET Core テンプレートに基づくアプリでは、フレームワークによって 250 を超えるサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-437">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="18586-438">次の表に、フレームワークによって登録されるサービスのごく一部を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-438">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="18586-439">サービスの種類</span><span class="sxs-lookup"><span data-stu-id="18586-439">Service Type</span></span>                                                                                    | <span data-ttu-id="18586-440">有効期間</span><span class="sxs-lookup"><span data-stu-id="18586-440">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="18586-441">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="18586-442">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-442">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="18586-443">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-443">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="18586-444">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-444">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="18586-445">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-445">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="18586-446">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="18586-447">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-447">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="18586-448">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-448">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="18586-449">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-449">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="18586-450">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-450">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="18586-451">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-451">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="18586-452">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-452">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="18586-453">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-453">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="18586-454">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-454">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="18586-455">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="18586-455">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="18586-456">NDC カンファレンス DI アプリ開発のパターン</span><span class="sxs-lookup"><span data-stu-id="18586-456">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="18586-457">ASP.NET Core で IDisposables を破棄する 4 つの方法</span><span class="sxs-lookup"><span data-stu-id="18586-457">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="18586-458">依存関係の挿入による ASP.NET Core でのクリーンなコードの作成 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="18586-458">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="18586-459">明示的な依存関係の原則</span><span class="sxs-lookup"><span data-stu-id="18586-459">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="18586-460">制御の反転コンテナーと依存関係の挿入パターン (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="18586-460">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="18586-461">ASP.NET Core DI 内の複数のインターフェイスにサービスを登録する方法</span><span class="sxs-lookup"><span data-stu-id="18586-461">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18586-462">作成者: [Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com)、[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="18586-462">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="18586-463">ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。</span><span class="sxs-lookup"><span data-stu-id="18586-463">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="18586-464">MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="18586-464">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="18586-465">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="18586-465">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="18586-466">依存関係の挿入の概要</span><span class="sxs-lookup"><span data-stu-id="18586-466">Overview of dependency injection</span></span>

<span data-ttu-id="18586-467">" *依存関係* " とは、他のオブジェクトが必要とする任意のオブジェクトのことです。</span><span class="sxs-lookup"><span data-stu-id="18586-467">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="18586-468">アプリ内の他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="18586-468">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="18586-469">クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="18586-469">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="18586-470">`MyDependency` クラスは `IndexModel` クラスの依存関係です。</span><span class="sxs-lookup"><span data-stu-id="18586-470">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="18586-471">このクラスは `MyDependency` のインスタンスを作成し、これに直接依存しています。</span><span class="sxs-lookup"><span data-stu-id="18586-471">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="18586-472">コードの依存関係 (前の例など) には問題が含まれ、次の理由から回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-472">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="18586-473">`MyDependency` を別の実装で置き換えるには、クラスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-473">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="18586-474">`MyDependency` が依存関係を含んでいる場合、これらはクラスによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-474">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="18586-475">複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。</span><span class="sxs-lookup"><span data-stu-id="18586-475">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="18586-476">このような実装では、単体テストを行うことが困難です。</span><span class="sxs-lookup"><span data-stu-id="18586-476">This implementation is difficult to unit test.</span></span> <span data-ttu-id="18586-477">アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。</span><span class="sxs-lookup"><span data-stu-id="18586-477">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="18586-478">依存関係の挿入は、次の方法によってこれらの問題に対応します。</span><span class="sxs-lookup"><span data-stu-id="18586-478">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="18586-479">依存関係の実装を抽象化するための、インターフェイスまたは基底クラスの使用。</span><span class="sxs-lookup"><span data-stu-id="18586-479">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="18586-480">サービス コンテナー内の依存関係の登録。</span><span class="sxs-lookup"><span data-stu-id="18586-480">Registration of the dependency in a service container.</span></span> <span data-ttu-id="18586-481">ASP.NET Core には、組み込みのサービス コンテナー <xref:System.IServiceProvider> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="18586-481">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="18586-482">サービスはアプリの `Startup.ConfigureServices` メソッドに登録されています。</span><span class="sxs-lookup"><span data-stu-id="18586-482">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="18586-483">サービスを使用するクラスのコンストラクターへの、サービスの " *挿入* "。</span><span class="sxs-lookup"><span data-stu-id="18586-483">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="18586-484">依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。</span><span class="sxs-lookup"><span data-stu-id="18586-484">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="18586-485">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、サービスがアプリに提供するメソッドが `IMyDependency` インターフェイスによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="18586-485">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="18586-486">このインターフェイスは、具象型 `MyDependency` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="18586-486">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="18586-487">`MyDependency` では、コンストラクター内で <xref:Microsoft.Extensions.Logging.ILogger`1> が要求されます。</span><span class="sxs-lookup"><span data-stu-id="18586-487">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="18586-488">依存関係の挿入をチェーン形式で使用することはよくあります。</span><span class="sxs-lookup"><span data-stu-id="18586-488">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="18586-489">次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="18586-489">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="18586-490">コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。</span><span class="sxs-lookup"><span data-stu-id="18586-490">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="18586-491">解決する必要がある依存関係の集合的なセットは、通常、" *依存関係ツリー* "、" *依存関係グラフ* "、または " *オブジェクト グラフ* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="18586-491">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="18586-492">`IMyDependency` と `ILogger<TCategoryName>` をサービス コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-492">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="18586-493">`IMyDependency` は `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-493">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="18586-494">`ILogger<TCategoryName>` はログ記録の抽象化インフラストラクチャによって登録されます。したがって、これは、フレームワークによって既定で登録される[フレームワークが提供するサービス](#framework-provided-services)です。</span><span class="sxs-lookup"><span data-stu-id="18586-494">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="18586-495">コンテナーでは、[(ジェネリック) オープン型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)を活用し、すべての [(ジェネリック) 構築型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)を登録する必要をなくすことで、`ILogger<TCategoryName>` を解決します。</span><span class="sxs-lookup"><span data-stu-id="18586-495">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="18586-496">サンプル アプリにおいて、`IMyDependency` サービスは `MyDependency` 具象型を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-496">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="18586-497">登録によって、サービスの有効期間が 1 つの要求の有効期間に範囲設定されます。</span><span class="sxs-lookup"><span data-stu-id="18586-497">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="18586-498">[サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="18586-498">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="18586-499">各 `services.Add{SERVICE_NAME}` 拡張メソッドは、サービスを追加 (および場合によっては構成) します。</span><span class="sxs-lookup"><span data-stu-id="18586-499">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="18586-500">たとえば、`services.AddMvc()` はサービスの :::no-loc(Razor)::: Pages と必須の MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="18586-500">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="18586-501">アプリをこの規則に従わせることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="18586-501">We recommended that apps follow this convention.</span></span> <span data-ttu-id="18586-502">拡張メソッドを [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 名前空間に配置して、サービス登録のグループをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="18586-502">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="18586-503">サービスのコンストラクターで[ビルトイン型](/dotnet/csharp/language-reference/keywords/built-in-types-table) (`string` など) が必要な場合は、[構成](xref:fundamentals/configuration/index)や[オプション パターン](xref:fundamentals/configuration/options)を使って型を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="18586-503">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="18586-504">クラスのコンストラクター経由でサービスのインスタンスが要求されます。サービスはプライベート フィールドに割り当てられて使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-504">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="18586-505">このフィールドは、クラス全体で必要に応じてサービスにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-505">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="18586-506">サンプル アプリでは、`IMyDependency` インスタンスが要求され、サービスの `WriteMessage` メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-506">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="18586-507">Startup に挿入されるサービス</span><span class="sxs-lookup"><span data-stu-id="18586-507">Services injected into Startup</span></span>

<span data-ttu-id="18586-508">汎用ホスト (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスの種類のみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="18586-508">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="18586-509">サービスは `Startup.Configure` に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="18586-509">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="18586-510">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-510">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="18586-511">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="18586-511">Framework-provided services</span></span>

<span data-ttu-id="18586-512">`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-512">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="18586-513">最初に、`ConfigureServices` に提供される `IServiceCollection` には、フレームワークによって定義されたサービスがあります ([ホストの構成方法](xref:fundamentals/index#host)によって異なります)。</span><span class="sxs-lookup"><span data-stu-id="18586-513">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="18586-514">ASP.NET Core テンプレートに基づくアプリが、フレームワークによって登録された何百ものサービスを持つことも珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="18586-514">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="18586-515">次の表に、フレームワークによって登録されたサービスのごく一部のサンプルを示します。</span><span class="sxs-lookup"><span data-stu-id="18586-515">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="18586-516">サービスの種類</span><span class="sxs-lookup"><span data-stu-id="18586-516">Service Type</span></span> | <span data-ttu-id="18586-517">有効期間</span><span class="sxs-lookup"><span data-stu-id="18586-517">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="18586-518">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-518">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="18586-519">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-519">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="18586-520">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-520">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="18586-521">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-521">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="18586-522">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-522">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="18586-523">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-523">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="18586-524">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-524">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="18586-525">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-525">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="18586-526">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-526">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="18586-527">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-527">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="18586-528">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-528">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="18586-529">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-529">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="18586-530">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-530">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="18586-531">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-531">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="18586-532">拡張メソッドを使用した追加サービスの登録する</span><span class="sxs-lookup"><span data-stu-id="18586-532">Register additional services with extension methods</span></span>

<span data-ttu-id="18586-533">サービス (および必要であればサービスが依存するサービス) を登録するためにサービス コレクションの拡張メソッドを使用できる場合は、1 つの `Add{SERVICE_NAME}` 拡張メソッドを使用してそのサービスが必要とするすべてのサービスを登録することが規則です。</span><span class="sxs-lookup"><span data-stu-id="18586-533">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="18586-534">次のコードは、拡張メソッド [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) と <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*> を使用して、コンテナーに追加のサービスを追加する方法の例です。</span><span class="sxs-lookup"><span data-stu-id="18586-534">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="18586-535">詳細については、API ドキュメントにある <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-535">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="18586-536">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="18586-536">Service lifetimes</span></span>

<span data-ttu-id="18586-537">登録される各サービスの適切な有効期間を選択します。</span><span class="sxs-lookup"><span data-stu-id="18586-537">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="18586-538">ASP.NET Core サービスは、次の有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="18586-538">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="18586-539">一時的</span><span class="sxs-lookup"><span data-stu-id="18586-539">Transient</span></span>

<span data-ttu-id="18586-540">有効期間が一時的なサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) は、サービス コンテナーから要求されるたびに作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-540">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="18586-541">この有効期間は、軽量でステートレスのサービスに最適です。</span><span class="sxs-lookup"><span data-stu-id="18586-541">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="18586-542">要求を処理するアプリでは、一時的なサービスが要求の最後に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-542">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="18586-543">スコープ</span><span class="sxs-lookup"><span data-stu-id="18586-543">Scoped</span></span>

<span data-ttu-id="18586-544">有効期間がスコープのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) は、クライアント要求 (接続) ごとに 1 回作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-544">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="18586-545">要求を処理するアプリでは、スコープ付きサービスは要求の最後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-545">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="18586-546">ミドルウェアでスコープ サービスを使用している場合、サービスを `Invoke` または `InvokeAsync` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="18586-546">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="18586-547">コンストラクターを使用して挿入すると、サービスがシングルトンのように動作するよう強制されるので、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用した挿入は行わないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-547">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="18586-548">詳細については、「<xref:fundamentals/middleware/write#per-request-middleware-dependencies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-548">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="18586-549">シングルトン</span><span class="sxs-lookup"><span data-stu-id="18586-549">Singleton</span></span>

<span data-ttu-id="18586-550">有効期間がシングルトンのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) は、最初に要求されたときに作成されます (または、`Startup.ConfigureServices` が実行されて、サービス登録でインスタンスが指定された場合)。</span><span class="sxs-lookup"><span data-stu-id="18586-550">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="18586-551">以降の要求は、すべて同じインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-551">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="18586-552">アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="18586-552">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="18586-553">クラス内のオブジェクトの有効期間を管理するために、シングルトンの設計パターンを実装してユーザー コードを提供しないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-553">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="18586-554">要求を処理するアプリでは、<xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> がアプリのシャットダウン時に破棄されたとき、シングルトン サービスが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-554">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="18586-555">シングルトンからスコープ サービスを解決するのは危険です。</span><span class="sxs-lookup"><span data-stu-id="18586-555">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="18586-556">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18586-556">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="18586-557">サービス登録メソッド</span><span class="sxs-lookup"><span data-stu-id="18586-557">Service registration methods</span></span>

<span data-ttu-id="18586-558">サービス登録拡張メソッドでは、特定のシナリオで役立つオーバーロードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="18586-558">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="18586-559">メソッド</span><span class="sxs-lookup"><span data-stu-id="18586-559">Method</span></span> | <span data-ttu-id="18586-560">自動</span><span class="sxs-lookup"><span data-stu-id="18586-560">Automatic</span></span><br><span data-ttu-id="18586-561">object</span><span class="sxs-lookup"><span data-stu-id="18586-561">object</span></span><br><span data-ttu-id="18586-562">破棄</span><span class="sxs-lookup"><span data-stu-id="18586-562">disposal</span></span> | <span data-ttu-id="18586-563">複数</span><span class="sxs-lookup"><span data-stu-id="18586-563">Multiple</span></span><br><span data-ttu-id="18586-564">実装</span><span class="sxs-lookup"><span data-stu-id="18586-564">implementations</span></span> | <span data-ttu-id="18586-565">引数を渡す</span><span class="sxs-lookup"><span data-stu-id="18586-565">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="18586-566">例:</span><span class="sxs-lookup"><span data-stu-id="18586-566">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="18586-567">はい</span><span class="sxs-lookup"><span data-stu-id="18586-567">Yes</span></span> | <span data-ttu-id="18586-568">はい</span><span class="sxs-lookup"><span data-stu-id="18586-568">Yes</span></span> | <span data-ttu-id="18586-569">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-569">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="18586-570">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="18586-571">はい</span><span class="sxs-lookup"><span data-stu-id="18586-571">Yes</span></span> | <span data-ttu-id="18586-572">はい</span><span class="sxs-lookup"><span data-stu-id="18586-572">Yes</span></span> | <span data-ttu-id="18586-573">はい</span><span class="sxs-lookup"><span data-stu-id="18586-573">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="18586-574">例:</span><span class="sxs-lookup"><span data-stu-id="18586-574">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="18586-575">はい</span><span class="sxs-lookup"><span data-stu-id="18586-575">Yes</span></span> | <span data-ttu-id="18586-576">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-576">No</span></span> | <span data-ttu-id="18586-577">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-577">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="18586-578">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-578">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="18586-579">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-579">No</span></span> | <span data-ttu-id="18586-580">はい</span><span class="sxs-lookup"><span data-stu-id="18586-580">Yes</span></span> | <span data-ttu-id="18586-581">はい</span><span class="sxs-lookup"><span data-stu-id="18586-581">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="18586-582">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-582">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="18586-583">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-583">No</span></span> | <span data-ttu-id="18586-584">いいえ</span><span class="sxs-lookup"><span data-stu-id="18586-584">No</span></span> | <span data-ttu-id="18586-585">はい</span><span class="sxs-lookup"><span data-stu-id="18586-585">Yes</span></span> |

<span data-ttu-id="18586-586">型の廃棄の詳細については、「[サービスの破棄](#disposal-of-services)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-586">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="18586-587">実装が複数の場合の一般的なシナリオとしては、[テスト用に型のモックを作成](xref:test/integration-tests#inject-mock-services)します。</span><span class="sxs-lookup"><span data-stu-id="18586-587">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="18586-588">実装型のみでサービスを登録することは、同じ実装とサービスの型でそのサービスを登録することと同じです。</span><span class="sxs-lookup"><span data-stu-id="18586-588">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="18586-589">明示的なサービス型を使用しないメソッドを使用してサービスの複数の実装を登録できないのは、このためです。</span><span class="sxs-lookup"><span data-stu-id="18586-589">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="18586-590">これらのメソッドでは、サービスの複数の " *インスタンス* " を登録できますが、すべて同じ " *実装* " 型になります。</span><span class="sxs-lookup"><span data-stu-id="18586-590">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="18586-591">上記のサービス登録メソッドのずれかを使用して、同じサービス型の複数のサービス インスタンスを登録できます。</span><span class="sxs-lookup"><span data-stu-id="18586-591">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="18586-592">次の例では、`IMyDependency` をサービス型として使用して、`AddSingleton` を 2 回呼び出します。</span><span class="sxs-lookup"><span data-stu-id="18586-592">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="18586-593">2 回目の `AddSingleton` の呼び出しにより、`IMyDependency` として解決された場合は前のものがオーバーライドされ、`IEnumerable<IMyDependency>` を介して複数のサービスが解決された場合は前のものに追加されます。</span><span class="sxs-lookup"><span data-stu-id="18586-593">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="18586-594">`IEnumerable<{SERVICE}>` を介して解決された場合、サービスは登録された順に表示されます。</span><span class="sxs-lookup"><span data-stu-id="18586-594">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="18586-595">フレームワークには `TryAdd{LIFETIME}` 拡張メソッドも用意されており、実装がまだ登録されていない場合にのみ、サービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-595">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="18586-596">次の例では、`AddSingleton` の呼び出しによって、`IMyDependency` の実装として `MyDependency` が登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-596">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="18586-597">`IMyDependency` には登録された実装が既に含まれているため、`TryAddSingleton` の呼び出しでは何も行われません。</span><span class="sxs-lookup"><span data-stu-id="18586-597">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="18586-598">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="18586-598">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="18586-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) メソッドでは、" *同じ型* " の実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="18586-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="18586-600">複数のサービスは、`IEnumerable<{SERVICE}>` によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="18586-600">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="18586-601">サービスを登録するとき、開発者は同じ型のものがまだ追加されていない場合にのみインスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="18586-601">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="18586-602">一般に、このメソッドは、インスタンスのコピーが 2 つコンテナーに登録されるのを回避するために、ライブラリ作成者によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-602">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="18586-603">次の例の最初の行では、`IMyDep1` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="18586-603">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="18586-604">2 行目では `IMyDep2` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="18586-604">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="18586-605">3 行目では何も行われません。`IMyDep1` には `MyDep` の登録済みの実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="18586-605">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="18586-606">コンストラクターの挿入の動作</span><span class="sxs-lookup"><span data-stu-id="18586-606">Constructor injection behavior</span></span>

<span data-ttu-id="18586-607">サービスは 2 つのメカニズムによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="18586-607">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="18586-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:依存関係の挿入コンテナーにサービスを登録することなくオブジェクトの作成を許可します。</span><span class="sxs-lookup"><span data-stu-id="18586-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="18586-609">`ActivatorUtilities` はユーザー側の抽象化 (タグ ヘルパー、MVC コントローラー、モデル バインダーなど) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="18586-609">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="18586-610">コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-610">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="18586-611">`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、 [コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、" *パブリック* " コンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="18586-611">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="18586-612">`ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、該当するコンストラクターが 1 つだけ存在することが必要です。</span><span class="sxs-lookup"><span data-stu-id="18586-612">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="18586-613">コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。</span><span class="sxs-lookup"><span data-stu-id="18586-613">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="18586-614">Entity Framework コンテキスト</span><span class="sxs-lookup"><span data-stu-id="18586-614">Entity Framework contexts</span></span>

<span data-ttu-id="18586-615">Entity Framework コンテキストでは通常、[範囲が指定された有効期間](#service-lifetimes)が利用され、サービス コンテナーに追加されます。これは、Web アプリ データベース操作は通常、その範囲がクライアント要求に設定されるためです。</span><span class="sxs-lookup"><span data-stu-id="18586-615">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="18586-616">データベース コンテキストの登録時、[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) オーバーロードによって有効期間が指定されなかった場合、既定の有効期間が範囲となります。</span><span class="sxs-lookup"><span data-stu-id="18586-616">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="18586-617">有効期間が与えられたサービスの場合、サービスより有効期間が短いデータベース コンテキストを使用できません。</span><span class="sxs-lookup"><span data-stu-id="18586-617">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="18586-618">有効期間と登録のオプション</span><span class="sxs-lookup"><span data-stu-id="18586-618">Lifetime and registration options</span></span>

<span data-ttu-id="18586-619">有効期間と登録のオプションの違いを示すために、タスクを一意の識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。</span><span class="sxs-lookup"><span data-stu-id="18586-619">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="18586-620">次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーは、クラスに要求されたときに、サービスの同じインスタンスか別のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="18586-620">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="18586-621">インターフェイスは `Operation` クラス内で実装されます。</span><span class="sxs-lookup"><span data-stu-id="18586-621">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="18586-622">指定されない場合、`Operation` コンストラクターは GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="18586-622">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="18586-623">`OperationService` が登録されます。これは、その他の `Operation` 型のそれぞれに依存します。</span><span class="sxs-lookup"><span data-stu-id="18586-623">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="18586-624">依存関係の挿入を経由して `OperationService` が要求される場合、これは各サービスの新しいインスタンスか、依存関係のあるサービスの有効期間に基づく既存のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="18586-624">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="18586-625">コンテナーからの要求時に一時的なサービスが作成されるとき、`IOperationTransient` サービスの `OperationId` と `OperationService` の `OperationId` は異なります。</span><span class="sxs-lookup"><span data-stu-id="18586-625">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="18586-626">`OperationService` は `IOperationTransient` クラスの新しいインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18586-626">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="18586-627">新しいインスタンスは異なる `OperationId` を生成します。</span><span class="sxs-lookup"><span data-stu-id="18586-627">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="18586-628">クライアント要求ごとにスコープ サービスが作成されるとき、`IOperationScoped` サービスの `OperationId` は、クライアント要求内の `OperationService` のものと同じになります。</span><span class="sxs-lookup"><span data-stu-id="18586-628">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="18586-629">クライアント要求間で、両方のサービスは異なる `OperationId` 値を共有します。</span><span class="sxs-lookup"><span data-stu-id="18586-629">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="18586-630">シングルトンとシングルトン インスタンスのサービスが 1 回作成され、すべてのクライアント要求とすべてのサービス間で使用されるとき、`OperationId` はすべてのサービス要求の間で一定です。</span><span class="sxs-lookup"><span data-stu-id="18586-630">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="18586-631">`Startup.ConfigureServices` では、各型が名前で指定されている有効期間に従ってコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="18586-631">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="18586-632">`IOperationSingletonInstance` サービスは、`Guid.Empty` の既知の ID を持った特定のインスタンスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="18586-632">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="18586-633">この型が使用されるタイミングは明らかです (その GUID はすべてゼロです)。</span><span class="sxs-lookup"><span data-stu-id="18586-633">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="18586-634">サンプル アプリでは、個々の要求内、および個々の要求間におけるオブジェクトの有効期間が示されます。</span><span class="sxs-lookup"><span data-stu-id="18586-634">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="18586-635">サンプル アプリの `IndexModel` には、各種類の `IOperation` 型と `OperationService` が必要です。</span><span class="sxs-lookup"><span data-stu-id="18586-635">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="18586-636">次に、ページ モデル クラスとサービスのすべての `OperationId` 値が、プロパティの割り当てを通じてページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="18586-636">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="18586-637">2 つの要求の結果を、以下の 2 つの出力に示します。</span><span class="sxs-lookup"><span data-stu-id="18586-637">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="18586-638">**最初の要求:**</span><span class="sxs-lookup"><span data-stu-id="18586-638">**First request:**</span></span>

<span data-ttu-id="18586-639">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="18586-639">Controller operations:</span></span>

<span data-ttu-id="18586-640">一時的: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="18586-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="18586-641">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="18586-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="18586-642">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="18586-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="18586-643">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="18586-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="18586-644">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="18586-644">`OperationService` operations:</span></span>

<span data-ttu-id="18586-645">一時的: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="18586-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="18586-646">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="18586-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="18586-647">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="18586-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="18586-648">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="18586-648">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="18586-649">**2 番目の要求:**</span><span class="sxs-lookup"><span data-stu-id="18586-649">**Second request:**</span></span>

<span data-ttu-id="18586-650">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="18586-650">Controller operations:</span></span>

<span data-ttu-id="18586-651">一時的: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="18586-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="18586-652">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="18586-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="18586-653">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="18586-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="18586-654">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="18586-654">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="18586-655">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="18586-655">`OperationService` operations:</span></span>

<span data-ttu-id="18586-656">一時的: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="18586-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="18586-657">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="18586-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="18586-658">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="18586-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="18586-659">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="18586-659">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="18586-660">要求内および要求間で、どの `OperationId` 値が変化しているかを確認してください。</span><span class="sxs-lookup"><span data-stu-id="18586-660">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="18586-661">" *一時的な* " オブジェクトは常に異なります。</span><span class="sxs-lookup"><span data-stu-id="18586-661">*Transient* objects are always different.</span></span> <span data-ttu-id="18586-662">1 番目と 2 番目のクライアント要求の一時的な `OperationId` 値は、`OperationService` 操作とクライアント要求間の両方に対して異なります。</span><span class="sxs-lookup"><span data-stu-id="18586-662">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="18586-663">個々のサービス要求とクライアント要求に対して、新しいインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="18586-663">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="18586-664">*Scoped* オブジェクトは、1 つのクライアント要求内では同じですが、複数のクライアント要求間では異なります。</span><span class="sxs-lookup"><span data-stu-id="18586-664">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="18586-665">" *シングルトン* " オブジェクトは、`Operation` インスタンスが `Startup.ConfigureServices` で提供されるかどうかに関係なく、すべてのオブジェクトとすべての要求について同じです。</span><span class="sxs-lookup"><span data-stu-id="18586-665">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="18586-666">main からサービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="18586-666">Call services from main</span></span>

<span data-ttu-id="18586-667">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) を使用して <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成し、アプリのスコープ内のスコープ サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="18586-667">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="18586-668">この方法は、起動時に初期化タスクを実行するために、スコープ サービスにアクセスするのに便利です。</span><span class="sxs-lookup"><span data-stu-id="18586-668">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="18586-669">次の例では、`Program.Main` で `MyScopedService` のコンテキストを取得する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-669">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="18586-670">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="18586-670">Scope validation</span></span>

<span data-ttu-id="18586-671">アプリが開発環境で実行されている場合、既定のサービス プロバイダーは次を確認するためのチェックを実行します。</span><span class="sxs-lookup"><span data-stu-id="18586-671">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="18586-672">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="18586-672">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="18586-673">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="18586-673">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="18586-674"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-674">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="18586-675">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-675">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="18586-676">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-676">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="18586-677">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="18586-677">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="18586-678">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="18586-678">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="18586-679">詳細については、「<xref:fundamentals/host/web-host#scope-validation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18586-679">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="18586-680">要求サービス</span><span class="sxs-lookup"><span data-stu-id="18586-680">Request Services</span></span>

<span data-ttu-id="18586-681">`HttpContext` からの ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) コレクションを通じて公開されます。</span><span class="sxs-lookup"><span data-stu-id="18586-681">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="18586-682">要求サービスは、アプリの一部として構成および要求されるサービスを表します。</span><span class="sxs-lookup"><span data-stu-id="18586-682">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="18586-683">オブジェクトで依存関係を指定すると、これらは `ApplicationServices` ではなく `RequestServices` で検出された型で満たされます。</span><span class="sxs-lookup"><span data-stu-id="18586-683">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="18586-684">一般に、アプリから直接これらのプロパティを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-684">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="18586-685">代わりに、クラスのコンストラクターを介してクラスに必要な型を要求し、フレームワークに依存関係を挿入させます。</span><span class="sxs-lookup"><span data-stu-id="18586-685">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="18586-686">これにより、テストしやすいクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="18586-686">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="18586-687">コンストラクターのパラメーターとして依存関係を要求し、`RequestServices` コレクションにアクセスするようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-687">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="18586-688">依存関係の挿入のためのサービスの設計</span><span class="sxs-lookup"><span data-stu-id="18586-688">Design services for dependency injection</span></span>

<span data-ttu-id="18586-689">ベスト プラクティスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="18586-689">Best practices are to:</span></span>

* <span data-ttu-id="18586-690">各依存関係を取得するために依存関係の挿入を使用するようサービスを設計します。</span><span class="sxs-lookup"><span data-stu-id="18586-690">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="18586-691">ステートフル、静的クラス、およびメンバーは避けてください。</span><span class="sxs-lookup"><span data-stu-id="18586-691">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="18586-692">代わりにシングルトン サービスを使用するようにアプリを設計し、グローバルな状態を作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-692">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="18586-693">サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。</span><span class="sxs-lookup"><span data-stu-id="18586-693">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="18586-694">直接のインスタンス化は、コードの固有の実装につながります。</span><span class="sxs-lookup"><span data-stu-id="18586-694">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="18586-695">アプリのクラスを、小さく、十分に要素に分割された、テストしやすいものにします。</span><span class="sxs-lookup"><span data-stu-id="18586-695">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="18586-696">クラスに含まれる挿入される依存関係が多すぎるように見える場合、これは通常、クラスが担当する役割が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反していることのサインです。</span><span class="sxs-lookup"><span data-stu-id="18586-696">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="18586-697">責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。</span><span class="sxs-lookup"><span data-stu-id="18586-697">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="18586-698">:::no-loc(Razor)::: Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。</span><span class="sxs-lookup"><span data-stu-id="18586-698">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="18586-699">ビジネス ルールとデータ アクセスの実装の詳細は、これらの[個別の問題](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)に適したクラスに分離する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-699">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="18586-700">サービスの破棄</span><span class="sxs-lookup"><span data-stu-id="18586-700">Disposal of services</span></span>

<span data-ttu-id="18586-701">コンテナーは、作成する <xref:System.IDisposable> 型の <xref:System.IDisposable.Dispose*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="18586-701">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="18586-702">ユーザー コードによってコンテナーに追加されたインスタンスは、自動的に破棄されません。</span><span class="sxs-lookup"><span data-stu-id="18586-702">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="18586-703">次の例では、サービスがサービス コンテナーによって作成され、自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="18586-703">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="18586-704">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="18586-704">In the following example:</span></span>

* <span data-ttu-id="18586-705">サービス インスタンスは、サービス コンテナーによって作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="18586-705">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="18586-706">サービスの有効期間は、フレームワークによって認識されません。</span><span class="sxs-lookup"><span data-stu-id="18586-706">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="18586-707">フレームワークでは、サービスが自動的に破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="18586-707">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="18586-708">サービスが開発者コードで明示的に破棄されていない場合は、アプリがシャットダウンするまで保持されます。</span><span class="sxs-lookup"><span data-stu-id="18586-708">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="18586-709">一時的なインスタンスと共有インスタンスのための IDisposable ガイダンス</span><span class="sxs-lookup"><span data-stu-id="18586-709">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="18586-710">一時的で限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="18586-710">Transient, limited lifetime</span></span>

<span data-ttu-id="18586-711">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="18586-711">**Scenario**</span></span>

<span data-ttu-id="18586-712">アプリには、次のいずれかのシナリオに対して一時的な有効期間を持つ <xref:System.IDisposable> インスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="18586-712">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="18586-713">インスタンスは、ルート スコープで解決されます。</span><span class="sxs-lookup"><span data-stu-id="18586-713">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="18586-714">インスタンスは、スコープが終了する前に破棄する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-714">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="18586-715">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="18586-715">**Solution**</span></span>

<span data-ttu-id="18586-716">ファクトリ パターンを使用して、親スコープの外部にインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="18586-716">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="18586-717">このような状況では、通常、アプリケーションには、最終的な型のコンストラクターを直接呼び出す `Create` メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="18586-717">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="18586-718">最終的な型に他の依存関係がある場合、ファクトリは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="18586-718">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="18586-719">そのコンストラクターで <xref:System.IServiceProvider> を受け取る。</span><span class="sxs-lookup"><span data-stu-id="18586-719">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="18586-720"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> を使用してコンテナー外部でインスタンスをインスタンス化し、同時にコンテナーを依存関係のために使用する。</span><span class="sxs-lookup"><span data-stu-id="18586-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="18586-721">共有インスタンス、限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="18586-721">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="18586-722">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="18586-722">**Scenario**</span></span>

<span data-ttu-id="18586-723">アプリでは、複数のサービスにわたって共有 <xref:System.IDisposable> インスタンスが必要ですが、<xref:System.IDisposable> の有効期間は限られています。</span><span class="sxs-lookup"><span data-stu-id="18586-723">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="18586-724">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="18586-724">**Solution**</span></span>

<span data-ttu-id="18586-725">インスタンスをスコープ付きの有効期間に登録します。</span><span class="sxs-lookup"><span data-stu-id="18586-725">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="18586-726"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> を使用してを開始し、新しい <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成します。</span><span class="sxs-lookup"><span data-stu-id="18586-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="18586-727">スコープの <xref:System.IServiceProvider> を使用して必要なサービスを取得します。</span><span class="sxs-lookup"><span data-stu-id="18586-727">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="18586-728">有効期間が終了するときにスコープを破棄します。</span><span class="sxs-lookup"><span data-stu-id="18586-728">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="18586-729">一般的なガイドライン</span><span class="sxs-lookup"><span data-stu-id="18586-729">General Guidelines</span></span>

* <span data-ttu-id="18586-730"><xref:System.IDisposable> インスタンスは、一時的なスコープを使用して登録しないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-730">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="18586-731">代わりに、ファクトリ パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="18586-731">Use the factory pattern instead.</span></span>
* <span data-ttu-id="18586-732">一時的またはスコープ付きの <xref:System.IDisposable> インスタンスをルート スコープ内で解決しないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-732">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="18586-733">唯一の一般的な例外は、アプリが <xref:System.IServiceProvider> を作成/再作成し、破棄する場合です。これは理想的なパターンではありません。</span><span class="sxs-lookup"><span data-stu-id="18586-733">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="18586-734">DI を使用して <xref:System.IDisposable> 依存関係を受け取る場合、受信側が <xref:System.IDisposable> 自体を実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-734">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="18586-735"><xref:System.IDisposable> 依存関係の受信側は、その依存関係で <xref:System.IDisposable.Dispose%2A> を呼び出してはなりません。</span><span class="sxs-lookup"><span data-stu-id="18586-735">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="18586-736">サービスの有効期間を制御するには、スコープを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-736">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="18586-737">スコープは階層構造ではなく、スコープ間に特別な接続はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-737">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="18586-738">既定のサービス コンテナーの置換</span><span class="sxs-lookup"><span data-stu-id="18586-738">Default service container replacement</span></span>

<span data-ttu-id="18586-739">組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを目的としたものです。</span><span class="sxs-lookup"><span data-stu-id="18586-739">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="18586-740">組み込みコンテナーでサポートされない、以下のような特定の機能が必要な場合を除き、組み込みコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="18586-740">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="18586-741">プロパティの挿入</span><span class="sxs-lookup"><span data-stu-id="18586-741">Property injection</span></span>
* <span data-ttu-id="18586-742">名前に基づく挿入</span><span class="sxs-lookup"><span data-stu-id="18586-742">Injection based on name</span></span>
* <span data-ttu-id="18586-743">子コンテナー</span><span class="sxs-lookup"><span data-stu-id="18586-743">Child containers</span></span>
* <span data-ttu-id="18586-744">有効期間のカスタム管理</span><span class="sxs-lookup"><span data-stu-id="18586-744">Custom lifetime management</span></span>
* <span data-ttu-id="18586-745">遅延初期化に対する `Func<T>` のサポート</span><span class="sxs-lookup"><span data-stu-id="18586-745">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="18586-746">規則に基づく登録</span><span class="sxs-lookup"><span data-stu-id="18586-746">Convention-based registration</span></span>

<span data-ttu-id="18586-747">次のサードパーティ コンテナーは ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="18586-747">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="18586-748">Autofac</span><span class="sxs-lookup"><span data-stu-id="18586-748">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="18586-749">DryIoc</span><span class="sxs-lookup"><span data-stu-id="18586-749">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="18586-750">Grace</span><span class="sxs-lookup"><span data-stu-id="18586-750">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="18586-751">LightInject</span><span class="sxs-lookup"><span data-stu-id="18586-751">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="18586-752">Lamar</span><span class="sxs-lookup"><span data-stu-id="18586-752">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="18586-753">Stashbox</span><span class="sxs-lookup"><span data-stu-id="18586-753">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="18586-754">Unity</span><span class="sxs-lookup"><span data-stu-id="18586-754">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="18586-755">スレッド セーフ</span><span class="sxs-lookup"><span data-stu-id="18586-755">Thread safety</span></span>

<span data-ttu-id="18586-756">スレッド セーフのシングルトン サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="18586-756">Create thread-safe singleton services.</span></span> <span data-ttu-id="18586-757">シングルトン サービスに一時的サービスへの依存関係がある場合、シングルトンによる一時的サービスの使い方によっては、一時的サービスもスレッド セーフであることが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="18586-757">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="18586-758">1 つのサービスのファクトリ メソッド (例: [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) に対する 2 番目の引数) をスレッド セーフにする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="18586-758">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="18586-759">型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回呼び出されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="18586-759">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="18586-760">推奨事項</span><span class="sxs-lookup"><span data-stu-id="18586-760">Recommendations</span></span>

* <span data-ttu-id="18586-761">`async/await` および `Task` ベースのサービスの解決はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="18586-761">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="18586-762">C# では非同期コンストラクターがサポートされていないため、推奨パターンは、サービスを同期的に解決した後に、非同期メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="18586-762">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="18586-763">データと構成をサービス コンテナーに直接格納しないようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-763">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="18586-764">たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。</span><span class="sxs-lookup"><span data-stu-id="18586-764">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="18586-765">構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-765">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="18586-766">同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="18586-766">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="18586-767">実際のアイテムを DI 経由で要求することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="18586-767">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="18586-768">サービスへの静的なアクセスを行わないようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-768">Avoid static access to services.</span></span> <span data-ttu-id="18586-769">たとえば、他の場所で使用するために [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) の静的な型指定を行わないようにします。</span><span class="sxs-lookup"><span data-stu-id="18586-769">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="18586-770">*サービス ロケーター パターン* は、 [制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)戦略が混在しているので使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-770">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="18586-771">サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService*> を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-771">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="18586-772">**正しくない:**</span><span class="sxs-lookup"><span data-stu-id="18586-772">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="18586-773">**正しい** :</span><span class="sxs-lookup"><span data-stu-id="18586-773">**Correct** :</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="18586-774"><xref:System.IServiceProvider.GetService*> を使用して、実行時に依存関係を解決するファクトリを挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="18586-774">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="18586-775">`HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="18586-775">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="18586-776">どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18586-776">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="18586-777">例外はまれです。ほとんどがフレームワーク自体の内の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="18586-777">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="18586-778">DI は静的/グローバル オブジェクト アクセス パターンの *代替機能* です。</span><span class="sxs-lookup"><span data-stu-id="18586-778">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="18586-779">静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。</span><span class="sxs-lookup"><span data-stu-id="18586-779">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18586-780">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="18586-780">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="18586-781">ASP.NET Core で IDisposables を破棄する 4 つの方法</span><span class="sxs-lookup"><span data-stu-id="18586-781">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="18586-782">依存関係の挿入による ASP.NET Core でのクリーンなコードの作成 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="18586-782">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="18586-783">明示的な依存関係の原則</span><span class="sxs-lookup"><span data-stu-id="18586-783">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="18586-784">制御の反転コンテナーと依存関係の挿入パターン (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="18586-784">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="18586-785">ASP.NET Core DI 内の複数のインターフェイスにサービスを登録する方法</span><span class="sxs-lookup"><span data-stu-id="18586-785">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

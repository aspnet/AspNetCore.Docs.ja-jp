---
title: 'ASP.NET Core :::no-loc(Blazor)::: 依存関係の挿入'
author: guardrex
description: ':::no-loc(Blazor)::: アプリでコンポーネントにサービスを挿入する方法について説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 32228cc98b4650d5871369511808e519a4f65be4
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343677"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="063cb-103">ASP.NET Core :::no-loc(Blazor)::: 依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="063cb-103">ASP.NET Core :::no-loc(Blazor)::: dependency injection</span></span>

<span data-ttu-id="063cb-104">作成者: [Rainer Stropek](https://www.timecockpit.com)、[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="063cb-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="063cb-105">:::no-loc(Blazor)::: では、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="063cb-105">:::no-loc(Blazor)::: supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="063cb-106">アプリでは、組み込みサービスをコンポーネントに挿入することにより、サービスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="063cb-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="063cb-107">また、アプリでは、カスタム サービスを定義して登録し、DI によってアプリ全体でそれを使用できるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="063cb-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="063cb-108">DI は、中央の場所で構成されたサービスにアクセスするための手法です。</span><span class="sxs-lookup"><span data-stu-id="063cb-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="063cb-109">これは、:::no-loc(Blazor)::: アプリで次のような場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="063cb-109">This can be useful in :::no-loc(Blazor)::: apps to:</span></span>

* <span data-ttu-id="063cb-110">サービス クラスの 1 つのインスタンスを、多数のコンポーネント間で共有します。これは、" *シングルトン* " サービスと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="063cb-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="063cb-111">参照の抽象化を使用することで、具象サービス クラスからコンポーネントを切り離します。</span><span class="sxs-lookup"><span data-stu-id="063cb-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="063cb-112">たとえば、アプリ内のデータにアクセスするためのインターフェイス `IDataAccess` について考えます。</span><span class="sxs-lookup"><span data-stu-id="063cb-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="063cb-113">このインターフェイスは、具象クラス `DataAccess` によって実装され、アプリのサービス コンテナーにサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="063cb-114">コンポーネントで DI を使用して `IDataAccess` の実装を受け取ると、コンポーネントは具象型に結合されません。</span><span class="sxs-lookup"><span data-stu-id="063cb-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="063cb-115">たとえば単体テストでのモック実装の場合など、実装をスワップすることができます。</span><span class="sxs-lookup"><span data-stu-id="063cb-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="063cb-116">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="063cb-116">Default services</span></span>

<span data-ttu-id="063cb-117">既定のサービスは、アプリのサービス コレクションに自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="063cb-118">サービス</span><span class="sxs-lookup"><span data-stu-id="063cb-118">Service</span></span> | <span data-ttu-id="063cb-119">有効期間</span><span class="sxs-lookup"><span data-stu-id="063cb-119">Lifetime</span></span> | <span data-ttu-id="063cb-120">説明</span><span class="sxs-lookup"><span data-stu-id="063cb-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="063cb-121">スコープ</span><span class="sxs-lookup"><span data-stu-id="063cb-121">Scoped</span></span> | <span data-ttu-id="063cb-122">URI によって識別されるリソースに HTTP 要求を送信し、そのリソースから HTTP 応答を受信するためのメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="063cb-123">:::no-loc(Blazor WebAssembly)::: アプリの <xref:System.Net.Http.HttpClient> のインスタンスでは、バックグラウンドでの HTTP トラフィックの処理にブラウザーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-123">The instance of <xref:System.Net.Http.HttpClient> in a :::no-loc(Blazor WebAssembly)::: app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="063cb-124">:::no-loc(Blazor Server)::: アプリには、既定でサービスとして構成される <xref:System.Net.Http.HttpClient> は含まれません。</span><span class="sxs-lookup"><span data-stu-id="063cb-124">:::no-loc(Blazor Server)::: apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="063cb-125">:::no-loc(Blazor Server)::: アプリには <xref:System.Net.Http.HttpClient> を指定します。</span><span class="sxs-lookup"><span data-stu-id="063cb-125">Provide an <xref:System.Net.Http.HttpClient> to a :::no-loc(Blazor Server)::: app.</span></span><br><br><span data-ttu-id="063cb-126">詳細については、「<xref:blazor/call-web-api>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="063cb-127"><xref:System.Net.Http.HttpClient> は、シングルトンではなく、スコープ サービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="063cb-128">詳細については、「[サービスの有効期間](#service-lifetime)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="063cb-129">シングルトン (:::no-loc(Blazor WebAssembly):::)</span><span class="sxs-lookup"><span data-stu-id="063cb-129">Singleton (:::no-loc(Blazor WebAssembly):::)</span></span><br><span data-ttu-id="063cb-130">スコープ (:::no-loc(Blazor Server):::)</span><span class="sxs-lookup"><span data-stu-id="063cb-130">Scoped (:::no-loc(Blazor Server):::)</span></span> | <span data-ttu-id="063cb-131">JavaScript の呼び出しがディスパッチされる JavaScript ランタイムのインスタンスを表します。</span><span class="sxs-lookup"><span data-stu-id="063cb-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="063cb-132">詳細については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="063cb-133">シングルトン (:::no-loc(Blazor WebAssembly):::)</span><span class="sxs-lookup"><span data-stu-id="063cb-133">Singleton (:::no-loc(Blazor WebAssembly):::)</span></span><br><span data-ttu-id="063cb-134">スコープ (:::no-loc(Blazor Server):::)</span><span class="sxs-lookup"><span data-stu-id="063cb-134">Scoped (:::no-loc(Blazor Server):::)</span></span> | <span data-ttu-id="063cb-135">URI とナビゲーション状態を操作するためのヘルパーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="063cb-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="063cb-136">詳細については、「[URI およびナビゲーション状態ヘルパー](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="063cb-137">カスタム サービス プロバイダーでは、表に示されている既定のサービスは自動的に提供されません。</span><span class="sxs-lookup"><span data-stu-id="063cb-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="063cb-138">カスタム サービス プロバイダーを使用し、表に示されているいずれかのサービスが必要な場合は、必要なサービスを新しいサービス プロバイダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="063cb-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="063cb-139">サービスをアプリに追加する</span><span class="sxs-lookup"><span data-stu-id="063cb-139">Add services to an app</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="063cb-140">`Program.cs` の `Main` メソッドで、アプリのサービス コレクション用のサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="063cb-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="063cb-141">次の例では、`MyDependency` の実装が `IMyDependency` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="063cb-142">ホストが構築されると、コンポーネントがレンダリングされる前に、ルート DI スコープからサービスにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="063cb-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="063cb-143">これは、コンテンツをレンダリングする前に初期化ロジックを実行する場合に役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="063cb-143">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="063cb-144">また、ホストでは、アプリの中央構成インスタンスも提供されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="063cb-145">前の例を基にして、天気予報サービスの URL を、既定の構成ソース (`:::no-loc(appsettings.json):::` など) から `InitializeWeatherAsync` に渡します。</span><span class="sxs-lookup"><span data-stu-id="063cb-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `:::no-loc(appsettings.json):::`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### :::no-loc(Blazor Server):::

<span data-ttu-id="063cb-146">新しいアプリを作成した後、`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="063cb-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="063cb-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> メソッドには、サービス記述子オブジェクト (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) のリストである <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> が渡されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="063cb-148">サービスは、`ConfigureServices` メソッドでサービス コレクションにサービス記述子を提供することによって追加されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="063cb-149">次の例では、`IDataAccess` インターフェイスとその具象実装 `DataAccess` での概念を示します。</span><span class="sxs-lookup"><span data-stu-id="063cb-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="063cb-150">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="063cb-150">Service lifetime</span></span>

<span data-ttu-id="063cb-151">サービスは、次の表に示す有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="063cb-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="063cb-152">有効期間</span><span class="sxs-lookup"><span data-stu-id="063cb-152">Lifetime</span></span> | <span data-ttu-id="063cb-153">説明</span><span class="sxs-lookup"><span data-stu-id="063cb-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="063cb-154">現在、:::no-loc(Blazor WebAssembly)::: アプリには DI スコープの概念はありません。</span><span class="sxs-lookup"><span data-stu-id="063cb-154">:::no-loc(Blazor WebAssembly)::: apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="063cb-155">`Scoped` 登録済みサービスは `Singleton` サービスのように動作します。</span><span class="sxs-lookup"><span data-stu-id="063cb-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="063cb-156">ただし、:::no-loc(Blazor Server)::: ホスティング モデルでは、`Scoped` 有効期間がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="063cb-156">However, the :::no-loc(Blazor Server)::: hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="063cb-157">:::no-loc(Blazor Server)::: アプリでは、スコープ サービスの登録は " *接続* " にスコープされます。</span><span class="sxs-lookup"><span data-stu-id="063cb-157">In :::no-loc(Blazor Server)::: apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="063cb-158">このため、現在の目的が :::no-loc(Blazor WebAssembly)::: アプリのブラウザーでクライアント側を実行する場合でも、現在のユーザーにスコープする必要があるサービスには、スコープ サービスを使用することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser in a :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="063cb-159">DI では、サービスの " *単一インスタンス* " が作成されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="063cb-160">`Singleton` サービスを必要とするすべてのコンポーネントは、同じサービスのインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="063cb-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="063cb-161">コンポーネントは、サービス コンテナーから `Transient` サービスのインスタンスを取得するたびに、サービスの " *新しいインスタンス* " を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="063cb-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="063cb-162">DI システムは、ASP.NET Core の DI システムが基になっています。</span><span class="sxs-lookup"><span data-stu-id="063cb-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="063cb-163">詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="063cb-164">コンポーネント内のサービスを要求する</span><span class="sxs-lookup"><span data-stu-id="063cb-164">Request a service in a component</span></span>

<span data-ttu-id="063cb-165">サービスがサービス コレクションに追加された後、[\@inject](xref:mvc/views/razor#inject) :::no-loc(Razor)::: ディレクティブを使用して、サービスをコンポーネントに挿入します。</span><span class="sxs-lookup"><span data-stu-id="063cb-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) :::no-loc(Razor)::: directive.</span></span> <span data-ttu-id="063cb-166">[`@inject`](xref:mvc/views/razor#inject) には、次の 2 つのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="063cb-166">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="063cb-167">型:挿入するサービスの型。</span><span class="sxs-lookup"><span data-stu-id="063cb-167">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="063cb-168">プロパティ:挿入されたアプリ サービスを受け取るプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="063cb-168">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="063cb-169">プロパティを手動で作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="063cb-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="063cb-170">プロパティはコンパイラによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-170">The compiler creates the property.</span></span>

<span data-ttu-id="063cb-171">詳細については、「<xref:mvc/views/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="063cb-172">異なるサービスを挿入するには、複数の [`@inject`](xref:mvc/views/razor#inject) ステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="063cb-172">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="063cb-173">次の例は、[`@inject`](xref:mvc/views/razor#inject) を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="063cb-173">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="063cb-174">`Services.IDataAccess` を実装するサービスを、コンポーネントのプロパティ `DataRepository` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="063cb-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="063cb-175">コードによって `IDataAccess` 抽象化だけが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="063cb-176">内部的には、生成されたプロパティ (`DataRepository`) によって、[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性が使用されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-176">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="063cb-177">通常、この属性を直接使用することはありません。</span><span class="sxs-lookup"><span data-stu-id="063cb-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="063cb-178">コンポーネントで基底クラスが必要であり、基底クラスで挿入されたプロパティも必要な場合は、[`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性を手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="063cb-178">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="063cb-179">基底クラスから派生されたコンポーネントでは、[`@inject`](xref:mvc/views/razor#inject) ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="063cb-179">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="063cb-180">基底クラスの <xref:Microsoft.AspNetCore.Components.InjectAttribute> で十分です。</span><span class="sxs-lookup"><span data-stu-id="063cb-180">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="063cb-181">サービスで DI を使用する</span><span class="sxs-lookup"><span data-stu-id="063cb-181">Use DI in services</span></span>

<span data-ttu-id="063cb-182">複雑なサービスでは、追加のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-182">Complex services might require additional services.</span></span> <span data-ttu-id="063cb-183">前の例では、`DataAccess` で <xref:System.Net.Http.HttpClient> の既定のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-183">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="063cb-184">[`@inject`](xref:mvc/views/razor#inject) (または [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性) は、サービスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="063cb-184">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="063cb-185">代わりに、" *コンストラクター挿入* " を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="063cb-186">サービスのコンストラクターにパラメーターを追加することによって、必要なサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="063cb-187">DI では、サービスを作成するときに、コンストラクターで必要なサービスが認識され、それに応じてサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="063cb-188">次の例では、コンストラクターは DI で <xref:System.Net.Http.HttpClient> を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="063cb-188">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="063cb-189"><xref:System.Net.Http.HttpClient> は既定のサービスです。</span><span class="sxs-lookup"><span data-stu-id="063cb-189"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="063cb-190">コンストラクター挿入の前提条件:</span><span class="sxs-lookup"><span data-stu-id="063cb-190">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="063cb-191">DI によってすべての引数を満たすことができるコンストラクターが 1 つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-191">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="063cb-192">DI で満たすことができない追加のパラメーターは、既定値が指定されている場合に許可されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-192">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="063cb-193">該当するコンストラクターは、`public` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-193">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="063cb-194">該当するコンストラクターが 1 つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-194">One applicable constructor must exist.</span></span> <span data-ttu-id="063cb-195">あいまいさがある場合は、DI で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="063cb-195">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="063cb-196">DI スコープを管理するためのユーティリティの基本コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="063cb-196">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="063cb-197">ASP.NET Core アプリでは、スコープ サービスは通常、現在の要求にスコープされます。</span><span class="sxs-lookup"><span data-stu-id="063cb-197">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="063cb-198">要求が完了すると、スコープ サービスまたは一時サービスは DI システムによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-198">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="063cb-199">:::no-loc(Blazor Server)::: アプリでは、要求スコープはクライアント接続の期間を通して保持されるため、一時サービスとスコープ サービスが予想よりはるかに長く存続する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-199">In :::no-loc(Blazor Server)::: apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="063cb-200">:::no-loc(Blazor WebAssembly)::: アプリでは、スコープ付きの有効期間で登録されたサービスはシングルトンとして扱われるため、通常の ASP.NET Core アプリのスコープ サービスより長く存続します。</span><span class="sxs-lookup"><span data-stu-id="063cb-200">In :::no-loc(Blazor WebAssembly)::: apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="063cb-201">アプリ内の破棄可能な一時サービスを検出するには、「[破棄可能な一時サービスの検出](#detect-transient-disposables)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-201">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="063cb-202">:::no-loc(Blazor)::: アプリでサービスの有効期間を制限するには、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="063cb-202">An approach that limits a service lifetime in :::no-loc(Blazor)::: apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="063cb-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> は <xref:Microsoft.AspNetCore.Components.ComponentBase> から派生された抽象型であり、コンポーネントの有効期間に対応する DI スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="063cb-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="063cb-204">このスコープを使用すると、スコープ付きの有効期間で DI サービスを使用し、コンポーネントと同じ期間だけ持続させることができます。</span><span class="sxs-lookup"><span data-stu-id="063cb-204">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="063cb-205">コンポーネントが破棄されると、コンポーネントのスコープ サービス プロバイダーからのサービスも破棄されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-205">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="063cb-206">これは、次のようなサービスに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="063cb-206">This can be useful for services that:</span></span>

* <span data-ttu-id="063cb-207">一時的な有効期間が不適切であるため、コンポーネント内で再利用する必要がある。</span><span class="sxs-lookup"><span data-stu-id="063cb-207">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="063cb-208">シングルトンの有効期間が不適切であるため、コンポーネント間で共有してはならない。</span><span class="sxs-lookup"><span data-stu-id="063cb-208">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="063cb-209"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 型には、使用できるバージョンが 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="063cb-209">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="063cb-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> は、<xref:Microsoft.AspNetCore.Components.ComponentBase> 型の抽象的で破棄可能な子であり、<xref:System.IServiceProvider>型の保護された <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="063cb-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="063cb-211">このプロバイダーを使用すると、コンポーネントの有効期間にスコープが設定されているサービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="063cb-211">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="063cb-212">[`@inject`](xref:mvc/views/razor#inject) または [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 属性を使用してコンポーネントに挿入された DI サービスは、コンポーネントのスコープでは作成されません。</span><span class="sxs-lookup"><span data-stu-id="063cb-212">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="063cb-213">コンポーネントのスコープを使用するには、<xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> または <xref:System.IServiceProvider.GetService%2A> を使用してサービスを解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-213">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="063cb-214"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロバイダーを使用して解決されたすべてのサービスには、同じスコープから提供される依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="063cb-214">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="063cb-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> から派生する <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> では、スコープ DI プロバイダーから `T` のインスタンスを返すプロパティ <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> が追加されます。</span><span class="sxs-lookup"><span data-stu-id="063cb-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="063cb-216">この型は、アプリで 1 つのプライマリ サービスをコンポーネントのスコープを使用して DI コンテナーに要求するときに、<xref:System.IServiceProvider> のインスタンスを使用せずにスコープ サービスにアクセスするための便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="063cb-216">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="063cb-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロパティを使用できるので、必要に応じて、アプリで他の型のサービスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="063cb-217">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="063cb-218">DI からの Entity Framework Core (EF Core) DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="063cb-218">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="063cb-219">詳細については、「<xref:blazor/blazor-server-ef-core>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-219">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="063cb-220">破棄可能な一時サービスの検出</span><span class="sxs-lookup"><span data-stu-id="063cb-220">Detect transient disposables</span></span>

<span data-ttu-id="063cb-221">次の例は、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> を使用する必要があるアプリ内の破棄可能な一時サービスを検出する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="063cb-221">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="063cb-222">詳細については、「[DI スコープを管理するためのユーティリティの基本コンポーネント クラス](#utility-base-component-classes-to-manage-a-di-scope)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="063cb-222">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="063cb-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="063cb-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="063cb-224">次の例では、`TransientDisposable` が検出されます (`Program.cs`)。</span><span class="sxs-lookup"><span data-stu-id="063cb-224">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### :::no-loc(Blazor Server):::

<span data-ttu-id="063cb-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="063cb-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="063cb-226">`Program`:</span><span class="sxs-lookup"><span data-stu-id="063cb-226">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="063cb-227">次の例では、`TransientDependency` が検出されます (`Startup.cs`)。</span><span class="sxs-lookup"><span data-stu-id="063cb-227">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="063cb-228">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="063cb-228">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="063cb-229">一時的なインスタンスと共有インスタンスのための `IDisposable` ガイダンス</span><span class="sxs-lookup"><span data-stu-id="063cb-229">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>

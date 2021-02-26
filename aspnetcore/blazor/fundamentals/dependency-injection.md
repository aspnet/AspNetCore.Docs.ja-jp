---
title: ASP.NET Core Blazor 依存関係の挿入
author: guardrex
description: Blazor アプリでコンポーネントにサービスを挿入する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 30edffedf1faf96ed54d5380762c8558e478966c
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106922"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="f782a-103">ASP.NET Core Blazor 依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="f782a-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="f782a-104">作成者: [Rainer Stropek](https://www.timecockpit.com)、[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="f782a-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="f782a-105">[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) は、中央の場所で構成されたサービスにアクセスするための手法です。</span><span class="sxs-lookup"><span data-stu-id="f782a-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="f782a-106">フレームワークによって登録されたサービスは、Blazor アプリのコンポーネントに直接挿入できます。</span><span class="sxs-lookup"><span data-stu-id="f782a-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="f782a-107">Blazor アプリによって、カスタム サービスの定義と登録が行われ、DI を通じてアプリ全体でそれらが使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f782a-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="f782a-108">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="f782a-108">Default services</span></span>

<span data-ttu-id="f782a-109">Blazor アプリでよく使用されるサービスを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="f782a-109">The services shown in the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="f782a-110">サービス</span><span class="sxs-lookup"><span data-stu-id="f782a-110">Service</span></span> | <span data-ttu-id="f782a-111">有効期間</span><span class="sxs-lookup"><span data-stu-id="f782a-111">Lifetime</span></span> | <span data-ttu-id="f782a-112">説明</span><span class="sxs-lookup"><span data-stu-id="f782a-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="f782a-113">スコープ</span><span class="sxs-lookup"><span data-stu-id="f782a-113">Scoped</span></span> | <p><span data-ttu-id="f782a-114">URI によって識別されるリソースに HTTP 要求を送信し、そのリソースから HTTP 応答を受信するためのメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="f782a-115">Blazor WebAssembly アプリの <xref:System.Net.Http.HttpClient> のインスタンスでは、バックグラウンドでの HTTP トラフィックの処理にブラウザーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="f782a-116">Blazor Server アプリには、既定でサービスとして構成される <xref:System.Net.Http.HttpClient> は含まれません。</span><span class="sxs-lookup"><span data-stu-id="f782a-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="f782a-117">Blazor Server アプリには <xref:System.Net.Http.HttpClient> を指定します。</span><span class="sxs-lookup"><span data-stu-id="f782a-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="f782a-118">詳細については、「<xref:blazor/call-web-api>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="f782a-119"><xref:System.Net.Http.HttpClient> は、シングルトンではなく、スコープ サービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="f782a-120">詳細については、「[サービスの有効期間](#service-lifetime)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="f782a-121">**Blazor WebAssembly** :シングルトン</span><span class="sxs-lookup"><span data-stu-id="f782a-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="f782a-122">**Blazor Server** :スコープ</span><span class="sxs-lookup"><span data-stu-id="f782a-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="f782a-123">JavaScript の呼び出しがディスパッチされる JavaScript ランタイムのインスタンスを表します。</span><span class="sxs-lookup"><span data-stu-id="f782a-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="f782a-124">詳細については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="f782a-125">**Blazor WebAssembly** :シングルトン</span><span class="sxs-lookup"><span data-stu-id="f782a-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="f782a-126">**Blazor Server** :スコープ</span><span class="sxs-lookup"><span data-stu-id="f782a-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="f782a-127">URI とナビゲーション状態を操作するためのヘルパーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f782a-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="f782a-128">詳細については、「[URI およびナビゲーション状態ヘルパー](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="f782a-129">カスタム サービス プロバイダーでは、表に示されている既定のサービスは自動的に提供されません。</span><span class="sxs-lookup"><span data-stu-id="f782a-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="f782a-130">カスタム サービス プロバイダーを使用し、表に示されているいずれかのサービスが必要な場合は、必要なサービスを新しいサービス プロバイダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="f782a-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="f782a-131">サービスをアプリに追加する</span><span class="sxs-lookup"><span data-stu-id="f782a-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="f782a-132">`Program.cs` の `Program.Main` メソッドで、アプリのサービス コレクション用のサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="f782a-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="f782a-133">次の例では、`MyDependency` の実装が `IMyDependency` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

<span data-ttu-id="f782a-134">ホストが構築されると、コンポーネントがレンダリングされる前に、ルート DI スコープからサービスを使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f782a-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="f782a-135">これは、コンテンツをレンダリングする前に初期化ロジックを実行する場合に役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="f782a-135">This can be useful for running initialization logic before rendering content:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

<span data-ttu-id="f782a-136">ホストによって、アプリの中央構成インスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="f782a-137">前の例を基にして、天気予報サービスの URL を、既定の構成ソース (`appsettings.json` など) から `InitializeWeatherAsync` に渡します。</span><span class="sxs-lookup"><span data-stu-id="f782a-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

<span data-ttu-id="f782a-138">新しいアプリを作成した後、`Startup.cs` で `Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="f782a-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="f782a-139"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> メソッドには、[サービス記述子](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor)オブジェクトのリストである <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> が渡されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="f782a-140">サービスは、`ConfigureServices` メソッドでサービス コレクションにサービス記述子を提供することによって追加されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="f782a-141">次の例では、`IDataAccess` インターフェイスとその具象実装 `DataAccess` での概念を示します。</span><span class="sxs-lookup"><span data-stu-id="f782a-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="f782a-142">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="f782a-142">Service lifetime</span></span>

<span data-ttu-id="f782a-143">サービスは、次の表に示す有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="f782a-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="f782a-144">有効期間</span><span class="sxs-lookup"><span data-stu-id="f782a-144">Lifetime</span></span> | <span data-ttu-id="f782a-145">説明</span><span class="sxs-lookup"><span data-stu-id="f782a-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="f782a-146">現在、Blazor WebAssembly アプリには DI スコープの概念はありません。</span><span class="sxs-lookup"><span data-stu-id="f782a-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="f782a-147">`Scoped` 登録済みサービスは `Singleton` サービスのように動作します。</span><span class="sxs-lookup"><span data-stu-id="f782a-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="f782a-148">Blazor Server ホスティング モデルでは、HTTP 要求間で `Scoped` 有効期間がサポートされていますが、クライアントに読み込まれるコンポーネント間での SignalR 接続/回線メッセージ間ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f782a-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SignalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="f782a-149">アプリの Razor ページまたは MVC の部分では、スコープ付きサービスが通常どおりに処理され、ページまたはビュー間を移動するとき、またはページやビューからコンポーネントに移動するときに、"*各 HTTP 要求*" に対してサービスが再作成されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="f782a-150">クライアント上のコンポーネント間を移動するときは、スコープ付きサービスは再構築されません。この場合、サーバーとの通信は、HTTP 要求ではなく、ユーザーの回線の SignalR 接続を介して行われます。</span><span class="sxs-lookup"><span data-stu-id="f782a-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="f782a-151">次のクライアント上のコンポーネント シナリオでは、ユーザー用に新しい回線が作成されるため、スコープ付きサービスは再構築されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="f782a-152">ユーザーがブラウザーのウィンドウを閉じる場合。</span><span class="sxs-lookup"><span data-stu-id="f782a-152">The user closes the browser's window.</span></span> <span data-ttu-id="f782a-153">ユーザーは新しいウィンドウを開き、アプリに戻ります。</span><span class="sxs-lookup"><span data-stu-id="f782a-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="f782a-154">ユーザーが、ブラウザー ウィンドウでアプリの最後のタブを閉じる場合。</span><span class="sxs-lookup"><span data-stu-id="f782a-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="f782a-155">ユーザーは新しいタブを開き、アプリに戻ります。</span><span class="sxs-lookup"><span data-stu-id="f782a-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="f782a-156">ユーザーが、ブラウザーの再読み込みまたは更新ボタンを選択する場合。</span><span class="sxs-lookup"><span data-stu-id="f782a-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="f782a-157">Blazor Server アプリのスコープ付きサービス間でユーザー状態を保持する方法の詳細については、「<xref:blazor/hosting-models?pivots=server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="f782a-158">DI では、サービスの "*単一インスタンス*" が作成されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="f782a-159">`Singleton` サービスを必要とするすべてのコンポーネントは、同じサービスのインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f782a-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="f782a-160">コンポーネントは、サービス コンテナーから `Transient` サービスのインスタンスを取得するたびに、サービスの "*新しいインスタンス*" を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f782a-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="f782a-161">DI システムは、ASP.NET Core の DI システムが基になっています。</span><span class="sxs-lookup"><span data-stu-id="f782a-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="f782a-162">詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="f782a-163">コンポーネント内のサービスを要求する</span><span class="sxs-lookup"><span data-stu-id="f782a-163">Request a service in a component</span></span>

<span data-ttu-id="f782a-164">サービスがサービス コレクションに追加された後、[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、サービスをコンポーネントに挿入します。これには 2 つのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="f782a-164">After services are added to the service collection, inject the services into the components using the [`@inject`](xref:mvc/views/razor#inject) Razor directive, which has two parameters:</span></span>

* <span data-ttu-id="f782a-165">型:挿入するサービスの型。</span><span class="sxs-lookup"><span data-stu-id="f782a-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="f782a-166">プロパティ:挿入されたアプリ サービスを受け取るプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="f782a-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="f782a-167">プロパティを手動で作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f782a-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="f782a-168">プロパティはコンパイラによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-168">The compiler creates the property.</span></span>

<span data-ttu-id="f782a-169">詳細については、「<xref:mvc/views/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="f782a-170">異なるサービスを挿入するには、複数の [`@inject`](xref:mvc/views/razor#inject) ステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="f782a-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="f782a-171">次の例は、[`@inject`](xref:mvc/views/razor#inject) を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f782a-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="f782a-172">`Services.IDataAccess` を実装するサービスを、コンポーネントのプロパティ `DataRepository` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="f782a-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="f782a-173">コードによって `IDataAccess` 抽象化だけが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="f782a-174">内部的には、生成されたプロパティ (`DataRepository`) によって、[`[Inject]` 属性](xref:Microsoft.AspNetCore.Components.InjectAttribute)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span></span> <span data-ttu-id="f782a-175">通常、この属性を直接使用することはありません。</span><span class="sxs-lookup"><span data-stu-id="f782a-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="f782a-176">コンポーネントで基底クラスが必要であり、基底クラスで挿入されたプロパティも必要な場合は、[`[Inject]` 属性](xref:Microsoft.AspNetCore.Components.InjectAttribute)を手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="f782a-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="f782a-177">基底クラスから派生されたコンポーネントでは、[`@inject`](xref:mvc/views/razor#inject) ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="f782a-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="f782a-178">基底クラスの <xref:Microsoft.AspNetCore.Components.InjectAttribute> で十分です。</span><span class="sxs-lookup"><span data-stu-id="f782a-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="f782a-179">サービスで DI を使用する</span><span class="sxs-lookup"><span data-stu-id="f782a-179">Use DI in services</span></span>

<span data-ttu-id="f782a-180">複雑なサービスでは、追加のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="f782a-180">Complex services might require additional services.</span></span> <span data-ttu-id="f782a-181">次の例では、`DataAccess` に <xref:System.Net.Http.HttpClient> の既定のサービスが必要です。</span><span class="sxs-lookup"><span data-stu-id="f782a-181">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="f782a-182">[`@inject`](xref:mvc/views/razor#inject) (または [`[Inject]` 属性](xref:Microsoft.AspNetCore.Components.InjectAttribute)) は、サービスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="f782a-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute)) isn't available for use in services.</span></span> <span data-ttu-id="f782a-183">代わりに、"*コンストラクター挿入*" を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f782a-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="f782a-184">サービスのコンストラクターにパラメーターを追加することによって、必要なサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="f782a-185">DI では、サービスを作成するときに、コンストラクターで必要なサービスが認識され、それに応じてサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="f782a-186">次の例では、コンストラクターは DI で <xref:System.Net.Http.HttpClient> を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f782a-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="f782a-187"><xref:System.Net.Http.HttpClient> は既定のサービスです。</span><span class="sxs-lookup"><span data-stu-id="f782a-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="f782a-188">コンストラクター挿入の前提条件:</span><span class="sxs-lookup"><span data-stu-id="f782a-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="f782a-189">DI によってすべての引数を満たすことができるコンストラクターが 1 つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f782a-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="f782a-190">DI で満たすことができない追加のパラメーターは、既定値が指定されている場合に許可されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="f782a-191">該当するコンストラクターは、`public` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="f782a-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="f782a-192">該当するコンストラクターが 1 つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f782a-192">One applicable constructor must exist.</span></span> <span data-ttu-id="f782a-193">あいまいさがある場合は、DI で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f782a-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="f782a-194">DI スコープを管理するためのユーティリティの基本コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="f782a-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="f782a-195">ASP.NET Core アプリでは、スコープ サービスは通常、現在の要求にスコープされます。</span><span class="sxs-lookup"><span data-stu-id="f782a-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="f782a-196">要求が完了すると、スコープ サービスまたは一時サービスは DI システムによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="f782a-197">Blazor Server アプリでは、要求スコープはクライアント接続の期間を通して保持されるため、一時サービスとスコープ サービスが予想よりはるかに長く存続する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f782a-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="f782a-198">Blazor WebAssembly アプリでは、スコープ付きの有効期間で登録されたサービスはシングルトンとして扱われるため、通常の ASP.NET Core アプリのスコープ サービスより長く存続します。</span><span class="sxs-lookup"><span data-stu-id="f782a-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="f782a-199">アプリ内の破棄可能な一時サービスを検出するには、「[破棄可能な一時サービスの検出](#detect-transient-disposables)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="f782a-200">Blazor アプリでサービスの有効期間を制限するには、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="f782a-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="f782a-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> は <xref:Microsoft.AspNetCore.Components.ComponentBase> から派生された抽象型であり、コンポーネントの有効期間に対応する DI スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="f782a-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="f782a-202">このスコープを使用すると、スコープ付きの有効期間で DI サービスを使用し、コンポーネントと同じ期間だけ持続させることができます。</span><span class="sxs-lookup"><span data-stu-id="f782a-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="f782a-203">コンポーネントが破棄されると、コンポーネントのスコープ サービス プロバイダーからのサービスも破棄されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="f782a-204">これは、次のようなサービスに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="f782a-204">This can be useful for services that:</span></span>

* <span data-ttu-id="f782a-205">一時的な有効期間が不適切であるため、コンポーネント内で再利用する必要がある。</span><span class="sxs-lookup"><span data-stu-id="f782a-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="f782a-206">シングルトンの有効期間が不適切であるため、コンポーネント間で共有してはならない。</span><span class="sxs-lookup"><span data-stu-id="f782a-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="f782a-207"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 型には、使用できるバージョンが 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="f782a-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="f782a-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> は、<xref:Microsoft.AspNetCore.Components.ComponentBase> 型の抽象的で破棄可能な子であり、<xref:System.IServiceProvider>型の保護された <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="f782a-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f782a-209">このプロバイダーを使用すると、コンポーネントの有効期間にスコープが設定されているサービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="f782a-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="f782a-210">[`@inject`](xref:mvc/views/razor#inject) または [`[Inject]` 属性](xref:Microsoft.AspNetCore.Components.InjectAttribute)を使用してコンポーネントに挿入された DI サービスは、コンポーネントのスコープでは作成されません。</span><span class="sxs-lookup"><span data-stu-id="f782a-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute) aren't created in the component's scope.</span></span> <span data-ttu-id="f782a-211">コンポーネントのスコープを使用するには、<xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> または <xref:System.IServiceProvider.GetService%2A> を使用してサービスを解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f782a-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="f782a-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロバイダーを使用して解決されたすべてのサービスには、同じスコープから提供される依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="f782a-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <span data-ttu-id="f782a-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> から派生する <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> では、スコープ DI プロバイダーから `T` のインスタンスを返すプロパティ <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> が追加されます。</span><span class="sxs-lookup"><span data-stu-id="f782a-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="f782a-214">この型は、アプリで 1 つのプライマリ サービスをコンポーネントのスコープを使用して DI コンテナーに要求するときに、<xref:System.IServiceProvider> のインスタンスを使用せずにスコープ サービスにアクセスするための便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="f782a-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="f782a-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> プロパティを使用できるので、必要に応じて、アプリで他の型のサービスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="f782a-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="f782a-216">DI からの Entity Framework Core (EF Core) DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="f782a-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="f782a-217">詳細については、「<xref:blazor/blazor-server-ef-core>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="f782a-218">破棄可能な一時サービスの検出</span><span class="sxs-lookup"><span data-stu-id="f782a-218">Detect transient disposables</span></span>

<span data-ttu-id="f782a-219">次の例は、<xref:Microsoft.AspNetCore.Components.OwningComponentBase> を使用する必要があるアプリ内の破棄可能な一時サービスを検出する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f782a-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="f782a-220">詳細については、「[DI スコープを管理するためのユーティリティの基本コンポーネント クラス](#utility-base-component-classes-to-manage-a-di-scope)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="f782a-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="f782a-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="f782a-222">次の例では、`TransientDisposable` が検出されます (`Program.cs`)。</span><span class="sxs-lookup"><span data-stu-id="f782a-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end 

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="f782a-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="f782a-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="f782a-224">`Program.cs` に <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="f782a-224">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="f782a-225">`Program.cs` の `Program.CreateHostBuilder` で:</span><span class="sxs-lookup"><span data-stu-id="f782a-225">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

<span data-ttu-id="f782a-226">次の例では、`TransientDependency` が検出されます (`Startup.cs`)。</span><span class="sxs-lookup"><span data-stu-id="f782a-226">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

<span data-ttu-id="f782a-227">アプリは、例外をスローすることなく、一時的な破棄可能を登録できます。</span><span class="sxs-lookup"><span data-stu-id="f782a-227">The app can register transient disposables without throwing an exception.</span></span> <span data-ttu-id="f782a-228">ただし、次の例に示すように、一時的に破棄可能な結果を解決しようとすると、<xref:System.InvalidOperationException> が発生します。</span><span class="sxs-lookup"><span data-stu-id="f782a-228">However, attempting to resolve a transient disposable results in an <xref:System.InvalidOperationException>, as the following example shows.</span></span>

<span data-ttu-id="f782a-229">`Pages/TransientDisposable.razor`:</span><span class="sxs-lookup"><span data-stu-id="f782a-229">`Pages/TransientDisposable.razor`:</span></span>

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

<span data-ttu-id="f782a-230">`/transient-disposable` で `TransientDisposable` コンポーネントに移動すると、フレームワークが `TransientDisposable` のインスタンスを構築しようとしたときに <xref:System.InvalidOperationException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f782a-230">Navigate to the `TransientDisposable` component at `/transient-disposable` and an <xref:System.InvalidOperationException> is thrown when the framework attempts to construct an instance of `TransientDisposable`:</span></span>

> <span data-ttu-id="f782a-231">System.InvalidOperationException:一時的に破棄可能なサービス TransientDisposable を間違ったスコープで解決しようとしています。</span><span class="sxs-lookup"><span data-stu-id="f782a-231">System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope.</span></span> <span data-ttu-id="f782a-232">解決しようとしているサービス 'T' に対して 'OwningComponentBase\<T>' コンポーネントの基底クラスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="f782a-232">Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f782a-233">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f782a-233">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="f782a-234">一時的なインスタンスと共有インスタンスのための `IDisposable` ガイダンス</span><span class="sxs-lookup"><span data-stu-id="f782a-234">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>

---
title: ASP.NET Core Blazor WebAssembly のセキュリティに関するその他のシナリオ
author: guardrex
description: セキュリティに関するその他のシナリオ用に Blazor WebAssembly を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: fb5b6f75959d9933e228b0288e70498ef05efc4a
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710634"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="e8ae2-103">ASP.NET Core Blazor WebAssembly のセキュリティに関するその他のシナリオ</span><span class="sxs-lookup"><span data-stu-id="e8ae2-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="e8ae2-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e8ae2-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="e8ae2-105">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="e8ae2-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> は、送信 <xref:System.Net.Http.HttpResponseMessage> インスタンスにアクセス トークンをアタッチするために使用される <xref:System.Net.Http.DelegatingHandler> です。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="e8ae2-107">トークンは、フレームワークによって登録されている <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> サービスを使用して取得されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="e8ae2-108">トークンを取得できない場合は、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="e8ae2-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> には、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> メソッドがあります。このメソッドを使用すると、ユーザーを ID プロバイダーに移動して、新しいトークンを取得できます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="e8ae2-110">便宜上、フレームワークには、アプリのベース アドレスを承認された URL として使用して事前構成が行われた <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="e8ae2-111">**アクセス トークンは、要求 URI がアプリのベース URI 内にある場合にのみ追加されます。**</span><span class="sxs-lookup"><span data-stu-id="e8ae2-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="e8ae2-112">送信要求 URI がアプリのベース URI 内にない場合は、[カスタム `AuthorizationMessageHandler` クラス (*推奨*)](#custom-authorizationmessagehandler-class) を使用するか、または [`AuthorizationMessageHandler`](#configure-authorizationmessagehandler) を構成します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="e8ae2-113">サーバー API アクセス用のクライアント アプリ構成に加えて、クライアントとサーバーが同じベース アドレス内に存在しない場合にクロスオリジン要求 (CORS) がサーバー API によって許可される必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="e8ae2-114">サーバー側の CORS 構成の詳細については、この記事で後述する「[クロスオリジン リソース共有 (CORS)](#cross-origin-resource-sharing-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="e8ae2-115">次の例では</span><span class="sxs-lookup"><span data-stu-id="e8ae2-115">In the following example:</span></span>

* <span data-ttu-id="e8ae2-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> によって、<xref:System.Net.Http.IHttpClientFactory> および関連サービスがサービス コレクションに追加され、名前付き <xref:System.Net.Http.HttpClient> (`ServerAPI`) が構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="e8ae2-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> は、要求を送信するときのリソース URI のベース アドレスです。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="e8ae2-118"><xref:System.Net.Http.IHttpClientFactory> は、[`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="e8ae2-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> は、送信 <xref:System.Net.Http.HttpResponseMessage> インスタンスにアクセス トークンをアタッチするために使用される <xref:System.Net.Http.DelegatingHandler> です。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="e8ae2-120">アクセス トークンは、要求 URI がアプリのベース URI 内にある場合にのみ追加されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="e8ae2-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> では、名前付き <xref:System.Net.Http.HttpClient> (`ServerAPI`) に対応する構成を使用して、送信要求用に <xref:System.Net.Http.HttpClient> インスタンスが作成および構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="e8ae2-122">Blazor WebAssembly のホストされたプロジェクト テンプレートに基づく Blazor アプリの場合、要求 URI は既定ではアプリのベース URI 内にあります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="e8ae2-123">したがって、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) は、プロジェクト テンプレートから生成されたアプリ内の <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="e8ae2-124">構成された <xref:System.Net.Http.HttpClient> を使用し、[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) パターンを使用して、承認された要求を行います。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Http.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="e8ae2-125">カスタム `AuthorizationMessageHandler` クラス</span><span class="sxs-lookup"><span data-stu-id="e8ae2-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="e8ae2-126">*このセクションのこのガイダンスは、アプリのベース URI 内に存在しない URI に対して送信要求を行うクライアント アプリに推奨されます。*</span><span class="sxs-lookup"><span data-stu-id="e8ae2-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="e8ae2-127">次の例では、カスタム クラスによって、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> が、<xref:System.Net.Http.HttpClient> 用の <xref:System.Net.Http.DelegatingHandler> として使用するために拡張されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="e8ae2-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> によって、アクセス トークンを使用して送信 HTTP 要求を承認できるように、このハンドラーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="e8ae2-129">アクセス トークンがアタッチされるのは、承認された URL の少なくとも 1 つが要求 URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>) のベースである場合に限られます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="e8ae2-130">`Program.Main` (`Program.cs`) では、`CustomAuthorizationMessageHandler` がスコープされたサービスとして登録され、名前付き <xref:System.Net.Http.HttpClient> によって作成された送信 <xref:System.Net.Http.HttpResponseMessage> インスタンスの <xref:System.Net.Http.DelegatingHandler> として構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="e8ae2-131">Blazor WebAssembly のホストされたプロジェクト テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) は既定では <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="e8ae2-132">構成が行われた <xref:System.Net.Http.HttpClient> を使用し、[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) パターンを使用して、承認された要求を行います。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="e8ae2-133"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) パッケージ) を使用してクライアントが作成される場合にサーバー API への要求を行うと、アクセス トークンが含まれるインスタンスが <xref:System.Net.Http.HttpClient> に提供されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="e8ae2-134">要求 URI が次の例 (`ExampleAPIMethod`) のように相対 URI である場合、それは、クライアント アプリから要求があったときに <xref:System.Net.Http.HttpClient.BaseAddress> に結合されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="e8ae2-135">`AuthorizationMessageHandler` を構成する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="e8ae2-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> メソッドを使用すれば、承認された URL、スコープ、および戻り先 URL で <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> を構成できます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="e8ae2-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> によって、アクセス トークンを使用して送信 HTTP 要求を承認できるように、ハンドラーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="e8ae2-138">アクセス トークンがアタッチされるのは、承認された URL の少なくとも 1 つが要求 URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>) のベースである場合に限られます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="e8ae2-139">要求 URI が相対 URI である場合、これは <xref:System.Net.Http.HttpClient.BaseAddress> に結合されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="e8ae2-140">次の例では、`Program.Main` (`Program.cs`) で、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> によって <xref:System.Net.Http.HttpClient> が構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="e8ae2-141">Blazor WebAssembly のホストされたプロジェクト テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> は既定では以下に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="e8ae2-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="e8ae2-143">`authorizedUrls` 配列の URL。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="e8ae2-144">型指定された `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="e8ae2-144">Typed `HttpClient`</span></span>

<span data-ttu-id="e8ae2-145">単一クラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="e8ae2-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="e8ae2-146">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient http;
 
    public WeatherForecastClient(HttpClient http)
    {
        this.http = http;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="e8ae2-147">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `using static BlazorSample.Data;`)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="e8ae2-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="e8ae2-149">Blazor WebAssembly のホストされたプロジェクト テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) は既定では <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="e8ae2-150">`FetchData` コンポーネント (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="e8ae2-151">`HttpClient` ハンドラーを構成する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="e8ae2-152">ハンドラーは、送信 HTTP 要求の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> を使用して、さらに構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="e8ae2-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="e8ae2-154">Blazor WebAssembly のホストされたプロジェクト テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> は既定では以下に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="e8ae2-155"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="e8ae2-156">`authorizedUrls` 配列の URL。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="e8ae2-157">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="e8ae2-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="e8ae2-158">通常、Blazor WebAssembly アプリがセキュリティで保護された既定の <xref:System.Net.Http.HttpClient> を使用する場合、アプリでは、名前付きの <xref:System.Net.Http.HttpClient> の構成を行うことで、認証または承認されていない Web API 要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="e8ae2-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="e8ae2-160">Blazor WebAssembly のホストされたプロジェクト テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) は既定では <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="e8ae2-161">前述の登録は、セキュリティで保護された既定の <xref:System.Net.Http.HttpClient> 登録に追加されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="e8ae2-162">コンポーネントでは、<xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) パッケージ) から <xref:System.Net.Http.HttpClient> が作成され、認証または承認されていない要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="e8ae2-163">サーバー API のコントローラー (前の例では `WeatherForecastNoAuthenticationController`) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性を使用してマークされることはありません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="e8ae2-164">既定の <xref:System.Net.Http.HttpClient> インスタンスとしてセキュリティで保護されたクライアントを使用するか、セキュリティで保護されていないクライアントを使用するかは、開発者が決定します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="e8ae2-165">この決定を行う方法の 1 つは、アプリが通信する認証済みのエンドポイントと認証されていないエンドポイントの数を考慮することです。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="e8ae2-166">アプリの要求の大部分が API エンドポイントをセキュリティで保護する場合は、認証された <xref:System.Net.Http.HttpClient> インスタンスを既定として使用します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="e8ae2-167">それ以外の場合は、認証されていない <xref:System.Net.Http.HttpClient> インスタンスを既定として登録します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="e8ae2-168"><xref:System.Net.Http.IHttpClientFactory> を使用する別の方法として、匿名エンドポイントへの認証されていないアクセス用に、[型指定されたクライアント](#typed-httpclient)を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="e8ae2-169">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-169">Request additional access tokens</span></span>

<span data-ttu-id="e8ae2-170">アクセス トークンは、`IAccessTokenProvider.RequestAccessToken` を呼び出して手動で取得できます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="e8ae2-171">次の例では、既定の <xref:System.Net.Http.HttpClient> に対して、アプリが追加のスコープを必要としています。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-171">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="e8ae2-172">Microsoft Authentication Library (MSAL) の例では、`MsalProviderOptions` を使用してスコープが構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-172">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="e8ae2-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="e8ae2-174">前の例の `{CUSTOM SCOPE 1}` と `{CUSTOM SCOPE 2}` のプレースホルダーは、カスタム スコープです。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-174">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="e8ae2-175">`IAccessTokenProvider.RequestToken` メソッドには、指定されたスコープ セットを使用して、アプリでアクセス トークンをプロビジョニングできるようにするオーバーロードが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-175">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="e8ae2-176">Razor コンポーネントでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-176">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="e8ae2-177">前の例の `{CUSTOM SCOPE 1}` と `{CUSTOM SCOPE 2}` のプレースホルダーは、カスタム スコープです。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-177">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="e8ae2-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> が次のように返します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="e8ae2-179">`token` を使用する場合は `true`。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-179">`true` with the `token` for use.</span></span>
* <span data-ttu-id="e8ae2-180">トークンが取得されない場合は `false`。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-180">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="e8ae2-181">クロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="e8ae2-181">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="e8ae2-182">CORS 要求で資格情報 (承認 cookie またはヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-182">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="e8ae2-183">次のポリシーには、以下についての構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-183">The following policy includes configuration for:</span></span>

* <span data-ttu-id="e8ae2-184">要求元 (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-184">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="e8ae2-185">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-185">Any method (verb).</span></span>
* <span data-ttu-id="e8ae2-186">`Content-Type` ヘッダーと `Authorization` ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-186">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="e8ae2-187">カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-187">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="e8ae2-188">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-188">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="e8ae2-189">Blazor のホストされたプロジェクト テンプレートに基づくホスト型 Blazor ソリューションでは、クライアントおよびサーバー アプリ用に同じベース アドレスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-189">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="e8ae2-190">クライアント アプリの <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> は、既定では `builder.HostEnvironment.BaseAddress` の URI に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-190">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="e8ae2-191">Blazor のホストされたプロジェクト テンプレートから作成されるホスト型アプリの既定の構成では、CORS 構成は **必須ではありません**。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-191">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="e8ae2-192">サーバー プロジェクトでホストされておらず、サーバー アプリのベース アドレスを共有していない追加のクライアント アプリでは、サーバー プロジェクト内の CORS 構成は **必須です**。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-192">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="e8ae2-193">詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (`Components/HTTPRequestTester.razor`) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-193">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="e8ae2-194">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-194">Handle token request errors</span></span>

<span data-ttu-id="e8ae2-195">シングル ページ アプリケーション (SPA) で OpenID Connect (OIDC) を使用してユーザーが認証されると、ユーザーが資格情報を入力したときに設定されるセッション cookie の形式で、SPA 内および Identity プロバイダー (IP) 内で、認証状態がローカルに維持されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-195">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="e8ae2-196">通常、IP によってユーザーに出力されるトークンが有効なのは短時間のため (通常は約 1 時間)、クライアント アプリでは定期的に新しいトークンをフェッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-196">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="e8ae2-197">それ以外の場合は、許可されたトークンの有効期限が切れると、ユーザーがログアウトします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-197">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="e8ae2-198">ほとんどの場合、OIDC クライアントでは、ユーザーに対して認証の再要求を行うことなく、新しいトークンをプロビジョニングできます。これは、認証状態または IP 内に保持される "セッション" によるものです。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-198">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="e8ae2-199">場合によっては、ユーザーの介入なしに、クライアントでトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-199">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="e8ae2-200">このシナリオは、ユーザーが `https://login.microsoftonline.com` にアクセスしてログアウトした場合に発生します。これらのシナリオでは、ユーザーがログアウトしたことを、アプリはすぐに認識しません。クライアントで保持されるトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-200">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="e8ae2-201">また、クライアントでは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-201">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="e8ae2-202">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-202">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="e8ae2-203">これらは、SPA の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-203">They are part of the nature of SPAs.</span></span> <span data-ttu-id="e8ae2-204">また、認証 cookie が削除されると、cookie を使用する SPA でサーバー API を呼び出すこともできません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-204">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="e8ae2-205">保護されたリソースに対する API 呼び出しをアプリで実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-205">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="e8ae2-206">API を呼び出すための新しいアクセス トークンをプロビジョニングするには、ユーザーの再認証が必要です。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-206">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="e8ae2-207">有効かもしれないトークンがクライアントにある場合でも、そのトークンはユーザーによって取り消されているため、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-207">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="e8ae2-208">アプリでトークンが要求されている場合は、次の 2 つの結果が考えられます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-208">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="e8ae2-209">要求が成功します。アプリには有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-209">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="e8ae2-210">要求が失敗します。新しいトークンを取得するために、ユーザーの再認証が必要となります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-210">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="e8ae2-211">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-211">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="e8ae2-212">次のようないくつかの方法がありますが、さらに複雑になります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-212">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="e8ae2-213">現在のページの状態をセッション ストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-213">Store the current page state in session storage.</span></span> <span data-ttu-id="e8ae2-214">[`OnInitializedAsync` ライフサイクル イベント](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 中に、続行する前に状態を復元できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-214">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="e8ae2-215">クエリ文字列パラメーターを追加して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-215">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="e8ae2-216">他の項目と競合するリスクなしにセッション ストレージにデータを格納するための一意識別子を持つクエリ文字列パラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-216">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="e8ae2-217">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-217">The following example shows how to:</span></span>

* <span data-ttu-id="e8ae2-218">ログイン ページにリダイレクトする前に、状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-218">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="e8ae2-219">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-219">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider
...

<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var http = new HttpClient();
        http.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            http.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await http.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="e8ae2-220">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-220">Save app state before an authentication operation</span></span>

<span data-ttu-id="e8ae2-221">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-221">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="e8ae2-222">状態コンテナーを使用していて、認証が成功した後に状態を復元する場合には、このようなことが起こる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-222">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="e8ae2-223">カスタム認証状態オブジェクトを使用して、アプリ固有の状態、またはその参照を保持し、認証操作が正常に完了した後で、その状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-223">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="e8ae2-224">このアプローチの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-224">The following example demonstrates the approach.</span></span>

<span data-ttu-id="e8ae2-225">状態コンテナー クラスは、アプリの状態値を保持するプロパティを使用して、アプリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-225">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="e8ae2-226">次の例では、コンテナーを使用して、既定のプロジェクト テンプレートの `Counter` コンポーネント (`Pages/Counter.razor`) のカウンター値を維持します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-226">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="e8ae2-227">コンテナーをシリアル化および逆シリアル化するためのメソッドは、<xref:System.Text.Json> に基づいています。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-227">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="e8ae2-228">`Counter` コンポーネントでは、状態コンテナーを使用して、コンポーネントの外部に `currentCount` 値を維持します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-228">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="e8ae2-229"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> から `ApplicationAuthenticationState` を作成します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-229">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="e8ae2-230">ローカルに格納されている状態の識別子として機能する `Id` プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-230">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="e8ae2-231">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="e8ae2-231">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="e8ae2-232">`Authentication` コンポーネント (`Pages/Authentication.razor`) では、`StateContainer` のシリアル化と逆シリアル化の方法である `GetStateForLocalStorage` および `SetStateFromLocalStorage` で、ローカル セッション ストレージを使用してアプリの状態を保存および復元します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-232">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="e8ae2-233">この例では、Azure Active Directory (AAD) を使用して認証を行います。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-233">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="e8ae2-234">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-234">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="e8ae2-235">`ApplicationAuthenticationState` は、Microsoft Authentication Library (MSAL) の `RemoteAuthenticationState` 型として、構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-235">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="e8ae2-236">状態コンテナーがサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-236">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="e8ae2-237">アプリ ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="e8ae2-237">Customize app routes</span></span>

<span data-ttu-id="e8ae2-238">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ライブラリでは既定で、次の表に示すルートを使用して、さまざまな認証状態が表されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-238">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="e8ae2-239">ルート</span><span class="sxs-lookup"><span data-stu-id="e8ae2-239">Route</span></span>                            | <span data-ttu-id="e8ae2-240">目的</span><span class="sxs-lookup"><span data-stu-id="e8ae2-240">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="e8ae2-241">サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-241">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="e8ae2-242">サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-242">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="e8ae2-243">何らかの理由でサインイン操作が失敗した場合に、エラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-243">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="e8ae2-244">サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-244">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="e8ae2-245">サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-245">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="e8ae2-246">何らかの理由でサインアウト操作が失敗した場合に、エラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-246">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="e8ae2-247">ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-247">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="e8ae2-248">ユーザー プロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-248">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="e8ae2-249">新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-249">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="e8ae2-250">上の表に示すルートは、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-250">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e8ae2-251">カスタム ルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-251">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="e8ae2-252">次の例では、すべてのパスが `/security` で始まります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-252">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="e8ae2-253">`Authentication` コンポーネント (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-253">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="e8ae2-254">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-254">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="e8ae2-255">完全に異なるパスを必要とする場合は、前述のようにルートを設定し、明示的なアクション パラメーターを使用して <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-255">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="e8ae2-256">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-256">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="e8ae2-257">認証ユーザー インターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="e8ae2-257">Customize the authentication user interface</span></span>

<span data-ttu-id="e8ae2-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> には、各認証状態の UI 部分の既定のセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="e8ae2-259">各状態は、カスタム <xref:Microsoft.AspNetCore.Components.RenderFragment> を渡すことでカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-259">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="e8ae2-260">最初のログイン プロセス中に表示されるテキストをカスタマイズするには、次のように <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> を変更します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-260">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="e8ae2-261">`Authentication` コンポーネント (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="e8ae2-261">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="e8ae2-262"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> には、次の表に示す認証ルートごとに使用できるフラグメントが 1 つあります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-262">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="e8ae2-263">ルート</span><span class="sxs-lookup"><span data-stu-id="e8ae2-263">Route</span></span>                            | <span data-ttu-id="e8ae2-264">Fragment</span><span class="sxs-lookup"><span data-stu-id="e8ae2-264">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="e8ae2-265">ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="e8ae2-265">Customize the user</span></span>

<span data-ttu-id="e8ae2-266">アプリにバインドされているユーザーをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-266">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="e8ae2-267">ペイロード要求を使用してユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="e8ae2-267">Customize the user with a payload claim</span></span>

<span data-ttu-id="e8ae2-268">次の例では、アプリの認証されたユーザーが、ユーザーの認証方法ごとに `amr` 要求を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-268">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="e8ae2-269">この `amr` 要求は、Microsoft Identity プラットフォーム v1.0 [ペイロード要求](/azure/active-directory/develop/access-tokens#the-amr-claim)でトークンのサブジェクトが認証された方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-269">The `amr` claim identifies how the subject of the token was authenticated in Microsoft Identity Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="e8ae2-270">この例では、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> に基づくカスタム ユーザー アカウント クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-270">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="e8ae2-271"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> クラスを拡張するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-271">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="e8ae2-272">次の例では、`AuthenticationMethod` プロパティを `amr` JSON プロパティ値のユーザー配列に設定します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-272">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="e8ae2-273">ユーザーが認証されると、フレームワークによって `AuthenticationMethod` が自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-273">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="e8ae2-274"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> を拡張するファクトリを作成して、`CustomUserAccount.AuthenticationMethod` に格納されているユーザーの認証方法から要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-274">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="e8ae2-275">使用中の認証プロバイダーの `CustomAccountFactory` を登録します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-275">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="e8ae2-276">次の登録のいずれかが有効です。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-276">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="e8ae2-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="e8ae2-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="e8ae2-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="e8ae2-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="e8ae2-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="e8ae2-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="e8ae2-280">カスタム ユーザー アカウント クラスを持つ AAD セキュリティ グループおよびロール</span><span class="sxs-lookup"><span data-stu-id="e8ae2-280">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="e8ae2-281">AAD セキュリティ グループと AAD 管理者ロール、およびカスタム ユーザー アカウント クラスを使用するその他の例については、<xref:blazor/security/webassembly/aad-groups-roles> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-281">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="e8ae2-282">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="e8ae2-282">Support prerendering with authentication</span></span>

<span data-ttu-id="e8ae2-283">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-283">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="e8ae2-284">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-284">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="e8ae2-285">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-285">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="e8ae2-286">*`Client`* アプリの `Program` クラス (`Program.cs`) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-286">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add...;

        builder.Services.AddScoped( ... );

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="e8ae2-287">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-287">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="e8ae2-288">サーバー アプリの `Startup.Configure` メソッドで、[`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) を [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-288">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="e8ae2-289">サーバー アプリで、`Pages` フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-289">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="e8ae2-290">サーバー アプリの `Pages` フォルダー内に `_Host.cshtml` ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-290">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="e8ae2-291">*`Client`* アプリの `wwwroot/index.html` ファイルの内容を `Pages/_Host.cshtml` ファイルに貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-291">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="e8ae2-292">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-292">Update the file's contents:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e8ae2-293">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-293">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="e8ae2-294">`<div id="app">Loading...</div>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-294">Replace the `<div id="app">Loading...</div>` tag with the following:</span></span>

  ```cshtml
  <div id="app">
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof({CLIENT APP ASSEMBLY NAME}.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </div>
  ```
  
  <span data-ttu-id="e8ae2-295">前の例では、プレースホルダー `{CLIENT APP ASSEMBLY NAME}` はクライアント アプリのアセンブリ名です (たとえば `BlazorSample.Client`)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-295">In the preceding example, the placeholder `{CLIENT APP ASSEMBLY NAME}` is the client app's assembly name (for example `BlazorSample.Client`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e8ae2-296">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-296">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="e8ae2-297">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-297">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof({CLIENT APP ASSEMBLY NAME}.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
  <span data-ttu-id="e8ae2-298">前の例では、プレースホルダー `{CLIENT APP ASSEMBLY NAME}` はクライアント アプリのアセンブリ名です (たとえば `BlazorSample.Client`)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-298">In the preceding example, the placeholder `{CLIENT APP ASSEMBLY NAME}` is the client app's assembly name (for example `BlazorSample.Client`).</span></span>

::: moniker-end
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="e8ae2-299">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="e8ae2-299">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="e8ae2-300">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-300">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="e8ae2-301">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-301">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="e8ae2-302">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-302">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="e8ae2-303">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="e8ae2-303">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="e8ae2-304">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-304">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="e8ae2-305">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="e8ae2-305">In this scenario:</span></span>

* <span data-ttu-id="e8ae2-306">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-306">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="e8ae2-307">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-307">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="e8ae2-308">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-308">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="e8ae2-309">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="e8ae2-309">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="e8ae2-310">サードパーティのログイン プロバイダーを使用して、Identity の構成を行います。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-310">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="e8ae2-311">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-311">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="e8ae2-312">ユーザーがログインすると、認証プロセスの一環として、アクセス トークンと更新トークンが IdentityID によって収集されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-312">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="e8ae2-313">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-313">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="e8ae2-314">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-314">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="e8ae2-315">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-315">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="e8ae2-316">そこから、サードパーティのアクセス トークンを使用して、クライアント上の IdentityID からサードパーティ API リソースを直接呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-316">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="e8ae2-317">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-317">We don't recommend this approach.</span></span> <span data-ttu-id="e8ae2-318">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-318">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="e8ae2-319">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-319">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="e8ae2-320">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-320">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="e8ae2-321">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-321">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="e8ae2-322">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-322">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="e8ae2-323">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="e8ae2-323">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="e8ae2-324">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-324">Make an API call from the client to the server API.</span></span> <span data-ttu-id="e8ae2-325">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-325">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="e8ae2-326">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-326">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="e8ae2-327">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-327">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="e8ae2-328">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-328">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="e8ae2-329">OpenID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-329">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="e8ae2-330">認証ライブラリと Blazor プロジェクト テンプレートでは、Open ID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-330">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="e8ae2-331">v2.0 エンドポイントを使用するには、JWT ベアラー <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> オプションの構成を行います。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-331">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="e8ae2-332">次の例では、<xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> プロパティに `v2.0` セグメントを追加することで、v2.0 に対して AAD の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-332">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="e8ae2-333">または、アプリ設定ファイル (`appsettings.json`) で設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-333">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="e8ae2-334">証明機関へのセグメントを追跡することがアプリの OIDC プロバイダー (AAD 以外のプロバイダーなど) にとって適切でない場合は、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> プロパティを直接設定します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-334">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="e8ae2-335"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> またはアプリ設定ファイル (`appsettings.json`) で `Authority` キーを使用してプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-335">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="e8ae2-336">ID トークンの要求のリストは、v2.0 エンドポイントで変更されています。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-336">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="e8ae2-337">詳細については、「[Microsoft ID プラットフォーム (v2.0) に更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-337">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="e8ae2-338">コンポーネントで gRPC を構成し、使用する</span><span class="sxs-lookup"><span data-stu-id="e8ae2-338">Configure and use gRPC in components</span></span>

<span data-ttu-id="e8ae2-339">[ASP.NET Core gRPC フレームワーク](xref:grpc/index)を使用するように Blazor WebAssembly アプリを構成するには:</span><span class="sxs-lookup"><span data-stu-id="e8ae2-339">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="e8ae2-340">サーバーで gRPC-Web を有効にします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-340">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="e8ae2-341">詳細については、「<xref:grpc/browser>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-341">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="e8ae2-342">アプリのメッセージ ハンドラーに gRPC サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-342">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="e8ae2-343">次の例では、gRPC チュートリアル[ (`Program.Main`) から ](xref:tutorials/grpc/grpc-start#create-a-grpc-service)`GreeterClient` サービスを使用するように、アプリの認可メッセージ ハンドラーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-343">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="e8ae2-344">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-344">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="e8ae2-345">ホストされている Blazor ソリューションの `Shared` プロジェクトに `.proto` ファイルを置きます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-345">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="e8ae2-346">クライアント アプリのコンポーネントでは、gRPC クライアント (`Pages/Grpc.razor`) を使用し、gRPC 呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-346">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="e8ae2-347">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-347">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="e8ae2-348">`Status.DebugException` プロパティを使用するには、[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) バージョン 2.30.0 以降を使用します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-348">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="e8ae2-349">詳細については、「<xref:grpc/browser>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-349">For more information, see <xref:grpc/browser>.</span></span>

## <a name="build-a-custom-version-of-the-authenticationmsal-javascript-library"></a><span data-ttu-id="e8ae2-350">Authentication.MSAL JavaScript ライブラリのカスタム バージョンをビルドする</span><span class="sxs-lookup"><span data-stu-id="e8ae2-350">Build a custom version of the Authentication.MSAL JavaScript library</span></span>

<span data-ttu-id="e8ae2-351">アプリでカスタム バージョンの [JavaScript 用 Microsoft Authentication Library (MSAL js)](https://www.npmjs.com/package/@azure/msal-browser) が必要な場合は、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-351">If an app requires a custom version of the [Microsoft Authentication Library for JavaScript (MSAL.js)](https://www.npmjs.com/package/@azure/msal-browser), perform the following steps:</span></span>

1. <span data-ttu-id="e8ae2-352">システムに最新の開発者用 .NET SDK がインストールされていることを確認するか、次から最新の開発者用 SDK を入手してインストールします: [.NET Core SDK:「インストーラーとバイナリ」](https://github.com/dotnet/installer#installers-and-binaries)。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-352">Confirm the the system has the latest developer .NET SDK or obtain and install the latest developer SDK from [.NET Core SDK: Installers and Binaries](https://github.com/dotnet/installer#installers-and-binaries).</span></span> <span data-ttu-id="e8ae2-353">このシナリオでは、内部 NuGet フィードの構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-353">Configuration of internal NuGet feeds isn't required for this scenario.</span></span>
1. <span data-ttu-id="e8ae2-354">「[ソースから ASP.NET Core をビルドする](https://github.com/dotnet/aspnetcore/blob/main/docs/BuildFromSource.md)」のドキュメントに従って、開発用の `dotnet/aspnetcore` GitHub リポジトリを設定します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-354">Set up the `dotnet/aspnetcore` GitHub repository for development per the docs at [Build ASP.NET Core from Source](https://github.com/dotnet/aspnetcore/blob/main/docs/BuildFromSource.md).</span></span> <span data-ttu-id="e8ae2-355">[dotnet/aspnetcore GitHub リポジトリ](https://github.com/dotnet/aspnetcore)をフォークしてクローンするか、ZIP アーカイブをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-355">Fork and clone or download a ZIP archive of the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore).</span></span>
1. <span data-ttu-id="e8ae2-356">`src/Components/WebAssembly/Authentication.Msal/src/Interop/package.json` ファイルを開き、必要なバージョンの `@azure/msal-browser` を設定します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-356">Open the the `src/Components/WebAssembly/Authentication.Msal/src/Interop/package.json` file and set the desired version of `@azure/msal-browser`.</span></span> <span data-ttu-id="e8ae2-357">リリース済みバージョンの一覧については、[`@azure/msal-browser` の npm Web サイト](https://www.npmjs.com/package/@azure/msal-browser)にアクセスし、 **[Versions]\(バージョン\)** タブを選択してください。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-357">For a list of released versions, visit the [`@azure/msal-browser` npm website](https://www.npmjs.com/package/@azure/msal-browser) and select the **Versions** tab.</span></span>
1. <span data-ttu-id="e8ae2-358">コマンド シェルで `yarn build` コマンドを使用して、`src/Components/WebAssembly/Authentication.Msal/src` フォルダー内に `Authentication.Msal` プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-358">Build the `Authentication.Msal` project in the `src/Components/WebAssembly/Authentication.Msal/src` folder with the `yarn build` command in a command shell.</span></span>
1. <span data-ttu-id="e8ae2-359">アプリで[圧縮された資産 (Brotli/Gzip)](xref:blazor/host-and-deploy/webassembly#compression) を使用する場合は、`Interop/dist/Release/AuthenticationService.js` ファイルを圧縮します。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-359">If the app uses [compressed assets (Brotli/Gzip)](xref:blazor/host-and-deploy/webassembly#compression), compress the `Interop/dist/Release/AuthenticationService.js` file.</span></span>
1. <span data-ttu-id="e8ae2-360">`AuthenticationService.js` ファイルと、生成した場合はファイルの圧縮バージョン (`.br`/`.gz`) を、`Interop/dist/Release` フォルダーから、アプリの発行済み資産にあるアプリの `publish/wwwroot/_content/Microsoft.Authentication.WebAssembly.Msal` フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e8ae2-360">Copy the `AuthenticationService.js` file and compressed versions (`.br`/`.gz`) of the file, if produced, from the `Interop/dist/Release` folder into the app's `publish/wwwroot/_content/Microsoft.Authentication.WebAssembly.Msal` folder in the app's published assets.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8ae2-361">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e8ae2-361">Additional resources</span></span>

* <xref:blazor/security/webassembly/graph-api>
* [<span data-ttu-id="e8ae2-362">`HttpClient` および `HttpRequestMessage` と Fetch API 要求オプション</span><span class="sxs-lookup"><span data-stu-id="e8ae2-362">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)

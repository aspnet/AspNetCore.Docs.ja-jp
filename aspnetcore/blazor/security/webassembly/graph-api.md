---
title: ASP.NET Core Blazor WebAssembly で Graph API を使用する
author: guardrex
description: Blazor WebAssemlby アプリで Graph API を使用する方法について説明します。
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 997d4dec05ddb6b9d0acb5ed36a6510c0836a4fb
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280497"
---
# <a name="use-graph-api-with-aspnet-core-blazor-webassembly"></a><span data-ttu-id="17e7a-103">ASP.NET Core Blazor WebAssembly で Graph API を使用する</span><span class="sxs-lookup"><span data-stu-id="17e7a-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="17e7a-104">[Microsoft Graph API](/graph/use-the-api) は、Blazor および他の .NET Framework アプリが Microsoft Cloud サービス リソースにアクセスできるようにする RESTful Web API です。</span><span class="sxs-lookup"><span data-stu-id="17e7a-104">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="17e7a-105">Graph SDK</span><span class="sxs-lookup"><span data-stu-id="17e7a-105">Graph SDK</span></span>

<span data-ttu-id="17e7a-106">[Microsoft Graph SDK](/graph/sdks/sdks-overview) は、Microsoft Graph にアクセスする高品質で効率的であり、回復性があるアプリケーションのビルドを簡素化するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="17e7a-106">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="17e7a-107">このセクションの例では、スタンドアロンのプロジェクト ファイルまたは *`Client`* アプリのプロジェクト ファイルに次のパッケージのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="17e7a-107">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="17e7a-108">次のユーティリティ クラスおよび構成は、この記事の次の各サブセクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-108">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="17e7a-109">Graph SDK を使用してコンポーネントから Graph API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="17e7a-109">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="17e7a-110">Graph SDK を使用してユーザー要求をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="17e7a-110">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="17e7a-111">Azure portal の AAD 領域で Microsoft Graph API スコープを追加した後:</span><span class="sxs-lookup"><span data-stu-id="17e7a-111">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="17e7a-112">ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリに次の `GraphClientExtensions.cs` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-112">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="17e7a-113">`AuthenticateRequestAsync` メソッド内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> プロパティに必要なスコープを提供します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-113">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="17e7a-114">次の例では、この記事の後述のセクションにある例と一致するように `User.Read` スコープを指定しています。</span><span class="sxs-lookup"><span data-stu-id="17e7a-114">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = new[] { "{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}" }
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

<span data-ttu-id="17e7a-115">前のコードのスコープのプレースホルダー `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` は、1 つ以上の許可されたスコープを表します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-115">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="17e7a-116">たとえば、`Scopes` を、この記事の後のセクションにある例の `User.Read` の 1 つのスコープの文字列配列に設定します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-116">For example, set `Scopes` to a string array of one scope for `User.Read` for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="17e7a-117">`Program.Main` (`Program.cs`) で、`AddGraphClient` 拡張メソッドを使用して Graph クライアント サービスと構成を追加します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-117">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

<span data-ttu-id="17e7a-118">前のコードのスコープのプレースホルダー `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` は、1 つ以上の許可されたスコープを表します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-118">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="17e7a-119">たとえば、`User.Read` を、この記事の次のセクションにある例の `AddGraphClient` スコープに渡します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-119">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="17e7a-120">Graph SDK を使用してコンポーネントから Graph API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="17e7a-120">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="17e7a-121">このセクションでは、この記事で既に説明した [ユーティリティ クラス (`GraphClientExtensions.cs`)](#graph-sdk) を使用します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-121">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="17e7a-122">次の `GraphExample` コンポーネントでは、挿入された `GraphServiceClient` を使用してユーザーの AAD プロファイル データを取得し、その携帯電話番号を表示します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-122">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="17e7a-123">Graph SDK を使用してユーザー要求をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="17e7a-123">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="17e7a-124">このセクションでは、この記事で既に説明した [ユーティリティ クラス (`GraphClientExtensions.cs`)](#graph-sdk) を使用します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-124">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="17e7a-125">次の例のアプリでは、AAD ユーザー プロファイルの携帯電話番号からユーザーの携帯電話番号要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-125">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="17e7a-126">このアプリには、AAD で構成された `User.Read` Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="17e7a-126">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="17e7a-127">次のカスタム ユーザー アカウント ファクトリでは、フレームワークの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> はユーザーのアカウントを表します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-127">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="17e7a-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで必要な場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-128">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="17e7a-129">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="17e7a-129">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="17e7a-130">`Program.Main` (`Program.cs`) で、カスタム ユーザー アカウント ファクトリを使用するように MSAL 認証を構成します。<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで使用されている場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-130">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a><span data-ttu-id="17e7a-131">名前付きクライアントと Graph API</span><span class="sxs-lookup"><span data-stu-id="17e7a-131">Named client with Graph API</span></span>

<span data-ttu-id="17e7a-132">このセクションの例では、Graph API の名前付き <xref:System.Net.Http.HttpClient> を使用して、通話を処理するユーザーの携帯電話番号を取得します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-132">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="17e7a-133">このセクションの例では、スタンドアロンのプロジェクト ファイルまたは *`Client`* アプリのプロジェクト ファイルに [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) のパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="17e7a-133">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="17e7a-134">Graph API を使用するには、次のクラスとプロジェクト構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-134">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="17e7a-135">次のクラスおよび構成は、この記事の次の各サブセクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-135">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="17e7a-136">コンポーネントから Graph API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="17e7a-136">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="17e7a-137">Graph API と名前付きクライアントを使用してユーザー要求をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="17e7a-137">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="17e7a-138">Azure portal の AAD 領域で Microsoft Graph API スコープを追加した後、アプリの構成済み Graph API 用ハンドラーに必要なスコープを提供します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-138">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="17e7a-139">次の例では、`User.Read` スコープのハンドラーを構成します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-139">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="17e7a-140">さらにスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-140">Additional scopes can be added.</span></span>

<span data-ttu-id="17e7a-141">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="17e7a-141">`GraphAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="17e7a-142">`Program.Main` (`Program.cs`) では、Graph API の名前付き <xref:System.Net.Http.HttpClient> を構成します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-142">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="17e7a-143">コンポーネントから Graph API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="17e7a-143">Call Graph API from a component</span></span>

<span data-ttu-id="17e7a-144">このセクションでは、この記事で既に説明した [Graph 認可メッセージ ハンドラー (`GraphAuthorizationMessageHandler.cs`) とアプリへの `Program.Main` の追加](#named-client-with-graph-api)を使用します。これにより、Graph API の名前付き <xref:System.Net.Http.HttpClient> が提供されます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-144">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="17e7a-145">Razor コンポーネントでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="17e7a-145">In a Razor component:</span></span>

* <span data-ttu-id="17e7a-146">Graph API の <xref:System.Net.Http.HttpClient> を作成し、ユーザーのプロファイル データの要求を発行します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-146">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="17e7a-147">`UserInfo.cs` クラスにより、必要なユーザー プロファイル プロパティを、<xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 属性と、AAD によってそれらのプロパティ用に使用される JSON 名を使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-147">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="17e7a-148">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="17e7a-148">`Pages/CallUser.razor`:</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                // Use userInfo.MobilePhone and callInfo.Message to make a call

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="17e7a-149">Graph API と名前付きクライアントを使用してユーザー要求をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="17e7a-149">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="17e7a-150">このセクションでは、この記事で既に説明した [Graph 認可メッセージ ハンドラー (`GraphAuthorizationMessageHandler.cs`) とアプリへの `Program.Main` の追加](#named-client-with-graph-api)を使用します。これにより、Graph API の名前付き <xref:System.Net.Http.HttpClient> が提供されます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-150">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="17e7a-151">次の例のアプリでは、AAD ユーザー プロファイルの携帯電話番号からユーザーの携帯電話番号要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-151">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="17e7a-152">このアプリには、AAD で構成された `User.Read` Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="17e7a-152">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="17e7a-153">`UserInfo.cs` クラスをアプリに追加し、必要なユーザー プロファイル プロパティを、<xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 属性と、AAD によってそれらのプロパティ用に使用される JSON 名を使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-153">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="17e7a-154">次のカスタム ユーザー アカウント ファクトリでは、フレームワークの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> はユーザーのアカウントを表します。</span><span class="sxs-lookup"><span data-stu-id="17e7a-154">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="17e7a-155"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで必要な場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-155">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="17e7a-156">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="17e7a-156">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="17e7a-157">`Program.Main` (`Program.cs`) で、カスタム ユーザー アカウント ファクトリを使用するように MSAL 認証を構成します。<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで使用されている場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="17e7a-157">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

<span data-ttu-id="17e7a-158">前の例は、MSAL で AAD 認証が使用されるアプリの場合です。</span><span class="sxs-lookup"><span data-stu-id="17e7a-158">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="17e7a-159">OIDC と API 認証にも、同様のパターンがあります。</span><span class="sxs-lookup"><span data-stu-id="17e7a-159">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="17e7a-160">詳細については、「[ペイロード要求を使用してユーザーをカスタマイズする](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim)」セクションの例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17e7a-160">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>

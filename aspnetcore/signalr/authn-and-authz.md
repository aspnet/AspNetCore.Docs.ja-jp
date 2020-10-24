---
title: ASP.NET Core での認証と承認 SignalR
author: bradygaster
description: ASP.NET Core で認証と承認を使用する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
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
uid: signalr/authn-and-authz
ms.openlocfilehash: e16efa59a82d0f3cb1a2272ae0c07654ebec6a51
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491562"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="ca236-103">ASP.NET Core での認証と承認 SignalR</span><span class="sxs-lookup"><span data-stu-id="ca236-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="ca236-104">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="ca236-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="ca236-105">[サンプル コードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) ([ダウンロード方法](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca236-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a><span data-ttu-id="ca236-106">ハブに接続しているユーザーを認証する SignalR</span><span class="sxs-lookup"><span data-stu-id="ca236-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="ca236-107">SignalR[ASP.NET Core 認証](xref:security/authentication/identity)と共に使用して、ユーザーを各接続に関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="ca236-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="ca236-108">ハブでは、 [HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) プロパティから認証データにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ca236-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="ca236-109">認証を使用すると、ユーザーに関連付けられているすべての接続で、ハブがメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ca236-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="ca236-110">詳細については、「 [」の SignalR 「ユーザーとグループの管理](xref:signalr/groups)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca236-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="ca236-111">複数の接続を1人のユーザーに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="ca236-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="ca236-112">`Startup.Configure` SignalR 認証を使用して ASP.NET Core 認証を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ca236-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="ca236-113">および ASP.NET Core 認証ミドルウェアを登録する順序は SignalR 重要です。</span><span class="sxs-lookup"><span data-stu-id="ca236-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="ca236-114">が `UseAuthentication` `UseSignalR` SignalR のユーザーを持つようにするには、常にを呼び出し `HttpContext` ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="no-loccookie-authentication"></a><span data-ttu-id="ca236-115">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="ca236-115">Cookie authentication</span></span>

<span data-ttu-id="ca236-116">ブラウザーベースのアプリでは、 cookie 認証によって既存のユーザーの資格情報を自動的に接続に渡すことができ SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="ca236-117">ブラウザークライアントを使用する場合、追加の構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ca236-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="ca236-118">ユーザーがアプリにログインしている場合、 SignalR 接続は自動的にこの認証を継承します。</span><span class="sxs-lookup"><span data-stu-id="ca236-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="ca236-119">Cookieは、アクセストークンを送信するためのブラウザー固有の方法ですが、ブラウザー以外のクライアントはそれらを送信できます。</span><span class="sxs-lookup"><span data-stu-id="ca236-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="ca236-120">[.Net クライアント](xref:signalr/dotnet-client)を使用する場合は、 `Cookies` `.WithUrl` を指定するための呼び出しでプロパティを構成でき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="ca236-121">ただし、 cookie .net クライアントから認証を使用するには、アプリケーションがの認証データを交換するための API を提供する必要があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="ca236-122">ベアラー トークン認証</span><span class="sxs-lookup"><span data-stu-id="ca236-122">Bearer token authentication</span></span>

<span data-ttu-id="ca236-123">クライアントは、を使用する代わりに、アクセストークンを提供でき cookie ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="ca236-124">サーバーはトークンを検証し、それを使用してユーザーを識別します。</span><span class="sxs-lookup"><span data-stu-id="ca236-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="ca236-125">この検証は、接続が確立された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="ca236-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="ca236-126">接続の有効期間中は、トークンの失効を確認するためにサーバーが自動的に再検証されることはありません。</span><span class="sxs-lookup"><span data-stu-id="ca236-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="ca236-127">JavaScript クライアントでは、 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) オプションを使用してトークンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ca236-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="ca236-128">.NET クライアントには、トークンの構成に使用できる同様の [れる場合](xref:signalr/configuration#configure-bearer-authentication) プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="ca236-128">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="ca236-129">提供するアクセストークン関数は、によって行われる **すべて** の HTTP 要求の前に呼び出され SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="ca236-130">接続をアクティブな状態に保つために、トークンを更新する必要がある場合 (接続中に期限切れになる可能性があるため)、この関数内からトークンを取得し、更新されたトークンを返します。</span><span class="sxs-lookup"><span data-stu-id="ca236-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="ca236-131">標準の web Api では、ベアラートークンは HTTP ヘッダーで送信されます。</span><span class="sxs-lookup"><span data-stu-id="ca236-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="ca236-132">ただし、で SignalR は、一部のトランスポートを使用するときに、これらのヘッダーをブラウザーで設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="ca236-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="ca236-133">Websocket と Server-Sent イベントを使用する場合、トークンはクエリ文字列パラメーターとして送信されます。</span><span class="sxs-lookup"><span data-stu-id="ca236-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> 

#### <a name="built-in-jwt-authentication"></a><span data-ttu-id="ca236-134">組み込み JWT 認証</span><span class="sxs-lookup"><span data-stu-id="ca236-134">Built-in JWT authentication</span></span>

<span data-ttu-id="ca236-135">サーバーでは、ベアラートークン認証は [JWT ベアラーミドルウェア](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="ca236-135">On the server, bearer token authentication is configured using the [JWT Bearer middleware](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="ca236-136">クエリ文字列は、ブラウザー API の制限により、Websocket と Server-Sent イベントに接続するときにブラウザーで使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca236-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="ca236-137">HTTPS を使用する場合、クエリ文字列の値は TLS 接続によって保護されます。</span><span class="sxs-lookup"><span data-stu-id="ca236-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="ca236-138">ただし、多くのサーバーはクエリ文字列の値をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="ca236-138">However, many servers log query string values.</span></span> <span data-ttu-id="ca236-139">詳細については、「 [ASP.NET Core SignalR のセキュリティに関する考慮事項](xref:signalr/security)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca236-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> <span data-ttu-id="ca236-140">SignalR はヘッダーを使用して、トークンをサポートする環境 (.NET や Java クライアントなど) でトークンを送信します。</span><span class="sxs-lookup"><span data-stu-id="ca236-140">SignalR uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

#### <a name="no-locidentity-server-jwt-authentication"></a><span data-ttu-id="ca236-141">Identity サーバー JWT 認証</span><span class="sxs-lookup"><span data-stu-id="ca236-141">Identity Server JWT authentication</span></span>

<span data-ttu-id="ca236-142">サーバーを使用する場合は Identity 、 <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> 次のようにサービスをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="ca236-142">When using Identity Server, add a <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> service to the project:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
public class ConfigureJwtBearerOptions : IPostConfigureOptions<JwtBearerOptions>
{
    public void PostConfigure(string name, JwtBearerOptions options)
    {
        var originalOnMessageReceived = options.Events.OnMessageReceived;
        options.Events.OnMessageReceived = async context =>
        {
            await originalOnMessageReceived(context);
                
            if (string.IsNullOrEmpty(context.Token))
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;
                
                if (!string.IsNullOrEmpty(accessToken) && 
                    path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }
            }
        };
    }
}
```

<span data-ttu-id="ca236-143">`Startup.ConfigureServices`認証用のサービス ( <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> ) とサーバーの認証ハンドラー () を追加した後で、にサービスを登録し Identity <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-143">Register the service in `Startup.ConfigureServices` after adding services for authentication (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) and the authentication handler for Identity Server (<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>):</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="no-loccookies-vs-bearer-tokens"></a><span data-ttu-id="ca236-144">Cookies およびベアラートークン</span><span class="sxs-lookup"><span data-stu-id="ca236-144">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="ca236-145">Cookieは、ブラウザーに固有のものです。</span><span class="sxs-lookup"><span data-stu-id="ca236-145">Cookies are specific to browsers.</span></span> <span data-ttu-id="ca236-146">他の種類のクライアントから送信すると、ベアラートークンの送信と比較して複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="ca236-146">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="ca236-147">その cookie ため、アプリがブラウザークライアントからのユーザーの認証のみを必要とする場合を除き、認証は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="ca236-147">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="ca236-148">ベアラートークン認証は、ブラウザークライアント以外のクライアントを使用する場合に推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="ca236-148">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="ca236-149">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="ca236-149">Windows authentication</span></span>

<span data-ttu-id="ca236-150">アプリで [Windows 認証](xref:security/authentication/windowsauth) が構成されている場合、は SignalR その id を使用してハブをセキュリティで保護することができます。</span><span class="sxs-lookup"><span data-stu-id="ca236-150">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="ca236-151">ただし、個々のユーザーにメッセージを送信するには、カスタムユーザー ID プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca236-151">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="ca236-152">Windows 認証システムは、"名前識別子" 要求を提供しません。</span><span class="sxs-lookup"><span data-stu-id="ca236-152">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> <span data-ttu-id="ca236-153">SignalR は、要求を使用してユーザー名を決定します。</span><span class="sxs-lookup"><span data-stu-id="ca236-153">SignalR uses the claim to determine the user name.</span></span>

<span data-ttu-id="ca236-154">を実装 `IUserIdProvider` し、識別子として使用する要求の1つをユーザーから取得する新しいクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="ca236-154">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="ca236-155">たとえば、"Name" 要求 (フォームの Windows ユーザー名) を使用するには、 `[Domain]\[Username]` 次のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca236-155">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="ca236-156">ではなく、 `ClaimTypes.Name` の任意の値 `User` (Windows SID 識別子など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ca236-156">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="ca236-157">選択する値は、システム内のすべてのユーザーの間で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca236-157">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="ca236-158">そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ca236-158">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="ca236-159">メソッドにこのコンポーネントを登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="ca236-159">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="ca236-160">.NET クライアントでは、 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) プロパティを設定することによって Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca236-160">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="ca236-161">Windows 認証は Internet Explorer と Microsoft Edge ではサポートされていますが、すべてのブラウザーではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ca236-161">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="ca236-162">たとえば、Chrome および Safari で Windows 認証と Websocket を使用しようとすると失敗します。</span><span class="sxs-lookup"><span data-stu-id="ca236-162">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="ca236-163">Windows 認証が失敗した場合、クライアントは、動作する可能性がある他のトランスポートへのフォールバックを試みます。</span><span class="sxs-lookup"><span data-stu-id="ca236-163">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="ca236-164">要求を使用して id 処理をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="ca236-164">Use claims to customize identity handling</span></span>

<span data-ttu-id="ca236-165">ユーザーを認証するアプリは、ユーザー要求からユーザー Id を派生させることができ SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-165">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="ca236-166">がユーザー Id を作成する方法を指定するには SignalR 、実装を実装し `IUserIdProvider` て登録します。</span><span class="sxs-lookup"><span data-stu-id="ca236-166">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="ca236-167">このサンプルコードでは、要求を使用して、識別プロパティとしてユーザーの電子メールアドレスを選択する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ca236-167">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="ca236-168">選択する値は、システム内のすべてのユーザーの間で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca236-168">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="ca236-169">そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ca236-169">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="ca236-170">アカウント登録によって、種類がの要求が `ClaimsTypes.Email` ASP.NET identity データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ca236-170">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="ca236-171">このコンポーネントをに登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="ca236-171">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="ca236-172">ハブおよびハブのメソッドへのアクセスをユーザーに承認する</span><span class="sxs-lookup"><span data-stu-id="ca236-172">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="ca236-173">既定では、ハブ内のすべてのメソッドを認証されていないユーザーが呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ca236-173">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="ca236-174">認証を要求するには、 [承認](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 属性をハブに適用します。</span><span class="sxs-lookup"><span data-stu-id="ca236-174">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="ca236-175">コンストラクターの引数と `[Authorize]` 属性のプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。</span><span class="sxs-lookup"><span data-stu-id="ca236-175">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="ca236-176">たとえば、というカスタム承認ポリシーがある場合は、 `MyAuthorizationPolicy` 次のコードを使用して、そのポリシーに一致するユーザーだけがハブにアクセスできるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="ca236-176">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="ca236-177">個々のハブメソッドに `[Authorize]` も属性を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ca236-177">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="ca236-178">現在のユーザーがメソッドに適用されているポリシーと一致しない場合は、呼び出し元にエラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="ca236-178">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="ca236-179">承認ハンドラーを使用してハブメソッドの承認をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="ca236-179">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="ca236-180">SignalR ハブメソッドが承認を必要とする場合に、カスタムリソースを承認ハンドラーに提供します。</span><span class="sxs-lookup"><span data-stu-id="ca236-180">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="ca236-181">リソースは `HubInvocationContext` のインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="ca236-181">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="ca236-182">には、、 `HubInvocationContext` `HubCallerContext` 呼び出されるハブメソッドの名前、およびハブメソッドへの引数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ca236-182">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="ca236-183">Azure Active Directory による複数の組織でのサインインを可能にするチャットルームの例を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ca236-183">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="ca236-184">Microsoft アカウントを持つユーザーはだれでもチャットにサインインできますが、所有している組織のメンバーだけがユーザーの許可を禁止したり、ユーザーのチャット履歴を表示したりできるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca236-184">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="ca236-185">さらに、特定のユーザーの特定の機能を制限することもできます。</span><span class="sxs-lookup"><span data-stu-id="ca236-185">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="ca236-186">ASP.NET Core 3.0 の更新された機能を使用すると、これは完全に可能です。</span><span class="sxs-lookup"><span data-stu-id="ca236-186">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="ca236-187">が `DomainRestrictedRequirement` カスタムとして機能することに注意して `IAuthorizationRequirement` ください。</span><span class="sxs-lookup"><span data-stu-id="ca236-187">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="ca236-188">`HubInvocationContext`リソースパラメーターが渡されたので、内部ロジックはハブが呼び出されているコンテキストを検査し、ユーザーが個々のハブメソッドを実行できるようにすることを決定します。</span><span class="sxs-lookup"><span data-stu-id="ca236-188">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="ca236-189">で、 `Startup.ConfigureServices` 新しいポリシーを追加し、ポリシーを `DomainRestrictedRequirement` 作成するためのパラメーターとしてカスタム要件を指定し `DomainRestricted` ます。</span><span class="sxs-lookup"><span data-stu-id="ca236-189">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="ca236-190">前の例では、クラスはとの両方で、その `DomainRestrictedRequirement` `IAuthorizationRequirement` `AuthorizationHandler` 要件に対応しています。</span><span class="sxs-lookup"><span data-stu-id="ca236-190">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="ca236-191">この2つのコンポーネントを別々のクラスに分割して、懸念事項を分けることができます。</span><span class="sxs-lookup"><span data-stu-id="ca236-191">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="ca236-192">この例のアプローチの利点は、 `AuthorizationHandler` 要件とハンドラーが同じであるため、起動時にを挿入する必要がないことです。</span><span class="sxs-lookup"><span data-stu-id="ca236-192">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ca236-193">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ca236-193">Additional resources</span></span>

* [<span data-ttu-id="ca236-194">ASP.NET Core でのベアラートークン認証</span><span class="sxs-lookup"><span data-stu-id="ca236-194">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="ca236-195">リソースベースの承認</span><span class="sxs-lookup"><span data-stu-id="ca236-195">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)

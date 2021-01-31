---
title: ASP.NET Core で証明書認証を構成する
author: blowdart
description: IIS および HTTP.sys の ASP.NET Core で証明書認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: c862bc8bff6c4cc80696d92067e814889d6e7782
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217532"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="597e2-103">ASP.NET Core で証明書認証を構成する</span><span class="sxs-lookup"><span data-stu-id="597e2-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="597e2-104">`Microsoft.AspNetCore.Authentication.Certificate` ASP.NET Core の [証明書認証](https://tools.ietf.org/html/rfc5246#section-7.4.4) に似た実装が含まれています。</span><span class="sxs-lookup"><span data-stu-id="597e2-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="597e2-105">証明書の認証は、ASP.NET Core に到達するずっと前に TLS レベルで実行されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="597e2-106">より正確には、これは証明書を検証し、その証明書をに解決できるイベントを提供する認証ハンドラーです `ClaimsPrincipal` 。</span><span class="sxs-lookup"><span data-stu-id="597e2-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="597e2-107">証明書認証用に[サーバーを構成](#configure-your-server-to-require-certificates)します。これは、IIS、Kestrel、Azure Web Apps など、使用している他の任意のものです。</span><span class="sxs-lookup"><span data-stu-id="597e2-107">[Configure your server](#configure-your-server-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="597e2-108">プロキシとロードバランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="597e2-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="597e2-109">証明書認証は、主に、プロキシまたはロードバランサーがクライアントとサーバー間のトラフィックを処理しない場合に使用されるステートフルシナリオです。</span><span class="sxs-lookup"><span data-stu-id="597e2-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="597e2-110">プロキシまたはロードバランサーが使用されている場合、証明書の認証はプロキシまたはロードバランサーの場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="597e2-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="597e2-111">認証を処理します。</span><span class="sxs-lookup"><span data-stu-id="597e2-111">Handles the authentication.</span></span>
* <span data-ttu-id="597e2-112">認証情報に対して動作するユーザー認証情報をアプリに渡します (要求ヘッダーなど)。</span><span class="sxs-lookup"><span data-stu-id="597e2-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="597e2-113">プロキシとロードバランサーを使用する環境での証明書認証の代わりに、OpenID Connect (OIDC) を使用したフェデレーションサービス (ADFS) Active Directory ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="597e2-114">開始</span><span class="sxs-lookup"><span data-stu-id="597e2-114">Get started</span></span>

<span data-ttu-id="597e2-115">HTTPS 証明書を取得して適用し、証明書を要求するように [サーバーを構成](#configure-your-server-to-require-certificates) します。</span><span class="sxs-lookup"><span data-stu-id="597e2-115">Acquire an HTTPS certificate, apply it, and [configure your server](#configure-your-server-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="597e2-116">Web アプリで、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="597e2-116">In your web app, add a reference to the [Microsoft.AspNetCore.Authentication.Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) package.</span></span> <span data-ttu-id="597e2-117">次に、 `Startup.ConfigureServices` メソッドで、オプションを指定してを呼び出し `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` 、 `OnCertificateValidated` 要求と共に送信されるクライアント証明書に対して補助的な検証を実行するためのデリゲートを提供します。</span><span class="sxs-lookup"><span data-stu-id="597e2-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="597e2-118">その情報をに変換 `ClaimsPrincipal` し、プロパティに設定し `context.Principal` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="597e2-119">認証が失敗した場合、このハンドラーは、 `403 (Forbidden)` 予期したとおりに応答を返し `401 (Unauthorized)` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="597e2-120">これは、最初の TLS 接続中に認証が行われるということです。</span><span class="sxs-lookup"><span data-stu-id="597e2-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="597e2-121">ハンドラーに到達するまでには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="597e2-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="597e2-122">匿名接続から証明書を使用して接続をアップグレードする方法はありません。</span><span class="sxs-lookup"><span data-stu-id="597e2-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="597e2-123">また `app.UseAuthentication();` 、メソッドにを追加 `Startup.Configure` します。</span><span class="sxs-lookup"><span data-stu-id="597e2-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="597e2-124">それ以外の場合、は `HttpContext.User` `ClaimsPrincipal` 証明書から作成されるように設定されません。</span><span class="sxs-lookup"><span data-stu-id="597e2-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="597e2-125">例:</span><span class="sxs-lookup"><span data-stu-id="597e2-125">For example:</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

<span data-ttu-id="597e2-126">前の例では、証明書認証を追加する既定の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="597e2-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="597e2-127">ハンドラーは、共通の証明書のプロパティを使用してユーザープリンシパルを構築します。</span><span class="sxs-lookup"><span data-stu-id="597e2-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="597e2-128">証明書の検証の構成</span><span class="sxs-lookup"><span data-stu-id="597e2-128">Configure certificate validation</span></span>

<span data-ttu-id="597e2-129">ハンドラーには、 `CertificateAuthenticationOptions` 証明書に対して実行する必要のある検証の最小値である組み込みの検証がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="597e2-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="597e2-130">これらの各設定は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="597e2-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="597e2-131">AllowedCertificateTypes = チェーン、自己署名、またはすべて (チェーン |自己署名済み)</span><span class="sxs-lookup"><span data-stu-id="597e2-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="597e2-132">既定値:`CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="597e2-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="597e2-133">このチェックでは、適切な証明書の種類のみが許可されていることが検証されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="597e2-134">アプリで自己署名証明書を使用している場合は、このオプションをまたはに設定する必要があり `CertificateTypes.All` `CertificateTypes.SelfSigned` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="597e2-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="597e2-135">ValidateCertificateUse</span></span>

<span data-ttu-id="597e2-136">既定値:`true`</span><span class="sxs-lookup"><span data-stu-id="597e2-136">Default value: `true`</span></span>

<span data-ttu-id="597e2-137">このチェックでは、クライアントが提示した証明書にクライアント認証の拡張キー使用法 (EKU) があること、またはまったく Eku がないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="597e2-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="597e2-138">仕様として、EKU が指定されていない場合は、すべての Eku が有効と見なされます。</span><span class="sxs-lookup"><span data-stu-id="597e2-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="597e2-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="597e2-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="597e2-140">既定値:`true`</span><span class="sxs-lookup"><span data-stu-id="597e2-140">Default value: `true`</span></span>

<span data-ttu-id="597e2-141">このチェックでは、証明書が有効期間内であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="597e2-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="597e2-142">要求が発生するたびに、ハンドラーは、提示されたときに有効だった証明書が現在のセッション中に期限切れにならないようにします。</span><span class="sxs-lookup"><span data-stu-id="597e2-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="597e2-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="597e2-143">RevocationFlag</span></span>

<span data-ttu-id="597e2-144">既定値:`X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="597e2-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="597e2-145">チェーン内のどの証明書の失効を確認するかを指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="597e2-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="597e2-146">失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="597e2-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="597e2-147">RevocationMode</span></span>

<span data-ttu-id="597e2-148">既定値:`X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="597e2-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="597e2-149">失効確認の実行方法を指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="597e2-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="597e2-150">オンラインチェックを指定すると、証明機関への接続中に長い遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="597e2-151">失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="597e2-152">特定のパスでのみ証明書を要求するようにアプリを構成することはできますか。</span><span class="sxs-lookup"><span data-stu-id="597e2-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="597e2-153">これはできません。</span><span class="sxs-lookup"><span data-stu-id="597e2-153">This isn't possible.</span></span> <span data-ttu-id="597e2-154">証明書の交換は、HTTPS メッセージ交換の開始時に行われます。これは、その接続で最初の要求を受信する前にサーバーによって行われるため、要求フィールドに基づいてスコープを設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="597e2-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="597e2-155">ハンドラーイベント</span><span class="sxs-lookup"><span data-stu-id="597e2-155">Handler events</span></span>

<span data-ttu-id="597e2-156">ハンドラーには、次の2つのイベントがあります。</span><span class="sxs-lookup"><span data-stu-id="597e2-156">The handler has two events:</span></span>

* <span data-ttu-id="597e2-157">`OnAuthenticationFailed`: 認証中に例外が発生し、対応できる場合に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-157">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="597e2-158">`OnCertificateValidated`: 証明書が検証され、検証が渡され、既定のプリンシパルが作成された後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-158">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="597e2-159">このイベントを使用すると、独自の検証を実行したり、プリンシパルを補強したり置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="597e2-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="597e2-160">例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="597e2-160">For examples include:</span></span>
  * <span data-ttu-id="597e2-161">証明書がサービスに対して認識されているかどうかを確認しています。</span><span class="sxs-lookup"><span data-stu-id="597e2-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="597e2-162">独自のプリンシパルを構築します。</span><span class="sxs-lookup"><span data-stu-id="597e2-162">Constructing your own principal.</span></span> <span data-ttu-id="597e2-163">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

    ```csharp
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate(options =>
        {
            options.Events = new CertificateAuthenticationEvents
            {
                OnCertificateValidated = context =>
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(ClaimTypes.Name,
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };
    
                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
    
                    return Task.CompletedTask;
                }
            };
        });
    ```

<span data-ttu-id="597e2-164">受信証明書が追加の検証を満たしていない場合は、 `context.Fail("failure reason")` 失敗の理由でを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="597e2-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="597e2-165">実際の機能の場合は、データベースまたはその他の種類のユーザーストアに接続する依存関係の挿入に登録されているサービスを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="597e2-166">デリゲートに渡されたコンテキストを使用してサービスにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="597e2-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="597e2-167">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="597e2-168">概念的には、証明書の検証は承認に関する問題です。</span><span class="sxs-lookup"><span data-stu-id="597e2-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="597e2-169">内部ではなく、承認ポリシーの発行者や拇印などのチェックを追加すること `OnCertificateValidated` は、まったく可能です。</span><span class="sxs-lookup"><span data-stu-id="597e2-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-server-to-require-certificates"></a><span data-ttu-id="597e2-170">証明書を要求するようにサーバーを構成する</span><span class="sxs-lookup"><span data-stu-id="597e2-170">Configure your server to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="597e2-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="597e2-171">Kestrel</span></span>

<span data-ttu-id="597e2-172">*Program.cs* で、次のように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="597e2-172">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="597e2-173"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="597e2-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="597e2-174">IIS</span><span class="sxs-lookup"><span data-stu-id="597e2-174">IIS</span></span>

<span data-ttu-id="597e2-175">IIS マネージャーで、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="597e2-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="597e2-176">[ **接続** ] タブからサイトを選択します。</span><span class="sxs-lookup"><span data-stu-id="597e2-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="597e2-177">[**機能ビュー** ] ウィンドウで、[ **SSL 設定**] オプションをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="597e2-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="597e2-178">[ **SSL が必要**] チェックボックスをオンにし、[**クライアント証明書**] セクションの [**必須**] オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="597e2-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS でのクライアント証明書の設定](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="597e2-180">Azure とカスタム web プロキシ</span><span class="sxs-lookup"><span data-stu-id="597e2-180">Azure and custom web proxies</span></span>

<span data-ttu-id="597e2-181">証明書転送ミドルウェアを構成する方法については、 [ホストを参照し、ドキュメントを展開](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="597e2-182">Azure Web Apps で証明書認証を使用する</span><span class="sxs-lookup"><span data-stu-id="597e2-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="597e2-183">Azure では、転送構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="597e2-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="597e2-184">これは既に証明書転送ミドルウェアで設定されています。</span><span class="sxs-lookup"><span data-stu-id="597e2-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="597e2-185">これを行うには、CertificateForwardingMiddleware が存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="597e2-186">カスタム web プロキシで証明書認証を使用する</span><span class="sxs-lookup"><span data-stu-id="597e2-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="597e2-187">メソッドを使用して、 `AddCertificateForwarding` 次のように指定します。</span><span class="sxs-lookup"><span data-stu-id="597e2-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="597e2-188">クライアントヘッダー名。</span><span class="sxs-lookup"><span data-stu-id="597e2-188">The client header name.</span></span>
* <span data-ttu-id="597e2-189">(プロパティを使用して) 証明書を読み込む方法 `HeaderConverter` 。</span><span class="sxs-lookup"><span data-stu-id="597e2-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="597e2-190">カスタム web プロキシでは、証明書はカスタム要求ヘッダーとして渡されます。たとえば、のように `X-SSL-CERT` なります。</span><span class="sxs-lookup"><span data-stu-id="597e2-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="597e2-191">これを使用するには、次のように証明書の転送を構成し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;

            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="597e2-192">次に、 `Startup.Configure` メソッドはミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="597e2-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="597e2-193">`UseCertificateForwarding` は、およびの呼び出しの前に呼び出され `UseAuthentication` `UseAuthorization` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="597e2-194">別のクラスを使用して、検証ロジックを実装できます。</span><span class="sxs-lookup"><span data-stu-id="597e2-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="597e2-195">この例では同じ自己署名証明書が使用されているため、使用できるのは証明書のみであることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="597e2-196">クライアント証明書とサーバー証明書の両方の拇印が一致しているかどうかを検証します。それ以外の場合は、証明書を使用して、認証に十分なものになります。</span><span class="sxs-lookup"><span data-stu-id="597e2-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="597e2-197">これは、メソッドの内部で使用され `AddCertificate` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="597e2-198">中間証明書または子証明書を使用している場合は、ここでサブジェクトまたは発行者を検証することもできます。</span><span class="sxs-lookup"><span data-stu-id="597e2-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="597e2-199">証明書と HttpClientHandler を使用して HttpClient を実装する</span><span class="sxs-lookup"><span data-stu-id="597e2-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="597e2-200">は、 `HttpClientHandler` クラスのコンストラクターに直接追加でき `HttpClient` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-200">The `HttpClientHandler` could be added directly in the constructor of the `HttpClient` class.</span></span> <span data-ttu-id="597e2-201">のインスタンスを作成するときは注意が必要 `HttpClient` です。</span><span class="sxs-lookup"><span data-stu-id="597e2-201">Care should be taken when creating instances of the `HttpClient`.</span></span> <span data-ttu-id="597e2-202">次に、は、 `HttpClient` 要求ごとに証明書を送信します。</span><span class="sxs-lookup"><span data-stu-id="597e2-202">The `HttpClient` will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="597e2-203">IHttpClientFactory から証明書と名前付き HttpClient を使用して HttpClient を実装する</span><span class="sxs-lookup"><span data-stu-id="597e2-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="597e2-204">次の例では、 `HttpClientHandler` ハンドラーのプロパティを使用して、クライアント証明書をに追加し `ClientCertificates` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-204">In the following example, a client certificate is added to a `HttpClientHandler` using the `ClientCertificates` property from the handler.</span></span> <span data-ttu-id="597e2-205">このハンドラーは、 `HttpClient` メソッドを使用して、の名前付きインスタンスで使用でき `ConfigurePrimaryHttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-205">This handler can then be used in a named instance of an `HttpClient` using the `ConfigurePrimaryHttpMessageHandler` method.</span></span> <span data-ttu-id="597e2-206">次のように設定されてい `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-206">This is setup in `Startup.ConfigureServices`:</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="597e2-207">その後、を使用して、 `IHttpClientFactory` ハンドラーと証明書を使用して名前付きインスタンスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="597e2-207">The `IHttpClientFactory` can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="597e2-208">`CreateClient` `Startup` インスタンスを取得するには、クラスで定義されているクライアントの名前を持つメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="597e2-208">The `CreateClient` method with the name of the client defined in the `Startup` class is used to get the instance.</span></span> <span data-ttu-id="597e2-209">HTTP 要求は、クライアントを使用して必要に応じて送信できます。</span><span class="sxs-lookup"><span data-stu-id="597e2-209">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="597e2-210">正しい証明書がサーバーに送信されると、データが返されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="597e2-211">証明書または間違った証明書が送信されない場合は、HTTP 403 ステータスコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="597e2-212">PowerShell での証明書の作成</span><span class="sxs-lookup"><span data-stu-id="597e2-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="597e2-213">証明書の作成は、このフローを設定するうえで最も難しい部分です。</span><span class="sxs-lookup"><span data-stu-id="597e2-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="597e2-214">ルート証明書は、PowerShell コマンドレットを使用して作成でき `New-SelfSignedCertificate` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="597e2-215">証明書を作成するときは、強力なパスワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="597e2-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="597e2-216">ここで示すように、 `KeyUsageProperty` パラメーターとパラメーターを追加することが重要です `KeyUsage` 。</span><span class="sxs-lookup"><span data-stu-id="597e2-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="597e2-217">ルート CA の作成</span><span class="sxs-lookup"><span data-stu-id="597e2-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="597e2-218">`-DnsName`パラメーター値は、アプリの配置ターゲットと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="597e2-219">たとえば、開発用の "localhost" などです。</span><span class="sxs-lookup"><span data-stu-id="597e2-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="597e2-220">信頼されたルートにインストールする</span><span class="sxs-lookup"><span data-stu-id="597e2-220">Install in the trusted root</span></span>

<span data-ttu-id="597e2-221">ルート証明書は、ホストシステムで信頼されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="597e2-222">証明機関によって作成されていないルート証明書は、既定では信頼されません。</span><span class="sxs-lookup"><span data-stu-id="597e2-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="597e2-223">次のリンクを使用して、Windows でこれを実現する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="597e2-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="597e2-224">中間証明機関の証明書</span><span class="sxs-lookup"><span data-stu-id="597e2-224">Intermediate certificate</span></span>

<span data-ttu-id="597e2-225">ルート証明書から中間証明書を作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="597e2-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="597e2-226">これはすべてのユースケースで必須ではありませんが、多くの証明書を作成したり、証明書のグループをアクティブ化または無効にしたりする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="597e2-227">`TextExtension`パラメーターは、証明書の基本的な制約でパスの長さを設定するために必要です。</span><span class="sxs-lookup"><span data-stu-id="597e2-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="597e2-228">その後、中間証明書を Windows ホストシステムの信頼された中間証明書に追加できます。</span><span class="sxs-lookup"><span data-stu-id="597e2-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="597e2-229">中間証明書から子証明書を作成する</span><span class="sxs-lookup"><span data-stu-id="597e2-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="597e2-230">子証明書は、中間証明書から作成できます。</span><span class="sxs-lookup"><span data-stu-id="597e2-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="597e2-231">これは end エンティティであり、さらに子証明書を作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="597e2-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="597e2-232">ルート証明書から子証明書を作成する</span><span class="sxs-lookup"><span data-stu-id="597e2-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="597e2-233">子証明書は、ルート証明書から直接作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="597e2-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="597e2-234">ルート中間証明書の例-証明書</span><span class="sxs-lookup"><span data-stu-id="597e2-234">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="597e2-235">ルート証明書、中間証明書、または子証明書を使用する場合は、必要に応じて拇印または PublicKey を使用して証明書を検証できます。</span><span class="sxs-lookup"><span data-stu-id="597e2-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a><span data-ttu-id="597e2-236">証明書の検証キャッシュ</span><span class="sxs-lookup"><span data-stu-id="597e2-236">Certificate validation caching</span></span>

<span data-ttu-id="597e2-237">ASP.NET Core 5.0 以降のバージョンでは、検証結果のキャッシュを有効にする機能がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="597e2-237">ASP.NET Core 5.0 and later versions support the ability to enable caching of validation results.</span></span> <span data-ttu-id="597e2-238">キャッシュは、検証がコストのかかる操作であるため、証明書の認証のパフォーマンスを大幅に向上させます。</span><span class="sxs-lookup"><span data-stu-id="597e2-238">The caching dramatically improves performance of certificate authentication, as validation is an expensive operation.</span></span>

<span data-ttu-id="597e2-239">既定では、証明書の認証によってキャッシュが無効になります。</span><span class="sxs-lookup"><span data-stu-id="597e2-239">By default, certificate authentication disables caching.</span></span> <span data-ttu-id="597e2-240">キャッシュを有効にするには、 `AddCertificateCache` 次のようにを呼び出し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-240">To enable caching, call `AddCertificateCache` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

<span data-ttu-id="597e2-241">既定のキャッシュ実装は、結果をメモリに格納します。</span><span class="sxs-lookup"><span data-stu-id="597e2-241">The default caching implementation stores results in memory.</span></span> <span data-ttu-id="597e2-242">独自のキャッシュを提供するには、を実装 `ICertificateValidationCache` し、依存関係の挿入に登録します。</span><span class="sxs-lookup"><span data-stu-id="597e2-242">You can provide your own cache by implementing `ICertificateValidationCache` and registering it with dependency injection.</span></span> <span data-ttu-id="597e2-243">たとえば、「 `services.AddSingleton<ICertificateValidationCache, YourCache>()` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="597e2-243">For example, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span></span>

::: moniker-end

## <a name="optional-client-certificates"></a><span data-ttu-id="597e2-244">オプションのクライアント証明書</span><span class="sxs-lookup"><span data-stu-id="597e2-244">Optional client certificates</span></span>

<span data-ttu-id="597e2-245">このセクションでは、証明書を使用してアプリのサブセットを保護する必要があるアプリについて説明します。</span><span class="sxs-lookup"><span data-stu-id="597e2-245">This section provides information for apps that must protect a subset of the app with a certificate.</span></span> <span data-ttu-id="597e2-246">たとえば、 Razor アプリ内のページまたはコントローラーにクライアント証明書が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-246">For example, a Razor Page or controller in the app might require client certificates.</span></span> <span data-ttu-id="597e2-247">これにより、クライアント証明書としての問題が示されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-247">This presents challenges as client certificates:</span></span>
  
* <span data-ttu-id="597e2-248">は TLS 機能であり、HTTP 機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="597e2-248">Are a TLS feature, not an HTTP feature.</span></span>
* <span data-ttu-id="597e2-249">は接続ごとにネゴシエートされます。 HTTP データを使用できるようにするには、接続の開始時にネゴシエートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-249">Are negotiated per-connection and must be be negotiated at the start of the connection before any HTTP data is available.</span></span> <span data-ttu-id="597e2-250">接続の開始時には、Server Name Indication (SNI) のみ &dagger; が認識されます。</span><span class="sxs-lookup"><span data-stu-id="597e2-250">At the start of the connection, only the Server Name Indication (SNI)&dagger; is known.</span></span> <span data-ttu-id="597e2-251">クライアント証明書とサーバー証明書は、接続の最初の要求の前にネゴシエートされ、通常、要求は再ネゴシエートできません。</span><span class="sxs-lookup"><span data-stu-id="597e2-251">The client and server certificates are negotiated prior to the first request on a connection and requests generally aren't able to renegotiate.</span></span>

<span data-ttu-id="597e2-252">TLS 再ネゴシエーションは、オプションのクライアント証明書を実装するための従来の方法でした。</span><span class="sxs-lookup"><span data-stu-id="597e2-252">TLS renegotiation was an old way to implement optional client certificates.</span></span> <span data-ttu-id="597e2-253">これは、次の理由で推奨されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="597e2-253">This is no longer recommended because:</span></span>
- <span data-ttu-id="597e2-254">HTTP/1.1 では、POST 要求中に renegotiating を実行すると、要求本文が TCP ウィンドウをいっぱいにして再ネゴシエーションパケットを受信できないデッドロックが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="597e2-254">In HTTP/1.1, renegotiating during a POST request could cause a deadlock where the request body filled up the TCP window and the renegotiation packets can't be received.</span></span>
- <span data-ttu-id="597e2-255">HTTP/2 は、再ネゴシエーションを [明示的に禁止](https://tools.ietf.org/html/rfc7540#section-9.2.1) します。</span><span class="sxs-lookup"><span data-stu-id="597e2-255">HTTP/2 [explicitly prohibits](https://tools.ietf.org/html/rfc7540#section-9.2.1) renegotiation.</span></span>
- <span data-ttu-id="597e2-256">TLS 1.3 では再ネゴシエーションのサポートが [削除され](https://tools.ietf.org/html/rfc8740#section-1) ました。</span><span class="sxs-lookup"><span data-stu-id="597e2-256">TLS 1.3 has [removed](https://tools.ietf.org/html/rfc8740#section-1) support for renegotiation.</span></span>

<span data-ttu-id="597e2-257">ASP.NET Core 5 preview 7 以降では、オプションのクライアント証明書の便利なサポートが追加されています。</span><span class="sxs-lookup"><span data-stu-id="597e2-257">ASP.NET Core 5 preview 7 and later adds more convenient support for optional client certificates.</span></span> <span data-ttu-id="597e2-258">詳細については、 [オプションの証明書のサンプル](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-258">For more information, see the [Optional certificates sample](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span></span>

<span data-ttu-id="597e2-259">次の方法では、オプションのクライアント証明書がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="597e2-259">The following approach supports optional client certificates:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="597e2-260">ドメインとサブドメインのバインドを設定します。</span><span class="sxs-lookup"><span data-stu-id="597e2-260">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="597e2-261">たとえば、とでバインドを設定 `contoso.com` し `myClient.contoso.com` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-261">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="597e2-262">`contoso.com`ホストはクライアント証明書を必要としませんが、そう `myClient.contoso.com` です。</span><span class="sxs-lookup"><span data-stu-id="597e2-262">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="597e2-263">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-263">For more information, see:</span></span>
    * <span data-ttu-id="597e2-264">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="597e2-264">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="597e2-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="597e2-265">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel/endpoints#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="597e2-266">注 Kestrel では、現在、1つのバインドで複数の TLS 構成がサポートされていません。一意の Ip またはポートを持つ2つのバインドが必要です。</span><span class="sxs-lookup"><span data-stu-id="597e2-266">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="597e2-267">「https://github.com/dotnet/runtime/issues/31097」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-267">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="597e2-268">IIS</span><span class="sxs-lookup"><span data-stu-id="597e2-268">IIS</span></span>
      * [<span data-ttu-id="597e2-269">IIS のホスト</span><span class="sxs-lookup"><span data-stu-id="597e2-269">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="597e2-270">IIS でのセキュリティの構成</span><span class="sxs-lookup"><span data-stu-id="597e2-270">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="597e2-271">HTTP.sys: [Windows Server の構成](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="597e2-271">HTTP.sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="597e2-272">ドメインとサブドメインのバインドを設定します。</span><span class="sxs-lookup"><span data-stu-id="597e2-272">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="597e2-273">たとえば、とでバインドを設定 `contoso.com` し `myClient.contoso.com` ます。</span><span class="sxs-lookup"><span data-stu-id="597e2-273">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="597e2-274">`contoso.com`ホストはクライアント証明書を必要としませんが、そう `myClient.contoso.com` です。</span><span class="sxs-lookup"><span data-stu-id="597e2-274">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="597e2-275">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-275">For more information, see:</span></span>
    * <span data-ttu-id="597e2-276">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="597e2-276">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="597e2-277">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="597e2-277">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="597e2-278">注 Kestrel では、現在、1つのバインドで複数の TLS 構成がサポートされていません。一意の Ip またはポートを持つ2つのバインドが必要です。</span><span class="sxs-lookup"><span data-stu-id="597e2-278">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="597e2-279">「https://github.com/dotnet/runtime/issues/31097」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-279">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="597e2-280">IIS</span><span class="sxs-lookup"><span data-stu-id="597e2-280">IIS</span></span>
      * [<span data-ttu-id="597e2-281">IIS のホスト</span><span class="sxs-lookup"><span data-stu-id="597e2-281">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="597e2-282">IIS でのセキュリティの構成</span><span class="sxs-lookup"><span data-stu-id="597e2-282">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="597e2-283">HTTP.sys: [Windows Server の構成](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="597e2-283">HTTP.sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

* <span data-ttu-id="597e2-284">クライアント証明書を必要としていない web アプリへの要求の場合:</span><span class="sxs-lookup"><span data-stu-id="597e2-284">For requests to the web app that require a client certificate and don't have one:</span></span>
  * <span data-ttu-id="597e2-285">クライアント証明書で保護されたサブドメインを使用して同じページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="597e2-285">Redirect to the same page using the client certificate protected subdomain.</span></span>
  * <span data-ttu-id="597e2-286">たとえば、にリダイレクト `myClient.contoso.com/requestedPage` します。</span><span class="sxs-lookup"><span data-stu-id="597e2-286">For example, redirect to `myClient.contoso.com/requestedPage`.</span></span> <span data-ttu-id="597e2-287">への要求は `myClient.contoso.com/requestedPage` とは異なるホスト名であるため、 `contoso.com/requestedPage` クライアントは別の接続を確立し、クライアント証明書を提供します。</span><span class="sxs-lookup"><span data-stu-id="597e2-287">Because the request to `myClient.contoso.com/requestedPage` is a different hostname than `contoso.com/requestedPage`, the client establishes a different connection and the client certificate is provided.</span></span>
  * <span data-ttu-id="597e2-288">詳細については、「<xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="597e2-288">For more information, see <xref:security/authorization/introduction>.</span></span>

<span data-ttu-id="597e2-289">[この GitHub ディスカッション](https://github.com/dotnet/AspNetCore.Docs/issues/18720)の問題では、オプションのクライアント証明書に関する質問、コメント、その他のフィードバックを残しておきます。</span><span class="sxs-lookup"><span data-stu-id="597e2-289">Leave questions, comments, and other feedback on optional client certificates in [this GitHub discussion](https://github.com/dotnet/AspNetCore.Docs/issues/18720) issue.</span></span>

<span data-ttu-id="597e2-290">&dagger; Server Name Indication (SNI) は、SSL ネゴシエーションの一部として仮想ドメインを含めるための TLS 拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="597e2-290">&dagger; Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="597e2-291">これは実質的に、仮想ドメイン名またはホスト名を使用してネットワークエンドポイントを識別できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="597e2-291">This effectively means the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

---
title: ホストされている ASP.NET Core Blazor WebAssembly アプリを Identity Server でセキュリティ保護する
author: guardrex
description: ホストされている ASP.NET Core Blazor WebAssembly アプリを Identity Server でセキュリティ保護する方法について説明します。
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: c74711c10fe399718600f879c3d9151bfb1abd42
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281008"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="a1e7c-103">ASP.NET Core Blazor WebAssembly でホストされているアプリを Identity Server でセキュリティ保護する</span><span class="sxs-lookup"><span data-stu-id="a1e7c-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="a1e7c-104">この記事では、ユーザーと API 呼び出しの認証に [IdentityServer](https://identityserver.io/) を使用する、[ホステッド Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-104">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="a1e7c-105">既存の外部 Identity サーバー インスタンスを使用するように、スタンドアロンの、またはホストされた Blazor WebAssembly アプリを構成するには、<xref:blazor/security/webassembly/standalone-with-authentication-library> のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-105">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1e7c-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1e7c-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1e7c-107">認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="a1e7c-108">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor WebAssembly アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="a1e7c-109">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納するには、 **[アプリ内のストア ユーザー アカウント]** オプションを使用して、 **[個人のユーザー アカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="a1e7c-110">**[詳細設定]** セクションで、 **[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-110">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="a1e7c-111">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1e7c-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="a1e7c-112">空のフォルダーに認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには、`-au|--auth` オプションを使用して `Individual` 認証メカニズムを指定し、ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-112">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="a1e7c-113">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="a1e7c-113">Placeholder</span></span>  | <span data-ttu-id="a1e7c-114">例</span><span class="sxs-lookup"><span data-stu-id="a1e7c-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="a1e7c-115">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="a1e7c-116">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1e7c-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a1e7c-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a1e7c-118">認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="a1e7c-119">**[新しい Blazor WebAssembly アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="a1e7c-120">ASP.NET Core の [Identity](xref:security/authentication/identity) を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="a1e7c-121">**[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-121">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="a1e7c-122">*`Server`* アプリの構成</span><span class="sxs-lookup"><span data-stu-id="a1e7c-122">*`Server`* app configuration</span></span>

<span data-ttu-id="a1e7c-123">次のセクションでは、認証のサポートが含まれる場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-123">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="a1e7c-124">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="a1e7c-124">Startup class</span></span>

<span data-ttu-id="a1e7c-125">`Startup` クラスには次のものが追加されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-125">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="a1e7c-126">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-126">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="a1e7c-127">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-127">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="a1e7c-128">IdentityServer 上に既定の ASP.NET Core 規則を設定する <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドが追加された IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-128">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="a1e7c-129">IdentityServer によって生成された JWT トークンを検証するようにアプリを構成する <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパー メソッドが追加された Authentication:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-129">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="a1e7c-130">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-130">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="a1e7c-131">IdentityServer のミドルウェアによって、OpenID Connect (OIDC) のエンドポイントが公開されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-131">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="a1e7c-132">認証ミドルウェアでは、要求の資格情報の検証と、要求コンテキストでのユーザーの設定が行われます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-132">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="a1e7c-133">承認ミドルウェアにより、承認機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-133">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthorization();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="a1e7c-134">Azure App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="a1e7c-134">Azure App Service on Linux</span></span>

<span data-ttu-id="a1e7c-135">Azure App Service on Linux にデプロイするときに、発行者を明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-135">Specify the issuer explicitly when deploying to Azure App Service on Linux.</span></span> <span data-ttu-id="a1e7c-136">詳細については、「<xref:security/authentication/identity/spa#azure-app-service-on-linux>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-136">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

### <a name="addapiauthorization"></a><span data-ttu-id="a1e7c-137">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="a1e7c-137">AddApiAuthorization</span></span>

<span data-ttu-id="a1e7c-138"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドでは、ASP.NET Core シナリオ対応に [IdentityServer](https://identityserver.io/) が構成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-138">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="a1e7c-139">IdentityServer は、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-139">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="a1e7c-140">IdentityServer を使用すると、ほとんどの一般的なシナリオには必要のない複雑さが発生します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-140">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="a1e7c-141">そのため、使用開始時に適切であると考えられる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-141">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="a1e7c-142">認証のニーズが変わったら、IdentityServer のあらゆる機能を利用し、アプリの要件に合わせて認証をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-142">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="a1e7c-143">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="a1e7c-143">AddIdentityServerJwt</span></span>

<span data-ttu-id="a1e7c-144"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパー メソッドでは、アプリに対するポリシー スキームが既定の認証ハンドラーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-144">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="a1e7c-145">そのポリシーは、Identity の URL 空間 `/Identity` のサブパスにルーティングされたすべての要求を Identity で処理できるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-145">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="a1e7c-146">それ以外のすべての要求は、<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> で処理されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-146">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="a1e7c-147">さらに、このメソッドでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-147">Additionally, this method:</span></span>

* <span data-ttu-id="a1e7c-148">既定のスコープ `{APPLICATION NAME}API` を使用して、`{APPLICATION NAME}API` API リソースが IdentityServer に登録されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-148">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="a1e7c-149">IdentityServer によってアプリに対して発行されたトークンを検証するように、JWT ベアラー トークン ミドルウェアが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-149">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="a1e7c-150">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="a1e7c-150">WeatherForecastController</span></span>

<span data-ttu-id="a1e7c-151">`WeatherForecastController` (`Controllers/WeatherForecastController.cs`) では、[`[Authorize]` 属性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)がクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-151">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) is applied to the class.</span></span> <span data-ttu-id="a1e7c-152">その属性では、ユーザーはリソースにアクセスするために既定のポリシーに基づいて承認される必要があることが示されています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-152">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="a1e7c-153">既定の承認ポリシーは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> によって設定される既定の認証スキームを使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-153">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="a1e7c-154">ヘルパー メソッドでは、アプリへの要求に対する既定のハンドラーとして <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> が構成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-154">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="a1e7c-155">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="a1e7c-155">ApplicationDbContext</span></span>

<span data-ttu-id="a1e7c-156">`ApplicationDbContext` (`Data/ApplicationDbContext.cs`) では、<xref:Microsoft.EntityFrameworkCore.DbContext> により、IdentityServer 用のスキーマを含むように <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> が拡張されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-156">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="a1e7c-157"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-157"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="a1e7c-158">データベース スキーマを完全に制御するには、使用可能な Identity <xref:Microsoft.EntityFrameworkCore.DbContext> クラスの 1 つを継承し、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> メソッドで `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` を呼び出すことによって、Identity スキーマを含むようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-158">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="a1e7c-159">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="a1e7c-159">OidcConfigurationController</span></span>

<span data-ttu-id="a1e7c-160">`OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) では、OIDC パラメーターを提供するために、クライアント エンドポイントがプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-160">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="a1e7c-161">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="a1e7c-161">App settings</span></span>

<span data-ttu-id="a1e7c-162">プロジェクト ルートにあるアプリ設定ファイル (`appsettings.json`) の `IdentityServer` セクションには、構成されているクライアントの一覧が記述されてます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-162">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="a1e7c-163">次の例には、1 つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-163">In the following example, there's a single client.</span></span> <span data-ttu-id="a1e7c-164">クライアント名はアプリケーション名に対応し、規則によって OAuth の `ClientId` パラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-164">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="a1e7c-165">構成対象のアプリの種類は、プロファイルによって示されています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-165">The profile indicates the app type being configured.</span></span> <span data-ttu-id="a1e7c-166">プロファイルは、サーバーの構成プロセスを簡素化する規則を促進するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-166">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="a1e7c-167">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.Client`)。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-167">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="a1e7c-168">*`Client`* アプリの構成</span><span class="sxs-lookup"><span data-stu-id="a1e7c-168">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a1e7c-169">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="a1e7c-169">Authentication package</span></span>

<span data-ttu-id="a1e7c-170">個人のユーザー アカウント (`Individual`) を使用するようにアプリを作成すると、アプリはそのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-170">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="a1e7c-171">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-171">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a1e7c-172">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-172">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="a1e7c-173">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-173">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="a1e7c-174">`HttpClient` 構成</span><span class="sxs-lookup"><span data-stu-id="a1e7c-174">`HttpClient` configuration</span></span>

<span data-ttu-id="a1e7c-175">`Program.Main` (`Program.cs`) では、サーバー API への要求を行うときのアクセス トークンが含まれる <xref:System.Net.Http.HttpClient> インスタンスを提供するように、名前付きの <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) が構成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-175">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="a1e7c-176">ホストされている Blazorソリューションの一部ではない既存の Identity Server インスタンスを使用するように Blazor WebAssembly アプリを構成する場合は、<xref:System.Net.Http.HttpClient> ベース アドレスの登録を <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) からサーバー アプリの API 認証エンドポイント URL に変更します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-176">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="a1e7c-177">API の承認のサポート</span><span class="sxs-lookup"><span data-stu-id="a1e7c-177">API authorization support</span></span>

<span data-ttu-id="a1e7c-178">ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージ内で提供される拡張メソッドによって、サービス コンテナーに接続されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-178">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="a1e7c-179">このメソッドでは、アプリが既存の承認システムとやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-179">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="a1e7c-180">既定では、アプリの構成は規則により `_configuration/{client-id}` から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-180">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="a1e7c-181">規則により、アプリのアセンブリ名にはクライアント ID が設定されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-181">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="a1e7c-182">オプションを指定してオーバーロードを呼び出すことにより、別のエンドポイントを指すようにこの URL を変更できます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-182">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="a1e7c-183">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="a1e7c-183">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a1e7c-184">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="a1e7c-184">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="a1e7c-185">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a1e7c-185">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a1e7c-186">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a1e7c-186">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a1e7c-187">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a1e7c-187">LoginDisplay component</span></span>

<span data-ttu-id="a1e7c-188">`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-188">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="a1e7c-189">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-189">For authenticated users:</span></span>
  * <span data-ttu-id="a1e7c-190">現在のユーザー名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-190">Displays the current user name.</span></span>
  * <span data-ttu-id="a1e7c-191">ASP.NET Core Identity のユーザー プロファイル ページへのリンクが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-191">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="a1e7c-192">アプリからログアウトするためのボタンが用意されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-192">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="a1e7c-193">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-193">For anonymous users:</span></span>
  * <span data-ttu-id="a1e7c-194">登録するオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-194">Offers the option to register.</span></span>
  * <span data-ttu-id="a1e7c-195">ログインするオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-195">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="a1e7c-196">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a1e7c-196">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a1e7c-197">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a1e7c-197">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a1e7c-198">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="a1e7c-198">Run the app</span></span>

<span data-ttu-id="a1e7c-199">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-199">Run the app from the Server project.</span></span> <span data-ttu-id="a1e7c-200">Visual Studio を使用しているときは、次のいずれかを行います。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-200">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a1e7c-201">ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-201">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a1e7c-202">**ソリューション エクスプローラー** でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-202">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="a1e7c-203">API 認証での名前とロール要求</span><span class="sxs-lookup"><span data-stu-id="a1e7c-203">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="a1e7c-204">カスタム ユーザー ファクトリ</span><span class="sxs-lookup"><span data-stu-id="a1e7c-204">Custom user factory</span></span>

<span data-ttu-id="a1e7c-205">*`Client`* アプリで、カスタム ユーザー ファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-205">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="a1e7c-206">Identity Server により、複数のロールが JSON 配列として 1 つの `role` 要求で送信されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-206">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="a1e7c-207">1 つのロールは、要求内で文字列値として送信されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-207">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="a1e7c-208">ファクトリにより、ユーザーのロールごとに個別の `role` 要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-208">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="a1e7c-209">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-209">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="a1e7c-210">*`Client`* アプリでは、`Program.Main` (`Program.cs`) にファクトリを登録します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-210">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="a1e7c-211">*`Server`* アプリでは、Identity ビルダーで <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> を呼び出します。これにより、ロールに関連するサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-211">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="a1e7c-212">Identity Server を構成する</span><span class="sxs-lookup"><span data-stu-id="a1e7c-212">Configure Identity Server</span></span>

<span data-ttu-id="a1e7c-213">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-213">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="a1e7c-214">API 承認のオプション</span><span class="sxs-lookup"><span data-stu-id="a1e7c-214">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="a1e7c-215">プロファイル サービス</span><span class="sxs-lookup"><span data-stu-id="a1e7c-215">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="a1e7c-216">API 承認のオプション</span><span class="sxs-lookup"><span data-stu-id="a1e7c-216">API authorization options</span></span>

<span data-ttu-id="a1e7c-217">*`Server`* アプリで:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-217">In the *`Server`* app:</span></span>

* <span data-ttu-id="a1e7c-218">`name` 要求と `role` 要求を ID トークンとアクセス トークンに挿入するように、Identity Server を構成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-218">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="a1e7c-219">JWT トークン ハンドラーでロールの既定のマッピングを禁止します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-219">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="a1e7c-220">プロファイル サービス</span><span class="sxs-lookup"><span data-stu-id="a1e7c-220">Profile Service</span></span>

<span data-ttu-id="a1e7c-221">*`Server`* アプリで、`ProfileService` の実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-221">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="a1e7c-222">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-222">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="a1e7c-223">*`Server`* アプリで、`Startup.ConfigureServices` にプロファイル サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-223">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="a1e7c-224">承認メカニズムを使用する</span><span class="sxs-lookup"><span data-stu-id="a1e7c-224">Use authorization mechanisms</span></span>

<span data-ttu-id="a1e7c-225">*`Client`* アプリでは、この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-225">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="a1e7c-226">コンポーネント内のすべての承認メカニズムで、ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-226">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="a1e7c-227">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="a1e7c-227">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="a1e7c-228">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="a1e7c-228">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="a1e7c-229">[手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="a1e7c-229">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="a1e7c-230">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-230">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="a1e7c-231">`User.Identity.Name` には、 *`Client`* アプリにおいてユーザーのユーザー名が設定されます。これは通常、サインイン メール アドレスです。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-231">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain-and-certificate"></a><span data-ttu-id="a1e7c-232">カスタム ドメインと証明書を使用した Azure App Service でのホスト</span><span class="sxs-lookup"><span data-stu-id="a1e7c-232">Host in Azure App Service with a custom domain and certificate</span></span>

<span data-ttu-id="a1e7c-233">以下のガイダンスでは、次について説明します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-233">The following guidance explains:</span></span>

* <span data-ttu-id="a1e7c-234">Identity Server を使用してホストされている Blazor WebAssembly アプリを、カスタム ドメインを使用して [Azure App Service](https://azure.microsoft.com/services/app-service/) にデプロイする方法。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-234">How to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>
* <span data-ttu-id="a1e7c-235">ブラウザーとの HTTPS プロトコル通信に対して、TLS 証明書を作成して使用する方法。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-235">How to create and use a TLS certificate for HTTPS protocol communication with browsers.</span></span> <span data-ttu-id="a1e7c-236">このガイダンスではカスタム ドメインで証明書を使用する方法に焦点を当てていますが、`contoso.azurewebsites.net` などの既定の Azure アプリのドメインを使用する場合にも同様に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-236">Although the guidance focuses on using the certificate with a custom domain, the guidance is equally applicable to using a default Azure Apps domain, for example `contoso.azurewebsites.net`.</span></span>

<span data-ttu-id="a1e7c-237">このホスティング シナリオの場合、[Identity Server のトークン署名キー](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation)と、サイトの HTTPS でセキュリティ保護されたブラウザーとの通信に、同じ証明書を **使用しないでください**。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-237">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="a1e7c-238">この 2 つの要件に対して異なる証明書を使用することは、それぞれの目的に対して秘密キーが分離されるため、優れたセキュリティ方法です。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-238">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="a1e7c-239">ブラウザーとの通信に使用される TLS 証明書は、Identity Server のトークン署名に影響を与えることなく、個別に管理されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-239">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="a1e7c-240">カスタム ドメイン バインドのために [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) で App Service アプリに証明書を提供すると、Identity Server で Azure Key Vault からトークン署名用に同じ証明書を取得することはできません。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-240">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="a1e7c-241">物理パスから同じ TLS 証明書を使用するように Identity Server を構成することはできますが、セキュリティ証明書をソース管理に置くことは **不適切な方法であり、ほとんどのシナリオで避ける必要があります**。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-241">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="a1e7c-242">次のガイダンスでは、自己署名証明書は Identity Server のトークン署名のためだけに Azure Key Vault に作成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-242">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="a1e7c-243">Identity Server の構成により、アプリの `My` > `CurrentUser` 証明書ストアを介して、キー コンテナー証明書が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-243">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="a1e7c-244">カスタム ドメインで HTTPS トラフィックに使用される他の証明書は、Identity Server の署名証明書とは別に作成および構成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-244">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="a1e7c-245">カスタム ドメインと HTTPS を使用してホストするようにアプリ、Azure App Service、Azure Key Vault を構成するには:</span><span class="sxs-lookup"><span data-stu-id="a1e7c-245">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="a1e7c-246">プラン レベル `Basic B1` 以上を使用して、[App Service プラン](/azure/app-service/overview-hosting-plans)を作成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-246">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="a1e7c-247">App Service でカスタム ドメインを使用するには、`Basic B1` 以上のサービス レベルが必要です。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-247">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="a1e7c-248">組織で管理するサイトの完全修飾ドメイン名 (FQDN) の共通名を使用して (例: `www.contoso.com`)、サイトのセキュリティで保護されたブラウザー通信 (HTTPS プロトコル) 用の PFX 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-248">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="a1e7c-249">次のように証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-249">Create the certificate with:</span></span>
   * <span data-ttu-id="a1e7c-250">キーで使用</span><span class="sxs-lookup"><span data-stu-id="a1e7c-250">Key uses</span></span>
     * <span data-ttu-id="a1e7c-251">デジタル署名の検証 (`digitalSignature`)</span><span class="sxs-lookup"><span data-stu-id="a1e7c-251">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="a1e7c-252">キーの暗号化 (`keyEncipherment`)</span><span class="sxs-lookup"><span data-stu-id="a1e7c-252">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="a1e7c-253">強化および拡張されたキーで使用</span><span class="sxs-lookup"><span data-stu-id="a1e7c-253">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="a1e7c-254">クライアント認証 (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="a1e7c-254">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="a1e7c-255">サーバー認証 (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="a1e7c-255">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="a1e7c-256">証明書を作成するには、次のいずれかの方法、または他の適切なツールやオンライン サービスを使用します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-256">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="a1e7c-257">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a1e7c-257">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="a1e7c-258">Windows での MakeCert</span><span class="sxs-lookup"><span data-stu-id="a1e7c-258">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="a1e7c-259">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="a1e7c-259">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="a1e7c-260">パスワードを記録しておきます。これは後で Azure Key Vault に証明書をインポートするために使用します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-260">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="a1e7c-261">Azure Key Vault 証明書の詳細については、[Azure Key Vault のCertificate\(次へ: 証明書\)](/azure/key-vault/certificates/) を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-261">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="a1e7c-262">新しい Azure キー コンテナーを作成するか、Azure サブスクリプションの既存のキー コンテナーを使用します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-262">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="a1e7c-263">キー コンテナーの **[証明書]** 領域で、PFX サイト証明書をインポートします。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-263">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="a1e7c-264">後でアプリの構成に使用するので、証明書のサムプリントを記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-264">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="a1e7c-265">Azure Key Vault で、Identity Server のトークン署名用に新しい自己署名証明書を生成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-265">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="a1e7c-266">証明書の **証明書名** と **サブジェクト** を指定します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-266">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="a1e7c-267">**サブジェクト** は `CN={COMMON NAME}` と指定します。`{COMMON NAME}` プレースホルダーは証明書の共通名です。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-267">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="a1e7c-268">共通名には、任意の英数字を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-268">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="a1e7c-269">たとえば、`CN=IdentityServerSigning` は証明書の有効な **サブジェクト** です。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-269">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="a1e7c-270">**[ポリシーの詳細構成]** は既定の設定を使用します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-270">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="a1e7c-271">後でアプリの構成に使用するので、証明書のサムプリントを記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-271">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="a1e7c-272">Azure portal で Azure App Service に移動し、次の構成を使用して新しいアプリ サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-272">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="a1e7c-273">**[発行]** は、`Code` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-273">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="a1e7c-274">**[ランタイム スタック]** は、アプリのランタイムに設定します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-274">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="a1e7c-275">**[SKU とサイズ]** については、App Service のレベルが `Basic B1` 以上であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-275">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="a1e7c-276">App Service でカスタム ドメインを使用するには、`Basic B1` 以上のサービス レベルが必要です。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-276">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="a1e7c-277">Azure によって App Service が作成された後、アプリの **[構成]** を開き、前に記録した証明書のサムプリントを指定して新しいアプリケーション設定を追加します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-277">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="a1e7c-278">アプリ設定キーは `WEBSITE_LOAD_CERTIFICATES` です。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-278">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="a1e7c-279">次の例に示すように、アプリの設定値で証明書のサムプリントを区切るにはコンマを使用します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-279">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="a1e7c-280">キー: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="a1e7c-280">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="a1e7c-281">値: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="a1e7c-281">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="a1e7c-282">Azure portal で、アプリの設定の保存は 2 段階のプロセスです。`WEBSITE_LOAD_CERTIFICATES` のキーと値の設定を保存した後、ブレードの上部にある **[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-282">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="a1e7c-283">アプリの **[TLS/SSL の設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-283">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="a1e7c-284">**[秘密キー証明書 (.pfx)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-284">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="a1e7c-285">**[Key Vault 証明書のインポート]** プロセスを 2 回使用して、HTTPS 通信用のサイトの証明書と、サイトの自己署名された Identity Server トークン署名証明書の両方をインポートします。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-285">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="a1e7c-286">**[カスタム ドメイン]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-286">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="a1e7c-287">ドメイン レジストラーの Web サイトで、**IP アドレス** と **カスタム ドメイン検証 ID** を使用して、ドメインを構成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-287">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="a1e7c-288">一般的なドメイン構成には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-288">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="a1e7c-289">**ホスト** が `@` で、値が Azure portal の IP アドレスである **A レコード**。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-289">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="a1e7c-290">**ホスト** が `asuid` で、値が Azure によって生成されて Azure portal によって提供される検証 ID である **TXT レコード**。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-290">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="a1e7c-291">ドメイン レジストラーの Web サイトで変更を正しく保存したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-291">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="a1e7c-292">レジストラーの Web サイトによっては、ドメイン レコードを保存するために 2 段階のプロセスが必要な場合があります。1 つ以上のレコードを個別に保存した後、別のボタンを使用してドメインの登録を更新します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-292">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="a1e7c-293">Azure portal の **[カスタム ドメイン]** ブレードに戻ります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-293">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="a1e7c-294">**[カスタム ドメインの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-294">Select **Add custom domain**.</span></span> <span data-ttu-id="a1e7c-295">**[A レコード]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-295">Select the **A Record** option.</span></span> <span data-ttu-id="a1e7c-296">ドメインを指定し、 **[検証]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-296">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="a1e7c-297">ドメイン レコードが正しく、インターネットを通して反映されている場合、ポータルで **[カスタム ドメインの追加]** ボタンを選択できます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-297">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="a1e7c-298">ドメイン登録の変更がインターネットのドメイン ネーム サーバー (DNS) 全体に反映されるまで、ドメイン レジストラーによって処理されてから数日かかることがあります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-298">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="a1e7c-299">ドメイン レコードが 3 営業日以内に更新されない場合は、レコードが正しく設定されていることをドメイン レジストラーで確認し、カスタマー サポートに問い合わせてください。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-299">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="a1e7c-300">**[カスタム ドメイン]** ブレードで、ドメインの **[SSL 状態]** は `Not Secure` とマークされます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-300">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="a1e7c-301">**[バインドの追加]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-301">Select the **Add binding** link.</span></span> <span data-ttu-id="a1e7c-302">キー コンテナーからカスタム ドメイン バインド用のサイトの HTTPS 証明書を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-302">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="a1e7c-303">Visual Studio で、*Server* プロジェクトのアプリ設定ファイル (`appsettings.json` または `appsettings.Production.json`) を開きます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-303">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="a1e7c-304">Identity Server の構成で、次の `Key` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-304">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="a1e7c-305">`Name` キーに対しては、自己署名証明書の **サブジェクト** を指定します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-305">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="a1e7c-306">次の例の場合、キー コンテナーで割り当てられている証明書の共通名は `IdentityServerSigning` であり、それにより `CN=IdentityServerSigning` という **サブジェクト** が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-306">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="a1e7c-307">Visual Studio で、*Server* プロジェクトに対する Azure App Service の "[発行プロファイル](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)" を作成します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-307">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="a1e7c-308">メニュー バーから次のように選択します。 **[ビルド]**  >  **[発行]**  >  **[新規]**  >  **[Azure]**  >  **[Azure App Service]** (Windows または Linux)。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-308">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="a1e7c-309">Visual Studio が Azure サブスクリプションに接続されたら、 **[リソースの種類]** で Azure リソースの **[ビュー]** を設定できます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-309">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="a1e7c-310">**[Web アプリ]** の一覧内を移動し、アプリの App Service を見つけて選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-310">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="a1e7c-311">**[完了]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-311">Select **Finish**.</span></span>
1. <span data-ttu-id="a1e7c-312">Visual Studio が **[発行]** ウィンドウに戻ると、キー コンテナーと SQL Server データベース サービスの依存関係が自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-312">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="a1e7c-313">Key Vault サービスの既定の設定で構成を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-313">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="a1e7c-314">テストの目的で、既定では Blazor テンプレートによって構成されるアプリのローカル [SQLite](https://www.sqlite.org/index.html) データベースを、追加の構成を行わずにアプリでデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-314">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="a1e7c-315">運用環境で Identity Server 用に別のデータベースを構成する方法については、この記事では説明しません。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-315">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="a1e7c-316">詳細については、次のドキュメント セットのデータベース リソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-316">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="a1e7c-317">App Service</span><span class="sxs-lookup"><span data-stu-id="a1e7c-317">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="a1e7c-318">Identity サーバー</span><span class="sxs-lookup"><span data-stu-id="a1e7c-318">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="a1e7c-319">ウィンドウの上部にある配置プロファイル名の下にある **[編集]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-319">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="a1e7c-320">デプロイ先の URL を、サイトのカスタム ドメイン URL に変更します (例: `https://www.contoso.com`)。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-320">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="a1e7c-321">設定を保存します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-321">Save the settings.</span></span>
1. <span data-ttu-id="a1e7c-322">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="a1e7c-322">Publish the app.</span></span> <span data-ttu-id="a1e7c-323">Visual Studio によってブラウザー ウィンドウが開かれ、カスタム ドメインのサイトが要求されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-323">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="a1e7c-324">Azure のドキュメントには、App Service の TLS バインドでの Azure サービスとカスタム ドメインの使用に関する詳細が含まれています。これには、A レコードの代わりに CNAME レコードを使用する方法の情報も含まれます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-324">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="a1e7c-325">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-325">For more information, see the following resources:</span></span>

* [<span data-ttu-id="a1e7c-326">App Service のドキュメント</span><span class="sxs-lookup"><span data-stu-id="a1e7c-326">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="a1e7c-327">チュートリアル:既存のカスタム DNS 名を Azure App Service にマップする</span><span class="sxs-lookup"><span data-stu-id="a1e7c-327">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="a1e7c-328">Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="a1e7c-328">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="a1e7c-329">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a1e7c-329">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="a1e7c-330">Azure portal でアプリ、アプリの構成、または Azure サービスを変更した後は、アプリのテストを実行するたびに、新しいプライベートまたはシークレットのブラウザー ウィンドウを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-330">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="a1e7c-331">サイトの構成が正しい場合でも、前回のテスト実行から残っている cookie により、サイトをテストするときに認証または承認が失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-331">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="a1e7c-332">テストを実行するたびに新しいプライベートまたはシークレットのブラウザー ウィンドウを開くように Visual Studio を構成する方法の詳細については、「[Cookie とサイト データ](#cookies-and-site-data)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-332">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="a1e7c-333">Azure portal で App Service の構成を変更すると、通常、更新は短時間で有効になりますが、すぐにではありません。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-333">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="a1e7c-334">場合によっては、構成の変更を有効にするために App Service が再起動されるまでしばらく待つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-334">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="a1e7c-335">証明書の読み込みに関する問題のトラブルシューティングを行う場合は、Azure portal の [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-335">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="a1e7c-336">このコマンドにより、アプリで `My` > `CurrentUser` 証明書ストアからアクセスできる証明書の一覧が提供されます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-336">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="a1e7c-337">出力には、アプリをデバッグするときに役立つ証明書のサブジェクトとサムプリントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-337">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a1e7c-338">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a1e7c-338">Additional resources</span></span>

* [<span data-ttu-id="a1e7c-339">Azure App Service にデプロイする</span><span class="sxs-lookup"><span data-stu-id="a1e7c-339">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="a1e7c-340">Key Vault から証明書をインポートする (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="a1e7c-340">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a1e7c-341">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="a1e7c-341">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="a1e7c-342"><xref:host-and-deploy/proxy-load-balancer>: 次のガイダンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-342"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="a1e7c-343">転送されたヘッダー ミドルウェアを使用した、プロキシ サーバーと内部ネットワークの間での HTTPS スキーム情報の保持。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-343">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="a1e7c-344">追加のシナリオとユース ケース (手動スキーム構成を含む)、正しい要求ルーティングのための要求パスの変更、Linux および IIS 以外のリバース プロキシのための要求スキームの転送。</span><span class="sxs-lookup"><span data-stu-id="a1e7c-344">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>

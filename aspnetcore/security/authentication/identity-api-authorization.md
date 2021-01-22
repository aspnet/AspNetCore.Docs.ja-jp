---
title: ASP.NET Core でのシングルページアプリの認証の概要
author: javiercn
description: IdentityASP.NET Core アプリ内でホストされるシングルページアプリで使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658665"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="faf3f-103">SPAs の認証と承認</span><span class="sxs-lookup"><span data-stu-id="faf3f-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="faf3f-104">ASP.NET Core 3.1 以降のテンプレートでは、API 承認のサポートを使用して、シングルページアプリ (spa) で認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="faf3f-105">ASP.NET Core Identity認証と保存のために、ユーザーを[ Identity サーバー](https://identityserver.io/)と組み合わせて OpenID connect を実装します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="faf3f-106">認証パラメーターが、 **Web アプリケーション (モデルビューコントローラー)** (MVC) および **web アプリケーション**(ページ) プロジェクトテンプレートの認証パラメーターに似た **角度** で、**応答** するプロジェクトテンプレートに追加されました Razor 。</span><span class="sxs-lookup"><span data-stu-id="faf3f-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="faf3f-107">許可されるパラメーター値は、 **None** および **個人** です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="faf3f-108">**React.js と Redux** プロジェクトテンプレートでは、現時点では認証パラメーターがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="faf3f-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="faf3f-109">API authorization サポートを使用してアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="faf3f-109">Create an app with API authorization support</span></span>

<span data-ttu-id="faf3f-110">ユーザーの認証と承認は、両方の角度で使用でき、SPAs として対応します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="faf3f-111">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="faf3f-112">**角度**:</span><span class="sxs-lookup"><span data-stu-id="faf3f-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="faf3f-113">**反応**:</span><span class="sxs-lookup"><span data-stu-id="faf3f-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="faf3f-114">前のコマンドは、 *ClientApp* ディレクトリに SPA を含む ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="faf3f-115">アプリの ASP.NET Core コンポーネントの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="faf3f-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="faf3f-116">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="faf3f-117">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="faf3f-117">Startup class</span></span>

<span data-ttu-id="faf3f-118">次のコード例は、AspNetCore に依存して [い Identity ます。サーバー](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="faf3f-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="faf3f-119">この例では、および拡張メソッドを使用して、API の認証と承認を構成し <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="faf3f-120">認証を使用して、応答または角速度の SPA プロジェクトテンプレートを使用するプロジェクトには、このパッケージへの参照が含まれます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="faf3f-121">クラスには `Startup` 、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="faf3f-122">メソッド内 `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="faf3f-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="faf3f-123">Identity 既定の UI では、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="faf3f-124">Identityサーバーに追加のヘルパーメソッドを使用して、 `AddApiAuthorization` サーバー上に既定の ASP.NET Core 規則を設定する Identity 。</span><span class="sxs-lookup"><span data-stu-id="faf3f-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="faf3f-125">IdentityServer によって生成された JWT トークンを検証するようにアプリを構成する `AddIdentityServerJwt` ヘルパー メソッドが追加された Authentication:</span><span class="sxs-lookup"><span data-stu-id="faf3f-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="faf3f-126">メソッド内 `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="faf3f-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="faf3f-127">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="faf3f-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="faf3f-128">IdentityOpenID connect エンドポイントを公開するサーバーミドルウェアは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="faf3f-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="faf3f-129">Azure App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="faf3f-129">Azure App Service on Linux</span></span>

<span data-ttu-id="faf3f-130">Linux での Azure App Service デプロイについては、次のように発行者を明示的に指定し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-130">For Azure App Service deployments on Linux, specify the issuer explicitly in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

<span data-ttu-id="faf3f-131">前のコードでは、 `{AUTHORITY}` <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> OpenID connect 呼び出しを行うときに、プレースホルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-131">In the preceding code, the `{AUTHORITY}` placeholder is the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> to use when making OpenID Connect calls.</span></span>

<span data-ttu-id="faf3f-132">例:</span><span class="sxs-lookup"><span data-stu-id="faf3f-132">Example:</span></span>

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a><span data-ttu-id="faf3f-133">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="faf3f-133">AddApiAuthorization</span></span>

<span data-ttu-id="faf3f-134">このヘルパーメソッド Identity は、サポートされている構成を使用するようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-134">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="faf3f-135">IdentityServer は、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="faf3f-135">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="faf3f-136">同時に、最も一般的なシナリオでは不必要な複雑さを公開します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-136">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="faf3f-137">そのため、適切な出発点と見なされる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-137">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="faf3f-138">認証を変更する必要がある場合でも、サーバーの能力を最大限に活用して、 Identity 必要に応じて認証をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-138">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="faf3f-139">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="faf3f-139">AddIdentityServerJwt</span></span>

<span data-ttu-id="faf3f-140">このヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-140">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="faf3f-141">このポリシーは、 Identity Identity URL スペース "/" のサブパスにルーティングされるすべての要求を処理できるように構成されてい Identity ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-141">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="faf3f-142">それ以外のすべての要求は、`JwtBearerHandler` で処理されます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-142">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="faf3f-143">さらに、このメソッドは `<<ApplicationName>>API` API リソースを Identity の既定のスコープと共にサーバーに登録 `<<ApplicationName>>API` し、アプリケーションのサーバーによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-143">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="faf3f-144">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="faf3f-144">WeatherForecastController</span></span>

<span data-ttu-id="faf3f-145">*Controllers\WeatherForecastController.cs* ファイルで、属性がクラスに適用されていることを確認します。これは、 `[Authorize]` リソースにアクセスするための既定のポリシーに基づいてユーザーを承認する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-145">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="faf3f-146">既定の承認ポリシーは、前に説明したポリシースキームによって設定される既定の認証スキームを使用するように構成され `AddIdentityServerJwt` 、 `JwtBearerHandler` このようなヘルパーメソッドによって構成されたは、アプリに対する要求の既定のハンドラーになります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-146">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="faf3f-147">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="faf3f-147">ApplicationDbContext</span></span>

<span data-ttu-id="faf3f-148">*Data\ApplicationDbContext.cs* ファイルで `DbContext` は、が拡張し Identity た例外 `ApiAuthorizationDbContext` (から派生したクラス) を使用して、 `IdentityDbContext` サーバーのスキーマを含めるという点に注意して Identity ください。</span><span class="sxs-lookup"><span data-stu-id="faf3f-148">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="faf3f-149">データベーススキーマを完全に制御するには、使用可能なクラスの1つを継承 Identity `DbContext` し、メソッドでを呼び出してスキーマを含めるようにコンテキストを構成し Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-149">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="faf3f-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="faf3f-150">OidcConfigurationController</span></span>

<span data-ttu-id="faf3f-151">*Controllers\OidcConfigurationController.cs* ファイルで、クライアントが使用する必要のある oidc パラメーターを提供するためにプロビジョニングされたエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-151">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="faf3f-152">*appsettings.json* プロジェクトルートのファイルには、 `IdentityServer` 構成されたクライアントの一覧を説明する新しいセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-152">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="faf3f-153">次の例には、1 つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-153">In the following example, there's a single client.</span></span> <span data-ttu-id="faf3f-154">クライアント名はアプリケーション名に対応し、規則によって OAuth の `ClientId` パラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="faf3f-155">構成対象のアプリの種類は、プロファイルによって示されています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="faf3f-156">サーバーの構成プロセスを簡略化する規則を実現するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-156">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="faf3f-157">「 [アプリケーションプロファイル](#application-profiles) 」セクションで説明されているように、使用可能なプロファイルがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-157">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="faf3f-158">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="faf3f-158">appsettings.Development.json</span></span>

<span data-ttu-id="faf3f-159">プロジェクトルートの *appsettings.Development.js* のファイルには、 `IdentityServer` トークンの署名に使用されるキーについて説明するセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-159">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="faf3f-160">運用環境にデプロイする場合は、「 [運用環境にデプロイする](#deploy-to-production) 」セクションで説明されているように、アプリと共にキーをプロビジョニングしてデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-160">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="faf3f-161">角度アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="faf3f-161">General description of the Angular app</span></span>

<span data-ttu-id="faf3f-162">角度テンプレートでの認証と API 承認のサポートは、独自の角度モジュールの *Clientapp-authorization* ディレクトリに存在します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-162">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="faf3f-163">モジュールは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-163">The module is composed of the following elements:</span></span>

* <span data-ttu-id="faf3f-164">3個のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="faf3f-164">3 components:</span></span>
  * <span data-ttu-id="faf3f-165">*login. component. ts*: アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-165">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="faf3f-166">*logout*: アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-166">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="faf3f-167">*login-menu. component. ts*: 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="faf3f-167">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="faf3f-168">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-168">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="faf3f-169">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="faf3f-169">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="faf3f-170">ルートに追加できるルートガード `AuthorizeGuard` 。ルートにアクセスする前にユーザーを認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-170">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="faf3f-171">`AuthorizeInterceptor`ユーザーが認証されるときに、API を対象とする発信 http 要求にアクセストークンを関連付ける http インターセプター。</span><span class="sxs-lookup"><span data-stu-id="faf3f-171">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="faf3f-172">`AuthorizeService`認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報を、使用するアプリの残りの部分に公開するサービス。</span><span class="sxs-lookup"><span data-stu-id="faf3f-172">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="faf3f-173">アプリの認証部分に関連付けられているルートを定義する角度モジュール。</span><span class="sxs-lookup"><span data-stu-id="faf3f-173">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="faf3f-174">ログインメニューコンポーネント、インターセプター、ガード、およびアプリの残りの部分から使用するためのサービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-174">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="faf3f-175">反応アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="faf3f-175">General description of the React app</span></span>

<span data-ttu-id="faf3f-176">応答テンプレートでの認証と API 承認のサポートは、 *ClientApp\src\components\api-authorization* ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-176">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="faf3f-177">これは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-177">It's composed of the following elements:</span></span>

* <span data-ttu-id="faf3f-178">4つのコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="faf3f-178">4 components:</span></span>
  * <span data-ttu-id="faf3f-179">*Login.js*: アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-179">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="faf3f-180">*Logout.js*: アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-180">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="faf3f-181">*LoginMenu.js*: 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="faf3f-181">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="faf3f-182">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-182">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="faf3f-183">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="faf3f-183">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="faf3f-184">*AuthorizeRoute.js*: パラメーターに示されているコンポーネントを表示する前に、ユーザーを認証する必要があるルートコンポーネント。 `Component`</span><span class="sxs-lookup"><span data-stu-id="faf3f-184">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="faf3f-185">`authService` `AuthorizeService` 認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開する、クラスのエクスポートされたインスタンス。</span><span class="sxs-lookup"><span data-stu-id="faf3f-185">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="faf3f-186">これで、ソリューションの主要なコンポーネントを確認できました。次は、アプリの個々のシナリオについて詳しく見ていきましょう。</span><span class="sxs-lookup"><span data-stu-id="faf3f-186">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="faf3f-187">新しい API での承認が必要</span><span class="sxs-lookup"><span data-stu-id="faf3f-187">Require authorization on a new API</span></span>

<span data-ttu-id="faf3f-188">既定では、システムは新しい Api の承認を要求するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-188">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="faf3f-189">これを行うには、新しいコントローラーを作成し、コントローラー `[Authorize]` クラスまたはコントローラー内の任意のアクションに属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-189">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="faf3f-190">API 認証ハンドラーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="faf3f-190">Customize the API authentication handler</span></span>

<span data-ttu-id="faf3f-191">API の JWT ハンドラーの構成をカスタマイズするには、そのインスタンスを構成し <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-191">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="faf3f-192">API の JWT ハンドラーは、を使用して認証プロセスを制御できるようにするイベントを発生させ `JwtBearerEvents` ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-192">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="faf3f-193">は、API 承認のサポートを提供するために、 `AddIdentityServerJwt` 独自のイベントハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-193">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="faf3f-194">イベントの処理をカスタマイズするには、必要に応じて追加のロジックを使用して既存のイベントハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="faf3f-194">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="faf3f-195">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-195">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="faf3f-196">前のコードでは、 `OnTokenValidated` イベントハンドラーはカスタム実装に置き換えられています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-196">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="faf3f-197">この実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="faf3f-197">This implementation:</span></span>

1. <span data-ttu-id="faf3f-198">API 承認サポートによって提供される元の実装を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-198">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="faf3f-199">独自のカスタムロジックを実行します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-199">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="faf3f-200">クライアント側のルートを保護する (角度)</span><span class="sxs-lookup"><span data-stu-id="faf3f-200">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="faf3f-201">クライアント側ルートの保護は、ルートを構成するときに実行するガードのリストに承認ガードを追加することによって行われます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-201">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="faf3f-202">例として、主要なアプリの `fetch-data` 角度モジュール内でルートがどのように構成されているかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-202">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="faf3f-203">ルートを保護しても実際のエンドポイントが保護されないことに注意してください (まだ属性が適用されている必要があり `[Authorize]` ます) が、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="faf3f-203">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="faf3f-204">API 要求の認証 (角度)</span><span class="sxs-lookup"><span data-stu-id="faf3f-204">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="faf3f-205">アプリと共にホストされる Api に対する要求の認証は、アプリによって定義された HTTP クライアントインターセプターを使用することによって自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-205">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="faf3f-206">クライアント側のルートを保護する (応答)</span><span class="sxs-lookup"><span data-stu-id="faf3f-206">Protect a client-side route (React)</span></span>

<span data-ttu-id="faf3f-207">プレーンコンポーネントの代わりにコンポーネントを使用して、クライアント側のルートを保護し `AuthorizeRoute` `Route` ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-207">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="faf3f-208">たとえば、 `fetch-data` コンポーネント内でルートがどのように構成されているかに注目して `App` ください。</span><span class="sxs-lookup"><span data-stu-id="faf3f-208">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="faf3f-209">ルートの保護:</span><span class="sxs-lookup"><span data-stu-id="faf3f-209">Protecting a route:</span></span>

* <span data-ttu-id="faf3f-210">は実際のエンドポイントを保護しません (まだ `[Authorize]` 属性が適用されている必要があります)。</span><span class="sxs-lookup"><span data-stu-id="faf3f-210">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="faf3f-211">は、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="faf3f-211">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="faf3f-212">API 要求の認証 (応答)</span><span class="sxs-lookup"><span data-stu-id="faf3f-212">Authenticate API requests (React)</span></span>

<span data-ttu-id="faf3f-213">を使用して要求を認証するには `authService` 、まずからインスタンスをインポートし `AuthorizeService` ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-213">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="faf3f-214">アクセストークンはから取得され、 `authService` 次に示すように要求にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-214">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="faf3f-215">コンポーネントの反応では、通常、この作業は `componentDidMount` ライフサイクルメソッドで実行されるか、一部のユーザー操作の結果として行われます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-215">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="faf3f-216">コンポーネントに authService をインポートする</span><span class="sxs-lookup"><span data-stu-id="faf3f-216">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="faf3f-217">アクセストークンを取得して応答にアタッチする</span><span class="sxs-lookup"><span data-stu-id="faf3f-217">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="faf3f-218">運用環境への配置</span><span class="sxs-lookup"><span data-stu-id="faf3f-218">Deploy to production</span></span>

<span data-ttu-id="faf3f-219">アプリを運用環境にデプロイするには、次のリソースをプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-219">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="faf3f-220">Identityユーザーアカウントとサーバー権限を格納するデータベース Identity 。</span><span class="sxs-lookup"><span data-stu-id="faf3f-220">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="faf3f-221">トークンの署名に使用する実稼働証明書。</span><span class="sxs-lookup"><span data-stu-id="faf3f-221">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="faf3f-222">この証明書には特定の要件はありません。自己署名証明書、または CA 証明機関を通じてプロビジョニングされた証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-222">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="faf3f-223">PowerShell や OpenSSL などの標準ツールを使用して生成できます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-223">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="faf3f-224">ターゲットコンピューターの証明書ストアにインストールすることも、強力なパスワードを使用して *.pfx* ファイルとして展開することもできます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-224">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="faf3f-225">例: Azure 以外の web ホスティングプロバイダーへのデプロイ</span><span class="sxs-lookup"><span data-stu-id="faf3f-225">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="faf3f-226">Web ホスティングパネルで、証明書を作成または読み込みます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-226">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="faf3f-227">次に、アプリの *appsettings.json* ファイルで、セクションを変更し `IdentityServer` てキーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-227">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="faf3f-228">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-228">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="faf3f-229">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="faf3f-229">In the preceding example:</span></span>

* <span data-ttu-id="faf3f-230">`StoreName` 証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-230">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="faf3f-231">この例では、web ホスティングストアを参照しています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-231">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="faf3f-232">`StoreLocation` 証明書の読み込み元 ( `CurrentUser` この場合は) を表します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-232">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="faf3f-233">`Name` 証明書の識別サブジェクトに対応します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-233">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="faf3f-234">例: Azure App Service に配置する</span><span class="sxs-lookup"><span data-stu-id="faf3f-234">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="faf3f-235">このセクションでは、証明書ストアに格納されている証明書を使用して Azure App Service するためのアプリの展開について説明します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-235">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="faf3f-236">証明書ストアから証明書を読み込むようにアプリを変更するには、後の手順で Azure portal でアプリを構成するときに、Standard レベルのサービスプラン以上が必要です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-236">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="faf3f-237">アプリのファイルで *appsettings.json* 、セクションを変更し `IdentityServer` て、キーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-237">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="faf3f-238">ストア名は、証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-238">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="faf3f-239">この場合、個人ユーザーストアを指します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-239">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="faf3f-240">ストアの場所は、証明書を読み込む場所 ( `CurrentUser` または) を表し `LocalMachine` ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-240">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="faf3f-241">Certificate の name プロパティは、証明書の識別サブジェクトに対応します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-241">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="faf3f-242">Azure App Service にデプロイするには、「 [azure へのアプリのデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)」の手順に従って、必要な azure リソースを作成し、アプリを運用環境にデプロイする方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-242">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="faf3f-243">前述の手順に従うと、アプリは Azure にデプロイされますが、まだ機能していません。</span><span class="sxs-lookup"><span data-stu-id="faf3f-243">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="faf3f-244">アプリによって使用される証明書は、Azure portal で構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-244">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="faf3f-245">証明書のサムプリントを見つけて、「 [証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)」で説明されている手順に従います。</span><span class="sxs-lookup"><span data-stu-id="faf3f-245">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="faf3f-246">これらの手順では SSL について説明していますが、Azure portal には、アプリで使用するプロビジョニング済みの証明書をアップロードできる **プライベート証明書** セクションがあります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-246">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="faf3f-247">Azure portal でアプリとアプリの設定を構成した後、ポータルでアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-247">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="faf3f-248">その他の構成オプション</span><span class="sxs-lookup"><span data-stu-id="faf3f-248">Other configuration options</span></span>

<span data-ttu-id="faf3f-249">API 承認のサポートは、 Identity 一連の規則、既定値、および拡張機能を使用してサーバー上に構築され、SPAs のエクスペリエンスが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="faf3f-249">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="faf3f-250">言うまで Identity もありませんが、ASP.NET Core 統合によってシナリオがカバーされていない場合は、サーバーの全機能をバックグラウンドで利用できます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-250">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="faf3f-251">ASP.NET Core サポートは、すべてのアプリが組織によって作成および展開される "ファーストパーティ" アプリに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-251">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="faf3f-252">そのため、同意やフェデレーションなどのサポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="faf3f-252">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="faf3f-253">これらのシナリオでは、Server を使用 Identity して、そのドキュメントに従ってください。</span><span class="sxs-lookup"><span data-stu-id="faf3f-253">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="faf3f-254">アプリケーションプロファイル</span><span class="sxs-lookup"><span data-stu-id="faf3f-254">Application profiles</span></span>

<span data-ttu-id="faf3f-255">アプリケーションプロファイルは、そのパラメーターをさらに定義するアプリの事前定義された構成です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-255">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="faf3f-256">現時点では、次のプロファイルがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-256">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="faf3f-257">`IdentityServerSPA`: サーバーと共にホストされる SPA を Identity 1 つの単位として表します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-257">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="faf3f-258">の `redirect_uri` 既定値は `/authentication/login-callback` です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-258">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="faf3f-259">の `post_logout_redirect_uri` 既定値は `/authentication/logout-callback` です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-259">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="faf3f-260">スコープのセットには、 `openid` `profile` アプリ内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="faf3f-261">許可される OIDC 応答の種類のセットは、 `id_token token` それぞれ個別に ( `id_token` 、 `token` ) です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="faf3f-262">許可される応答モードは `fragment` です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="faf3f-263">`SPA`: サーバーでホストされていない SPA を表し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-263">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="faf3f-264">スコープのセットには、 `openid` `profile` アプリ内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-264">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="faf3f-265">許可される OIDC 応答の種類のセットは、 `id_token token` それぞれ個別に ( `id_token` 、 `token` ) です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-265">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="faf3f-266">許可される応答モードは `fragment` です。</span><span class="sxs-lookup"><span data-stu-id="faf3f-266">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="faf3f-267">`IdentityServerJwt`: サーバーと共にホストされる API を表し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-267">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="faf3f-268">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-268">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="faf3f-269">`API`: サーバーでホストされていない API を表し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-269">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="faf3f-270">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="faf3f-270">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="faf3f-271">AppSettings を使用した構成</span><span class="sxs-lookup"><span data-stu-id="faf3f-271">Configuration through AppSettings</span></span>

<span data-ttu-id="faf3f-272">構成システムを使用して、またはの一覧にアプリを追加して、アプリを構成し `Clients` `Resources` ます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-272">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="faf3f-273">`redirect_uri` `post_logout_redirect_uri` 次の例に示すように、各クライアントのおよびプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="faf3f-273">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="faf3f-274">リソースを構成するときは、次に示すように、リソースのスコープを構成できます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-274">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="faf3f-275">コードを使用した構成</span><span class="sxs-lookup"><span data-stu-id="faf3f-275">Configuration through code</span></span>

<span data-ttu-id="faf3f-276">また、 `AddApiAuthorization` オプションを構成するためのアクションを実行するのオーバーロードを使用して、コードを使用してクライアントとリソースを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="faf3f-276">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="faf3f-277">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="faf3f-277">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>

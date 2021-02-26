---
title: 認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: 認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280882"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="0e153-103">認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="0e153-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="0e153-104">*Azure Active Directory (AAD) および Azure Active Directory B2C (AAD B2C) については、このトピックのガイダンスに従わず、この目次ノードの AAD および AAD B2C のトピックを参照してください。*</span><span class="sxs-lookup"><span data-stu-id="0e153-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="0e153-105">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ライブラリを使用する [スタンドアロン Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成するには、選択したツールのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="0e153-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span> <span data-ttu-id="0e153-106">認証のサポートを追加する場合は、アプリの設定と構成に関するガイダンスについて、この記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e153-106">If adding support for authentication, see the following sections of this article for guidance on setting up and configuring the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0e153-107">Identity プロバイダー (IP) では [OpenID Connect (OIDC)](https://openid.net/connect/) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e153-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="0e153-108">たとえば、Facebook の IP は OIDC に準拠しているプロバイダーではないため、このトピックのガイダンスは Facebook IP では機能しません。</span><span class="sxs-lookup"><span data-stu-id="0e153-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="0e153-109">詳細については、「<xref:blazor/security/webassembly/index#authentication-library>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e153-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e153-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e153-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e153-111">認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="0e153-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="0e153-112">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor WebAssembly アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e153-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="0e153-113">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するには、 **[アプリ内のストア ユーザー アカウント]** オプションを使用して、 **[個人のユーザー アカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e153-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="0e153-114">この選択によって認証のサポートが追加されます。ユーザーがデータベースに保存されることはありません。</span><span class="sxs-lookup"><span data-stu-id="0e153-114">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="0e153-115">この記事の以降のセクションで詳細について説明します。</span><span class="sxs-lookup"><span data-stu-id="0e153-115">The following sections of this article provide further details.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="0e153-116">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0e153-116">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="0e153-117">空のフォルダーに、認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e153-117">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="0e153-118">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するには、`-au|--auth` オプションを使用して `Individual` 認証メカニズムを指定します。</span><span class="sxs-lookup"><span data-stu-id="0e153-118">Specify the `Individual` authentication mechanism with the `-au|--auth` option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="0e153-119">この選択によって認証のサポートが追加されます。ユーザーがデータベースに保存されることはありません。</span><span class="sxs-lookup"><span data-stu-id="0e153-119">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="0e153-120">この記事の以降のセクションで詳細について説明します。</span><span class="sxs-lookup"><span data-stu-id="0e153-120">The following sections of this article provide further details.</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="0e153-121">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="0e153-121">Placeholder</span></span>  | <span data-ttu-id="0e153-122">例</span><span class="sxs-lookup"><span data-stu-id="0e153-122">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="0e153-123">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="0e153-123">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="0e153-124">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e153-124">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0e153-125">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0e153-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0e153-126">認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="0e153-126">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="0e153-127">**[新しい Blazor WebAssembly アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e153-127">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="0e153-128">アプリは ASP.NET Core [Identity](xref:security/authentication/identity) を使用するように作成されます。これによりユーザーがデータベースに保存されることはありません。</span><span class="sxs-lookup"><span data-stu-id="0e153-128">The app is created to use ASP.NET Core [Identity](xref:security/authentication/identity) and doesn't result in storing users in a database.</span></span> <span data-ttu-id="0e153-129">この記事の以降のセクションで詳細について説明します。</span><span class="sxs-lookup"><span data-stu-id="0e153-129">The following sections of this article provide further details.</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="0e153-130">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="0e153-130">Authentication package</span></span>

<span data-ttu-id="0e153-131">個人のユーザー アカウントを使用するようにアプリを作成すると、そのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="0e153-131">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="0e153-132">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0e153-132">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0e153-133">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="0e153-133">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="0e153-134">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="0e153-134">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="0e153-135">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="0e153-135">Authentication service support</span></span>

<span data-ttu-id="0e153-136">ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-136">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="0e153-137">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-137">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="0e153-138">新しいアプリの場合は、次の構成で `{AUTHORITY}` と `{CLIENT ID}` のプレースホルダーに値を指定します。</span><span class="sxs-lookup"><span data-stu-id="0e153-138">For a new app, provide values for the `{AUTHORITY}` and `{CLIENT ID}` placeholders in the following configuration.</span></span> <span data-ttu-id="0e153-139">アプリの IP での使用に必要なその他の構成値を指定します。</span><span class="sxs-lookup"><span data-stu-id="0e153-139">Provide other configuration values that are required for use with the app's IP.</span></span> <span data-ttu-id="0e153-140">この例は Google 用であり、`PostLogoutRedirectUri`、`RedirectUri`、`ResponseType` が必要です。</span><span class="sxs-lookup"><span data-stu-id="0e153-140">The example is for Google, which requires `PostLogoutRedirectUri`, `RedirectUri`, and `ResponseType`.</span></span> <span data-ttu-id="0e153-141">アプリに認証を追加する場合は、次のコードと構成を、プレースホルダーの値とその他の構成値を指定して、アプリに手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="0e153-141">If adding authentication to an app, manually add the following code and configuration to the app with values for the placeholders and other configuration values.</span></span>

<span data-ttu-id="0e153-142">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0e153-142">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="0e153-143">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-143">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="0e153-144">Google OAuth 2.0 OIDC の例:</span><span class="sxs-lookup"><span data-stu-id="0e153-144">Google OAuth 2.0 OIDC example:</span></span>

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

<span data-ttu-id="0e153-145">リダイレクト URI (`https://localhost:5001/authentication/login-callback`) は、[Google APIs コンソール](https://console.developers.google.com/apis/dashboard)で **[Credentials]\(認証情報\)**  >  **[`{NAME}`]**  >  **[Authorized redirect URIs]\(承認されたリダイレクト URI\)** を使用して登録します。ここで、`{NAME}` は、Google APIs Console の **OAuth 2.0 クライアント ID** アプリの一覧にあるアプリのクライアント名です。</span><span class="sxs-lookup"><span data-stu-id="0e153-145">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="0e153-146">スタンドアロン アプリの認証サポートは、OpenID Connect (OIDC) を使用して提供されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-146">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="0e153-147"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> メソッドでは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0e153-147">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="0e153-148">アプリの構成に必要な値は、OIDC に準拠する IP から取得できます。</span><span class="sxs-lookup"><span data-stu-id="0e153-148">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="0e153-149">アプリを登録するときに値を取得します。これは通常、オンライン ポータルで実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-149">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="0e153-150">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="0e153-150">Access token scopes</span></span>

<span data-ttu-id="0e153-151">Blazor WebAssembly テンプレートでは、`openid` と `profile` の既定のスコープが自動的に構成されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-151">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="0e153-152">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="0e153-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="0e153-153">サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> の既定のトークン スコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e153-153">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>.</span></span> <span data-ttu-id="0e153-154">アプリに認証を追加する場合は、次のコードを手動で追加し、スコープ URI を構成します。</span><span class="sxs-lookup"><span data-stu-id="0e153-154">If adding authentication to an app, manually add the following code and configure the scope URI.</span></span>

<span data-ttu-id="0e153-155">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0e153-155">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="0e153-156">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e153-156">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="0e153-157">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="0e153-157">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="0e153-158">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="0e153-158">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="0e153-159">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="0e153-159">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="0e153-160">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="0e153-160">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="0e153-161">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0e153-161">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="0e153-162">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0e153-162">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="0e153-163">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0e153-163">LoginDisplay component</span></span>

<span data-ttu-id="0e153-164">`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-164">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="0e153-165">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="0e153-165">For authenticated users:</span></span>
  * <span data-ttu-id="0e153-166">現在のユーザー名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-166">Displays the current username.</span></span>
  * <span data-ttu-id="0e153-167">アプリからログアウトするためのボタンが用意されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-167">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="0e153-168">匿名ユーザーの場合は、ログインするオプションが用意されます。</span><span class="sxs-lookup"><span data-stu-id="0e153-168">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a><span data-ttu-id="0e153-169">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0e153-169">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0e153-170">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0e153-170">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="0e153-171">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="0e153-171">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="0e153-172"><xref:host-and-deploy/proxy-load-balancer>: 次のガイダンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e153-172"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="0e153-173">転送されたヘッダー ミドルウェアを使用した、プロキシ サーバーと内部ネットワークの間での HTTPS スキーム情報の保持。</span><span class="sxs-lookup"><span data-stu-id="0e153-173">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="0e153-174">追加のシナリオとユース ケース (手動スキーム構成を含む)、正しい要求ルーティングのための要求パスの変更、Linux および IIS 以外のリバース プロキシのための要求スキームの転送。</span><span class="sxs-lookup"><span data-stu-id="0e153-174">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>

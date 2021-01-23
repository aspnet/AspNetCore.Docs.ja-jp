---
title: ASP.NET Core Blazor WebAssembly をセキュリティで保護する
author: guardrex
description: シングル ページ アプリケーション (SPA) として Blazor WebAssemlby アプリをセキュリティで保護する方法について説明します。
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: 2df938f3ace47472536020f9848e954fc4446f15
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658587"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="e73fd-103">ASP.NET Core Blazor WebAssembly をセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="e73fd-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="e73fd-104">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="e73fd-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="e73fd-105">Blazor WebAssembly アプリは、シングル ページ アプリケーション (SPA) と同じ方法でセキュリティ保護されます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-105">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="e73fd-106">ユーザーを SPA で認証する方法はいくつかありますが、最も一般的で包括的な方法は、[OpenID Connect (OIDC)](https://openid.net/connect/) などの [OAuth 2.0 プロトコル](https://oauth.net/)に基づく実装を使用することです。</span><span class="sxs-lookup"><span data-stu-id="e73fd-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="e73fd-107">認証ライブラリ</span><span class="sxs-lookup"><span data-stu-id="e73fd-107">Authentication library</span></span>

<span data-ttu-id="e73fd-108">Blazor WebAssembly では、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ライブラリを介して OIDC を使用したアプリの認証と承認がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e73fd-108">Blazor WebAssembly supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="e73fd-109">ライブラリには、ASP.NET Core バックエンドに対してシームレスに認証を行うための一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e73fd-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="e73fd-110">ライブラリによって ASP.NET Core Identity が、[Identity Server](https://identityserver.io/) 上に構築された API 認可サポートと統合されます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="e73fd-111">このライブラリは、OpenID Provider (OP) と呼ばれる OIDC をサポートするすべてのサードパーティ Identity プロバイダー (IP) に対して認証できます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="e73fd-112">Blazor WebAssembly の認証のサポートは、基になる認証プロトコルの詳細を処理するために使用される、`oidc-client.js` ライブラリに基づいて構築されています。</span><span class="sxs-lookup"><span data-stu-id="e73fd-112">The authentication support in Blazor WebAssembly is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="e73fd-113">SameSite cookie の使用など、SPA を認証するためのその他のオプションも存在します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="e73fd-114">ただし、Blazor WebAssembly のエンジニアリング設計は、Blazor WebAssembly アプリでの認証に最適なオプションとして OAuth および OIDC にも採用されています。</span><span class="sxs-lookup"><span data-stu-id="e73fd-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="e73fd-115">[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) に基づく[トークンベースの認証](xref:security/anti-request-forgery#token-based-authentication)は、機能とセキュリティ上の理由により、[cookie ベースの認証](xref:security/anti-request-forgery#cookie-based-authentication)に基づいて選択されています。</span><span class="sxs-lookup"><span data-stu-id="e73fd-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="e73fd-116">トークンベースのプロトコルを使用すると、トークンがすべての要求で送信されないため、攻撃対象領域が小さくなります。</span><span class="sxs-lookup"><span data-stu-id="e73fd-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="e73fd-117">サーバー エンドポイントでは、トークンが明示的に送信されるため、[クロスサイト リクエスト フォージェリ (CSRF)](xref:security/anti-request-forgery) に対する保護が必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e73fd-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="e73fd-118">これにより、MVC または Razor ページ アプリと共に、Blazor WebAssembly アプリをホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="e73fd-119">トークンの権限は cookie よりも狭くなります。</span><span class="sxs-lookup"><span data-stu-id="e73fd-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="e73fd-120">たとえば、該当する機能が明示的に実装されていない限り、トークンを使用してユーザー アカウントを管理したり、ユーザーのパスワードを変更したりできません。</span><span class="sxs-lookup"><span data-stu-id="e73fd-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="e73fd-121">トークンの有効期間は短く、既定では 1 時間です。これにより、攻撃時間が制限されます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="e73fd-122">トークンは、いつでも取り消すことができます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="e73fd-123">自己完結型の JWT は、クライアントとサーバーの認証プロセスを保証します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="e73fd-124">たとえば、クライアントには、受信したトークンが正当であり、指定した認証プロセスの一環として出力されたことを検出して検証する手段があります。</span><span class="sxs-lookup"><span data-stu-id="e73fd-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="e73fd-125">サード パーティが認証プロセスの途中でトークンを切り替えようとすると、クライアントは切り替えられたトークンを検出して使用しないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="e73fd-126">OAuth および OIDC を使用したトークンは、アプリが安全であることを確認するために、正しく動作しているユーザー エージェントに依存しません。</span><span class="sxs-lookup"><span data-stu-id="e73fd-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="e73fd-127">OAuth や OIDC などのトークンベースのプロトコルでは、同じセキュリティ特性のセットを使用して、ホストされたアプリとスタンドアロンのアプリの認証と認可を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="e73fd-128">OIDC を使用した認証プロセス</span><span class="sxs-lookup"><span data-stu-id="e73fd-128">Authentication process with OIDC</span></span>

<span data-ttu-id="e73fd-129">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ライブラリには、OIDC を使用した認証と認可を実装するためのいくつかのプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e73fd-129">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="e73fd-130">大まかにいうと、認証は次のようにして行われます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="e73fd-131">匿名ユーザーがログイン ボタンを選択する、または [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性が適用されたページを要求すると、そのユーザーがアプリのログイン ページ (`/authentication/login`) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="e73fd-132">ログイン ページで、認証ライブラリが承認エンドポイントへのリダイレクトを準備します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="e73fd-133">承認エンドポイントは、Blazor WebAssembly アプリの外部にあり、別のオリジンでホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="e73fd-134">エンドポイントは、ユーザーが認証されているかどうかを判断し、応答として 1 つ以上のトークンを発行する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="e73fd-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="e73fd-135">認証ライブラリは、認証応答を受信するためのログイン コールバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="e73fd-136">ユーザーが認証されていない場合、そのユーザーは基になる認証システム (通常は ASP.NET Core Identity) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="e73fd-137">ユーザーが既に認証されている場合、承認エンドポイントは適切なトークンを生成し、ブラウザーをログイン コールバック エンドポイント (`/authentication/login-callback`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="e73fd-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="e73fd-138">Blazor WebAssembly アプリでログイン コールバック エンドポイント (`/authentication/login-callback`) が読み込まれると、認証応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="e73fd-139">認証プロセスが正常に完了した場合、ユーザーは認証され、必要に応じてユーザーが要求した元の保護された URL に戻されます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="e73fd-140">何らかの理由で認証プロセスが失敗した場合、ユーザーはログイン失敗ページ (`/authentication/login-failed`) に送信され、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="e73fd-141">`Authentication` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="e73fd-141">`Authentication` component</span></span>

<span data-ttu-id="e73fd-142">`Authentication` コンポーネント (`Pages/Authentication.razor`) はリモート認証操作を処理し、次のことをアプリに許可します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-142">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="e73fd-143">認証状態のアプリのルートを構成する。</span><span class="sxs-lookup"><span data-stu-id="e73fd-143">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="e73fd-144">認証状態の UI コンテンツを設定する。</span><span class="sxs-lookup"><span data-stu-id="e73fd-144">Set UI content for authentication states.</span></span>
* <span data-ttu-id="e73fd-145">認証状態を管理する。</span><span class="sxs-lookup"><span data-stu-id="e73fd-145">Manage authentication state.</span></span>

<span data-ttu-id="e73fd-146">ユーザーの登録やサインインなどの認証アクションは、Blazor フレームワークの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> コンポーネントに渡されます。ここでは認証操作全体で状態が保持され、制御されます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-146">Authentication actions, such as registering or signing in a user, are passed to the Blazor framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="e73fd-147">使用例を含む詳細については、「<xref:blazor/security/webassembly/additional-scenarios>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e73fd-147">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="e73fd-148">承認</span><span class="sxs-lookup"><span data-stu-id="e73fd-148">Authorization</span></span>

<span data-ttu-id="e73fd-149">Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、承認チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e73fd-149">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="e73fd-150">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="e73fd-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="e73fd-151">**常に、クライアント側アプリからアクセスされるすべての API エンドポイント内のサーバー上で承認チェックを実行します。**</span><span class="sxs-lookup"><span data-stu-id="e73fd-151">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="e73fd-152">アプリ全体での承認を要求する</span><span class="sxs-lookup"><span data-stu-id="e73fd-152">Require authorization for the entire app</span></span>

<span data-ttu-id="e73fd-153">次のいずれかの方法を使用して、[`[Authorize]` 属性](xref:blazor/security/index#authorize-attribute) ([API ドキュメント](xref:System.Web.Mvc.AuthorizeAttribute)) をアプリの各 Razor コンポーネントに適用します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-153">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each Razor component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="e73fd-154">`_Imports.razor` ファイルの [`@attribute`](xref:mvc/views/razor#attribute) ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-154">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="e73fd-155">`Pages` フォルダー内の各 Razor コンポーネントに属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-155">Add the attribute to each Razor component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="e73fd-156"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> を持つポリシーに <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> を設定することはサポートされて **いません**。</span><span class="sxs-lookup"><span data-stu-id="e73fd-156">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="e73fd-157">更新トークン</span><span class="sxs-lookup"><span data-stu-id="e73fd-157">Refresh tokens</span></span>

<span data-ttu-id="e73fd-158">更新トークンは、Blazor WebAssembly アプリのクライアント側でセキュリティで保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="e73fd-158">Refresh tokens can't be secured client-side in Blazor WebAssembly apps.</span></span> <span data-ttu-id="e73fd-159">そのため、更新トークンを直接使用するためにアプリに送信するべきではありません。</span><span class="sxs-lookup"><span data-stu-id="e73fd-159">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="e73fd-160">更新トークンは、ホストされている Blazor WebAssembly ソリューションのサーバー側アプリで、サード パーティの API にアクセスするために保持および使用することができます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-160">Refresh tokens can be maintained and used by the server-side app in a Hosted Blazor WebAssembly solution to access third-party APIs.</span></span> <span data-ttu-id="e73fd-161">詳細については、「<xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e73fd-161">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="e73fd-162">ユーザーに対するクレームを確立する</span><span class="sxs-lookup"><span data-stu-id="e73fd-162">Establish claims for users</span></span>

<span data-ttu-id="e73fd-163">アプリでは、サーバーへの Web API 呼び出しに基づくユーザーへのクレームが必要になることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="e73fd-163">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="e73fd-164">たとえば、クレームは、アプリで[承認を確立する](xref:blazor/security/index#authorization)ためによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-164">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="e73fd-165">このようなシナリオにおいて、アプリでは、サービスにアクセスするためのアクセス トークンを要求し、そのトークンを使用してクレームに対するユーザー データを取得します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-165">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="e73fd-166">例については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e73fd-166">For examples, see the following resources:</span></span>

* [<span data-ttu-id="e73fd-167">その他のシナリオ: ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="e73fd-167">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a><span data-ttu-id="e73fd-168">Identity Server を使用した Azure App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="e73fd-168">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="e73fd-169">Identity Server を使用して Azure App Service on Linux にデプロイするときに、発行者を明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-169">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="e73fd-170">詳細については、「<xref:security/authentication/identity/spa#azure-app-service-on-linux>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e73fd-170">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="implementation-guidance"></a><span data-ttu-id="e73fd-171">実装ガイダンス</span><span class="sxs-lookup"><span data-stu-id="e73fd-171">Implementation guidance</span></span>

<span data-ttu-id="e73fd-172">この「*概要*」の記事では、特定のプロバイダーに対して Blazor WebAssembly アプリのユーザーを認証する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e73fd-172">Articles under this *Overview* provide information on authenticating users in Blazor WebAssembly apps against specific providers.</span></span>

<span data-ttu-id="e73fd-173">スタンドアロン Blazor WebAssembly アプリ:</span><span class="sxs-lookup"><span data-stu-id="e73fd-173">Standalone Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="e73fd-174">OIDC プロバイダーと WebAssembly 認証ライブラリ向けの一般的なガイダンス</span><span class="sxs-lookup"><span data-stu-id="e73fd-174">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="e73fd-175">Microsoft アカウント</span><span class="sxs-lookup"><span data-stu-id="e73fd-175">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="e73fd-176">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="e73fd-176">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="e73fd-177">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="e73fd-177">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="e73fd-178">ホストされている Blazor WebAssembly アプリ:</span><span class="sxs-lookup"><span data-stu-id="e73fd-178">Hosted Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="e73fd-179">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="e73fd-179">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="e73fd-180">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="e73fd-180">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="e73fd-181">Identity サーバー</span><span class="sxs-lookup"><span data-stu-id="e73fd-181">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e73fd-182">構成に関するその他のガイダンスについては、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e73fd-182">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e73fd-183">構成に関するその他のガイダンスについては、「<xref:blazor/security/webassembly/additional-scenarios>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e73fd-183">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e73fd-184">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e73fd-184">Additional resources</span></span>

* <span data-ttu-id="e73fd-185"><xref:host-and-deploy/proxy-load-balancer>: 次のガイダンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e73fd-185"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="e73fd-186">転送されたヘッダー ミドルウェアを使用した、プロキシ サーバーと内部ネットワークの間での HTTPS スキーム情報の保持。</span><span class="sxs-lookup"><span data-stu-id="e73fd-186">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="e73fd-187">追加のシナリオとユース ケース (手動スキーム構成を含む)、正しい要求ルーティングのための要求パスの変更、Linux および IIS 以外のリバース プロキシのための要求スキームの転送。</span><span class="sxs-lookup"><span data-stu-id="e73fd-187">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>

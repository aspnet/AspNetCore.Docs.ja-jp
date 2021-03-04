---
title: ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する
author: rick-anderson
description: 外部プロバイダーから追加の要求とトークンを確立する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2021
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 9c04ca466566e956b5e6dfec8131096c3995bc14
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110145"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="fe5f6-103">ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fe5f6-104">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="fe5f6-105">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="fe5f6-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe5f6-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="fe5f6-107">Prerequisites</span></span>

<span data-ttu-id="fe5f6-108">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="fe5f6-109">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="fe5f6-110">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="fe5f6-111">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="fe5f6-112">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-112">Set the client ID and client secret</span></span>

<span data-ttu-id="fe5f6-113">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="fe5f6-114">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="fe5f6-115">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="fe5f6-116">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="fe5f6-117">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="fe5f6-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="fe5f6-118">Google での認証</span><span class="sxs-lookup"><span data-stu-id="fe5f6-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="fe5f6-119">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="fe5f6-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="fe5f6-120">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="fe5f6-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="fe5f6-121">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="fe5f6-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="fe5f6-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="fe5f6-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="fe5f6-123">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="fe5f6-124">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-124">Establish the authentication scope</span></span>

<span data-ttu-id="fe5f6-125">を指定して、プロバイダーから取得するアクセス許可の一覧を指定し <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="fe5f6-126">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="fe5f6-127">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="fe5f6-127">Provider</span></span>  | <span data-ttu-id="fe5f6-128">Scope</span><span class="sxs-lookup"><span data-stu-id="fe5f6-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="fe5f6-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="fe5f6-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="fe5f6-130">Google</span><span class="sxs-lookup"><span data-stu-id="fe5f6-130">Google</span></span>    | <span data-ttu-id="fe5f6-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="fe5f6-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="fe5f6-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="fe5f6-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="fe5f6-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="fe5f6-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="fe5f6-134">サンプルアプリで `profile` は、がで呼び出されると、Google の、、の各 `email` `openid` スコープがフレームワークによって自動的に追加され <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="fe5f6-135">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="fe5f6-136">次の例で `https://www.googleapis.com/auth/user.birthday.read` は、ユーザーの誕生日を取得するために Google スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="fe5f6-137">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-137">Map user data keys and create claims</span></span>

<span data-ttu-id="fe5f6-138">プロバイダーのオプションで、 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="fe5f6-139">要求の種類の詳細については、「」を参照してください <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="fe5f6-140">このサンプルアプリでは、 `urn:google:locale` `urn:google:picture` `locale` Google ユーザーデータのキーとキーから、ロケール () と画像 () の要求を作成し `picture` ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="fe5f6-141">では、 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) がを使用してアプリにサインイン <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="fe5f6-142">サインインプロセス中に、で <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 使用可能なユーザーデータの要求を格納でき <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="fe5f6-143">サンプルアプリでは、 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*) によって、 `urn:google:locale` `urn:google:picture` `ApplicationUser` 次の要求を含む、サインインしたのロケール () と画像 () の要求が確立 <xref:System.Security.Claims.ClaimTypes.GivenName> されます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="fe5f6-144">既定では、ユーザーの要求は認証に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="fe5f6-145">認証 cookie が大きすぎると、次の理由により、アプリでエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="fe5f6-146">ブラウザーは、 cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="fe5f6-147">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="fe5f6-148">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="fe5f6-149">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="fe5f6-150">認証ミドルウェアのカスタムを使用し <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> て、 Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="fe5f6-151">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="fe5f6-152">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-152">Save the access token</span></span>

<span data-ttu-id="fe5f6-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 承認が成功した後に、アクセストークンと更新トークンをに格納するかどうかを定義し <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="fe5f6-154">`SaveTokens` は `false` 、最終的な認証のサイズを小さくするために、既定でに設定され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="fe5f6-155">サンプルアプリでは、の値をに設定し `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="fe5f6-156">を実行するときに、 `OnPostConfirmationAsync` の外部プロバイダーからアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納 `ApplicationUser` `AuthenticationProperties` します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="fe5f6-157">サンプルアプリでは、アクセストークン `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録されたユーザー) を *Account/externallogin. cshtml. cs* に保存します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> <span data-ttu-id="fe5f6-158">アプリのコンポーネントにトークンを渡す方法の詳細につい Razor Blazor Server ては、「」を参照してください <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> 。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-158">For information on passing tokens to the Razor components of a Blazor Server app, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="fe5f6-159">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="fe5f6-159">How to add additional custom tokens</span></span>

<span data-ttu-id="fe5f6-160">の一部として格納されているカスタムトークンを追加する方法を示すために、 `SaveTokens` サンプルアプリでは、の AuthenticationToken.Name の現在のを使用してを追加し <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> [](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-160">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="fe5f6-161">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="fe5f6-161">Creating and adding claims</span></span>

<span data-ttu-id="fe5f6-162">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-162">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="fe5f6-163">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-163">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="fe5f6-164">ユーザーは、から派生し、抽象メソッドを実装することによって、カスタムアクションを定義でき <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-164">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="fe5f6-165">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-165">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="add-and-update-user-claims"></a><span data-ttu-id="fe5f6-166">ユーザー要求の追加と更新</span><span class="sxs-lookup"><span data-stu-id="fe5f6-166">Add and update user claims</span></span>

<span data-ttu-id="fe5f6-167">要求は、サインイン時ではなく、最初の登録時に外部プロバイダーからユーザーデータベースにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-167">Claims are copied from external providers to the user database on first registration, not on sign in.</span></span> <span data-ttu-id="fe5f6-168">ユーザーがアプリを使用するように登録した後、アプリで追加の要求が有効になっている場合は、ユーザーの [RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) を呼び出して、新しい認証を強制的に生成 cookie します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-168">If additional claims are enabled in an app after a user registers to use the app, call [SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on a user to force the generation of a new authentication cookie.</span></span>

<span data-ttu-id="fe5f6-169">テストユーザーアカウントを使用する開発環境では、単にユーザーアカウントを削除して再作成することができます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-169">In the Development environment working with test user accounts, you can simply delete and recreate the user account.</span></span> <span data-ttu-id="fe5f6-170">実稼働システムでは、アプリに追加された新しい要求をユーザーアカウントにバックすることができます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-170">For production systems, new claims added to the app can be backfilled into user accounts.</span></span> <span data-ttu-id="fe5f6-171">で [ `ExternalLogin` ページ](xref:security/authentication/scaffold-identity) をアプリにスキャフォールディングした後 `Areas/Pages/Identity/Account/Manage` 、ファイル内のに次のコードを追加し `ExternalLoginModel` `ExternalLogin.cshtml.cs` ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-171">After [scaffolding the `ExternalLogin` page](xref:security/authentication/scaffold-identity) into the app at `Areas/Pages/Identity/Account/Manage`, add the following code to the `ExternalLoginModel` in the `ExternalLogin.cshtml.cs` file.</span></span>

<span data-ttu-id="fe5f6-172">追加された要求の辞書を追加します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-172">Add a dictionary of added claims.</span></span> <span data-ttu-id="fe5f6-173">ディクショナリキーを使用して要求の種類を保持し、値を使用して既定値を保持します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-173">Use the dictionary keys to hold the claim types, and use the values to hold a default value.</span></span> <span data-ttu-id="fe5f6-174">クラスの先頭に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-174">Add the following line to the top of the class.</span></span> <span data-ttu-id="fe5f6-175">次の例では、既定値として一般的なヘッドショットイメージを使用して、ユーザーの Google picture に1つの要求が追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-175">The following example assumes that one claim is added for the user's Google picture with a generic headshot image as the default value:</span></span>

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

<span data-ttu-id="fe5f6-176">メソッドの既定のコードを `OnGetCallbackAsync` 次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-176">Replace the default code of the `OnGetCallbackAsync` method with the following code.</span></span> <span data-ttu-id="fe5f6-177">このコードは、要求ディクショナリをループ処理します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-177">The code loops through the claims dictionary.</span></span> <span data-ttu-id="fe5f6-178">要求は、ユーザーごとに追加 (バックフィル) または更新されます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-178">Claims are added (backfilled) or updated for each user.</span></span> <span data-ttu-id="fe5f6-179">要求が追加または更新されると、を使用してユーザーのサインインが更新され、 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 既存の認証プロパティ () が保持され `AuthenticationProperties` ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-179">When claims are added or updated, the user sign-in is refreshed using the <xref:Microsoft.AspNetCore.Identity.SignInManager%601>, preserving the existing authentication properties (`AuthenticationProperties`).</span></span>

```csharp
public async Task<IActionResult> OnGetCallbackAsync(
    string returnUrl = null, string remoteError = null)
{
    returnUrl = returnUrl ?? Url.Content("~/");

    if (remoteError != null)
    {
        ErrorMessage = $"Error from external provider: {remoteError}";

        return RedirectToPage("./Login", new {ReturnUrl = returnUrl });
    }

    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        ErrorMessage = "Error loading external login information.";
        return RedirectToPage("./Login", new { ReturnUrl = returnUrl });
    }

    // Sign in the user with this external login provider if the user already has a 
    // login.
    var result = await _signInManager.ExternalLoginSignInAsync(info.LoginProvider, 
        info.ProviderKey, isPersistent: false, bypassTwoFactor : true);

    if (result.Succeeded)
    {
        _logger.LogInformation("{Name} logged in with {LoginProvider} provider.", 
            info.Principal.Identity.Name, info.LoginProvider);

        if (_claimsToSync.Count > 0)
        {
            var user = await _userManager.FindByLoginAsync(info.LoginProvider, 
                info.ProviderKey);
            var userClaims = await _userManager.GetClaimsAsync(user);
            bool refreshSignIn = false;

            foreach (var addedClaim in _claimsToSync)
            {
                var userClaim = userClaims
                    .FirstOrDefault(c => c.Type == addedClaim.Key);

                if (info.Principal.HasClaim(c => c.Type == addedClaim.Key))
                {
                    var externalClaim = info.Principal.FindFirst(addedClaim.Key);

                    if (userClaim == null)
                    {
                        await _userManager.AddClaimAsync(user, 
                            new Claim(addedClaim.Key, externalClaim.Value));
                        refreshSignIn = true;
                    }
                    else if (userClaim.Value != externalClaim.Value)
                    {
                        await _userManager
                            .ReplaceClaimAsync(user, userClaim, externalClaim);
                        refreshSignIn = true;
                    }
                }
                else if (userClaim == null)
                {
                    // Fill with a default value
                    await _userManager.AddClaimAsync(user, new Claim(addedClaim.Key, 
                        addedClaim.Value));
                    refreshSignIn = true;
                }
            }

            if (refreshSignIn)
            {
                await _signInManager.RefreshSignInAsync(user);
            }
        }

        return LocalRedirect(returnUrl);
    }

    if (result.IsLockedOut)
    {
        return RedirectToPage("./Lockout");
    }
    else
    {
        // If the user does not have an account, then ask the user to create an 
        // account.
        ReturnUrl = returnUrl;
        ProviderDisplayName = info.ProviderDisplayName;

        if (info.Principal.HasClaim(c => c.Type == ClaimTypes.Email))
        {
            Input = new InputModel
            {
                Email = info.Principal.FindFirstValue(ClaimTypes.Email)
            };
        }

        return Page();
    }
}
```

<span data-ttu-id="fe5f6-180">同様のアプローチは、ユーザーがサインインしている間に要求が変更されても、バックフィルステップが不要な場合にも行われます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-180">A similar approach is taken when claims change while a user is signed in but a backfill step isn't required.</span></span> <span data-ttu-id="fe5f6-181">ユーザーの要求を更新するには、ユーザーに対して次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-181">To update a user's claims, call the following on the user:</span></span>

* <span data-ttu-id="fe5f6-182">Usermanager。 id データベースに格納されているクレームに対して、ユーザーの[Eclaimasync](xref:Microsoft.AspNetCore.Identity.UserManager%601)を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-182">[UserManager.ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) on the user for claims stored in the identity database.</span></span>
* <span data-ttu-id="fe5f6-183">新しい認証を強制的に生成するために、ユーザーの[RefreshSignInAsync を SignInManager します](xref:Microsoft.AspNetCore.Identity.SignInManager%601) cookie 。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-183">[SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on the user to force the generation of a new authentication cookie.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="fe5f6-184">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="fe5f6-184">Removal of claim actions and claims</span></span>

<span data-ttu-id="fe5f6-185">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) は、指定されたのすべての要求アクションを <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> コレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-185">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="fe5f6-186">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) は、指定されたの要求を <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id から削除します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-186">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="fe5f6-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は、主に [OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="fe5f6-188">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="fe5f6-188">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fe5f6-189">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-189">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="fe5f6-190">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-190">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="fe5f6-191">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe5f6-192">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="fe5f6-192">Prerequisites</span></span>

<span data-ttu-id="fe5f6-193">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-193">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="fe5f6-194">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-194">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="fe5f6-195">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-195">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="fe5f6-196">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-196">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="fe5f6-197">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-197">Set the client ID and client secret</span></span>

<span data-ttu-id="fe5f6-198">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-198">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="fe5f6-199">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-199">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="fe5f6-200">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-200">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="fe5f6-201">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-201">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="fe5f6-202">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="fe5f6-202">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="fe5f6-203">Google での認証</span><span class="sxs-lookup"><span data-stu-id="fe5f6-203">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="fe5f6-204">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="fe5f6-204">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="fe5f6-205">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="fe5f6-205">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="fe5f6-206">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="fe5f6-206">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="fe5f6-207">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="fe5f6-207">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="fe5f6-208">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-208">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="fe5f6-209">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-209">Establish the authentication scope</span></span>

<span data-ttu-id="fe5f6-210">を指定して、プロバイダーから取得するアクセス許可の一覧を指定し <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-210">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="fe5f6-211">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-211">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="fe5f6-212">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="fe5f6-212">Provider</span></span>  | <span data-ttu-id="fe5f6-213">Scope</span><span class="sxs-lookup"><span data-stu-id="fe5f6-213">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="fe5f6-214">Facebook</span><span class="sxs-lookup"><span data-stu-id="fe5f6-214">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="fe5f6-215">Google</span><span class="sxs-lookup"><span data-stu-id="fe5f6-215">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="fe5f6-216">Microsoft</span><span class="sxs-lookup"><span data-stu-id="fe5f6-216">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="fe5f6-217">Twitter</span><span class="sxs-lookup"><span data-stu-id="fe5f6-217">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="fe5f6-218">サンプルアプリで `userinfo.profile` <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> は、がで呼び出されたときに、Google のスコープがフレームワークによって自動的に追加され <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-218">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="fe5f6-219">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-219">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="fe5f6-220">次の例で `https://www.googleapis.com/auth/user.birthday.read` は、ユーザーの誕生日を取得するために Google スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-220">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="fe5f6-221">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-221">Map user data keys and create claims</span></span>

<span data-ttu-id="fe5f6-222">プロバイダーのオプションで、 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-222">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="fe5f6-223">要求の種類の詳細については、「」を参照してください <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-223">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="fe5f6-224">このサンプルアプリでは、 `urn:google:locale` `urn:google:picture` `locale` Google ユーザーデータのキーとキーから、ロケール () と画像 () の要求を作成し `picture` ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-224">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="fe5f6-225">では、 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) がを使用してアプリにサインイン <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-225">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="fe5f6-226">サインインプロセス中に、で <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 使用可能なユーザーデータの要求を格納でき <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-226">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="fe5f6-227">サンプルアプリでは、 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*) によって、 `urn:google:locale` `urn:google:picture` `ApplicationUser` 次の要求を含む、サインインしたのロケール () と画像 () の要求が確立 <xref:System.Security.Claims.ClaimTypes.GivenName> されます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-227">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="fe5f6-228">既定では、ユーザーの要求は認証に格納され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-228">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="fe5f6-229">認証 cookie が大きすぎると、次の理由により、アプリでエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-229">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="fe5f6-230">ブラウザーは、 cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-230">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="fe5f6-231">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-231">The overall size of the request is too large.</span></span>

<span data-ttu-id="fe5f6-232">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-232">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="fe5f6-233">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-233">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="fe5f6-234">認証ミドルウェアのカスタムを使用し <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> て、 Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-234">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="fe5f6-235">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-235">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="fe5f6-236">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="fe5f6-236">Save the access token</span></span>

<span data-ttu-id="fe5f6-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 承認が成功した後に、アクセストークンと更新トークンをに格納するかどうかを定義し <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="fe5f6-238">`SaveTokens` は `false` 、最終的な認証のサイズを小さくするために、既定でに設定され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-238">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="fe5f6-239">サンプルアプリでは、の値をに設定し `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-239">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="fe5f6-240">を実行するときに、 `OnPostConfirmationAsync` の外部プロバイダーからアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納 `ApplicationUser` `AuthenticationProperties` します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-240">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="fe5f6-241">サンプルアプリでは、アクセストークン `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録されたユーザー) を *Account/externallogin. cshtml. cs* に保存します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-241">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="fe5f6-242">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="fe5f6-242">How to add additional custom tokens</span></span>

<span data-ttu-id="fe5f6-243">の一部として格納されているカスタムトークンを追加する方法を示すために、 `SaveTokens` サンプルアプリでは、の AuthenticationToken.Name の現在のを使用してを追加し <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> [](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-243">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="fe5f6-244">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="fe5f6-244">Creating and adding claims</span></span>

<span data-ttu-id="fe5f6-245">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-245">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="fe5f6-246">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-246">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="fe5f6-247">ユーザーは、から派生し、抽象メソッドを実装することによって、カスタムアクションを定義でき <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> ます。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-247">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="fe5f6-248">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-248">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="fe5f6-249">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="fe5f6-249">Removal of claim actions and claims</span></span>

<span data-ttu-id="fe5f6-250">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) は、指定されたのすべての要求アクションを <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> コレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-250">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="fe5f6-251">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) は、指定されたの要求を <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id から削除します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-251">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="fe5f6-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は、主に [OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="fe5f6-253">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="fe5f6-253">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fe5f6-254">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="fe5f6-254">Additional resources</span></span>

* <span data-ttu-id="fe5f6-255">[dotnet/AspNetCore engineering の社会 Alsample アプリ](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): リンクされたサンプルアプリは、 [Dotnet/AspNetCore GitHub リポジトリの](https://github.com/dotnet/AspNetCore) `master` エンジニアリングブランチにあります。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-255">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="fe5f6-256">ブランチには、 `master` ASP.NET Core の次のリリースでアクティブな開発のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-256">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="fe5f6-257">リリースされたバージョンの ASP.NET Core 用のサンプルアプリのバージョンを表示するには、[ **ブランチ** ] ドロップダウンリストを使用してリリースブランチを選択します (たとえば、 `release/{X.Y}` )。</span><span class="sxs-lookup"><span data-stu-id="fe5f6-257">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>

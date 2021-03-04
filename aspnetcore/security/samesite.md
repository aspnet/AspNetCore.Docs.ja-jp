---
title: cookieASP.NET Core での SameSite s の使用
author: rick-anderson
description: を SameSite ASP.NET Core s に使用する方法について説明します。 cookie
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
- Electron
uid: security/samesite
ms.openlocfilehash: e06c73edfc999053e0aa37f05d984a2b428f69a9
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109937"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="3665a-103">cookieASP.NET Core での SameSite s の使用</span><span class="sxs-lookup"><span data-stu-id="3665a-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="3665a-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3665a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3665a-105">SameSite は、クロスサイトリクエスト偽造 (CSRF) 攻撃に対して何らかの保護を提供するように設計された [IETF](https://ietf.org/about/) ドラフト標準です。</span><span class="sxs-lookup"><span data-stu-id="3665a-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="3665a-106">最初は [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)でドラフトされていましたが、draft standard は [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)で更新されました。</span><span class="sxs-lookup"><span data-stu-id="3665a-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="3665a-107">更新された標準は以前の標準との下位互換性はありませんが、次のように最も顕著な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="3665a-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="3665a-108">CookieSameSite ヘッダーのない s は、既定でとして扱われ `SameSite=Lax` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="3665a-109">`SameSite=None` クロスサイトの使用を許可するには、を使用する必要があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="3665a-110">Cookieアサートするは、 `SameSite=None` としてもマークされている必要があり `Secure` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="3665a-111">を使用するアプリケーションは、 [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) `sameSite=Lax` `sameSite=Strict` cookie `<iframe>` クロスサイトシナリオとして扱われるため、またはに問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3665a-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="3665a-112">値 `SameSite=None` は [2016 標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07) では許可されていないため、一部の実装ではとしてを処理し cookie `SameSite=Strict` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="3665a-113">このドキュメントの「 [古いブラウザーのサポート](#sob) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3665a-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="3665a-114">この `SameSite=Lax` 設定は、ほとんどのアプリケーションで機能し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="3665a-115">[OpenID connect](https://openid.net/connect/) (oidc) や[ws-federation](https://auth0.com/docs/protocols/ws-fed)のような認証形式では、既定で POST ベースのリダイレクトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="3665a-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="3665a-116">POST ベースのリダイレクトによって SameSite ブラウザーの保護がトリガーされるため、これらのコンポーネントの SameSite は無効になります。</span><span class="sxs-lookup"><span data-stu-id="3665a-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="3665a-117">ほとんどの [OAuth](https://oauth.net/) ログインは、要求フローの違いによって影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="3665a-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="3665a-118">を出力する各 ASP.NET Core コンポーネント cookie は、SameSite が適切かどうかを判断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3665a-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-identity"></a><span data-ttu-id="3665a-119">SameSite と Identity</span><span class="sxs-lookup"><span data-stu-id="3665a-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="3665a-120">SameSite テストサンプルコード</span><span class="sxs-lookup"><span data-stu-id="3665a-120">SameSite test sample code</span></span>

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="3665a-121">次のサンプルをダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="3665a-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="3665a-122">サンプル</span><span class="sxs-lookup"><span data-stu-id="3665a-122">Sample</span></span>               | <span data-ttu-id="3665a-123">ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3665a-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="3665a-124">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3665a-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="3665a-125">[.NET Core Razor ページ](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="3665a-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3665a-126">次のサンプルをダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="3665a-126">The following sample can be downloaded and tested:</span></span>

| <span data-ttu-id="3665a-127">サンプル</span><span class="sxs-lookup"><span data-stu-id="3665a-127">Sample</span></span>               | <span data-ttu-id="3665a-128">ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3665a-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3665a-129">[.NET Core Razor ページ](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="3665a-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="3665a-130">SameSite 属性の .NET Core のサポート</span><span class="sxs-lookup"><span data-stu-id="3665a-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="3665a-131">.NET Core 2.2 以降では、年 12 2019 月の更新プログラムのリリース以降、SameSite の2019ドラフト標準がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3665a-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="3665a-132">開発者は、プロパティを使用して、sameSite 属性の値をプログラムで制御でき `HttpCookie.SameSite` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="3665a-133">`SameSite`プロパティを Strict、厳密でない、または None に設定すると、これらの値はと共にネットワーク上に書き込まれ cookie ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="3665a-134">これをに設定すると `(SameSiteMode)(-1)` 、sameSite 属性をネットワークに含める必要があることを示します。 cookie</span><span class="sxs-lookup"><span data-stu-id="3665a-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="3665a-135">.NET Core 3.0 以降では、更新された SameSite 値がサポートされ、列挙型に追加の列挙値が追加され `SameSiteMode.Unspecified` `SameSiteMode` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="3665a-136">この新しい値は、で sameSite が送信されないことを示し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="3665a-137">12月のパッチ動作の変更</span><span class="sxs-lookup"><span data-stu-id="3665a-137">December patch behavior changes</span></span>

<span data-ttu-id="3665a-138">.NET Framework と .NET Core 2.1 の特定の動作変更は、 `SameSite` プロパティが値を解釈する方法です `None` 。</span><span class="sxs-lookup"><span data-stu-id="3665a-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="3665a-139">パッチの前に、 `None` "属性をまったく出力しない" という値が適用される前に、パッチの後に "値を持つ属性を生成する" という意味になり `None` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="3665a-140">Patch の後に `SameSite` の値を指定する `(SameSiteMode)(-1)` と、属性は出力されません。</span><span class="sxs-lookup"><span data-stu-id="3665a-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="3665a-141">フォーム認証およびセッション状態 s の既定の SameSite 値は、 cookie からに変更されました `None` `Lax` 。</span><span class="sxs-lookup"><span data-stu-id="3665a-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="3665a-142">SameSite を使用した API の使用</span><span class="sxs-lookup"><span data-stu-id="3665a-142">API usage with SameSite</span></span>

<span data-ttu-id="3665a-143">[HttpContext Cookie 。s. 追加](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) の既定値はです。 `Unspecified` つまり、に SameSite 属性が追加されず、クライアントは既定の動作を使用します cookie (新しいブラウザーではなく、古いブラウザーではありません)。</span><span class="sxs-lookup"><span data-stu-id="3665a-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="3665a-144">次のコードは、SameSite の値をに変更する方法を示してい cookie `SameSiteMode.Lax` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3665a-145">S を出力するすべて cookie の ASP.NET Core コンポーネントは、前述の既定値よりも、そのシナリオに適した設定を上書きします。</span><span class="sxs-lookup"><span data-stu-id="3665a-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="3665a-146">オーバーライドされた前の既定値は変更されていません。</span><span class="sxs-lookup"><span data-stu-id="3665a-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="3665a-147">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3665a-147">Component</span></span> | cookie | <span data-ttu-id="3665a-148">Default</span><span class="sxs-lookup"><span data-stu-id="3665a-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="3665a-149">[SessionOptions。Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="3665a-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="3665a-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="3665a-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="3665a-151">[アンチ Forgeryoptions。Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="3665a-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="3665a-152">[Cookie 認証](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="3665a-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="3665a-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="3665a-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="3665a-154">[CookieTwitterOptions](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="3665a-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="3665a-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. 関連付けCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="3665a-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="3665a-156">[OpenIdConnectOptionsCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="3665a-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="3665a-157">[CookieHttpContext。s. 追加](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="3665a-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="3665a-158">ASP.NET Core 3.1 以降では、次の SameSite サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3665a-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="3665a-159">の動作を再定義して `SameSiteMode.None` を生成します。 `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="3665a-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="3665a-160">新しい値を追加し `SameSiteMode.Unspecified` て、SameSite 属性を省略します。</span><span class="sxs-lookup"><span data-stu-id="3665a-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="3665a-161">すべてのの cookie api は、既定でに設定さ `Unspecified` れています。</span><span class="sxs-lookup"><span data-stu-id="3665a-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="3665a-162">S を使用するコンポーネントの中 cookie には、そのシナリオにより固有の値が設定されているものがあります。</span><span class="sxs-lookup"><span data-stu-id="3665a-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="3665a-163">例については、上の表を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3665a-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3665a-164">ASP.NET Core 3.0 以降では、一貫性のないクライアントの既定値と競合しないように、SameSite の既定値が変更されました。</span><span class="sxs-lookup"><span data-stu-id="3665a-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="3665a-165">次の Api では、既定のをからに変更して、 `SameSiteMode.Lax ` `-1` これらのの SameSite 属性が出力されないようにしました cookie 。</span><span class="sxs-lookup"><span data-stu-id="3665a-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="3665a-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> HttpContext と共に使用されます。 [ Cookies. 追加](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="3665a-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="3665a-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  のファクトリとして使用されます。 `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="3665a-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="3665a-168">[CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="3665a-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="3665a-169">履歴と変更</span><span class="sxs-lookup"><span data-stu-id="3665a-169">History and changes</span></span>

<span data-ttu-id="3665a-170">SameSite サポートは、2.0 の ASP.NET Core で最初に [2016 ドラフト標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)を使用して実装されました。</span><span class="sxs-lookup"><span data-stu-id="3665a-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="3665a-171">2016標準はオプトインでした。</span><span class="sxs-lookup"><span data-stu-id="3665a-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="3665a-172">既定で複数のをに設定することによりオプトイン ASP.NET Core cookie `Lax` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="3665a-173">認証でいくつかの [問題](https://github.com/aspnet/Announcements/issues/318) が発生すると、ほとんどの SameSite の使用が [無効に](https://github.com/aspnet/Announcements/issues/348)なりました。</span><span class="sxs-lookup"><span data-stu-id="3665a-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="3665a-174">2016標準から2019標準に更新するために、2019年11月に[修正プログラム](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)が発行されました。</span><span class="sxs-lookup"><span data-stu-id="3665a-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="3665a-175">[SameSite 仕様の2019ドラフト](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="3665a-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="3665a-176">は、2016ドラフトとの下位互換性が **ありません** 。</span><span class="sxs-lookup"><span data-stu-id="3665a-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="3665a-177">詳細については、このドキュメントの「 [古いブラウザーのサポート](#sob) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3665a-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="3665a-178">cookieは、既定でとして扱われることを指定し `SameSite=Lax` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="3665a-179">cookieクロスサイト配信を有効にするために明示的にアサートするを指定し `SameSite=None` ます。これは、としてマークする必要があり `Secure` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="3665a-180">`None` は、オプトアウトする新しいエントリです。</span><span class="sxs-lookup"><span data-stu-id="3665a-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="3665a-181">は、ASP.NET Core 2.1、2.2、および3.0 に対して発行された修正プログラムによってサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3665a-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="3665a-182">ASP.NET Core 3.1 には、追加の SameSite サポートがあります。</span><span class="sxs-lookup"><span data-stu-id="3665a-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="3665a-183">[2 月 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)日に既定で[Chrome](https://chromestatus.com/feature/5088147346030592)によって有効になるようにスケジュールされています。</span><span class="sxs-lookup"><span data-stu-id="3665a-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="3665a-184">ブラウザーは2019でこの標準への移行を開始しました。</span><span class="sxs-lookup"><span data-stu-id="3665a-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="3665a-185">2016 SameSite draft standard から2019ドラフト標準への変更によって影響を受ける Api</span><span class="sxs-lookup"><span data-stu-id="3665a-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="3665a-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="3665a-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="3665a-187">[CookieSameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="3665a-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="3665a-188">[CookieSameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="3665a-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="3665a-189">[CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="3665a-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="3665a-190">古いブラウザーのサポート</span><span class="sxs-lookup"><span data-stu-id="3665a-190">Supporting older browsers</span></span>

<span data-ttu-id="3665a-191">2016 SameSite 標準では、不明な値を値として扱う必要があり `SameSite=Strict` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="3665a-192">2016 SameSite 標準をサポートする古いブラウザーからアクセスされるアプリは、値がの SameSite プロパティを取得すると破損する可能性があり `None` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="3665a-193">Web apps が古いブラウザーをサポートする予定の場合は、ブラウザーの検出を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3665a-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="3665a-194">User-Agents 値が非常に揮発性で頻繁に変更されるため、ASP.NET Core はブラウザーの検出を実装しません。</span><span class="sxs-lookup"><span data-stu-id="3665a-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="3665a-195">の拡張ポイントでは <xref:Microsoft.AspNetCore.CookiePolicy> User-Agent 特定のロジックにプラグインできます。</span><span class="sxs-lookup"><span data-stu-id="3665a-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="3665a-196">で `Startup.Configure` 、を <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> 呼び出す前、 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> または s を書き込む *任意* のメソッドを呼び出すコードを追加し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="3665a-197">で `Startup.ConfigureServices` 、次のようなコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="3665a-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range=">= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="3665a-198">前の例では、は、ユーザー `MyUserAgentDetectionLib.DisallowsSameSiteNone` エージェントが SameSite をサポートしていないかどうかを検出する、ユーザーが指定したライブラリです `None` 。</span><span class="sxs-lookup"><span data-stu-id="3665a-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="3665a-199">次のコードは、サンプルメソッドを示してい `DisallowsSameSiteNone` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="3665a-200">次のコードは、デモンストレーションのみを対象としています。</span><span class="sxs-lookup"><span data-stu-id="3665a-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="3665a-201">完全であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="3665a-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="3665a-202">管理されていないか、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3665a-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="3665a-203">SameSite 問題のアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3665a-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="3665a-204">サードパーティのログインによってなどのリモートサイトと対話するアプリでは、次の操作を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="3665a-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="3665a-205">複数のブラウザーで相互作用をテストします。</span><span class="sxs-lookup"><span data-stu-id="3665a-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="3665a-206">このドキュメントで説明され[ Cookie ているポリシーブラウザーの検出と軽減策](#sob)を適用します。</span><span class="sxs-lookup"><span data-stu-id="3665a-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="3665a-207">新しい SameSite 動作にオプトインできるクライアントバージョンを使用して、web アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="3665a-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="3665a-208">Chrome、Firefox、Chromium Edge には、テストに使用できる新しいオプトイン機能フラグがあります。</span><span class="sxs-lookup"><span data-stu-id="3665a-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="3665a-209">アプリが SameSite パッチを適用したら、古いクライアントバージョン (特に Safari) を使用してテストします。</span><span class="sxs-lookup"><span data-stu-id="3665a-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="3665a-210">詳細については、このドキュメントの「 [古いブラウザーのサポート](#sob) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3665a-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="3665a-211">Chrome を使用したテスト</span><span class="sxs-lookup"><span data-stu-id="3665a-211">Test with Chrome</span></span>

<span data-ttu-id="3665a-212">Chrome 78 + には一時的な軽減策があるため、誤解を招く結果になります。</span><span class="sxs-lookup"><span data-stu-id="3665a-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="3665a-213">Chrome 78 + 一時的な軽減策を使用すると、が cookie 2 分未満になります。</span><span class="sxs-lookup"><span data-stu-id="3665a-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="3665a-214">適切なテストフラグが有効になっている Chrome 76 または77では、より正確な結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="3665a-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="3665a-215">新しい SameSite の動作をテストするには、[ `chrome://flags/#same-site-by-default-cookies` **有効** に切り替えます。</span><span class="sxs-lookup"><span data-stu-id="3665a-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="3665a-216">新しい設定で失敗するように、Chrome の旧バージョン (75 以降) が報告され `None` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="3665a-217">このドキュメントの「 [古いブラウザーのサポート](#sob) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3665a-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="3665a-218">Google では、以前のバージョンの chrome は使用できません。</span><span class="sxs-lookup"><span data-stu-id="3665a-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="3665a-219">「 [Chromium のダウンロード](https://www.chromium.org/getting-involved/download-chromium) 」の手順に従って、Chrome の旧バージョンをテストします。</span><span class="sxs-lookup"><span data-stu-id="3665a-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="3665a-220">以前のバージョンの chrome を検索することによって提供されるリンクから Chrome **をダウンロードしないでください** 。</span><span class="sxs-lookup"><span data-stu-id="3665a-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="3665a-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="3665a-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="3665a-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="3665a-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="3665a-223">カナリアバージョン以降では、テストのために、厳密でない `80.0.3975.0` + POST の一時的な軽減策を無効にすることができます。この場合、新しいフラグを使用して、 `--enable-features=SameSiteDefaultChecksMethodRigorously` 軽減策が削除された機能の最終的な終了状態でサイトとサービスをテストできます。</span><span class="sxs-lookup"><span data-stu-id="3665a-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="3665a-224">詳細については、「Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3665a-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="3665a-225">Safari を使用したテスト</span><span class="sxs-lookup"><span data-stu-id="3665a-225">Test with Safari</span></span>

<span data-ttu-id="3665a-226">Safari 12 では、以前のドラフトが厳密に実装されており、新しい `None` 値がに含まれていると失敗し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="3665a-227">`None` このドキュメントでは、以前の [ブラウザーをサポート](#sob) するブラウザーの検出コードを使用して回避します。</span><span class="sxs-lookup"><span data-stu-id="3665a-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="3665a-228">MSAL、ADAL、使用している任意のライブラリを使用して、Safari 12、Safari 13、WebKit ベースの OS スタイルのログインをテストします。</span><span class="sxs-lookup"><span data-stu-id="3665a-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="3665a-229">この問題は、基盤の OS バージョンによって変わります。</span><span class="sxs-lookup"><span data-stu-id="3665a-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="3665a-230">OSX Mojave (10.14) と iOS 12 には、新しい SameSite の動作に互換性の問題があることがわかっています。</span><span class="sxs-lookup"><span data-stu-id="3665a-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="3665a-231">OS を OSX Catalina.properties (10.15) または iOS 13 にアップグレードすると、問題が解決されます。</span><span class="sxs-lookup"><span data-stu-id="3665a-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="3665a-232">Safari には、現在、新しい仕様動作をテストするオプトインフラグがありません。</span><span class="sxs-lookup"><span data-stu-id="3665a-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="3665a-233">Firefox でのテスト</span><span class="sxs-lookup"><span data-stu-id="3665a-233">Test with Firefox</span></span>

<span data-ttu-id="3665a-234">新しい標準の Firefox サポートは、バージョン68以降で、機能フラグを使用してページをオンにすることでテストでき `about:config` `network.cookie.sameSite.laxByDefault` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="3665a-235">以前のバージョンの Firefox との互換性に関する問題は報告されていません。</span><span class="sxs-lookup"><span data-stu-id="3665a-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="3665a-236">Edge ブラウザーを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="3665a-236">Test with Edge browser</span></span>

<span data-ttu-id="3665a-237">Edge では、古い SameSite 標準がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3665a-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="3665a-238">Edge バージョン44には、新しい標準との互換性に関する既知の問題はありません。</span><span class="sxs-lookup"><span data-stu-id="3665a-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="3665a-239">Edge でテストする (Chromium)</span><span class="sxs-lookup"><span data-stu-id="3665a-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="3665a-240">ページに SameSite フラグが設定されてい `edge://flags/#same-site-by-default-cookies` ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="3665a-241">Edge Chromium で互換性の問題は検出されませんでした。</span><span class="sxs-lookup"><span data-stu-id="3665a-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="3665a-242">テストに使用する Electron</span><span class="sxs-lookup"><span data-stu-id="3665a-242">Test with Electron</span></span>

<span data-ttu-id="3665a-243">のバージョンに Electron は、古いバージョンの Chromium が含まれています。</span><span class="sxs-lookup"><span data-stu-id="3665a-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="3665a-244">たとえば、 Electron チームによって使用されるのバージョンは Chromium 66 であり、以前の動作を示しています。</span><span class="sxs-lookup"><span data-stu-id="3665a-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="3665a-245">使用している製品のバージョンで、独自の互換性テストを実行する必要があり Electron ます。</span><span class="sxs-lookup"><span data-stu-id="3665a-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="3665a-246">次のセクションの「 [古いブラウザーのサポート](#sob) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3665a-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3665a-247">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3665a-247">Additional resources</span></span>

* [<span data-ttu-id="3665a-248">Chromium ブログ: 開発者: 新しい SameSite の準備 = None;セキュリティで保護された Cookie 設定</span><span class="sxs-lookup"><span data-stu-id="3665a-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="3665a-249">[SameSite の cookie 説明](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="3665a-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="3665a-250">2019年11月の修正プログラム</span><span class="sxs-lookup"><span data-stu-id="3665a-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="3665a-251">サンプル</span><span class="sxs-lookup"><span data-stu-id="3665a-251">Sample</span></span>               | <span data-ttu-id="3665a-252">ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3665a-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="3665a-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3665a-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="3665a-254">[.NET Core Razor ページ](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="3665a-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="3665a-255">サンプル</span><span class="sxs-lookup"><span data-stu-id="3665a-255">Sample</span></span>               | <span data-ttu-id="3665a-256">ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3665a-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3665a-257">[.NET Core Razor ページ](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="3665a-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

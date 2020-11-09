---
title: ASP.NET Core で特定のスキームを使用して承認する
author: rick-anderson
description: この記事では、複数の認証方法を使用する場合に、特定のスキームに id を制限する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 4dc86480d40d8ee40b3c03aa7fd2994e6c15b105
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053125"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="50731-103">ASP.NET Core で特定のスキームを使用して承認する</span><span class="sxs-lookup"><span data-stu-id="50731-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="50731-104">シングルページアプリケーション (spa) などの一部のシナリオでは、複数の認証方法を使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="50731-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="50731-105">たとえば、アプリはベース cookie 認証を使用して、JavaScript 要求のログインと JWT ベアラー認証を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="50731-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="50731-106">場合によっては、アプリに認証ハンドラーのインスタンスが複数存在することがあります。</span><span class="sxs-lookup"><span data-stu-id="50731-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="50731-107">たとえば、1つのに基本 id が格納されている2つの cookie ハンドラーと、multi-factor authentication (MFA) がトリガーされたときに作成されるハンドラーがあります。</span><span class="sxs-lookup"><span data-stu-id="50731-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="50731-108">ユーザーが追加のセキュリティを必要とする操作を要求したため、MFA がトリガーされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="50731-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="50731-109">ユーザーが MFA を必要とするリソースを要求したときに MFA を適用する方法の詳細については、「MFA を使用した GitHub の問題の [保護」セクション](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="50731-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="50731-110">認証時に認証サービスが構成されると、認証スキームに名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="50731-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="50731-111">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="50731-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="50731-112">上記のコードでは、2つの認証ハンドラーが追加されています。1つは cookie s、もう1つはベアラー用です。</span><span class="sxs-lookup"><span data-stu-id="50731-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="50731-113">既定のスキームを指定すると、 `HttpContext.User` プロパティはその id に設定されます。</span><span class="sxs-lookup"><span data-stu-id="50731-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="50731-114">この動作が望ましくない場合は、のパラメーターなしの形式を呼び出して無効に `AddAuthentication` します。</span><span class="sxs-lookup"><span data-stu-id="50731-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="50731-115">認証属性を使用したスキームの選択</span><span class="sxs-lookup"><span data-stu-id="50731-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="50731-116">承認の時点で、アプリは使用するハンドラーを示します。</span><span class="sxs-lookup"><span data-stu-id="50731-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="50731-117">認証スキームのコンマ区切りの一覧をに渡して、アプリが承認するハンドラーを選択し `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="50731-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="50731-118">属性は、 `[Authorize]` 既定値が構成されているかどうかに関係なく、使用する認証スキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="50731-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="50731-119">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="50731-119">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="50731-120">前の例では、 cookie およびベアラーハンドラーの両方が実行され、現在のユーザーの id を作成して追加する機会があります。</span><span class="sxs-lookup"><span data-stu-id="50731-120">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="50731-121">1つのスキームのみを指定すると、対応するハンドラーが実行されます。</span><span class="sxs-lookup"><span data-stu-id="50731-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="50731-122">前のコードでは、"ベアラー" スキームを持つハンドラーだけが実行されます。</span><span class="sxs-lookup"><span data-stu-id="50731-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="50731-123">cookieベースの id は無視されます。</span><span class="sxs-lookup"><span data-stu-id="50731-123">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="50731-124">ポリシーを使用したスキームの選択</span><span class="sxs-lookup"><span data-stu-id="50731-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="50731-125">[ポリシー](xref:security/authorization/policies)で目的のスキームを指定する場合は、ポリシーを追加するときにコレクションを設定でき `AuthenticationSchemes` ます。</span><span class="sxs-lookup"><span data-stu-id="50731-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="50731-126">前の例では、"Over18" ポリシーは "ベアラー" ハンドラーによって作成された id に対してのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="50731-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="50731-127">属性のプロパティを設定して、ポリシーを使用し `[Authorize]` `Policy` ます。</span><span class="sxs-lookup"><span data-stu-id="50731-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="50731-128">複数の認証方式を使用する</span><span class="sxs-lookup"><span data-stu-id="50731-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="50731-129">アプリによっては、複数の種類の認証をサポートする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="50731-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="50731-130">たとえば、アプリは Azure Active Directory とユーザーデータベースからユーザーを認証する場合があります。</span><span class="sxs-lookup"><span data-stu-id="50731-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="50731-131">もう1つの例として、Active Directory フェデレーションサービス (AD FS) と Azure Active Directory B2C の両方からユーザーを認証するアプリがあります。</span><span class="sxs-lookup"><span data-stu-id="50731-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="50731-132">この場合、アプリはいくつかの発行者から JWT ベアラートークンを受け入れる必要があります。</span><span class="sxs-lookup"><span data-stu-id="50731-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="50731-133">同意するすべての認証スキームを追加します。</span><span class="sxs-lookup"><span data-stu-id="50731-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="50731-134">たとえば、の次のコードは、 `Startup.ConfigureServices` 発行者が異なる2つの JWT ベアラー認証スキームを追加します。</span><span class="sxs-lookup"><span data-stu-id="50731-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="50731-135">既定の認証スキームでは、JWT ベアラー認証が1つだけ登録され `JwtBearerDefaults.AuthenticationScheme` ます。</span><span class="sxs-lookup"><span data-stu-id="50731-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="50731-136">追加の認証は、一意の認証スキームを使用して登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="50731-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="50731-137">次の手順では、両方の認証方式を受け入れるように既定の承認ポリシーを更新します。</span><span class="sxs-lookup"><span data-stu-id="50731-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="50731-138">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="50731-138">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="50731-139">既定の承認ポリシーがオーバーライドされると、コントローラーで属性を使用できるようになり `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="50731-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="50731-140">コントローラーは、最初または2番目の発行者によって発行された JWT の要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="50731-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

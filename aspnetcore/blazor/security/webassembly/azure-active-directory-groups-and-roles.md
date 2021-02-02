---
title: Azure Active Directory のグループとロールを使用する ASP.NET Core Blazor WebAssembly
author: guardrex
description: Azure Active Directory のグループとロールを使用するように Blazor WebAssembly を構成する方法を説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: d1c75d85283b583d8bfd885fcd6552b69c2528c7
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758266"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="34e2a-103">Azure Active Directory (AAD) グループ、管理者ロール、およびアプリ ロール</span><span class="sxs-lookup"><span data-stu-id="34e2a-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="34e2a-104">作成者: [Luke Latham](https://github.com/guardrex)、[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="34e2a-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="34e2a-105">Azure Active Directory (AAD) には、ASP.NET Core Identity と組み合わせることができる承認方法がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="34e2a-106">グループ</span><span class="sxs-lookup"><span data-stu-id="34e2a-106">Groups</span></span>
  * <span data-ttu-id="34e2a-107">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="34e2a-107">Security</span></span>
  * <span data-ttu-id="34e2a-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="34e2a-108">Microsoft 365</span></span>
  * <span data-ttu-id="34e2a-109">配布</span><span class="sxs-lookup"><span data-stu-id="34e2a-109">Distribution</span></span>
* <span data-ttu-id="34e2a-110">役割</span><span class="sxs-lookup"><span data-stu-id="34e2a-110">Roles</span></span>
  * <span data-ttu-id="34e2a-111">AAD 管理者ロール</span><span class="sxs-lookup"><span data-stu-id="34e2a-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="34e2a-112">アプリ ロール</span><span class="sxs-lookup"><span data-stu-id="34e2a-112">App Roles</span></span>

<span data-ttu-id="34e2a-113">この記事のガイダンスは、次のトピックで説明されている Blazor WebAssembly AAD デプロイ シナリオに適用されます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="34e2a-114">Microsoft アカウントによるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="34e2a-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="34e2a-115">AAD によるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="34e2a-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="34e2a-116">AAD によるホスティング</span><span class="sxs-lookup"><span data-stu-id="34e2a-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="34e2a-117">この記事のガイダンスでは、クライアントとサーバーのアプリについて説明します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-117">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="34e2a-118">**クライアント**:スタンドアロン Blazor WebAssembly アプリ、またはホストされた Blazor ソリューションの *`Client`* アプリ。</span><span class="sxs-lookup"><span data-stu-id="34e2a-118">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="34e2a-119">**サーバー**:スタンドアロン ASP.NET Core サーバー API または Web API アプリ、またはホストされた Blazor ソリューションの *`Server`* アプリ。</span><span class="sxs-lookup"><span data-stu-id="34e2a-119">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="34e2a-120">スコープ</span><span class="sxs-lookup"><span data-stu-id="34e2a-120">Scopes</span></span>

<span data-ttu-id="34e2a-121">ユーザー プロファイル、ロールの割り当て、およびグループ メンバーシップ データに対する [Microsoft Graph API](/graph/use-the-api) 呼び出しを許可するために、**クライアント** は Azure portal で (`https://graph.microsoft.com/User.Read`) [Graph API アクセス許可 (スコープ)](/graph/permissions-reference) を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-121">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="34e2a-122">ロールおよびグループ メンバーシップ データに対して Graph API を呼び出す **サーバー** アプリには、Azure portal で `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API アクセス許可 (スコープ)](/graph/permissions-reference) が付与されます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-122">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="34e2a-123">この記事の最初のセクションに記載したトピックで説明されている AAD デプロイ シナリオに必要なスコープに加えて、これらのスコープが必要になります。</span><span class="sxs-lookup"><span data-stu-id="34e2a-123">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="34e2a-124">"アクセス許可" と "スコープ" という語は、Azure portal や、Microsoft および外部のさまざまなドキュメントのセットで同じように使用されています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-124">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="34e2a-125">この記事では、Azure portal でアプリに割り当てられたアクセス許可に対して、"スコープ" という語を使用します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-125">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="34e2a-126">グループ メンバーシップ要求の属性</span><span class="sxs-lookup"><span data-stu-id="34e2a-126">Group Membership Claims attribute</span></span>

<span data-ttu-id="34e2a-127">**クライアント** および **サーバー** アプリに対する Azure portal のアプリ マニフェストで、[`groupMembershipClaims` 属性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)を `All` に設定します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-127">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="34e2a-128">値を `All` にすると、サインインしているユーザーが属しているすべてのセキュリティ グループ、配布グループ、およびロールが取得されます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-128">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="34e2a-129">アプリの Azure portal の登録を開きます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-129">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="34e2a-130">サイド バーで **[管理]**  >  **[マニフェスト]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-130">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="34e2a-131">`groupMembershipClaims` 属性を見つけます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-131">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="34e2a-132">この値を `All` に設定します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-132">Set the value to `All`.</span></span>
1. <span data-ttu-id="34e2a-133">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-133">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="34e2a-134">カスタム ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="34e2a-134">Custom user account</span></span>

<span data-ttu-id="34e2a-135">Azure portal で AAD セキュリティ グループと AAD 管理者ロールにユーザーを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-135">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="34e2a-136">この記事の例では:</span><span class="sxs-lookup"><span data-stu-id="34e2a-136">The examples in this article:</span></span>

* <span data-ttu-id="34e2a-137">サーバー API データへのアクセスを認可するために、ユーザーが Azure portal AAD テナントの AAD "*課金管理者*" ロールに割り当てられているとします。</span><span class="sxs-lookup"><span data-stu-id="34e2a-137">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="34e2a-138">[認可ポリシー](xref:security/authorization/policies)を使用して、**クライアント** および **サーバー** アプリ内でアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-138">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="34e2a-139">**クライアント** アプリで、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張して次のプロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-139">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="34e2a-140">`Roles`: AAD アプリ ロールの配列 (「[アプリ ロール](#app-roles)」セクションで説明します)</span><span class="sxs-lookup"><span data-stu-id="34e2a-140">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="34e2a-141">`Wids`: [既知の ID 要求 (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims) の AAD 管理者ロール</span><span class="sxs-lookup"><span data-stu-id="34e2a-141">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="34e2a-142">`Oid`: 変更不可能な[オブジェクト識別子要求 (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (テナント内およびテナント間でユーザーを一意に識別します)</span><span class="sxs-lookup"><span data-stu-id="34e2a-142">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="34e2a-143">各配列プロパティには空の配列を代入します。これにより、これらのプロパティを `foreach` ループ内で使用するときに `null` を確認しなくても済むようになります。</span><span class="sxs-lookup"><span data-stu-id="34e2a-143">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="34e2a-144">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="34e2a-144">`CustomUserAccount.cs`:</span></span>

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

<span data-ttu-id="34e2a-145">**クライアント** アプリのプロジェクト ファイルに [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-145">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="34e2a-146"><xref:blazor/security/webassembly/graph-api#graph-sdk> の記事のセクション「*Graph SDK*」にある Graph SDK ユーティリティ クラスと構成を追加します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-146">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="34e2a-147">`GraphClientExtensions` クラスの `AuthenticateRequestAsync` メソッドで、アクセス トークンの `User.Read` スコープを指定します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-147">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="34e2a-148">次のカスタム ユーザー アカウント ファクトリを **クライアント** アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-148">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="34e2a-149">カスタム ユーザー ファクトリは、以下を確立するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-149">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="34e2a-150">アプリ ロールの要求 (`appRole`) (「[アプリ ロール](#app-roles)」セクションで説明します)</span><span class="sxs-lookup"><span data-stu-id="34e2a-150">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="34e2a-151">AAD 管理者ロールの要求 (`directoryRole`)</span><span class="sxs-lookup"><span data-stu-id="34e2a-151">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="34e2a-152">ユーザーの携帯電話番号に対するユーザー プロファイル データの要求の例 (`mobilePhone`)</span><span class="sxs-lookup"><span data-stu-id="34e2a-152">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="34e2a-153">AAD グループ要求 (`directoryGroup`)</span><span class="sxs-lookup"><span data-stu-id="34e2a-153">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="34e2a-154">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="34e2a-154">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
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
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
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

<span data-ttu-id="34e2a-155">上記のコードには、推移的なメンバーシップは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="34e2a-155">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="34e2a-156">アプリで直接的および推移的なグループ メンバーシップの要求が必要である場合は、`MemberOf` プロパティ (`IUserMemberOfCollectionWithReferencesRequestBuilder`) を `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-156">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="34e2a-157">上記のコードでは、AAD 管理者ロール (`#microsoft.graph.directoryRole` の種類) であるグループ メンバーシップの要求 (`groups`) が無視されます。これは、Microsoft Identity プラットフォーム 2.0 によって返される GUID 値が、[**ロール テンプレート ID**](/azure/active-directory/roles/permissions-reference#role-template-ids) ではなく AAD 管理者ロールの **エンティティ ID** であるためです。</span><span class="sxs-lookup"><span data-stu-id="34e2a-157">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="34e2a-158">エンティティ ID は Microsoft Identity プラットフォーム 2.0 のテナント間で一定ではないため、アプリでユーザーの認可ポリシーを作成するために使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="34e2a-158">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="34e2a-159">**`wids` 要求によって提供される** AAD 管理者ロールの **ロール テンプレート ID** を常に使用してください。</span><span class="sxs-lookup"><span data-stu-id="34e2a-159">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="34e2a-160">**クライアント** アプリの `Program.Main` で、カスタム ユーザー アカウント ファクトリを使用するように MSAL 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-160">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="34e2a-161">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="34e2a-161">`Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a><span data-ttu-id="34e2a-162">承認の構成</span><span class="sxs-lookup"><span data-stu-id="34e2a-162">Authorization configuration</span></span>

<span data-ttu-id="34e2a-163">**クライアント** アプリの `Program.Main` で、各[アプリ ロール](#app-roles)、AAD 管理者ロール、またはセキュリティ グループに対して[ポリシー](xref:security/authorization/policies)を作成します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-163">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="34e2a-164">次の例では、AAD の "*課金管理者*" ロールに対するポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-164">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="34e2a-165">AAD 管理者ロールの ID の完全な一覧については、Azure ドキュメントの「[ロール テンプレート ID](/azure/active-directory/roles/permissions-reference#role-template-ids)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="34e2a-165">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="34e2a-166">認可ポリシーの詳細については、「<xref:security/authorization/policies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="34e2a-166">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="34e2a-167">次の例では、**クライアント** アプリで前述のポリシーを使用してユーザーを承認します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-167">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="34e2a-168">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component)ではポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-168">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="34e2a-169">コンポーネント全体へのアクセスは、[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) を使用するポリシーを基にして行うことができます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-169">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="34e2a-170">ログインしていないユーザーは、AAD のサインイン ページにリダイレクトされ、サインイン後にコンポーネントに戻されます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-170">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="34e2a-171">ポリシー チェックは、[手続き型ロジックを使用してコードで実行する](xref:blazor/security/index#procedural-logic)こともできます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-171">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="34e2a-172">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="34e2a-172">`Pages/CheckPolicy.razor`:</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="34e2a-173">サーバー API または Web API のアクセスを承認する</span><span class="sxs-lookup"><span data-stu-id="34e2a-173">Authorize server API/web API access</span></span>

<span data-ttu-id="34e2a-174">**サーバー** API アプリでは、アクセス トークンに `groups`、`wids`、`http://schemas.microsoft.com/ws/2008/06/identity/claims/role` の要求が含まれている場合に、セキュリティ グループ、AAD 管理者ロール、およびアプリ ロールに対する[認可ポリシー](xref:security/authorization/policies)を使用して、セキュリティで保護された API エンドポイントにアクセスすることをユーザーに許可できます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-174">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="34e2a-175">次の例では、`Startup.ConfigureServices` で `wids` (既知の ID またはロール テンプレート ID) 要求を使用して、AAD "*課金管理者*" ロールに対するポリシーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-175">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="34e2a-176">AAD 管理者ロールの ID の完全な一覧については、Azure ドキュメントの「[ロール テンプレート ID](/azure/active-directory/roles/permissions-reference#role-template-ids)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="34e2a-176">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="34e2a-177">認可ポリシーの詳細については、「<xref:security/authorization/policies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="34e2a-177">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="34e2a-178">**サーバー** アプリのコントローラーへのアクセスは、[`[Authorize]` 属性](xref:security/authorization/simple)とポリシー名の使用に基づいて行うことができます (API ドキュメント:<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)。</span><span class="sxs-lookup"><span data-stu-id="34e2a-178">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="34e2a-179">次の例では、`BillingDataController` からの課金データへのアクセスを、`BillingAdministrator` というポリシー名を持つ Azure 課金管理者に制限しています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-179">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

<span data-ttu-id="34e2a-180">詳細については、「<xref:security/authorization/policies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="34e2a-180">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="34e2a-181">アプリ ロール</span><span class="sxs-lookup"><span data-stu-id="34e2a-181">App Roles</span></span>

<span data-ttu-id="34e2a-182">アプリ ロールのメンバーシップ要求を提供するように Azure portal でアプリを構成するには、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)」を Azure ドキュメントで参照してください。</span><span class="sxs-lookup"><span data-stu-id="34e2a-182">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="34e2a-183">以下の例では、**クライアント** と **サーバー** アプリが 2 つのロールを使用して構成されていること、およびロールがテスト ユーザーに割り当てられていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-183">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="34e2a-184">ホストされている Blazor WebAssembly アプリ、またはスタンドアロン アプリのクライアントとサーバーのペア (スタンドアロンの Blazor WebAssembly アプリとスタンドアロンの ASP.NET Core サーバー API または Web API アプリ) を開発する場合、クライアントとサーバー両方の Azure portal アプリ登録の `appRoles` マニフェスト プロパティに、構成済みの同じロールを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="34e2a-184">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="34e2a-185">クライアント アプリのマニフェストでロールを確立したら、それら全体をサーバー アプリのマニフェストにコピーします。</span><span class="sxs-lookup"><span data-stu-id="34e2a-185">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="34e2a-186">クライアントとサーバーのアプリ登録の間でマニフェスト `appRoles` をミラー化しないと、アクセス トークンに適切なロール要求があっても、サーバー API または Web API の認証済みユーザーに対してロール要求が確立されません。</span><span class="sxs-lookup"><span data-stu-id="34e2a-186">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="34e2a-187">Azure AD Premium アカウントがないとグループにロールを割り当てることはできませんが、Standard Azure アカウントを使用して、ユーザーにロールを割り当て、ユーザーに対するロール要求を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-187">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="34e2a-188">このセクションのガイダンスでは、AAD Premium アカウントは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="34e2a-188">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="34e2a-189">Azure portal で各追加ロール割り当てに対して **_ユーザーを再追加する_** ことにより、複数のロールを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-189">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="34e2a-190">「[カスタム ユーザー アカウント](#custom-user-account)」セクションで示されている `CustomAccountFactory` は、JSON 配列値を持つ `roles` 要求に対して動作するように設定されています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-190">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="34e2a-191">「[カスタム ユーザー アカウント](#custom-user-account)」セクションで示されているように、**クライアント** アプリに `CustomAccountFactory` を追加して登録します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-191">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="34e2a-192">元の `roles` 要求はフレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="34e2a-192">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="34e2a-193">**クライアント** アプリの `Program.Main` で、<xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> のチェック用にロール要求として "`appRole`" という名前の要求を指定します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-193">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="34e2a-194">`directoryRoles` 要求 (管理者ロールの追加) を使用したい場合は、"`directoryRoles`" を <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> に代入します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-194">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="34e2a-195">**サーバー** アプリの `Startup.ConfigureServices` で、<xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> のチェック用にロール要求として "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" という名前の要求を指定します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-195">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> <span data-ttu-id="34e2a-196">`wids` 要求 (管理者ロールの追加) を使用したい場合は、"`wids`" を <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> に代入します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-196">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="34e2a-197">この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-197">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="34e2a-198">**クライアント** アプリのコンポーネント内のすべての認可メカニズムで、`admin` ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-198">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="34e2a-199">`AuthorizeView` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="34e2a-199">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="34e2a-200">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="34e2a-200">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="34e2a-201">手続き型ロジック</span><span class="sxs-lookup"><span data-stu-id="34e2a-201">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="34e2a-202">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-202">Multiple role tests are supported:</span></span>

* <span data-ttu-id="34e2a-203">`AuthorizeView` コンポーネントを使用して、ユーザーが `admin` **または** `developer` ロールの **いずれか** であることを要求します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-203">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="34e2a-204">`AuthorizeView` コンポーネントを使用して、ユーザーが `admin` **および** `developer` ロールの **両方** であることを要求します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-204">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="34e2a-205">`[Authorize]` 属性を使用して、ユーザーが `admin` **または** `developer` ロールの **いずれか** であることを要求します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-205">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="34e2a-206">`[Authorize]` 属性を使用して、ユーザーが `admin` **および** `developer` ロールの **両方** であることを要求します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-206">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="34e2a-207">手続き型のコードを使用して、ユーザーが `admin` **または** `developer` ロールの **いずれか** であることを要求します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-207">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* <span data-ttu-id="34e2a-208">手続き型のコードを使用して、ユーザーが `admin` **および** `developer` ロールの **両方** であることを要求します。それには、前の例の[条件付き OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) を[条件付き AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) に変更します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-208">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="34e2a-209">**サーバー** アプリのコントローラー内のすべての認可メカニズムで、`admin` ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="34e2a-209">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="34e2a-210">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="34e2a-210">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="34e2a-211">手続き型ロジック</span><span class="sxs-lookup"><span data-stu-id="34e2a-211">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="34e2a-212">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="34e2a-212">Multiple role tests are supported:</span></span>

* <span data-ttu-id="34e2a-213">`[Authorize]` 属性を使用して、ユーザーが `admin` **または** `developer` ロールの **いずれか** であることを要求します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-213">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="34e2a-214">`[Authorize]` 属性を使用して、ユーザーが `admin` **および** `developer` ロールの **両方** であることを要求します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-214">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="34e2a-215">手続き型のコードを使用して、ユーザーが `admin` **または** `developer` ロールの **いずれか** であることを要求します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-215">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* <span data-ttu-id="34e2a-216">手続き型のコードを使用して、ユーザーが `admin` **および** `developer` ロールの **両方** であることを要求します。それには、前の例の[条件付き OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) を[条件付き AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) に変更します。</span><span class="sxs-lookup"><span data-stu-id="34e2a-216">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="34e2a-217">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="34e2a-217">Additional resources</span></span>

* [<span data-ttu-id="34e2a-218">ロール テンプレート ID (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="34e2a-218">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="34e2a-219">`groupMembershipClaims` 属性 (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="34e2a-219">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="34e2a-220">方法: アプリケーションにアプリ ロールを追加してトークンで受け取る (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="34e2a-220">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="34e2a-221">アプリケーション ロール (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="34e2a-221">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>

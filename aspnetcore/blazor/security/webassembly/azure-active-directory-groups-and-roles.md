---
title: Azure Active Directory のグループとロールを使用する ASP.NET Core Blazor WebAssembly
author: guardrex
description: Azure Active Directory のグループとロールを使用するように Blazor WebAssembly を構成する方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 96a7dde9a5a756e40125ffda4c54fbf24fdc616a
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058260"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="30c55-103">Azure Active Directory (AAD) グループ、管理者ロール、およびユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="30c55-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="30c55-104">作成者: [Luke Latham](https://github.com/guardrex)、[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="30c55-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="30c55-105">この記事は、Microsoft Identity 1.0 を使用する Blazor ASP.NET Core アプリ バージョン 3.1 に適用され、まもなく Identity 2.0 を使用する 5.0 に更新される予定です。</span><span class="sxs-lookup"><span data-stu-id="30c55-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="30c55-106">詳しくは、次の GitHub イシューと pull request をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="30c55-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="30c55-107">pull request の **[Files changed]** タブには、記事の更新に関する下書きのテキストと例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="30c55-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="30c55-108">レビューと最終更新の後、pull request はライブ ドキュメント セットにマージされます。</span><span class="sxs-lookup"><span data-stu-id="30c55-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="30c55-109">問題:[Blazor AAD グループとロールを使用した WASM (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="30c55-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="30c55-110">Pull Request:[Blazor AAD グループとロールのトピック 5.0 (dotnet/AspNetCore #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="30c55-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="30c55-111">Azure Active Directory (AAD) には、ASP.NET Core Identity と組み合わせることができる承認方法がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="30c55-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="30c55-112">ユーザー定義グループ</span><span class="sxs-lookup"><span data-stu-id="30c55-112">User-defined groups</span></span>
  * <span data-ttu-id="30c55-113">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="30c55-113">Security</span></span>
  * <span data-ttu-id="30c55-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="30c55-114">Microsoft 365</span></span>
  * <span data-ttu-id="30c55-115">配布</span><span class="sxs-lookup"><span data-stu-id="30c55-115">Distribution</span></span>
* <span data-ttu-id="30c55-116">役割</span><span class="sxs-lookup"><span data-stu-id="30c55-116">Roles</span></span>
  * <span data-ttu-id="30c55-117">AAD 管理者ロール</span><span class="sxs-lookup"><span data-stu-id="30c55-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="30c55-118">ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="30c55-118">User-defined roles</span></span>

<span data-ttu-id="30c55-119">この記事のガイダンスは、次のトピックで説明されている Blazor WebAssembly AAD デプロイ シナリオに適用されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="30c55-120">Microsoft アカウントによるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="30c55-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="30c55-121">AAD によるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="30c55-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="30c55-122">AAD によるホスティング</span><span class="sxs-lookup"><span data-stu-id="30c55-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="30c55-123">スコープ</span><span class="sxs-lookup"><span data-stu-id="30c55-123">Scopes</span></span>

<span data-ttu-id="30c55-124">6 つ以上の AAD 管理者ロールおよびセキュリティ グループ メンバーシップを持つアプリ ユーザーの場合は、[Microsoft Graph API](/graph/use-the-api) の呼び出しが必須です。</span><span class="sxs-lookup"><span data-stu-id="30c55-124">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="30c55-125">Graph API の呼び出しを許可するには、Azure portal で、ホストされている Blazor ソリューションのスタンドアロンまたは *`Client`* アプリに次のいずれかの [Graph API アクセス許可 (スコープ)](/graph/permissions-reference) を付与します。</span><span class="sxs-lookup"><span data-stu-id="30c55-125">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="30c55-126">`Directory.Read.All` は、最小特権スコープであり、この記事で説明する例に使用されているスコープです。</span><span class="sxs-lookup"><span data-stu-id="30c55-126">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="30c55-127">ユーザー定義グループと管理者ロール</span><span class="sxs-lookup"><span data-stu-id="30c55-127">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="30c55-128">`groups` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、次の Azure の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30c55-128">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="30c55-129">ユーザー定義 AAD グループと AAD 管理者ロールにユーザーを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="30c55-129">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="30c55-130">Azure AD セキュリティ グループを使用したロール</span><span class="sxs-lookup"><span data-stu-id="30c55-130">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="30c55-131">`groupMembershipClaims` 属性</span><span class="sxs-lookup"><span data-stu-id="30c55-131">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="30c55-132">次の例では、ユーザーが AAD の "*課金管理者*" ロールに割り当てられているものとします。</span><span class="sxs-lookup"><span data-stu-id="30c55-132">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="30c55-133">AAD によって送信される単一の `groups` 要求では、ユーザーのグループとロールが JSON 配列内のオブジェクト ID (GUID) として示されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-133">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="30c55-134">アプリでは、グループとロールの JSON 配列を、アプリで[ポリシー](xref:security/authorization/policies)を作成できる個々の `group` 要求に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="30c55-134">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="30c55-135">割り当てられている AAD 管理者ロールとユーザー定義グループの数が 5 つを超えると、`groups` 要求を送信するのではなく、`true` 値を持つ `hasgroups` 要求が AAD から送信されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-135">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="30c55-136">ユーザーに割り当てられているロールとグループが 5 つを超える可能性があるアプリでは、ユーザーのロールとグループを取得するために、個別の Graph API 呼び出しを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="30c55-136">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="30c55-137">この記事で提供している実装例は、このシナリオに対処するものです。</span><span class="sxs-lookup"><span data-stu-id="30c55-137">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="30c55-138">詳細については、`groups` および `hasgroups` 要求の情報について [Microsoft ID プラットフォーム アクセス トークン: ペイロードの要求](/azure/active-directory/develop/access-tokens#payload-claims)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30c55-138">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="30c55-139"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張して、グループとロールの配列プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="30c55-139">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="30c55-140">各プロパティには空の配列を割り当てます。これにより、これらのプロパティを後で `foreach` ループ内で使用する場合に `null` を確認しなくても済むようになります。</span><span class="sxs-lookup"><span data-stu-id="30c55-140">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="30c55-141">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="30c55-141">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="30c55-142">次の **いずれか** の方法を使用して、AAD グループとロールの要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-142">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="30c55-143">Graph SDK を使用する</span><span class="sxs-lookup"><span data-stu-id="30c55-143">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="30c55-144">名前付き `HttpClient` を使用する</span><span class="sxs-lookup"><span data-stu-id="30c55-144">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="30c55-145">Graph SDK を使用する</span><span class="sxs-lookup"><span data-stu-id="30c55-145">Use the Graph SDK</span></span>

<span data-ttu-id="30c55-146">[`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) 用のホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリにパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-146">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="30c55-147"><xref:blazor/security/webassembly/graph-api#graph-sdk> の記事のセクション「*Graph SDK*」にある Graph SDK ユーティリティ クラスと構成を追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-147">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="30c55-148">ホストされている Blazor ソリューション (`CustomAccountFactory.cs`) のスタンドアロン アプリまたは *`Client`* アプリに次のカスタム ユーザー アカウント ファクトリを追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-148">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="30c55-149">カスタム ユーザー ファクトリは、ロールおよびグループの要求を処理するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-149">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="30c55-150">`roles` 要求の配列については、「[ユーザー定義ロール](#user-defined-roles)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="30c55-150">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="30c55-151">`hasgroups` 要求が存在する場合、Graph SDK を使用して、ユーザーのロールとグループを取得するために Graph API に対する承認された要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-151">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="30c55-152">上記のコードには、推移的なメンバーシップは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="30c55-152">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="30c55-153">アプリが直接的および推移的なグループ メンバーシップの要求を必要とする場合:</span><span class="sxs-lookup"><span data-stu-id="30c55-153">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="30c55-154">`groupsAndAzureRoles` の `IUserMemberOfCollectionWithReferencesPage` 型を `IUserTransitiveMemberOfCollectionWithReferencesPage` に変更します。</span><span class="sxs-lookup"><span data-stu-id="30c55-154">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="30c55-155">ユーザーのグループおよびロールを要求する場合、`MemberOf` プロパティを `TransitiveMemberOf` に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="30c55-155">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="30c55-156">`Program.Main` (`Program.cs`) で、カスタム ユーザー アカウント ファクトリを使用するように MSAL 認証を構成します。<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで使用されている場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="30c55-156">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a><span data-ttu-id="30c55-157">名前付き `HttpClient` を使用する</span><span class="sxs-lookup"><span data-stu-id="30c55-157">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="30c55-158">ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリで、カスタム <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-158">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="30c55-159">ロールおよびグループの情報を取得する Graph API 呼び出しには、正しいスコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="30c55-159">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="30c55-160">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="30c55-160">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="30c55-161">`Program.Main` (`Program.cs`) では、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 実装サービスを追加し、さらに Graph API 要求を行うための名前付き <xref:System.Net.Http.HttpClient> を追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-161">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="30c55-162">次の例では、クライアントに `GraphAPI` という名前を付けています。</span><span class="sxs-lookup"><span data-stu-id="30c55-162">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="30c55-163">Graph API 呼び出しから Open Data Protocol (OData) ロールおよびグループを受信するように AAD ディレクトリ オブジェクト クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-163">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="30c55-164">OData は JSON 形式で届きます。<xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> の呼び出しによって `DirectoryObjects` クラスのインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-164">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="30c55-165">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="30c55-165">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="30c55-166">ロールとグループの要求を処理するカスタム ユーザー ファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-166">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="30c55-167">次の実装例では、「[ユーザー定義ロール](#user-defined-roles)」セクションで説明する `roles` 要求の配列も処理します。</span><span class="sxs-lookup"><span data-stu-id="30c55-167">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="30c55-168">`hasgroups` 要求が存在する場合は、名前付きの <xref:System.Net.Http.HttpClient> を使用して、ユーザーのロールとグループを取得するために Graph API に承認された要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="30c55-168">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="30c55-169">この実装では、Microsoft Identity Platform v1.0 エンドポイント `https://graph.microsoft.com/v1.0/me/memberOf` ([API ドキュメント](/graph/api/user-list-memberof)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="30c55-169">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="30c55-170">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="30c55-170">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="30c55-171">元の `groups` 要求は (存在する場合) フレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="30c55-171">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="30c55-172">この例のアプローチは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="30c55-172">The approach in this example:</span></span>
>
> * <span data-ttu-id="30c55-173">カスタム <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> クラスを追加して、アクセス トークンを送信要求にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="30c55-173">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="30c55-174">セキュリティで保護された外部 Web API エンドポイントに Web API 要求を送信するために、名前付きの <xref:System.Net.Http.HttpClient> を追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-174">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="30c55-175">名前付きの <xref:System.Net.Http.HttpClient> を使用して、承認された要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="30c55-175">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="30c55-176">このアプローチの一般的な対象範囲については、記事 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30c55-176">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="30c55-177">ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリの `Program.Main` (`Program.cs`) に、ファクトリを登録します。</span><span class="sxs-lookup"><span data-stu-id="30c55-177">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="30c55-178">アプリの追加のスコープとして `Directory.Read.All` スコープに同意します。</span><span class="sxs-lookup"><span data-stu-id="30c55-178">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a><span data-ttu-id="30c55-179">承認の構成</span><span class="sxs-lookup"><span data-stu-id="30c55-179">Authorization configuration</span></span>

<span data-ttu-id="30c55-180">`Program.Main` で、グループごとまたはロールごとに[ポリシー](xref:security/authorization/policies)を作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-180">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="30c55-181">次の例では、AAD の "*課金管理者*" ロールに対するポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-181">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="30c55-182">AAD ロール オブジェクト ID の完全な一覧については、「[AAD 管理者ロールのオブジェクト ID](#aad-administrator-role-object-ids)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="30c55-182">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="30c55-183">次の例のアプリでは、前述のポリシーを使用してユーザーを承認します。</span><span class="sxs-lookup"><span data-stu-id="30c55-183">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="30c55-184">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component)ではポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-184">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="30c55-185">コンポーネント全体へのアクセスは、[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) を使用するポリシーを基にして行うことができます。</span><span class="sxs-lookup"><span data-stu-id="30c55-185">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="30c55-186">ログインしていないユーザーは、AAD のサインイン ページにリダイレクトされ、サインイン後にコンポーネントに戻されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-186">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="30c55-187">ポリシー チェックは、[手続き型ロジックを使用してコードで実行する](xref:blazor/security/index#procedural-logic)こともできます。</span><span class="sxs-lookup"><span data-stu-id="30c55-187">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="30c55-188">ユーザー定義グループと管理者ロールに対してサーバー API アクセスを承認する</span><span class="sxs-lookup"><span data-stu-id="30c55-188">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="30c55-189">クライアント側 WebAssembly のユーザーにページとリソースへのアクセスを承認するだけでなく、サーバー API でユーザーにセキュリティで保護された API エンドポイントへのアクセスを承認することができます。</span><span class="sxs-lookup"><span data-stu-id="30c55-189">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="30c55-190">*Server* アプリによってユーザーのアクセス トークンが検証された後、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="30c55-190">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="30c55-191">サーバー API アプリでは、そのアクセス トークンからのユーザーの変更不可能な[オブジェクト識別子要求 (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) を使用して、Graph API のアクセス トークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="30c55-191">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="30c55-192">Graph API 呼び出しでは、ユーザーの [`memberOf`](/graph/api/user-list-memberof) を呼び出すことで、ユーザーの Azure ユーザー定義セキュリティ グループと管理者ロールのメンバーシップを取得します。</span><span class="sxs-lookup"><span data-stu-id="30c55-192">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="30c55-193">メンバーシップは `group` 要求を確立するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-193">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="30c55-194">[認証ポリシー](xref:security/authorization/policies)を使用すると、アプリ全体でサーバー API エンドポイントへのユーザー アクセスを制限できます。</span><span class="sxs-lookup"><span data-stu-id="30c55-194">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="30c55-195">現在、このガイドには、ユーザーの [AAD ユーザー定義ロール](#user-defined-roles)に基づく承認は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="30c55-195">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="30c55-196">このセクションのガイダンスでは、サーバー API アプリを Microsoft Graph API 呼び出しの [*デーモン アプリ*](/azure/active-directory/develop/scenario-daemon-overview)として構成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-196">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="30c55-197">この方法では、次のことを行い **ません**。</span><span class="sxs-lookup"><span data-stu-id="30c55-197">This approach does **not**:</span></span>

* <span data-ttu-id="30c55-198">`access_as_user` スコープを要求する。</span><span class="sxs-lookup"><span data-stu-id="30c55-198">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="30c55-199">API 要求を行うユーザーまたはクライアントに代わって Graph API にアクセスする。</span><span class="sxs-lookup"><span data-stu-id="30c55-199">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="30c55-200">サーバー API アプリによる Graph API の呼び出しには、Azure portal 内の `Directory.Read.All` のサーバー API アプリ **アプリケーション** Graph API スコープのみが必要です。</span><span class="sxs-lookup"><span data-stu-id="30c55-200">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="30c55-201">この方法により、クライアント アプリは、サーバー API によって明示的に許可されていないディレクトリ データに完全にアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="30c55-201">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="30c55-202">クライアント アプリは、サーバー API アプリのコントローラー エンドポイントにのみアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="30c55-202">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="30c55-203">Azure の構成</span><span class="sxs-lookup"><span data-stu-id="30c55-203">Azure configuration</span></span>

* <span data-ttu-id="30c55-204">*Server* アプリの登録に、`Directory.Read.All` の (**委任** ではなく) **アプリケーション** Graph API スコープが付与されていることを確認します。これは、セキュリティ グループの最小特権アクセス レベルです。</span><span class="sxs-lookup"><span data-stu-id="30c55-204">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="30c55-205">スコープの割り当てを行った後、管理者の同意がスコープに適用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30c55-205">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="30c55-206">新しいクライアント シークレットを *Server* アプリに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="30c55-206">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="30c55-207">「[アプリケーション設定](#app-settings)」セクションでのアプリの構成のために、シークレットをメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="30c55-207">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="30c55-208">アプリケーション設定</span><span class="sxs-lookup"><span data-stu-id="30c55-208">App settings</span></span>

<span data-ttu-id="30c55-209">アプリ設定ファイル (`appsettings.json` または `appsettings.Production.json`) に、Azure portal からの *Server* アプリのクライアント シークレットを持つ `ClientSecret` エントリを作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-209">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="30c55-210">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="30c55-210">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="30c55-211">テナント パブリッシャー ドメインが検証されていない場合、ユーザーまたはクライアント アクセスのサーバー API スコープでは、`https://` ベースの URI が使用されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-211">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="30c55-212">このシナリオでは、サーバー API アプリで、`appsettings.json` ファイル内に `Audience` 構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="30c55-212">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="30c55-213">次の構成では、`Audience` の末尾に、既定のスコープ `/{DEFAULT SCOPE}` が含まれて **いません**。ここで、プレースホルダー `{DEFAULT SCOPE}` は既定のスコープです。</span><span class="sxs-lookup"><span data-stu-id="30c55-213">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="30c55-214">上記の構成例では、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="30c55-214">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="30c55-215">テナント ドメインは、`contoso.onmicrosoft.com` です。</span><span class="sxs-lookup"><span data-stu-id="30c55-215">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="30c55-216">サーバー API アプリ `ClientId` は、`41451fa7-82d9-4673-8fa5-69eff5a761fd` です。</span><span class="sxs-lookup"><span data-stu-id="30c55-216">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="30c55-217">通常、`api://` ベースの API スコープを持つ検証済みのパブリッシャー ドメインを持つアプリでは、通常、`Audience` を明示的に構成する必要は **ありません**。</span><span class="sxs-lookup"><span data-stu-id="30c55-217">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="30c55-218">詳細については、「<xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30c55-218">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="30c55-219">承認ポリシー</span><span class="sxs-lookup"><span data-stu-id="30c55-219">Authorization policies</span></span>

<span data-ttu-id="30c55-220">グループ オブジェクト ID と [AAD 管理者ロール オブジェクト ID](#aad-administrator-role-object-ids) に基づいて、*Server* アプリの `Startup.ConfigureServices` (`Startup.cs`) で、AAD セキュリティ グループと AAD 管理者ロールに対する [承認ポリシー](xref:security/authorization/policies)を作成します。</span><span class="sxs-lookup"><span data-stu-id="30c55-220">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="30c55-221">たとえば、Azure の課金管理者ロール ポリシーには、次の構成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="30c55-221">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="30c55-222">詳細については、<xref:security/authorization/policies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30c55-222">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="30c55-223">コントローラーのアクセス</span><span class="sxs-lookup"><span data-stu-id="30c55-223">Controller access</span></span>

<span data-ttu-id="30c55-224">*Server* アプリのコントローラーに対するポリシーが必要です。</span><span class="sxs-lookup"><span data-stu-id="30c55-224">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="30c55-225">次の例では、`BillingDataController` からの課金データへのアクセスを、「[承認ポリシー](#authorization-policies)」セクションで構成したように、`BillingAdmin` というポリシー名を持つ Azure 課金管理者に制限しています。</span><span class="sxs-lookup"><span data-stu-id="30c55-225">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="30c55-226">パッケージ</span><span class="sxs-lookup"><span data-stu-id="30c55-226">Packages</span></span>

<span data-ttu-id="30c55-227">次のパッケージの *Server* アプリにパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-227">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="30c55-228">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="30c55-228">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="30c55-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="30c55-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="30c55-230">サービス</span><span class="sxs-lookup"><span data-stu-id="30c55-230">Services</span></span>

<span data-ttu-id="30c55-231">*Server* アプリの `Startup.ConfigureServices` メソッドでは、*Server* アプリの `Startup` クラスのコードに追加の名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="30c55-231">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="30c55-232">次の名前空間を `Startup.cs` に追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-232">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="30c55-233"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> を構成する場合:</span><span class="sxs-lookup"><span data-stu-id="30c55-233">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="30c55-234">必要に応じて <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> の処理を含めます。</span><span class="sxs-lookup"><span data-stu-id="30c55-234">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="30c55-235">たとえば、アプリでは失敗した認証をログに記録できます。</span><span class="sxs-lookup"><span data-stu-id="30c55-235">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="30c55-236"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> で、Graph API 呼び出しを行ってユーザーのグループとロールを取得します。</span><span class="sxs-lookup"><span data-stu-id="30c55-236">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="30c55-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> によって、メッセージのログ記録に個人を特定できる情報 (PII) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="30c55-238">PII は、テスト ユーザー アカウントを使用したデバッグに対してのみアクティブにしてください。</span><span class="sxs-lookup"><span data-stu-id="30c55-238">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="30c55-239">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="30c55-239">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="30c55-240">前のコードでは、次のトークン エラーの処理を省略できます。</span><span class="sxs-lookup"><span data-stu-id="30c55-240">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="30c55-241">`MsalUiRequiredException`: アプリには、十分なアクセス許可 (スコープ) がありません。</span><span class="sxs-lookup"><span data-stu-id="30c55-241">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="30c55-242">Azure portal 内のサーバー API アプリ スコープに、`Directory.Read.All` の **アプリケーション** アクセス許可が含まれているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="30c55-242">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="30c55-243">テナント管理者にアプリへのアクセス許可が付与されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30c55-243">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="30c55-244">`MsalServiceException` (`AADSTS70011`):スコープが `https://graph.microsoft.com/.default` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30c55-244">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="30c55-245">パッケージ</span><span class="sxs-lookup"><span data-stu-id="30c55-245">Packages</span></span>

<span data-ttu-id="30c55-246">次のパッケージの *Server* アプリにパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-246">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="30c55-247">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="30c55-247">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="30c55-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="30c55-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="30c55-249">サービス構成</span><span class="sxs-lookup"><span data-stu-id="30c55-249">Service configuration</span></span>

<span data-ttu-id="30c55-250">*Server* アプリの `Startup.ConfigureServices` メソッドに、Graph API 呼び出しを行い、ユーザーのセキュリティ グループとロールに対するユーザー `group` 要求を確立するためのロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="30c55-250">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="30c55-251">このセクションのコード例では、Microsoft Identity Platform v1.0 に基づく Active Directory 認証ライブラリ (ADAL) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="30c55-251">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="30c55-252">*Server* アプリの `Startup` クラスのコードには、追加の名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="30c55-252">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="30c55-253">次の一連の `using` ステートメントには、このセクションで後述するコードに必要な名前空間が含まれています。</span><span class="sxs-lookup"><span data-stu-id="30c55-253">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="30c55-254"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> を構成する場合:</span><span class="sxs-lookup"><span data-stu-id="30c55-254">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="30c55-255">必要に応じて <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> の処理を含めます。</span><span class="sxs-lookup"><span data-stu-id="30c55-255">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="30c55-256">たとえば、アプリでは失敗した認証をログに記録できます。</span><span class="sxs-lookup"><span data-stu-id="30c55-256">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="30c55-257"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> で、Graph API 呼び出しを行ってユーザーのグループとロールを取得します。</span><span class="sxs-lookup"><span data-stu-id="30c55-257">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="30c55-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> によって、メッセージのログ記録に個人を特定できる情報 (PII) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="30c55-259">PII は、テスト ユーザー アカウントを使用したデバッグに対してのみアクティブにしてください。</span><span class="sxs-lookup"><span data-stu-id="30c55-259">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="30c55-260">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="30c55-260">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="30c55-261">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="30c55-261">In the preceding example:</span></span>

* <span data-ttu-id="30c55-262">アクセス トークンが既に ADAL トークン キャッシュに格納されている可能性があるため、最初にサイレント トークンの取得 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) が試行されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-262">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="30c55-263">キャッシュからトークンを取得する方が、新しいトークンを要求するよりも高速です。</span><span class="sxs-lookup"><span data-stu-id="30c55-263">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="30c55-264">アクセス トークンがキャッシュから取得されない場合 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> または <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> がスローされた場合) は、クライアント資格情報 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) を使用してユーザー アサーション (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) を作成し、ユーザーの代わりにトークンを取得します (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>)。</span><span class="sxs-lookup"><span data-stu-id="30c55-264">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="30c55-265">次に、`Microsoft.Graph.GraphServiceClient` により、トークンを使用して Graph API 呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="30c55-265">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="30c55-266">トークンは ADAL トークン キャッシュに格納されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-266">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="30c55-267">その後同じユーザーに対して Graph API を呼び出す場合、トークンは <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> を使用してキャッシュからサイレントに取得されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-267">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="30c55-268"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> のコードでは、推移的なメンバーシップが取得されません。</span><span class="sxs-lookup"><span data-stu-id="30c55-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="30c55-269">直接的および推移的なメンバーシップを取得するようにコードを変更するには:</span><span class="sxs-lookup"><span data-stu-id="30c55-269">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="30c55-270">次のコード行に対して:</span><span class="sxs-lookup"><span data-stu-id="30c55-270">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="30c55-271">前の行を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="30c55-271">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="30c55-272">次のコード行に対して:</span><span class="sxs-lookup"><span data-stu-id="30c55-272">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="30c55-273">前の行を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="30c55-273">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="30c55-274"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> のコードでは、要求の作成時に AAD セキュリティ グループと AAD 管理者ロールが区別されません。</span><span class="sxs-lookup"><span data-stu-id="30c55-274">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="30c55-275">アプリでグループとロールを区別するには、グループとロールを反復処理するときに `entry.ODataType` をチェックします。</span><span class="sxs-lookup"><span data-stu-id="30c55-275">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="30c55-276">セキュリティ グループとロールの要求を別々に作成するには、次のようなコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="30c55-276">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="30c55-277">ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="30c55-277">User-defined roles</span></span>

<span data-ttu-id="30c55-278">ユーザー定義ロールを使用するように、AAD 登録済みアプリを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="30c55-278">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="30c55-279">`roles` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)」を Azure ドキュメントで参照してください。</span><span class="sxs-lookup"><span data-stu-id="30c55-279">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="30c55-280">以下の例では、次の 2 つのロールがアプリに構成されているものとします。</span><span class="sxs-lookup"><span data-stu-id="30c55-280">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="30c55-281">Azure AD Premium アカウントがないとセキュリティ グループにロールを割り当てることはできませんが、ユーザーをロールに割り当てて、Standard Azure アカウントを使用するユーザーに対する `roles` 要求を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="30c55-281">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="30c55-282">このセクションのガイダンスでは、Azure AD Premium アカウントは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="30c55-282">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="30c55-283">Azure portal で各追加ロール割り当てに対して **_ユーザーを再追加する_** ことにより、複数のロールを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="30c55-283">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="30c55-284">AAD によって送信される単一の `roles` 要求では、ユーザー定義のロールは JSON 配列内の `appRoles` の `value` として示されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-284">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="30c55-285">アプリでは、ロールの JSON 配列を個々の `role` 要求に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="30c55-285">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="30c55-286">[ユーザー定義グループと AAD 管理者ロール](#user-defined-groups-and-administrator-roles)に関するセクションで示されている `CustomUserFactory` は、JSON 配列値を持つ `roles` 要求に対して動作するように設定されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-286">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="30c55-287">[ユーザー定義グループと AAD 管理者ロール](#user-defined-groups-and-administrator-roles)に関するセクションで示されているように、ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリに `CustomUserFactory` を追加して登録します。</span><span class="sxs-lookup"><span data-stu-id="30c55-287">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="30c55-288">元の `roles` 要求はフレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="30c55-288">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="30c55-289">ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリの `Program.Main` で、ロール要求として "`role`" という名前の要求を指定します。</span><span class="sxs-lookup"><span data-stu-id="30c55-289">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="30c55-290">この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="30c55-290">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="30c55-291">コンポーネント内のすべての承認メカニズムで、`admin` ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="30c55-291">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="30c55-292">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="30c55-292">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="30c55-293">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="30c55-293">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="30c55-294">[手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="30c55-294">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="30c55-295">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="30c55-295">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="30c55-296">AAD 管理者ロールのオブジェクト ID</span><span class="sxs-lookup"><span data-stu-id="30c55-296">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="30c55-297">次の表で示されているオブジェクト ID は、`group` 要求に対する[ポリシー](xref:security/authorization/policies)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="30c55-297">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="30c55-298">ポリシーでは、アプリ内のさまざまなアクティビティについてユーザーを承認することをアプリに許可します。</span><span class="sxs-lookup"><span data-stu-id="30c55-298">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="30c55-299">詳細については、[ユーザー定義グループと AAD 管理者ロール](#user-defined-groups-and-administrator-roles)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="30c55-299">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="30c55-300">AAD 管理者ロール</span><span class="sxs-lookup"><span data-stu-id="30c55-300">AAD Administrator Role</span></span> | <span data-ttu-id="30c55-301">Object ID</span><span class="sxs-lookup"><span data-stu-id="30c55-301">Object ID</span></span>
--- | ---
<span data-ttu-id="30c55-302">アプリケーション管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-302">Application administrator</span></span> | <span data-ttu-id="30c55-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="30c55-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="30c55-304">アプリケーション開発者</span><span class="sxs-lookup"><span data-stu-id="30c55-304">Application developer</span></span> | <span data-ttu-id="30c55-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="30c55-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="30c55-306">認証管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-306">Authentication administrator</span></span> | <span data-ttu-id="30c55-307">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="30c55-307">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="30c55-308">Azure DevOps 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-308">Azure DevOps administrator</span></span> | <span data-ttu-id="30c55-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="30c55-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="30c55-310">Azure Information Protection 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-310">Azure Information Protection administrator</span></span> | <span data-ttu-id="30c55-311">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="30c55-311">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="30c55-312">B2C IEF キーセット管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-312">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="30c55-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="30c55-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="30c55-314">B2C IEF ポリシー管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-314">B2C IEF Policy administrator</span></span> | <span data-ttu-id="30c55-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="30c55-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="30c55-316">B2C ユーザー フロー管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-316">B2C user flow administrator</span></span> | <span data-ttu-id="30c55-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="30c55-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="30c55-318">B2C ユーザー フロー属性管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-318">B2C user flow attribute administrator</span></span> | <span data-ttu-id="30c55-319">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="30c55-319">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="30c55-320">課金管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-320">Billing administrator</span></span> | <span data-ttu-id="30c55-321">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="30c55-321">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="30c55-322">クラウド アプリケーション管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-322">Cloud application administrator</span></span> | <span data-ttu-id="30c55-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="30c55-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="30c55-324">クラウド デバイス管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-324">Cloud device administrator</span></span> | <span data-ttu-id="30c55-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="30c55-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="30c55-326">コンプライアンス管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-326">Compliance administrator</span></span> | <span data-ttu-id="30c55-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="30c55-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="30c55-328">コンプライアンス データ管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-328">Compliance data administrator</span></span> | <span data-ttu-id="30c55-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="30c55-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="30c55-330">条件付きアクセス管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-330">Conditional Access administrator</span></span> | <span data-ttu-id="30c55-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="30c55-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="30c55-332">カスタマー ロックボックスのアクセス承認者</span><span class="sxs-lookup"><span data-stu-id="30c55-332">Customer LockBox access approver</span></span> | <span data-ttu-id="30c55-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="30c55-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="30c55-334">デスクトップ Analytics 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-334">Desktop Analytics administrator</span></span> | <span data-ttu-id="30c55-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="30c55-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="30c55-336">ディレクトリ閲覧者</span><span class="sxs-lookup"><span data-stu-id="30c55-336">Directory readers</span></span> | <span data-ttu-id="30c55-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="30c55-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="30c55-338">Dynamics 365 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-338">Dynamics 365 administrator</span></span> | <span data-ttu-id="30c55-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="30c55-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="30c55-340">Exchange 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-340">Exchange administrator</span></span> | <span data-ttu-id="30c55-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="30c55-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="30c55-342">外部 IdentityID プロバイダー管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-342">External Identity Provider administrator</span></span> | <span data-ttu-id="30c55-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="30c55-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="30c55-344">全体管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-344">Global administrator</span></span> | <span data-ttu-id="30c55-345">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="30c55-345">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="30c55-346">グローバル閲覧者</span><span class="sxs-lookup"><span data-stu-id="30c55-346">Global reader</span></span> | <span data-ttu-id="30c55-347">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="30c55-347">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="30c55-348">グループ管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-348">Groups administrator</span></span> | <span data-ttu-id="30c55-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="30c55-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="30c55-350">ゲスト招待元</span><span class="sxs-lookup"><span data-stu-id="30c55-350">Guest inviter</span></span> | <span data-ttu-id="30c55-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="30c55-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="30c55-352">ヘルプデスク管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-352">Helpdesk administrator</span></span> | <span data-ttu-id="30c55-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="30c55-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="30c55-354">Intune 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-354">Intune administrator</span></span> | <span data-ttu-id="30c55-355">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="30c55-355">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="30c55-356">Kaizala 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-356">Kaizala administrator</span></span> | <span data-ttu-id="30c55-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="30c55-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="30c55-358">ライセンス管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-358">License administrator</span></span> | <span data-ttu-id="30c55-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="30c55-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="30c55-360">メッセージ センターのプライバシー閲覧者</span><span class="sxs-lookup"><span data-stu-id="30c55-360">Message center privacy reader</span></span> | <span data-ttu-id="30c55-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="30c55-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="30c55-362">メッセージ センター閲覧者</span><span class="sxs-lookup"><span data-stu-id="30c55-362">Message center reader</span></span> | <span data-ttu-id="30c55-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="30c55-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="30c55-364">Office アプリ管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-364">Office apps administrator</span></span> | <span data-ttu-id="30c55-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="30c55-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="30c55-366">パスワード管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-366">Password administrator</span></span> | <span data-ttu-id="30c55-367">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="30c55-367">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="30c55-368">Power BI 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-368">Power BI administrator</span></span> | <span data-ttu-id="30c55-369">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="30c55-369">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="30c55-370">Power Platform 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-370">Power platform administrator</span></span> | <span data-ttu-id="30c55-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="30c55-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="30c55-372">特権認証管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-372">Privileged authentication administrator</span></span> | <span data-ttu-id="30c55-373">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="30c55-373">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="30c55-374">特権ロール管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-374">Privileged role administrator</span></span> | <span data-ttu-id="30c55-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="30c55-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="30c55-376">レポート閲覧者</span><span class="sxs-lookup"><span data-stu-id="30c55-376">Reports reader</span></span> | <span data-ttu-id="30c55-377">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="30c55-377">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="30c55-378">Search 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-378">Search administrator</span></span> | <span data-ttu-id="30c55-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="30c55-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="30c55-380">Search エディター</span><span class="sxs-lookup"><span data-stu-id="30c55-380">Search editor</span></span> | <span data-ttu-id="30c55-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="30c55-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="30c55-382">セキュリティ管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-382">Security administrator</span></span> | <span data-ttu-id="30c55-383">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="30c55-383">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="30c55-384">セキュリティ オペレーター</span><span class="sxs-lookup"><span data-stu-id="30c55-384">Security operator</span></span> | <span data-ttu-id="30c55-385">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="30c55-385">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="30c55-386">セキュリティ閲覧者</span><span class="sxs-lookup"><span data-stu-id="30c55-386">Security reader</span></span> | <span data-ttu-id="30c55-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="30c55-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="30c55-388">サービス サポート管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-388">Service support administrator</span></span> | <span data-ttu-id="30c55-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="30c55-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="30c55-390">SharePoint 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-390">SharePoint administrator</span></span> | <span data-ttu-id="30c55-391">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="30c55-391">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="30c55-392">Skype for Business 管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-392">Skype for Business administrator</span></span> | <span data-ttu-id="30c55-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="30c55-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="30c55-394">Teams 通信管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-394">Teams Communications Administrator</span></span> | <span data-ttu-id="30c55-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="30c55-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="30c55-396">Teams 通信サポート エンジニア</span><span class="sxs-lookup"><span data-stu-id="30c55-396">Teams Communications Support Engineer</span></span> | <span data-ttu-id="30c55-397">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="30c55-397">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="30c55-398">Teams 通信スペシャリスト</span><span class="sxs-lookup"><span data-stu-id="30c55-398">Teams Communications Specialist</span></span> | <span data-ttu-id="30c55-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="30c55-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="30c55-400">Teams サービス管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-400">Teams Service Administrator</span></span> | <span data-ttu-id="30c55-401">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="30c55-401">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="30c55-402">ユーザー管理者</span><span class="sxs-lookup"><span data-stu-id="30c55-402">User administrator</span></span> | <span data-ttu-id="30c55-403">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="30c55-403">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30c55-404">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="30c55-404">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>

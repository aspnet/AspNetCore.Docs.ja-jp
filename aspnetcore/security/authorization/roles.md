---
title: ASP.NET Core でのロールベースの承認
author: rick-anderson
description: ロールを承認属性に渡すことによって、ASP.NET Core コントローラーとアクションアクセスを制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051162"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="d0541-103">ASP.NET Core でのロールベースの承認</span><span class="sxs-lookup"><span data-stu-id="d0541-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="d0541-104">Id が作成されると、1つまたは複数のロールに属することができます。</span><span class="sxs-lookup"><span data-stu-id="d0541-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="d0541-105">たとえば、Tracy は管理者ロールとユーザーロールに属している場合がありますが、Scott はユーザーロールのみに属している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d0541-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="d0541-106">これらのロールを作成および管理する方法は、承認プロセスのバッキングストアによって異なります。</span><span class="sxs-lookup"><span data-stu-id="d0541-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="d0541-107">ロールは、 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)クラスの[IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)メソッドを使用して開発者に公開されます。</span><span class="sxs-lookup"><span data-stu-id="d0541-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="d0541-108">ロールチェックの追加</span><span class="sxs-lookup"><span data-stu-id="d0541-108">Adding role checks</span></span>

<span data-ttu-id="d0541-109">ロールベースの承認チェックは宣言型です &mdash; 。開発者は、コントローラーまたはコントローラー内のアクションに対してコード内にそれらを埋め込み、要求されたリソースにアクセスするために、現在のユーザーがメンバーである必要があるロールを指定します。</span><span class="sxs-lookup"><span data-stu-id="d0541-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="d0541-110">たとえば、次のコードは、のアクションへのアクセスを、 `AdministrationController` ロールのメンバーであるユーザーに制限し `Administrator` ます。</span><span class="sxs-lookup"><span data-stu-id="d0541-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="d0541-111">複数のロールは、コンマ区切りの一覧として指定できます。</span><span class="sxs-lookup"><span data-stu-id="d0541-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="d0541-112">このコントローラーにアクセスできるのは、ロールまたはロールのメンバーであるユーザーだけ `HRManager` `Finance` です。</span><span class="sxs-lookup"><span data-stu-id="d0541-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="d0541-113">複数の属性を適用する場合、アクセスするユーザーは、指定されたすべてのロールのメンバーである必要があります。次の例では、ユーザーがとの両方のロールのメンバーである必要があり `PowerUser` `ControlPanelUser` ます。</span><span class="sxs-lookup"><span data-stu-id="d0541-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="d0541-114">アクションレベルで追加のロール承認属性を適用することで、さらにアクセスを制限できます。</span><span class="sxs-lookup"><span data-stu-id="d0541-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="d0541-115">前のコードスニペットでは、ロールのメンバー、 `Administrator` または `PowerUser` ロールはコントローラーとアクションにアクセスできますが、ロール `SetTime` のメンバーだけが `Administrator` アクションにアクセスでき `ShutDown` ます。</span><span class="sxs-lookup"><span data-stu-id="d0541-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="d0541-116">コントローラーをロックダウンすることもできますが、個々のアクションへの認証されていない匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="d0541-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="d0541-117">ページの場合 Razor 、は `AuthorizeAttribute` 次のいずれかの方法で適用できます。</span><span class="sxs-lookup"><span data-stu-id="d0541-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="d0541-118">[規則](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)の使用、または</span><span class="sxs-lookup"><span data-stu-id="d0541-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="d0541-119">`AuthorizeAttribute`をインスタンスに適用し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="d0541-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="d0541-120">フィルター属性 (を含む) は `AuthorizeAttribute` 、PageModel にのみ適用でき、特定のページハンドラーメソッドには適用できません。</span><span class="sxs-lookup"><span data-stu-id="d0541-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="d0541-121">ポリシーベースのロールチェック</span><span class="sxs-lookup"><span data-stu-id="d0541-121">Policy based role checks</span></span>

<span data-ttu-id="d0541-122">ロール要件は、新しいポリシー構文を使用して表すこともできます。ここでは、開発者が承認サービス構成の一部としてスタートアップ時にポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="d0541-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="d0541-123">これは通常、 `ConfigureServices()` *Startup.cs* ファイル内で発生します。</span><span class="sxs-lookup"><span data-stu-id="d0541-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

<span data-ttu-id="d0541-124">ポリシーは、属性のプロパティを使用して適用され `Policy` `AuthorizeAttribute` ます。</span><span class="sxs-lookup"><span data-stu-id="d0541-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="d0541-125">要件に複数の許可されたロールを指定する場合は、メソッドのパラメーターとして指定でき `RequireRole` ます。</span><span class="sxs-lookup"><span data-stu-id="d0541-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="d0541-126">この例 `Administrator` では、、、またはロールに属しているユーザーを承認 `PowerUser` `BackupAdministrator` します。</span><span class="sxs-lookup"><span data-stu-id="d0541-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="d0541-127">役割サービスの追加先 Identity</span><span class="sxs-lookup"><span data-stu-id="d0541-127">Add Role services to Identity</span></span>

<span data-ttu-id="d0541-128">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) を追加します。</span><span class="sxs-lookup"><span data-stu-id="d0541-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end


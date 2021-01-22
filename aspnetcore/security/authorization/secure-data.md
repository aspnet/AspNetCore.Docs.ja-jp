---
title: 承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する
author: rick-anderson
description: 認証によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。 HTTPS、認証、セキュリティ、などが含まれ ASP.NET Core Identity ます。
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689306"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="84035-104">認証によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="84035-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="84035-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="84035-106">[次の pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)を参照</span><span class="sxs-lookup"><span data-stu-id="84035-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84035-107">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="84035-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="84035-108">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="84035-109">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="84035-109">There are three security groups:</span></span>

* <span data-ttu-id="84035-110">**登録されているユーザー** は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="84035-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="84035-111">**管理者** は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="84035-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="84035-112">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="84035-113">**管理者** は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="84035-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="84035-114">このドキュメントの画像は、最新のテンプレートと完全には一致しません。</span><span class="sxs-lookup"><span data-stu-id="84035-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="84035-115">次の図では、user Rick ( `rick@example.com` ) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="84035-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="84035-116">Rick は、承認された連絡先を表示したり、[削除] を **編集** したりするだけで、 /  / 連絡先の新しいリンクを **作成** できます。</span><span class="sxs-lookup"><span data-stu-id="84035-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="84035-117">Rick によって作成された最後のレコードにのみ、 **編集** および **削除** のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="84035-118">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="84035-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="84035-120">次の図で `manager@contoso.com` は、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="84035-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインインしたことを示すスクリーンショット manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="84035-122">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="84035-122">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="84035-124">[ **承認** ] ボタンと [ **却下** ] ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="84035-125">次の図で `admin@contoso.com` は、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="84035-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインインしたことを示すスクリーンショット admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="84035-127">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="84035-127">The administrator has all privileges.</span></span> <span data-ttu-id="84035-128">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="84035-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="84035-129">アプリは、次のモデルを [スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) することによって作成されました `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="84035-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="84035-130">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="84035-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="84035-131">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="84035-132">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="84035-133">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="84035-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84035-134">前提条件</span><span class="sxs-lookup"><span data-stu-id="84035-134">Prerequisites</span></span>

<span data-ttu-id="84035-135">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="84035-135">This tutorial is advanced.</span></span> <span data-ttu-id="84035-136">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-136">You should be familiar with:</span></span>

* [<span data-ttu-id="84035-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84035-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="84035-138">Authentication</span><span class="sxs-lookup"><span data-stu-id="84035-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="84035-139">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="84035-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="84035-140">承認</span><span class="sxs-lookup"><span data-stu-id="84035-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="84035-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="84035-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="84035-142">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="84035-142">The starter and completed app</span></span>

<span data-ttu-id="84035-143">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="84035-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="84035-144">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="84035-145">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="84035-145">The starter app</span></span>

<span data-ttu-id="84035-146">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="84035-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="84035-147">アプリを実行し、[ **Contactmanager** ] リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="84035-148">スターターアプリを作成するには、「 [スターターアプリを作成](#create-the-starter-app)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84035-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="84035-149">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="84035-149">Secure user data</span></span>

<span data-ttu-id="84035-150">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="84035-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="84035-151">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="84035-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="84035-152">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="84035-152">Tie the contact data to the user</span></span>

<span data-ttu-id="84035-153">ASP.NET ユーザー ID を使用すると、 [Identity](xref:security/authentication/identity) ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="84035-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="84035-154">`OwnerID` `ContactStatus` モデルにおよびを追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="84035-155">`OwnerID` データベース内のテーブルのユーザー ID を示し `AspNetUser` [Identity](xref:security/authentication/identity) ます。</span><span class="sxs-lookup"><span data-stu-id="84035-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="84035-156">フィールドは、 `Status` 一般的なユーザーが連絡先を表示できるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="84035-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="84035-157">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="84035-158">役割サービスの追加先 Identity</span><span class="sxs-lookup"><span data-stu-id="84035-158">Add Role services to Identity</span></span>

<span data-ttu-id="84035-159">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) を追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="84035-160">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="84035-160">Require authenticated users</span></span>

<span data-ttu-id="84035-161">ユーザーが認証されるようにするには、フォールバック認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="84035-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="84035-162">前の強調表示されたコードは、 [フォールバック認証ポリシー](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)を設定します。</span><span class="sxs-lookup"><span data-stu-id="84035-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="84035-163">フォールバック認証ポリシーでは、\*\* Razor 認証属性を持つページ、コントローラー、またはアクションメソッドを除き、\* すべての _ ユーザーを認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="84035-164">たとえば、 Razor ページ、コントローラー、アクションメソッド `[AllowAnonymous]` は、 `[Authorize(PolicyName="MyPolicy")]` フォールバック認証ポリシーではなく、適用された認証属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="84035-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 現在のインスタンスにを追加し <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> 、現在のユーザーが認証されるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="84035-166">フォールバック認証ポリシー:</span><span class="sxs-lookup"><span data-stu-id="84035-166">The fallback authentication policy:</span></span>

<span data-ttu-id="84035-167">_ は、認証ポリシーを明示的に指定しないすべての要求に適用されます。</span><span class="sxs-lookup"><span data-stu-id="84035-167">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="84035-168">エンドポイントのルーティングによって提供される要求の場合、これには承認属性を指定しないエンドポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="84035-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="84035-169">[静的ファイル](xref:fundamentals/static-files)など、承認ミドルウェアの後に他のミドルウェアによって提供される要求の場合、ポリシーがすべての要求に適用されます。</span><span class="sxs-lookup"><span data-stu-id="84035-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="84035-170">代替認証ポリシーを設定して、ユーザーが認証を要求するようにすると、新しく追加されたページとコントローラーが保護され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="84035-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="84035-171">既定で認証が必要になることは、新しいコントローラーやページを利用して属性を含めるよりも安全です Razor `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="84035-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="84035-172">クラスには <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> も含まれ <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="84035-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="84035-173">は、 `DefaultPolicy` `[Authorize]` ポリシーが指定されていない場合に、属性で使用されるポリシーです。</span><span class="sxs-lookup"><span data-stu-id="84035-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="84035-174">`[Authorize]` はと異なり、名前付きポリシーを含んでいません `[Authorize(PolicyName="MyPolicy")]` 。</span><span class="sxs-lookup"><span data-stu-id="84035-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="84035-175">ポリシーの詳細については、「」を参照してください <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="84035-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="84035-176">すべてのユーザーの認証を要求する MVC コントローラーとページの別の方法 Razor は、承認フィルターを追加することです。</span><span class="sxs-lookup"><span data-stu-id="84035-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="84035-177">上記のコードでは、承認フィルターを使用します。フォールバックポリシーを設定するには、エンドポイントルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="84035-178">すべてのユーザーが認証されるようにするには、フォールバックポリシーを設定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="84035-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="84035-179">[](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) `Index` `Privacy` 匿名ユーザーが登録前にサイトに関する情報を取得できるように、allowanonymous をおよびページに追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="84035-180">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="84035-180">Configure the test account</span></span>

<span data-ttu-id="84035-181">クラスは、 `SeedData` 管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="84035-182">これらのアカウントのパスワードを設定するには、 [シークレットマネージャーツール](xref:security/app-secrets) を使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="84035-183">プロジェクトディレクトリ ( *Program.cs* を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="84035-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="84035-184">強力なパスワードが指定されていない場合は、が呼び出されると例外がスローされ `SeedData.Initialize` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="84035-185">`Main`テストパスワードを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="84035-186">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="84035-187">クラスのメソッドを更新して、 `Initialize` `SeedData` テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="84035-188">管理者のユーザー ID とを `ContactStatus` 連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="84035-189">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="84035-190">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="84035-191">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="84035-192">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="84035-193">`ContactIsOwnerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="84035-194">は、 `ContactIsOwnerAuthorizationHandler` リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="84035-195">は `ContactIsOwnerAuthorizationHandler` コンテキストを呼び出し [ます。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="84035-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="84035-196">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="84035-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="84035-197">`context.Succeed`要件が満たされた場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="84035-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="84035-198">`Task.CompletedTask`要件を満たしていない場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="84035-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="84035-199">`Task.CompletedTask` が成功または失敗ではない場合は &mdash; 、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="84035-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="84035-200">明示的に失敗する必要がある場合は、コンテキストを返し [ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="84035-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="84035-201">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="84035-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="84035-202">`ContactIsOwnerAuthorizationHandler` 要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="84035-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="84035-203">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-203">Create a manager authorization handler</span></span>

<span data-ttu-id="84035-204">`ContactManagerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="84035-205">は、 `ContactManagerAuthorizationHandler` リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="84035-206">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="84035-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="84035-207">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="84035-208">`ContactAdministratorsAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="84035-209">は、 `ContactAdministratorsAuthorizationHandler` リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="84035-210">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="84035-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="84035-211">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="84035-211">Register the authorization handlers</span></span>

<span data-ttu-id="84035-212">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="84035-213">は、 `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core 上に構築された ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="84035-214">サービスコレクションにハンドラーを登録し `ContactsController` て、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="84035-215">の末尾に次のコードを追加し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="84035-216">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="84035-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="84035-217">これらは EF を使用せず、必要なすべての情報がメソッドのパラメーターに含まれているため、シングルトンになり `Context` `HandleRequirementAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="84035-218">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="84035-218">Support authorization</span></span>

<span data-ttu-id="84035-219">このセクションでは、ページを更新 Razor し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="84035-220">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="84035-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="84035-221">クラスを確認 `ContactOperations` します。</span><span class="sxs-lookup"><span data-stu-id="84035-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="84035-222">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="84035-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="84035-223">連絡先ページの基本クラスを作成する Razor</span><span class="sxs-lookup"><span data-stu-id="84035-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="84035-224">[連絡先] ページで使用されるサービスを含む基本クラスを作成 Razor します。</span><span class="sxs-lookup"><span data-stu-id="84035-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="84035-225">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="84035-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="84035-226">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="84035-226">The preceding code:</span></span>

* <span data-ttu-id="84035-227">`IAuthorizationService`認証ハンドラーにアクセスするためのサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="84035-228">サービスを追加し Identity `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="84035-229">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="84035-230">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-230">Update the CreateModel</span></span>

<span data-ttu-id="84035-231">Create page model コンストラクターを更新して、 `DI_BasePageModel` 基本クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="84035-232">`CreateModel.OnPostAsync`メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="84035-233">モデルにユーザー ID を追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="84035-234">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="84035-235">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-235">Update the IndexModel</span></span>

<span data-ttu-id="84035-236">`OnGetAsync`承認された連絡先だけが一般ユーザーに表示されるように、メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="84035-237">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-237">Update the EditModel</span></span>

<span data-ttu-id="84035-238">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="84035-239">リソース承認が検証されているため、 `[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="84035-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="84035-240">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="84035-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="84035-241">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="84035-242">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="84035-243">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="84035-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="84035-244">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-244">Update the DeleteModel</span></span>

<span data-ttu-id="84035-245">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="84035-246">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="84035-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="84035-247">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="84035-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="84035-248">*ページ/_ViewImports* ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="84035-249">前のマークアップは、いくつかのステートメントを追加し `using` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="84035-250">*Pages/Contacts/Index. cshtml* の **編集** と **削除** のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="84035-251">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="84035-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="84035-252">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="84035-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="84035-253">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="84035-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="84035-254">Razorページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="84035-255">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="84035-255">Update Details</span></span>

<span data-ttu-id="84035-256">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="84035-257">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="84035-258">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="84035-258">Add or remove a user to a role</span></span>

<span data-ttu-id="84035-259">次の情報については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84035-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="84035-260">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="84035-260">Removing privileges from a user.</span></span> <span data-ttu-id="84035-261">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="84035-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="84035-262">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="84035-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="84035-263">チャレンジと禁止の違い</span><span class="sxs-lookup"><span data-stu-id="84035-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="84035-264">このアプリでは、認証された [ユーザーを要求](#rau)する既定のポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="84035-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="84035-265">次のコードでは、匿名ユーザーを許可します。</span><span class="sxs-lookup"><span data-stu-id="84035-265">The following code allows anonymous users.</span></span> <span data-ttu-id="84035-266">匿名ユーザーは、チャレンジと禁止の違いを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="84035-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="84035-267">上記のコードにより、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="84035-267">In the preceding code:</span></span>

* <span data-ttu-id="84035-268">ユーザーが認証 **されていない** 場合は、 `ChallengeResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="84035-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="84035-269">が返されると、 `ChallengeResult` ユーザーはサインインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="84035-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="84035-270">ユーザーが認証されているが承認されていない場合は、 `ForbidResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="84035-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="84035-271">が返されると、ユーザーは [ `ForbidResult` アクセス拒否] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="84035-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="84035-272">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="84035-272">Test the completed app</span></span>

<span data-ttu-id="84035-273">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、 [シークレットマネージャーツール](xref:security/app-secrets#secret-manager) を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="84035-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="84035-274">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="84035-275">たとえば、は `Passw0rd!` 強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="84035-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="84035-276">プロジェクトのフォルダーから次のコマンドを実行します。ここで、 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="84035-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="84035-277">アプリに連絡先がある場合:</span><span class="sxs-lookup"><span data-stu-id="84035-277">If the app has contacts:</span></span>

* <span data-ttu-id="84035-278">テーブル内のすべてのレコードを削除 `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="84035-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="84035-279">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="84035-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="84035-280">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="84035-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="84035-281">1つのブラウザーで、新しいユーザーを登録します (たとえば、 `test@example.com` )。</span><span class="sxs-lookup"><span data-stu-id="84035-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="84035-282">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="84035-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="84035-283">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-283">Verify the following operations:</span></span>

* <span data-ttu-id="84035-284">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="84035-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="84035-285">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="84035-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="84035-286">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="84035-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="84035-287">このビューには、 `Details` [ **承認** ] ボタンと [ **却下** ] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="84035-288">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="84035-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="84035-289">User</span><span class="sxs-lookup"><span data-stu-id="84035-289">User</span></span>                | <span data-ttu-id="84035-290">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="84035-290">Seeded by the app</span></span> | <span data-ttu-id="84035-291">オプション</span><span class="sxs-lookup"><span data-stu-id="84035-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="84035-292">いいえ</span><span class="sxs-lookup"><span data-stu-id="84035-292">No</span></span>                | <span data-ttu-id="84035-293">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="84035-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="84035-294">はい</span><span class="sxs-lookup"><span data-stu-id="84035-294">Yes</span></span>               | <span data-ttu-id="84035-295">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="84035-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="84035-296">はい</span><span class="sxs-lookup"><span data-stu-id="84035-296">Yes</span></span>               | <span data-ttu-id="84035-297">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="84035-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="84035-298">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="84035-299">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="84035-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="84035-300">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="84035-301">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-301">Create the starter app</span></span>

* <span data-ttu-id="84035-302">Razor"ContactManager" という名前のページアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="84035-303">**個々のユーザーアカウント** を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="84035-304">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="84035-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="84035-305">`-uld` SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="84035-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="84035-306">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="84035-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="84035-307">モデルをスキャフォールディング `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="84035-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="84035-308">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="84035-309">コマンドでバグが発生した場合は `dotnet aspnet-codegenerator razorpage` 、 [GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84035-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="84035-310">*Pages/Shared/_Layout cshtml* ファイルで **contactmanager** アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="84035-311">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="84035-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="84035-312">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="84035-312">Seed the database</span></span>

<span data-ttu-id="84035-313">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="84035-314">呼び出し `SeedData.Initialize` 元 `Main` :</span><span class="sxs-lookup"><span data-stu-id="84035-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="84035-315">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="84035-315">Test that the app seeded the database.</span></span> <span data-ttu-id="84035-316">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="84035-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="84035-317">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="84035-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="84035-318">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="84035-319">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="84035-319">There are three security groups:</span></span>

* <span data-ttu-id="84035-320">**登録されているユーザー** は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="84035-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="84035-321">**管理者** は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="84035-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="84035-322">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="84035-323">**管理者** は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="84035-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="84035-324">次の図では、user Rick ( `rick@example.com` ) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="84035-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="84035-325">Rick は、承認された連絡先を表示したり、[削除] を **編集** したりするだけで、 /  / 連絡先の新しいリンクを **作成** できます。</span><span class="sxs-lookup"><span data-stu-id="84035-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="84035-326">Rick によって作成された最後のレコードにのみ、 **編集** および **削除** のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="84035-327">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="84035-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="84035-329">次の図で `manager@contoso.com` は、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="84035-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインインしたことを示すスクリーンショット manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="84035-331">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="84035-331">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="84035-333">[ **承認** ] ボタンと [ **却下** ] ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="84035-334">次の図で `admin@contoso.com` は、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="84035-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインインしたことを示すスクリーンショット admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="84035-336">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="84035-336">The administrator has all privileges.</span></span> <span data-ttu-id="84035-337">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="84035-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="84035-338">アプリは、次のモデルを [スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) することによって作成されました `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="84035-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="84035-339">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="84035-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="84035-340">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="84035-341">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="84035-342">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="84035-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84035-343">前提条件</span><span class="sxs-lookup"><span data-stu-id="84035-343">Prerequisites</span></span>

<span data-ttu-id="84035-344">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="84035-344">This tutorial is advanced.</span></span> <span data-ttu-id="84035-345">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-345">You should be familiar with:</span></span>

* [<span data-ttu-id="84035-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84035-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="84035-347">Authentication</span><span class="sxs-lookup"><span data-stu-id="84035-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="84035-348">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="84035-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="84035-349">承認</span><span class="sxs-lookup"><span data-stu-id="84035-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="84035-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="84035-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="84035-351">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="84035-351">The starter and completed app</span></span>

<span data-ttu-id="84035-352">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="84035-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="84035-353">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="84035-354">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="84035-354">The starter app</span></span>

<span data-ttu-id="84035-355">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="84035-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="84035-356">アプリを実行し、[ **Contactmanager** ] リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="84035-357">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="84035-357">Secure user data</span></span>

<span data-ttu-id="84035-358">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="84035-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="84035-359">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="84035-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="84035-360">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="84035-360">Tie the contact data to the user</span></span>

<span data-ttu-id="84035-361">ASP.NET ユーザー ID を使用すると、 [Identity](xref:security/authentication/identity) ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="84035-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="84035-362">`OwnerID` `ContactStatus` モデルにおよびを追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="84035-363">`OwnerID` データベース内のテーブルのユーザー ID を示し `AspNetUser` [Identity](xref:security/authentication/identity) ます。</span><span class="sxs-lookup"><span data-stu-id="84035-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="84035-364">フィールドは、 `Status` 一般的なユーザーが連絡先を表示できるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="84035-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="84035-365">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="84035-366">役割サービスの追加先 Identity</span><span class="sxs-lookup"><span data-stu-id="84035-366">Add Role services to Identity</span></span>

<span data-ttu-id="84035-367">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) を追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="84035-368">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="84035-368">Require authenticated users</span></span>

<span data-ttu-id="84035-369">ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="84035-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="84035-370">Razorページ、コントローラー、またはアクションメソッドレベルで、属性を使用して認証を無効にすることができ `[AllowAnonymous]` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="84035-371">既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加されたページとコントローラーは保護され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="84035-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="84035-372">既定で認証が必要になることは、新しいコントローラーやページを利用して属性を含めるよりも安全です Razor `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="84035-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="84035-373">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) を Index、About、および Contact の各ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="84035-374">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="84035-374">Configure the test account</span></span>

<span data-ttu-id="84035-375">クラスは、 `SeedData` 管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="84035-376">これらのアカウントのパスワードを設定するには、 [シークレットマネージャーツール](xref:security/app-secrets) を使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="84035-377">プロジェクトディレクトリ ( *Program.cs* を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="84035-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="84035-378">強力なパスワードが指定されていない場合は、が呼び出されると例外がスローされ `SeedData.Initialize` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="84035-379">`Main`テストパスワードを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="84035-380">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="84035-381">クラスのメソッドを更新して、 `Initialize` `SeedData` テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="84035-382">管理者のユーザー ID とを `ContactStatus` 連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="84035-383">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="84035-384">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="84035-385">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="84035-386">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="84035-387">*承認* フォルダーを作成し、 `ContactIsOwnerAuthorizationHandler` そこにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="84035-388">は、 `ContactIsOwnerAuthorizationHandler` リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="84035-389">は `ContactIsOwnerAuthorizationHandler` コンテキストを呼び出し [ます。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="84035-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="84035-390">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="84035-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="84035-391">`context.Succeed`要件が満たされた場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="84035-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="84035-392">`Task.CompletedTask`要件を満たしていない場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="84035-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="84035-393">`Task.CompletedTask` が成功または失敗ではない場合は &mdash; 、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="84035-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="84035-394">明示的に失敗する必要がある場合は、コンテキストを返し [ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="84035-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="84035-395">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="84035-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="84035-396">`ContactIsOwnerAuthorizationHandler` 要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="84035-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="84035-397">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-397">Create a manager authorization handler</span></span>

<span data-ttu-id="84035-398">`ContactManagerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="84035-399">は、 `ContactManagerAuthorizationHandler` リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="84035-400">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="84035-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="84035-401">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="84035-402">`ContactAdministratorsAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="84035-403">は、 `ContactAdministratorsAuthorizationHandler` リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="84035-404">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="84035-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="84035-405">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="84035-405">Register the authorization handlers</span></span>

<span data-ttu-id="84035-406">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="84035-407">は、 `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core 上に構築された ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="84035-408">サービスコレクションにハンドラーを登録し `ContactsController` て、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="84035-409">の末尾に次のコードを追加し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="84035-410">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="84035-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="84035-411">これらは EF を使用せず、必要なすべての情報がメソッドのパラメーターに含まれているため、シングルトンになり `Context` `HandleRequirementAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="84035-412">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="84035-412">Support authorization</span></span>

<span data-ttu-id="84035-413">このセクションでは、ページを更新 Razor し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="84035-414">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="84035-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="84035-415">クラスを確認 `ContactOperations` します。</span><span class="sxs-lookup"><span data-stu-id="84035-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="84035-416">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="84035-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="84035-417">連絡先ページの基本クラスを作成する Razor</span><span class="sxs-lookup"><span data-stu-id="84035-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="84035-418">[連絡先] ページで使用されるサービスを含む基本クラスを作成 Razor します。</span><span class="sxs-lookup"><span data-stu-id="84035-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="84035-419">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="84035-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="84035-420">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="84035-420">The preceding code:</span></span>

* <span data-ttu-id="84035-421">`IAuthorizationService`認証ハンドラーにアクセスするためのサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="84035-422">サービスを追加し Identity `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="84035-423">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="84035-424">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-424">Update the CreateModel</span></span>

<span data-ttu-id="84035-425">Create page model コンストラクターを更新して、 `DI_BasePageModel` 基本クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="84035-426">`CreateModel.OnPostAsync`メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="84035-427">モデルにユーザー ID を追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="84035-428">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="84035-429">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-429">Update the IndexModel</span></span>

<span data-ttu-id="84035-430">`OnGetAsync`承認された連絡先だけが一般ユーザーに表示されるように、メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="84035-431">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-431">Update the EditModel</span></span>

<span data-ttu-id="84035-432">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="84035-433">リソース承認が検証されているため、 `[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="84035-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="84035-434">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="84035-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="84035-435">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="84035-436">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="84035-437">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="84035-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="84035-438">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="84035-438">Update the DeleteModel</span></span>

<span data-ttu-id="84035-439">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="84035-440">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="84035-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="84035-441">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="84035-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="84035-442">すべてのビューで使用できるように、 *views/_ViewImports cshtml* ファイルに承認サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="84035-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="84035-443">前のマークアップは、いくつかのステートメントを追加し `using` ます。</span><span class="sxs-lookup"><span data-stu-id="84035-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="84035-444">*Pages/Contacts/Index. cshtml* の **編集** と **削除** のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="84035-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="84035-445">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="84035-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="84035-446">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="84035-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="84035-447">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="84035-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="84035-448">Razorページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="84035-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="84035-449">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="84035-449">Update Details</span></span>

<span data-ttu-id="84035-450">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="84035-451">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="84035-452">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="84035-452">Add or remove a user to a role</span></span>

<span data-ttu-id="84035-453">次の情報については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84035-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="84035-454">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="84035-454">Removing privileges from a user.</span></span> <span data-ttu-id="84035-455">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="84035-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="84035-456">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="84035-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="84035-457">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="84035-457">Test the completed app</span></span>

<span data-ttu-id="84035-458">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、 [シークレットマネージャーツール](xref:security/app-secrets#secret-manager) を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="84035-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="84035-459">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="84035-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="84035-460">たとえば、は `Passw0rd!` 強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="84035-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="84035-461">プロジェクトのフォルダーから次のコマンドを実行します。ここで、 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="84035-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="84035-462">データベースを削除して更新する</span><span class="sxs-lookup"><span data-stu-id="84035-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="84035-463">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="84035-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="84035-464">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="84035-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="84035-465">1つのブラウザーで、新しいユーザーを登録します (たとえば、 `test@example.com` )。</span><span class="sxs-lookup"><span data-stu-id="84035-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="84035-466">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="84035-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="84035-467">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-467">Verify the following operations:</span></span>

* <span data-ttu-id="84035-468">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="84035-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="84035-469">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="84035-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="84035-470">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="84035-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="84035-471">このビューには、 `Details` [ **承認** ] ボタンと [ **却下** ] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="84035-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="84035-472">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="84035-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="84035-473">User</span><span class="sxs-lookup"><span data-stu-id="84035-473">User</span></span>                | <span data-ttu-id="84035-474">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="84035-474">Seeded by the app</span></span> | <span data-ttu-id="84035-475">オプション</span><span class="sxs-lookup"><span data-stu-id="84035-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="84035-476">いいえ</span><span class="sxs-lookup"><span data-stu-id="84035-476">No</span></span>                | <span data-ttu-id="84035-477">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="84035-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="84035-478">はい</span><span class="sxs-lookup"><span data-stu-id="84035-478">Yes</span></span>               | <span data-ttu-id="84035-479">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="84035-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="84035-480">はい</span><span class="sxs-lookup"><span data-stu-id="84035-480">Yes</span></span>               | <span data-ttu-id="84035-481">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="84035-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="84035-482">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="84035-483">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="84035-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="84035-484">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="84035-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="84035-485">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-485">Create the starter app</span></span>

* <span data-ttu-id="84035-486">Razor"ContactManager" という名前のページアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="84035-487">**個々のユーザーアカウント** を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="84035-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="84035-488">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="84035-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="84035-489">`-uld` SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="84035-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="84035-490">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="84035-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="84035-491">モデルをスキャフォールディング `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="84035-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="84035-492">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="84035-493">*Pages/_Layout cshtml* ファイルで **contactmanager** アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="84035-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="84035-494">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="84035-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="84035-495">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="84035-495">Seed the database</span></span>

<span data-ttu-id="84035-496">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="84035-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="84035-497">呼び出し `SeedData.Initialize` 元 `Main` :</span><span class="sxs-lookup"><span data-stu-id="84035-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="84035-498">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="84035-498">Test that the app seeded the database.</span></span> <span data-ttu-id="84035-499">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="84035-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="84035-500">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="84035-500">Additional resources</span></span>

* [<span data-ttu-id="84035-501">Azure App Service で .NET Core および SQL Database のアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84035-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="84035-502">[ASP.NET Core の承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="84035-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="84035-503">このチュートリアルで紹介するセキュリティ機能の詳細については、このラボを参照してください。</span><span class="sxs-lookup"><span data-stu-id="84035-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="84035-504">カスタムポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="84035-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

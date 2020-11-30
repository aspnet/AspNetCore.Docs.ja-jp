---
title: ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory でセキュリティ保護する
author: guardrex
description: ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory でセキュリティ保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 0542e7556b82c22a8844f4d1f4b2ba852a420246
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025068"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="044cb-103">ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory でセキュリティ保護する</span><span class="sxs-lookup"><span data-stu-id="044cb-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="044cb-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="044cb-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="044cb-105">この記事では、認証用に [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) を使用する[ホステッド Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="044cb-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="044cb-106">AAD 組織ディレクトリのアカウントをサポートするように構成されている Visual Studio で作成された Blazor WebAssembly アプリの場合、現在、プロジェクトの生成時に Visual Studio によってソリューションのプロジェクトまたは Azure portal アプリの登録が正しく構成されません。</span><span class="sxs-lookup"><span data-stu-id="044cb-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure the solution's projects or the Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="044cb-107">これは、Visual Studio の将来のリリースで対処される予定です。</span><span class="sxs-lookup"><span data-stu-id="044cb-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="044cb-108">この記事では、.NET CLI `dotnet new` コマンドを使用してソリューションと Azure アプリ ポータルの登録を作成する方法と、Azure portal でアプリの登録を手動で作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="044cb-108">This article shows how to create the solution and Azure app portal registrations with the .NET CLI `dotnet new` command and by manually creating the app registrations in the Azure portal.</span></span>
>
> <span data-ttu-id="044cb-109">IDE が更新される前に Visual Studio を使用してソリューションと Azure アプリの登録を作成する場合は、**_この記事の各セクション_** を参照し、Visual Studio によってソリューションが作成された後、アプリの構成とアプリの登録を確認または更新します。</span><span class="sxs-lookup"><span data-stu-id="044cb-109">If you prefer to create the solution and Azure app registrations with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the apps' configurations and the apps' registrations after Visual Studio creates the solution.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="044cb-110">AAD でアプリを登録してソリューションを作成する</span><span class="sxs-lookup"><span data-stu-id="044cb-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="044cb-111">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="044cb-111">Create a tenant</span></span>

<span data-ttu-id="044cb-112">「[クイック スタート: テナントを設定する](/azure/active-directory/develop/quickstart-create-new-tenant)」のガイダンスに従って、AAD でテナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="044cb-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="044cb-113">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="044cb-113">Register a server API app</span></span>

<span data-ttu-id="044cb-114">「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app)」のガイダンスおよび以降の Azure AAD のトピックに従って、"*サーバー API アプリ*" 用の AAD アプリを登録した後、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="044cb-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="044cb-115">**[Azure Active Directory]**  >  **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="044cb-116">アプリの **[名前]** を指定します (例: **Blazor Server AAD**)。</span><span class="sxs-lookup"><span data-stu-id="044cb-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="044cb-117">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="044cb-118">このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** (シングル テナント) を選択できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="044cb-119">"*サーバー API アプリ*" の場合、このシナリオでは **[リダイレクト URI]** は必要ないので、ドロップダウンは **[Web]** に設定されたままにして、リダイレクト URI は入力しません。</span><span class="sxs-lookup"><span data-stu-id="044cb-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="044cb-120">**[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="044cb-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="044cb-121">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-121">Select **Register**.</span></span>

<span data-ttu-id="044cb-122">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="044cb-122">Record the following information:</span></span>

* <span data-ttu-id="044cb-123">"*サーバー API アプリ*" のアプリケーション (クライアント) ID (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="044cb-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="044cb-124">ディレクトリ (テナント) ID (例: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="044cb-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="044cb-125">AAD プライマリ、パブリッシャー、テナント ドメイン (`contoso.onmicrosoft.com` など):ドメインは、Azure portal で登録されているアプリに対する **[ブランド]** ブレードの **[パブリッシャー ドメイン]** として使用できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="044cb-126">アプリにはサインインまたはユーザー プロファイルのアクセスは必要ないため、 **[API のアクセス許可]** で、**Microsoft Graph** > **User.Read** アクセス許可を削除します。</span><span class="sxs-lookup"><span data-stu-id="044cb-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="044cb-127">**[API の公開]** で:</span><span class="sxs-lookup"><span data-stu-id="044cb-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="044cb-128">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="044cb-129">**[Save and continue]\(保存して続行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="044cb-130">**[スコープ名]** を指定します (例: `API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="044cb-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="044cb-131">**[管理者の同意の表示名]** を指定します (例: `Access API`)。</span><span class="sxs-lookup"><span data-stu-id="044cb-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="044cb-132">**[管理者の同意の説明]** を指定します (例: `Allows the app to access server app API endpoints.`)。</span><span class="sxs-lookup"><span data-stu-id="044cb-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="044cb-133">**[状態]** が **[有効]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="044cb-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="044cb-134">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-134">Select **Add scope**.</span></span>

<span data-ttu-id="044cb-135">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="044cb-135">Record the following information:</span></span>

* <span data-ttu-id="044cb-136">アプリ ID の URI (例: `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`、`https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`、または指定するカスタム値)</span><span class="sxs-lookup"><span data-stu-id="044cb-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="044cb-137">スコープ名 (例: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="044cb-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="044cb-138">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="044cb-138">Register a client app</span></span>

<span data-ttu-id="044cb-139">「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app)」のガイダンスおよび以降の Azure AAD のトピックに従って、 *`Client`* アプリ用の AAD アプリを登録した後、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="044cb-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="044cb-140">**[Azure Active Directory]** > **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="044cb-141">アプリの **[名前]** を指定します (例: **Blazor クライアント AAD**)。</span><span class="sxs-lookup"><span data-stu-id="044cb-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="044cb-142">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="044cb-143">このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** (シングル テナント) を選択できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="044cb-144">**[リダイレクト URI]** ドロップダウンを **[シングルページ アプリケーション (SPA)]** に設定し、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="044cb-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="044cb-145">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="044cb-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="044cb-146">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="044cb-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="044cb-147">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルの *`Server`* アプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="044cb-148">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="044cb-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="044cb-149">「[アプリを作成する](#create-the-app)」セクションの解説には、IIS Express ユーザーに対してリダイレクト URI の更新を促す注意が示されています。</span><span class="sxs-lookup"><span data-stu-id="044cb-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="044cb-150">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="044cb-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="044cb-151">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-151">Select **Register**.</span></span>

<span data-ttu-id="044cb-152">*`Client`* アプリのアプリケーション (クライアント) ID を記録しておきます (例: `4369008b-21fa-427c-abaa-9b53bf58e538`)。</span><span class="sxs-lookup"><span data-stu-id="044cb-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="044cb-153">**[認証]** > **[プラットフォーム構成]** > **[シングルページ アプリケーション (SPA)]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="044cb-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="044cb-154">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="044cb-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="044cb-155">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスが **オフ** になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="044cb-155">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="044cb-156">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="044cb-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="044cb-157">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="044cb-158">**[Azure Active Directory]** > **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-158">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="044cb-159">アプリの **[名前]** を指定します (例: **Blazor クライアント AAD**)。</span><span class="sxs-lookup"><span data-stu-id="044cb-159">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="044cb-160">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-160">Choose a **Supported account types**.</span></span> <span data-ttu-id="044cb-161">このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** (シングル テナント) を選択できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="044cb-162">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="044cb-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="044cb-163">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="044cb-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="044cb-164">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="044cb-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="044cb-165">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルの *`Server`* アプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="044cb-166">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="044cb-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="044cb-167">「[アプリを作成する](#create-the-app)」セクションの解説には、IIS Express ユーザーに対してリダイレクト URI の更新を促す注意が示されています。</span><span class="sxs-lookup"><span data-stu-id="044cb-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="044cb-168">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="044cb-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="044cb-169">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-169">Select **Register**.</span></span>

<span data-ttu-id="044cb-170">*`Client`* アプリのアプリケーション (クライアント) ID を記録しておきます (例: `4369008b-21fa-427c-abaa-9b53bf58e538`)。</span><span class="sxs-lookup"><span data-stu-id="044cb-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="044cb-171">**[認証]** > **[プラットフォーム構成]** > **[Web]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="044cb-171">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="044cb-172">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="044cb-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="044cb-173">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="044cb-173">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="044cb-174">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="044cb-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="044cb-175">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="044cb-176">**[API のアクセス許可]** で:</span><span class="sxs-lookup"><span data-stu-id="044cb-176">In **API permissions**:</span></span>

1. <span data-ttu-id="044cb-177">アプリに **Microsoft Graph** > **User.Read** アクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="044cb-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="044cb-178">**[アクセス許可の追加]** を選択し、 **[自分の API]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-178">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="044cb-179">**[名前]** 列で "*サーバー API アプリ*" を選択します (例: **Blazor Server AAD**)。</span><span class="sxs-lookup"><span data-stu-id="044cb-179">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="044cb-180">**[API]** の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="044cb-180">Open the **API** list.</span></span>
1. <span data-ttu-id="044cb-181">API へのアクセスを有効にします (例: `API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="044cb-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="044cb-182">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="044cb-182">Select **Add permissions**.</span></span>
1. <span data-ttu-id="044cb-183">**[<テナント名> に管理者の同意を与えます]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="044cb-184">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="044cb-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="044cb-185">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="044cb-185">Create the app</span></span>

<span data-ttu-id="044cb-186">空のフォルダーで、次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルでこのコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="044cb-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="044cb-187">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="044cb-187">Placeholder</span></span>                  | <span data-ttu-id="044cb-188">Azure portal での名前</span><span class="sxs-lookup"><span data-stu-id="044cb-188">Azure portal name</span></span>                                     | <span data-ttu-id="044cb-189">例</span><span class="sxs-lookup"><span data-stu-id="044cb-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="044cb-190">*`Client`* アプリのアプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="044cb-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="044cb-191">スコープ名</span><span class="sxs-lookup"><span data-stu-id="044cb-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="044cb-192">"*サーバー API アプリ*" のアプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="044cb-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="044cb-193">アプリケーション ID URI&dagger;</span><span class="sxs-lookup"><span data-stu-id="044cb-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="044cb-194">プライマリ、パブリッシャー、テナント ドメイン</span><span class="sxs-lookup"><span data-stu-id="044cb-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="044cb-195">ディレクトリ (テナント) ID</span><span class="sxs-lookup"><span data-stu-id="044cb-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="044cb-196">&dagger;`dotnet new` コマンドに渡されるアプリ ID URI 引数に、Blazor WebAssembly テンプレートによって `api://` のスキームが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-196">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="044cb-197">`{SERVER API APP ID URI}` プレースホルダーでアプリ ID URI を指定し、スキームが `api://` の場合は、上記の表の例の値に示すように、引数からスキーム (`api://`) を削除します。</span><span class="sxs-lookup"><span data-stu-id="044cb-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="044cb-198">アプリ ID URI がカスタム値の場合、または他の何らかのスキームである場合 (たとえば、`https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` のような信頼されていない発行元ドメインの `https://`) は、既定のスコープ URI を手動で更新し、テンプレートによって *`Client`* アプリが作成された後で、`api://` スキームを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="044cb-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="044cb-199">詳細については、「[アクセス トークン スコープ](#access-token-scopes)」セクションの注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="044cb-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="044cb-200">Blazor WebAssembly テンプレートは、このようなシナリオに対応するため、ASP.NET Core の今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="044cb-200">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="044cb-201">詳細については、「[Blazor WASM テンプレートでのアプリ ID URI 用の二重スキーム (ホストされた、単一組織) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="044cb-201">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="044cb-202">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="044cb-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="044cb-203">未確認の発行元ドメインがある Azure テナントを使用する場合は、構成の変更が必要になることがあります。詳細については、「[アプリの設定](#app-settings)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="044cb-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="044cb-204">未確認の発行元ドメインがある Azure テナントを使用する場合は、構成の変更が必要になることがあります。詳細については、「[アクセス トークン スコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="044cb-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="044cb-205">Azure portal では、 *`Client`* アプリのプラットフォーム構成の **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="044cb-206">*`Client`* アプリがランダムな IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルの "*サーバー API アプリ*" のプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="044cb-207">ポートが *`Client`* アプリの既知のポートで事前に構成されていない場合は、Azure portal で *`Client`* アプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="044cb-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="044cb-208">*`Server`* アプリの構成</span><span class="sxs-lookup"><span data-stu-id="044cb-208">*`Server`* app configuration</span></span>

<span data-ttu-id="044cb-209">"*このセクションは、ソリューションの **`Server`** アプリに関連しています。* "</span><span class="sxs-lookup"><span data-stu-id="044cb-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="044cb-210">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="044cb-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="044cb-211">Microsoft Identity Platform での ASP.NET Core Web API の呼び出しの認証と承認のサポートは、次のパッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="044cb-212">プレースホルダー `{VERSION}` では、NuGet.org のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="044cb-213">ASP.NET Core Web API の呼び出しの認証と承認のサポートは、[`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="044cb-214">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="044cb-215">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="044cb-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="044cb-216">`AddAuthentication` メソッドにより、アプリ内での認証サービスが設定され、JWT ベアラー ハンドラーが既定の認証方法として構成されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="044cb-217"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> メソッドによって、Microsoft Identity Platform v2.0 を使用して Web API を保護するようにサービスを構成できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-217">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="044cb-218">このメソッドでは、認証オプションを初期化するために必要な設定で、アプリの構成の `AzureAd` セクションが想定されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="044cb-219">`AddAuthentication` メソッドにより、アプリ内での認証サービスが設定され、JWT ベアラー ハンドラーが既定の認証方法として構成されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="044cb-220"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> メソッドにより、Azure Active Directory によって送信されるトークンを検証するために必要な JWT ベアラー ハンドラーの特定のパラメーターが設定されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="044cb-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> と <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> により、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="044cb-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="044cb-222">アプリにより、受信要求のトークンの解析と検証が試みられます。</span><span class="sxs-lookup"><span data-stu-id="044cb-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="044cb-223">適切な資格情報なしで保護されたリソースへのアクセスを試みた要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="044cb-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="044cb-224">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="044cb-224">User.Identity.Name</span></span>

<span data-ttu-id="044cb-225">既定では、 *`Server`* アプリ API により、`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 要求の種類からの値が `User.Identity.Name` に設定されます (例: `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`)。</span><span class="sxs-lookup"><span data-stu-id="044cb-225">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="044cb-226">`name` 要求の種類から値を受け取るようにアプリを構成するには、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> の <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> を構成します。</span><span class="sxs-lookup"><span data-stu-id="044cb-226">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="044cb-227">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="044cb-227">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="044cb-228">`appsettings.json` ファイルには、アクセス トークンの検証に使用される JWT ベアラー ハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="044cb-228">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="044cb-229">例:</span><span class="sxs-lookup"><span data-stu-id="044cb-229">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="044cb-230">`appsettings.json` ファイルには、アクセス トークンの検証に使用される JWT ベアラー ハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="044cb-230">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="044cb-231">例:</span><span class="sxs-lookup"><span data-stu-id="044cb-231">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="044cb-232">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="044cb-232">WeatherForecast controller</span></span>

<span data-ttu-id="044cb-233">WeatherForecast コントローラー (*Controllers/WeatherForecastController.cs*) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がコントローラーに適用されている保護された API が公開されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-233">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="044cb-234">次のことを理解しておくことが **重要** です。</span><span class="sxs-lookup"><span data-stu-id="044cb-234">It's **important** to understand that:</span></span>

* <span data-ttu-id="044cb-235">この API コントローラーの [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、この API を不正アクセスから保護する唯一のものです。</span><span class="sxs-lookup"><span data-stu-id="044cb-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="044cb-236">Blazor WebAssembly アプリで使用される [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、アプリが正しく動作するにはユーザーを承認する必要がある、というアプリへのヒントとしてのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="044cb-236">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="044cb-237">*`Client`* アプリの構成</span><span class="sxs-lookup"><span data-stu-id="044cb-237">*`Client`* app configuration</span></span>

<span data-ttu-id="044cb-238">"*このセクションは、ソリューションの **`Client`** アプリに関連しています。* "</span><span class="sxs-lookup"><span data-stu-id="044cb-238">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="044cb-239">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="044cb-239">Authentication package</span></span>

<span data-ttu-id="044cb-240">職場または学校アカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="044cb-240">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="044cb-241">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="044cb-241">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="044cb-242">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="044cb-242">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="044cb-243">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-243">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="044cb-244">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-244">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="044cb-245">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="044cb-245">Authentication service support</span></span>

<span data-ttu-id="044cb-246">サーバー プロジェクトへの要求を行うときのアクセス トークンが含まれる <xref:System.Net.Http.HttpClient> インスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-246">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="044cb-247">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="044cb-247">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="044cb-248">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.ServerAPI`)。</span><span class="sxs-lookup"><span data-stu-id="044cb-248">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="044cb-249">ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-249">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="044cb-250">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-250">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="044cb-251">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="044cb-251">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="044cb-252"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="044cb-252">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="044cb-253">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal の AAD の構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-253">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="044cb-254">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-254">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="044cb-255">例:</span><span class="sxs-lookup"><span data-stu-id="044cb-255">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="044cb-256">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="044cb-256">Access token scopes</span></span>

<span data-ttu-id="044cb-257">既定のアクセス トークン スコープでは、次のようなアクセス トークン スコープの一覧が表されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-257">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="044cb-258">サインイン要求に既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="044cb-258">Included by default in the sign in request.</span></span>
* <span data-ttu-id="044cb-259">認証直後にアクセス トークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-259">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="044cb-260">すべてのスコープは、Azure Active Directory の規則に従って同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="044cb-260">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="044cb-261">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="044cb-261">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="044cb-262">`dotnet new` コマンドに渡されるアプリ ID URI 引数に、Blazor WebAssembly テンプレートによって `api://` のスキームが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="044cb-262">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="044cb-263">Blazor プロジェクト テンプレートからアプリを生成するときは、既定のアクセス トークン スコープの値で、Azure portal で指定した正しいカスタム アプリ ID URI の値、または次の **いずれか** の形式の値が使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="044cb-263">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="044cb-264">ディレクトリの発行元ドメインが **信頼されている** 場合は、通常、既定のアクセス トークンのスコープは次の例のような値になります。`API.Access` は既定のスコープ名です。</span><span class="sxs-lookup"><span data-stu-id="044cb-264">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="044cb-265">二重スキーム (`api://api://...`) の値を調べます。</span><span class="sxs-lookup"><span data-stu-id="044cb-265">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="044cb-266">二重スキームが存在する場合は、最初の `api://` スキームを値から削除します。</span><span class="sxs-lookup"><span data-stu-id="044cb-266">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="044cb-267">ディレクトリの発行元ドメインが **信頼されていない** 場合は、通常、既定のアクセス トークンのスコープは次の例のような値になります。`API.Access` は既定のスコープ名です。</span><span class="sxs-lookup"><span data-stu-id="044cb-267">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="044cb-268">余分な `api://` スキーム (`api://https://contoso.onmicrosoft.com/...`) の値を調べます。</span><span class="sxs-lookup"><span data-stu-id="044cb-268">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="044cb-269">余分な `api://` スキームが存在する場合は、`api://` スキームを値から削除します。</span><span class="sxs-lookup"><span data-stu-id="044cb-269">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="044cb-270">Blazor WebAssembly テンプレートは、このようなシナリオに対応するため、ASP.NET Core の今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="044cb-270">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="044cb-271">詳細については、「[Blazor WASM テンプレートでのアプリ ID URI 用の二重スキーム (ホストされた、単一組織) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="044cb-271">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="044cb-272">`AdditionalScopesToConsent` を使用して追加のスコープを指定します。</span><span class="sxs-lookup"><span data-stu-id="044cb-272">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="044cb-273">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="044cb-273">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="044cb-274">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="044cb-274">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="044cb-275">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="044cb-275">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="044cb-276">ログイン モード</span><span class="sxs-lookup"><span data-stu-id="044cb-276">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="044cb-277">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="044cb-277">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="044cb-278">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="044cb-278">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="044cb-279">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="044cb-279">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="044cb-280">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="044cb-280">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="044cb-281">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="044cb-281">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="044cb-282">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="044cb-282">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="044cb-283">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="044cb-283">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="044cb-284">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="044cb-284">Run the app</span></span>

<span data-ttu-id="044cb-285">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="044cb-285">Run the app from the Server project.</span></span> <span data-ttu-id="044cb-286">Visual Studio を使用しているときは、次のいずれかを行います。</span><span class="sxs-lookup"><span data-stu-id="044cb-286">When using Visual Studio, either:</span></span>

* <span data-ttu-id="044cb-287">ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="044cb-287">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="044cb-288">**ソリューション エクスプローラー** でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。</span><span class="sxs-lookup"><span data-stu-id="044cb-288">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="044cb-289">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="044cb-289">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="044cb-290">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="044cb-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="044cb-291">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="044cb-291">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

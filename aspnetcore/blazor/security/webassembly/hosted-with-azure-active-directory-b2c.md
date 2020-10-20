---
title: ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory B2C でセキュリティ保護する
author: guardrex
description: ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory B2C でセキュリティ保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2020
no-loc:
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: aa6c865f5fd51d1634bde3ac96e1fddc7216a801
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900948"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="84af5-103">ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory B2C でセキュリティ保護する</span><span class="sxs-lookup"><span data-stu-id="84af5-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="84af5-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="84af5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="84af5-105">この記事では、認証用に [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) を使用する[ホステッド Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="84af5-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="84af5-106">AAD B2C でアプリを登録してソリューションを作成する</span><span class="sxs-lookup"><span data-stu-id="84af5-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="84af5-107">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="84af5-107">Create a tenant</span></span>

<span data-ttu-id="84af5-108">「[チュートリアル: Azure Active Directory B2C テナントの作成](/azure/active-directory-b2c/tutorial-create-tenant)」のガイダンスに従って、AAD B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="84af5-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="84af5-109">AAD B2C インスタンスを記録しておきます (例: 末尾にスラッシュが含まれている `https://contoso.b2clogin.com/`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-109">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="84af5-110">インスタンスは、Azure B2C アプリの登録のスキームとホストです。これは、Azure portal で **[アプリの登録]** ページから **[エンドポイント]** ウィンドウを開くことによって見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="84af5-110">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="84af5-111">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="84af5-111">Register a server API app</span></span>

<span data-ttu-id="84af5-112">「[チュートリアル: Azure Active Directory B2C に Web アプリケーションを登録する](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに従って、"*サーバー API アプリ*" 用の AAD アプリを登録した後、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="84af5-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="84af5-113">**[Azure Active Directory]**  >  **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="84af5-114">アプリの **[名前]** を指定します (例: **Blazor Server AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="84af5-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="84af5-115">**[サポートされているアカウントの種類]** で、マルチテナント オプションを選択します: **[任意の組織ディレクトリ内のアカウントまたは任意の ID プロバイダー。Azure AD B2C でユーザーを認証します。]**</span><span class="sxs-lookup"><span data-stu-id="84af5-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="84af5-116">"*サーバー API アプリ*" の場合、このシナリオでは **[リダイレクト URI]** は必要ないので、ドロップダウンは **[Web]** に設定されたままにして、リダイレクト URI は入力しません。</span><span class="sxs-lookup"><span data-stu-id="84af5-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="84af5-117">**[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84af5-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="84af5-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-118">Select **Register**.</span></span>

<span data-ttu-id="84af5-119">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="84af5-119">Record the following information:</span></span>

* <span data-ttu-id="84af5-120">"*サーバー API アプリ*" のアプリケーション (クライアント) ID (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="84af5-120">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="84af5-121">AAD プライマリ、パブリッシャー、テナント ドメイン (`contoso.onmicrosoft.com` など):ドメインは、Azure portal で登録されているアプリに対する **[ブランド]** ブレードの **[パブリッシャー ドメイン]** として使用できます。</span><span class="sxs-lookup"><span data-stu-id="84af5-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="84af5-122">**[API の公開]** で:</span><span class="sxs-lookup"><span data-stu-id="84af5-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="84af5-123">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="84af5-124">**[Save and continue]\(保存して続行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="84af5-125">**[スコープ名]** を指定します (例: `API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="84af5-126">**[管理者の同意の表示名]** を指定します (例: `Access API`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="84af5-127">**[管理者の同意の説明]** を指定します (例: `Allows the app to access server app API endpoints.`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="84af5-128">**[状態]** が **[有効]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84af5-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="84af5-129">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-129">Select **Add scope**.</span></span>

<span data-ttu-id="84af5-130">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="84af5-130">Record the following information:</span></span>

* <span data-ttu-id="84af5-131">アプリ ID の URI (例: `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`、`https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`、または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="84af5-131">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="84af5-132">スコープ名 (例: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="84af5-132">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="84af5-133">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="84af5-133">Register a client app</span></span>

<span data-ttu-id="84af5-134">「[チュートリアル: Azure Active Directory B2C に Web アプリケーションを登録する](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに再度従って、 *`Client`* アプリに AAD アプリを登録した後、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="84af5-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="84af5-135">**[Azure Active Directory]** > **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="84af5-136">アプリの **[名前]** を指定します (例: **Blazor クライアント AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="84af5-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="84af5-137">**[サポートされているアカウントの種類]** で、マルチテナント オプションを選択します: **[任意の組織ディレクトリ内のアカウントまたは任意の ID プロバイダー。Azure AD B2C でユーザーを認証します。]**</span><span class="sxs-lookup"><span data-stu-id="84af5-137">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="84af5-138">**[リダイレクト URI]** ドロップダウンを **[シングルページ アプリケーション (SPA)]** に設定し、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="84af5-138">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="84af5-139">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="84af5-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="84af5-140">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="84af5-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="84af5-141">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルの *`Server`* アプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="84af5-141">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="84af5-142">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="84af5-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="84af5-143">「[アプリを作成する](#create-the-app)」セクションの解説には、IIS Express ユーザーに対してリダイレクト URI の更新を促す注意が示されています。</span><span class="sxs-lookup"><span data-stu-id="84af5-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="84af5-144">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84af5-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="84af5-145">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-145">Select **Register**.</span></span>

1. <span data-ttu-id="84af5-146">アプリケーション (クライアント) ID を記録しておきます (例: `4369008b-21fa-427c-abaa-9b53bf58e538`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="84af5-147">**[認証]** > **[プラットフォーム構成]** > **[シングルページ アプリケーション (SPA)]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="84af5-147">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="84af5-148">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84af5-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="84af5-149">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスが**オフ**になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84af5-149">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="84af5-150">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="84af5-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="84af5-151">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-151">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="84af5-152">**[Azure Active Directory]** > **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-152">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="84af5-153">アプリの **[名前]** を指定します (例: **Blazor クライアント AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="84af5-153">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="84af5-154">**[サポートされているアカウントの種類]** で、マルチテナント オプションを選択します: **[任意の組織ディレクトリ内のアカウントまたは任意の ID プロバイダー。Azure AD B2C でユーザーを認証します。]**</span><span class="sxs-lookup"><span data-stu-id="84af5-154">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="84af5-155">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="84af5-155">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="84af5-156">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="84af5-156">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="84af5-157">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="84af5-157">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="84af5-158">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルの *`Server`* アプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="84af5-158">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="84af5-159">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="84af5-159">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="84af5-160">「[アプリを作成する](#create-the-app)」セクションの解説には、IIS Express ユーザーに対してリダイレクト URI の更新を促す注意が示されています。</span><span class="sxs-lookup"><span data-stu-id="84af5-160">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="84af5-161">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84af5-161">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="84af5-162">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-162">Select **Register**.</span></span>

<span data-ttu-id="84af5-163">アプリケーション (クライアント) ID を記録しておきます (例: `4369008b-21fa-427c-abaa-9b53bf58e538`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-163">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="84af5-164">**[認証]** > **[プラットフォーム構成]** > **[Web]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="84af5-164">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="84af5-165">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="84af5-165">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="84af5-166">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="84af5-166">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="84af5-167">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="84af5-167">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="84af5-168">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-168">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="84af5-169">**[API のアクセス許可]** で:</span><span class="sxs-lookup"><span data-stu-id="84af5-169">In **API permissions**:</span></span>

1. <span data-ttu-id="84af5-170">**[アクセス許可の追加]** を選択し、 **[自分の API]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-170">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="84af5-171">**[名前]** 列で "*サーバー API アプリ*" を選択します (例: **Blazor Server AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="84af5-171">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="84af5-172">**[API]** の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="84af5-172">Open the **API** list.</span></span>
1. <span data-ttu-id="84af5-173">API へのアクセスを有効にします (例: `API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-173">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="84af5-174">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="84af5-174">Select **Add permissions**.</span></span>
1. <span data-ttu-id="84af5-175">**[<テナント名> に管理者の同意を与えます]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-175">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="84af5-176">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="84af5-176">Select **Yes** to confirm.</span></span>

<span data-ttu-id="84af5-177">**[ホーム]**  >  **[Azure AD B2C]**  >  **[ユーザー フロー]** で、次を行います。</span><span class="sxs-lookup"><span data-stu-id="84af5-177">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="84af5-178">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="84af5-178">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="84af5-179">少なくとも、 **[アプリケーション要求]**  >  **[表示名]** ユーザー属性を選択して、`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) に `context.User.Identity.Name` を設定します。</span><span class="sxs-lookup"><span data-stu-id="84af5-179">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="84af5-180">アプリ用に作成されたサインアップおよびサインイン ユーザーフロー名を記録しておきます (例: `B2C_1_signupsignin`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-180">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="84af5-181">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84af5-181">Create the app</span></span>

<span data-ttu-id="84af5-182">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルでこのコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="84af5-182">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="84af5-183">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="84af5-183">Placeholder</span></span>                   | <span data-ttu-id="84af5-184">Azure portal での名前</span><span class="sxs-lookup"><span data-stu-id="84af5-184">Azure portal name</span></span>                                     | <span data-ttu-id="84af5-185">例</span><span class="sxs-lookup"><span data-stu-id="84af5-185">Example</span></span>                                      |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="84af5-186">インスタンス</span><span class="sxs-lookup"><span data-stu-id="84af5-186">Instance</span></span>                                              | `https://contoso.b2clogin.com/`              |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                               |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="84af5-187">*`Client`* アプリのアプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="84af5-187">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="84af5-188">スコープ名</span><span class="sxs-lookup"><span data-stu-id="84af5-188">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="84af5-189">"*サーバー API アプリ*" のアプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="84af5-189">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="84af5-190">アプリケーション ID URI</span><span class="sxs-lookup"><span data-stu-id="84af5-190">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="84af5-191">サインアップまたはサインインのユーザー フロー</span><span class="sxs-lookup"><span data-stu-id="84af5-191">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                        |
| `{TENANT DOMAIN}`             | <span data-ttu-id="84af5-192">プライマリ、パブリッシャー、テナント ドメイン</span><span class="sxs-lookup"><span data-stu-id="84af5-192">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |

<span data-ttu-id="84af5-193">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="84af5-193">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="84af5-194">ホストされている Blazor テンプレートによって設定されるスコープでは、アプリ ID URI ホストが繰り返される場合があります。</span><span class="sxs-lookup"><span data-stu-id="84af5-194">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="84af5-195">`DefaultAccessTokenScopes` コレクションに対して構成されたスコープが *`Client`* アプリの `Program.Main` (`Program.cs`) で正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="84af5-195">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="84af5-196">Azure portal では、 *`Client`* アプリのプラットフォーム構成の **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-196">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="84af5-197">*`Client`* アプリがランダムな IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルの "*サーバー API アプリ*" のプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="84af5-197">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="84af5-198">ポートが *`Client`* アプリの既知のポートで事前に構成されていない場合は、Azure portal で *`Client`* アプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="84af5-198">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="84af5-199">*`Server`* アプリの構成</span><span class="sxs-lookup"><span data-stu-id="84af5-199">*`Server`* app configuration</span></span>

<span data-ttu-id="84af5-200">"*このセクションは、ソリューションの **`Server`** アプリに関連しています。* "</span><span class="sxs-lookup"><span data-stu-id="84af5-200">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="84af5-201">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="84af5-201">Authentication package</span></span>

<span data-ttu-id="84af5-202">ASP.NET Core Web API の呼び出しの認証と承認のサポートは、[`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-202">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="84af5-203">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) のパッケージの**バージョン履歴**にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="84af5-203">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="84af5-204">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="84af5-204">Authentication service support</span></span>

<span data-ttu-id="84af5-205">`AddAuthentication` メソッドにより、アプリ内での認証サービスが設定され、JWT ベアラー ハンドラーが既定の認証方法として構成されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-205">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="84af5-206"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> メソッドにより、Azure Active Directory B2C によって送信されるトークンを検証するために必要な JWT ベアラー ハンドラーの特定のパラメーターが設定されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-206">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="84af5-207"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> と <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> により、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="84af5-207"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="84af5-208">アプリにより、受信要求のトークンの解析と検証が試みられます。</span><span class="sxs-lookup"><span data-stu-id="84af5-208">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="84af5-209">適切な資格情報なしで保護されたリソースへのアクセスを試みた要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="84af5-209">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="84af5-210">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="84af5-210">User.Identity.Name</span></span>

<span data-ttu-id="84af5-211">既定では、`User.Identity.Name` は設定されません。</span><span class="sxs-lookup"><span data-stu-id="84af5-211">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="84af5-212">`name` 要求の種類から値を受け取るようにアプリを構成するには、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> の <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> を構成します。</span><span class="sxs-lookup"><span data-stu-id="84af5-212">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="84af5-213">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="84af5-213">App settings</span></span>

<span data-ttu-id="84af5-214">`appsettings.json` ファイルには、アクセス トークンの検証に使用される JWT ベアラー ハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="84af5-214">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="84af5-215">例:</span><span class="sxs-lookup"><span data-stu-id="84af5-215">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="84af5-216">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="84af5-216">WeatherForecast controller</span></span>

<span data-ttu-id="84af5-217">WeatherForecast コントローラー (*Controllers/WeatherForecastController.cs*) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がコントローラーに適用されている保護された API が公開されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-217">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="84af5-218">次のことを理解しておくことが**重要**です。</span><span class="sxs-lookup"><span data-stu-id="84af5-218">It's **important** to understand that:</span></span>

* <span data-ttu-id="84af5-219">この API コントローラーの [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、この API を不正アクセスから保護する唯一のものです。</span><span class="sxs-lookup"><span data-stu-id="84af5-219">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="84af5-220">Blazor WebAssembly アプリで使用される [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、アプリが正しく動作するにはユーザーを承認する必要がある、というアプリへのヒントとしてのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="84af5-220">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="84af5-221">*`Client`* アプリの構成</span><span class="sxs-lookup"><span data-stu-id="84af5-221">*`Client`* app configuration</span></span>

<span data-ttu-id="84af5-222">"*このセクションは、ソリューションの **`Client`** アプリに関連しています。* "</span><span class="sxs-lookup"><span data-stu-id="84af5-222">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="84af5-223">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="84af5-223">Authentication package</span></span>

<span data-ttu-id="84af5-224">個人の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="84af5-224">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="84af5-225">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="84af5-225">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="84af5-226">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="84af5-226">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="84af5-227">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) のパッケージの**バージョン履歴**にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="84af5-227">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="84af5-228">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-228">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="84af5-229">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="84af5-229">Authentication service support</span></span>

<span data-ttu-id="84af5-230">サーバー プロジェクトへの要求を行うときのアクセス トークンが含まれる <xref:System.Net.Http.HttpClient> インスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-230">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="84af5-231">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="84af5-231">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="84af5-232">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.ServerAPI`)。</span><span class="sxs-lookup"><span data-stu-id="84af5-232">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="84af5-233">ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-233">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="84af5-234">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-234">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="84af5-235">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="84af5-235">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="84af5-236"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="84af5-236">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="84af5-237">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal の AAD の構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="84af5-237">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="84af5-238">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-238">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="84af5-239">例:</span><span class="sxs-lookup"><span data-stu-id="84af5-239">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="84af5-240">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="84af5-240">Access token scopes</span></span>

<span data-ttu-id="84af5-241">既定のアクセス トークン スコープでは、次のようなアクセス トークン スコープの一覧が表されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-241">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="84af5-242">サインイン要求に既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="84af5-242">Included by default in the sign in request.</span></span>
* <span data-ttu-id="84af5-243">認証直後にアクセス トークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="84af5-243">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="84af5-244">すべてのスコープは、Azure Active Directory の規則に従って同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="84af5-244">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="84af5-245">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="84af5-245">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="84af5-246">`AdditionalScopesToConsent` を使用して追加のスコープを指定します。</span><span class="sxs-lookup"><span data-stu-id="84af5-246">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="84af5-247">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="84af5-247">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="84af5-248">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="84af5-248">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="84af5-249">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="84af5-249">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="84af5-250">ログイン モード</span><span class="sxs-lookup"><span data-stu-id="84af5-250">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="84af5-251">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="84af5-251">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="84af5-252">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="84af5-252">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="84af5-253">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84af5-253">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="84af5-254">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84af5-254">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="84af5-255">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84af5-255">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="84af5-256">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84af5-256">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="84af5-257">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84af5-257">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="84af5-258">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="84af5-258">Run the app</span></span>

<span data-ttu-id="84af5-259">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="84af5-259">Run the app from the Server project.</span></span> <span data-ttu-id="84af5-260">Visual Studio を使用しているときは、次のいずれかを行います。</span><span class="sxs-lookup"><span data-stu-id="84af5-260">When using Visual Studio, either:</span></span>

* <span data-ttu-id="84af5-261">ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="84af5-261">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="84af5-262">**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。</span><span class="sxs-lookup"><span data-stu-id="84af5-262">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="84af5-263">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="84af5-263">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="84af5-264">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="84af5-264">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="84af5-265">チュートリアル: Azure Active Directory B2C テナントを作成する</span><span class="sxs-lookup"><span data-stu-id="84af5-265">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="84af5-266">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="84af5-266">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

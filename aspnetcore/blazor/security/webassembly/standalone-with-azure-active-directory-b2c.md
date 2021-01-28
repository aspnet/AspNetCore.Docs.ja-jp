---
title: Azure Active Directory B2C を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: Azure Active Directory B2C を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する方法について説明します。
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 68b1162ea35b401c47f89b7a930b6f0d5c3ab6c5
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710532"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="77e6a-103">Azure Active Directory B2C を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="77e6a-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="77e6a-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="77e6a-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="77e6a-105">この記事では、認証用に [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) を使用する[スタンドアロン Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

<span data-ttu-id="77e6a-106">テナントを作成するか、[AAD B2C テナントの作成 (Azure ドキュメント)](/azure/active-directory-b2c/tutorial-create-tenant) に関する記事に記載されているガイダンスに従って Azure portal で使用するアプリの既存の B2C テナントを選択します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-106">Create a tenant or identify an existing B2C tenant for the app to use in the Azure portal by following the guidance in the [Create an AAD B2C tenant (Azure documentation)](/azure/active-directory-b2c/tutorial-create-tenant) article.</span></span> <span data-ttu-id="77e6a-107">使用するテナントを作成または特定した直後に、この記事に戻ります。</span><span class="sxs-lookup"><span data-stu-id="77e6a-107">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="77e6a-108">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-108">Record the following information:</span></span>

* <span data-ttu-id="77e6a-109">AAD B2C インスタンス (例: 末尾にスラッシュが含まれている `https://contoso.b2clogin.com/`):インスタンスは、Azure B2C アプリの登録のスキームとホストです。これは、Azure portal で **[アプリの登録]** ページから **[エンドポイント]** ウィンドウを開くことによって見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="77e6a-110">AAD B2C プライマリ、パブリッシャー、テナント ドメイン (`contoso.onmicrosoft.com` など):ドメインは、Azure portal で登録されているアプリに対する **[ブランド]** ブレードの **[パブリッシャー ドメイン]** として使用できます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-110">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="77e6a-111">AAD B2C アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-111">Register an AAD B2C app:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="77e6a-112">**[Azure Active Directory]** > **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="77e6a-113">アプリの **名前** を指定します (例: **Blazor スタンドアロン AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="77e6a-113">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="77e6a-114">**[サポートされているアカウントの種類]** で、マルチテナント オプションを選択します: **[任意の組織ディレクトリ内のアカウントまたは任意の ID プロバイダー。Azure AD B2C でユーザーを認証します。]**</span><span class="sxs-lookup"><span data-stu-id="77e6a-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="77e6a-115">**[リダイレクト URI]** ドロップダウンを **[シングルページ アプリケーション (SPA)]** に設定し、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="77e6a-115">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="77e6a-116">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="77e6a-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="77e6a-117">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="77e6a-118">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="77e6a-119">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="77e6a-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="77e6a-120">このトピックの後半で、IIS Express ユーザーにリダイレクト URI を更新するよう促す注意が表示されます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="77e6a-121">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-121">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="77e6a-122">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-122">Select **Register**.</span></span>

<span data-ttu-id="77e6a-123">アプリケーション (クライアント) ID を記録しておきます (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)。</span><span class="sxs-lookup"><span data-stu-id="77e6a-123">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="77e6a-124">**[認証]** > **[プラットフォーム構成]** > **[シングルページ アプリケーション (SPA)]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="77e6a-124">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="77e6a-125">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-125">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="77e6a-126">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスが **オフ** になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-126">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="77e6a-127">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="77e6a-127">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="77e6a-128">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-128">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="77e6a-129">**[Azure Active Directory]** > **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-129">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="77e6a-130">アプリの **名前** を指定します (例: **Blazor スタンドアロン AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="77e6a-130">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="77e6a-131">**[サポートされているアカウントの種類]** で、マルチテナント オプションを選択します: **[任意の組織ディレクトリ内のアカウントまたは任意の ID プロバイダー。Azure AD B2C でユーザーを認証します。]**</span><span class="sxs-lookup"><span data-stu-id="77e6a-131">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="77e6a-132">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="77e6a-132">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="77e6a-133">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="77e6a-133">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="77e6a-134">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-134">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="77e6a-135">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-135">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="77e6a-136">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="77e6a-136">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="77e6a-137">このトピックの後半で、IIS Express ユーザーにリダイレクト URI を更新するよう促す注意が表示されます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-137">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="77e6a-138">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-138">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="77e6a-139">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-139">Select **Register**.</span></span>

<span data-ttu-id="77e6a-140">アプリケーション (クライアント) ID を記録しておきます (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)。</span><span class="sxs-lookup"><span data-stu-id="77e6a-140">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="77e6a-141">**[認証]** > **[プラットフォーム構成]** > **[Web]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="77e6a-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="77e6a-142">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-142">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="77e6a-143">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="77e6a-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="77e6a-144">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="77e6a-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="77e6a-145">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-145">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="77e6a-146">**[ホーム]**  >  **[Azure AD B2C]**  >  **[ユーザー フロー]** で、次を行います。</span><span class="sxs-lookup"><span data-stu-id="77e6a-146">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="77e6a-147">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="77e6a-147">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="77e6a-148">少なくとも、 **[アプリケーション要求]**  >  **[表示名]** ユーザー属性を選択して、`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) に `context.User.Identity.Name` を設定します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-148">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="77e6a-149">アプリ用に作成されたサインアップおよびサインイン ユーザーフロー名を記録しておきます (例: `B2C_1_signupsignin`)。</span><span class="sxs-lookup"><span data-stu-id="77e6a-149">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="77e6a-150">空のフォルダーで、次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルでこのコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-150">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="77e6a-151">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="77e6a-151">Placeholder</span></span>                   | <span data-ttu-id="77e6a-152">Azure portal での名前</span><span class="sxs-lookup"><span data-stu-id="77e6a-152">Azure portal name</span></span>               | <span data-ttu-id="77e6a-153">例</span><span class="sxs-lookup"><span data-stu-id="77e6a-153">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="77e6a-154">インスタンス</span><span class="sxs-lookup"><span data-stu-id="77e6a-154">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="77e6a-155">アプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="77e6a-155">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="77e6a-156">サインアップまたはサインインのユーザー フロー</span><span class="sxs-lookup"><span data-stu-id="77e6a-156">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="77e6a-157">プライマリ、パブリッシャー、テナント ドメイン</span><span class="sxs-lookup"><span data-stu-id="77e6a-157">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="77e6a-158">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="77e6a-158">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="77e6a-159">Azure portal では、アプリのプラットフォーム構成の **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-159">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="77e6a-160">アプリがランダム IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-160">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="77e6a-161">ポートがアプリの既知のポートで事前に構成されていない場合は、Azure portal でアプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-161">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="77e6a-162">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="77e6a-162">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="77e6a-163">AAD ユーザー アカウントを使用してアプリにログインする。</span><span class="sxs-lookup"><span data-stu-id="77e6a-163">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="77e6a-164">Microsoft API のアクセス トークンを要求する。</span><span class="sxs-lookup"><span data-stu-id="77e6a-164">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="77e6a-165">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="77e6a-165">For more information, see:</span></span>
  * [<span data-ttu-id="77e6a-166">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="77e6a-166">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="77e6a-167">[クイック スタート:Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="77e6a-167">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="77e6a-168">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="77e6a-168">Authentication package</span></span>

<span data-ttu-id="77e6a-169">個人の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="77e6a-169">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="77e6a-170">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="77e6a-170">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="77e6a-171">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-171">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="77e6a-172">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-172">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="77e6a-173">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-173">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="77e6a-174">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="77e6a-174">Authentication service support</span></span>

<span data-ttu-id="77e6a-175">ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-175">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="77e6a-176">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なすべてのサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-176">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="77e6a-177">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="77e6a-177">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="77e6a-178"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-178">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="77e6a-179">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-179">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="77e6a-180">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="77e6a-180">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="77e6a-181">例:</span><span class="sxs-lookup"><span data-stu-id="77e6a-181">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="77e6a-182">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="77e6a-182">Access token scopes</span></span>

<span data-ttu-id="77e6a-183">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="77e6a-183">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="77e6a-184">サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> の既定のアクセス トークン スコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-184">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="77e6a-185">`AdditionalScopesToConsent` を使用して追加のスコープを指定します。</span><span class="sxs-lookup"><span data-stu-id="77e6a-185">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="77e6a-186">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="77e6a-186">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="77e6a-187">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="77e6a-187">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="77e6a-188">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="77e6a-188">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="77e6a-189">ログイン モード</span><span class="sxs-lookup"><span data-stu-id="77e6a-189">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="77e6a-190">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="77e6a-190">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="77e6a-191">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="77e6a-191">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="77e6a-192">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="77e6a-192">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="77e6a-193">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="77e6a-193">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="77e6a-194">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="77e6a-194">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="77e6a-195">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="77e6a-195">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="77e6a-196">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="77e6a-196">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="77e6a-197">Authentication.MSAL JavaScript ライブラリのカスタム バージョンをビルドする</span><span class="sxs-lookup"><span data-stu-id="77e6a-197">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="77e6a-198">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="77e6a-198">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="77e6a-199">チュートリアル: Azure Active Directory B2C テナントを作成する</span><span class="sxs-lookup"><span data-stu-id="77e6a-199">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="77e6a-200">チュートリアル:Azure Active Directory B2C にアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="77e6a-200">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="77e6a-201">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="77e6a-201">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

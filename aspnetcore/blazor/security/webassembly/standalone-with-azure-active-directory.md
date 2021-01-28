---
title: Azure Active Directory を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: Azure Active Directory を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する方法について説明します。
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: eb5bdb046c4360d1cd33a81c569c86b991e7729d
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710542"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="a73ac-103">Azure Active Directory を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="a73ac-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="a73ac-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a73ac-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a73ac-105">この記事では、認証用に [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) を使用する[スタンドアロン Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a73ac-106">AAD 組織ディレクトリのアカウントをサポートするように構成されている Visual Studio で作成された Blazor WebAssembly アプリの場合、現在、プロジェクトの生成時に Visual Studio によって Azure portal アプリの登録が正しく構成されません。</span><span class="sxs-lookup"><span data-stu-id="a73ac-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="a73ac-107">これは、Visual Studio の将来のリリースで対処される予定です。</span><span class="sxs-lookup"><span data-stu-id="a73ac-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="a73ac-108">この記事では、.NET CLI `dotnet new` コマンドを使用してソリューションと Azure アプリ ポータルの登録を作成する方法と、Azure portal でアプリの登録を手動で作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-108">This article shows how to create the solution and Azure app portal registration with the .NET CLI `dotnet new` command and by manually creating the app registration in the Azure portal.</span></span>
>
> <span data-ttu-id="a73ac-109">IDE が更新される前に Visual Studio を使用してソリューションと Azure アプリの登録を作成する場合は、**_この記事の各セクション_** を参照し、Visual Studio によってソリューションが作成された後、アプリの構成とアプリの登録を確認または更新します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-109">If you prefer to create the solution and Azure app registration with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the app's configuration and the app's registration after Visual Studio creates the solution.</span></span>

<span data-ttu-id="a73ac-110">Azure portal の **[Azure Active Directory]** > **[アプリの登録]** 領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-110">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a73ac-111">アプリの **名前** を指定します (例: **Blazor スタンドアロン AAD**)。</span><span class="sxs-lookup"><span data-stu-id="a73ac-111">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="a73ac-112">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="a73ac-113">このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** を選択できます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-113">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="a73ac-114">**[リダイレクト URI]** ドロップダウンを **[シングルページ アプリケーション (SPA)]** に設定し、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="a73ac-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a73ac-115">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="a73ac-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a73ac-116">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a73ac-117">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a73ac-118">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="a73ac-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a73ac-119">このトピックの後半で、IIS Express ユーザーにリダイレクト URI を更新するよう促す注意が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a73ac-120">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="a73ac-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a73ac-121">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-121">Select **Register**.</span></span>

<span data-ttu-id="a73ac-122">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-122">Record the following information:</span></span>

* <span data-ttu-id="a73ac-123">アプリケーション (クライアント) ID (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="a73ac-123">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="a73ac-124">ディレクトリ (テナント) ID (例: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="a73ac-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="a73ac-125">**[認証]** > **[プラットフォーム構成]** > **[シングルページ アプリケーション (SPA)]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="a73ac-125">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="a73ac-126">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a73ac-127">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスが **オフ** になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-127">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="a73ac-128">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="a73ac-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a73ac-129">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-129">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a73ac-130">Azure portal の **[Azure Active Directory]** > **[アプリの登録]** 領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-130">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a73ac-131">アプリの **名前** を指定します (例: **Blazor スタンドアロン AAD**)。</span><span class="sxs-lookup"><span data-stu-id="a73ac-131">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="a73ac-132">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-132">Choose a **Supported account types**.</span></span> <span data-ttu-id="a73ac-133">このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** を選択できます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-133">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="a73ac-134">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="a73ac-134">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a73ac-135">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="a73ac-135">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a73ac-136">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-136">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a73ac-137">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-137">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a73ac-138">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="a73ac-138">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a73ac-139">このトピックの後半で、IIS Express ユーザーにリダイレクト URI を更新するよう促す注意が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-139">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a73ac-140">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="a73ac-140">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a73ac-141">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-141">Select **Register**.</span></span>

<span data-ttu-id="a73ac-142">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-142">Record the following information:</span></span>

* <span data-ttu-id="a73ac-143">アプリケーション (クライアント) ID (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="a73ac-143">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="a73ac-144">ディレクトリ (テナント) ID (例: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="a73ac-144">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="a73ac-145">**[認証]** > **[プラットフォーム構成]** > **[Web]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="a73ac-145">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a73ac-146">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-146">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a73ac-147">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a73ac-147">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a73ac-148">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="a73ac-148">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a73ac-149">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-149">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="a73ac-150">空のフォルダーにアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-150">Create the app in an empty folder.</span></span> <span data-ttu-id="a73ac-151">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルでこのコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-151">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="a73ac-152">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="a73ac-152">Placeholder</span></span>   | <span data-ttu-id="a73ac-153">Azure portal での名前</span><span class="sxs-lookup"><span data-stu-id="a73ac-153">Azure portal name</span></span>       | <span data-ttu-id="a73ac-154">例</span><span class="sxs-lookup"><span data-stu-id="a73ac-154">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="a73ac-155">アプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="a73ac-155">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="a73ac-156">ディレクトリ (テナント) ID</span><span class="sxs-lookup"><span data-stu-id="a73ac-156">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="a73ac-157">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="a73ac-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a73ac-158">Azure portal では、アプリのプラットフォーム構成の **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a73ac-159">アプリがランダム IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a73ac-160">ポートがアプリの既知のポートで事前に構成されていない場合は、Azure portal でアプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="a73ac-161">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="a73ac-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="a73ac-162">AAD ユーザー アカウントを使用してアプリにログインする。</span><span class="sxs-lookup"><span data-stu-id="a73ac-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="a73ac-163">Microsoft API のアクセス トークンを要求する。</span><span class="sxs-lookup"><span data-stu-id="a73ac-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="a73ac-164">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="a73ac-164">For more information, see:</span></span>
  * [<span data-ttu-id="a73ac-165">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="a73ac-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="a73ac-166">[クイック スタート:Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a73ac-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a73ac-167">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="a73ac-167">Authentication package</span></span>

<span data-ttu-id="a73ac-168">職場または学校アカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a73ac-168">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="a73ac-169">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a73ac-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a73ac-170">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="a73ac-171">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="a73ac-172">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a73ac-173">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="a73ac-173">Authentication service support</span></span>

<span data-ttu-id="a73ac-174">ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="a73ac-175">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-175">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a73ac-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a73ac-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="a73ac-177"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a73ac-178">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="a73ac-179">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a73ac-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a73ac-180">例:</span><span class="sxs-lookup"><span data-stu-id="a73ac-180">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="a73ac-181">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="a73ac-181">Access token scopes</span></span>

<span data-ttu-id="a73ac-182">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="a73ac-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a73ac-183">サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> の既定のアクセス トークン スコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a73ac-184">`AdditionalScopesToConsent` を使用して追加のスコープを指定します。</span><span class="sxs-lookup"><span data-stu-id="a73ac-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="a73ac-185">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a73ac-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a73ac-186">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="a73ac-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a73ac-187">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="a73ac-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="a73ac-188">ログイン モード</span><span class="sxs-lookup"><span data-stu-id="a73ac-188">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="a73ac-189">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="a73ac-189">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a73ac-190">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="a73ac-190">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a73ac-191">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a73ac-191">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a73ac-192">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a73ac-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a73ac-193">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a73ac-193">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a73ac-194">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a73ac-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a73ac-195">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a73ac-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a73ac-196">Authentication.MSAL JavaScript ライブラリのカスタム バージョンをビルドする</span><span class="sxs-lookup"><span data-stu-id="a73ac-196">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="a73ac-197">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="a73ac-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="a73ac-198">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="a73ac-198">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)

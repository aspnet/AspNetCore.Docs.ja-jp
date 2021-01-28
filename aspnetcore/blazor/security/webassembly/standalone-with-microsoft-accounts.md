---
title: Microsoft アカウントを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: Microsoft アカウントを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する方法について説明します。
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 268debd51b6828aad0bcfe917bdf95b691ac7365
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710519"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="a550d-103">Microsoft アカウントを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="a550d-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="a550d-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a550d-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a550d-105">この記事では、認証用に [Azure Active Directory (AAD) を使用する Microsoft アカウント](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する [スタンドアロンBlazor WebAssembly ](xref:blazor/hosting-models#blazor-webassembly) アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a550d-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication.</span></span>

<span data-ttu-id="a550d-106">Azure portal の **[Azure Active Directory]**  >  **[アプリの登録]** 領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="a550d-106">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="a550d-107">アプリの **名前** を指定します (例: **Blazor スタンドアロン AAD Microsoft アカウント**)。</span><span class="sxs-lookup"><span data-stu-id="a550d-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="a550d-108">**[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a550d-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="a550d-109">**[リダイレクト URI]** ドロップダウンを **[シングルページ アプリケーション (SPA)]** に設定し、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="a550d-109">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a550d-110">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="a550d-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a550d-111">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="a550d-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a550d-112">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="a550d-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a550d-113">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="a550d-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a550d-114">このトピックの後半で、IIS Express ユーザーにリダイレクト URI を更新するよう促す注意が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a550d-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a550d-115">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="a550d-115">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a550d-116">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a550d-116">Select **Register**.</span></span>

<span data-ttu-id="a550d-117">アプリケーション (クライアント) ID を記録しておきます (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)。</span><span class="sxs-lookup"><span data-stu-id="a550d-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="a550d-118">**[認証]** > **[プラットフォーム構成]** > **[シングルページ アプリケーション (SPA)]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="a550d-118">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="a550d-119">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a550d-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a550d-120">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスが **オフ** になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a550d-120">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="a550d-121">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="a550d-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a550d-122">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a550d-122">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="a550d-123">アプリの **名前** を指定します (例: **Blazor スタンドアロン AAD Microsoft アカウント**)。</span><span class="sxs-lookup"><span data-stu-id="a550d-123">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="a550d-124">**[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a550d-124">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="a550d-125">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="a550d-125">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a550d-126">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="a550d-126">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a550d-127">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="a550d-127">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a550d-128">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="a550d-128">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a550d-129">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="a550d-129">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a550d-130">このトピックの後半で、IIS Express ユーザーにリダイレクト URI を更新するよう促す注意が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a550d-130">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a550d-131">**[アクセス許可]** > **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="a550d-131">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a550d-132">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a550d-132">Select **Register**.</span></span>

<span data-ttu-id="a550d-133">アプリケーション (クライアント) ID を記録しておきます (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)。</span><span class="sxs-lookup"><span data-stu-id="a550d-133">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="a550d-134">**[認証]** > **[プラットフォーム構成]** > **[Web]** で次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="a550d-134">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a550d-135">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a550d-135">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a550d-136">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a550d-136">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a550d-137">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="a550d-137">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a550d-138">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a550d-138">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="a550d-139">アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a550d-139">Create the app.</span></span> <span data-ttu-id="a550d-140">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a550d-140">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="a550d-141">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="a550d-141">Placeholder</span></span>   | <span data-ttu-id="a550d-142">Azure portal での名前</span><span class="sxs-lookup"><span data-stu-id="a550d-142">Azure portal name</span></span>       | <span data-ttu-id="a550d-143">例</span><span class="sxs-lookup"><span data-stu-id="a550d-143">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="a550d-144">アプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="a550d-144">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="a550d-145">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="a550d-145">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a550d-146">Azure portal では、アプリのプラットフォーム構成の **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="a550d-146">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a550d-147">アプリがランダム IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="a550d-147">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a550d-148">ポートがアプリの既知のポートで事前に構成されていない場合は、Azure portal でアプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="a550d-148">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="a550d-149">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="a550d-149">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="a550d-150">Microsoft アカウントを使用してアプリにログインする。</span><span class="sxs-lookup"><span data-stu-id="a550d-150">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="a550d-151">Microsoft API のアクセス トークンを要求する。</span><span class="sxs-lookup"><span data-stu-id="a550d-151">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="a550d-152">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="a550d-152">For more information, see:</span></span>
  * [<span data-ttu-id="a550d-153">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="a550d-153">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="a550d-154">[クイック スタート:Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a550d-154">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a550d-155">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="a550d-155">Authentication package</span></span>

<span data-ttu-id="a550d-156">職場または学校アカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a550d-156">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="a550d-157">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a550d-157">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a550d-158">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="a550d-158">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="a550d-159">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="a550d-159">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="a550d-160">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a550d-160">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a550d-161">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="a550d-161">Authentication service support</span></span>

<span data-ttu-id="a550d-162">ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="a550d-162">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="a550d-163">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なすべてのサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="a550d-163">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a550d-164">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a550d-164">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="a550d-165"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="a550d-165">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a550d-166">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="a550d-166">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="a550d-167">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a550d-167">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a550d-168">例:</span><span class="sxs-lookup"><span data-stu-id="a550d-168">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="a550d-169">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="a550d-169">Access token scopes</span></span>

<span data-ttu-id="a550d-170">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="a550d-170">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a550d-171">サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> の既定のアクセス トークン スコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="a550d-171">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a550d-172">`AdditionalScopesToConsent` を使用して追加のスコープを指定します。</span><span class="sxs-lookup"><span data-stu-id="a550d-172">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="a550d-173">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a550d-173">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a550d-174">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="a550d-174">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a550d-175">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="a550d-175">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="a550d-176">ログイン モード</span><span class="sxs-lookup"><span data-stu-id="a550d-176">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="a550d-177">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="a550d-177">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a550d-178">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="a550d-178">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a550d-179">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a550d-179">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a550d-180">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a550d-180">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a550d-181">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a550d-181">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a550d-182">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a550d-182">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a550d-183">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a550d-183">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a550d-184">Authentication.MSAL JavaScript ライブラリのカスタム バージョンをビルドする</span><span class="sxs-lookup"><span data-stu-id="a550d-184">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="a550d-185">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="a550d-185">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="a550d-186">クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="a550d-186">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="a550d-187">クイック スタート:Web API を公開するようにアプリケーションを構成する</span><span class="sxs-lookup"><span data-stu-id="a550d-187">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

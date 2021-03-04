---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントユーザー認証を統合する方法について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/18/2021
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
uid: security/authentication/google-logins
ms.openlocfilehash: 181ce87e8085839e0fcc0d77010c588ef7a290b1
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110132"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="3c58d-103">ASP.NET Core での Google 外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="3c58d-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="3c58d-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3c58d-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3c58d-105">このチュートリアルでは、 [前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが自分の Google アカウントでサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="3c58d-106">Google API コンソールプロジェクトとクライアント ID を作成する</span><span class="sxs-lookup"><span data-stu-id="3c58d-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="3c58d-107">アプリに [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-107">Add the [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet package to the app.</span></span>
* <span data-ttu-id="3c58d-108">[「Google Sign-In を web アプリに統合する](https://developers.google.com/identity/sign-in/web/sign-in)(google ドキュメント)」のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="3c58d-108">Follow the guidance in [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) (Google documentation).</span></span>
* <span data-ttu-id="3c58d-109">[Google コンソール](https://console.developers.google.com/apis/credentials)の [**資格情報**] ページで、[**資格情報の作成**] [  >  **OAuth クライアント ID**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-109">In the **Credentials** page of the [Google console](https://console.developers.google.com/apis/credentials), select **CREATE CREDENTIALS** > **OAuth client ID**.</span></span>
* <span data-ttu-id="3c58d-110">[ **アプリケーションの種類** ] ダイアログボックスで、[ **Web アプリケーション**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-110">In the **Application type** dialog, select **Web application**.</span></span> <span data-ttu-id="3c58d-111">アプリの **名前** を指定します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-111">Provide a **Name** for the app.</span></span>
* <span data-ttu-id="3c58d-112">[承認された **リダイレクト uri** ] セクションで、[ **uri の追加** ] を選択してリダイレクト uri を設定します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-112">In the **Authorized redirect URIs** section, select **ADD URI** to set the redirect URI.</span></span> <span data-ttu-id="3c58d-113">リダイレクト URI の例: `https://localhost:{PORT}/signin-google` 、 `{PORT}` プレースホルダーはアプリのポートです。</span><span class="sxs-lookup"><span data-stu-id="3c58d-113">Example redirect URI: `https://localhost:{PORT}/signin-google`, where the `{PORT}` placeholder is the app's port.</span></span>
* <span data-ttu-id="3c58d-114">[ **作成** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-114">Select the **CREATE** button.</span></span>
* <span data-ttu-id="3c58d-115">アプリの構成で使用するために、 **クライアント ID** と **クライアントシークレット** を保存します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-115">Save the **Client ID** and **Client Secret** for use in the app's configuration.</span></span>
* <span data-ttu-id="3c58d-116">サイトを展開するときは、次のいずれかの方法を実行します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-116">When deploying the site, either:</span></span>
  * <span data-ttu-id="3c58d-117">**Google Console** のアプリのリダイレクト uri をアプリのデプロイ済みリダイレクト uri に更新します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-117">Update the app's redirect URI in the **Google Console** to the app's deployed redirect URI.</span></span>
  * <span data-ttu-id="3c58d-118">運用環境のリダイレクト URI を使用して、 **Google コンソール** で、実稼働アプリの新しい google API 登録を作成します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-118">Create a new Google API registration in the **Google Console** for the production app with its production redirect URI.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="3c58d-119">Google クライアント ID とシークレットを保存する</span><span class="sxs-lookup"><span data-stu-id="3c58d-119">Store the Google client ID and secret</span></span>

<span data-ttu-id="3c58d-120">Google クライアント ID やシークレット値などの機微な設定を [Secret Manager](xref:security/app-secrets)に保存します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-120">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="3c58d-121">このサンプルでは、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-121">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="3c58d-122">「 [シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-122">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="3c58d-123">秘密キーとシークレットキーを使用して、機密設定をローカルシークレットストアに保存 `Authentication:Google:ClientId` し `Authentication:Google:ClientSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-123">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="3c58d-124">Api [コンソール](https://console.developers.google.com/apis/dashboard)で api の資格情報と使用状況を管理できます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-124">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="3c58d-125">Google 認証を構成する</span><span class="sxs-lookup"><span data-stu-id="3c58d-125">Configure Google authentication</span></span>

<span data-ttu-id="3c58d-126">Google サービスをに追加し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-126">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="3c58d-127">Google でサインイン</span><span class="sxs-lookup"><span data-stu-id="3c58d-127">Sign in with Google</span></span>

* <span data-ttu-id="3c58d-128">アプリを実行し、[ **ログイン**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3c58d-128">Run the app and click **Log in**.</span></span> <span data-ttu-id="3c58d-129">Google でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-129">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="3c58d-130">[ **Google** ] ボタンをクリックします。これにより、認証のために google にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-130">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="3c58d-131">Google の資格情報を入力すると、web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-131">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="3c58d-132"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Google 認証でサポートされる構成オプションの詳細については、「API リファレンス」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c58d-132">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="3c58d-133">これは、ユーザーに関するさまざまな情報を要求するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-133">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="3c58d-134">既定のコールバック URI を変更する</span><span class="sxs-lookup"><span data-stu-id="3c58d-134">Change the default callback URI</span></span>

<span data-ttu-id="3c58d-135">URI セグメント `/signin-google` は、Google 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-135">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="3c58d-136">クラスの "継承された" プロパティを使用して Google authentication ミドルウェアを構成するときに、既定のコールバック URI を変更でき <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-136">You can change the default callback URI while configuring the Google authentication middleware via the inherited <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType>) property of the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3c58d-137">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3c58d-137">Troubleshooting</span></span>

* <span data-ttu-id="3c58d-138">サインインが機能せず、エラーが発生しない場合は、開発モードに切り替えて、問題を簡単にデバッグできるようにします。</span><span class="sxs-lookup"><span data-stu-id="3c58d-138">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="3c58d-139">Identityでを呼び出すことによってが構成されていない場合 `services.AddIdentity` 、ArgumentException で結果を認証しようとしています。 `ConfigureServices` *' SignInScheme ' オプションを指定する必要があり* ます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-139">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="3c58d-140">このチュートリアルで使用するプロジェクトテンプレートによって、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-140">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="3c58d-141">初期移行を適用してサイトデータベースが作成されていない場合は、 *要求エラーの処理中にデータベース操作が失敗* します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-141">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="3c58d-142">[ **移行の適用** ] を選択してデータベースを作成し、ページを更新してエラーを解消します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-142">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3c58d-143">次のステップ</span><span class="sxs-lookup"><span data-stu-id="3c58d-143">Next steps</span></span>

* <span data-ttu-id="3c58d-144">この記事では、Google で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="3c58d-144">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="3c58d-145">同様のアプローチに従って、 [前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="3c58d-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="3c58d-146">アプリを Azure に発行したら、 `ClientSecret` GOOGLE API コンソールでをリセットします。</span><span class="sxs-lookup"><span data-stu-id="3c58d-146">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="3c58d-147">とを `Authentication:Google:ClientId` 、 `Authentication:Google:ClientSecret` Azure portal のアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="3c58d-147">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="3c58d-148">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="3c58d-148">The configuration system is set up to read keys from environment variables.</span></span>

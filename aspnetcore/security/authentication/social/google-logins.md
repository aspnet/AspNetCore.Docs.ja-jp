---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントユーザー認証を統合する方法について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
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
uid: security/authentication/google-logins
ms.openlocfilehash: 111ea7c972778dfd5296d0401c16563aeaa36a63
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060314"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="1818c-103">ASP.NET Core での Google 外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="1818c-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="1818c-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1818c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1818c-105">このチュートリアルでは、 [前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが自分の Google アカウントでサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1818c-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="1818c-106">Google API コンソールプロジェクトとクライアント ID を作成する</span><span class="sxs-lookup"><span data-stu-id="1818c-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="1818c-107">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="1818c-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="1818c-108">[Google Sign-In と web アプリの統合](https://developers.google.com/identity/sign-in/web/sign-in)に移動し、[ **プロジェクトの構成** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1818c-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) and select **Configure a project** .</span></span>
* <span data-ttu-id="1818c-109">[ **OAuth クライアントの構成** ] ダイアログで、[ **Web サーバー** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1818c-109">In the **Configure your OAuth client** dialog, select **Web server** .</span></span>
* <span data-ttu-id="1818c-110">[承認された **リダイレクト uri** のテキスト入力] ボックスで、リダイレクト uri を設定します。</span><span class="sxs-lookup"><span data-stu-id="1818c-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="1818c-111">たとえば、`https://localhost:44312/signin-google` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="1818c-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="1818c-112">**クライアント ID** と **クライアントシークレット** を保存します。</span><span class="sxs-lookup"><span data-stu-id="1818c-112">Save the **Client ID** and **Client Secret** .</span></span>
* <span data-ttu-id="1818c-113">サイトをデプロイするときに、新しいパブリック url を **Google コンソール** から登録します。</span><span class="sxs-lookup"><span data-stu-id="1818c-113">When deploying the site, register the new public url from the **Google Console** .</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="1818c-114">Google クライアント ID とシークレットを保存する</span><span class="sxs-lookup"><span data-stu-id="1818c-114">Store the Google client ID and secret</span></span>

<span data-ttu-id="1818c-115">Google クライアント ID やシークレット値などの機微な設定を [Secret Manager](xref:security/app-secrets)に保存します。</span><span class="sxs-lookup"><span data-stu-id="1818c-115">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="1818c-116">このサンプルでは、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="1818c-116">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="1818c-117">「 [シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="1818c-117">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="1818c-118">秘密キーとシークレットキーを使用して、機密設定をローカルシークレットストアに保存 `Authentication:Google:ClientId` し `Authentication:Google:ClientSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="1818c-118">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="1818c-119">Api [コンソール](https://console.developers.google.com/apis/dashboard)で api の資格情報と使用状況を管理できます。</span><span class="sxs-lookup"><span data-stu-id="1818c-119">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="1818c-120">Google 認証を構成する</span><span class="sxs-lookup"><span data-stu-id="1818c-120">Configure Google authentication</span></span>

<span data-ttu-id="1818c-121">Google サービスをに追加し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="1818c-121">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="1818c-122">Google でサインイン</span><span class="sxs-lookup"><span data-stu-id="1818c-122">Sign in with Google</span></span>

* <span data-ttu-id="1818c-123">アプリを実行し、[ **ログイン** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1818c-123">Run the app and click **Log in** .</span></span> <span data-ttu-id="1818c-124">Google でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1818c-124">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="1818c-125">[ **Google** ] ボタンをクリックします。これにより、認証のために google にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1818c-125">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="1818c-126">Google の資格情報を入力すると、web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1818c-126">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="1818c-127"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Google 認証でサポートされる構成オプションの詳細については、「API リファレンス」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1818c-127">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="1818c-128">これは、ユーザーに関するさまざまな情報を要求するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="1818c-128">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="1818c-129">既定のコールバック URI を変更する</span><span class="sxs-lookup"><span data-stu-id="1818c-129">Change the default callback URI</span></span>

<span data-ttu-id="1818c-130">URI セグメント `/signin-google` は、Google 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="1818c-130">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="1818c-131">[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Google 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="1818c-131">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1818c-132">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="1818c-132">Troubleshooting</span></span>

* <span data-ttu-id="1818c-133">サインインが機能せず、エラーが発生しない場合は、開発モードに切り替えて、問題を簡単にデバッグできるようにします。</span><span class="sxs-lookup"><span data-stu-id="1818c-133">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="1818c-134">Identityでを呼び出すことによってが構成されていない場合 `services.AddIdentity` 、ArgumentException で結果を認証しようとしています。 `ConfigureServices` *' SignInScheme ' オプションを指定する必要があり* ます。</span><span class="sxs-lookup"><span data-stu-id="1818c-134">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="1818c-135">このチュートリアルで使用するプロジェクトテンプレートによって、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="1818c-135">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="1818c-136">初期移行を適用してサイトデータベースが作成されていない場合は、 *要求エラーの処理中にデータベース操作が失敗* します。</span><span class="sxs-lookup"><span data-stu-id="1818c-136">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="1818c-137">[ **移行の適用** ] を選択してデータベースを作成し、ページを更新してエラーを解消します。</span><span class="sxs-lookup"><span data-stu-id="1818c-137">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1818c-138">次のステップ</span><span class="sxs-lookup"><span data-stu-id="1818c-138">Next steps</span></span>

* <span data-ttu-id="1818c-139">この記事では、Google で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="1818c-139">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="1818c-140">同様のアプローチに従って、 [前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="1818c-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="1818c-141">アプリを Azure に発行したら、 `ClientSecret` GOOGLE API コンソールでをリセットします。</span><span class="sxs-lookup"><span data-stu-id="1818c-141">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="1818c-142">とを `Authentication:Google:ClientId` 、 `Authentication:Google:ClientSecret` Azure portal のアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="1818c-142">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="1818c-143">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="1818c-143">The configuration system is set up to read keys from environment variables.</span></span>

---
title: ASP.NET Core の Azure Active Directory B2C を使用したクラウド認証
author: camsoper
description: ASP.NET Core で Azure Active Directory B2C 認証をセットアップする方法について説明します。
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
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
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061393"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="842a4-103">ASP.NET Core の Azure Active Directory B2C を使用したクラウド認証</span><span class="sxs-lookup"><span data-stu-id="842a4-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="842a4-104">作成者: [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="842a4-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="842a4-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) は、web アプリとモバイルアプリのクラウド id 管理ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="842a4-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="842a4-106">このサービスは、クラウドとオンプレミスでホストされているアプリの認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="842a4-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="842a4-107">認証の種類には、個々のアカウント、ソーシャルネットワークアカウント、およびフェデレーションエンタープライズアカウントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="842a4-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="842a4-108">また、Azure AD B2C では、最小構成で multi-factor authentication を提供できます。</span><span class="sxs-lookup"><span data-stu-id="842a4-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="842a4-109">Azure Active Directory (Azure AD) と Azure AD B2C は別々の製品オファリングです。</span><span class="sxs-lookup"><span data-stu-id="842a4-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="842a4-110">Azure AD テナントは組織を表し、Azure AD B2C テナントは証明書利用者アプリケーションで使用される id のコレクションを表します。</span><span class="sxs-lookup"><span data-stu-id="842a4-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="842a4-111">詳細については、「 [Azure AD B2C: よく寄せられる質問 (faq)](/azure/active-directory-b2c/active-directory-b2c-faqs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="842a4-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="842a4-112">このチュートリアルで学習する内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="842a4-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="842a4-113">Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="842a4-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="842a4-114">Azure AD B2C にアプリを登録する</span><span class="sxs-lookup"><span data-stu-id="842a4-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="842a4-115">Visual Studio を使用して、認証に Azure AD B2C テナントを使用するように構成された ASP.NET Core web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="842a4-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="842a4-116">Azure AD B2C テナントの動作を制御するポリシーを構成する</span><span class="sxs-lookup"><span data-stu-id="842a4-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="842a4-117">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="842a4-117">Prerequisites</span></span>

<span data-ttu-id="842a4-118">このチュートリアルでは、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="842a4-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="842a4-119">Microsoft Azure サブスクリプション</span><span class="sxs-lookup"><span data-stu-id="842a4-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="842a4-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="842a4-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="842a4-121">Azure Active Directory B2C テナントを作成する</span><span class="sxs-lookup"><span data-stu-id="842a4-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="842a4-122">[ドキュメントで説明されているように](/azure/active-directory-b2c/active-directory-b2c-get-started)Azure Active Directory B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="842a4-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="842a4-123">このチュートリアルでは、メッセージが表示されたら、テナントと Azure サブスクリプションを関連付けることはできません。</span><span class="sxs-lookup"><span data-stu-id="842a4-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="842a4-124">Azure AD B2C にアプリを登録する</span><span class="sxs-lookup"><span data-stu-id="842a4-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="842a4-125">新しく作成された Azure AD B2C テナントで、「 **web アプリを登録する** 」セクションにある [ドキュメントの手順](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)に従って、アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="842a4-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="842a4-126">[ **Web アプリクライアントシークレットの作成** ] セクションで停止します。</span><span class="sxs-lookup"><span data-stu-id="842a4-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="842a4-127">このチュートリアルでは、クライアントシークレットは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="842a4-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="842a4-128">次の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="842a4-128">Use the following values:</span></span>

| <span data-ttu-id="842a4-129">設定</span><span class="sxs-lookup"><span data-stu-id="842a4-129">Setting</span></span>                       | <span data-ttu-id="842a4-130">値</span><span class="sxs-lookup"><span data-stu-id="842a4-130">Value</span></span>                     | <span data-ttu-id="842a4-131">Notes</span><span class="sxs-lookup"><span data-stu-id="842a4-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="842a4-132">**名前**</span><span class="sxs-lookup"><span data-stu-id="842a4-132">**Name**</span></span>                      | <span data-ttu-id="842a4-133">*&lt;アプリ名&gt;*</span><span class="sxs-lookup"><span data-stu-id="842a4-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="842a4-134">アプリをコンシューマーに説明するアプリの **名前** を入力します。</span><span class="sxs-lookup"><span data-stu-id="842a4-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="842a4-135">**Web アプリ/Web API を含める**</span><span class="sxs-lookup"><span data-stu-id="842a4-135">**Include web app / web API**</span></span> | <span data-ttu-id="842a4-136">はい</span><span class="sxs-lookup"><span data-stu-id="842a4-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="842a4-137">**暗黙的フローを許可する**</span><span class="sxs-lookup"><span data-stu-id="842a4-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="842a4-138">はい</span><span class="sxs-lookup"><span data-stu-id="842a4-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="842a4-139">**応答 URL**</span><span class="sxs-lookup"><span data-stu-id="842a4-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="842a4-140">応答 URL は、アプリが要求したトークンを Azure AD B2C が返すエンドポイントです。</span><span class="sxs-lookup"><span data-stu-id="842a4-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="842a4-141">Visual Studio には、使用する応答 URL が用意されています。</span><span class="sxs-lookup"><span data-stu-id="842a4-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="842a4-142">ここでは、「」と入力してフォームを完成させ `https://localhost:44300/signin-oidc` ます。</span><span class="sxs-lookup"><span data-stu-id="842a4-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="842a4-143">**アプリ ID URI**</span><span class="sxs-lookup"><span data-stu-id="842a4-143">**App ID URI**</span></span>                | <span data-ttu-id="842a4-144">空白のままにします</span><span class="sxs-lookup"><span data-stu-id="842a4-144">Leave blank</span></span>               | <span data-ttu-id="842a4-145">このチュートリアルでは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="842a4-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="842a4-146">**ネイティブ クライアントを含める**</span><span class="sxs-lookup"><span data-stu-id="842a4-146">**Include native client**</span></span>     | <span data-ttu-id="842a4-147">いいえ</span><span class="sxs-lookup"><span data-stu-id="842a4-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="842a4-148">Localhost 以外の応答 URL を設定する場合は、[ [応答 url] の一覧で許可されている内容に関する制約](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)に注意してください。</span><span class="sxs-lookup"><span data-stu-id="842a4-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="842a4-149">アプリが登録されると、テナント内のアプリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="842a4-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="842a4-150">登録したばかりのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="842a4-150">Select the app that was just registered.</span></span> <span data-ttu-id="842a4-151">[ **アプリケーション ID** ] フィールドの右側にある **コピー** アイコンを選択して、クリップボードにコピーします。</span><span class="sxs-lookup"><span data-stu-id="842a4-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="842a4-152">現時点では、Azure AD B2C テナントで構成できるものはありませんが、ブラウザーウィンドウは開いたままにしておきます。</span><span class="sxs-lookup"><span data-stu-id="842a4-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="842a4-153">ASP.NET Core アプリが作成された後は、さらに多くの構成があります。</span><span class="sxs-lookup"><span data-stu-id="842a4-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="842a4-154">Visual Studio での ASP.NET Core アプリの作成</span><span class="sxs-lookup"><span data-stu-id="842a4-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="842a4-155">Visual Studio Web アプリケーションテンプレートは、認証に Azure AD B2C テナントを使用するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="842a4-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="842a4-156">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="842a4-156">In Visual Studio:</span></span>

1. <span data-ttu-id="842a4-157">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="842a4-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="842a4-158">テンプレートの一覧から [ **Web アプリケーション** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="842a4-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="842a4-159">[ **認証の変更** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="842a4-159">Select the **Change Authentication** button.</span></span>
    
    ![[認証の変更] ボタン](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="842a4-161">[ **認証の変更** ] ダイアログで、[ **個別のユーザーアカウント** ] を選択し、ドロップダウンで [ **クラウド内の既存のユーザーストアに接続する** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="842a4-161">In the **Change Authentication** dialog, select **Individual User Accounts** , and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![認証ダイアログの変更](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="842a4-163">次の値を使用して、フォームに入力します。</span><span class="sxs-lookup"><span data-stu-id="842a4-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="842a4-164">設定</span><span class="sxs-lookup"><span data-stu-id="842a4-164">Setting</span></span>                       | <span data-ttu-id="842a4-165">値</span><span class="sxs-lookup"><span data-stu-id="842a4-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="842a4-166">**ドメイン名**</span><span class="sxs-lookup"><span data-stu-id="842a4-166">**Domain Name**</span></span>               | <span data-ttu-id="842a4-167">*&lt;B2C テナントのドメイン名&gt;*</span><span class="sxs-lookup"><span data-stu-id="842a4-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="842a4-168">**アプリケーション ID**</span><span class="sxs-lookup"><span data-stu-id="842a4-168">**Application ID**</span></span>            | <span data-ttu-id="842a4-169">*&lt;クリップボードからアプリケーション ID を貼り付けます&gt;*</span><span class="sxs-lookup"><span data-stu-id="842a4-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="842a4-170">**コールバックパス**</span><span class="sxs-lookup"><span data-stu-id="842a4-170">**Callback Path**</span></span>             | <span data-ttu-id="842a4-171">*&lt;既定値を使用する&gt;*</span><span class="sxs-lookup"><span data-stu-id="842a4-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="842a4-172">**サインアップまたはサインインポリシー**</span><span class="sxs-lookup"><span data-stu-id="842a4-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="842a4-173">**パスワードポリシーのリセット**</span><span class="sxs-lookup"><span data-stu-id="842a4-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="842a4-174">**プロファイルポリシーの編集**</span><span class="sxs-lookup"><span data-stu-id="842a4-174">**Edit profile policy**</span></span>       | <span data-ttu-id="842a4-175">*&lt;空白のままにする&gt;*</span><span class="sxs-lookup"><span data-stu-id="842a4-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="842a4-176">[ **応答 uri** ] の横にある [ **コピー** ] リンクを選択して、応答 uri をクリップボードにコピーします。</span><span class="sxs-lookup"><span data-stu-id="842a4-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="842a4-177">[ **OK]** を選択して [ **認証の変更** ] ダイアログを閉じます。</span><span class="sxs-lookup"><span data-stu-id="842a4-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="842a4-178">[ **OK]** を選択して web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="842a4-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="842a4-179">B2C アプリの登録を完了する</span><span class="sxs-lookup"><span data-stu-id="842a4-179">Finish the B2C app registration</span></span>

<span data-ttu-id="842a4-180">B2C アプリのプロパティを開いたままブラウザーウィンドウに戻ります。</span><span class="sxs-lookup"><span data-stu-id="842a4-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="842a4-181">前に指定した一時的な **応答 URL** を、Visual Studio からコピーした値に変更します。</span><span class="sxs-lookup"><span data-stu-id="842a4-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="842a4-182">ウィンドウの上部にある [ **保存** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="842a4-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="842a4-183">応答 URL をコピーしていない場合は、web プロジェクトのプロパティの [デバッグ] タブにある HTTPS アドレスを使用して、からの [の転送 **パス** ] の値を追加し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="842a4-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json* .</span></span>

## <a name="configure-policies"></a><span data-ttu-id="842a4-184">ポリシーの構成</span><span class="sxs-lookup"><span data-stu-id="842a4-184">Configure policies</span></span>

<span data-ttu-id="842a4-185">Azure AD B2C のドキュメントに記載されている手順に従って、 [サインアップまたはサインインポリシーを作成](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)し、 [パスワードリセットポリシーを作成](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)します。</span><span class="sxs-lookup"><span data-stu-id="842a4-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="842a4-186">**Identity プロバイダー** 、 **サインアップ属性** 、および **アプリケーション要求** に関するドキュメントに記載されている値の例を使用します。</span><span class="sxs-lookup"><span data-stu-id="842a4-186">Use the example values provided in the documentation for **Identity providers** , **Sign-up attributes** , and **Application claims** .</span></span> <span data-ttu-id="842a4-187">ドキュメントで説明されているように、[ **今すぐ実行** ] ボタンを使用してポリシーをテストすることは、オプションです。</span><span class="sxs-lookup"><span data-stu-id="842a4-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="842a4-188">ポリシー名は、Visual Studio の [ **認証の変更** ] ダイアログで使用されていたので、ドキュメントに記載されているとおりに記述してください。</span><span class="sxs-lookup"><span data-stu-id="842a4-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="842a4-189">ポリシー名はで確認でき *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="842a4-189">The policy names can be verified in *appsettings.json* .</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="842a4-190">基になる OpenIdConnectOptions/JwtBearer/オプションを構成する Cookie</span><span class="sxs-lookup"><span data-stu-id="842a4-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="842a4-191">基になるオプションを直接構成するには、で適切な scheme 定数を使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="842a4-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="842a4-192">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="842a4-192">Run the app</span></span>

<span data-ttu-id="842a4-193">Visual Studio で、 **F5** キーを押してアプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="842a4-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="842a4-194">Web アプリが起動したら、[ **同意** する] を選択して、 cookie (メッセージが表示された場合は) の使用を受け入れ、[ **サインイン** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="842a4-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in** .</span></span>

![アプリにサインインする](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="842a4-196">ブラウザーが Azure AD B2C テナントにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="842a4-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="842a4-197">(ポリシーのテストを作成した場合は) 既存のアカウントでサインインするか、[ **今すぐサインアップ** ] を選択して新しいアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="842a4-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="842a4-198">忘れたパスワードをリセットするには、[ **パスワードを忘れ** た場合] リンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="842a4-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C のログイン](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="842a4-200">サインインに成功すると、ブラウザーが web アプリにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="842a4-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Success](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="842a4-202">次のステップ</span><span class="sxs-lookup"><span data-stu-id="842a4-202">Next steps</span></span>

<span data-ttu-id="842a4-203">このチュートリアルでは、以下の内容を学習しました。</span><span class="sxs-lookup"><span data-stu-id="842a4-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="842a4-204">Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="842a4-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="842a4-205">Azure AD B2C にアプリを登録する</span><span class="sxs-lookup"><span data-stu-id="842a4-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="842a4-206">Visual Studio を使用して、認証に Azure AD B2C テナントを使用するように構成された ASP.NET Core Web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="842a4-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="842a4-207">Azure AD B2C テナントの動作を制御するポリシーを構成する</span><span class="sxs-lookup"><span data-stu-id="842a4-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="842a4-208">認証に Azure AD B2C を使用するように ASP.NET Core アプリが構成されたので、 [承認属性](xref:security/authorization/simple) を使用してアプリをセキュリティで保護することができます。</span><span class="sxs-lookup"><span data-stu-id="842a4-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="842a4-209">次のことを学習してアプリの開発を続けます。</span><span class="sxs-lookup"><span data-stu-id="842a4-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="842a4-210">[Azure AD B2C ユーザーインターフェイスをカスタマイズ](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)します。</span><span class="sxs-lookup"><span data-stu-id="842a4-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="842a4-211">[パスワードの複雑さの要件を構成](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)します。</span><span class="sxs-lookup"><span data-stu-id="842a4-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="842a4-212">[多要素認証を有効に](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)します。</span><span class="sxs-lookup"><span data-stu-id="842a4-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="842a4-213">[Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)、 [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)、 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)、 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)、 [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)など、その他の id プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="842a4-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="842a4-214">[Azure AD Graph API を使用](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) して、Azure AD B2C テナントからグループメンバーシップなどの追加のユーザー情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="842a4-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="842a4-215">[Azure AD B2C を使用して ASP.NET Core でビルドされた WEB API をセキュリティで保護する方法について説明](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)します。</span><span class="sxs-lookup"><span data-stu-id="842a4-215">[How to secure a Web API built with ASP.NET Core using the Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span></span>
* <span data-ttu-id="842a4-216">[チュートリアル: Azure Active Directory B2C を使用して、ASP.NET WEB API へのアクセスを許可](/azure/active-directory-b2c/tutorial-web-api-dotnet)します。</span><span class="sxs-lookup"><span data-stu-id="842a4-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>

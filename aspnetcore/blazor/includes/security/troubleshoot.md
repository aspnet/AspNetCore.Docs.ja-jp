---
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
ms.openlocfilehash: 4e7c0e9b0a164e0181af5d6baaedf0669c1c06aa
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551751"
---
## <a name="troubleshoot"></a><span data-ttu-id="5e97d-101">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="5e97d-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="5e97d-102">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="5e97d-102">Common errors</span></span>

* <span data-ttu-id="5e97d-103">AAD で承認されないクライアント</span><span class="sxs-lookup"><span data-stu-id="5e97d-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="5e97d-104">情報:Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] 承認に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="5e97d-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="5e97d-105">次の要件が満たされていません。DenyAnonymousAuthorizationRequirement:認証済みユーザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="5e97d-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="5e97d-106">AAD からのログイン コールバック エラー:</span><span class="sxs-lookup"><span data-stu-id="5e97d-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="5e97d-107">エラー: `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="5e97d-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="5e97d-108">説明: `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="5e97d-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="5e97d-109">このエラーを解決するには:</span><span class="sxs-lookup"><span data-stu-id="5e97d-109">To resolve the error:</span></span>

  1. <span data-ttu-id="5e97d-110">Azure portal で、[アプリのマニフェスト](/azure/active-directory/develop/reference-app-manifest)にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="5e97d-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="5e97d-111">[`allowPublicClient` 属性](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute)を `null` または `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-111">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="5e97d-112">Cookie とサイト データ</span><span class="sxs-lookup"><span data-stu-id="5e97d-112">Cookies and site data</span></span>

<span data-ttu-id="5e97d-113">Cookie とサイト データは、アプリが更新されても保持され、テストやトラブルシューティングに影響する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5e97d-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="5e97d-114">アプリ コードの変更、プロバイダーによるユーザー アカウントの変更、プロバイダー アプリの構成変更を行うときは、次のものをクリアしてください。</span><span class="sxs-lookup"><span data-stu-id="5e97d-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="5e97d-115">ユーザー サインイン cookie</span><span class="sxs-lookup"><span data-stu-id="5e97d-115">User sign-in cookies</span></span>
* <span data-ttu-id="5e97d-116">アプリ cookie</span><span class="sxs-lookup"><span data-stu-id="5e97d-116">App cookies</span></span>
* <span data-ttu-id="5e97d-117">キャッシュおよび保存されたサイト データ</span><span class="sxs-lookup"><span data-stu-id="5e97d-117">Cached and stored site data</span></span>

<span data-ttu-id="5e97d-118">残った cookie とサイト データがテストとトラブルシューティングに影響しないようにする方法を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="5e97d-119">ブラウザーを構成する</span><span class="sxs-lookup"><span data-stu-id="5e97d-119">Configure a browser</span></span>
  * <span data-ttu-id="5e97d-120">ブラウザーが閉じるたびに cookie とサイト データをすべて削除するように構成できることをテストするために、ブラウザーを使用します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="5e97d-121">アプリ、テスト ユーザー、プロバイダー構成が変更されるたびにブラウザーが手動で、または IDE によって閉じられていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="5e97d-122">カスタム コマンドを使用して、Visual Studio でブラウザーをシークレットまたはプライベート モードで開く。</span><span class="sxs-lookup"><span data-stu-id="5e97d-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="5e97d-123">Visual Studio の **[実行]** ボタンをクリックして **[ブラウザーの選択]** ダイアログボックスを開きます。</span><span class="sxs-lookup"><span data-stu-id="5e97d-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="5e97d-124">**[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="5e97d-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="5e97d-125">**[プログラム]** フィールドでブラウザーのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="5e97d-126">次の実行可能パスが、Windows 10 の一般的なインストール場所です。</span><span class="sxs-lookup"><span data-stu-id="5e97d-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="5e97d-127">ブラウザーが別の場所にインストールされている場合、または Windows 10 を使用していない場合は、ブラウザーの実行可能ファイルのパスを指定してください。</span><span class="sxs-lookup"><span data-stu-id="5e97d-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="5e97d-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="5e97d-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="5e97d-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="5e97d-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="5e97d-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="5e97d-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="5e97d-131">**[引数]** フィールドに、ブラウザーをシークレットまたはプライベート モードで開くために使用するコマンドライン オプションを指定します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="5e97d-132">ブラウザーによっては、アプリの URL が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="5e97d-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="5e97d-133">Microsoft Edge:`-inprivate` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="5e97d-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="5e97d-134">Google Chrome:`--incognito --new-window {URL}` を使用します。プレースホルダー `{URL}` は開く URL (`https://localhost:5001` など) です。</span><span class="sxs-lookup"><span data-stu-id="5e97d-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="5e97d-135">Mozilla Firefox:`-private -url {URL}` を使用します。プレースホルダー `{URL}` は開く URL (`https://localhost:5001` など) です。</span><span class="sxs-lookup"><span data-stu-id="5e97d-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="5e97d-136">**[フレンドリ名]** フィールドに名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="5e97d-137">たとえば、`Firefox Auth Testing` のようにします。</span><span class="sxs-lookup"><span data-stu-id="5e97d-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="5e97d-138">**[OK]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="5e97d-139">アプリでテストを繰り返すたびにブラウザー プロファイルを選択する必要がないようにするには、 **[既定値として設定]** ボタンでプロファイルを既定値として設定します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="5e97d-140">アプリ、テスト ユーザー、またはプロバイダー構成が変更されるたびに、ブラウザーが IDE によって閉じられていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="5e97d-141">Server アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="5e97d-141">Run the Server app</span></span>

<span data-ttu-id="5e97d-142">ホストされている Blazor アプリのテストとトラブルシューティングを行うときは、 **`Server`** プロジェクトからアプリを実行していることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="5e97d-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="5e97d-143">たとえば、Visual Studio で、次のいずれかの方法を使用してアプリを起動する前に、Server プロジェクトが **ソリューション エクスプローラー** で強調表示されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="5e97d-144">**[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-144">Select the **Run** button.</span></span>
* <span data-ttu-id="5e97d-145">メニューの、 **[デバッグ]**  >  **[デバッグ開始]** を使用します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="5e97d-146"><kbd>F5</kbd>キーを押します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="5e97d-147">JSON Web トークン (JWT) の内容を検査する</span><span class="sxs-lookup"><span data-stu-id="5e97d-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="5e97d-148">JSON Web トークン (JWT) をデコードするには、Microsoft の [jwt.ms](https://jwt.ms/) ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="5e97d-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="5e97d-149">UI の値がブラウザーに残ることはありません。</span><span class="sxs-lookup"><span data-stu-id="5e97d-149">Values in the UI never leave your browser.</span></span>

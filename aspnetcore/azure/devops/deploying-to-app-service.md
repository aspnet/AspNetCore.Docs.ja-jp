---
title: アプリを App Service にデプロイする - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: ASP.NET Core および Azure を使用した DevOps の最初のステップとして、ASP.NET Core アプリを Azure App Service にデプロイします。
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: 52c4905ecb3a76f1dd10629f834b2b541b698774
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052358"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="32d11-103">App Service にアプリをデプロイする</span><span class="sxs-lookup"><span data-stu-id="32d11-103">Deploy an app to App Service</span></span>

<span data-ttu-id="32d11-104">[Azure App Service](/azure/app-service/) は、Azure の Web ホスティング プラットフォームです。</span><span class="sxs-lookup"><span data-stu-id="32d11-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="32d11-105">Azure App Service への Web アプリのデプロイは、手動または自動プロセスで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="32d11-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="32d11-106">ガイドのこのセクションでは、手動で、コマンド ラインを使用してスクリプトで、または Visual Studio を使用して手動でトリガーできる、デプロイ方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="32d11-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="32d11-107">このセクションでは、次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="32d11-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="32d11-108">サンプル アプリをダウンロードしてビルドします。</span><span class="sxs-lookup"><span data-stu-id="32d11-108">Download and build the sample app.</span></span>
* <span data-ttu-id="32d11-109">Azure Cloud Shell を使用して Azure App Service Web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="32d11-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="32d11-110">Git を使用してサンプル アプリを Azure にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="32d11-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="32d11-111">Visual Studio を使用して変更をアプリにデプロイします。</span><span class="sxs-lookup"><span data-stu-id="32d11-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="32d11-112">ステージング スロットを Web アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="32d11-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="32d11-113">更新プログラムをステージング スロットにデプロイします。</span><span class="sxs-lookup"><span data-stu-id="32d11-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="32d11-114">ステージング スロットと運用スロットをスワップします。</span><span class="sxs-lookup"><span data-stu-id="32d11-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="32d11-115">アプリをダウンロードしてテストする</span><span class="sxs-lookup"><span data-stu-id="32d11-115">Download and test the app</span></span>

<span data-ttu-id="32d11-116">このガイドで使用するアプリは、ビルド済みの ASP.NET Core アプリである [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/) です。</span><span class="sxs-lookup"><span data-stu-id="32d11-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="32d11-117">これは、`Microsoft.SyndicationFeed.ReaderWriter` API を使用して RSS または Atom フィードを取得し、ニュース項目を一覧で表示する Razor Pages アプリです。</span><span class="sxs-lookup"><span data-stu-id="32d11-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="32d11-118">コードを自由に確認してかまいませんが、このアプリに関して特別なことは何もないことを理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="32d11-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="32d11-119">例示だけを目的とするシンプルな ASP.NET Core アプリです。</span><span class="sxs-lookup"><span data-stu-id="32d11-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="32d11-120">次のようにコマンド シェルからコードをダウンロードし、プロジェクトをビルドして、実行します。</span><span class="sxs-lookup"><span data-stu-id="32d11-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="32d11-121">*注:Linux/macOS ユーザーは、パスを適切に変更する必要があります。たとえば、バック スラッシュ (`\`) ではなくスラッシュ (`/`) を使用します。*</span><span class="sxs-lookup"><span data-stu-id="32d11-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="32d11-122">ローカル コンピューター上のフォルダーにコードを複製します。</span><span class="sxs-lookup"><span data-stu-id="32d11-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="32d11-123">作業フォルダーを、作成された *simple-feed-reader* フォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="32d11-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="32d11-124">パッケージを復元し、ソリューションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="32d11-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="32d11-125">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="32d11-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![dotnet run コマンドが成功する](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="32d11-127">ブラウザーを開き、 `http://localhost:5000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="32d11-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="32d11-128">アプリでは、配信フィード URL を入力するか貼り付けて、ニュース項目の一覧を表示することができます。</span><span class="sxs-lookup"><span data-stu-id="32d11-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![RSS フィードのコンテンツが表示されているアプリ](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="32d11-130">アプリが正常に動作していることを確認したら、コマンド シェルで **Ctrl** + **C** キーを押してシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="32d11-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="32d11-131">Azure App Service Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="32d11-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="32d11-132">アプリをデプロイするには、App Service [Web アプリ](/azure/app-service/app-service-web-overview)を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="32d11-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="32d11-133">Web アプリを作成した後、Git を使用してローカル コンピューターからそれをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="32d11-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="32d11-134">[Azure Cloud Shell](https://shell.azure.com/bash) にサインインします。</span><span class="sxs-lookup"><span data-stu-id="32d11-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="32d11-135">メモ:初めてサインインすると、構成ファイル用のストレージ アカウントの作成を求めるメッセージが Cloud Shell に表示されます。</span><span class="sxs-lookup"><span data-stu-id="32d11-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="32d11-136">既定値をそのまま使用するか、一意の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="32d11-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="32d11-137">以下の手順では Cloud Shell を使用します。</span><span class="sxs-lookup"><span data-stu-id="32d11-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="32d11-138">a.</span><span class="sxs-lookup"><span data-stu-id="32d11-138">a.</span></span> <span data-ttu-id="32d11-139">Web アプリの名前を格納する変数を宣言します。</span><span class="sxs-lookup"><span data-stu-id="32d11-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="32d11-140">既定の URL で使用するには、名前が一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="32d11-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="32d11-141">`$RANDOM` Bash 関数を使用して名前を作成すると、一意性が保証され、`webappname99999` の形式になります。</span><span class="sxs-lookup"><span data-stu-id="32d11-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="32d11-142">b.</span><span class="sxs-lookup"><span data-stu-id="32d11-142">b.</span></span> <span data-ttu-id="32d11-143">リソース グループを作成します。</span><span class="sxs-lookup"><span data-stu-id="32d11-143">Create a resource group.</span></span> <span data-ttu-id="32d11-144">リソース グループを使用すると、管理対象の Azure リソースをグループとして集約させることができます。</span><span class="sxs-lookup"><span data-stu-id="32d11-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="32d11-145">`az` コマンドでは [Azure CLI](/cli/azure/) が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="32d11-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="32d11-146">CLI はローカル環境でも実行できますが、Cloud Shell で使用すると、時間と構成が節約されます。</span><span class="sxs-lookup"><span data-stu-id="32d11-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="32d11-147">c.</span><span class="sxs-lookup"><span data-stu-id="32d11-147">c.</span></span> <span data-ttu-id="32d11-148">S1 レベルで App Service プランを作成します。</span><span class="sxs-lookup"><span data-stu-id="32d11-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="32d11-149">App Service プランは、同じ価格レベルを共有する Web アプリをグループ化したものです。</span><span class="sxs-lookup"><span data-stu-id="32d11-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="32d11-150">S1 レベルは無料ではありませんが、ステージング スロット機能に必要です。</span><span class="sxs-lookup"><span data-stu-id="32d11-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="32d11-151">d.</span><span class="sxs-lookup"><span data-stu-id="32d11-151">d.</span></span> <span data-ttu-id="32d11-152">App Service プランを使用して、同じリソース グループ内に Web アプリ リソースを作成します。</span><span class="sxs-lookup"><span data-stu-id="32d11-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="32d11-153">e.</span><span class="sxs-lookup"><span data-stu-id="32d11-153">e.</span></span> <span data-ttu-id="32d11-154">デプロイの資格情報を設定します。</span><span class="sxs-lookup"><span data-stu-id="32d11-154">Set the deployment credentials.</span></span> <span data-ttu-id="32d11-155">これらのデプロイ資格情報は、サブスクリプション内のすべての Web アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="32d11-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="32d11-156">ユーザー名では特殊文字を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="32d11-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="32d11-157">f.</span><span class="sxs-lookup"><span data-stu-id="32d11-157">f.</span></span> <span data-ttu-id="32d11-158">ローカル環境の Git からデプロイを受け入れるように Web アプリを構成し、" *Git デプロイ URL* " を表示します。</span><span class="sxs-lookup"><span data-stu-id="32d11-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="32d11-159">**後で参照するので、この URL を記録しておきます** 。</span><span class="sxs-lookup"><span data-stu-id="32d11-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="32d11-160">g.</span><span class="sxs-lookup"><span data-stu-id="32d11-160">g.</span></span> <span data-ttu-id="32d11-161">" *Web アプリの URL* " を表示します。</span><span class="sxs-lookup"><span data-stu-id="32d11-161">Display the *web app URL*.</span></span> <span data-ttu-id="32d11-162">この URL を参照すると、空の Web アプリが表示されます。</span><span class="sxs-lookup"><span data-stu-id="32d11-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="32d11-163">**後で参照するので、この URL を記録しておきます** 。</span><span class="sxs-lookup"><span data-stu-id="32d11-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="32d11-164">ローカル コンピューターでコマンド シェルを使用して、Web アプリのプロジェクト フォルダー (`.\simple-feed-reader\SimpleFeedReader` など) に移動します。</span><span class="sxs-lookup"><span data-stu-id="32d11-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="32d11-165">次のコマンドを実行し、デプロイ URL にプッシュするように Git を設定します。</span><span class="sxs-lookup"><span data-stu-id="32d11-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="32d11-166">a.</span><span class="sxs-lookup"><span data-stu-id="32d11-166">a.</span></span> <span data-ttu-id="32d11-167">ローカル リポジトリにリモート URL を追加します。</span><span class="sxs-lookup"><span data-stu-id="32d11-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="32d11-168">b.</span><span class="sxs-lookup"><span data-stu-id="32d11-168">b.</span></span> <span data-ttu-id="32d11-169">ローカルの *master* ブランチを、 *azure-prod* リモートの *master* ブランチにプッシュします。</span><span class="sxs-lookup"><span data-stu-id="32d11-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="32d11-170">前に作成したデプロイ資格情報の入力を求められます。</span><span class="sxs-lookup"><span data-stu-id="32d11-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="32d11-171">コマンド シェルで出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="32d11-171">Observe the output in the command shell.</span></span> <span data-ttu-id="32d11-172">Azure によって、ASP.NET Core アプリがリモートでビルドされます。</span><span class="sxs-lookup"><span data-stu-id="32d11-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="32d11-173">ブラウザーで " *Web アプリ URL* " に移動し、アプリがビルドおよびデプロイされたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="32d11-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="32d11-174">`git commit` を使用して、追加の変更をローカル Git リポジトリにコミットできます。</span><span class="sxs-lookup"><span data-stu-id="32d11-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="32d11-175">前の `git push` コマンドを使用すると、これらの変更が Azure にプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="32d11-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="32d11-176">Visual Studio でのデプロイ</span><span class="sxs-lookup"><span data-stu-id="32d11-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="32d11-177">*注:このセクションは Windows のみに適用されます。Linux と macOS のユーザーは、下のステップ 2 で説明されている変更を行う必要があります。ファイルを保存し、`git commit` を使用してローカル リポジトリに変更をコミットします。最後に、最初のセクションと同様に、`git push` を使用して変更をプッシュします。*</span><span class="sxs-lookup"><span data-stu-id="32d11-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="32d11-178">アプリはコマンド シェルから既にデプロイされています。</span><span class="sxs-lookup"><span data-stu-id="32d11-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="32d11-179">Visual Studio の統合ツールを使用して、アプリに更新プログラムをデプロイしてみましょう。</span><span class="sxs-lookup"><span data-stu-id="32d11-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="32d11-180">Visual Studio の使い慣れた UI では、バックグラウンドで、コマンド ライン ツールと同じことが実行されています。</span><span class="sxs-lookup"><span data-stu-id="32d11-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="32d11-181">Visual Studio で *SimpleFeedReader.sln* を開きます。</span><span class="sxs-lookup"><span data-stu-id="32d11-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="32d11-182">ソリューション エクスプローラーで *Pages\Index.cshtml* を開きます。</span><span class="sxs-lookup"><span data-stu-id="32d11-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="32d11-183">`<h2>Simple Feed Reader</h2>` を `<h2>Simple Feed Reader - V2</h2>` に変更します。</span><span class="sxs-lookup"><span data-stu-id="32d11-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="32d11-184">**Ctrl** + **Shift** + **B** キーを押して、アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="32d11-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="32d11-185">ソリューション エクスプローラーでプロジェクトを右クリックし、 **[発行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="32d11-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![右クリックの [発行] を示すスクリーンショット](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="32d11-187">Visual Studio で新しい App Service リソースを作成できますが、この更新プログラムは既存のデプロイに対して発行されます。</span><span class="sxs-lookup"><span data-stu-id="32d11-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="32d11-188">**[発行先を選択]** ダイアログで、左側の一覧から **[App Service]** を選択し、 **[既存のものを選択]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="32d11-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="32d11-189">**[発行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="32d11-189">Click **Publish**.</span></span>
6. <span data-ttu-id="32d11-190">**[App Service]** ダイアログで、Azure サブスクリプションの作成に使用した Microsoft アカウントまたは組織アカウントが右上に表示されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="32d11-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="32d11-191">そうでない場合は、ドロップダウンをクリックして追加します。</span><span class="sxs-lookup"><span data-stu-id="32d11-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="32d11-192">正しい Azure **サブスクリプション** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="32d11-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="32d11-193">**[表示]** で、 **[リソース グループ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="32d11-193">For **View** , select **Resource Group**.</span></span> <span data-ttu-id="32d11-194">**AzureTutorial** リソース グループを展開し、既存の Web アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="32d11-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="32d11-195">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="32d11-195">Click **OK**.</span></span>

    ![App Service への発行ダイアログを示すスクリーンショット](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="32d11-197">Visual Studio によってアプリがビルドされ、Azure にデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="32d11-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="32d11-198">Web アプリの URL を参照します。</span><span class="sxs-lookup"><span data-stu-id="32d11-198">Browse to the web app URL.</span></span> <span data-ttu-id="32d11-199">`<h2>` 要素の変更が有効であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="32d11-199">Validate that the `<h2>` element modification is live.</span></span>

![タイトルが変更されたアプリ](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="32d11-201">デプロイ スロット</span><span class="sxs-lookup"><span data-stu-id="32d11-201">Deployment slots</span></span>

<span data-ttu-id="32d11-202">デプロイ スロットは、運用環境で実行されているアプリに影響を与えることなく、変更のステージングがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="32d11-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="32d11-203">アプリのステージング バージョンが品質保証チームによって検証されると、運用スロットとステージング スロットを入れ替えることができます。</span><span class="sxs-lookup"><span data-stu-id="32d11-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="32d11-204">ステージング中のアプリは、この方法で運用環境にレベル上げされます。</span><span class="sxs-lookup"><span data-stu-id="32d11-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="32d11-205">次の手順では、ステージング スロットを作成し、変更をそこにデプロイして、検証後にステージング スロットと運用環境を入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="32d11-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="32d11-206">まだサインインしていない場合は、[Azure Cloud Shell](https://shell.azure.com/bash) にサインインします。</span><span class="sxs-lookup"><span data-stu-id="32d11-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="32d11-207">ステージング スロットを作成します。</span><span class="sxs-lookup"><span data-stu-id="32d11-207">Create the staging slot.</span></span>

    <span data-ttu-id="32d11-208">a.</span><span class="sxs-lookup"><span data-stu-id="32d11-208">a.</span></span> <span data-ttu-id="32d11-209">*staging* という名前でデプロイ スロットを作成します。</span><span class="sxs-lookup"><span data-stu-id="32d11-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="32d11-210">b.</span><span class="sxs-lookup"><span data-stu-id="32d11-210">b.</span></span> <span data-ttu-id="32d11-211">ローカル環境の Git からのデプロイを使用するようにステージング スロットを構成し、 **ステージング** デプロイ URL を取得します。</span><span class="sxs-lookup"><span data-stu-id="32d11-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="32d11-212">**後で参照するので、この URL を記録しておきます** 。</span><span class="sxs-lookup"><span data-stu-id="32d11-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="32d11-213">c.</span><span class="sxs-lookup"><span data-stu-id="32d11-213">c.</span></span> <span data-ttu-id="32d11-214">ステージング スロットの URL を表示します。</span><span class="sxs-lookup"><span data-stu-id="32d11-214">Display the staging slot's URL.</span></span> <span data-ttu-id="32d11-215">URL を参照して、空のステージン グスロットを表示します。</span><span class="sxs-lookup"><span data-stu-id="32d11-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="32d11-216">**後で参照するので、この URL を記録しておきます** 。</span><span class="sxs-lookup"><span data-stu-id="32d11-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="32d11-217">テキスト エディターまたは Visual Studio で、`<h2>` 要素が `<h2>Simple Feed Reader - V3</h2>` を読み取り、ファイルを保存するように、 *Pages/Index.cshtml* をもう一度変更します。</span><span class="sxs-lookup"><span data-stu-id="32d11-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="32d11-218">Visual Studio の *[チーム エクスプローラー]* タブの **[変更]** ページを使用するか、ローカル コンピューターのコマンド シェルを使用して次のように入力し、ファイルをローカル Git リポジトリにコミットします。</span><span class="sxs-lookup"><span data-stu-id="32d11-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="32d11-219">ローカル コンピューターのコマンド シェルを使用して、ステージング デプロイ URL を Git リモートとして追加し、コミットされた変更をプッシュします。</span><span class="sxs-lookup"><span data-stu-id="32d11-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="32d11-220">a.</span><span class="sxs-lookup"><span data-stu-id="32d11-220">a.</span></span> <span data-ttu-id="32d11-221">ステージング用のリモート URL をローカル Git リポジトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="32d11-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="32d11-222">b.</span><span class="sxs-lookup"><span data-stu-id="32d11-222">b.</span></span> <span data-ttu-id="32d11-223">ローカルの *master* ブランチを、 *azure-staging* リモートの *master* ブランチにプッシュします。</span><span class="sxs-lookup"><span data-stu-id="32d11-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="32d11-224">Azure によってアプリがビルドされてデプロイされるまで待ちます。</span><span class="sxs-lookup"><span data-stu-id="32d11-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="32d11-225">V3 がステージング スロットにデプロイされたことを確認するため、2 つのブラウザー ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="32d11-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="32d11-226">1 つのウィンドウで、元の Web アプリの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="32d11-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="32d11-227">もう 1 つのウィンドウで、ステージング Web アプリの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="32d11-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="32d11-228">運用 URL では、アプリの V2 が提供されています。</span><span class="sxs-lookup"><span data-stu-id="32d11-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="32d11-229">ステージング URL では、アプリの V3 が提供されています。</span><span class="sxs-lookup"><span data-stu-id="32d11-229">The staging URL serves V3 of the app.</span></span>

    ![ブラウザー ウィンドウの比較のスクリーンショット](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="32d11-231">Cloud Shell で、検証済みのウォームアップされたステージング スロットを運用環境にスワップします。</span><span class="sxs-lookup"><span data-stu-id="32d11-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="32d11-232">2 つのブラウザー ウィンドウを更新して、スワップが発生したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="32d11-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![スワップ後のブラウザー ウィンドウの比較](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="32d11-234">まとめ</span><span class="sxs-lookup"><span data-stu-id="32d11-234">Summary</span></span>

<span data-ttu-id="32d11-235">このセクションでは、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="32d11-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="32d11-236">サンプル アプリをダウンロードしてビルドしました。</span><span class="sxs-lookup"><span data-stu-id="32d11-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="32d11-237">Azure Cloud Shell を使用して Azure App Service Web アプリを作成しました。</span><span class="sxs-lookup"><span data-stu-id="32d11-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="32d11-238">Git を使用してサンプル アプリを Azure にデプロイしました。</span><span class="sxs-lookup"><span data-stu-id="32d11-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="32d11-239">Visual Studio を使用して変更をアプリにデプロイしました。</span><span class="sxs-lookup"><span data-stu-id="32d11-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="32d11-240">ステージング スロットを Web アプリに追加しました。</span><span class="sxs-lookup"><span data-stu-id="32d11-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="32d11-241">更新プログラムをステージング スロットにデプロイしました。</span><span class="sxs-lookup"><span data-stu-id="32d11-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="32d11-242">ステージング スロットと運用スロットをスワップしました。</span><span class="sxs-lookup"><span data-stu-id="32d11-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="32d11-243">次のセクションでは、Azure Pipelines を使用して DevOps パイプラインを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="32d11-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="32d11-244">その他の参考資料</span><span class="sxs-lookup"><span data-stu-id="32d11-244">Additional reading</span></span>

* [<span data-ttu-id="32d11-245">Web Apps の概要</span><span class="sxs-lookup"><span data-stu-id="32d11-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="32d11-246">Azure App Service で .NET Core および SQL Database のアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="32d11-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="32d11-247">Azure App Service のデプロイ資格情報の構成</span><span class="sxs-lookup"><span data-stu-id="32d11-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="32d11-248">Azure App Service でステージング環境を設定する</span><span class="sxs-lookup"><span data-stu-id="32d11-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)

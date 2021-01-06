---
title: 継続的インテグレーションおよび継続的デプロイ - ASP.NET Core と Azure を使用した DevOps
author: CamSoper
description: ASP.NET Core と Azure を使用した DevOps での継続的インテグレーションおよび継続的デプロイ
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/cicd
ms.openlocfilehash: 2ac7a130d223b21330d0a797c1d460fc0cf467d7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901211"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="f017b-103">継続的インテグレーションと継続的配置</span><span class="sxs-lookup"><span data-stu-id="f017b-103">Continuous integration and deployment</span></span>

<span data-ttu-id="f017b-104">前の章では、Simple Feed Reader アプリ用にローカル Git リポジトリを作成しました。</span><span class="sxs-lookup"><span data-stu-id="f017b-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="f017b-105">この章では、そのコードを GitHub リポジトリに発行してから、Azure Pipelines を使用して Azure DevOps Services パイプラインを構築します。</span><span class="sxs-lookup"><span data-stu-id="f017b-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="f017b-106">パイプラインを使用すれば、アプリの継続的なビルドとデプロイが可能になります。</span><span class="sxs-lookup"><span data-stu-id="f017b-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="f017b-107">GitHub リポジトリへのコミットによって、ビルドと Azure Web アプリのステージング スロットへのデプロイがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f017b-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="f017b-108">このセクションでは、次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="f017b-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="f017b-109">アプリのコードを GitHub に発行する</span><span class="sxs-lookup"><span data-stu-id="f017b-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="f017b-110">ローカル Git デプロイの接続を解除する</span><span class="sxs-lookup"><span data-stu-id="f017b-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="f017b-111">Azure DevOps 組織を作成する</span><span class="sxs-lookup"><span data-stu-id="f017b-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="f017b-112">Azure DevOps Services でチーム プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="f017b-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="f017b-113">ビルド定義の作成</span><span class="sxs-lookup"><span data-stu-id="f017b-113">Create a build definition</span></span>
* <span data-ttu-id="f017b-114">リリース パイプラインを作成する</span><span class="sxs-lookup"><span data-stu-id="f017b-114">Create a release pipeline</span></span>
* <span data-ttu-id="f017b-115">GitHub への変更をコミットし、Azure に自動的にデプロイする</span><span class="sxs-lookup"><span data-stu-id="f017b-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="f017b-116">Azure Pipelines パイプラインを調べる</span><span class="sxs-lookup"><span data-stu-id="f017b-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="f017b-117">アプリのコードを GitHub に発行する</span><span class="sxs-lookup"><span data-stu-id="f017b-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="f017b-118">ブラウザー ウィンドウを開き、`https://github.com` に移動します。</span><span class="sxs-lookup"><span data-stu-id="f017b-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="f017b-119">ヘッダーの **[+]** ドロップダウンをクリックし、 **[新しいリポジトリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub の [新しいリポジトリ] オプション](media/cicd/github-new-repo.png)

1. <span data-ttu-id="f017b-121">**[所有者]** ドロップダウンでご自分のアカウントを選択し、 **[リポジトリ名]** テキストボックスに「*simple-feed-reader*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="f017b-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="f017b-122">**[リポジトリの作成]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="f017b-123">ご利用のローカル コンピューターのコマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="f017b-123">Open your local machine's command shell.</span></span> <span data-ttu-id="f017b-124">*simple-feed-reader* Git リポジトリが格納されているディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="f017b-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="f017b-125">既存の *origin* リモートを *upstream* に名前変更します。</span><span class="sxs-lookup"><span data-stu-id="f017b-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="f017b-126">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f017b-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="f017b-127">GitHub にあるリポジトリのご自分のコピーを指す新しい *origin* リモートを追加します。</span><span class="sxs-lookup"><span data-stu-id="f017b-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="f017b-128">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f017b-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="f017b-129">新しく作成した GitHub リポジトリにご自分のローカル Git リポジトリを発行します。</span><span class="sxs-lookup"><span data-stu-id="f017b-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="f017b-130">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f017b-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="f017b-131">ブラウザー ウィンドウを開き、`https://github.com/<GitHub_username>/simple-feed-reader/` に移動します。</span><span class="sxs-lookup"><span data-stu-id="f017b-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="f017b-132">GitHub リポジトリにご自分のコードが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f017b-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="f017b-133">ローカル Git デプロイの接続を解除する</span><span class="sxs-lookup"><span data-stu-id="f017b-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="f017b-134">次の手順に従って、ローカル Git デプロイを削除します。</span><span class="sxs-lookup"><span data-stu-id="f017b-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="f017b-135">その機能は Azure Pipelines (Azure DevOps サービス) に置き換えられ、強化されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="f017b-136">[Azure portal](https://portal.azure.com/) を開き、*staging (mywebapp\<unique_number\>/staging)* Web アプリに移動します。</span><span class="sxs-lookup"><span data-stu-id="f017b-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="f017b-137">この Web アプリは、ポータルの検索ボックスに 「*staging*」と入力すれば、すぐに見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="f017b-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![Web アプリの検索用語 staging](media/cicd/portal-search-box.png)

1. <span data-ttu-id="f017b-139">**デプロイ センター** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-139">Click **Deployment Center**.</span></span> <span data-ttu-id="f017b-140">新しいパネルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-140">A new panel appears.</span></span> <span data-ttu-id="f017b-141">**[接続解除]** をクリックして、前の章で追加したローカル Git ソース管理構成を削除します。</span><span class="sxs-lookup"><span data-stu-id="f017b-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="f017b-142">**[はい]** ボタンをクリックして削除操作を確定します。</span><span class="sxs-lookup"><span data-stu-id="f017b-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="f017b-143">*mywebapp<unique_number>* App Service に移動します。</span><span class="sxs-lookup"><span data-stu-id="f017b-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="f017b-144">繰り返しますが、ポータルの検索ボックスを使用すれば、App Service をすばやく見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="f017b-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="f017b-145">**デプロイ センター** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-145">Click **Deployment Center**.</span></span> <span data-ttu-id="f017b-146">新しいパネルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-146">A new panel appears.</span></span> <span data-ttu-id="f017b-147">**[接続解除]** をクリックして、前の章で追加したローカル Git ソース管理構成を削除します。</span><span class="sxs-lookup"><span data-stu-id="f017b-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="f017b-148">**[はい]** ボタンをクリックして削除操作を確定します。</span><span class="sxs-lookup"><span data-stu-id="f017b-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="f017b-149">Azure DevOps 組織を作成する</span><span class="sxs-lookup"><span data-stu-id="f017b-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="f017b-150">ブラウザーを開き、[Azure DevOps 組織作成ページ](https://go.microsoft.com/fwlink/?LinkId=307137)に移動します。</span><span class="sxs-lookup"><span data-stu-id="f017b-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="f017b-151">**[覚えやすい名前を選んでください]** テキストボックスに一意の名前を入力して、ご自分の Azure DevOps 組織にアクセスするための URL を作成します。</span><span class="sxs-lookup"><span data-stu-id="f017b-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="f017b-152">コードは GitHub リポジトリでホストされているため、 **[Git]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="f017b-153">**[Continue]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-153">Click the **Continue** button.</span></span> <span data-ttu-id="f017b-154">しばらく待つと *MyFirstProject* という名前のアカウントとチームプロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Azure DevOps 組織作成ページ](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="f017b-156">Azure DevOps の組織とプロジェクトが使用できる状態であることを示す確認メールを開きます。</span><span class="sxs-lookup"><span data-stu-id="f017b-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="f017b-157">次に示す **[プロジェクトの開始]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-157">Click the **Start your project** button:</span></span>

    ![[プロジェクトの開始] ボタン](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="f017b-159">ブラウザーが開き、 *\<account_name\>.visualstudio.com* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="f017b-160">*[MyFirstProject]* リンクをクリックして、プロジェクトの DevOps パイプラインの構成を開始します。</span><span class="sxs-lookup"><span data-stu-id="f017b-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="f017b-161">Azure Pipelines パイプラインを構成する</span><span class="sxs-lookup"><span data-stu-id="f017b-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="f017b-162">3 つのステップを個別に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f017b-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="f017b-163">以下の 3 つのセクションの手順を完了すると、運用 DevOps パイプラインが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="f017b-164">GitHub リポジトリへのアクセス権を Azure DevOps に付与する</span><span class="sxs-lookup"><span data-stu-id="f017b-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="f017b-165">**[または外部リポジトリからコードを構築する]** アコーディオンを展開します。</span><span class="sxs-lookup"><span data-stu-id="f017b-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="f017b-166">**[ビルドのセットアップ]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-166">Click the **Setup Build** button:</span></span>

    ![[ビルドのセットアップ] ボタン](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="f017b-168">**[ソースの選択]** セクションから **[GitHub]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![ソースの選択 - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="f017b-170">ご利用の GitHub リポジトリに Azure DevOps がアクセスできるようにするには、事前に承認が必要です。</span><span class="sxs-lookup"><span data-stu-id="f017b-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="f017b-171">**[接続名]** テキストボックスに「 *<GitHub_username> GitHub connection*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="f017b-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="f017b-172">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f017b-172">For example:</span></span>

    ![GitHub 接続名](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="f017b-174">ご利用の GitHub アカウントで 2 要素認証が有効になっている場合は、個人用アクセス トークンが必要です。</span><span class="sxs-lookup"><span data-stu-id="f017b-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="f017b-175">その場合は、 **[GitHub 個人用アクセス トークンで認証する]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="f017b-176">詳細については、[公式の GitHub 個人用アクセストークン作成手順](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f017b-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="f017b-177">必要な権限のスコープは *repo* のみです。</span><span class="sxs-lookup"><span data-stu-id="f017b-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="f017b-178">それ以外の場合は、 **[OAuth を使用して承認]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="f017b-179">メッセージが表示されたら、ご自分の GitHub アカウントにサインインします。</span><span class="sxs-lookup"><span data-stu-id="f017b-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="f017b-180">次に、[承認] を選択して、ご自分の Azure DevOps 組織へのアクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="f017b-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="f017b-181">成功した場合は、新しいサービス エンドポイントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="f017b-182">**[リポジトリ]** ボタンの横にある省略記号ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="f017b-183">一覧から、 *<GitHub_username>/simple-feed-reader* リポジトリを選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="f017b-184">**[選択]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="f017b-185">**[手動のビルドとスケジュールされたビルドの既定のブランチ]** ドロップダウンから既定のブランチ (*master*) を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-185">Select the default branch (*master*) from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="f017b-186">**[Continue]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-186">Click the **Continue** button.</span></span> <span data-ttu-id="f017b-187">テンプレートの選択ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="f017b-188">ビルド定義を作成する</span><span class="sxs-lookup"><span data-stu-id="f017b-188">Create the build definition</span></span>

1. <span data-ttu-id="f017b-189">テンプレートの選択ページの [検索] ボックスに「*ASP.NET Core*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="f017b-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![テンプレート ページでの ASP.NET Core の検索](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="f017b-191">テンプレートの検索結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-191">The template search results appear.</span></span> <span data-ttu-id="f017b-192">**[ASP.NET Core]** テンプレートにマウス ポインターを合わせ、 **[適用]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="f017b-193">ビルド定義の **[タスク]** タブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="f017b-194">**[トリガー]** タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="f017b-195">**[継続的インテグレーションを有効にする]** ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="f017b-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="f017b-196">**[ブランチ フィルター]** セクションで、 **[種類]** ドロップダウンが *[Include]* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f017b-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="f017b-197">**[ブランチ仕様]** ドロップダウンを *master* に設定します。</span><span class="sxs-lookup"><span data-stu-id="f017b-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![[継続的インテグレーションを有効にする] に関する設定](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="f017b-199">これらの設定を行うと、GitHub リポジトリの既定のブランチ (*master*) に何らかの変更がプッシュされたときにビルドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f017b-199">These settings cause a build to trigger when any change is pushed to the default branch (*master*) of the GitHub repository.</span></span> <span data-ttu-id="f017b-200">継続的インテグレーションのテストについては、「[GitHub への変更をコミットし、Azure に自動的にデプロイする](#commit-changes-to-github-and-automatically-deploy-to-azure)」セクションで行います。</span><span class="sxs-lookup"><span data-stu-id="f017b-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="f017b-201">**[保存してキューに登録]** ボタンをクリックし、 **[保存]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![[保存] ボタン](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="f017b-203">次のモーダル ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-203">The following modal dialog appears:</span></span>

    ![ビルド定義を保存する - モーダル ダイアログ](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="f017b-205">既定のフォルダーである *\\* を使用して、 **[保存]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="f017b-206">リリース パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="f017b-206">Create the release pipeline</span></span>

1. <span data-ttu-id="f017b-207">チーム プロジェクトの **[リリース]** タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="f017b-208">**[新しいパイプライン]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-208">Click the **New pipeline** button.</span></span>

    ![[リリース] タブ - [新しい定義] ボタン](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="f017b-210">テンプレート選択ウィンドウが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="f017b-211">テンプレート選択ページで、[検索] ボックスに「*App Service*」と入力します。</span><span class="sxs-lookup"><span data-stu-id="f017b-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![リリース パイプライン テンプレートの検索ボックス](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="f017b-213">テンプレートの検索結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-213">The template search results appear.</span></span> <span data-ttu-id="f017b-214">**[スロットを使用した Azure App Service の配置]** テンプレートにマウス ポインターを合わせ、 **[適用]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="f017b-215">リリース パイプラインの **[パイプライン]** タブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![リリース パイプラインの [パイプライン] タブ](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="f017b-217">**[成果物]** ボックスの **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="f017b-218">**[成果物の追加]** パネルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-218">The **Add artifact** panel appears:</span></span>

    ![リリース パイプライン - [成果物の追加] パネル](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="f017b-220">**[ソースの種類]** セクションから **[ビルド]** タイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="f017b-221">この種類を選択すれば、リリース パイプラインをビルド定義にリンクすることができます。</span><span class="sxs-lookup"><span data-stu-id="f017b-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="f017b-222">**[プロジェクト]** ドロップダウンから *[MyFirstProject]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="f017b-223">**[ソース (ビルド定義)]** ドロップダウンから、ビルド定義名として *MyFirstProject-ASP.NET Core-CI* を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="f017b-224">**[既定のバージョン]** ドロップダウンから *[最新]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="f017b-225">このオプションでは、ビルド定義の最新の実行によって生成された成果物がビルドされます。</span><span class="sxs-lookup"><span data-stu-id="f017b-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="f017b-226">**[ソースの別名]** テキストボックス内のテキストを *Drop* に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f017b-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="f017b-227">**[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-227">Click the **Add** button.</span></span> <span data-ttu-id="f017b-228">**[成果物]** セクションが更新され、変更内容が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="f017b-229">[稲妻] アイコンをクリックして、継続的デプロイを有効にします。</span><span class="sxs-lookup"><span data-stu-id="f017b-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![リリース パイプラインの成果物 - [稲妻] アイコン](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="f017b-231">このオプションを有効にすると、新しいビルドが使用可能になるたびにデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="f017b-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="f017b-232">**[継続的デプロイ トリガー]** パネルが右側に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="f017b-233">トグル ボタンをクリックして、この機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="f017b-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="f017b-234">**[Pull request のトリガー]** を有効にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f017b-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="f017b-235">**[ビルド ブランチ フィルター]** セクションの **[追加]** ドロップダウンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="f017b-236">**[ビルド定義の既定のブランチ]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="f017b-237">このフィルターを使用すると、GitHub リポジトリの既定のブランチ (*master*) からのビルドの場合にのみリリースがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f017b-237">This filter causes the release to trigger only for a build from the GitHub repository's default branch (*master*).</span></span>
1. <span data-ttu-id="f017b-238">**[保存]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-238">Click the **Save** button.</span></span> <span data-ttu-id="f017b-239">結果として生成された **[保存]** モーダル ダイアログの **[OK]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="f017b-240">**[環境 1]** ボックスをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="f017b-241">**[環境]** パネルが右側に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="f017b-242">**[環境名]** テキストボックスのテキスト "*環境 1*" を "*運用*" に変更します。</span><span class="sxs-lookup"><span data-stu-id="f017b-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![リリース パイプライン - [環境名] テキストボックス](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="f017b-244">**[運用]** ボックスで、 **[1 フェーズ、2 タスク]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![リリース パイプライン - 運用環境リンク.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="f017b-246">その環境の **[タスク]** タブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="f017b-247">**[Deploy Azure App Service to Slot]\(Azure App Service をスロットにデプロイ\)** タスクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="f017b-248">その設定が右側のパネルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="f017b-249">App Service に関連付けられている Azure サブスクリプションを **[Azure サブスクリプション]** ドロップダウンから選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="f017b-250">選択したら、 **[承認]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="f017b-251">**[アプリの種類]** ドロップダウンから *[Web アプリ]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="f017b-252">**[App Service の名前]** ドロップダウンから *mywebapp/<unique_number/>* を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="f017b-253">*[リソース グループ]* ドロップダウンから **[AzureTutorial]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="f017b-254">**[スロット]** ドロップダウンから *[ステージング]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="f017b-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="f017b-255">**[保存]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="f017b-256">既定のリリース パイプライン名にマウス ポインターを合わせます。</span><span class="sxs-lookup"><span data-stu-id="f017b-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="f017b-257">それを編集するには、鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="f017b-258">名前として *MyFirstProject-ASP.NET Core-CD* を使用します。</span><span class="sxs-lookup"><span data-stu-id="f017b-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![リリース パイプラインの名前](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="f017b-260">**[保存]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="f017b-261">GitHub への変更をコミットし、Azure に自動的にデプロイする</span><span class="sxs-lookup"><span data-stu-id="f017b-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="f017b-262">Visual Studio で *SimpleFeedReader.sln* を開きます。</span><span class="sxs-lookup"><span data-stu-id="f017b-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="f017b-263">ソリューション エクスプローラーで *Pages\Index.cshtml* を開きます。</span><span class="sxs-lookup"><span data-stu-id="f017b-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="f017b-264">`<h2>Simple Feed Reader - V3</h2>` を `<h2>Simple Feed Reader - V4</h2>` に変更します。</span><span class="sxs-lookup"><span data-stu-id="f017b-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="f017b-265">**Ctrl** + **Shift** + **B** キーを押して、アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="f017b-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="f017b-266">GitHub リポジトリにファイルをコミットします。</span><span class="sxs-lookup"><span data-stu-id="f017b-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="f017b-267">Visual Studio の *[チーム エクスプローラー]* タブの **[変更]** ページを使用するか、ローカル コンピューターのコマンド シェルを使用して以下を実行します。</span><span class="sxs-lookup"><span data-stu-id="f017b-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="f017b-268">既定のブランチ (*master*) での変更を、ご利用の GitHub リポジトリの *origin* リモートにプッシュします。</span><span class="sxs-lookup"><span data-stu-id="f017b-268">Push the change in the default branch (*master*) to the *origin* remote of your GitHub repository.</span></span> <span data-ttu-id="f017b-269">次のコマンドで、プレースホルダー `{BRANCH}` を既定のブランチ (`master` を使用) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f017b-269">In the following command, replace the placeholder `{BRANCH}` with the default branch (use `master`):</span></span>

    ```console
    git push origin {BRANCH}
    ```

    <span data-ttu-id="f017b-270">このコミットが、GitHub リポジトリの既定のブランチ (*master*) に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-270">The commit appears in the GitHub repository's default branch (*master*):</span></span>

    ![既定のブランチ (master) での GitHub コミット](media/cicd/github-commit.png)

    <span data-ttu-id="f017b-272">ビルド定義の **[トリガー]** タブで継続的インテグレーションが有効になっているため、ビルドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f017b-272">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![継続的インテグレーションを有効にする](media/cicd/enable-ci.png)

1. <span data-ttu-id="f017b-274">Azure DevOps Services で、 **[Azure Pipelines]**  >  **[ビルド]** ページの **[キューに登録済み]** タブに移動します。</span><span class="sxs-lookup"><span data-stu-id="f017b-274">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="f017b-275">キューに登録されたビルドに、ビルドをトリガーしたブランチとコミットが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-275">The queued build shows the branch and commit that triggered the build:</span></span>

    ![キューに登録されたビルド](media/cicd/build-queued.png)

1. <span data-ttu-id="f017b-277">ビルドが成功すると、Azure へのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="f017b-277">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="f017b-278">ブラウザーでアプリに移動します。</span><span class="sxs-lookup"><span data-stu-id="f017b-278">Navigate to the app in the browser.</span></span> <span data-ttu-id="f017b-279">見出しに "V4" というテキストが表示されるのがわかります。</span><span class="sxs-lookup"><span data-stu-id="f017b-279">Notice that the "V4" text appears in the heading:</span></span>

    ![更新されたアプリ](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="f017b-281">Azure Pipelines パイプラインを調べる</span><span class="sxs-lookup"><span data-stu-id="f017b-281">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="f017b-282">ビルド定義</span><span class="sxs-lookup"><span data-stu-id="f017b-282">Build definition</span></span>

<span data-ttu-id="f017b-283">ビルド定義は *MyFirstProject-ASP.NET Core-CI* という名前で作成しました。</span><span class="sxs-lookup"><span data-stu-id="f017b-283">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="f017b-284">完了時には、発行する資産を含む *.zip* ファイルがビルドによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-284">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="f017b-285">それらの資産はリリース パイプラインによって Azure にデプロイされます。</span><span class="sxs-lookup"><span data-stu-id="f017b-285">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="f017b-286">ビルド定義の **[タスク]** タブには、使用されている個々のステップが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-286">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="f017b-287">ビルド タスクは 5 つあります。</span><span class="sxs-lookup"><span data-stu-id="f017b-287">There are five build tasks.</span></span>

![ビルド定義タスク](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="f017b-289">**復元** &mdash; `dotnet restore` コマンドを実行して、アプリの NuGet パッケージが復元されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-289">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="f017b-290">使用される既定のパッケージ フィードは nuget.org です。</span><span class="sxs-lookup"><span data-stu-id="f017b-290">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="f017b-291">**ビルド** &mdash; `dotnet build --configuration release` コマンドを実行して、アプリのコードがコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="f017b-291">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="f017b-292">この `--configuration` オプションは、コードの最適化されたバージョン (運用環境へのデプロイに適している) を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-292">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="f017b-293">たとえば、デバッグ構成が必要な場合は、ビルド定義の **[変数]** タブで *BuildConfiguration* 変数を変更します。</span><span class="sxs-lookup"><span data-stu-id="f017b-293">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="f017b-294">**テスト** &mdash; `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` コマンドを実行して、アプリの単体テストが実行されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-294">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="f017b-295">単体テストは、`**/*Tests/*.csproj` glob パターンに一致する C# プロジェクト内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-295">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="f017b-296">`--results-directory` オプションで指定された場所にある *.trx* ファイルにテスト結果が保存されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-296">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="f017b-297">いずれかのテストが失敗した場合、ビルドは失敗し、デプロイは行われません。</span><span class="sxs-lookup"><span data-stu-id="f017b-297">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="f017b-298">単体テストが機能していることを確認するには、テストの 1 つを意図的に中断するように *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* を変更します。</span><span class="sxs-lookup"><span data-stu-id="f017b-298">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="f017b-299">たとえば、`Returns_News_Stories_Given_Valid_Uri` メソッド内で `Assert.True(result.Count > 0);` を `Assert.False(result.Count > 0);` に変更します。</span><span class="sxs-lookup"><span data-stu-id="f017b-299">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="f017b-300">変更をコミットして GitHub にプッシュします。</span><span class="sxs-lookup"><span data-stu-id="f017b-300">Commit and push the change to GitHub.</span></span> <span data-ttu-id="f017b-301">ビルドがトリガーされますが失敗します。</span><span class="sxs-lookup"><span data-stu-id="f017b-301">The build is triggered and fails.</span></span> <span data-ttu-id="f017b-302">ビルド パイプラインの状態が **[失敗]** に変わります。</span><span class="sxs-lookup"><span data-stu-id="f017b-302">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="f017b-303">変更を元に戻し、コミットして、もう一度プッシュします。</span><span class="sxs-lookup"><span data-stu-id="f017b-303">Revert the change, commit, and push again.</span></span> <span data-ttu-id="f017b-304">ビルドは成功します。</span><span class="sxs-lookup"><span data-stu-id="f017b-304">The build succeeds.</span></span>

1. <span data-ttu-id="f017b-305">**発行** &mdash; `dotnet publish --configuration release --output <local_path_on_build_agent>` コマンドを実行して、デプロイする成果物を含む *.zip* ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-305">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="f017b-306">`--output` オプションによって *.zip* ファイルの発行場所が指定されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-306">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="f017b-307">その場所は、`$(build.artifactstagingdirectory)` という名前の[定義済みの変数](/azure/devops/pipelines/build/variables)を渡すことによって指定されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-307">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="f017b-308">その変数は、ビルド エージェント上のローカル パス (*c:\agent\_work\1\a* など) に展開されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-308">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="f017b-309">**成果物の発行** &mdash; **発行** タスクによって生成された *.zip* ファイルを発行します。</span><span class="sxs-lookup"><span data-stu-id="f017b-309">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="f017b-310">このタスクでは、 *.zip* ファイルの場所をパラメーターとして受け取ります。それは定義済みの変数 `$(build.artifactstagingdirectory)` です。</span><span class="sxs-lookup"><span data-stu-id="f017b-310">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="f017b-311">*.zip* ファイルは、*drop* という名前のフォルダーとして発行されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-311">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="f017b-312">ビルド定義の **[概要]** リンクをクリックして、ビルドの定義に関する履歴を表示します。</span><span class="sxs-lookup"><span data-stu-id="f017b-312">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![ビルド定義の履歴を示すスクリーンショット](media/cicd/build-definition-summary.png)

<span data-ttu-id="f017b-314">結果として表示されるページで、一意のビルド番号に対応するリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f017b-314">On the resulting page, click the link corresponding to the unique build number:</span></span>

![ビルド定義の概要ページを示すスクリーンショット](media/cicd/build-definition-completed.png)

<span data-ttu-id="f017b-316">この特定のビルドの概要が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-316">A summary of this specific build is displayed.</span></span> <span data-ttu-id="f017b-317">**[成果物]** タブをクリックすると、ビルドによって生成された *drop* フォルダーが一覧に表示されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="f017b-317">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![ビルド定義の成果物を示すスクリーンショット - drop フォルダー](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="f017b-319">**[ダウンロード]** および **[探索]** リンクを使用して、発行された成果物を検査します。</span><span class="sxs-lookup"><span data-stu-id="f017b-319">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="f017b-320">リリース パイプライン</span><span class="sxs-lookup"><span data-stu-id="f017b-320">Release pipeline</span></span>

<span data-ttu-id="f017b-321">リリース パイプラインは、*MyFirstProject-ASP.NET Core-CD* という名前で作成しました。</span><span class="sxs-lookup"><span data-stu-id="f017b-321">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![リリース パイプラインの概要を示すスクリーンショット](media/cicd/release-definition-overview.png)

<span data-ttu-id="f017b-323">リリース パイプラインの 2 つの主要なコンポーネントは、 **[成果物]** と **[環境]** です。</span><span class="sxs-lookup"><span data-stu-id="f017b-323">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="f017b-324">**[成果物]** セクションのボックスをクリックすると、次のパネルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-324">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![リリース パイプラインの成果物を示すスクリーンショット](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="f017b-326">**[ソース (ビルド定義)]** の値は、このリリース パイプラインがリンクされているビルド定義を表します。</span><span class="sxs-lookup"><span data-stu-id="f017b-326">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="f017b-327">ビルド定義が正常に実行された場合に生成される *.zip* ファイルは、Azure にデプロイするための "*運用*" 環境に提供されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-327">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="f017b-328">*[運用]* 環境ボックス内の *[1 フェーズ、2 タスク]* リンクをクリックすると、リリース パイプラインのタスクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-328">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![リリース パイプラインのタスクを示すスクリーンショット](media/cicd/release-definition-tasks.png)

<span data-ttu-id="f017b-330">リリース パイプラインは、次の 2 つのタスクで構成されています: *[Deploy Azure App Service to Slot]\(Azure App Service をスロットにデプロイ\)* と *[Azure App Service の管理 - スロットのスワップ]* 。</span><span class="sxs-lookup"><span data-stu-id="f017b-330">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="f017b-331">最初のタスクをクリックすると、次のタスク構成が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-331">Clicking the first task reveals the following task configuration:</span></span>

![リリース パイプラインのデプロイ タスクを示すスクリーンショット](media/cicd/release-definition-task1.png)

<span data-ttu-id="f017b-333">デプロイ タスクでは、Azure サブスクリプション、サービスの種類、Web アプリ名、リソース グループ、デプロイ スロットが定義されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-333">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="f017b-334">**[パッケージまたはフォルダー]** テキストボックスには、*mywebapp\<unique_number\>* という Web アプリの "*ステージング*" スロットに抽出されてデプロイされる *.zip* ファイル パスが保持されています。</span><span class="sxs-lookup"><span data-stu-id="f017b-334">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="f017b-335">スロット スワップ タスクをクリックすると、次のタスク構成が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-335">Clicking the slot swap task reveals the following task configuration:</span></span>

![リリース パイプラインのスロット スワップ タスクを示すスクリーン ショット](media/cicd/release-definition-task2.png)

<span data-ttu-id="f017b-337">サブスクリプション、リソース グループ、サービスの種類、Web アプリ名、デプロイ スロットの詳細が提供されます。</span><span class="sxs-lookup"><span data-stu-id="f017b-337">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="f017b-338">**[本稼働とのスワップ]** チェック ボックスがオンになっています。</span><span class="sxs-lookup"><span data-stu-id="f017b-338">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="f017b-339">したがって、"*ステージング*" スロットにデプロイされたビットは運用環境にスワップされます。</span><span class="sxs-lookup"><span data-stu-id="f017b-339">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="f017b-340">その他の参考資料</span><span class="sxs-lookup"><span data-stu-id="f017b-340">Additional reading</span></span>

* [<span data-ttu-id="f017b-341">Azure Pipelines による最初のパイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="f017b-341">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="f017b-342">ビルドと .NET Core プロジェクト</span><span class="sxs-lookup"><span data-stu-id="f017b-342">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="f017b-343">Azure Pipelines を使用した Web アプリのデプロイ</span><span class="sxs-lookup"><span data-stu-id="f017b-343">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)

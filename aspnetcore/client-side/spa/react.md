---
title: ASP.NET Core で React プロジェクト テンプレートを使用する
author: SteveSandersonMS
description: React と create-react-app 用の ASP.NET Core シングル ページ アプリケーション (SPA) プロジェクト テンプレートの使用を開始する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: spa/react
ms.openlocfilehash: 6c3539d96329489189f49a3af0c718791824be6b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054451"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="cf4cd-103">ASP.NET Core で React プロジェクト テンプレートを使用する</span><span class="sxs-lookup"><span data-stu-id="cf4cd-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="cf4cd-104">更新された React プロジェクト テンプレートは、React および [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) 規則を使用してリッチなクライアント側ユーザー インターフェイス (UI) を実装する ASP.NET Core アプリを開発する場合に、便利な開始点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="cf4cd-105">このテンプレートには、API バックエンドとして機能する ASP.NET Core プロジェクトと、UI として機能する標準の CRA React プロジェクトの両方を作成する場合と同等の機能がありますが、単一のユニットとしてビルドと発行が可能な単一のアプリ プロジェクトで両方をホストできるので便利です。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="cf4cd-106">React プロジェクト テンプレートは、サーバー側のレンダリング (SSR) 用ではありません。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="cf4cd-107">React および Node.js を使用して SSR を行うには、[Next.js](https://github.com/zeit/next.js/) または [Razzle](https://github.com/jaredpalmer/razzle) を検討してください。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="cf4cd-108">新しいアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="cf4cd-108">Create a new app</span></span>

<span data-ttu-id="cf4cd-109">ASP.NET Core 2.1 がインストールされている場合は、React プロジェクト テンプレートをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="cf4cd-110">コマンド プロンプトで `dotnet new react` コマンドを使用して、空のディレクトリの中に新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="cf4cd-111">たとえば、次のコマンドは、 *my-new-app* ディレクトリにアプリを作成し、そのディレクトリに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="cf4cd-112">Visual Studio または .NET Core CLI からアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf4cd-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf4cd-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cf4cd-114">生成された *.csproj* ファイルを開き、そこから通常の方法でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="cf4cd-115">ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="cf4cd-116">以降のビルドは非常に高速になります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cf4cd-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cf4cd-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cf4cd-118">値が `Development` である `ASPNETCORE_Environment` という名前の環境変数があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="cf4cd-119">Windows では (PowerShell ではないプロンプトで) `SET ASPNETCORE_Environment=Development` を実行します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="cf4cd-120">Linux または macOS では、`export ASPNETCORE_Environment=Development` を実行します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="cf4cd-121">[dotnet build](/dotnet/core/tools/dotnet-build) を実行して、アプリが正しくビルドされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="cf4cd-122">ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="cf4cd-123">以降のビルドは非常に高速になります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="cf4cd-124">[dotnet run](/dotnet/core/tools/dotnet-run) を実行してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="cf4cd-125">プロジェクト テンプレートは、ASP.NET Core アプリと React アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="cf4cd-126">ASP.NET Core アプリは、データ アクセス、承認、およびその他のサーバー側の操作で使用することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="cf4cd-127">*ClientApp* サブディレクトリ内の React アプリは、UI に関するすべての 操作で使用することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="cf4cd-128">ページ、画像、スタイル、モジュールなどを追加する</span><span class="sxs-lookup"><span data-stu-id="cf4cd-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="cf4cd-129">*ClientApp* ディレクトリは標準の CRA React アプリです。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="cf4cd-130">詳細については、公式の [CRA ドキュメント](https://create-react-app.dev/docs/getting-started/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="cf4cd-131">このテンプレートによって作成される React アプリと CRA 自体によって作成されるアプリにはわずかな違いがありますが、アプリの機能は変わりません。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="cf4cd-132">テンプレートによって作成されるアプリには、[ブートストラップ](https://getbootstrap.com/)ベースのレイアウトと基本的なルーティングの例が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="cf4cd-133">npm パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="cf4cd-133">Install npm packages</span></span>

<span data-ttu-id="cf4cd-134">サードパーティ製の npm パッケージをインストールするには、 *ClientApp* サブディレクトリでコマンド プロンプトを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="cf4cd-135">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="cf4cd-136">発行と配置</span><span class="sxs-lookup"><span data-stu-id="cf4cd-136">Publish and deploy</span></span>

<span data-ttu-id="cf4cd-137">開発中、アプリは、開発者に便利なように最適化されたモードで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="cf4cd-138">たとえば、JavaScript バンドルには、ソース マップが含まれます (デバッグ時に元のソース コードを確認できるようにするためです)。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="cf4cd-139">アプリは、ディスク上の JavaScript、HTML および CSS ファイルの変更を監視し、これらのファイルの変更が発生した場合は、再コンパイルと再読み込みを自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="cf4cd-140">運用時は、パフォーマンスが最適化されたバージョンのアプリが提供されます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="cf4cd-141">これは、自動的に実行されるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-141">This is configured to happen automatically.</span></span> <span data-ttu-id="cf4cd-142">発行すると、ビルド構成によって、クライアント側コードの縮小され、トランスパイルされたビルドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="cf4cd-143">開発ビルドとは異なり、運用ビルドは、サーバーへの Node.js のインストールを必要としません。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="cf4cd-144">標準的な [ASP.NET Core のホストと展開方法](xref:host-and-deploy/index)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="cf4cd-145">CRA サーバーを個別に実行する</span><span class="sxs-lookup"><span data-stu-id="cf4cd-145">Run the CRA server independently</span></span>

<span data-ttu-id="cf4cd-146">ASP.NET Core アプリが開発モードで起動された場合、プロジェクトは、CRA 開発サーバーの独自のインスタンスをバックグラウンドで開始するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="cf4cd-147">これが便利なのは、別のサーバーを手動で実行する必要がないためです。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="cf4cd-148">この既定の設定には欠点があります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="cf4cd-149">C# コードを変更し、ASP.NET Core アプリを再起動する必要がある場合、CRA サーバーが毎回再起動します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="cf4cd-150">再起動には数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="cf4cd-151">C# コードを何度も編集するが、CRA サーバーが再起動するまで待ちたくない場合は、ASP.NET Core プロセスから独立した CRA サーバーを外部で実行します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="cf4cd-152">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-152">To do so:</span></span>

1. <span data-ttu-id="cf4cd-153">次の設定を使用して、 *.env* ファイルを *ClientApp* サブディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="cf4cd-154">これにより、CRA サーバーを外部で起動するときに、Web ブラウザーが開かなくなります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="cf4cd-155">コマンド プロンプトで、 *ClientApp* サブディレクトリに切り替え、CRA 開発サーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="cf4cd-156">独自のインスタンスを起動する代わりに外部の CRA サーバー インスタンスを使用するように ASP.NET Core アプリケーションを変更します。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="cf4cd-157">*Startup* クラスで、`spa.UseReactDevelopmentServer` の呼び出しを以下に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="cf4cd-158">ASP.NET Core アプリの起動時に CRA サーバーが起動されなくなります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="cf4cd-159">代わりに、手動で開始したインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="cf4cd-160">これにより、起動と再起動を高速化できます。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="cf4cd-161">React アプリが毎回リビルドされるまで待つ必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cf4cd-162">"サーバー側のレンダリング" は、このテンプレートではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="cf4cd-163">このテンプレートの目標は、"create-react-app" と同等のものを提供することです。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="cf4cd-164">そのため、"create-react-app" プロジェクトに含まれていないシナリオや機能 (SSR など) はサポートされておらず、ユーザーのための演習として残されています。</span><span class="sxs-lookup"><span data-stu-id="cf4cd-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf4cd-165">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cf4cd-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>

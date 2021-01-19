---
title: ASP.NET Core 向けの Docker イメージ
author: rick-anderson
description: 公開されている ASP.NET Core Docker イメージを Docker レジストリから使用する方法について説明します。 独自のイメージをプルしてビルドします。
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 2cd21722082af88e536bc1001b606ee96e7cf59b
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972055"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="9278a-104">ASP.NET Core 向けの Docker イメージ</span><span class="sxs-lookup"><span data-stu-id="9278a-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="9278a-105">このチュートリアルでは、Docker コンテナー内で ASP.NET Core アプリを実行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9278a-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="9278a-106">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="9278a-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="9278a-107">ASP.NET Core Docker イメージについて学習する</span><span class="sxs-lookup"><span data-stu-id="9278a-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="9278a-108">ASP.NET Core サンプル アプリをダウンロードする</span><span class="sxs-lookup"><span data-stu-id="9278a-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="9278a-109">サンプル アプリをローカルで実行する</span><span class="sxs-lookup"><span data-stu-id="9278a-109">Run the sample app locally</span></span>
> * <span data-ttu-id="9278a-110">Linux コンテナー内でサンプル アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9278a-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="9278a-111">Windows コンテナー内でサンプル アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9278a-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="9278a-112">手動でビルドしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="9278a-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="9278a-113">ASP.NET Core の Docker イメージ</span><span class="sxs-lookup"><span data-stu-id="9278a-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="9278a-114">このチュートリアルでは、ASP.NET Core サンプル アプリをダウンロードして、Docker コンテナー内で実行します。</span><span class="sxs-lookup"><span data-stu-id="9278a-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="9278a-115">このサンプルは Linux コンテナーと Windows コンテナーのどちらでも動作します。</span><span class="sxs-lookup"><span data-stu-id="9278a-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="9278a-116">さまざまなコンテナー内でビルドして実行するために、サンプルの Dockerfile では [Docker のマルチステージ ビルド機能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)を使用しています。</span><span class="sxs-lookup"><span data-stu-id="9278a-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="9278a-117">ビルドと実行のコンテナーは、マイクロソフトが Docker Hub に提供しているイメージから作成されます。</span><span class="sxs-lookup"><span data-stu-id="9278a-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="9278a-118">サンプルでは、アプリをビルドするためにこのイメージを使用します。</span><span class="sxs-lookup"><span data-stu-id="9278a-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="9278a-119">イメージには、コマンド ライン ツール (CLI) が組み込まれた .NET SDK が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9278a-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="9278a-120">イメージはローカル開発、デバッグ、および単体テスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="9278a-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="9278a-121">開発とコンパイルのためにツールがインストールされているため、比較的大きなイメージになっています。</span><span class="sxs-lookup"><span data-stu-id="9278a-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="9278a-122">サンプルでは、アプリをビルドするためにこのイメージを使用します。</span><span class="sxs-lookup"><span data-stu-id="9278a-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="9278a-123">イメージには、コマンド ライン ツール (CLI) が組み込まれた .NET Core SDK が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9278a-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="9278a-124">イメージはローカル開発、デバッグ、および単体テスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="9278a-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="9278a-125">開発とコンパイルのためにツールがインストールされているため、比較的大きなイメージになっています。</span><span class="sxs-lookup"><span data-stu-id="9278a-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="9278a-126">サンプルでは、アプリを実行するためにこのイメージを使用します。</span><span class="sxs-lookup"><span data-stu-id="9278a-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="9278a-127">イメージには ASP.NET Core ランタイムとライブラリが含まれており、実稼働環境でアプリを実行するために最適化されています。</span><span class="sxs-lookup"><span data-stu-id="9278a-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="9278a-128">デプロイとアプリ起動の速度に対応した設計になっており、Docker レジストリから Docker ホストへのネットワーク パフォーマンスが最適化されていることから、イメージは比較的小さいです。</span><span class="sxs-lookup"><span data-stu-id="9278a-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="9278a-129">アプリの実行に必要なバイナリとコンテンツのみが、コンテナーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="9278a-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="9278a-130">コンテンツは実行できる状態になっており、`docker run` からアプリの起動までを最速で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9278a-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="9278a-131">動的コード コンパイルは Docker モデルで必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9278a-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9278a-132">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9278a-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="9278a-133">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="9278a-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="9278a-134">.NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="9278a-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="9278a-135">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="9278a-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="9278a-136">Docker クライアント 18.03 以降</span><span class="sxs-lookup"><span data-stu-id="9278a-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="9278a-137">Linux ディストリビューション</span><span class="sxs-lookup"><span data-stu-id="9278a-137">Linux distributions</span></span>
    * [<span data-ttu-id="9278a-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="9278a-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="9278a-139">Debian</span><span class="sxs-lookup"><span data-stu-id="9278a-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="9278a-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="9278a-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="9278a-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="9278a-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="9278a-142">macOS</span><span class="sxs-lookup"><span data-stu-id="9278a-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="9278a-143">Windows</span><span class="sxs-lookup"><span data-stu-id="9278a-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="9278a-144">Git</span><span class="sxs-lookup"><span data-stu-id="9278a-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="9278a-145">サンプル アプリ をダウンロードする</span><span class="sxs-lookup"><span data-stu-id="9278a-145">Download the sample app</span></span>

* <span data-ttu-id="9278a-146">[.NET Docker リポジトリ](https://github.com/dotnet/dotnet-docker)を複製して、サンプルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="9278a-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="9278a-147">アプリをローカルで実行する</span><span class="sxs-lookup"><span data-stu-id="9278a-147">Run the app locally</span></span>

* <span data-ttu-id="9278a-148">*dotnet-docker/samples/aspnetapp/aspnetapp* にあるプロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9278a-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="9278a-149">次のコマンドを実行し、アプリをビルドしてローカルで実行します。</span><span class="sxs-lookup"><span data-stu-id="9278a-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="9278a-150">アプリをテストするには、ブラウザーで `http://localhost:5000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9278a-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="9278a-151">コマンド プロンプト上で Ctrl +C キーを押して、アプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="9278a-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="9278a-152">Linux コンテナーでの実行</span><span class="sxs-lookup"><span data-stu-id="9278a-152">Run in a Linux container</span></span>

* <span data-ttu-id="9278a-153">Docker クライアント上で、[Linux コンテナーに切り替えます](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。</span><span class="sxs-lookup"><span data-stu-id="9278a-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="9278a-154">*dotnet-docker/samples/aspnetapp* にある Dockerfile フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9278a-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="9278a-155">次のコマンドを実行して、Docker 内でサンプルをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="9278a-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="9278a-156">`build` コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="9278a-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="9278a-157">イメージに aspnetapp という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9278a-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="9278a-158">現在のフォルダー内にある Dockerfile を探します (末尾にピリオド)。</span><span class="sxs-lookup"><span data-stu-id="9278a-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="9278a-159">実行コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="9278a-159">The run command arguments:</span></span>
  * <span data-ttu-id="9278a-160">擬似端末を割り当てて、接続されていない場合でも開いた状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="9278a-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="9278a-161">(`--interactive --tty` と効果は同じです。)</span><span class="sxs-lookup"><span data-stu-id="9278a-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="9278a-162">コンテナーが存在する場合は、自動的に削除します。</span><span class="sxs-lookup"><span data-stu-id="9278a-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="9278a-163">ローカル コンピューター上のポート 5000 をコンテナー内のポート 80 にマップします。</span><span class="sxs-lookup"><span data-stu-id="9278a-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="9278a-164">コンテナーに aspnetcore_sample という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9278a-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="9278a-165">aspnetapp イメージを指定します。</span><span class="sxs-lookup"><span data-stu-id="9278a-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="9278a-166">アプリをテストするには、ブラウザーで `http://localhost:5000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9278a-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="9278a-167">Windows コンテナーでの実行</span><span class="sxs-lookup"><span data-stu-id="9278a-167">Run in a Windows container</span></span>

* <span data-ttu-id="9278a-168">Docker クライアント上で、[Windows コンテナーに切り替えます](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。</span><span class="sxs-lookup"><span data-stu-id="9278a-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="9278a-169">`dotnet-docker/samples/aspnetapp` にある Docker ファイルのフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9278a-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="9278a-170">次のコマンドを実行して、Docker 内でサンプルをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="9278a-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="9278a-171">Windows コンテナーの場合、コンテナーの IP アドレスが必要です (`http://localhost:5000` の参照は機能しません)。</span><span class="sxs-lookup"><span data-stu-id="9278a-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="9278a-172">別のコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="9278a-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="9278a-173">`docker ps` を実行して、実行中のコンテナーを表示します。</span><span class="sxs-lookup"><span data-stu-id="9278a-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="9278a-174">"aspnetcore_sample" コンテナーがそこにあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9278a-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="9278a-175">`docker exec aspnetcore_sample ipconfig` を実行して、コンテナーの IP アドレスを表示します。</span><span class="sxs-lookup"><span data-stu-id="9278a-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="9278a-176">コマンドからの出力は、この例のようになります。</span><span class="sxs-lookup"><span data-stu-id="9278a-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="9278a-177">コンテナーの IPv4 アドレス (たとえば、172.29.245.43) をコピーして、ブラウザーのアドレス バーに貼り付けてアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="9278a-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="9278a-178">手動でビルドしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="9278a-178">Build and deploy manually</span></span>

<span data-ttu-id="9278a-179">一部のシナリオでは、実行時に必要なアプリのアセットをコピーすることで、アプリをコンテナーにデプロイする方がよい場合があります。</span><span class="sxs-lookup"><span data-stu-id="9278a-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="9278a-180">このセクションでは、手動によるデプロイの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9278a-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="9278a-181">*dotnet-docker/samples/aspnetapp/aspnetapp* にあるプロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9278a-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="9278a-182">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9278a-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="9278a-183">コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="9278a-183">The command arguments:</span></span>
  * <span data-ttu-id="9278a-184">リリース モードでアプリをビルドします (既定はデバッグ モードです)。</span><span class="sxs-lookup"><span data-stu-id="9278a-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="9278a-185">*published* フォルダーにアセットを作成します。</span><span class="sxs-lookup"><span data-stu-id="9278a-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="9278a-186">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9278a-186">Run the app.</span></span>

  * <span data-ttu-id="9278a-187">Windows の場合:</span><span class="sxs-lookup"><span data-stu-id="9278a-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="9278a-188">Linux の場合:</span><span class="sxs-lookup"><span data-stu-id="9278a-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="9278a-189">`http://localhost:5000` を参照してホーム ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="9278a-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="9278a-190">Docker コンテナー内で手動で発行されたアプリを使用するには、新しい *Dockerfile* を作成し、`docker build .` コマンドを使用してコンテナーをビルドします。</span><span class="sxs-lookup"><span data-stu-id="9278a-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="9278a-191">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="9278a-191">The Dockerfile</span></span>

<span data-ttu-id="9278a-192">ここに示すのは、先ほど実行した `docker build` コマンドで使用された *Dockerfile* です。</span><span class="sxs-lookup"><span data-stu-id="9278a-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="9278a-193">このセクションで実行したときと同じ方法で `dotnet publish` を使用して、ビルドとデプロイを行います。</span><span class="sxs-lookup"><span data-stu-id="9278a-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="9278a-194">前の *Dockerfile* では、`*.csproj` ファイルは別個の "*レイヤー*" としてコピーおよび復元されます。</span><span class="sxs-lookup"><span data-stu-id="9278a-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="9278a-195">`docker build` コマンドを使用してイメージをビルドすると、組み込みのキャッシュが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9278a-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="9278a-196">`docker build` コマンドが最後に実行されてから `*.csproj` ファイルが変更されていない場合、`dotnet restore` コマンドを再度実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9278a-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="9278a-197">代わりに、対応する `dotnet restore` レイヤーの組み込みキャッシュが再利用されます。</span><span class="sxs-lookup"><span data-stu-id="9278a-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="9278a-198">詳細については、「[Dockerfile を記述するためのベスト プラクティス](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9278a-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="9278a-199">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="9278a-199">The Dockerfile</span></span>

<span data-ttu-id="9278a-200">ここに示すのは、先ほど実行した `docker build` コマンドで使用された *Dockerfile* です。</span><span class="sxs-lookup"><span data-stu-id="9278a-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="9278a-201">このセクションで実行したときと同じ方法で `dotnet publish` を使用して、ビルドとデプロイを行います。</span><span class="sxs-lookup"><span data-stu-id="9278a-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="9278a-202">前の Dockerfile で示されているように、`*.csproj` ファイルは別個の "*レイヤー*" としてコピーおよび復元されます。</span><span class="sxs-lookup"><span data-stu-id="9278a-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="9278a-203">`docker build` コマンドを使用してイメージをビルドすると、組み込みのキャッシュが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9278a-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="9278a-204">`docker build` コマンドが最後に実行されてから `*.csproj` ファイルが変更されていない場合、`dotnet restore` コマンドを再度実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9278a-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="9278a-205">代わりに、対応する `dotnet restore` レイヤーの組み込みキャッシュが再利用されます。</span><span class="sxs-lookup"><span data-stu-id="9278a-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="9278a-206">詳細については、「[Dockerfile を記述するためのベスト プラクティス](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9278a-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="9278a-207">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="9278a-207">The Dockerfile</span></span>

<span data-ttu-id="9278a-208">ここに示すのは、先ほど実行した `docker build` コマンドで使用された *Dockerfile* です。</span><span class="sxs-lookup"><span data-stu-id="9278a-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="9278a-209">このセクションで実行したときと同じ方法で `dotnet publish` を使用して、ビルドとデプロイを行います。</span><span class="sxs-lookup"><span data-stu-id="9278a-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9278a-210">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9278a-210">Additional resources</span></span>

* [<span data-ttu-id="9278a-211">Docker の build コマンド</span><span class="sxs-lookup"><span data-stu-id="9278a-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="9278a-212">Docker の run コマンド</span><span class="sxs-lookup"><span data-stu-id="9278a-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="9278a-213">[ASP.NET Core の Docker サンプル](https://github.com/dotnet/dotnet-docker) (このチュートリアルで使用されたものです。)</span><span class="sxs-lookup"><span data-stu-id="9278a-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="9278a-214">プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する</span><span class="sxs-lookup"><span data-stu-id="9278a-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="9278a-215">Visual Studio Docker ツールの使用</span><span class="sxs-lookup"><span data-stu-id="9278a-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="9278a-216">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="9278a-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="9278a-217">ドッカーと小さなコンテナを使用した GC</span><span class="sxs-lookup"><span data-stu-id="9278a-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="9278a-218">次の手順</span><span class="sxs-lookup"><span data-stu-id="9278a-218">Next steps</span></span>

<span data-ttu-id="9278a-219">同じアプリを格納している Git リポジトリにも、ドキュメントが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9278a-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="9278a-220">リポジトリ内にある利用可能なリソースの概要については、[README ファイル](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9278a-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="9278a-221">特に、HTTPS を実装する方法について確認してください。</span><span class="sxs-lookup"><span data-stu-id="9278a-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="9278a-222">「[Developing ASP.NET Core Applications with Docker over HTTPS (Docker を使用して HTTPS による ASP.NET Core アプリケーションを開発する)](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)」</span><span class="sxs-lookup"><span data-stu-id="9278a-222">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)</span></span>

---
title: ASP.NET Core で .NET Core gRPC のクライアントとサーバーを作成する
author: juntaoluo
description: このチュートリアルでは、ASP.NET Core で gRPC サービスと gRPC クライアントを作成する方法を示します。 gRPC サービス プロジェクトの作成方法、proto ファイルの編集方法、二重ストリーミング呼び出しの追加方法について学習します。
ms.author: johluo
ms.date: 10/23/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 9388a2f814008ebb50171f85b8baccf6dadfac27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057025"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="16216-104">チュートリアル: ASP.NET Core で gRPC のクライアントとサーバーを作成する</span><span class="sxs-lookup"><span data-stu-id="16216-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="16216-105">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="16216-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="16216-106">このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="16216-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="16216-107">最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。</span><span class="sxs-lookup"><span data-stu-id="16216-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="16216-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="16216-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="16216-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="16216-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="16216-110">gRPC サービスを作成する。</span><span class="sxs-lookup"><span data-stu-id="16216-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="16216-111">gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="16216-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="16216-112">gRPC あいさつサービスで gRPC クライアント サービスをテストする。</span><span class="sxs-lookup"><span data-stu-id="16216-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="16216-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="16216-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16216-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16216-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="16216-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16216-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16216-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="16216-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="16216-117">Visual Studio for Mac バージョン 8.7 以降</span><span class="sxs-lookup"><span data-stu-id="16216-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="16216-118">gRPC サービスの作成</span><span class="sxs-lookup"><span data-stu-id="16216-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16216-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16216-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="16216-120">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-120">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="16216-121">または、Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="16216-122">**[新しいプロジェクトの作成]** ダイアログで、 **[gRPC サービス]** を選択して、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next** :</span></span>

  ![Visual Studio の [新しいプロジェクトの作成] ダイアログ](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="16216-124">プロジェクトに **GrpcGreeter** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="16216-124">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="16216-125">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="16216-125">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="16216-126">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-126">Select **Create**.</span></span>
* <span data-ttu-id="16216-127">**[Create a new gRPC service]\(新しい gPRC サービスの作成\)** ダイアログで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="16216-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="16216-128">**gRPC サービス** テンプレートが選択されています。</span><span class="sxs-lookup"><span data-stu-id="16216-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="16216-129">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-129">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16216-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16216-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="16216-131">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="16216-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="16216-132">ディレクトリ (`cd`) を、プロジェクトのフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="16216-132">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="16216-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="16216-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="16216-134">`dotnet new` コマンドでは、 *GrpcGreeter* フォルダー内に新しい gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="16216-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="16216-135">`code` コマンドでは、Visual Studio Code の新しいインスタンス内に *GrpcGreeter* フォルダーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="16216-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="16216-136">" **ビルドとデバッグに必要な資産が 'GrpcGreeter' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="16216-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="16216-137">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-137">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16216-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="16216-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="16216-139">Visual Studio for Mac を起動し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-139">Start Visual Studio for Mac and select **Create a new project**.</span></span> <span data-ttu-id="16216-140">または、Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="16216-141">**[新しいプロジェクトの作成]** ダイアログで、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[gRPC サービス]** の順に選択して **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next** :</span></span>

  ![macOS での [新しいプロジェクトの作成] ダイアログ](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="16216-143">ターゲット フレームワークとして **[.NET Core 3.1]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-143">Select **.NET Core 3.1** for the target framework and select **Next**.</span></span>
* <span data-ttu-id="16216-144">プロジェクトに **GrpcGreeter** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="16216-144">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="16216-145">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="16216-145">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="16216-146">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-146">Select **Create**.</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="16216-147">サービスを実行する</span><span class="sxs-lookup"><span data-stu-id="16216-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="16216-148">サービスが `https://localhost:5001` でリッスンしていることがログに示されます。</span><span class="sxs-lookup"><span data-stu-id="16216-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="16216-149">gRPC テンプレートは[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="16216-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="16216-150">gRPC クライアントでは、HTTPS を使用してサーバーを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="16216-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="16216-151">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="16216-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="16216-152">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="16216-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="16216-153">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16216-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="16216-154">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="16216-154">Examine the project files</span></span>

<span data-ttu-id="16216-155">*GrpcGreeter* プロジェクト ファイル:</span><span class="sxs-lookup"><span data-stu-id="16216-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="16216-156">*greet.proto* : *Protos/greet.proto* ファイルは、`Greeter` gRPC を定義し、gRPC サーバー資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="16216-156">*greet.proto* : The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="16216-157">詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16216-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="16216-158">*Services* フォルダー:`Greeter` サービスの実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="16216-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="16216-159">*appSettings.json* :Kestrel で使用されるプロトコルなどの構成データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="16216-159">*appSettings.json* : Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="16216-160">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16216-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="16216-161">*Program.cs* :gRPC サービスのエントリ ポイントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="16216-161">*Program.cs* : Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="16216-162">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16216-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="16216-163">*Startup.cs* :アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="16216-163">*Startup.cs* : Contains code that configures app behavior.</span></span> <span data-ttu-id="16216-164">詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16216-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="16216-165">.NET コンソール アプリで gRPC クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="16216-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16216-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16216-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="16216-167">Visual Studio のインスタンスをもう 1 つ開き、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-167">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="16216-168">**[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="16216-169">**[プロジェクト名]** テキスト ボックスに「 **GrpcGreeterClient** 」を入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16216-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16216-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="16216-171">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="16216-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="16216-172">ディレクトリ (`cd`) を、プロジェクトのフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="16216-172">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="16216-173">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="16216-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16216-174">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="16216-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="16216-175">「 [Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築](/dotnet/core/tutorials/using-on-mac-vs-full-solution)」の手順に従って、 *GrpcGreeterClient* という名前のコンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="16216-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="16216-176">必要なパッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="16216-176">Add required packages</span></span>

<span data-ttu-id="16216-177">gRPC クライアント プロジェクトには、次のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="16216-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="16216-178">.NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。</span><span class="sxs-lookup"><span data-stu-id="16216-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="16216-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="16216-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="16216-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="16216-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="16216-181">ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。</span><span class="sxs-lookup"><span data-stu-id="16216-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16216-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16216-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16216-183">パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="16216-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="16216-184">パッケージをインストールするための PMC オプション</span><span class="sxs-lookup"><span data-stu-id="16216-184">PMC option to install packages</span></span>

* <span data-ttu-id="16216-185">Visual Studio で **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="16216-186">**[パッケージ マネージャー コンソール]** ウィンドウから `cd GrpcGreeterClient` を実行し、 *GrpcGreeterClient.csproj* ファイルが含まれるフォルダーにディレクトリを変更します。</span><span class="sxs-lookup"><span data-stu-id="16216-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="16216-187">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="16216-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="16216-188">パッケージをインストールするための [NuGet パッケージの管理] オプション</span><span class="sxs-lookup"><span data-stu-id="16216-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="16216-189">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="16216-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="16216-190">**[参照]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="16216-191">検索ボックスに「 **Grpc.Net.Client** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="16216-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="16216-192">**[参照]** タブから **Grpc.Net.Client** パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="16216-193">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="16216-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16216-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16216-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="16216-195">**統合ターミナル** から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="16216-195">Run the following commands from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16216-196">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="16216-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="16216-197">**[Solution Pad]** で **[GrpcGreeterClient]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="16216-198">検索ボックスに「 **Grpc.Net.Client** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="16216-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="16216-199">結果ペインから **Grpc.Net.Client** パッケージを選択し、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**.</span></span>
* <span data-ttu-id="16216-200">**[Accept License]\(ライセンスに同意する\)** ダイアログの **[承諾]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-200">Select the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="16216-201">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="16216-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="16216-202">greet.proto を追加する</span><span class="sxs-lookup"><span data-stu-id="16216-202">Add greet.proto</span></span>

* <span data-ttu-id="16216-203">gRPC クライアント プロジェクトで *Protos* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="16216-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="16216-204">gRPC あいさつサービスから gRPC クライアント プロジェクトに *Protos\greet.proto* ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="16216-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="16216-205">`greet.proto` ファイル内の名前空間を、プロジェクトの名前空間に更新します。</span><span class="sxs-lookup"><span data-stu-id="16216-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="16216-206">*GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="16216-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="16216-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16216-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="16216-208">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="16216-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16216-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="16216-210">*GrpcGreeterClient.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16216-211">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="16216-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="16216-212">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16216-212">Right-click the project and select **Edit Project File**.</span></span>

  ---

* <span data-ttu-id="16216-213">*greet.proto* ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="16216-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="16216-214">Greeter クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="16216-214">Create the Greeter client</span></span>

<span data-ttu-id="16216-215">クライアント プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。</span><span class="sxs-lookup"><span data-stu-id="16216-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="16216-216">`GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="16216-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="16216-217">次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="16216-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="16216-218">*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="16216-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="16216-219">Greeter クライアントは、次の方法で作成されます。</span><span class="sxs-lookup"><span data-stu-id="16216-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="16216-220">gRPC サービスへの接続を作成するための情報が含まれている `GrpcChannel` をインスタンス化する。</span><span class="sxs-lookup"><span data-stu-id="16216-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="16216-221">`GrpcChannel` を使用して、Greeter クライアントを構築します。</span><span class="sxs-lookup"><span data-stu-id="16216-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="16216-222">Greeter クライアントから非同期の `SayHello` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="16216-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="16216-223">`SayHello` 呼び出しの結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="16216-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="16216-224">gRPC あいさつサービスで gRPC クライアントをテストする</span><span class="sxs-lookup"><span data-stu-id="16216-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16216-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16216-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="16216-226">あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="16216-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="16216-227">`GrpcGreeterClient` プロジェクトで、`Ctrl+F5` 押してデバッガーなしでクライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="16216-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16216-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16216-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="16216-229">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="16216-229">Start the Greeter service.</span></span>
* <span data-ttu-id="16216-230">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="16216-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16216-231">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="16216-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="16216-232">前述の [macOS の HTTP/2 TLS 問題の回避策](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)により、クライアントのチャンネル アドレスを "http://localhost:5000" に更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16216-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="16216-233">**GrpcGreeterClient/Program.cs** の 13 行目を次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="16216-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="16216-234">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="16216-234">Start the Greeter service.</span></span>
* <span data-ttu-id="16216-235">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="16216-235">Start the client.</span></span>

---

<span data-ttu-id="16216-236">クライアントにより、その名前 *GreeterClient* が含まれるあいさつメッセージが、サービスに送信されます。</span><span class="sxs-lookup"><span data-stu-id="16216-236">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="16216-237">サービスから応答として "Hello GreeterClient" のメッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="16216-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="16216-238">"Hello GreeterClient" の応答がコマンド プロンプトに表示されます。</span><span class="sxs-lookup"><span data-stu-id="16216-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="16216-239">gRPC サービスにより、成功した呼び出しの詳細が、コマンド プロンプトに書き込まれるログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="16216-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="16216-240">この記事のコードでは、gRPC サービスをセキュリティで保護するために、ASP.NET Core HTTPS 開発証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="16216-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="16216-241">.NET gRPC クライアントが `The remote certificate is invalid according to the validation procedure.` または `The SSL connection could not be established.` というメッセージで失敗する場合、その開発証明書は信頼されていません。</span><span class="sxs-lookup"><span data-stu-id="16216-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="16216-242">この問題を解決するには、「[信頼されていないか無効な証明書で gRPC サービスを呼び出す](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16216-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="16216-243">次の手順</span><span class="sxs-lookup"><span data-stu-id="16216-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

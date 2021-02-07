---
title: ASP.NET Core の構成
author: rick-anderson
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 62c9d1a58e0f771d91e2bc57f39ec5ebb25baaed
ms.sourcegitcommit: 37186f76e4a50d7fb7389026dd0e5e234b51ebb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99541369"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="4f3bc-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="4f3bc-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="4f3bc-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4f3bc-105">ASP.NET Core の構成は、1つまたは複数の[構成プロバイダー](#cp)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="4f3bc-106">構成プロバイダーは、以下のようなさまざまな構成ソースを使用して、キーと値のペアから構成データを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="4f3bc-107">*appsettings.json* などの設定ファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="4f3bc-108">環境変数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-108">Environment variables</span></span>
* <span data-ttu-id="4f3bc-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4f3bc-109">Azure Key Vault</span></span>
* <span data-ttu-id="4f3bc-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4f3bc-110">Azure App Configuration</span></span>
* <span data-ttu-id="4f3bc-111">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-111">Command-line arguments</span></span>
* <span data-ttu-id="4f3bc-112">インストール済みまたは作成済みのカスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="4f3bc-113">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-113">Directory files</span></span>
* <span data-ttu-id="4f3bc-114">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="4f3bc-114">In-memory .NET objects</span></span>

<span data-ttu-id="4f3bc-115">このトピックでは ASP.NET Core の構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="4f3bc-116">コンソール アプリでの構成の使用方法について詳しくは、[.NET 構成](/dotnet/core/extensions/configuration)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="4f3bc-117">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="4f3bc-118">既定の構成</span><span class="sxs-lookup"><span data-stu-id="4f3bc-118">Default configuration</span></span>

<span data-ttu-id="4f3bc-119">[dotnet new](/dotnet/core/tools/dotnet-new) または Visual Studio で作成された ASP.NET Core の web アプリが、次のコードを生成します:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="4f3bc-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="4f3bc-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) は:既存の `IConfiguration` をソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="4f3bc-122">既定の構成では、[ホスト](#hvac)構成を追加し、_アプリ_ 構成の最初のソースとして設定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="4f3bc-123">[JSON 構成プロバイダー](#file-configuration-provider)を使用する [appsettings.json](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="4f3bc-124">[JSON 構成プロバイダー](#file-configuration-provider)を使用する *appsettings.* `Environment`*json*。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="4f3bc-125">たとえば、*appsettings*.***Production\*\*_._json* および *appsettings*.\*\*\*Development** _._json\*。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="4f3bc-126">`Development` 環境でアプリが実行される際の [App シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="4f3bc-127">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="4f3bc-128">[コマンドライン構成プロバイダー](#command-line)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="4f3bc-129">後から追加される構成プロバイダーは、それ以前のキー設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="4f3bc-130">たとえば、`MyKey` が *appsettings.json* と環境の両方で設定されている場合、環境の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="4f3bc-131">既定の構成プロバイダーを使用すると、[コマンドライン構成プロバイダー](#clcp) が他のすべてのプロバイダーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="4f3bc-132">`CreateDefaultBuilder` の詳細については、[既定のビルダー設定](xref:fundamentals/host/generic-host#default-builder-settings)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="4f3bc-133">以下のコードでは、追加した順に、有効な構成プロバイダーが表示されます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="4f3bc-134">次の *appsettings.json* ファイルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="4f3bc-135">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-136">既定の <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> は、以下の順序で構成を読み込みます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="4f3bc-137">*appsettings.* `Environment` *.json*:たとえば、*appsettings*.***Production\*\*_._json* および *appsettings*.\*\*\*Development** _._json\* ファイル。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="4f3bc-138">ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="4f3bc-139">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4f3bc-140">*appsettings*.`Environment`.*json* の値によって、 *appsettings.json* 内のキーがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="4f3bc-141">たとえば、既定では次のようになります:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-141">For example, by default:</span></span>

* <span data-ttu-id="4f3bc-142">開発中は、*appsettings*.***Development** _._json* 構成によって、 *appsettings.json* で見つかった値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="4f3bc-143">運用環境では、*appsettings*.***Production** _._json* 構成によって、 *appsettings.json* で見つかった値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="4f3bc-144">たとえば、Azure にアプリをデプロイする場合。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="4f3bc-145">オプションパターンを使用して、階層型の構成データをバインドします</span><span class="sxs-lookup"><span data-stu-id="4f3bc-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="4f3bc-146">[既定](#default)の構成を利用する場合、[reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) で *appsettings.json* と *appsettings.* `Environment` *.json* ファイルを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="4f3bc-147">アプリの開始 ***後*** に *appsettings.json* と *appsettings.* `Environment` *.json* ファイルに加えられた変更は、[JSON 構成プロバイダー](#jcp)によって読み取られます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4f3bc-148">追加の JSON 構成ファイルを追加する方法の詳細については、このドキュメント中の「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="4f3bc-149">サービス コレクションの結合</span><span class="sxs-lookup"><span data-stu-id="4f3bc-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="4f3bc-150">セキュリティとユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="4f3bc-150">Security and user secrets</span></span>

<span data-ttu-id="4f3bc-151">構成データのガイドライン:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="4f3bc-152">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="4f3bc-153">[Secret Manager](xref:security/app-secrets) ツールを使用すると、開発時にシークレットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="4f3bc-154">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="4f3bc-155">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="4f3bc-156">[既定](#default)では、ユーザー シークレットの構成ソースは、JSON 構成ソースの後に登録されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="4f3bc-157">このため、ユーザー シークレット キーは、 *appsettings.json* および *appsettings.* `Environment` *.json* よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="4f3bc-158">パスワードその他の機密データの格納については、次を参照してください：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="4f3bc-159"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="4f3bc-160">Secret Manager ツールでは、[ファイル構成プロバイダー](#fcp)を使用して、ユーザーのシークレットがローカル システム上の JSON ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="4f3bc-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="4f3bc-162">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="4f3bc-163">環境変数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-163">Environment variables</span></span>

<span data-ttu-id="4f3bc-164">[既定](#default)の構成を使用すると、<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> によって、 *appsettings.json* 、*appsettings.* `Environment` *.json*、および [ユーザー シークレット](xref:security/app-secrets)の読み取り後に、環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="4f3bc-165">そのため、環境から読み取られたキー値によって、 *appsettings.json* 、*appsettings.* `Environment` *.json*、およびユーザー シークレットから読み取られた値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4f3bc-166">次の `set` コマンドは：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-166">The following `set` commands:</span></span>

* <span data-ttu-id="4f3bc-167">Windows で[ 上記の例 ](#appsettingsjson)の環境キーと値を設定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="4f3bc-168">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)を使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="4f3bc-169">`dotnet run` コマンドは、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="4f3bc-170">上記の環境設定は：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-170">The preceding environment settings:</span></span>

* <span data-ttu-id="4f3bc-171">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="4f3bc-172">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="4f3bc-173">次の [setx](/windows-server/administration/windows-commands/setx) コマンドで、Windows 上の環境キーと値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="4f3bc-174">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="4f3bc-175">`/M` は、システム環境で変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="4f3bc-176">`/M` スイッチが使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="4f3bc-177">上記のコマンドによって、 *appsettings.json* および *appsettings.* `Environment` *.json* がオーバーライドされるのをテストするには:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="4f3bc-178">Visual Studio の場合:Visual Studio を終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="4f3bc-179">CLI の場合:新しいコマンド ウィンドウを起動し、`dotnet run` を入力します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="4f3bc-180">環境変数のプレフィックスを指定する文字列を指定して <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="4f3bc-181">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-181">In the preceding code:</span></span>

* <span data-ttu-id="4f3bc-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` は[既定の構成プロバイダー](#default)の後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="4f3bc-183">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="4f3bc-184">`MyCustomPrefix_` プレフィックスを使用して設定された環境変数は、[既定の構成プロバイダー](#default)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="4f3bc-185">これには、プレフィックスのない環境変数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="4f3bc-186">構成のキーと値のペアの読み取り時に、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="4f3bc-187">次のコマンドは、カスタム プレフィックスをテストします：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="4f3bc-188">[既定の構成](#default)では、`DOTNET_` と `ASPNETCORE_` のプレフィックスが付いた環境変数とコマンド ライン引数を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="4f3bc-189">`DOTNET_` と `ASPNETCORE_` のプレフィックスは ASP.NET Core によって[ホストとアプリの構成](xref:fundamentals/host/generic-host#host-configuration)に使用されますが、ユーザーの構成には使用されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="4f3bc-190">ホストとアプリの構成の詳細については、「[.NET 汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="4f3bc-191">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの **新しいアプリケーション設定** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="4f3bc-192">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="4f3bc-193">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="4f3bc-194">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-194">Exposed as environment variables.</span></span>

<span data-ttu-id="4f3bc-195">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4f3bc-196">Azure データベース接続文字列の詳細については、「[接続文字列のプレフィックス](#constr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="4f3bc-197">環境変数の名前付け</span><span class="sxs-lookup"><span data-stu-id="4f3bc-197">Naming of environment variables</span></span>

<span data-ttu-id="4f3bc-198">環境変数名には、 *appsettings.json* ファイルの構造が反映されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="4f3bc-199">階層内の各要素は、二重アンダースコア (推奨) またはコロンによって区切られます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="4f3bc-200">要素構造に配列が含まれている場合は、配列のインデックスをこのパスの追加の要素名として扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="4f3bc-201">次の *appsettings.json* ファイルと、環境変数として表されたその同等の値を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="4f3bc-202">**環境変数**</span><span class="sxs-lookup"><span data-stu-id="4f3bc-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="4f3bc-203">launchSettings.json で設定された環境変数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-203">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="4f3bc-204">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="4f3bc-205">コマンド ライン</span><span class="sxs-lookup"><span data-stu-id="4f3bc-205">Command-line</span></span>

<span data-ttu-id="4f3bc-206">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> は、以下の構成ソースの後にコマンド ライン引数のキーと値のペアから構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-206">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="4f3bc-207">*appsettings.json* および *appsettings*.`Environment`.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-207">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="4f3bc-208">開発環境での[アプリ シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-208">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4f3bc-209">環境変数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-209">Environment variables.</span></span>

<span data-ttu-id="4f3bc-210">[既定](#default)では、コマンド ラインで設定された構成値は、他のすべての構成プロバイダーで設定された構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-210">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="4f3bc-211">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-211">Command-line arguments</span></span>

<span data-ttu-id="4f3bc-212">次のコマンドは `=` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-212">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="4f3bc-213">次のコマンドは `/` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-213">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="4f3bc-214">次のコマンドは `--` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-214">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="4f3bc-215">キーの値:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-215">The key value:</span></span>

* <span data-ttu-id="4f3bc-216">`=` の後に続ける必要があります。または、値がスペースの後にある場合は、キーのプレフィックスが `--` または `/` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-216">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="4f3bc-217">`=` を使用する場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-217">Isn't required if `=` is used.</span></span> <span data-ttu-id="4f3bc-218">たとえば、`MySetting=` のようにします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-218">For example, `MySetting=`.</span></span>

<span data-ttu-id="4f3bc-219">同じコマンド内では、`=` を使用するコマンド ライン引数のキーと値のペアを、スペースを使用するキーと値のペアと混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-219">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="4f3bc-220">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="4f3bc-220">Switch mappings</span></span>

<span data-ttu-id="4f3bc-221">スイッチ マッピングでは、**キー** 名の置換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-221">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="4f3bc-222"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換するディクショナリを提供します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-222">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="4f3bc-223">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-223">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="4f3bc-224">ディクショナリ中にコマンド ライン キーが見つかった場合は、そのディクショナリの値が返され、キーと値のペアがアプリの構成に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-224">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="4f3bc-225">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-225">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="4f3bc-226">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-226">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="4f3bc-227">スイッチは `-` または `--`で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-227">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="4f3bc-228">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-228">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="4f3bc-229">スイッチ マッピング ディクショナリを使用するには、それを `AddCommandLine` の呼び出しに渡します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-229">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="4f3bc-230">次のコードは、置換されたキーのキー値を示しています：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-230">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-231">次のコマンドを実行して、キーの置換をテストできます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-231">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="4f3bc-232">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-232">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="4f3bc-233">`CreateDefaultBuilder` メソッドの `AddCommandLine` 呼び出しには、マップされたスイッチが含まれていないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-233">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4f3bc-234">解決策は、`CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-234">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="4f3bc-235">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="4f3bc-235">Hierarchical configuration data</span></span>

<span data-ttu-id="4f3bc-236">構成 API では、構成キーの区切り記号を使用して階層データをフラット化することにより、階層型の構成データの読み取りが行われます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-236">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="4f3bc-237">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *appsettings.json* ファイルが含まれます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-237">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="4f3bc-238">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-238">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-239">階層型の構成データを読み取る方法としては、オプション パターンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-239">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="4f3bc-240">詳細については、このドキュメント中の「[階層型の構成データをバインドする](#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-240">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="4f3bc-241"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-241"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="4f3bc-242">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-242">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="4f3bc-243">構成キーと値</span><span class="sxs-lookup"><span data-stu-id="4f3bc-243">Configuration keys and values</span></span>

<span data-ttu-id="4f3bc-244">構成キー:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-244">Configuration keys:</span></span>

* <span data-ttu-id="4f3bc-245">構成キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-245">Are case-insensitive.</span></span> <span data-ttu-id="4f3bc-246">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-246">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="4f3bc-247">キーと値が複数の構成プロバイダーで設定されている場合は、最後に追加されたプロバイダーの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-247">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="4f3bc-248">詳細については、「[既定の構成](#default)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-248">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="4f3bc-249">階層キー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-249">Hierarchical keys</span></span>
  * <span data-ttu-id="4f3bc-250">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-250">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="4f3bc-251">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-251">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="4f3bc-252">ダブル アンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロン `:` に自動で変換されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-252">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="4f3bc-253">Azure Key Vault では、階層型のキーは区切り記号に `--` を使用します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-253">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="4f3bc-254">シークレットがアプリの構成に読み込まれると、[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)によって `--` が `:` に自動的に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-254">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="4f3bc-255"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-255">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4f3bc-256">配列のバインドについては、「[配列をクラスにバインドする](#boa)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-256">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="4f3bc-257">構成値:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-257">Configuration values:</span></span>

* <span data-ttu-id="4f3bc-258">構成値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-258">Are strings.</span></span>
* <span data-ttu-id="4f3bc-259">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-259">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="4f3bc-260">構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-260">Configuration providers</span></span>

<span data-ttu-id="4f3bc-261">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-261">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="4f3bc-262">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-262">Provider</span></span> | <span data-ttu-id="4f3bc-263">以下から構成を提供します</span><span class="sxs-lookup"><span data-stu-id="4f3bc-263">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="4f3bc-264">Azure Key Vault 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-264">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="4f3bc-265">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4f3bc-265">Azure Key Vault</span></span> |
| [<span data-ttu-id="4f3bc-266">Azure App Configuration プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-266">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="4f3bc-267">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4f3bc-267">Azure App Configuration</span></span> |
| [<span data-ttu-id="4f3bc-268">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-268">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="4f3bc-269">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="4f3bc-269">Command-line parameters</span></span> |
| [<span data-ttu-id="4f3bc-270">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-270">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="4f3bc-271">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="4f3bc-271">Custom source</span></span> |
| [<span data-ttu-id="4f3bc-272">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-272">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="4f3bc-273">環境変数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-273">Environment variables</span></span> |
| [<span data-ttu-id="4f3bc-274">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-274">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="4f3bc-275">INI、JSON、および XML ファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-275">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="4f3bc-276">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-276">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="4f3bc-277">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-277">Directory files</span></span> |
| [<span data-ttu-id="4f3bc-278">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-278">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="4f3bc-279">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="4f3bc-279">In-memory collections</span></span> |
| [<span data-ttu-id="4f3bc-280">ユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="4f3bc-280">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="4f3bc-281">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-281">File in the user profile directory</span></span> |

<span data-ttu-id="4f3bc-282">構成ソースは、構成プロバイダーで指定された順序で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-282">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="4f3bc-283">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-283">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="4f3bc-284">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-284">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="4f3bc-285">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="4f3bc-285">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="4f3bc-286">ユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="4f3bc-286">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="4f3bc-287">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-287">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="4f3bc-288">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-288">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="4f3bc-289">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするには、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-289">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="4f3bc-290">上記の一連のプロバイダーは、[既定の構成](#default)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-290">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="4f3bc-291">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-291">Connection string prefixes</span></span>

<span data-ttu-id="4f3bc-292">構成 API には、4つの接続文字列環境変数に対する特別なプロセスルールがあります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-292">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="4f3bc-293">これらの接続文字列は、アプリ環境用の Azure 接続文字列の構成に含まれています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-293">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="4f3bc-294">表に示されたプレフィックスを持つ環境変数は、[既定の構成](#default) を使用するとき、または `AddEnvironmentVariables` にプレフィックスが指定されていない場合に、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-294">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="4f3bc-295">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-295">Connection string prefix</span></span> | <span data-ttu-id="4f3bc-296">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-296">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="4f3bc-297">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-297">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="4f3bc-298">MySQL</span><span class="sxs-lookup"><span data-stu-id="4f3bc-298">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="4f3bc-299">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="4f3bc-299">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="4f3bc-300">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4f3bc-300">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="4f3bc-301">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-301">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="4f3bc-302">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-302">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="4f3bc-303">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-303">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="4f3bc-304">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-304">Environment variable key</span></span> | <span data-ttu-id="4f3bc-305">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-305">Converted configuration key</span></span> | <span data-ttu-id="4f3bc-306">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="4f3bc-306">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4f3bc-307">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-307">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4f3bc-308">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4f3bc-309">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-309">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4f3bc-310">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-310">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4f3bc-311">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-311">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4f3bc-312">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-312">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4f3bc-313">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-313">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="4f3bc-314">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-314">File configuration provider</span></span>

<span data-ttu-id="4f3bc-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-315"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="4f3bc-316">以下の構成プロバイダーは `FileConfigurationProvider` から派生したものです：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-316">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="4f3bc-317">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-317">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="4f3bc-318">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-318">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="4f3bc-319">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-319">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="4f3bc-320">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-320">INI configuration provider</span></span>

<span data-ttu-id="4f3bc-321"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-321">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="4f3bc-322">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="4f3bc-323">上記のコードでは、*MyIniConfig.ini* と *MyIniConfig*.`Environment`.*ini* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-323">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="4f3bc-324">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-324">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="4f3bc-325">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-325">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4f3bc-326">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyIniConfig* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="4f3bc-327">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="4f3bc-328">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-328">JSON configuration provider</span></span>

<span data-ttu-id="4f3bc-329"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、JSON ファイルのキーと値のペアから構成が読み込みまれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-329">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="4f3bc-330">オーバーロードでは、次の指定ができます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-330">Overloads can specify:</span></span>

* <span data-ttu-id="4f3bc-331">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-331">Whether the file is optional.</span></span>
* <span data-ttu-id="4f3bc-332">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-332">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="4f3bc-333">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-333">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="4f3bc-334">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-334">The preceding code:</span></span>

* <span data-ttu-id="4f3bc-335">次のオプションを使用して、*MyConfig.json* ファイルを読み込むように JSON 構成プロバイダーを構成します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-335">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="4f3bc-336">`optional: true`:ファイルは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-336">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="4f3bc-337">`reloadOnChange: true` は、次のとおりです。変更が保存されると、ファイルが再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-337">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="4f3bc-338">*MyConfig.json* ファイルの前に [既定の構成プロバイダー](#default)を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-338">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="4f3bc-339">[環境変数構成プロバイダー](#evcp) および [コマンド ライン構成プロバイダー](#clcp)を含む、既定の構成プロバイダーでの *MyConfig.json* ファイルのオーバーライドの設定。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-339">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4f3bc-340">通常は、[環境変数構成プロバイダー](#evcp)および [コマンドライン構成プロバイダー](#clcp)で設定されている値をオーバーライドするカスタム JSON ファイルは ***必要ありません***。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-340">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4f3bc-341">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-341">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="4f3bc-342">上記のコードでは、*MyConfig.json* と *MyConfig*.`Environment`.*json* ファイルの設定は：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-342">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="4f3bc-343">*appsettings.json* および *appsettings*.`Environment`.*json* ファイル内の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-343">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="4f3bc-344">[環境変数の構成プロバイダー](#evcp)と[コマンドライン構成プロバイダー](#clcp)の設定によってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-344">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4f3bc-345">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *MyConfig.json* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-345">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="4f3bc-346">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-346">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="4f3bc-347">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-347">XML configuration provider</span></span>

<span data-ttu-id="4f3bc-348"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-348">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="4f3bc-349">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="4f3bc-350">上記のコードでは、*MyXMLFile.xml* と *MyXMLFile*.`Environment`.*xml* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-350">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="4f3bc-351">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="4f3bc-352">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4f3bc-353">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyXMLFile.xml* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="4f3bc-354">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-355">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-355">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="4f3bc-356">以下のコードでは、前の構成ファイルを読み取って、キーと値を表示します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-356">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-357">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-357">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="4f3bc-358">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-358">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4f3bc-359">key:attribute</span><span class="sxs-lookup"><span data-stu-id="4f3bc-359">key:attribute</span></span>
* <span data-ttu-id="4f3bc-360">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="4f3bc-360">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="4f3bc-361">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-361">Key-per-file configuration provider</span></span>

<span data-ttu-id="4f3bc-362"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-362">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="4f3bc-363">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-363">The key is the file name.</span></span> <span data-ttu-id="4f3bc-364">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-364">The value contains the file's contents.</span></span> <span data-ttu-id="4f3bc-365">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-365">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="4f3bc-366">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-366">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="4f3bc-367">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-367">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="4f3bc-368">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-368">Overloads permit specifying:</span></span>

* <span data-ttu-id="4f3bc-369">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-369">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="4f3bc-370">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-370">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="4f3bc-371">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-371">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="4f3bc-372">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-372">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="4f3bc-373">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-373">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="4f3bc-374">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-374">Memory configuration provider</span></span>

<span data-ttu-id="4f3bc-375"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-375">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="4f3bc-376">次のコードでは、構成システムにメモリコ レクションが追加されています：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-376">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="4f3bc-377">[サンプルのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の以下のコードでは、上記の構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-377">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-378">上記のコードでは、[既定の構成プロバイダー](#default)の後に `config.AddInMemoryCollection(Dict)` が追加されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-378">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="4f3bc-379">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-379">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4f3bc-380">`MemoryConfigurationProvider` を使用した別の例については、[配列をバインド](#boa)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-380">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="4f3bc-381">GetValue</span><span class="sxs-lookup"><span data-stu-id="4f3bc-381">GetValue</span></span>

<span data-ttu-id="4f3bc-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを使用して、構成から単一の値を抽出し、それを指定した型に変換します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-382">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-383">上記のコードでは、`NumberKey` が構成中に見つからない場合には `99` の既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-383">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="4f3bc-384">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="4f3bc-384">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="4f3bc-385">以下の例では、次の *MySubsection.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-385">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="4f3bc-386">以下のコードでは、*MySubsection セクション* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-386">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="4f3bc-387">GetSection</span><span class="sxs-lookup"><span data-stu-id="4f3bc-387">GetSection</span></span>

<span data-ttu-id="4f3bc-388">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) は、指定されたサブセクションのキーを持つ構成サブセクションを返します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-388">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="4f3bc-389">次のコードは、`section1` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-389">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-390">次のコードは、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-390">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-391">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-391">`GetSection` never returns `null`.</span></span> <span data-ttu-id="4f3bc-392">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-392">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="4f3bc-393">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-393">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="4f3bc-394"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-394">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="4f3bc-395">GetChildren と Exists</span><span class="sxs-lookup"><span data-stu-id="4f3bc-395">GetChildren and Exists</span></span>

<span data-ttu-id="4f3bc-396">次のコードは、[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) を呼び出し、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-396">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-397">上記のコードは、[ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を呼び出し、セクションが存在することを確認します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-397">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="4f3bc-398">配列をバインドする</span><span class="sxs-lookup"><span data-stu-id="4f3bc-398">Bind an array</span></span>

<span data-ttu-id="4f3bc-399">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) は、構成キーの配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-399">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4f3bc-400">数値のキー セグメントを公開する配列形式は、すべて [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-400">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="4f3bc-401">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)の *MyArray.json* について考えます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-401">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="4f3bc-402">次のコードでは、*MyArray.json* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-402">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="4f3bc-403">次のコードでは、構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-403">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-404">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="4f3bc-405">上記の出力では、インデックス3の値が `value40` になります。これは *MyArray. json* の `"4": "value40",` に対応しています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-405">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="4f3bc-406">このバインドされた配列インデックスは連続的であり、構成キーインデックスにバインドされていません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-406">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="4f3bc-407">構成バインダーは、バインドされたオブジェクトに null 値をバインドしたり、null エントリを作成したりはできません</span><span class="sxs-lookup"><span data-stu-id="4f3bc-407">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="4f3bc-408">次のコードでは、<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドで `array:entries` 構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-408">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="4f3bc-409">次のコードでは、`arrayDict` `Dictionary` の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-409">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-410">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-410">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="4f3bc-411">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-411">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="4f3bc-412">配列を含む構成データがバインドされている場合、構成キーの配列インデックスは、オブジェクト作成時の構成データの反復のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-412">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="4f3bc-413">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-413">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="4f3bc-414">インデックス&num;3 に不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、インデックス&num;3のキーと値のペアを読み取る構成プロバイダーによってサプライできます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-414">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="4f3bc-415">サンプルダウンロードの、次の *Value3.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-415">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="4f3bc-416">次のコードには、*Value3.json* と `arrayDict` `Dictionary` の構成が含まれています：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-416">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="4f3bc-417">次のコードは、上記の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-417">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-418">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="4f3bc-419">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-419">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="4f3bc-420">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-420">Custom configuration provider</span></span>

<span data-ttu-id="4f3bc-421">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-421">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="4f3bc-422">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-422">The provider has the following characteristics:</span></span>

* <span data-ttu-id="4f3bc-423">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-423">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="4f3bc-424">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-424">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="4f3bc-425">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-425">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="4f3bc-426">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-426">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="4f3bc-427">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-427">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="4f3bc-428">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-428">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="4f3bc-429">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-429">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="4f3bc-430">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-430">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="4f3bc-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-431">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="4f3bc-432"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-432">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="4f3bc-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-433">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="4f3bc-434"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-434">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="4f3bc-435">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-435">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="4f3bc-436">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-436">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="4f3bc-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-437">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="4f3bc-438">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-438">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="4f3bc-439">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-439">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="4f3bc-440">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-440">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="4f3bc-441">起動時の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-441">Access configuration in Startup</span></span>

<span data-ttu-id="4f3bc-442">次のコードでは `Startup` メソッドの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-442">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="4f3bc-443">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-443">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="4f3bc-444">Razor ページの構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="4f3bc-444">Access configuration in Razor Pages</span></span>

<span data-ttu-id="4f3bc-445">次のコードでは Razor ページの構成データが表示されます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-445">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="4f3bc-446">次のコードでは、`MyOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-446">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="4f3bc-447">次のマークアップは、[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、オプションの値を解決して表示します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-447">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="4f3bc-448">MVC ビューファイルの構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-448">Access configuration in a MVC view file</span></span>

<span data-ttu-id="4f3bc-449">次のコードでは、MVC ビューの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-449">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="4f3bc-450">デリゲートでオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="4f3bc-450">Configure options with a delegate</span></span>

<span data-ttu-id="4f3bc-451">デリゲートで構成されたオプションは、構成プロバイダーで設定された値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-451">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="4f3bc-452">サンプル アプリの例 2 では、デリゲートでオプションを構成しています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-452">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="4f3bc-453">次のコードでは、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-453">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="4f3bc-454">デリゲートを利用して `MyOptions` の値が構成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-454">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="4f3bc-455">このコードには、次のオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-455">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="4f3bc-456">先の例では、値 `Option1` と `Option2` が *appsettings.json* で指定されてから、構成されているデリゲートによりオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-456">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="4f3bc-457">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="4f3bc-457">Host versus app configuration</span></span>

<span data-ttu-id="4f3bc-458">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="4f3bc-459">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4f3bc-460">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="4f3bc-461">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="4f3bc-462">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="4f3bc-463">既定のホスト構成</span><span class="sxs-lookup"><span data-stu-id="4f3bc-463">Default host configuration</span></span>

<span data-ttu-id="4f3bc-464">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="4f3bc-465">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="4f3bc-466">[環境変数構成プロバイダー](#environment-variables)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="4f3bc-467">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="4f3bc-468">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="4f3bc-469">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="4f3bc-470">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="4f3bc-471">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="4f3bc-472">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="4f3bc-473">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="4f3bc-474">その他の構成</span><span class="sxs-lookup"><span data-stu-id="4f3bc-474">Other configuration</span></span>

<span data-ttu-id="4f3bc-475">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="4f3bc-476">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="4f3bc-477">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="4f3bc-478"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="4f3bc-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="4f3bc-479">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="4f3bc-480">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4f3bc-481">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-481">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="4f3bc-482">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-482">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="4f3bc-483">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="4f3bc-483">Add configuration from an external assembly</span></span>

<span data-ttu-id="4f3bc-484"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-484">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4f3bc-485">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-485">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f3bc-486">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4f3bc-486">Additional resources</span></span>

* [<span data-ttu-id="4f3bc-487">構成のソースコード</span><span class="sxs-lookup"><span data-stu-id="4f3bc-487">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4f3bc-488">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-488">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="4f3bc-489">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-489">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="4f3bc-490">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4f3bc-490">Azure Key Vault</span></span>
* <span data-ttu-id="4f3bc-491">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4f3bc-491">Azure App Configuration</span></span>
* <span data-ttu-id="4f3bc-492">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-492">Command-line arguments</span></span>
* <span data-ttu-id="4f3bc-493">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-493">Custom providers (installed or created)</span></span>
* <span data-ttu-id="4f3bc-494">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-494">Directory files</span></span>
* <span data-ttu-id="4f3bc-495">環境変数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-495">Environment variables</span></span>
* <span data-ttu-id="4f3bc-496">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="4f3bc-496">In-memory .NET objects</span></span>
* <span data-ttu-id="4f3bc-497">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-497">Settings files</span></span>

<span data-ttu-id="4f3bc-498">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-498">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4f3bc-499">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-499">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="4f3bc-500">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-500">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="4f3bc-501">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-501">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="4f3bc-502">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="4f3bc-503">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-503">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="4f3bc-504">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="4f3bc-504">Host versus app configuration</span></span>

<span data-ttu-id="4f3bc-505">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-505">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="4f3bc-506">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-506">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4f3bc-507">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-507">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="4f3bc-508">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-508">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="4f3bc-509">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-509">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="4f3bc-510">その他の構成</span><span class="sxs-lookup"><span data-stu-id="4f3bc-510">Other configuration</span></span>

<span data-ttu-id="4f3bc-511">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-511">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="4f3bc-512">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-512">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="4f3bc-513">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-513">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="4f3bc-514"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="4f3bc-514">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="4f3bc-515">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-515">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="4f3bc-516">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-516">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4f3bc-517">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-517">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="4f3bc-518">既定の構成</span><span class="sxs-lookup"><span data-stu-id="4f3bc-518">Default configuration</span></span>

<span data-ttu-id="4f3bc-519">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-519">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="4f3bc-520">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-520">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="4f3bc-521">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-521">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4f3bc-522">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-522">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="4f3bc-523">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-523">Host configuration is provided from:</span></span>
  * <span data-ttu-id="4f3bc-524">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-524">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="4f3bc-525">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-525">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="4f3bc-526">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="4f3bc-527">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-527">App configuration is provided from:</span></span>
  * <span data-ttu-id="4f3bc-528">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-528">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="4f3bc-529">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-529">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="4f3bc-530">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-530">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="4f3bc-531">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-531">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="4f3bc-532">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-532">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="4f3bc-533">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="4f3bc-533">Security</span></span>

<span data-ttu-id="4f3bc-534">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-534">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="4f3bc-535">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-535">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="4f3bc-536">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-536">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="4f3bc-537">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-537">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="4f3bc-538">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-538">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="4f3bc-539"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-539"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="4f3bc-540">Secret Manager ツールでは、ファイル構成プロバイダーを使用して、ユーザー シークレットがローカル システム上の JSON ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-540">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="4f3bc-541">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-541">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="4f3bc-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-542">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="4f3bc-543">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-543">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="4f3bc-544">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="4f3bc-544">Hierarchical configuration data</span></span>

<span data-ttu-id="4f3bc-545">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-545">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="4f3bc-546">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-546">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="4f3bc-547">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-547">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="4f3bc-548">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-548">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="4f3bc-549">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-549">section0:key0</span></span>
* <span data-ttu-id="4f3bc-550">section0:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-550">section0:key1</span></span>
* <span data-ttu-id="4f3bc-551">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-551">section1:key0</span></span>
* <span data-ttu-id="4f3bc-552">section1:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-552">section1:key1</span></span>

<span data-ttu-id="4f3bc-553"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-553"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="4f3bc-554">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-554">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="4f3bc-555">規約</span><span class="sxs-lookup"><span data-stu-id="4f3bc-555">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="4f3bc-556">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-556">Sources and providers</span></span>

<span data-ttu-id="4f3bc-557">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-557">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="4f3bc-558">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-558">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="4f3bc-559">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-559">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="4f3bc-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4f3bc-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-561"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="4f3bc-562">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-562">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="4f3bc-563">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-563">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="4f3bc-564">キー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-564">Keys</span></span>

<span data-ttu-id="4f3bc-565">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-565">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="4f3bc-566">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-566">Keys are case-insensitive.</span></span> <span data-ttu-id="4f3bc-567">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-567">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="4f3bc-568">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-568">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="4f3bc-569">重複する JSON キーの詳細については、「[こちらの GitHub のイシュー](https://github.com/dotnet/extensions/issues/2381)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-569">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="4f3bc-570">階層キー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-570">Hierarchical keys</span></span>
  * <span data-ttu-id="4f3bc-571">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-571">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="4f3bc-572">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-572">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="4f3bc-573">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-573">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="4f3bc-574">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-574">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="4f3bc-575">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-575">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="4f3bc-576"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-576">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4f3bc-577">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-577">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="4f3bc-578">値</span><span class="sxs-lookup"><span data-stu-id="4f3bc-578">Values</span></span>

<span data-ttu-id="4f3bc-579">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-579">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="4f3bc-580">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-580">Values are strings.</span></span>
* <span data-ttu-id="4f3bc-581">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-581">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="4f3bc-582">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-582">Providers</span></span>

<span data-ttu-id="4f3bc-583">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-583">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="4f3bc-584">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-584">Provider</span></span> | <span data-ttu-id="4f3bc-585">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-585">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="4f3bc-586">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-586">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="4f3bc-587">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4f3bc-587">Azure Key Vault</span></span> |
| <span data-ttu-id="4f3bc-588">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-588">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="4f3bc-589">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4f3bc-589">Azure App Configuration</span></span> |
| [<span data-ttu-id="4f3bc-590">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-590">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="4f3bc-591">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="4f3bc-591">Command-line parameters</span></span> |
| [<span data-ttu-id="4f3bc-592">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-592">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="4f3bc-593">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="4f3bc-593">Custom source</span></span> |
| [<span data-ttu-id="4f3bc-594">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-594">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="4f3bc-595">環境変数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-595">Environment variables</span></span> |
| [<span data-ttu-id="4f3bc-596">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-596">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="4f3bc-597">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-597">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="4f3bc-598">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-598">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="4f3bc-599">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-599">Directory files</span></span> |
| [<span data-ttu-id="4f3bc-600">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-600">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="4f3bc-601">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="4f3bc-601">In-memory collections</span></span> |
| <span data-ttu-id="4f3bc-602">[ユーザー シークレット](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-602">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="4f3bc-603">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="4f3bc-603">File in the user profile directory</span></span> |

<span data-ttu-id="4f3bc-604">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-604">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="4f3bc-605">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-605">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="4f3bc-606">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-606">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="4f3bc-607">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-607">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="4f3bc-608">ファイル ( *appsettings.json* 、*appsettings.{Environment}.json*。ここで、`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-608">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="4f3bc-609">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4f3bc-609">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="4f3bc-610">[ユーザー シークレット](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-610">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="4f3bc-611">環境変数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-611">Environment variables</span></span>
1. <span data-ttu-id="4f3bc-612">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-612">Command-line arguments</span></span>

<span data-ttu-id="4f3bc-613">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-613">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="4f3bc-614">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-614">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4f3bc-615">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-615">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="4f3bc-616">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="4f3bc-616">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="4f3bc-617">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-617">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="4f3bc-618">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="4f3bc-618">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4f3bc-619">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-619">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="4f3bc-620">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="4f3bc-620">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="4f3bc-621">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-621">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="4f3bc-622">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="4f3bc-622">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="4f3bc-623">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-623">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="4f3bc-624">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="4f3bc-624">Consume configuration during app startup</span></span>

<span data-ttu-id="4f3bc-625">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-625">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4f3bc-626">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-626">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="4f3bc-627">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-627">Command-line Configuration Provider</span></span>

<span data-ttu-id="4f3bc-628"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-628">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="4f3bc-629">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-629">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4f3bc-630">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-630">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="4f3bc-631">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-631">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4f3bc-632">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-632">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4f3bc-633">*appsettings.json* および *appsettings.{Environment}.json* ファイルからの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-633">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="4f3bc-634">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-634">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4f3bc-635">環境変数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-635">Environment variables.</span></span>

<span data-ttu-id="4f3bc-636">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-636">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="4f3bc-637">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-637">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="4f3bc-638">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-638">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="4f3bc-639">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-639">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="4f3bc-640">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-640">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4f3bc-641">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-641">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="4f3bc-642">**例**</span><span class="sxs-lookup"><span data-stu-id="4f3bc-642">**Example**</span></span>

<span data-ttu-id="4f3bc-643">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-643">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="4f3bc-644">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-644">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="4f3bc-645">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-645">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="4f3bc-646">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-646">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4f3bc-647">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-647">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="4f3bc-648">引数</span><span class="sxs-lookup"><span data-stu-id="4f3bc-648">Arguments</span></span>

<span data-ttu-id="4f3bc-649">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-649">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="4f3bc-650">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-650">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="4f3bc-651">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-651">Key prefix</span></span>               | <span data-ttu-id="4f3bc-652">例</span><span class="sxs-lookup"><span data-stu-id="4f3bc-652">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="4f3bc-653">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="4f3bc-653">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="4f3bc-654">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-654">Two dashes (`--`)</span></span>        | <span data-ttu-id="4f3bc-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-655">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="4f3bc-656">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="4f3bc-656">Forward slash (`/`)</span></span>      | <span data-ttu-id="4f3bc-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-657">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="4f3bc-658">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-658">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="4f3bc-659">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-659">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="4f3bc-660">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="4f3bc-660">Switch mappings</span></span>

<span data-ttu-id="4f3bc-661">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-661">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="4f3bc-662"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-662">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="4f3bc-663">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-663">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="4f3bc-664">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-664">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="4f3bc-665">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-665">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="4f3bc-666">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-666">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="4f3bc-667">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-667">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="4f3bc-668">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-668">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="4f3bc-669">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-669">Create a switch mappings dictionary.</span></span> <span data-ttu-id="4f3bc-670">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-670">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="4f3bc-671">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-671">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="4f3bc-672">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-672">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="4f3bc-673">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-673">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4f3bc-674">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-674">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="4f3bc-675">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-675">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="4f3bc-676">Key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-676">Key</span></span>       | <span data-ttu-id="4f3bc-677">値</span><span class="sxs-lookup"><span data-stu-id="4f3bc-677">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="4f3bc-678">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-678">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="4f3bc-679">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-679">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="4f3bc-680">Key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-680">Key</span></span>               | <span data-ttu-id="4f3bc-681">[値]</span><span class="sxs-lookup"><span data-stu-id="4f3bc-681">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="4f3bc-682">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-682">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="4f3bc-683"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-683">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="4f3bc-684">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-684">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4f3bc-685">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-685">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="4f3bc-686">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-686">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4f3bc-687">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-687">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="4f3bc-688">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-688">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4f3bc-689">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-689">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4f3bc-690">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-690">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="4f3bc-691">*appsettings.json* および *appsettings.{Environment}.json* ファイルからの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-691">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="4f3bc-692">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-692">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4f3bc-693">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-693">Command-line arguments.</span></span>

<span data-ttu-id="4f3bc-694">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-694">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="4f3bc-695">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-695">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="4f3bc-696">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-696">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="4f3bc-697">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-697">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="4f3bc-698">**例**</span><span class="sxs-lookup"><span data-stu-id="4f3bc-698">**Example**</span></span>

<span data-ttu-id="4f3bc-699">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-699">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="4f3bc-700">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-700">Run the sample app.</span></span> <span data-ttu-id="4f3bc-701">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-701">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4f3bc-702">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-702">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="4f3bc-703">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-703">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="4f3bc-704">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-704">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="4f3bc-705">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-705">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="4f3bc-706">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-706">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="4f3bc-707">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-707">Prefixes</span></span>

<span data-ttu-id="4f3bc-708">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-708">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="4f3bc-709">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-709">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="4f3bc-710">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-710">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="4f3bc-711">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-711">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="4f3bc-712">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-712">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4f3bc-713">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="4f3bc-713">**Connection string prefixes**</span></span>

<span data-ttu-id="4f3bc-714">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-714">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="4f3bc-715">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-715">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="4f3bc-716">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-716">Connection string prefix</span></span> | <span data-ttu-id="4f3bc-717">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-717">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="4f3bc-718">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-718">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="4f3bc-719">MySQL</span><span class="sxs-lookup"><span data-stu-id="4f3bc-719">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="4f3bc-720">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="4f3bc-720">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="4f3bc-721">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4f3bc-721">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="4f3bc-722">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-722">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="4f3bc-723">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-723">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="4f3bc-724">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-724">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="4f3bc-725">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-725">Environment variable key</span></span> | <span data-ttu-id="4f3bc-726">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-726">Converted configuration key</span></span> | <span data-ttu-id="4f3bc-727">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="4f3bc-727">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4f3bc-728">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-728">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4f3bc-729">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4f3bc-730">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-730">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4f3bc-731">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4f3bc-732">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-732">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4f3bc-733">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-733">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4f3bc-734">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-734">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="4f3bc-735">**例**</span><span class="sxs-lookup"><span data-stu-id="4f3bc-735">**Example**</span></span>

<span data-ttu-id="4f3bc-736">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-736">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="4f3bc-737">名前: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-737">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="4f3bc-738">値: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="4f3bc-738">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="4f3bc-739">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-739">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="4f3bc-740">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-740">File Configuration Provider</span></span>

<span data-ttu-id="4f3bc-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-741"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="4f3bc-742">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-742">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="4f3bc-743">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-743">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="4f3bc-744">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-744">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="4f3bc-745">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-745">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="4f3bc-746">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-746">INI Configuration Provider</span></span>

<span data-ttu-id="4f3bc-747"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-747">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="4f3bc-748">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-748">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4f3bc-749">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-749">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="4f3bc-750">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-750">Overloads permit specifying:</span></span>

* <span data-ttu-id="4f3bc-751">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-751">Whether the file is optional.</span></span>
* <span data-ttu-id="4f3bc-752">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-752">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4f3bc-753">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-753">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4f3bc-754">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-754">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4f3bc-755">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-755">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="4f3bc-756">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-756">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4f3bc-757">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-757">section0:key0</span></span>
* <span data-ttu-id="4f3bc-758">section0:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-758">section0:key1</span></span>
* <span data-ttu-id="4f3bc-759">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-759">section1:subsection:key</span></span>
* <span data-ttu-id="4f3bc-760">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-760">section2:subsection0:key</span></span>
* <span data-ttu-id="4f3bc-761">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-761">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="4f3bc-762">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-762">JSON Configuration Provider</span></span>

<span data-ttu-id="4f3bc-763"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-763">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="4f3bc-764">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-764">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4f3bc-765">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-765">Overloads permit specifying:</span></span>

* <span data-ttu-id="4f3bc-766">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-766">Whether the file is optional.</span></span>
* <span data-ttu-id="4f3bc-767">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-767">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4f3bc-768">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-768">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4f3bc-769">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-769">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4f3bc-770">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-770">The method is called to load configuration from:</span></span>

* <span data-ttu-id="4f3bc-771">*appsettings.json* : このファイルは最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-771">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="4f3bc-772">ファイルの環境バージョンを使用すると、 *appsettings.json* ファイルによって指定された値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-772">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="4f3bc-773">*appsettings.{Environment}.json*:ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-773">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="4f3bc-774">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-774">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4f3bc-775">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-775">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4f3bc-776">環境変数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-776">Environment variables.</span></span>
* <span data-ttu-id="4f3bc-777">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-777">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4f3bc-778">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-778">Command-line arguments.</span></span>

<span data-ttu-id="4f3bc-779">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-779">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="4f3bc-780">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-780">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="4f3bc-781">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、 *appsettings.json* および *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-781">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4f3bc-782">**例**</span><span class="sxs-lookup"><span data-stu-id="4f3bc-782">**Example**</span></span>

<span data-ttu-id="4f3bc-783">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-783">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="4f3bc-784">`AddJsonFile` の最初の呼び出しを行うと、 *appsettings.json* から構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-784">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="4f3bc-785">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-785">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="4f3bc-786">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-786">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="4f3bc-787">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-787">Run the sample app.</span></span> <span data-ttu-id="4f3bc-788">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-788">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4f3bc-789">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-789">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="4f3bc-790">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-790">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="4f3bc-791">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-791">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="4f3bc-792">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-792">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="4f3bc-793">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-793">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="4f3bc-794">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-794">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="4f3bc-795">*appsettings.Development.json* 内の設定によって、 *appsettings.json* 内の設定はオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-795">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="4f3bc-796">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-796">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="4f3bc-797">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-797">XML Configuration Provider</span></span>

<span data-ttu-id="4f3bc-798"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-798">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="4f3bc-799">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-799">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4f3bc-800">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-800">Overloads permit specifying:</span></span>

* <span data-ttu-id="4f3bc-801">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-801">Whether the file is optional.</span></span>
* <span data-ttu-id="4f3bc-802">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-802">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4f3bc-803">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-803">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4f3bc-804">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-804">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="4f3bc-805">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-805">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="4f3bc-806">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-806">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4f3bc-807">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-807">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="4f3bc-808">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4f3bc-809">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-809">section0:key0</span></span>
* <span data-ttu-id="4f3bc-810">section0:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-810">section0:key1</span></span>
* <span data-ttu-id="4f3bc-811">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-811">section1:key0</span></span>
* <span data-ttu-id="4f3bc-812">section1:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-812">section1:key1</span></span>

<span data-ttu-id="4f3bc-813">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-813">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="4f3bc-814">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4f3bc-815">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-815">section:section0:key:key0</span></span>
* <span data-ttu-id="4f3bc-816">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-816">section:section0:key:key1</span></span>
* <span data-ttu-id="4f3bc-817">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-817">section:section1:key:key0</span></span>
* <span data-ttu-id="4f3bc-818">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-818">section:section1:key:key1</span></span>

<span data-ttu-id="4f3bc-819">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-819">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="4f3bc-820">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-820">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4f3bc-821">key:attribute</span><span class="sxs-lookup"><span data-stu-id="4f3bc-821">key:attribute</span></span>
* <span data-ttu-id="4f3bc-822">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="4f3bc-822">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="4f3bc-823">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-823">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="4f3bc-824"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-824">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="4f3bc-825">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-825">The key is the file name.</span></span> <span data-ttu-id="4f3bc-826">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-826">The value contains the file's contents.</span></span> <span data-ttu-id="4f3bc-827">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-827">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="4f3bc-828">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-828">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="4f3bc-829">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-829">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="4f3bc-830">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-830">Overloads permit specifying:</span></span>

* <span data-ttu-id="4f3bc-831">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-831">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="4f3bc-832">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-832">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="4f3bc-833">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-833">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="4f3bc-834">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-834">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="4f3bc-835">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="4f3bc-836">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-836">Memory Configuration Provider</span></span>

<span data-ttu-id="4f3bc-837"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-837">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="4f3bc-838">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-838">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4f3bc-839">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-839">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="4f3bc-840">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-840">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="4f3bc-841">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-841">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="4f3bc-842">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-842">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="4f3bc-843">GetValue</span><span class="sxs-lookup"><span data-stu-id="4f3bc-843">GetValue</span></span>

<span data-ttu-id="4f3bc-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを持つ構成から単一の値を抽出し、指定したコレクション以外の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-844">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="4f3bc-845">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-845">An overload accepts a default value.</span></span>

<span data-ttu-id="4f3bc-846">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-846">The following example:</span></span>

* <span data-ttu-id="4f3bc-847">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-847">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="4f3bc-848">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-848">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="4f3bc-849">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-849">Types the value as an `int`.</span></span>
* <span data-ttu-id="4f3bc-850">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-850">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="4f3bc-851">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="4f3bc-851">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="4f3bc-852">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-852">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="4f3bc-853">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-853">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="4f3bc-854">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-854">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="4f3bc-855">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-855">section0:key0</span></span>
* <span data-ttu-id="4f3bc-856">section0:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-856">section0:key1</span></span>
* <span data-ttu-id="4f3bc-857">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-857">section1:key0</span></span>
* <span data-ttu-id="4f3bc-858">section1:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-858">section1:key1</span></span>
* <span data-ttu-id="4f3bc-859">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-859">section2:subsection0:key0</span></span>
* <span data-ttu-id="4f3bc-860">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-860">section2:subsection0:key1</span></span>
* <span data-ttu-id="4f3bc-861">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-861">section2:subsection1:key0</span></span>
* <span data-ttu-id="4f3bc-862">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-862">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="4f3bc-863">GetSection</span><span class="sxs-lookup"><span data-stu-id="4f3bc-863">GetSection</span></span>

<span data-ttu-id="4f3bc-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-864">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="4f3bc-865">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-865">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="4f3bc-866">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-866">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="4f3bc-867">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-867">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="4f3bc-868">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-868">`GetSection` never returns `null`.</span></span> <span data-ttu-id="4f3bc-869">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-869">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="4f3bc-870">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-870">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="4f3bc-871"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-871">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="4f3bc-872">GetChildren</span><span class="sxs-lookup"><span data-stu-id="4f3bc-872">GetChildren</span></span>

<span data-ttu-id="4f3bc-873">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-873">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="4f3bc-874">既存</span><span class="sxs-lookup"><span data-stu-id="4f3bc-874">Exists</span></span>

<span data-ttu-id="4f3bc-875">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-875">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="4f3bc-876">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-876">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="4f3bc-877">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="4f3bc-877">Bind to an object graph</span></span>

<span data-ttu-id="4f3bc-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-878"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="4f3bc-879">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-879">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="4f3bc-880">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-880">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="4f3bc-881">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-881">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="4f3bc-882">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-882">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="4f3bc-883">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-883">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="4f3bc-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-884">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="4f3bc-885">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-885">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="4f3bc-886">次のコードは、上記の例で `Get<T>` を使用する方法を示します：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-886">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="4f3bc-887">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="4f3bc-887">Bind an array to a class</span></span>

<span data-ttu-id="4f3bc-888">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="4f3bc-888">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="4f3bc-889"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-889">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4f3bc-890">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-890">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="4f3bc-891">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-891">Binding is provided by convention.</span></span> <span data-ttu-id="4f3bc-892">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-892">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="4f3bc-893">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="4f3bc-893">**In-memory array processing**</span></span>

<span data-ttu-id="4f3bc-894">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-894">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="4f3bc-895">Key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-895">Key</span></span>             | <span data-ttu-id="4f3bc-896">[値]</span><span class="sxs-lookup"><span data-stu-id="4f3bc-896">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="4f3bc-897">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-897">array:entries:0</span></span> | <span data-ttu-id="4f3bc-898">value0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-898">value0</span></span> |
| <span data-ttu-id="4f3bc-899">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-899">array:entries:1</span></span> | <span data-ttu-id="4f3bc-900">value1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-900">value1</span></span> |
| <span data-ttu-id="4f3bc-901">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="4f3bc-901">array:entries:2</span></span> | <span data-ttu-id="4f3bc-902">value2</span><span class="sxs-lookup"><span data-stu-id="4f3bc-902">value2</span></span> |
| <span data-ttu-id="4f3bc-903">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="4f3bc-903">array:entries:4</span></span> | <span data-ttu-id="4f3bc-904">value4</span><span class="sxs-lookup"><span data-stu-id="4f3bc-904">value4</span></span> |
| <span data-ttu-id="4f3bc-905">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="4f3bc-905">array:entries:5</span></span> | <span data-ttu-id="4f3bc-906">value5</span><span class="sxs-lookup"><span data-stu-id="4f3bc-906">value5</span></span> |

<span data-ttu-id="4f3bc-907">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-907">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="4f3bc-908">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-908">The array skips a value for index &num;3.</span></span> <span data-ttu-id="4f3bc-909">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-909">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="4f3bc-910">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-910">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="4f3bc-911">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-911">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="4f3bc-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用することもできます。これにより、コードがよりコンパクトになります：</span><span class="sxs-lookup"><span data-stu-id="4f3bc-912">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="4f3bc-913">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-913">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="4f3bc-914">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-914">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="4f3bc-915">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="4f3bc-915">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="4f3bc-916">0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-916">0</span></span>                            | <span data-ttu-id="4f3bc-917">value0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-917">value0</span></span>                       |
| <span data-ttu-id="4f3bc-918">1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-918">1</span></span>                            | <span data-ttu-id="4f3bc-919">value1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-919">value1</span></span>                       |
| <span data-ttu-id="4f3bc-920">2</span><span class="sxs-lookup"><span data-stu-id="4f3bc-920">2</span></span>                            | <span data-ttu-id="4f3bc-921">value2</span><span class="sxs-lookup"><span data-stu-id="4f3bc-921">value2</span></span>                       |
| <span data-ttu-id="4f3bc-922">3</span><span class="sxs-lookup"><span data-stu-id="4f3bc-922">3</span></span>                            | <span data-ttu-id="4f3bc-923">value4</span><span class="sxs-lookup"><span data-stu-id="4f3bc-923">value4</span></span>                       |
| <span data-ttu-id="4f3bc-924">4</span><span class="sxs-lookup"><span data-stu-id="4f3bc-924">4</span></span>                            | <span data-ttu-id="4f3bc-925">value5</span><span class="sxs-lookup"><span data-stu-id="4f3bc-925">value5</span></span>                       |

<span data-ttu-id="4f3bc-926">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-926">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="4f3bc-927">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-927">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="4f3bc-928">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-928">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="4f3bc-929">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-929">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="4f3bc-930">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-930">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="4f3bc-931">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-931">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="4f3bc-932">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-932">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="4f3bc-933">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-933">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="4f3bc-934">Key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-934">Key</span></span>             | <span data-ttu-id="4f3bc-935">値</span><span class="sxs-lookup"><span data-stu-id="4f3bc-935">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="4f3bc-936">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="4f3bc-936">array:entries:3</span></span> | <span data-ttu-id="4f3bc-937">value3</span><span class="sxs-lookup"><span data-stu-id="4f3bc-937">value3</span></span> |

<span data-ttu-id="4f3bc-938">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-938">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="4f3bc-939">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-939">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="4f3bc-940">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="4f3bc-940">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="4f3bc-941">0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-941">0</span></span>                            | <span data-ttu-id="4f3bc-942">value0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-942">value0</span></span>                       |
| <span data-ttu-id="4f3bc-943">1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-943">1</span></span>                            | <span data-ttu-id="4f3bc-944">value1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-944">value1</span></span>                       |
| <span data-ttu-id="4f3bc-945">2</span><span class="sxs-lookup"><span data-stu-id="4f3bc-945">2</span></span>                            | <span data-ttu-id="4f3bc-946">value2</span><span class="sxs-lookup"><span data-stu-id="4f3bc-946">value2</span></span>                       |
| <span data-ttu-id="4f3bc-947">3</span><span class="sxs-lookup"><span data-stu-id="4f3bc-947">3</span></span>                            | <span data-ttu-id="4f3bc-948">value3</span><span class="sxs-lookup"><span data-stu-id="4f3bc-948">value3</span></span>                       |
| <span data-ttu-id="4f3bc-949">4</span><span class="sxs-lookup"><span data-stu-id="4f3bc-949">4</span></span>                            | <span data-ttu-id="4f3bc-950">value4</span><span class="sxs-lookup"><span data-stu-id="4f3bc-950">value4</span></span>                       |
| <span data-ttu-id="4f3bc-951">5</span><span class="sxs-lookup"><span data-stu-id="4f3bc-951">5</span></span>                            | <span data-ttu-id="4f3bc-952">value5</span><span class="sxs-lookup"><span data-stu-id="4f3bc-952">value5</span></span>                       |

<span data-ttu-id="4f3bc-953">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="4f3bc-953">**JSON array processing**</span></span>

<span data-ttu-id="4f3bc-954">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-954">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="4f3bc-955">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-955">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="4f3bc-956">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-956">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="4f3bc-957">Key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-957">Key</span></span>                     | <span data-ttu-id="4f3bc-958">値</span><span class="sxs-lookup"><span data-stu-id="4f3bc-958">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="4f3bc-959">json_array:key</span><span class="sxs-lookup"><span data-stu-id="4f3bc-959">json_array:key</span></span>          | <span data-ttu-id="4f3bc-960">valueA</span><span class="sxs-lookup"><span data-stu-id="4f3bc-960">valueA</span></span> |
| <span data-ttu-id="4f3bc-961">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-961">json_array:subsection:0</span></span> | <span data-ttu-id="4f3bc-962">valueB</span><span class="sxs-lookup"><span data-stu-id="4f3bc-962">valueB</span></span> |
| <span data-ttu-id="4f3bc-963">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-963">json_array:subsection:1</span></span> | <span data-ttu-id="4f3bc-964">valueC</span><span class="sxs-lookup"><span data-stu-id="4f3bc-964">valueC</span></span> |
| <span data-ttu-id="4f3bc-965">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="4f3bc-965">json_array:subsection:2</span></span> | <span data-ttu-id="4f3bc-966">valueD</span><span class="sxs-lookup"><span data-stu-id="4f3bc-966">valueD</span></span> |

<span data-ttu-id="4f3bc-967">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-967">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="4f3bc-968">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-968">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="4f3bc-969">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-969">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="4f3bc-970">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="4f3bc-970">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="4f3bc-971">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="4f3bc-971">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="4f3bc-972">0</span><span class="sxs-lookup"><span data-stu-id="4f3bc-972">0</span></span>                                   | <span data-ttu-id="4f3bc-973">valueB</span><span class="sxs-lookup"><span data-stu-id="4f3bc-973">valueB</span></span>                              |
| <span data-ttu-id="4f3bc-974">1</span><span class="sxs-lookup"><span data-stu-id="4f3bc-974">1</span></span>                                   | <span data-ttu-id="4f3bc-975">valueC</span><span class="sxs-lookup"><span data-stu-id="4f3bc-975">valueC</span></span>                              |
| <span data-ttu-id="4f3bc-976">2</span><span class="sxs-lookup"><span data-stu-id="4f3bc-976">2</span></span>                                   | <span data-ttu-id="4f3bc-977">valueD</span><span class="sxs-lookup"><span data-stu-id="4f3bc-977">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="4f3bc-978">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4f3bc-978">Custom configuration provider</span></span>

<span data-ttu-id="4f3bc-979">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-979">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="4f3bc-980">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-980">The provider has the following characteristics:</span></span>

* <span data-ttu-id="4f3bc-981">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-981">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="4f3bc-982">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-982">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="4f3bc-983">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-983">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="4f3bc-984">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-984">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="4f3bc-985">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-985">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="4f3bc-986">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-986">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="4f3bc-987">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-987">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="4f3bc-988">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-988">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="4f3bc-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-989">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="4f3bc-990"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-990">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="4f3bc-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-991">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="4f3bc-992"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-992">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="4f3bc-993">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-993">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="4f3bc-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-994">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="4f3bc-995">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-995">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="4f3bc-996">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-996">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="4f3bc-997">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-997">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="4f3bc-998">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="4f3bc-998">Access configuration during startup</span></span>

<span data-ttu-id="4f3bc-999">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-999">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4f3bc-1000">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1000">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="4f3bc-1001">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1001">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="4f3bc-1002">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1002">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="4f3bc-1003">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) 名前空間に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1003">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="4f3bc-1004">Razor Pages ページで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1004">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="4f3bc-1005">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1005">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="4f3bc-1006">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1006">Add configuration from an external assembly</span></span>

<span data-ttu-id="4f3bc-1007"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1007">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4f3bc-1008">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1008">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f3bc-1009">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4f3bc-1009">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

---
title: ASP.NET Core の構成
author: rick-anderson
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
ms.openlocfilehash: 0f069b049889f7caade493e238ac7a23db5e79af
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536305"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="ff10d-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="ff10d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="ff10d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff10d-105">ASP.NET Core の構成は、1つまたは複数の[構成プロバイダー](#cp)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="ff10d-106">構成プロバイダーは、以下のようなさまざまな構成ソースを使用して、キーと値のペアから構成データを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="ff10d-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="ff10d-107">*appsettings.json* などの設定ファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="ff10d-108">環境変数</span><span class="sxs-lookup"><span data-stu-id="ff10d-108">Environment variables</span></span>
* <span data-ttu-id="ff10d-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff10d-109">Azure Key Vault</span></span>
* <span data-ttu-id="ff10d-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="ff10d-110">Azure App Configuration</span></span>
* <span data-ttu-id="ff10d-111">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="ff10d-111">Command-line arguments</span></span>
* <span data-ttu-id="ff10d-112">インストール済みまたは作成済みのカスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="ff10d-113">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-113">Directory files</span></span>
* <span data-ttu-id="ff10d-114">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="ff10d-114">In-memory .NET objects</span></span>

<span data-ttu-id="ff10d-115">このトピックでは ASP.NET Core の構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="ff10d-116">コンソール アプリでの構成の使用方法について詳しくは、[.NET 構成](/dotnet/core/extensions/configuration)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="ff10d-117">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ff10d-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="ff10d-118">既定の構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-118">Default configuration</span></span>

<span data-ttu-id="ff10d-119">[dotnet new](/dotnet/core/tools/dotnet-new) または Visual Studio で作成された ASP.NET Core の web アプリが、次のコードを生成します:</span><span class="sxs-lookup"><span data-stu-id="ff10d-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="ff10d-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="ff10d-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) は:既存の `IConfiguration` をソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="ff10d-122">既定の構成では、[ホスト](#hvac)構成を追加し、_アプリ_ 構成の最初のソースとして設定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="ff10d-123">[JSON 構成プロバイダー](#file-configuration-provider)を使用する [appsettings.json](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="ff10d-124">[JSON 構成プロバイダー](#file-configuration-provider)を使用する *appsettings.* `Environment`*json*。</span><span class="sxs-lookup"><span data-stu-id="ff10d-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="ff10d-125">たとえば、*appsettings*.***Production\*\*_._json* および *appsettings*.\*\*\*Development** _._json\*。</span><span class="sxs-lookup"><span data-stu-id="ff10d-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="ff10d-126">`Development` 環境でアプリが実行される際の [App シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="ff10d-127">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="ff10d-128">[コマンドライン構成プロバイダー](#command-line)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="ff10d-129">後から追加される構成プロバイダーは、それ以前のキー設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="ff10d-130">たとえば、`MyKey` が *appsettings.json* と環境の両方で設定されている場合、環境の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="ff10d-131">既定の構成プロバイダーを使用すると、[コマンドライン構成プロバイダー](#clcp) が他のすべてのプロバイダーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="ff10d-132">`CreateDefaultBuilder` の詳細については、[既定のビルダー設定](xref:fundamentals/host/generic-host#default-builder-settings)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="ff10d-133">以下のコードでは、追加した順に、有効な構成プロバイダーが表示されます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="ff10d-134">次の *appsettings.json* ファイルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="ff10d-135">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-136">既定の <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> は、以下の順序で構成を読み込みます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="ff10d-137">*appsettings.* `Environment` *.json*:たとえば、*appsettings*.***Production\*\*_._json* および *appsettings*.\*\*\*Development** _._json\* ファイル。</span><span class="sxs-lookup"><span data-stu-id="ff10d-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="ff10d-138">ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="ff10d-139">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ff10d-140">*appsettings*.`Environment`.*json* の値によって、 *appsettings.json* 内のキーがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="ff10d-141">たとえば、既定では次のようになります:</span><span class="sxs-lookup"><span data-stu-id="ff10d-141">For example, by default:</span></span>

* <span data-ttu-id="ff10d-142">開発中は、*appsettings*.***Development** _._json* 構成によって、 *appsettings.json* で見つかった値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="ff10d-143">運用環境では、*appsettings*.***Production** _._json* 構成によって、 *appsettings.json* で見つかった値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="ff10d-144">たとえば、Azure にアプリをデプロイする場合。</span><span class="sxs-lookup"><span data-stu-id="ff10d-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="ff10d-145">オプションパターンを使用して、階層型の構成データをバインドします</span><span class="sxs-lookup"><span data-stu-id="ff10d-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="ff10d-146">[既定](#default)の構成を利用する場合、[reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) で *appsettings.json* と *appsettings.* `Environment` *.json* ファイルを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="ff10d-147">アプリの開始 ***後*** に *appsettings.json* と *appsettings.* `Environment` *.json* ファイルに加えられた変更は、[JSON 構成プロバイダー](#jcp)によって読み取られます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="ff10d-148">追加の JSON 構成ファイルを追加する方法の詳細については、このドキュメント中の「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="ff10d-149">サービス コレクションの結合</span><span class="sxs-lookup"><span data-stu-id="ff10d-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="ff10d-150">セキュリティとユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="ff10d-150">Security and user secrets</span></span>

<span data-ttu-id="ff10d-151">構成データのガイドライン:</span><span class="sxs-lookup"><span data-stu-id="ff10d-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="ff10d-152">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="ff10d-153">[Secret Manager](xref:security/app-secrets) ツールを使用すると、開発時にシークレットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="ff10d-154">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="ff10d-155">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="ff10d-156">[既定](#default)では、ユーザー シークレットの構成ソースは、JSON 構成ソースの後に登録されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="ff10d-157">このため、ユーザー シークレット キーは、 *appsettings.json* および *appsettings.* `Environment` *.json* よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="ff10d-158">パスワードその他の機密データの格納については、次を参照してください：</span><span class="sxs-lookup"><span data-stu-id="ff10d-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="ff10d-159"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="ff10d-160">Secret Manager ツールでは、[ファイル構成プロバイダー](#fcp)を使用して、ユーザーのシークレットがローカル システム上の JSON ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="ff10d-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="ff10d-162">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="ff10d-163">環境変数</span><span class="sxs-lookup"><span data-stu-id="ff10d-163">Environment variables</span></span>

<span data-ttu-id="ff10d-164">[既定](#default)の構成を使用すると、<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> によって、 *appsettings.json* 、*appsettings.* `Environment` *.json*、および [ユーザー シークレット](xref:security/app-secrets)の読み取り後に、環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="ff10d-165">そのため、環境から読み取られたキー値によって、 *appsettings.json* 、*appsettings.* `Environment` *.json*、およびユーザー シークレットから読み取られた値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="ff10d-166">次の `set` コマンドは：</span><span class="sxs-lookup"><span data-stu-id="ff10d-166">The following `set` commands:</span></span>

* <span data-ttu-id="ff10d-167">Windows で[ 上記の例 ](#appsettingsjson)の環境キーと値を設定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="ff10d-168">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)を使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="ff10d-169">`dotnet run` コマンドは、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="ff10d-170">上記の環境設定は：</span><span class="sxs-lookup"><span data-stu-id="ff10d-170">The preceding environment settings:</span></span>

* <span data-ttu-id="ff10d-171">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="ff10d-172">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="ff10d-173">次の [setx](/windows-server/administration/windows-commands/setx) コマンドで、Windows 上の環境キーと値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="ff10d-174">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="ff10d-175">`/M` は、システム環境で変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="ff10d-176">`/M` スイッチが使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="ff10d-177">上記のコマンドによって、 *appsettings.json* および *appsettings.* `Environment` *.json* がオーバーライドされるのをテストするには:</span><span class="sxs-lookup"><span data-stu-id="ff10d-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="ff10d-178">Visual Studio の場合:Visual Studio を終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="ff10d-179">CLI の場合:新しいコマンド ウィンドウを起動し、`dotnet run` を入力します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="ff10d-180">環境変数のプレフィックスを指定する文字列を指定して <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="ff10d-181">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-181">In the preceding code:</span></span>

* <span data-ttu-id="ff10d-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` は[既定の構成プロバイダー](#default)の後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="ff10d-183">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="ff10d-184">`MyCustomPrefix_` プレフィックスを使用して設定された環境変数は、[既定の構成プロバイダー](#default)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="ff10d-185">これには、プレフィックスのない環境変数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="ff10d-186">構成のキーと値のペアの読み取り時に、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="ff10d-187">次のコマンドは、カスタム プレフィックスをテストします：</span><span class="sxs-lookup"><span data-stu-id="ff10d-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="ff10d-188">[既定の構成](#default)では、`DOTNET_` と `ASPNETCORE_` のプレフィックスが付いた環境変数とコマンド ライン引数を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="ff10d-189">`DOTNET_` と `ASPNETCORE_` のプレフィックスは ASP.NET Core によって[ホストとアプリの構成](xref:fundamentals/host/generic-host#host-configuration)に使用されますが、ユーザーの構成には使用されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="ff10d-190">ホストとアプリの構成の詳細については、「[.NET 汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="ff10d-191">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの **新しいアプリケーション設定** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="ff10d-192">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="ff10d-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="ff10d-193">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="ff10d-194">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-194">Exposed as environment variables.</span></span>

<span data-ttu-id="ff10d-195">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="ff10d-196">Azure データベース接続文字列の詳細については、「[接続文字列のプレフィックス](#constr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="ff10d-197">環境変数の名前付け</span><span class="sxs-lookup"><span data-stu-id="ff10d-197">Naming of environment variables</span></span>

<span data-ttu-id="ff10d-198">環境変数名には、 *appsettings.json* ファイルの構造が反映されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="ff10d-199">階層内の各要素は、二重アンダースコア (推奨) またはコロンによって区切られます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="ff10d-200">要素構造に配列が含まれている場合は、配列のインデックスをこのパスの追加の要素名として扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="ff10d-201">次の *appsettings.json* ファイルと、環境変数として表されたその同等の値を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ff10d-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="ff10d-202">**環境変数**</span><span class="sxs-lookup"><span data-stu-id="ff10d-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="ff10d-203">生成された launchSettings.json に設定されている環境変数</span><span class="sxs-lookup"><span data-stu-id="ff10d-203">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="ff10d-204">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="ff10d-205">たとえば、ASP.NET Core Web テンプレートでは、エンドポイント構成を次のように設定する *launchSettings.json* ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-205">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="ff10d-206">`applicationUrl` を構成すると `ASPNETCORE_URLS` 環境変数が設定され、環境で設定されている値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-206">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="ff10d-207">Linux で環境変数をエスケープする</span><span class="sxs-lookup"><span data-stu-id="ff10d-207">Escape environment variables on Linux</span></span>

<span data-ttu-id="ff10d-208">Linux では、`systemd` で解析できるように URL 環境変数の値をエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-208">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="ff10d-209">Linux ツール `systemd-escape` を使用すると、`http:--localhost:5001` が生成されます</span><span class="sxs-lookup"><span data-stu-id="ff10d-209">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="ff10d-210">環境変数を表示する</span><span class="sxs-lookup"><span data-stu-id="ff10d-210">Display environment variables</span></span>

<span data-ttu-id="ff10d-211">次のコードでは、アプリケーションの起動時に環境変数と値が表示されます。これは環境設定をデバッグするときに役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-211">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="ff10d-212">コマンド ライン</span><span class="sxs-lookup"><span data-stu-id="ff10d-212">Command-line</span></span>

<span data-ttu-id="ff10d-213">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> は、以下の構成ソースの後にコマンド ライン引数のキーと値のペアから構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-213">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="ff10d-214">*appsettings.json* および *appsettings*.`Environment`.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="ff10d-214">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="ff10d-215">開発環境での[アプリ シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-215">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ff10d-216">環境変数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-216">Environment variables.</span></span>

<span data-ttu-id="ff10d-217">[既定](#default)では、コマンド ラインで設定された構成値は、他のすべての構成プロバイダーで設定された構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-217">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="ff10d-218">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="ff10d-218">Command-line arguments</span></span>

<span data-ttu-id="ff10d-219">次のコマンドは `=` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-219">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="ff10d-220">次のコマンドは `/` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-220">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="ff10d-221">次のコマンドは `--` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-221">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="ff10d-222">キーの値:</span><span class="sxs-lookup"><span data-stu-id="ff10d-222">The key value:</span></span>

* <span data-ttu-id="ff10d-223">`=` の後に続ける必要があります。または、値がスペースの後にある場合は、キーのプレフィックスが `--` または `/` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-223">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="ff10d-224">`=` を使用する場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-224">Isn't required if `=` is used.</span></span> <span data-ttu-id="ff10d-225">たとえば、`MySetting=` のようにします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-225">For example, `MySetting=`.</span></span>

<span data-ttu-id="ff10d-226">同じコマンド内では、`=` を使用するコマンド ライン引数のキーと値のペアを、スペースを使用するキーと値のペアと混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-226">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="ff10d-227">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="ff10d-227">Switch mappings</span></span>

<span data-ttu-id="ff10d-228">スイッチ マッピングでは、**キー** 名の置換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-228">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="ff10d-229"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換するディクショナリを提供します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-229">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="ff10d-230">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-230">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="ff10d-231">ディクショナリ中にコマンド ライン キーが見つかった場合は、そのディクショナリの値が返され、キーと値のペアがアプリの構成に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-231">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="ff10d-232">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-232">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="ff10d-233">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="ff10d-233">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="ff10d-234">スイッチは `-` または `--`で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-234">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="ff10d-235">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-235">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="ff10d-236">スイッチ マッピング ディクショナリを使用するには、それを `AddCommandLine` の呼び出しに渡します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-236">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="ff10d-237">次のコードは、置換されたキーのキー値を示しています：</span><span class="sxs-lookup"><span data-stu-id="ff10d-237">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-238">次のコマンドを実行して、キーの置換をテストできます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="ff10d-239">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="ff10d-240">`CreateDefaultBuilder` メソッドの `AddCommandLine` 呼び出しには、マップされたスイッチが含まれていないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff10d-241">解決策は、`CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="ff10d-242">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="ff10d-242">Hierarchical configuration data</span></span>

<span data-ttu-id="ff10d-243">構成 API では、構成キーの区切り記号を使用して階層データをフラット化することにより、階層型の構成データの読み取りが行われます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="ff10d-244">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *appsettings.json* ファイルが含まれます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="ff10d-245">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-246">階層型の構成データを読み取る方法としては、オプション パターンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="ff10d-247">詳細については、このドキュメント中の「[階層型の構成データをバインドする](#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="ff10d-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="ff10d-249">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="ff10d-250">構成キーと値</span><span class="sxs-lookup"><span data-stu-id="ff10d-250">Configuration keys and values</span></span>

<span data-ttu-id="ff10d-251">構成キー:</span><span class="sxs-lookup"><span data-stu-id="ff10d-251">Configuration keys:</span></span>

* <span data-ttu-id="ff10d-252">構成キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-252">Are case-insensitive.</span></span> <span data-ttu-id="ff10d-253">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="ff10d-254">キーと値が複数の構成プロバイダーで設定されている場合は、最後に追加されたプロバイダーの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="ff10d-255">詳細については、「[既定の構成](#default)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="ff10d-256">階層キー</span><span class="sxs-lookup"><span data-stu-id="ff10d-256">Hierarchical keys</span></span>
  * <span data-ttu-id="ff10d-257">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="ff10d-258">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="ff10d-259">ダブル アンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロン `:` に自動で変換されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="ff10d-260">Azure Key Vault では、階層型のキーは区切り記号に `--` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="ff10d-261">シークレットがアプリの構成に読み込まれると、[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)によって `--` が `:` に自動的に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="ff10d-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ff10d-263">配列のバインドについては、「[配列をクラスにバインドする](#boa)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="ff10d-264">構成値:</span><span class="sxs-lookup"><span data-stu-id="ff10d-264">Configuration values:</span></span>

* <span data-ttu-id="ff10d-265">構成値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-265">Are strings.</span></span>
* <span data-ttu-id="ff10d-266">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="ff10d-267">構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-267">Configuration providers</span></span>

<span data-ttu-id="ff10d-268">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="ff10d-269">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-269">Provider</span></span> | <span data-ttu-id="ff10d-270">以下から構成を提供します</span><span class="sxs-lookup"><span data-stu-id="ff10d-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="ff10d-271">Azure Key Vault 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="ff10d-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff10d-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="ff10d-273">Azure App Configuration プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="ff10d-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="ff10d-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="ff10d-275">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="ff10d-276">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="ff10d-276">Command-line parameters</span></span> |
| [<span data-ttu-id="ff10d-277">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="ff10d-278">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="ff10d-278">Custom source</span></span> |
| [<span data-ttu-id="ff10d-279">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="ff10d-280">環境変数</span><span class="sxs-lookup"><span data-stu-id="ff10d-280">Environment variables</span></span> |
| [<span data-ttu-id="ff10d-281">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="ff10d-282">INI、JSON、および XML ファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="ff10d-283">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="ff10d-284">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-284">Directory files</span></span> |
| [<span data-ttu-id="ff10d-285">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="ff10d-286">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="ff10d-286">In-memory collections</span></span> |
| [<span data-ttu-id="ff10d-287">ユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="ff10d-287">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="ff10d-288">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-288">File in the user profile directory</span></span> |

<span data-ttu-id="ff10d-289">構成ソースは、構成プロバイダーで指定された順序で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="ff10d-290">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="ff10d-291">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-291">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="ff10d-292">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="ff10d-292">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="ff10d-293">ユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="ff10d-293">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="ff10d-294">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-294">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="ff10d-295">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-295">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="ff10d-296">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするには、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-296">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="ff10d-297">上記の一連のプロバイダーは、[既定の構成](#default)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-297">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="ff10d-298">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="ff10d-298">Connection string prefixes</span></span>

<span data-ttu-id="ff10d-299">構成 API には、4つの接続文字列環境変数に対する特別なプロセスルールがあります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-299">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="ff10d-300">これらの接続文字列は、アプリ環境用の Azure 接続文字列の構成に含まれています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-300">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="ff10d-301">表に示されたプレフィックスを持つ環境変数は、[既定の構成](#default) を使用するとき、または `AddEnvironmentVariables` にプレフィックスが指定されていない場合に、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-301">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="ff10d-302">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="ff10d-302">Connection string prefix</span></span> | <span data-ttu-id="ff10d-303">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-303">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="ff10d-304">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-304">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="ff10d-305">MySQL</span><span class="sxs-lookup"><span data-stu-id="ff10d-305">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="ff10d-306">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="ff10d-306">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="ff10d-307">SQL Server</span><span class="sxs-lookup"><span data-stu-id="ff10d-307">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="ff10d-308">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="ff10d-308">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="ff10d-309">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-309">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="ff10d-310">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-310">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="ff10d-311">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="ff10d-311">Environment variable key</span></span> | <span data-ttu-id="ff10d-312">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="ff10d-312">Converted configuration key</span></span> | <span data-ttu-id="ff10d-313">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="ff10d-313">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ff10d-314">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-314">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ff10d-315">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff10d-315">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ff10d-316">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff10d-316">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ff10d-317">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff10d-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ff10d-318">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff10d-318">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ff10d-319">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff10d-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ff10d-320">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff10d-320">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="ff10d-321">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-321">File configuration provider</span></span>

<span data-ttu-id="ff10d-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="ff10d-323">以下の構成プロバイダーは `FileConfigurationProvider` から派生したものです：</span><span class="sxs-lookup"><span data-stu-id="ff10d-323">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="ff10d-324">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-324">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="ff10d-325">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-325">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="ff10d-326">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-326">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="ff10d-327">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-327">INI configuration provider</span></span>

<span data-ttu-id="ff10d-328"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-328">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="ff10d-329">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-329">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="ff10d-330">上記のコードでは、*MyIniConfig.ini* と *MyIniConfig*.`Environment`.*ini* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-330">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="ff10d-331">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-331">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="ff10d-332">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-332">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ff10d-333">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyIniConfig* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-333">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="ff10d-334">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-334">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="ff10d-335">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-335">JSON configuration provider</span></span>

<span data-ttu-id="ff10d-336"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、JSON ファイルのキーと値のペアから構成が読み込みまれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-336">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="ff10d-337">オーバーロードでは、次の指定ができます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-337">Overloads can specify:</span></span>

* <span data-ttu-id="ff10d-338">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="ff10d-338">Whether the file is optional.</span></span>
* <span data-ttu-id="ff10d-339">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="ff10d-339">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="ff10d-340">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-340">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="ff10d-341">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-341">The preceding code:</span></span>

* <span data-ttu-id="ff10d-342">次のオプションを使用して、*MyConfig.json* ファイルを読み込むように JSON 構成プロバイダーを構成します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-342">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="ff10d-343">`optional: true`:ファイルは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-343">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="ff10d-344">`reloadOnChange: true` は、次のとおりです。変更が保存されると、ファイルが再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-344">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="ff10d-345">*MyConfig.json* ファイルの前に [既定の構成プロバイダー](#default)を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-345">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="ff10d-346">[環境変数構成プロバイダー](#evcp) および [コマンド ライン構成プロバイダー](#clcp)を含む、既定の構成プロバイダーでの *MyConfig.json* ファイルのオーバーライドの設定。</span><span class="sxs-lookup"><span data-stu-id="ff10d-346">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ff10d-347">通常は、[環境変数構成プロバイダー](#evcp)および [コマンドライン構成プロバイダー](#clcp)で設定されている値をオーバーライドするカスタム JSON ファイルは ***必要ありません***。</span><span class="sxs-lookup"><span data-stu-id="ff10d-347">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ff10d-348">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="ff10d-349">上記のコードでは、*MyConfig.json* と *MyConfig*.`Environment`.*json* ファイルの設定は：</span><span class="sxs-lookup"><span data-stu-id="ff10d-349">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="ff10d-350">*appsettings.json* および *appsettings*.`Environment`.*json* ファイル内の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-350">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="ff10d-351">[環境変数の構成プロバイダー](#evcp)と[コマンドライン構成プロバイダー](#clcp)の設定によってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-351">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ff10d-352">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *MyConfig.json* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="ff10d-353">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="ff10d-354">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-354">XML configuration provider</span></span>

<span data-ttu-id="ff10d-355"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-355">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="ff10d-356">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-356">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="ff10d-357">上記のコードでは、*MyXMLFile.xml* と *MyXMLFile*.`Environment`.*xml* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-357">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="ff10d-358">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-358">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="ff10d-359">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-359">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="ff10d-360">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyXMLFile.xml* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-360">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="ff10d-361">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-361">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-362">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-362">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="ff10d-363">以下のコードでは、前の構成ファイルを読み取って、キーと値を表示します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-363">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-364">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-364">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="ff10d-365">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-365">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff10d-366">key:attribute</span><span class="sxs-lookup"><span data-stu-id="ff10d-366">key:attribute</span></span>
* <span data-ttu-id="ff10d-367">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="ff10d-367">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="ff10d-368">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-368">Key-per-file configuration provider</span></span>

<span data-ttu-id="ff10d-369"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-369">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="ff10d-370">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-370">The key is the file name.</span></span> <span data-ttu-id="ff10d-371">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-371">The value contains the file's contents.</span></span> <span data-ttu-id="ff10d-372">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-372">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="ff10d-373">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-373">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="ff10d-374">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-374">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="ff10d-375">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff10d-376">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="ff10d-376">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="ff10d-377">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="ff10d-377">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="ff10d-378">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-378">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="ff10d-379">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-379">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="ff10d-380">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-380">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="ff10d-381">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-381">Memory configuration provider</span></span>

<span data-ttu-id="ff10d-382"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-382">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="ff10d-383">次のコードでは、構成システムにメモリコ レクションが追加されています：</span><span class="sxs-lookup"><span data-stu-id="ff10d-383">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="ff10d-384">[サンプルのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の以下のコードでは、上記の構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-384">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-385">上記のコードでは、[既定の構成プロバイダー](#default)の後に `config.AddInMemoryCollection(Dict)` が追加されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-385">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="ff10d-386">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-386">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="ff10d-387">`MemoryConfigurationProvider` を使用した別の例については、[配列をバインド](#boa)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-387">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="ff10d-388">Kestrel のエンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-388">Kestrel endpoint configuration</span></span>

<span data-ttu-id="ff10d-389">Kestrel 固有のエンドポイント構成によって、すべての[サーバー間](xref:fundamentals/servers/index)のエンドポイント構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-389">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="ff10d-390">サーバー間のエンドポイント構成には、次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-390">Cross-server endpoint configurations include:</span></span>

  * [<span data-ttu-id="ff10d-391">UseUrls</span><span class="sxs-lookup"><span data-stu-id="ff10d-391">UseUrls</span></span>](xref:fundamentals/host/web-host#server-urls)
  * <span data-ttu-id="ff10d-392">[コマンド ライン](xref:fundamentals/configuration/index#command-line)での `--urls`</span><span class="sxs-lookup"><span data-stu-id="ff10d-392">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="ff10d-393">[環境変数](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ff10d-393">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="ff10d-394">ASP.NET Core Web アプリで使用される次の *appsettings.json* ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ff10d-394">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="ff10d-395">上の強調表示されているマークアップが ASP.NET Core Web アプリで使用され、"***かつ***" 次のサーバー間エンドポイント構成を使用してコマンド ラインでアプリが起動される場合:</span><span class="sxs-lookup"><span data-stu-id="ff10d-395">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="ff10d-396">Kestrel は、`https://localhost:7777` ではなく、 *appsettings.json* ファイルで Kestrel 専用に構成されたエンドポイント (`https://localhost:9999`) にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-396">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="ff10d-397">環境変数として構成された Kestrel 固有のエンドポイントを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-397">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="ff10d-398">上記の環境変数では、`Https` が Kestrel 固有のエンドポイントの名前です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-398">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="ff10d-399">前の *appsettings.json* ファイルでも、`Https` という名前の Kestrel 固有のエンドポイントが定義されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-399">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="ff10d-400">[既定](#default-configuration)で、[環境変数構成プロバイダー](#evcp)を使用している環境変数は *appsettings.* `Environment` *.json* の後に読み取られます。したがって、上記の環境変数は `Https` エンドポイント用に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-400">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="ff10d-401">GetValue</span><span class="sxs-lookup"><span data-stu-id="ff10d-401">GetValue</span></span>

<span data-ttu-id="ff10d-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを使用して、構成から単一の値を抽出し、それを指定した型に変換します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-403">上記のコードでは、`NumberKey` が構成中に見つからない場合には `99` の既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-403">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="ff10d-404">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="ff10d-404">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="ff10d-405">以下の例では、次の *MySubsection.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-405">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="ff10d-406">以下のコードでは、*MySubsection セクション* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-406">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="ff10d-407">GetSection</span><span class="sxs-lookup"><span data-stu-id="ff10d-407">GetSection</span></span>

<span data-ttu-id="ff10d-408">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) は、指定されたサブセクションのキーを持つ構成サブセクションを返します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-408">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="ff10d-409">次のコードは、`section1` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-409">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-410">次のコードは、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-410">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-411">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-411">`GetSection` never returns `null`.</span></span> <span data-ttu-id="ff10d-412">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-412">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="ff10d-413">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-413">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="ff10d-414"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-414">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="ff10d-415">GetChildren と Exists</span><span class="sxs-lookup"><span data-stu-id="ff10d-415">GetChildren and Exists</span></span>

<span data-ttu-id="ff10d-416">次のコードは、[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) を呼び出し、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-416">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-417">上記のコードは、[ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を呼び出し、セクションが存在することを確認します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-417">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="ff10d-418">配列をバインドする</span><span class="sxs-lookup"><span data-stu-id="ff10d-418">Bind an array</span></span>

<span data-ttu-id="ff10d-419">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) は、構成キーの配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-419">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ff10d-420">数値のキー セグメントを公開する配列形式は、すべて [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-420">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="ff10d-421">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)の *MyArray.json* について考えます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-421">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="ff10d-422">次のコードでは、*MyArray.json* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-422">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="ff10d-423">次のコードでは、構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-423">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-424">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-424">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="ff10d-425">上記の出力では、インデックス3の値が `value40` になります。これは *MyArray. json* の `"4": "value40",` に対応しています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-425">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="ff10d-426">このバインドされた配列インデックスは連続的であり、構成キーインデックスにバインドされていません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-426">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="ff10d-427">構成バインダーは、バインドされたオブジェクトに null 値をバインドしたり、null エントリを作成したりはできません</span><span class="sxs-lookup"><span data-stu-id="ff10d-427">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="ff10d-428">次のコードでは、<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドで `array:entries` 構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-428">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="ff10d-429">次のコードでは、`arrayDict` `Dictionary` の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-429">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-430">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-430">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="ff10d-431">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-431">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="ff10d-432">配列を含む構成データがバインドされている場合、構成キーの配列インデックスは、オブジェクト作成時の構成データの反復のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-432">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="ff10d-433">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-433">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="ff10d-434">インデックス&num;3 に不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、インデックス&num;3のキーと値のペアを読み取る構成プロバイダーによってサプライできます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-434">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="ff10d-435">サンプルダウンロードの、次の *Value3.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-435">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="ff10d-436">次のコードには、*Value3.json* と `arrayDict` `Dictionary` の構成が含まれています：</span><span class="sxs-lookup"><span data-stu-id="ff10d-436">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="ff10d-437">次のコードは、上記の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-437">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-438">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-438">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="ff10d-439">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-439">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="ff10d-440">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-440">Custom configuration provider</span></span>

<span data-ttu-id="ff10d-441">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-441">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="ff10d-442">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-442">The provider has the following characteristics:</span></span>

* <span data-ttu-id="ff10d-443">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-443">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="ff10d-444">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-444">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="ff10d-445">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-445">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="ff10d-446">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-446">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="ff10d-447">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-447">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="ff10d-448">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-448">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="ff10d-449">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-449">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="ff10d-450">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-450">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="ff10d-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="ff10d-452"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-452">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="ff10d-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="ff10d-454"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-454">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="ff10d-455">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-455">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="ff10d-456">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-456">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="ff10d-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="ff10d-458">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-458">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="ff10d-459">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-459">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="ff10d-460">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-460">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="ff10d-461">起動時の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="ff10d-461">Access configuration in Startup</span></span>

<span data-ttu-id="ff10d-462">次のコードでは `Startup` メソッドの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-462">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="ff10d-463">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-463">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="ff10d-464">Razor ページの構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="ff10d-464">Access configuration in Razor Pages</span></span>

<span data-ttu-id="ff10d-465">次のコードでは Razor ページの構成データが表示されます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-465">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="ff10d-466">次のコードでは、`MyOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="ff10d-466">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="ff10d-467">次のマークアップは、[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、オプションの値を解決して表示します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-467">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="ff10d-468">MVC ビューファイルの構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="ff10d-468">Access configuration in a MVC view file</span></span>

<span data-ttu-id="ff10d-469">次のコードでは、MVC ビューの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="ff10d-469">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="ff10d-470">デリゲートでオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="ff10d-470">Configure options with a delegate</span></span>

<span data-ttu-id="ff10d-471">デリゲートで構成されたオプションは、構成プロバイダーで設定された値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-471">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="ff10d-472">サンプル アプリの例 2 では、デリゲートでオプションを構成しています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-472">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="ff10d-473">次のコードでは、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-473">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ff10d-474">デリゲートを利用して `MyOptions` の値が構成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-474">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="ff10d-475">このコードには、次のオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-475">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="ff10d-476">先の例では、値 `Option1` と `Option2` が *appsettings.json* で指定されてから、構成されているデリゲートによりオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-476">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="ff10d-477">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-477">Host versus app configuration</span></span>

<span data-ttu-id="ff10d-478">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-478">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="ff10d-479">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-479">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="ff10d-480">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-480">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="ff10d-481">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-481">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="ff10d-482">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-482">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="ff10d-483">既定のホスト構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-483">Default host configuration</span></span>

<span data-ttu-id="ff10d-484">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](?view=aspnetcore-2.2&preserve-view=true)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-484">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="ff10d-485">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-485">Host configuration is provided from:</span></span>
  * <span data-ttu-id="ff10d-486">[環境変数構成プロバイダー](#environment-variables)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-486">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="ff10d-487">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-487">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="ff10d-488">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-488">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="ff10d-489">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-489">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="ff10d-490">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-490">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="ff10d-491">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-491">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="ff10d-492">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-492">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="ff10d-493">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-493">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="ff10d-494">その他の構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-494">Other configuration</span></span>

<span data-ttu-id="ff10d-495">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-495">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="ff10d-496">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-496">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="ff10d-497">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-497">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="ff10d-498"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="ff10d-498">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="ff10d-499">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="ff10d-499">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="ff10d-500">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-500">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="ff10d-501">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-501">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="ff10d-502">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-502">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="ff10d-503">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="ff10d-503">Add configuration from an external assembly</span></span>

<span data-ttu-id="ff10d-504"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-504">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="ff10d-505">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-505">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff10d-506">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ff10d-506">Additional resources</span></span>

* [<span data-ttu-id="ff10d-507">構成のソースコード</span><span class="sxs-lookup"><span data-stu-id="ff10d-507">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff10d-508">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-508">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="ff10d-509">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-509">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="ff10d-510">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff10d-510">Azure Key Vault</span></span>
* <span data-ttu-id="ff10d-511">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="ff10d-511">Azure App Configuration</span></span>
* <span data-ttu-id="ff10d-512">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="ff10d-512">Command-line arguments</span></span>
* <span data-ttu-id="ff10d-513">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="ff10d-513">Custom providers (installed or created)</span></span>
* <span data-ttu-id="ff10d-514">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-514">Directory files</span></span>
* <span data-ttu-id="ff10d-515">環境変数</span><span class="sxs-lookup"><span data-stu-id="ff10d-515">Environment variables</span></span>
* <span data-ttu-id="ff10d-516">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="ff10d-516">In-memory .NET objects</span></span>
* <span data-ttu-id="ff10d-517">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-517">Settings files</span></span>

<span data-ttu-id="ff10d-518">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-518">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ff10d-519">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="ff10d-519">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="ff10d-520">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-520">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="ff10d-521">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-521">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="ff10d-522">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-522">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="ff10d-523">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ff10d-523">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="ff10d-524">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-524">Host versus app configuration</span></span>

<span data-ttu-id="ff10d-525">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-525">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="ff10d-526">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-526">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="ff10d-527">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-527">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="ff10d-528">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-528">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="ff10d-529">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-529">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="ff10d-530">その他の構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-530">Other configuration</span></span>

<span data-ttu-id="ff10d-531">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-531">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="ff10d-532">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-532">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="ff10d-533">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-533">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="ff10d-534"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="ff10d-534">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="ff10d-535">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="ff10d-535">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="ff10d-536">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-536">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="ff10d-537">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-537">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="ff10d-538">既定の構成</span><span class="sxs-lookup"><span data-stu-id="ff10d-538">Default configuration</span></span>

<span data-ttu-id="ff10d-539">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-539">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="ff10d-540">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-540">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="ff10d-541">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-541">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="ff10d-542">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-542">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="ff10d-543">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-543">Host configuration is provided from:</span></span>
  * <span data-ttu-id="ff10d-544">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-544">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="ff10d-545">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-545">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="ff10d-546">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-546">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="ff10d-547">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-547">App configuration is provided from:</span></span>
  * <span data-ttu-id="ff10d-548">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="ff10d-548">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="ff10d-549">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="ff10d-549">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="ff10d-550">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-550">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="ff10d-551">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-551">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="ff10d-552">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-552">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="ff10d-553">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="ff10d-553">Security</span></span>

<span data-ttu-id="ff10d-554">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-554">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="ff10d-555">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-555">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="ff10d-556">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-556">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="ff10d-557">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-557">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="ff10d-558">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-558">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="ff10d-559"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-559"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="ff10d-560">Secret Manager ツールでは、ファイル構成プロバイダーを使用して、ユーザー シークレットがローカル システム上の JSON ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-560">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="ff10d-561">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-561">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="ff10d-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="ff10d-563">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-563">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="ff10d-564">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="ff10d-564">Hierarchical configuration data</span></span>

<span data-ttu-id="ff10d-565">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-565">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="ff10d-566">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-566">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="ff10d-567">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-567">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="ff10d-568">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-568">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="ff10d-569">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-569">section0:key0</span></span>
* <span data-ttu-id="ff10d-570">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-570">section0:key1</span></span>
* <span data-ttu-id="ff10d-571">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-571">section1:key0</span></span>
* <span data-ttu-id="ff10d-572">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-572">section1:key1</span></span>

<span data-ttu-id="ff10d-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="ff10d-574">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-574">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="ff10d-575">規約</span><span class="sxs-lookup"><span data-stu-id="ff10d-575">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="ff10d-576">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-576">Sources and providers</span></span>

<span data-ttu-id="ff10d-577">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-577">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="ff10d-578">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-578">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="ff10d-579">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-579">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="ff10d-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ff10d-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="ff10d-582">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-582">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="ff10d-583">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-583">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="ff10d-584">キー</span><span class="sxs-lookup"><span data-stu-id="ff10d-584">Keys</span></span>

<span data-ttu-id="ff10d-585">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-585">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="ff10d-586">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-586">Keys are case-insensitive.</span></span> <span data-ttu-id="ff10d-587">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-587">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="ff10d-588">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-588">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="ff10d-589">重複する JSON キーの詳細については、「[こちらの GitHub のイシュー](https://github.com/dotnet/extensions/issues/2381)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-589">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="ff10d-590">階層キー</span><span class="sxs-lookup"><span data-stu-id="ff10d-590">Hierarchical keys</span></span>
  * <span data-ttu-id="ff10d-591">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-591">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="ff10d-592">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-592">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="ff10d-593">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-593">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="ff10d-594">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-594">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="ff10d-595">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-595">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="ff10d-596"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-596">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ff10d-597">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-597">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="ff10d-598">値</span><span class="sxs-lookup"><span data-stu-id="ff10d-598">Values</span></span>

<span data-ttu-id="ff10d-599">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-599">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="ff10d-600">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-600">Values are strings.</span></span>
* <span data-ttu-id="ff10d-601">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-601">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="ff10d-602">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-602">Providers</span></span>

<span data-ttu-id="ff10d-603">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-603">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="ff10d-604">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-604">Provider</span></span> | <span data-ttu-id="ff10d-605">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-605">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="ff10d-606">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="ff10d-606">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="ff10d-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff10d-607">Azure Key Vault</span></span> |
| <span data-ttu-id="ff10d-608">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="ff10d-608">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="ff10d-609">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="ff10d-609">Azure App Configuration</span></span> |
| [<span data-ttu-id="ff10d-610">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-610">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="ff10d-611">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="ff10d-611">Command-line parameters</span></span> |
| [<span data-ttu-id="ff10d-612">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-612">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="ff10d-613">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="ff10d-613">Custom source</span></span> |
| [<span data-ttu-id="ff10d-614">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-614">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="ff10d-615">環境変数</span><span class="sxs-lookup"><span data-stu-id="ff10d-615">Environment variables</span></span> |
| [<span data-ttu-id="ff10d-616">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-616">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="ff10d-617">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="ff10d-617">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="ff10d-618">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-618">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="ff10d-619">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-619">Directory files</span></span> |
| [<span data-ttu-id="ff10d-620">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-620">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="ff10d-621">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="ff10d-621">In-memory collections</span></span> |
| <span data-ttu-id="ff10d-622">[ユーザー シークレット](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="ff10d-622">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="ff10d-623">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="ff10d-623">File in the user profile directory</span></span> |

<span data-ttu-id="ff10d-624">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-624">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="ff10d-625">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-625">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="ff10d-626">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-626">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="ff10d-627">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-627">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="ff10d-628">ファイル ( *appsettings.json* 、*appsettings.{Environment}.json*。ここで、`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="ff10d-628">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="ff10d-629">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff10d-629">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="ff10d-630">[ユーザー シークレット](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="ff10d-630">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="ff10d-631">環境変数</span><span class="sxs-lookup"><span data-stu-id="ff10d-631">Environment variables</span></span>
1. <span data-ttu-id="ff10d-632">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="ff10d-632">Command-line arguments</span></span>

<span data-ttu-id="ff10d-633">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-633">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="ff10d-634">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-634">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff10d-635">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-635">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="ff10d-636">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="ff10d-636">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="ff10d-637">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-637">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="ff10d-638">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="ff10d-638">ConfigureAppConfiguration</span></span>

<span data-ttu-id="ff10d-639">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-639">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="ff10d-640">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="ff10d-640">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="ff10d-641">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-641">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="ff10d-642">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="ff10d-642">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="ff10d-643">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-643">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="ff10d-644">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="ff10d-644">Consume configuration during app startup</span></span>

<span data-ttu-id="ff10d-645">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-645">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ff10d-646">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-646">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="ff10d-647">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-647">Command-line Configuration Provider</span></span>

<span data-ttu-id="ff10d-648"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-648">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="ff10d-649">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-649">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff10d-650">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-650">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="ff10d-651">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-651">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ff10d-652">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-652">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ff10d-653">*appsettings.json* および *appsettings.{Environment}.json* ファイルからの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="ff10d-653">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="ff10d-654">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-654">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ff10d-655">環境変数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-655">Environment variables.</span></span>

<span data-ttu-id="ff10d-656">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-656">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="ff10d-657">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-657">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="ff10d-658">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-658">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="ff10d-659">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-659">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="ff10d-660">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-660">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff10d-661">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-661">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="ff10d-662">**例**</span><span class="sxs-lookup"><span data-stu-id="ff10d-662">**Example**</span></span>

<span data-ttu-id="ff10d-663">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-663">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="ff10d-664">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-664">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="ff10d-665">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-665">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="ff10d-666">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-666">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ff10d-667">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-667">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="ff10d-668">引数</span><span class="sxs-lookup"><span data-stu-id="ff10d-668">Arguments</span></span>

<span data-ttu-id="ff10d-669">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-669">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="ff10d-670">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-670">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="ff10d-671">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="ff10d-671">Key prefix</span></span>               | <span data-ttu-id="ff10d-672">例</span><span class="sxs-lookup"><span data-stu-id="ff10d-672">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="ff10d-673">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="ff10d-673">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="ff10d-674">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="ff10d-674">Two dashes (`--`)</span></span>        | <span data-ttu-id="ff10d-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="ff10d-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="ff10d-676">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="ff10d-676">Forward slash (`/`)</span></span>      | <span data-ttu-id="ff10d-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="ff10d-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="ff10d-678">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-678">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="ff10d-679">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="ff10d-679">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="ff10d-680">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="ff10d-680">Switch mappings</span></span>

<span data-ttu-id="ff10d-681">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-681">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="ff10d-682"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-682">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="ff10d-683">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-683">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="ff10d-684">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-684">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="ff10d-685">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-685">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="ff10d-686">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="ff10d-686">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="ff10d-687">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-687">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="ff10d-688">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-688">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="ff10d-689">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-689">Create a switch mappings dictionary.</span></span> <span data-ttu-id="ff10d-690">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-690">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="ff10d-691">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-691">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="ff10d-692">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-692">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="ff10d-693">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-693">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff10d-694">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-694">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="ff10d-695">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-695">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="ff10d-696">Key</span><span class="sxs-lookup"><span data-stu-id="ff10d-696">Key</span></span>       | <span data-ttu-id="ff10d-697">値</span><span class="sxs-lookup"><span data-stu-id="ff10d-697">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="ff10d-698">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-698">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="ff10d-699">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-699">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="ff10d-700">Key</span><span class="sxs-lookup"><span data-stu-id="ff10d-700">Key</span></span>               | <span data-ttu-id="ff10d-701">[値]</span><span class="sxs-lookup"><span data-stu-id="ff10d-701">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="ff10d-702">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-702">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="ff10d-703"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-703">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="ff10d-704">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-704">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="ff10d-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="ff10d-706">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-706">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="ff10d-707">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-707">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="ff10d-708">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-708">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ff10d-709">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-709">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ff10d-710">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="ff10d-710">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="ff10d-711">*appsettings.json* および *appsettings.{Environment}.json* ファイルからの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="ff10d-711">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="ff10d-712">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-712">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ff10d-713">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-713">Command-line arguments.</span></span>

<span data-ttu-id="ff10d-714">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-714">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="ff10d-715">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-715">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="ff10d-716">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-716">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="ff10d-717">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-717">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="ff10d-718">**例**</span><span class="sxs-lookup"><span data-stu-id="ff10d-718">**Example**</span></span>

<span data-ttu-id="ff10d-719">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-719">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="ff10d-720">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-720">Run the sample app.</span></span> <span data-ttu-id="ff10d-721">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-721">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ff10d-722">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-722">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="ff10d-723">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-723">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="ff10d-724">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-724">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="ff10d-725">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-725">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="ff10d-726">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-726">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="ff10d-727">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="ff10d-727">Prefixes</span></span>

<span data-ttu-id="ff10d-728">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-728">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="ff10d-729">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-729">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="ff10d-730">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-730">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="ff10d-731">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-731">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="ff10d-732">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-732">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ff10d-733">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="ff10d-733">**Connection string prefixes**</span></span>

<span data-ttu-id="ff10d-734">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-734">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="ff10d-735">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-735">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="ff10d-736">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="ff10d-736">Connection string prefix</span></span> | <span data-ttu-id="ff10d-737">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-737">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="ff10d-738">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-738">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="ff10d-739">MySQL</span><span class="sxs-lookup"><span data-stu-id="ff10d-739">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="ff10d-740">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="ff10d-740">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="ff10d-741">SQL Server</span><span class="sxs-lookup"><span data-stu-id="ff10d-741">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="ff10d-742">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="ff10d-742">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="ff10d-743">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-743">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="ff10d-744">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-744">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="ff10d-745">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="ff10d-745">Environment variable key</span></span> | <span data-ttu-id="ff10d-746">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="ff10d-746">Converted configuration key</span></span> | <span data-ttu-id="ff10d-747">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="ff10d-747">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ff10d-748">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-748">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ff10d-749">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff10d-749">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ff10d-750">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff10d-750">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ff10d-751">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff10d-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ff10d-752">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff10d-752">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="ff10d-753">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff10d-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="ff10d-754">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff10d-754">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="ff10d-755">**例**</span><span class="sxs-lookup"><span data-stu-id="ff10d-755">**Example**</span></span>

<span data-ttu-id="ff10d-756">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-756">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="ff10d-757">名前: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="ff10d-757">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="ff10d-758">値: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="ff10d-758">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="ff10d-759">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-759">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="ff10d-760">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-760">File Configuration Provider</span></span>

<span data-ttu-id="ff10d-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="ff10d-762">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-762">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="ff10d-763">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-763">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="ff10d-764">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-764">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="ff10d-765">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-765">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="ff10d-766">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-766">INI Configuration Provider</span></span>

<span data-ttu-id="ff10d-767"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-767">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="ff10d-768">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-768">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff10d-769">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-769">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="ff10d-770">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-770">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff10d-771">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="ff10d-771">Whether the file is optional.</span></span>
* <span data-ttu-id="ff10d-772">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="ff10d-772">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ff10d-773">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-773">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ff10d-774">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ff10d-775">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="ff10d-775">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="ff10d-776">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-776">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff10d-777">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-777">section0:key0</span></span>
* <span data-ttu-id="ff10d-778">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-778">section0:key1</span></span>
* <span data-ttu-id="ff10d-779">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="ff10d-779">section1:subsection:key</span></span>
* <span data-ttu-id="ff10d-780">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="ff10d-780">section2:subsection0:key</span></span>
* <span data-ttu-id="ff10d-781">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="ff10d-781">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="ff10d-782">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-782">JSON Configuration Provider</span></span>

<span data-ttu-id="ff10d-783"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-783">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="ff10d-784">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-784">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff10d-785">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-785">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff10d-786">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="ff10d-786">Whether the file is optional.</span></span>
* <span data-ttu-id="ff10d-787">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="ff10d-787">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ff10d-788">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-788">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ff10d-789">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-789">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff10d-790">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-790">The method is called to load configuration from:</span></span>

* <span data-ttu-id="ff10d-791">*appsettings.json* : このファイルは最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-791">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="ff10d-792">ファイルの環境バージョンを使用すると、 *appsettings.json* ファイルによって指定された値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-792">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="ff10d-793">*appsettings.{Environment}.json*:ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-793">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="ff10d-794">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-794">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ff10d-795">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-795">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ff10d-796">環境変数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-796">Environment variables.</span></span>
* <span data-ttu-id="ff10d-797">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-797">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ff10d-798">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="ff10d-798">Command-line arguments.</span></span>

<span data-ttu-id="ff10d-799">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-799">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="ff10d-800">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-800">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="ff10d-801">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、 *appsettings.json* および *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ff10d-802">**例**</span><span class="sxs-lookup"><span data-stu-id="ff10d-802">**Example**</span></span>

<span data-ttu-id="ff10d-803">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-803">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="ff10d-804">`AddJsonFile` の最初の呼び出しを行うと、 *appsettings.json* から構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-804">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="ff10d-805">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-805">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="ff10d-806">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-806">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="ff10d-807">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-807">Run the sample app.</span></span> <span data-ttu-id="ff10d-808">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-808">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ff10d-809">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-809">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="ff10d-810">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-810">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="ff10d-811">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-811">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="ff10d-812">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-812">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="ff10d-813">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-813">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="ff10d-814">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-814">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="ff10d-815">*appsettings.Development.json* 内の設定によって、 *appsettings.json* 内の設定はオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="ff10d-815">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="ff10d-816">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-816">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="ff10d-817">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-817">XML Configuration Provider</span></span>

<span data-ttu-id="ff10d-818"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-818">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="ff10d-819">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-819">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff10d-820">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-820">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff10d-821">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="ff10d-821">Whether the file is optional.</span></span>
* <span data-ttu-id="ff10d-822">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="ff10d-822">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ff10d-823">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-823">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ff10d-824">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-824">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="ff10d-825">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-825">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="ff10d-826">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-826">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ff10d-827">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-827">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="ff10d-828">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-828">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff10d-829">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-829">section0:key0</span></span>
* <span data-ttu-id="ff10d-830">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-830">section0:key1</span></span>
* <span data-ttu-id="ff10d-831">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-831">section1:key0</span></span>
* <span data-ttu-id="ff10d-832">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-832">section1:key1</span></span>

<span data-ttu-id="ff10d-833">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-833">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="ff10d-834">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-834">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff10d-835">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-835">section:section0:key:key0</span></span>
* <span data-ttu-id="ff10d-836">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-836">section:section0:key:key1</span></span>
* <span data-ttu-id="ff10d-837">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-837">section:section1:key:key0</span></span>
* <span data-ttu-id="ff10d-838">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-838">section:section1:key:key1</span></span>

<span data-ttu-id="ff10d-839">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-839">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="ff10d-840">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-840">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff10d-841">key:attribute</span><span class="sxs-lookup"><span data-stu-id="ff10d-841">key:attribute</span></span>
* <span data-ttu-id="ff10d-842">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="ff10d-842">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="ff10d-843">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-843">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="ff10d-844"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-844">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="ff10d-845">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-845">The key is the file name.</span></span> <span data-ttu-id="ff10d-846">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-846">The value contains the file's contents.</span></span> <span data-ttu-id="ff10d-847">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-847">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="ff10d-848">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-848">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="ff10d-849">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-849">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="ff10d-850">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-850">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff10d-851">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="ff10d-851">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="ff10d-852">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="ff10d-852">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="ff10d-853">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-853">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="ff10d-854">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-854">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="ff10d-855">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-855">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="ff10d-856">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-856">Memory Configuration Provider</span></span>

<span data-ttu-id="ff10d-857"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-857">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="ff10d-858">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-858">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff10d-859">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-859">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="ff10d-860">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-860">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="ff10d-861">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-861">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="ff10d-862">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-862">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="ff10d-863">GetValue</span><span class="sxs-lookup"><span data-stu-id="ff10d-863">GetValue</span></span>

<span data-ttu-id="ff10d-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを持つ構成から単一の値を抽出し、指定したコレクション以外の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="ff10d-865">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-865">An overload accepts a default value.</span></span>

<span data-ttu-id="ff10d-866">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-866">The following example:</span></span>

* <span data-ttu-id="ff10d-867">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-867">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="ff10d-868">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-868">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="ff10d-869">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-869">Types the value as an `int`.</span></span>
* <span data-ttu-id="ff10d-870">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-870">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="ff10d-871">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="ff10d-871">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="ff10d-872">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-872">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="ff10d-873">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-873">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="ff10d-874">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-874">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="ff10d-875">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-875">section0:key0</span></span>
* <span data-ttu-id="ff10d-876">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-876">section0:key1</span></span>
* <span data-ttu-id="ff10d-877">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-877">section1:key0</span></span>
* <span data-ttu-id="ff10d-878">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-878">section1:key1</span></span>
* <span data-ttu-id="ff10d-879">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-879">section2:subsection0:key0</span></span>
* <span data-ttu-id="ff10d-880">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-880">section2:subsection0:key1</span></span>
* <span data-ttu-id="ff10d-881">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="ff10d-881">section2:subsection1:key0</span></span>
* <span data-ttu-id="ff10d-882">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="ff10d-882">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="ff10d-883">GetSection</span><span class="sxs-lookup"><span data-stu-id="ff10d-883">GetSection</span></span>

<span data-ttu-id="ff10d-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="ff10d-885">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-885">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="ff10d-886">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-886">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="ff10d-887">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-887">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="ff10d-888">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-888">`GetSection` never returns `null`.</span></span> <span data-ttu-id="ff10d-889">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-889">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="ff10d-890">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-890">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="ff10d-891"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-891">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="ff10d-892">GetChildren</span><span class="sxs-lookup"><span data-stu-id="ff10d-892">GetChildren</span></span>

<span data-ttu-id="ff10d-893">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-893">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="ff10d-894">既存</span><span class="sxs-lookup"><span data-stu-id="ff10d-894">Exists</span></span>

<span data-ttu-id="ff10d-895">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-895">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="ff10d-896">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-896">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="ff10d-897">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="ff10d-897">Bind to an object graph</span></span>

<span data-ttu-id="ff10d-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="ff10d-899">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-899">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="ff10d-900">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="ff10d-900">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="ff10d-901">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-901">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="ff10d-902">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-902">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="ff10d-903">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-903">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="ff10d-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="ff10d-905">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-905">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="ff10d-906">次のコードは、上記の例で `Get<T>` を使用する方法を示します：</span><span class="sxs-lookup"><span data-stu-id="ff10d-906">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="ff10d-907">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="ff10d-907">Bind an array to a class</span></span>

<span data-ttu-id="ff10d-908">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="ff10d-908">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="ff10d-909"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ff10d-909">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ff10d-910">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-910">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="ff10d-911">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-911">Binding is provided by convention.</span></span> <span data-ttu-id="ff10d-912">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-912">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="ff10d-913">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="ff10d-913">**In-memory array processing**</span></span>

<span data-ttu-id="ff10d-914">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-914">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="ff10d-915">Key</span><span class="sxs-lookup"><span data-stu-id="ff10d-915">Key</span></span>             | <span data-ttu-id="ff10d-916">[値]</span><span class="sxs-lookup"><span data-stu-id="ff10d-916">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="ff10d-917">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="ff10d-917">array:entries:0</span></span> | <span data-ttu-id="ff10d-918">value0</span><span class="sxs-lookup"><span data-stu-id="ff10d-918">value0</span></span> |
| <span data-ttu-id="ff10d-919">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="ff10d-919">array:entries:1</span></span> | <span data-ttu-id="ff10d-920">value1</span><span class="sxs-lookup"><span data-stu-id="ff10d-920">value1</span></span> |
| <span data-ttu-id="ff10d-921">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="ff10d-921">array:entries:2</span></span> | <span data-ttu-id="ff10d-922">value2</span><span class="sxs-lookup"><span data-stu-id="ff10d-922">value2</span></span> |
| <span data-ttu-id="ff10d-923">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="ff10d-923">array:entries:4</span></span> | <span data-ttu-id="ff10d-924">value4</span><span class="sxs-lookup"><span data-stu-id="ff10d-924">value4</span></span> |
| <span data-ttu-id="ff10d-925">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="ff10d-925">array:entries:5</span></span> | <span data-ttu-id="ff10d-926">value5</span><span class="sxs-lookup"><span data-stu-id="ff10d-926">value5</span></span> |

<span data-ttu-id="ff10d-927">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-927">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="ff10d-928">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-928">The array skips a value for index &num;3.</span></span> <span data-ttu-id="ff10d-929">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-929">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="ff10d-930">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-930">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="ff10d-931">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-931">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="ff10d-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用することもできます。これにより、コードがよりコンパクトになります：</span><span class="sxs-lookup"><span data-stu-id="ff10d-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="ff10d-933">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-933">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="ff10d-934">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="ff10d-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="ff10d-935">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="ff10d-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="ff10d-936">0</span><span class="sxs-lookup"><span data-stu-id="ff10d-936">0</span></span>                            | <span data-ttu-id="ff10d-937">value0</span><span class="sxs-lookup"><span data-stu-id="ff10d-937">value0</span></span>                       |
| <span data-ttu-id="ff10d-938">1</span><span class="sxs-lookup"><span data-stu-id="ff10d-938">1</span></span>                            | <span data-ttu-id="ff10d-939">value1</span><span class="sxs-lookup"><span data-stu-id="ff10d-939">value1</span></span>                       |
| <span data-ttu-id="ff10d-940">2</span><span class="sxs-lookup"><span data-stu-id="ff10d-940">2</span></span>                            | <span data-ttu-id="ff10d-941">value2</span><span class="sxs-lookup"><span data-stu-id="ff10d-941">value2</span></span>                       |
| <span data-ttu-id="ff10d-942">3</span><span class="sxs-lookup"><span data-stu-id="ff10d-942">3</span></span>                            | <span data-ttu-id="ff10d-943">value4</span><span class="sxs-lookup"><span data-stu-id="ff10d-943">value4</span></span>                       |
| <span data-ttu-id="ff10d-944">4</span><span class="sxs-lookup"><span data-stu-id="ff10d-944">4</span></span>                            | <span data-ttu-id="ff10d-945">value5</span><span class="sxs-lookup"><span data-stu-id="ff10d-945">value5</span></span>                       |

<span data-ttu-id="ff10d-946">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-946">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="ff10d-947">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-947">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="ff10d-948">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-948">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="ff10d-949">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-949">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="ff10d-950">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-950">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="ff10d-951">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-951">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="ff10d-952">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="ff10d-952">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="ff10d-953">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-953">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="ff10d-954">Key</span><span class="sxs-lookup"><span data-stu-id="ff10d-954">Key</span></span>             | <span data-ttu-id="ff10d-955">値</span><span class="sxs-lookup"><span data-stu-id="ff10d-955">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="ff10d-956">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="ff10d-956">array:entries:3</span></span> | <span data-ttu-id="ff10d-957">value3</span><span class="sxs-lookup"><span data-stu-id="ff10d-957">value3</span></span> |

<span data-ttu-id="ff10d-958">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-958">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="ff10d-959">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="ff10d-959">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="ff10d-960">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="ff10d-960">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="ff10d-961">0</span><span class="sxs-lookup"><span data-stu-id="ff10d-961">0</span></span>                            | <span data-ttu-id="ff10d-962">value0</span><span class="sxs-lookup"><span data-stu-id="ff10d-962">value0</span></span>                       |
| <span data-ttu-id="ff10d-963">1</span><span class="sxs-lookup"><span data-stu-id="ff10d-963">1</span></span>                            | <span data-ttu-id="ff10d-964">value1</span><span class="sxs-lookup"><span data-stu-id="ff10d-964">value1</span></span>                       |
| <span data-ttu-id="ff10d-965">2</span><span class="sxs-lookup"><span data-stu-id="ff10d-965">2</span></span>                            | <span data-ttu-id="ff10d-966">value2</span><span class="sxs-lookup"><span data-stu-id="ff10d-966">value2</span></span>                       |
| <span data-ttu-id="ff10d-967">3</span><span class="sxs-lookup"><span data-stu-id="ff10d-967">3</span></span>                            | <span data-ttu-id="ff10d-968">value3</span><span class="sxs-lookup"><span data-stu-id="ff10d-968">value3</span></span>                       |
| <span data-ttu-id="ff10d-969">4</span><span class="sxs-lookup"><span data-stu-id="ff10d-969">4</span></span>                            | <span data-ttu-id="ff10d-970">value4</span><span class="sxs-lookup"><span data-stu-id="ff10d-970">value4</span></span>                       |
| <span data-ttu-id="ff10d-971">5</span><span class="sxs-lookup"><span data-stu-id="ff10d-971">5</span></span>                            | <span data-ttu-id="ff10d-972">value5</span><span class="sxs-lookup"><span data-stu-id="ff10d-972">value5</span></span>                       |

<span data-ttu-id="ff10d-973">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="ff10d-973">**JSON array processing**</span></span>

<span data-ttu-id="ff10d-974">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-974">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="ff10d-975">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="ff10d-975">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="ff10d-976">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-976">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="ff10d-977">Key</span><span class="sxs-lookup"><span data-stu-id="ff10d-977">Key</span></span>                     | <span data-ttu-id="ff10d-978">値</span><span class="sxs-lookup"><span data-stu-id="ff10d-978">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="ff10d-979">json_array:key</span><span class="sxs-lookup"><span data-stu-id="ff10d-979">json_array:key</span></span>          | <span data-ttu-id="ff10d-980">valueA</span><span class="sxs-lookup"><span data-stu-id="ff10d-980">valueA</span></span> |
| <span data-ttu-id="ff10d-981">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="ff10d-981">json_array:subsection:0</span></span> | <span data-ttu-id="ff10d-982">valueB</span><span class="sxs-lookup"><span data-stu-id="ff10d-982">valueB</span></span> |
| <span data-ttu-id="ff10d-983">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="ff10d-983">json_array:subsection:1</span></span> | <span data-ttu-id="ff10d-984">valueC</span><span class="sxs-lookup"><span data-stu-id="ff10d-984">valueC</span></span> |
| <span data-ttu-id="ff10d-985">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="ff10d-985">json_array:subsection:2</span></span> | <span data-ttu-id="ff10d-986">valueD</span><span class="sxs-lookup"><span data-stu-id="ff10d-986">valueD</span></span> |

<span data-ttu-id="ff10d-987">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-987">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="ff10d-988">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-988">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="ff10d-989">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-989">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="ff10d-990">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="ff10d-990">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="ff10d-991">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="ff10d-991">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="ff10d-992">0</span><span class="sxs-lookup"><span data-stu-id="ff10d-992">0</span></span>                                   | <span data-ttu-id="ff10d-993">valueB</span><span class="sxs-lookup"><span data-stu-id="ff10d-993">valueB</span></span>                              |
| <span data-ttu-id="ff10d-994">1</span><span class="sxs-lookup"><span data-stu-id="ff10d-994">1</span></span>                                   | <span data-ttu-id="ff10d-995">valueC</span><span class="sxs-lookup"><span data-stu-id="ff10d-995">valueC</span></span>                              |
| <span data-ttu-id="ff10d-996">2</span><span class="sxs-lookup"><span data-stu-id="ff10d-996">2</span></span>                                   | <span data-ttu-id="ff10d-997">valueD</span><span class="sxs-lookup"><span data-stu-id="ff10d-997">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="ff10d-998">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ff10d-998">Custom configuration provider</span></span>

<span data-ttu-id="ff10d-999">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-999">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="ff10d-1000">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1000">The provider has the following characteristics:</span></span>

* <span data-ttu-id="ff10d-1001">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1001">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="ff10d-1002">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1002">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="ff10d-1003">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1003">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="ff10d-1004">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1004">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="ff10d-1005">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1005">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="ff10d-1006">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1006">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="ff10d-1007">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-1007">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="ff10d-1008">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1008">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="ff10d-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="ff10d-1010"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1010">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="ff10d-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="ff10d-1012"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1012">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="ff10d-1013">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1013">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="ff10d-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="ff10d-1015">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1015">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="ff10d-1016">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff10d-1016">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="ff10d-1017">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1017">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="ff10d-1018">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="ff10d-1018">Access configuration during startup</span></span>

<span data-ttu-id="ff10d-1019">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1019">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ff10d-1020">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1020">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="ff10d-1021">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1021">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="ff10d-1022">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="ff10d-1022">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="ff10d-1023">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) 名前空間に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1023">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="ff10d-1024">Razor Pages ページで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1024">In a Razor Pages page:</span></span>

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

<span data-ttu-id="ff10d-1025">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="ff10d-1025">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="ff10d-1026">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="ff10d-1026">Add configuration from an external assembly</span></span>

<span data-ttu-id="ff10d-1027"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1027">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="ff10d-1028">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ff10d-1028">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff10d-1029">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ff10d-1029">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

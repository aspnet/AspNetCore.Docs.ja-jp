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
ms.openlocfilehash: 24b4d5fc11d21dce4d9e0fd2f8f0dd2d45e82baa
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110080"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="90e98-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="90e98-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="90e98-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="90e98-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90e98-105">ASP.NET Core の構成は、1つまたは複数の[構成プロバイダー](#cp)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="90e98-106">構成プロバイダーは、以下のようなさまざまな構成ソースを使用して、キーと値のペアから構成データを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="90e98-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="90e98-107">*appsettings.json* などの設定ファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="90e98-108">環境変数</span><span class="sxs-lookup"><span data-stu-id="90e98-108">Environment variables</span></span>
* <span data-ttu-id="90e98-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90e98-109">Azure Key Vault</span></span>
* <span data-ttu-id="90e98-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="90e98-110">Azure App Configuration</span></span>
* <span data-ttu-id="90e98-111">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="90e98-111">Command-line arguments</span></span>
* <span data-ttu-id="90e98-112">インストール済みまたは作成済みのカスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="90e98-113">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-113">Directory files</span></span>
* <span data-ttu-id="90e98-114">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="90e98-114">In-memory .NET objects</span></span>

<span data-ttu-id="90e98-115">このトピックでは ASP.NET Core の構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="90e98-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="90e98-116">コンソール アプリでの構成の使用方法について詳しくは、[.NET 構成](/dotnet/core/extensions/configuration)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90e98-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="90e98-117">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="90e98-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="90e98-118">既定の構成</span><span class="sxs-lookup"><span data-stu-id="90e98-118">Default configuration</span></span>

<span data-ttu-id="90e98-119">[dotnet new](/dotnet/core/tools/dotnet-new) または Visual Studio で作成された ASP.NET Core の web アプリが、次のコードを生成します:</span><span class="sxs-lookup"><span data-stu-id="90e98-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="90e98-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="90e98-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) は:既存の `IConfiguration` をソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="90e98-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="90e98-122">既定の構成では、[ホスト](#hvac)構成を追加し、_アプリ_ 構成の最初のソースとして設定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="90e98-123">[JSON 構成プロバイダー](#file-configuration-provider)を使用する [appsettings.json](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="90e98-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="90e98-124">[JSON 構成プロバイダー](#file-configuration-provider)を使用する *appsettings.* `Environment`*json*。</span><span class="sxs-lookup"><span data-stu-id="90e98-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="90e98-125">たとえば、*appsettings*.***Production\*\*_._json* および *appsettings*.\*\*\*Development** _._json\*。</span><span class="sxs-lookup"><span data-stu-id="90e98-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="90e98-126">`Development` 環境でアプリが実行される際の [App シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90e98-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="90e98-127">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="90e98-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="90e98-128">[コマンドライン構成プロバイダー](#command-line)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90e98-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="90e98-129">後から追加される構成プロバイダーは、それ以前のキー設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90e98-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="90e98-130">たとえば、`MyKey` が *appsettings.json* と環境の両方で設定されている場合、環境の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="90e98-131">既定の構成プロバイダーを使用すると、[コマンドライン構成プロバイダー](#clcp) が他のすべてのプロバイダーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90e98-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="90e98-132">`CreateDefaultBuilder` の詳細については、[既定のビルダー設定](xref:fundamentals/host/generic-host#default-builder-settings)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="90e98-133">以下のコードでは、追加した順に、有効な構成プロバイダーが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90e98-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="90e98-134">次の *appsettings.json* ファイルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="90e98-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="90e98-135">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90e98-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-136">既定の <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> は、以下の順序で構成を読み込みます:</span><span class="sxs-lookup"><span data-stu-id="90e98-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="90e98-137">*appsettings.* `Environment` *.json*:たとえば、*appsettings*.***Production\*\*_._json* および *appsettings*.\*\*\*Development** _._json\* ファイル。</span><span class="sxs-lookup"><span data-stu-id="90e98-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="90e98-138">ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="90e98-139">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="90e98-140">*appsettings*.`Environment`.*json* の値によって、 *appsettings.json* 内のキーがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="90e98-141">たとえば、既定では次のようになります:</span><span class="sxs-lookup"><span data-stu-id="90e98-141">For example, by default:</span></span>

* <span data-ttu-id="90e98-142">開発中は、*appsettings*.***Development** _._json* 構成によって、 *appsettings.json* で見つかった値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="90e98-143">運用環境では、*appsettings*.***Production** _._json* 構成によって、 *appsettings.json* で見つかった値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="90e98-144">たとえば、Azure にアプリをデプロイする場合。</span><span class="sxs-lookup"><span data-stu-id="90e98-144">For example, when deploying the app to Azure.</span></span>

<span data-ttu-id="90e98-145">構成値を保証する必要がある場合は、「[GetValue](#getvalue)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-145">If a configuration value must be guaranteed, see [GetValue](#getvalue).</span></span> <span data-ttu-id="90e98-146">前の例では文字列が読み取られるだけで、既定値はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="90e98-146">The preceding example only reads strings and doesn’t support a default value</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="90e98-147">オプションパターンを使用して、階層型の構成データをバインドします</span><span class="sxs-lookup"><span data-stu-id="90e98-147">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="90e98-148">[既定](#default)の構成を利用する場合、[reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) で *appsettings.json* と *appsettings.* `Environment` *.json* ファイルを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-148">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="90e98-149">アプリの開始 ***後*** に *appsettings.json* と *appsettings.* `Environment` *.json* ファイルに加えられた変更は、[JSON 構成プロバイダー](#jcp)によって読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90e98-149">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="90e98-150">追加の JSON 構成ファイルを追加する方法の詳細については、このドキュメント中の「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-150">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="90e98-151">サービス コレクションの結合</span><span class="sxs-lookup"><span data-stu-id="90e98-151">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="90e98-152">セキュリティとユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="90e98-152">Security and user secrets</span></span>

<span data-ttu-id="90e98-153">構成データのガイドライン:</span><span class="sxs-lookup"><span data-stu-id="90e98-153">Configuration data guidelines:</span></span>

* <span data-ttu-id="90e98-154">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="90e98-154">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="90e98-155">[Secret Manager](xref:security/app-secrets) ツールを使用すると、開発時にシークレットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-155">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="90e98-156">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="90e98-156">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="90e98-157">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-157">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="90e98-158">[既定](#default)では、ユーザー シークレットの構成ソースは、JSON 構成ソースの後に登録されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-158">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="90e98-159">このため、ユーザー シークレット キーは、 *appsettings.json* および *appsettings.* `Environment` *.json* よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-159">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="90e98-160">パスワードその他の機密データの格納については、次を参照してください：</span><span class="sxs-lookup"><span data-stu-id="90e98-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="90e98-161"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-161"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="90e98-162">Secret Manager ツールでは、[ファイル構成プロバイダー](#fcp)を使用して、ユーザーのシークレットがローカル システム上の JSON ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-162">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="90e98-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="90e98-164">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="90e98-165">環境変数</span><span class="sxs-lookup"><span data-stu-id="90e98-165">Environment variables</span></span>

<span data-ttu-id="90e98-166">[既定](#default)の構成を使用すると、<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> によって、 *appsettings.json* 、*appsettings.* `Environment` *.json*、および [ユーザー シークレット](xref:security/app-secrets)の読み取り後に、環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="90e98-167">そのため、環境から読み取られたキー値によって、 *appsettings.json* 、*appsettings.* `Environment` *.json*、およびユーザー シークレットから読み取られた値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="90e98-168">次の `set` コマンドは：</span><span class="sxs-lookup"><span data-stu-id="90e98-168">The following `set` commands:</span></span>

* <span data-ttu-id="90e98-169">Windows で[ 上記の例 ](#appsettingsjson)の環境キーと値を設定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="90e98-170">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)を使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="90e98-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="90e98-171">`dotnet run` コマンドは、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="90e98-172">上記の環境設定は：</span><span class="sxs-lookup"><span data-stu-id="90e98-172">The preceding environment settings:</span></span>

* <span data-ttu-id="90e98-173">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="90e98-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="90e98-174">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="90e98-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="90e98-175">次の [setx](/windows-server/administration/windows-commands/setx) コマンドで、Windows 上の環境キーと値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="90e98-176">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="90e98-177">`/M` は、システム環境で変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="90e98-178">`/M` スイッチが使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="90e98-179">上記のコマンドによって、 *appsettings.json* および *appsettings.* `Environment` *.json* がオーバーライドされるのをテストするには:</span><span class="sxs-lookup"><span data-stu-id="90e98-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="90e98-180">Visual Studio の場合:Visual Studio を終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="90e98-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="90e98-181">CLI の場合:新しいコマンド ウィンドウを起動し、`dotnet run` を入力します。</span><span class="sxs-lookup"><span data-stu-id="90e98-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="90e98-182">環境変数のプレフィックスを指定する文字列を指定して <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します：</span><span class="sxs-lookup"><span data-stu-id="90e98-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="90e98-183">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="90e98-183">In the preceding code:</span></span>

* <span data-ttu-id="90e98-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` は[既定の構成プロバイダー](#default)の後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="90e98-185">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="90e98-186">`MyCustomPrefix_` プレフィックスを使用して設定された環境変数は、[既定の構成プロバイダー](#default)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90e98-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="90e98-187">これには、プレフィックスのない環境変数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="90e98-188">構成のキーと値のペアの読み取り時に、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="90e98-189">次のコマンドは、カスタム プレフィックスをテストします：</span><span class="sxs-lookup"><span data-stu-id="90e98-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="90e98-190">[既定の構成](#default)では、`DOTNET_` と `ASPNETCORE_` のプレフィックスが付いた環境変数とコマンド ライン引数を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="90e98-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="90e98-191">`DOTNET_` と `ASPNETCORE_` のプレフィックスは ASP.NET Core によって[ホストとアプリの構成](xref:fundamentals/host/generic-host#host-configuration)に使用されますが、ユーザーの構成には使用されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="90e98-192">ホストとアプリの構成の詳細については、「[.NET 汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="90e98-193">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの **新しいアプリケーション設定** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90e98-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="90e98-194">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="90e98-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="90e98-195">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="90e98-196">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-196">Exposed as environment variables.</span></span>

<span data-ttu-id="90e98-197">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="90e98-198">Azure データベース接続文字列の詳細については、「[接続文字列のプレフィックス](#constr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="90e98-199">環境変数の名前付け</span><span class="sxs-lookup"><span data-stu-id="90e98-199">Naming of environment variables</span></span>

<span data-ttu-id="90e98-200">環境変数名には、 *appsettings.json* ファイルの構造が反映されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-200">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="90e98-201">階層内の各要素は、二重アンダースコア (推奨) またはコロンによって区切られます。</span><span class="sxs-lookup"><span data-stu-id="90e98-201">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="90e98-202">要素構造に配列が含まれている場合は、配列のインデックスをこのパスの追加の要素名として扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-202">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="90e98-203">次の *appsettings.json* ファイルと、環境変数として表されたその同等の値を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="90e98-203">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="90e98-204">**環境変数**</span><span class="sxs-lookup"><span data-stu-id="90e98-204">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="90e98-205">生成された launchSettings.json に設定されている環境変数</span><span class="sxs-lookup"><span data-stu-id="90e98-205">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="90e98-206">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-206">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="90e98-207">たとえば、ASP.NET Core Web テンプレートでは、エンドポイント構成を次のように設定する *launchSettings.json* ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-207">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="90e98-208">`applicationUrl` を構成すると `ASPNETCORE_URLS` 環境変数が設定され、環境で設定されている値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-208">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="90e98-209">Linux で環境変数をエスケープする</span><span class="sxs-lookup"><span data-stu-id="90e98-209">Escape environment variables on Linux</span></span>

<span data-ttu-id="90e98-210">Linux では、`systemd` で解析できるように URL 環境変数の値をエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-210">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="90e98-211">Linux ツール `systemd-escape` を使用すると、`http:--localhost:5001` が生成されます</span><span class="sxs-lookup"><span data-stu-id="90e98-211">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="90e98-212">環境変数を表示する</span><span class="sxs-lookup"><span data-stu-id="90e98-212">Display environment variables</span></span>

<span data-ttu-id="90e98-213">次のコードでは、アプリケーションの起動時に環境変数と値が表示されます。これは環境設定をデバッグするときに役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="90e98-213">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="90e98-214">コマンド ライン</span><span class="sxs-lookup"><span data-stu-id="90e98-214">Command-line</span></span>

<span data-ttu-id="90e98-215">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> は、以下の構成ソースの後にコマンド ライン引数のキーと値のペアから構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="90e98-215">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="90e98-216">*appsettings.json* および *appsettings*.`Environment`.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="90e98-216">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="90e98-217">開発環境での[アプリ シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90e98-217">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90e98-218">環境変数。</span><span class="sxs-lookup"><span data-stu-id="90e98-218">Environment variables.</span></span>

<span data-ttu-id="90e98-219">[既定](#default)では、コマンド ラインで設定された構成値は、他のすべての構成プロバイダーで設定された構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90e98-219">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="90e98-220">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="90e98-220">Command-line arguments</span></span>

<span data-ttu-id="90e98-221">次のコマンドは `=` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="90e98-221">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="90e98-222">次のコマンドは `/` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="90e98-222">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="90e98-223">次のコマンドは `--` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="90e98-223">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="90e98-224">キーの値:</span><span class="sxs-lookup"><span data-stu-id="90e98-224">The key value:</span></span>

* <span data-ttu-id="90e98-225">`=` の後に続ける必要があります。または、値がスペースの後にある場合は、キーのプレフィックスが `--` または `/` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-225">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="90e98-226">`=` を使用する場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-226">Isn't required if `=` is used.</span></span> <span data-ttu-id="90e98-227">たとえば、`MySetting=` のようにします。</span><span class="sxs-lookup"><span data-stu-id="90e98-227">For example, `MySetting=`.</span></span>

<span data-ttu-id="90e98-228">同じコマンド内では、`=` を使用するコマンド ライン引数のキーと値のペアを、スペースを使用するキーと値のペアと混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="90e98-228">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="90e98-229">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="90e98-229">Switch mappings</span></span>

<span data-ttu-id="90e98-230">スイッチ マッピングでは、**キー** 名の置換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-230">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="90e98-231"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換するディクショナリを提供します。</span><span class="sxs-lookup"><span data-stu-id="90e98-231">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="90e98-232">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-232">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="90e98-233">ディクショナリ中にコマンド ライン キーが見つかった場合は、そのディクショナリの値が返され、キーと値のペアがアプリの構成に設定されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-233">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="90e98-234">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="90e98-234">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="90e98-235">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="90e98-235">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="90e98-236">スイッチは `-` または `--`で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-236">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="90e98-237">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="90e98-237">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="90e98-238">スイッチ マッピング ディクショナリを使用するには、それを `AddCommandLine` の呼び出しに渡します：</span><span class="sxs-lookup"><span data-stu-id="90e98-238">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="90e98-239">次のコードは、置換されたキーのキー値を示しています：</span><span class="sxs-lookup"><span data-stu-id="90e98-239">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-240">次のコマンドを実行して、キーの置換をテストできます：</span><span class="sxs-lookup"><span data-stu-id="90e98-240">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="90e98-241">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="90e98-241">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="90e98-242">`CreateDefaultBuilder` メソッドの `AddCommandLine` 呼び出しには、マップされたスイッチが含まれていないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="90e98-242">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90e98-243">解決策は、`CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="90e98-243">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="90e98-244">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="90e98-244">Hierarchical configuration data</span></span>

<span data-ttu-id="90e98-245">構成 API では、構成キーの区切り記号を使用して階層データをフラット化することにより、階層型の構成データの読み取りが行われます。</span><span class="sxs-lookup"><span data-stu-id="90e98-245">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="90e98-246">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *appsettings.json* ファイルが含まれます:</span><span class="sxs-lookup"><span data-stu-id="90e98-246">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="90e98-247">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="90e98-247">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-248">階層型の構成データを読み取る方法としては、オプション パターンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="90e98-248">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="90e98-249">詳細については、このドキュメント中の「[階層型の構成データをバインドする](#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-249">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="90e98-250"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-250"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="90e98-251">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="90e98-251">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="90e98-252">構成キーと値</span><span class="sxs-lookup"><span data-stu-id="90e98-252">Configuration keys and values</span></span>

<span data-ttu-id="90e98-253">構成キー:</span><span class="sxs-lookup"><span data-stu-id="90e98-253">Configuration keys:</span></span>

* <span data-ttu-id="90e98-254">構成キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-254">Are case-insensitive.</span></span> <span data-ttu-id="90e98-255">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="90e98-255">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="90e98-256">キーと値が複数の構成プロバイダーで設定されている場合は、最後に追加されたプロバイダーの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-256">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="90e98-257">詳細については、「[既定の構成](#default)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-257">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="90e98-258">階層キー</span><span class="sxs-lookup"><span data-stu-id="90e98-258">Hierarchical keys</span></span>
  * <span data-ttu-id="90e98-259">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="90e98-259">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="90e98-260">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-260">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="90e98-261">ダブル アンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロン `:` に自動で変換されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-261">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="90e98-262">Azure Key Vault では、階層型のキーは区切り記号に `--` を使用します。</span><span class="sxs-lookup"><span data-stu-id="90e98-262">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="90e98-263">シークレットがアプリの構成に読み込まれると、[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)によって `--` が `:` に自動的に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="90e98-263">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="90e98-264"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="90e98-264">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90e98-265">配列のバインドについては、「[配列をクラスにバインドする](#boa)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="90e98-265">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="90e98-266">構成値:</span><span class="sxs-lookup"><span data-stu-id="90e98-266">Configuration values:</span></span>

* <span data-ttu-id="90e98-267">構成値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="90e98-267">Are strings.</span></span>
* <span data-ttu-id="90e98-268">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="90e98-268">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="90e98-269">構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-269">Configuration providers</span></span>

<span data-ttu-id="90e98-270">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="90e98-270">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="90e98-271">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-271">Provider</span></span> | <span data-ttu-id="90e98-272">以下から構成を提供します</span><span class="sxs-lookup"><span data-stu-id="90e98-272">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="90e98-273">Azure Key Vault 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-273">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="90e98-274">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90e98-274">Azure Key Vault</span></span> |
| [<span data-ttu-id="90e98-275">Azure App Configuration プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-275">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="90e98-276">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="90e98-276">Azure App Configuration</span></span> |
| [<span data-ttu-id="90e98-277">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-277">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="90e98-278">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="90e98-278">Command-line parameters</span></span> |
| [<span data-ttu-id="90e98-279">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-279">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="90e98-280">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="90e98-280">Custom source</span></span> |
| [<span data-ttu-id="90e98-281">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-281">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="90e98-282">環境変数</span><span class="sxs-lookup"><span data-stu-id="90e98-282">Environment variables</span></span> |
| [<span data-ttu-id="90e98-283">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-283">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="90e98-284">INI、JSON、および XML ファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-284">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="90e98-285">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-285">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="90e98-286">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-286">Directory files</span></span> |
| [<span data-ttu-id="90e98-287">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-287">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="90e98-288">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="90e98-288">In-memory collections</span></span> |
| [<span data-ttu-id="90e98-289">ユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="90e98-289">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="90e98-290">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-290">File in the user profile directory</span></span> |

<span data-ttu-id="90e98-291">構成ソースは、構成プロバイダーで指定された順序で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90e98-291">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="90e98-292">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="90e98-292">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="90e98-293">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="90e98-293">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="90e98-294">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="90e98-294">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="90e98-295">ユーザー シークレット</span><span class="sxs-lookup"><span data-stu-id="90e98-295">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="90e98-296">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="90e98-296">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="90e98-297">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90e98-297">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="90e98-298">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするには、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="90e98-298">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="90e98-299">上記の一連のプロバイダーは、[既定の構成](#default)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-299">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="90e98-300">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="90e98-300">Connection string prefixes</span></span>

<span data-ttu-id="90e98-301">構成 API には、4つの接続文字列環境変数に対する特別なプロセスルールがあります。</span><span class="sxs-lookup"><span data-stu-id="90e98-301">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="90e98-302">これらの接続文字列は、アプリ環境用の Azure 接続文字列の構成に含まれています。</span><span class="sxs-lookup"><span data-stu-id="90e98-302">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="90e98-303">表に示されたプレフィックスを持つ環境変数は、[既定の構成](#default) を使用するとき、または `AddEnvironmentVariables` にプレフィックスが指定されていない場合に、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-303">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="90e98-304">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="90e98-304">Connection string prefix</span></span> | <span data-ttu-id="90e98-305">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-305">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="90e98-306">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-306">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="90e98-307">MySQL</span><span class="sxs-lookup"><span data-stu-id="90e98-307">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="90e98-308">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="90e98-308">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="90e98-309">SQL Server</span><span class="sxs-lookup"><span data-stu-id="90e98-309">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="90e98-310">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="90e98-310">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="90e98-311">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-311">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="90e98-312">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="90e98-312">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="90e98-313">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="90e98-313">Environment variable key</span></span> | <span data-ttu-id="90e98-314">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="90e98-314">Converted configuration key</span></span> | <span data-ttu-id="90e98-315">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="90e98-315">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90e98-316">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-316">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90e98-317">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90e98-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90e98-318">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="90e98-318">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90e98-319">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90e98-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90e98-320">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90e98-320">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90e98-321">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90e98-321">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90e98-322">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90e98-322">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="90e98-323">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-323">File configuration provider</span></span>

<span data-ttu-id="90e98-324"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="90e98-324"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="90e98-325">以下の構成プロバイダーは `FileConfigurationProvider` から派生したものです：</span><span class="sxs-lookup"><span data-stu-id="90e98-325">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="90e98-326">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-326">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="90e98-327">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-327">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="90e98-328">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-328">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="90e98-329">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-329">INI configuration provider</span></span>

<span data-ttu-id="90e98-330"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-330">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="90e98-331">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="90e98-331">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="90e98-332">上記のコードでは、*MyIniConfig.ini* と *MyIniConfig*.`Environment`.*ini* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="90e98-332">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="90e98-333">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-333">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="90e98-334">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="90e98-334">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90e98-335">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyIniConfig* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="90e98-335">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="90e98-336">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90e98-336">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="90e98-337">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-337">JSON configuration provider</span></span>

<span data-ttu-id="90e98-338"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、JSON ファイルのキーと値のペアから構成が読み込みまれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-338">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="90e98-339">オーバーロードでは、次の指定ができます：</span><span class="sxs-lookup"><span data-stu-id="90e98-339">Overloads can specify:</span></span>

* <span data-ttu-id="90e98-340">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="90e98-340">Whether the file is optional.</span></span>
* <span data-ttu-id="90e98-341">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="90e98-341">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="90e98-342">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="90e98-342">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="90e98-343">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="90e98-343">The preceding code:</span></span>

* <span data-ttu-id="90e98-344">次のオプションを使用して、*MyConfig.json* ファイルを読み込むように JSON 構成プロバイダーを構成します：</span><span class="sxs-lookup"><span data-stu-id="90e98-344">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="90e98-345">`optional: true`:ファイルは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="90e98-345">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="90e98-346">`reloadOnChange: true` は、次のとおりです。変更が保存されると、ファイルが再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-346">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="90e98-347">*MyConfig.json* ファイルの前に [既定の構成プロバイダー](#default)を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90e98-347">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="90e98-348">[環境変数構成プロバイダー](#evcp) および [コマンド ライン構成プロバイダー](#clcp)を含む、既定の構成プロバイダーでの *MyConfig.json* ファイルのオーバーライドの設定。</span><span class="sxs-lookup"><span data-stu-id="90e98-348">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90e98-349">通常は、[環境変数構成プロバイダー](#evcp)および [コマンドライン構成プロバイダー](#clcp)で設定されている値をオーバーライドするカスタム JSON ファイルは ***必要ありません***。</span><span class="sxs-lookup"><span data-stu-id="90e98-349">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90e98-350">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="90e98-350">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="90e98-351">上記のコードでは、*MyConfig.json* と *MyConfig*.`Environment`.*json* ファイルの設定は：</span><span class="sxs-lookup"><span data-stu-id="90e98-351">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="90e98-352">*appsettings.json* および *appsettings*.`Environment`.*json* ファイル内の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90e98-352">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="90e98-353">[環境変数の構成プロバイダー](#evcp)と[コマンドライン構成プロバイダー](#clcp)の設定によってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-353">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90e98-354">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *MyConfig.json* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="90e98-354">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="90e98-355">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90e98-355">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="90e98-356">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-356">XML configuration provider</span></span>

<span data-ttu-id="90e98-357"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-357">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="90e98-358">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="90e98-358">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="90e98-359">上記のコードでは、*MyXMLFile.xml* と *MyXMLFile*.`Environment`.*xml* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="90e98-359">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="90e98-360">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-360">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="90e98-361">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="90e98-361">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90e98-362">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyXMLFile.xml* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="90e98-362">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="90e98-363">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90e98-363">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-364">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="90e98-364">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="90e98-365">以下のコードでは、前の構成ファイルを読み取って、キーと値を表示します：</span><span class="sxs-lookup"><span data-stu-id="90e98-365">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-366">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-366">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="90e98-367">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-367">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90e98-368">key:attribute</span><span class="sxs-lookup"><span data-stu-id="90e98-368">key:attribute</span></span>
* <span data-ttu-id="90e98-369">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="90e98-369">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="90e98-370">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-370">Key-per-file configuration provider</span></span>

<span data-ttu-id="90e98-371"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-371">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="90e98-372">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="90e98-372">The key is the file name.</span></span> <span data-ttu-id="90e98-373">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-373">The value contains the file's contents.</span></span> <span data-ttu-id="90e98-374">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-374">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="90e98-375">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-375">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="90e98-376">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-376">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="90e98-377">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="90e98-378">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="90e98-378">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="90e98-379">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="90e98-379">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="90e98-380">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-380">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="90e98-381">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-381">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="90e98-382">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-382">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="90e98-383">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-383">Memory configuration provider</span></span>

<span data-ttu-id="90e98-384"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-384">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="90e98-385">次のコードでは、構成システムにメモリコ レクションが追加されています：</span><span class="sxs-lookup"><span data-stu-id="90e98-385">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="90e98-386">[サンプルのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の以下のコードでは、上記の構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="90e98-386">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-387">上記のコードでは、[既定の構成プロバイダー](#default)の後に `config.AddInMemoryCollection(Dict)` が追加されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-387">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="90e98-388">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="90e98-389">`MemoryConfigurationProvider` を使用した別の例については、[配列をバインド](#boa)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="90e98-390">Kestrel のエンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="90e98-390">Kestrel endpoint configuration</span></span>

<span data-ttu-id="90e98-391">Kestrel 固有のエンドポイント構成によって、すべての[サーバー間](xref:fundamentals/servers/index)のエンドポイント構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-391">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="90e98-392">サーバー間のエンドポイント構成には、次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="90e98-392">Cross-server endpoint configurations include:</span></span>

  * [<span data-ttu-id="90e98-393">UseUrls</span><span class="sxs-lookup"><span data-stu-id="90e98-393">UseUrls</span></span>](xref:fundamentals/host/web-host#server-urls)
  * <span data-ttu-id="90e98-394">[コマンド ライン](xref:fundamentals/configuration/index#command-line)での `--urls`</span><span class="sxs-lookup"><span data-stu-id="90e98-394">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="90e98-395">[環境変数](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="90e98-395">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="90e98-396">ASP.NET Core Web アプリで使用される次の *appsettings.json* ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="90e98-396">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="90e98-397">上の強調表示されているマークアップが ASP.NET Core Web アプリで使用され、"***かつ***" 次のサーバー間エンドポイント構成を使用してコマンド ラインでアプリが起動される場合:</span><span class="sxs-lookup"><span data-stu-id="90e98-397">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="90e98-398">Kestrel は、`https://localhost:7777` ではなく、 *appsettings.json* ファイルで Kestrel 専用に構成されたエンドポイント (`https://localhost:9999`) にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-398">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="90e98-399">環境変数として構成された Kestrel 固有のエンドポイントを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="90e98-399">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="90e98-400">上記の環境変数では、`Https` が Kestrel 固有のエンドポイントの名前です。</span><span class="sxs-lookup"><span data-stu-id="90e98-400">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="90e98-401">前の *appsettings.json* ファイルでも、`Https` という名前の Kestrel 固有のエンドポイントが定義されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-401">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="90e98-402">[既定](#default-configuration)で、[環境変数構成プロバイダー](#evcp)を使用している環境変数は *appsettings.* `Environment` *.json* の後に読み取られます。したがって、上記の環境変数は `Https` エンドポイント用に使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-402">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="90e98-403">GetValue</span><span class="sxs-lookup"><span data-stu-id="90e98-403">GetValue</span></span>

<span data-ttu-id="90e98-404">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを使用して、構成から単一の値を抽出し、それを指定した型に変換します：</span><span class="sxs-lookup"><span data-stu-id="90e98-404">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-405">上記のコードでは、`NumberKey` が構成中に見つからない場合には `99` の既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-405">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="90e98-406">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="90e98-406">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="90e98-407">以下の例では、次の *MySubsection.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="90e98-407">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="90e98-408">以下のコードでは、*MySubsection セクション* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="90e98-408">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="90e98-409">GetSection</span><span class="sxs-lookup"><span data-stu-id="90e98-409">GetSection</span></span>

<span data-ttu-id="90e98-410">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) は、指定されたサブセクションのキーを持つ構成サブセクションを返します。</span><span class="sxs-lookup"><span data-stu-id="90e98-410">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="90e98-411">次のコードは、`section1` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="90e98-411">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-412">次のコードは、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="90e98-412">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-413">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-413">`GetSection` never returns `null`.</span></span> <span data-ttu-id="90e98-414">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-414">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="90e98-415">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-415">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="90e98-416"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-416">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="90e98-417">GetChildren と Exists</span><span class="sxs-lookup"><span data-stu-id="90e98-417">GetChildren and Exists</span></span>

<span data-ttu-id="90e98-418">次のコードは、[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) を呼び出し、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="90e98-418">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-419">上記のコードは、[ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を呼び出し、セクションが存在することを確認します：</span><span class="sxs-lookup"><span data-stu-id="90e98-419">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="90e98-420">配列をバインドする</span><span class="sxs-lookup"><span data-stu-id="90e98-420">Bind an array</span></span>

<span data-ttu-id="90e98-421">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) は、構成キーの配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="90e98-421">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90e98-422">数値のキー セグメントを公開する配列形式は、すべて [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="90e98-422">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="90e98-423">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)の *MyArray.json* について考えます：</span><span class="sxs-lookup"><span data-stu-id="90e98-423">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="90e98-424">次のコードでは、*MyArray.json* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="90e98-424">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="90e98-425">次のコードでは、構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="90e98-425">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-426">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="90e98-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="90e98-427">上記の出力では、インデックス3の値が `value40` になります。これは *MyArray. json* の `"4": "value40",` に対応しています。</span><span class="sxs-lookup"><span data-stu-id="90e98-427">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="90e98-428">このバインドされた配列インデックスは連続的であり、構成キーインデックスにバインドされていません。</span><span class="sxs-lookup"><span data-stu-id="90e98-428">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="90e98-429">構成バインダーは、バインドされたオブジェクトに null 値をバインドしたり、null エントリを作成したりはできません</span><span class="sxs-lookup"><span data-stu-id="90e98-429">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="90e98-430">次のコードでは、<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドで `array:entries` 構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="90e98-430">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="90e98-431">次のコードでは、`arrayDict` `Dictionary` の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="90e98-431">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-432">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="90e98-432">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="90e98-433">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-433">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="90e98-434">配列を含む構成データがバインドされている場合、構成キーの配列インデックスは、オブジェクト作成時の構成データの反復のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-434">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="90e98-435">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-435">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="90e98-436">インデックス&num;3 に不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、インデックス&num;3のキーと値のペアを読み取る構成プロバイダーによってサプライできます。</span><span class="sxs-lookup"><span data-stu-id="90e98-436">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="90e98-437">サンプルダウンロードの、次の *Value3.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="90e98-437">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="90e98-438">次のコードには、*Value3.json* と `arrayDict` `Dictionary` の構成が含まれています：</span><span class="sxs-lookup"><span data-stu-id="90e98-438">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="90e98-439">次のコードは、上記の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="90e98-439">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-440">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="90e98-440">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="90e98-441">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-441">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="90e98-442">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-442">Custom configuration provider</span></span>

<span data-ttu-id="90e98-443">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="90e98-443">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="90e98-444">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="90e98-444">The provider has the following characteristics:</span></span>

* <span data-ttu-id="90e98-445">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-445">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="90e98-446">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-446">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="90e98-447">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90e98-447">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="90e98-448">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="90e98-448">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="90e98-449">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-449">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="90e98-450">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="90e98-450">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="90e98-451">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-451">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="90e98-452">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="90e98-452">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="90e98-453">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-453">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="90e98-454"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="90e98-454">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="90e98-455">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-455">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="90e98-456"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="90e98-456">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="90e98-457">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="90e98-457">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="90e98-458">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-458">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="90e98-459">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-459">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="90e98-460">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-460">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="90e98-461">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-461">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="90e98-462">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="90e98-462">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="90e98-463">起動時の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="90e98-463">Access configuration in Startup</span></span>

<span data-ttu-id="90e98-464">次のコードでは `Startup` メソッドの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="90e98-464">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="90e98-465">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90e98-465">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="90e98-466">Razor ページの構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="90e98-466">Access configuration in Razor Pages</span></span>

<span data-ttu-id="90e98-467">次のコードでは Razor ページの構成データが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90e98-467">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="90e98-468">次のコードでは、`MyOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="90e98-468">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="90e98-469">次のマークアップは、[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、オプションの値を解決して表示します。</span><span class="sxs-lookup"><span data-stu-id="90e98-469">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="90e98-470">MVC ビューファイルの構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="90e98-470">Access configuration in a MVC view file</span></span>

<span data-ttu-id="90e98-471">次のコードでは、MVC ビューの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="90e98-471">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="90e98-472">デリゲートでオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="90e98-472">Configure options with a delegate</span></span>

<span data-ttu-id="90e98-473">デリゲートで構成されたオプションは、構成プロバイダーで設定された値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90e98-473">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="90e98-474">サンプル アプリの例 2 では、デリゲートでオプションを構成しています。</span><span class="sxs-lookup"><span data-stu-id="90e98-474">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="90e98-475">次のコードでは、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-475">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="90e98-476">デリゲートを利用して `MyOptions` の値が構成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-476">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="90e98-477">このコードには、次のオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-477">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="90e98-478">先の例では、値 `Option1` と `Option2` が *appsettings.json* で指定されてから、構成されているデリゲートによりオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-478">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="90e98-479">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="90e98-479">Host versus app configuration</span></span>

<span data-ttu-id="90e98-480">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="90e98-480">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="90e98-481">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="90e98-481">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="90e98-482">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="90e98-482">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="90e98-483">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-483">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="90e98-484">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90e98-484">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="90e98-485">既定のホスト構成</span><span class="sxs-lookup"><span data-stu-id="90e98-485">Default host configuration</span></span>

<span data-ttu-id="90e98-486">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](?view=aspnetcore-2.2&preserve-view=true)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-486">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="90e98-487">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-487">Host configuration is provided from:</span></span>
  * <span data-ttu-id="90e98-488">[環境変数構成プロバイダー](#environment-variables)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="90e98-488">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="90e98-489">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-489">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="90e98-490">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90e98-490">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="90e98-491">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="90e98-491">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="90e98-492">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-492">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="90e98-493">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="90e98-493">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="90e98-494">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="90e98-494">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="90e98-495">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="90e98-495">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="90e98-496">その他の構成</span><span class="sxs-lookup"><span data-stu-id="90e98-496">Other configuration</span></span>

<span data-ttu-id="90e98-497">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="90e98-497">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="90e98-498">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-498">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="90e98-499">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-499">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="90e98-500"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="90e98-500">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="90e98-501">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="90e98-501">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="90e98-502">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-502">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="90e98-503">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-503">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="90e98-504">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90e98-504">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="90e98-505">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="90e98-505">Add configuration from an external assembly</span></span>

<span data-ttu-id="90e98-506"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="90e98-506">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="90e98-507">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-507">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90e98-508">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="90e98-508">Additional resources</span></span>

* [<span data-ttu-id="90e98-509">構成のソースコード</span><span class="sxs-lookup"><span data-stu-id="90e98-509">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90e98-510">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="90e98-510">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="90e98-511">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="90e98-511">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="90e98-512">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90e98-512">Azure Key Vault</span></span>
* <span data-ttu-id="90e98-513">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="90e98-513">Azure App Configuration</span></span>
* <span data-ttu-id="90e98-514">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="90e98-514">Command-line arguments</span></span>
* <span data-ttu-id="90e98-515">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="90e98-515">Custom providers (installed or created)</span></span>
* <span data-ttu-id="90e98-516">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-516">Directory files</span></span>
* <span data-ttu-id="90e98-517">環境変数</span><span class="sxs-lookup"><span data-stu-id="90e98-517">Environment variables</span></span>
* <span data-ttu-id="90e98-518">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="90e98-518">In-memory .NET objects</span></span>
* <span data-ttu-id="90e98-519">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-519">Settings files</span></span>

<span data-ttu-id="90e98-520">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-520">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="90e98-521">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="90e98-521">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="90e98-522">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="90e98-522">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="90e98-523">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="90e98-523">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="90e98-524">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-524">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="90e98-525">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="90e98-525">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="90e98-526">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="90e98-526">Host versus app configuration</span></span>

<span data-ttu-id="90e98-527">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="90e98-527">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="90e98-528">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="90e98-528">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="90e98-529">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="90e98-529">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="90e98-530">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-530">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="90e98-531">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90e98-531">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="90e98-532">その他の構成</span><span class="sxs-lookup"><span data-stu-id="90e98-532">Other configuration</span></span>

<span data-ttu-id="90e98-533">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="90e98-533">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="90e98-534">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-534">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="90e98-535">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-535">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="90e98-536"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="90e98-536">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="90e98-537">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="90e98-537">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="90e98-538">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-538">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="90e98-539">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90e98-539">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="90e98-540">既定の構成</span><span class="sxs-lookup"><span data-stu-id="90e98-540">Default configuration</span></span>

<span data-ttu-id="90e98-541">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-541">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="90e98-542">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-542">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="90e98-543">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-543">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="90e98-544">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-544">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="90e98-545">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-545">Host configuration is provided from:</span></span>
  * <span data-ttu-id="90e98-546">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="90e98-546">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="90e98-547">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-547">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="90e98-548">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90e98-548">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="90e98-549">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-549">App configuration is provided from:</span></span>
  * <span data-ttu-id="90e98-550">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="90e98-550">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="90e98-551">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="90e98-551">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="90e98-552">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90e98-552">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="90e98-553">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="90e98-553">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="90e98-554">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90e98-554">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="90e98-555">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="90e98-555">Security</span></span>

<span data-ttu-id="90e98-556">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="90e98-556">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="90e98-557">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="90e98-557">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="90e98-558">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="90e98-558">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="90e98-559">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-559">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="90e98-560">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-560">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="90e98-561"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-561"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="90e98-562">Secret Manager ツールでは、ファイル構成プロバイダーを使用して、ユーザー シークレットがローカル システム上の JSON ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-562">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="90e98-563">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="90e98-563">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="90e98-564">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-564">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="90e98-565">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-565">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="90e98-566">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="90e98-566">Hierarchical configuration data</span></span>

<span data-ttu-id="90e98-567">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-567">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="90e98-568">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="90e98-568">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="90e98-569">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-569">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="90e98-570">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-570">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="90e98-571">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-571">section0:key0</span></span>
* <span data-ttu-id="90e98-572">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-572">section0:key1</span></span>
* <span data-ttu-id="90e98-573">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-573">section1:key0</span></span>
* <span data-ttu-id="90e98-574">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-574">section1:key1</span></span>

<span data-ttu-id="90e98-575"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-575"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="90e98-576">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="90e98-576">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="90e98-577">規約</span><span class="sxs-lookup"><span data-stu-id="90e98-577">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="90e98-578">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-578">Sources and providers</span></span>

<span data-ttu-id="90e98-579">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90e98-579">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="90e98-580">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="90e98-580">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="90e98-581">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="90e98-581">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="90e98-582"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-582"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="90e98-583"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-583"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="90e98-584">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-584">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="90e98-585">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="90e98-585">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="90e98-586">キー</span><span class="sxs-lookup"><span data-stu-id="90e98-586">Keys</span></span>

<span data-ttu-id="90e98-587">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-587">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="90e98-588">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-588">Keys are case-insensitive.</span></span> <span data-ttu-id="90e98-589">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="90e98-589">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="90e98-590">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="90e98-590">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="90e98-591">重複する JSON キーの詳細については、「[こちらの GitHub のイシュー](https://github.com/dotnet/extensions/issues/2381)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-591">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="90e98-592">階層キー</span><span class="sxs-lookup"><span data-stu-id="90e98-592">Hierarchical keys</span></span>
  * <span data-ttu-id="90e98-593">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="90e98-593">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="90e98-594">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-594">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="90e98-595">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-595">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="90e98-596">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="90e98-596">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="90e98-597">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="90e98-597">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="90e98-598"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="90e98-598">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90e98-599">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="90e98-599">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="90e98-600">値</span><span class="sxs-lookup"><span data-stu-id="90e98-600">Values</span></span>

<span data-ttu-id="90e98-601">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-601">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="90e98-602">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="90e98-602">Values are strings.</span></span>
* <span data-ttu-id="90e98-603">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="90e98-603">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="90e98-604">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-604">Providers</span></span>

<span data-ttu-id="90e98-605">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="90e98-605">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="90e98-606">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-606">Provider</span></span> | <span data-ttu-id="90e98-607">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="90e98-607">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="90e98-608">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="90e98-608">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="90e98-609">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90e98-609">Azure Key Vault</span></span> |
| <span data-ttu-id="90e98-610">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="90e98-610">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="90e98-611">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="90e98-611">Azure App Configuration</span></span> |
| [<span data-ttu-id="90e98-612">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-612">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="90e98-613">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="90e98-613">Command-line parameters</span></span> |
| [<span data-ttu-id="90e98-614">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-614">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="90e98-615">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="90e98-615">Custom source</span></span> |
| [<span data-ttu-id="90e98-616">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-616">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="90e98-617">環境変数</span><span class="sxs-lookup"><span data-stu-id="90e98-617">Environment variables</span></span> |
| [<span data-ttu-id="90e98-618">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-618">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="90e98-619">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="90e98-619">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="90e98-620">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-620">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="90e98-621">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-621">Directory files</span></span> |
| [<span data-ttu-id="90e98-622">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-622">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="90e98-623">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="90e98-623">In-memory collections</span></span> |
| <span data-ttu-id="90e98-624">[ユーザー シークレット](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="90e98-624">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="90e98-625">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="90e98-625">File in the user profile directory</span></span> |

<span data-ttu-id="90e98-626">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90e98-626">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="90e98-627">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="90e98-627">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="90e98-628">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="90e98-628">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="90e98-629">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="90e98-629">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="90e98-630">ファイル ( *appsettings.json* 、*appsettings.{Environment}.json*。ここで、`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="90e98-630">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="90e98-631">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90e98-631">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="90e98-632">[ユーザー シークレット](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="90e98-632">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="90e98-633">環境変数</span><span class="sxs-lookup"><span data-stu-id="90e98-633">Environment variables</span></span>
1. <span data-ttu-id="90e98-634">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="90e98-634">Command-line arguments</span></span>

<span data-ttu-id="90e98-635">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="90e98-635">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="90e98-636">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-636">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90e98-637">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-637">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="90e98-638">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="90e98-638">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="90e98-639">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-639">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="90e98-640">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="90e98-640">ConfigureAppConfiguration</span></span>

<span data-ttu-id="90e98-641">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-641">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="90e98-642">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="90e98-642">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="90e98-643">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-643">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="90e98-644">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="90e98-644">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="90e98-645">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-645">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="90e98-646">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="90e98-646">Consume configuration during app startup</span></span>

<span data-ttu-id="90e98-647">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-647">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="90e98-648">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-648">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="90e98-649">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-649">Command-line Configuration Provider</span></span>

<span data-ttu-id="90e98-650"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-650">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="90e98-651">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-651">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90e98-652">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-652">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="90e98-653">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-653">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90e98-654">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-654">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90e98-655">*appsettings.json* および *appsettings.{Environment}.json* ファイルからの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="90e98-655">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="90e98-656">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90e98-656">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90e98-657">環境変数。</span><span class="sxs-lookup"><span data-stu-id="90e98-657">Environment variables.</span></span>

<span data-ttu-id="90e98-658">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="90e98-658">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="90e98-659">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-659">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="90e98-660">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="90e98-660">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="90e98-661">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-661">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="90e98-662">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="90e98-662">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90e98-663">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-663">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="90e98-664">**例**</span><span class="sxs-lookup"><span data-stu-id="90e98-664">**Example**</span></span>

<span data-ttu-id="90e98-665">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-665">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="90e98-666">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="90e98-666">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="90e98-667">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="90e98-667">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="90e98-668">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="90e98-668">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90e98-669">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="90e98-669">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="90e98-670">引数</span><span class="sxs-lookup"><span data-stu-id="90e98-670">Arguments</span></span>

<span data-ttu-id="90e98-671">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-671">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="90e98-672">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-672">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="90e98-673">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="90e98-673">Key prefix</span></span>               | <span data-ttu-id="90e98-674">例</span><span class="sxs-lookup"><span data-stu-id="90e98-674">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="90e98-675">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="90e98-675">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="90e98-676">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="90e98-676">Two dashes (`--`)</span></span>        | <span data-ttu-id="90e98-677">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="90e98-677">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="90e98-678">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="90e98-678">Forward slash (`/`)</span></span>      | <span data-ttu-id="90e98-679">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="90e98-679">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="90e98-680">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="90e98-680">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="90e98-681">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="90e98-681">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="90e98-682">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="90e98-682">Switch mappings</span></span>

<span data-ttu-id="90e98-683">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-683">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="90e98-684"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-684">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="90e98-685">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-685">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="90e98-686">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-686">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="90e98-687">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="90e98-687">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="90e98-688">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="90e98-688">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="90e98-689">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-689">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="90e98-690">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="90e98-690">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="90e98-691">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="90e98-691">Create a switch mappings dictionary.</span></span> <span data-ttu-id="90e98-692">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-692">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="90e98-693">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-693">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="90e98-694">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="90e98-694">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="90e98-695">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-695">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90e98-696">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-696">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="90e98-697">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-697">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="90e98-698">Key</span><span class="sxs-lookup"><span data-stu-id="90e98-698">Key</span></span>       | <span data-ttu-id="90e98-699">[値]</span><span class="sxs-lookup"><span data-stu-id="90e98-699">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="90e98-700">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="90e98-700">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="90e98-701">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-701">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="90e98-702">Key</span><span class="sxs-lookup"><span data-stu-id="90e98-702">Key</span></span>               | <span data-ttu-id="90e98-703">[値]</span><span class="sxs-lookup"><span data-stu-id="90e98-703">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="90e98-704">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-704">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="90e98-705"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-705">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="90e98-706">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-706">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="90e98-707">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-707">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="90e98-708">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-708">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="90e98-709">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-709">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="90e98-710">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-710">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90e98-711">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-711">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90e98-712">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="90e98-712">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="90e98-713">*appsettings.json* および *appsettings.{Environment}.json* ファイルからの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="90e98-713">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="90e98-714">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90e98-714">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90e98-715">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="90e98-715">Command-line arguments.</span></span>

<span data-ttu-id="90e98-716">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-716">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="90e98-717">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-717">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="90e98-718">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-718">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="90e98-719">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="90e98-719">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="90e98-720">**例**</span><span class="sxs-lookup"><span data-stu-id="90e98-720">**Example**</span></span>

<span data-ttu-id="90e98-721">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-721">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="90e98-722">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="90e98-722">Run the sample app.</span></span> <span data-ttu-id="90e98-723">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="90e98-723">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90e98-724">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="90e98-724">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="90e98-725">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="90e98-725">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="90e98-726">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-726">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="90e98-727">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-727">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="90e98-728">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="90e98-728">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="90e98-729">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="90e98-729">Prefixes</span></span>

<span data-ttu-id="90e98-730">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-730">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="90e98-731">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-731">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="90e98-732">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-732">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="90e98-733">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-733">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="90e98-734">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-734">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90e98-735">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="90e98-735">**Connection string prefixes**</span></span>

<span data-ttu-id="90e98-736">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-736">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="90e98-737">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-737">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="90e98-738">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="90e98-738">Connection string prefix</span></span> | <span data-ttu-id="90e98-739">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-739">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="90e98-740">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-740">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="90e98-741">MySQL</span><span class="sxs-lookup"><span data-stu-id="90e98-741">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="90e98-742">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="90e98-742">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="90e98-743">SQL Server</span><span class="sxs-lookup"><span data-stu-id="90e98-743">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="90e98-744">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="90e98-744">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="90e98-745">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-745">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="90e98-746">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="90e98-746">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="90e98-747">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="90e98-747">Environment variable key</span></span> | <span data-ttu-id="90e98-748">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="90e98-748">Converted configuration key</span></span> | <span data-ttu-id="90e98-749">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="90e98-749">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90e98-750">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-750">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90e98-751">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90e98-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90e98-752">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="90e98-752">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90e98-753">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90e98-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90e98-754">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90e98-754">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90e98-755">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90e98-755">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90e98-756">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90e98-756">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="90e98-757">**例**</span><span class="sxs-lookup"><span data-stu-id="90e98-757">**Example**</span></span>

<span data-ttu-id="90e98-758">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-758">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="90e98-759">名前: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="90e98-759">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="90e98-760">値: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="90e98-760">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="90e98-761">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90e98-761">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="90e98-762">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-762">File Configuration Provider</span></span>

<span data-ttu-id="90e98-763"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="90e98-763"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="90e98-764">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="90e98-764">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="90e98-765">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-765">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="90e98-766">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-766">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="90e98-767">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-767">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="90e98-768">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-768">INI Configuration Provider</span></span>

<span data-ttu-id="90e98-769"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-769">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="90e98-770">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-770">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90e98-771">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-771">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="90e98-772">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-772">Overloads permit specifying:</span></span>

* <span data-ttu-id="90e98-773">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="90e98-773">Whether the file is optional.</span></span>
* <span data-ttu-id="90e98-774">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="90e98-774">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90e98-775">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-775">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90e98-776">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-776">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90e98-777">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="90e98-777">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="90e98-778">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-778">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90e98-779">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-779">section0:key0</span></span>
* <span data-ttu-id="90e98-780">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-780">section0:key1</span></span>
* <span data-ttu-id="90e98-781">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="90e98-781">section1:subsection:key</span></span>
* <span data-ttu-id="90e98-782">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="90e98-782">section2:subsection0:key</span></span>
* <span data-ttu-id="90e98-783">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="90e98-783">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="90e98-784">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-784">JSON Configuration Provider</span></span>

<span data-ttu-id="90e98-785"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-785">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="90e98-786">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-786">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90e98-787">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-787">Overloads permit specifying:</span></span>

* <span data-ttu-id="90e98-788">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="90e98-788">Whether the file is optional.</span></span>
* <span data-ttu-id="90e98-789">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="90e98-789">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90e98-790">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-790">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90e98-791">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-791">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90e98-792">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-792">The method is called to load configuration from:</span></span>

* <span data-ttu-id="90e98-793">*appsettings.json* : このファイルは最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90e98-793">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="90e98-794">ファイルの環境バージョンを使用すると、 *appsettings.json* ファイルによって指定された値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="90e98-794">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="90e98-795">*appsettings.{Environment}.json*:ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-795">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="90e98-796">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-796">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90e98-797">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-797">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90e98-798">環境変数。</span><span class="sxs-lookup"><span data-stu-id="90e98-798">Environment variables.</span></span>
* <span data-ttu-id="90e98-799">開発環境での[ユーザー シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90e98-799">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90e98-800">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="90e98-800">Command-line arguments.</span></span>

<span data-ttu-id="90e98-801">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-801">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="90e98-802">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-802">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="90e98-803">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、 *appsettings.json* および *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90e98-804">**例**</span><span class="sxs-lookup"><span data-stu-id="90e98-804">**Example**</span></span>

<span data-ttu-id="90e98-805">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-805">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="90e98-806">`AddJsonFile` の最初の呼び出しを行うと、 *appsettings.json* から構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-806">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="90e98-807">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="90e98-807">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="90e98-808">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-808">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="90e98-809">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="90e98-809">Run the sample app.</span></span> <span data-ttu-id="90e98-810">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="90e98-810">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90e98-811">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="90e98-811">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="90e98-812">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="90e98-812">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="90e98-813">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="90e98-813">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="90e98-814">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="90e98-814">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="90e98-815">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="90e98-815">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="90e98-816">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="90e98-816">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="90e98-817">*appsettings.Development.json* 内の設定によって、 *appsettings.json* 内の設定はオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="90e98-817">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="90e98-818">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="90e98-818">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="90e98-819">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-819">XML Configuration Provider</span></span>

<span data-ttu-id="90e98-820"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-820">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="90e98-821">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-821">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90e98-822">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-822">Overloads permit specifying:</span></span>

* <span data-ttu-id="90e98-823">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="90e98-823">Whether the file is optional.</span></span>
* <span data-ttu-id="90e98-824">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="90e98-824">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90e98-825">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-825">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90e98-826">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-826">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="90e98-827">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="90e98-827">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="90e98-828">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90e98-829">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-829">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="90e98-830">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-830">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90e98-831">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-831">section0:key0</span></span>
* <span data-ttu-id="90e98-832">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-832">section0:key1</span></span>
* <span data-ttu-id="90e98-833">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-833">section1:key0</span></span>
* <span data-ttu-id="90e98-834">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-834">section1:key1</span></span>

<span data-ttu-id="90e98-835">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="90e98-835">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="90e98-836">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-836">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90e98-837">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-837">section:section0:key:key0</span></span>
* <span data-ttu-id="90e98-838">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-838">section:section0:key:key1</span></span>
* <span data-ttu-id="90e98-839">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-839">section:section1:key:key0</span></span>
* <span data-ttu-id="90e98-840">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-840">section:section1:key:key1</span></span>

<span data-ttu-id="90e98-841">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-841">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="90e98-842">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-842">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90e98-843">key:attribute</span><span class="sxs-lookup"><span data-stu-id="90e98-843">key:attribute</span></span>
* <span data-ttu-id="90e98-844">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="90e98-844">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="90e98-845">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-845">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="90e98-846"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-846">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="90e98-847">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="90e98-847">The key is the file name.</span></span> <span data-ttu-id="90e98-848">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-848">The value contains the file's contents.</span></span> <span data-ttu-id="90e98-849">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-849">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="90e98-850">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-850">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="90e98-851">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="90e98-851">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="90e98-852">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-852">Overloads permit specifying:</span></span>

* <span data-ttu-id="90e98-853">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="90e98-853">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="90e98-854">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="90e98-854">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="90e98-855">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-855">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="90e98-856">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-856">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="90e98-857">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-857">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="90e98-858">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-858">Memory Configuration Provider</span></span>

<span data-ttu-id="90e98-859"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-859">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="90e98-860">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-860">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90e98-861">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-861">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="90e98-862">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-862">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="90e98-863">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-863">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="90e98-864">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-864">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="90e98-865">GetValue</span><span class="sxs-lookup"><span data-stu-id="90e98-865">GetValue</span></span>

<span data-ttu-id="90e98-866">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを持つ構成から単一の値を抽出し、指定したコレクション以外の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="90e98-866">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="90e98-867">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="90e98-867">An overload accepts a default value.</span></span>

<span data-ttu-id="90e98-868">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="90e98-868">The following example:</span></span>

* <span data-ttu-id="90e98-869">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="90e98-869">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="90e98-870">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-870">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="90e98-871">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="90e98-871">Types the value as an `int`.</span></span>
* <span data-ttu-id="90e98-872">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="90e98-872">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="90e98-873">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="90e98-873">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="90e98-874">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="90e98-874">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="90e98-875">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="90e98-875">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="90e98-876">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-876">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="90e98-877">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-877">section0:key0</span></span>
* <span data-ttu-id="90e98-878">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-878">section0:key1</span></span>
* <span data-ttu-id="90e98-879">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-879">section1:key0</span></span>
* <span data-ttu-id="90e98-880">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-880">section1:key1</span></span>
* <span data-ttu-id="90e98-881">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-881">section2:subsection0:key0</span></span>
* <span data-ttu-id="90e98-882">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-882">section2:subsection0:key1</span></span>
* <span data-ttu-id="90e98-883">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="90e98-883">section2:subsection1:key0</span></span>
* <span data-ttu-id="90e98-884">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="90e98-884">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="90e98-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="90e98-885">GetSection</span></span>

<span data-ttu-id="90e98-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="90e98-887">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-887">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="90e98-888">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-888">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="90e98-889">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-889">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="90e98-890">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-890">`GetSection` never returns `null`.</span></span> <span data-ttu-id="90e98-891">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-891">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="90e98-892">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-892">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="90e98-893"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-893">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="90e98-894">GetChildren</span><span class="sxs-lookup"><span data-stu-id="90e98-894">GetChildren</span></span>

<span data-ttu-id="90e98-895">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-895">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="90e98-896">既存</span><span class="sxs-lookup"><span data-stu-id="90e98-896">Exists</span></span>

<span data-ttu-id="90e98-897">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="90e98-897">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="90e98-898">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="90e98-898">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="90e98-899">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="90e98-899">Bind to an object graph</span></span>

<span data-ttu-id="90e98-900"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="90e98-900"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="90e98-901">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-901">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="90e98-902">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="90e98-902">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="90e98-903">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-903">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="90e98-904">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-904">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="90e98-905">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="90e98-905">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="90e98-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="90e98-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="90e98-907">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="90e98-907">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="90e98-908">次のコードは、上記の例で `Get<T>` を使用する方法を示します：</span><span class="sxs-lookup"><span data-stu-id="90e98-908">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="90e98-909">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="90e98-909">Bind an array to a class</span></span>

<span data-ttu-id="90e98-910">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="90e98-910">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="90e98-911"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="90e98-911">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90e98-912">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="90e98-912">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="90e98-913">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-913">Binding is provided by convention.</span></span> <span data-ttu-id="90e98-914">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-914">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="90e98-915">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="90e98-915">**In-memory array processing**</span></span>

<span data-ttu-id="90e98-916">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="90e98-916">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="90e98-917">Key</span><span class="sxs-lookup"><span data-stu-id="90e98-917">Key</span></span>             | <span data-ttu-id="90e98-918">[値]</span><span class="sxs-lookup"><span data-stu-id="90e98-918">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="90e98-919">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="90e98-919">array:entries:0</span></span> | <span data-ttu-id="90e98-920">value0</span><span class="sxs-lookup"><span data-stu-id="90e98-920">value0</span></span> |
| <span data-ttu-id="90e98-921">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="90e98-921">array:entries:1</span></span> | <span data-ttu-id="90e98-922">value1</span><span class="sxs-lookup"><span data-stu-id="90e98-922">value1</span></span> |
| <span data-ttu-id="90e98-923">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="90e98-923">array:entries:2</span></span> | <span data-ttu-id="90e98-924">value2</span><span class="sxs-lookup"><span data-stu-id="90e98-924">value2</span></span> |
| <span data-ttu-id="90e98-925">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="90e98-925">array:entries:4</span></span> | <span data-ttu-id="90e98-926">value4</span><span class="sxs-lookup"><span data-stu-id="90e98-926">value4</span></span> |
| <span data-ttu-id="90e98-927">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="90e98-927">array:entries:5</span></span> | <span data-ttu-id="90e98-928">value5</span><span class="sxs-lookup"><span data-stu-id="90e98-928">value5</span></span> |

<span data-ttu-id="90e98-929">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-929">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="90e98-930">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="90e98-930">The array skips a value for index &num;3.</span></span> <span data-ttu-id="90e98-931">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="90e98-931">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="90e98-932">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-932">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="90e98-933">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="90e98-933">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="90e98-934">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用することもできます。これにより、コードがよりコンパクトになります：</span><span class="sxs-lookup"><span data-stu-id="90e98-934">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="90e98-935">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="90e98-935">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="90e98-936">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="90e98-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="90e98-937">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="90e98-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="90e98-938">0</span><span class="sxs-lookup"><span data-stu-id="90e98-938">0</span></span>                            | <span data-ttu-id="90e98-939">value0</span><span class="sxs-lookup"><span data-stu-id="90e98-939">value0</span></span>                       |
| <span data-ttu-id="90e98-940">1</span><span class="sxs-lookup"><span data-stu-id="90e98-940">1</span></span>                            | <span data-ttu-id="90e98-941">value1</span><span class="sxs-lookup"><span data-stu-id="90e98-941">value1</span></span>                       |
| <span data-ttu-id="90e98-942">2</span><span class="sxs-lookup"><span data-stu-id="90e98-942">2</span></span>                            | <span data-ttu-id="90e98-943">value2</span><span class="sxs-lookup"><span data-stu-id="90e98-943">value2</span></span>                       |
| <span data-ttu-id="90e98-944">3</span><span class="sxs-lookup"><span data-stu-id="90e98-944">3</span></span>                            | <span data-ttu-id="90e98-945">value4</span><span class="sxs-lookup"><span data-stu-id="90e98-945">value4</span></span>                       |
| <span data-ttu-id="90e98-946">4</span><span class="sxs-lookup"><span data-stu-id="90e98-946">4</span></span>                            | <span data-ttu-id="90e98-947">value5</span><span class="sxs-lookup"><span data-stu-id="90e98-947">value5</span></span>                       |

<span data-ttu-id="90e98-948">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-948">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="90e98-949">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-949">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="90e98-950">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="90e98-950">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="90e98-951">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-951">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="90e98-952">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="90e98-952">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="90e98-953">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="90e98-953">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="90e98-954">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="90e98-954">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="90e98-955">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-955">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="90e98-956">Key</span><span class="sxs-lookup"><span data-stu-id="90e98-956">Key</span></span>             | <span data-ttu-id="90e98-957">[値]</span><span class="sxs-lookup"><span data-stu-id="90e98-957">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="90e98-958">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="90e98-958">array:entries:3</span></span> | <span data-ttu-id="90e98-959">value3</span><span class="sxs-lookup"><span data-stu-id="90e98-959">value3</span></span> |

<span data-ttu-id="90e98-960">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="90e98-960">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="90e98-961">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="90e98-961">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="90e98-962">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="90e98-962">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="90e98-963">0</span><span class="sxs-lookup"><span data-stu-id="90e98-963">0</span></span>                            | <span data-ttu-id="90e98-964">value0</span><span class="sxs-lookup"><span data-stu-id="90e98-964">value0</span></span>                       |
| <span data-ttu-id="90e98-965">1</span><span class="sxs-lookup"><span data-stu-id="90e98-965">1</span></span>                            | <span data-ttu-id="90e98-966">value1</span><span class="sxs-lookup"><span data-stu-id="90e98-966">value1</span></span>                       |
| <span data-ttu-id="90e98-967">2</span><span class="sxs-lookup"><span data-stu-id="90e98-967">2</span></span>                            | <span data-ttu-id="90e98-968">value2</span><span class="sxs-lookup"><span data-stu-id="90e98-968">value2</span></span>                       |
| <span data-ttu-id="90e98-969">3</span><span class="sxs-lookup"><span data-stu-id="90e98-969">3</span></span>                            | <span data-ttu-id="90e98-970">value3</span><span class="sxs-lookup"><span data-stu-id="90e98-970">value3</span></span>                       |
| <span data-ttu-id="90e98-971">4</span><span class="sxs-lookup"><span data-stu-id="90e98-971">4</span></span>                            | <span data-ttu-id="90e98-972">value4</span><span class="sxs-lookup"><span data-stu-id="90e98-972">value4</span></span>                       |
| <span data-ttu-id="90e98-973">5</span><span class="sxs-lookup"><span data-stu-id="90e98-973">5</span></span>                            | <span data-ttu-id="90e98-974">value5</span><span class="sxs-lookup"><span data-stu-id="90e98-974">value5</span></span>                       |

<span data-ttu-id="90e98-975">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="90e98-975">**JSON array processing**</span></span>

<span data-ttu-id="90e98-976">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-976">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="90e98-977">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="90e98-977">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="90e98-978">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90e98-978">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="90e98-979">Key</span><span class="sxs-lookup"><span data-stu-id="90e98-979">Key</span></span>                     | <span data-ttu-id="90e98-980">[値]</span><span class="sxs-lookup"><span data-stu-id="90e98-980">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="90e98-981">json_array:key</span><span class="sxs-lookup"><span data-stu-id="90e98-981">json_array:key</span></span>          | <span data-ttu-id="90e98-982">valueA</span><span class="sxs-lookup"><span data-stu-id="90e98-982">valueA</span></span> |
| <span data-ttu-id="90e98-983">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="90e98-983">json_array:subsection:0</span></span> | <span data-ttu-id="90e98-984">valueB</span><span class="sxs-lookup"><span data-stu-id="90e98-984">valueB</span></span> |
| <span data-ttu-id="90e98-985">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="90e98-985">json_array:subsection:1</span></span> | <span data-ttu-id="90e98-986">valueC</span><span class="sxs-lookup"><span data-stu-id="90e98-986">valueC</span></span> |
| <span data-ttu-id="90e98-987">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="90e98-987">json_array:subsection:2</span></span> | <span data-ttu-id="90e98-988">valueD</span><span class="sxs-lookup"><span data-stu-id="90e98-988">valueD</span></span> |

<span data-ttu-id="90e98-989">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-989">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="90e98-990">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="90e98-990">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="90e98-991">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-991">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="90e98-992">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="90e98-992">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="90e98-993">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="90e98-993">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="90e98-994">0</span><span class="sxs-lookup"><span data-stu-id="90e98-994">0</span></span>                                   | <span data-ttu-id="90e98-995">valueB</span><span class="sxs-lookup"><span data-stu-id="90e98-995">valueB</span></span>                              |
| <span data-ttu-id="90e98-996">1</span><span class="sxs-lookup"><span data-stu-id="90e98-996">1</span></span>                                   | <span data-ttu-id="90e98-997">valueC</span><span class="sxs-lookup"><span data-stu-id="90e98-997">valueC</span></span>                              |
| <span data-ttu-id="90e98-998">2</span><span class="sxs-lookup"><span data-stu-id="90e98-998">2</span></span>                                   | <span data-ttu-id="90e98-999">valueD</span><span class="sxs-lookup"><span data-stu-id="90e98-999">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="90e98-1000">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90e98-1000">Custom configuration provider</span></span>

<span data-ttu-id="90e98-1001">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1001">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="90e98-1002">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="90e98-1002">The provider has the following characteristics:</span></span>

* <span data-ttu-id="90e98-1003">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="90e98-1003">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="90e98-1004">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1004">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="90e98-1005">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90e98-1005">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="90e98-1006">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="90e98-1006">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="90e98-1007">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="90e98-1007">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="90e98-1008">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1008">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="90e98-1009">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-1009">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="90e98-1010">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1010">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="90e98-1011">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-1011">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="90e98-1012"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1012">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="90e98-1013">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-1013">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="90e98-1014"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1014">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="90e98-1015">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1015">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="90e98-1016">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-1016">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="90e98-1017">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="90e98-1017">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="90e98-1018">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="90e98-1018">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="90e98-1019">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1019">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="90e98-1020">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="90e98-1020">Access configuration during startup</span></span>

<span data-ttu-id="90e98-1021">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="90e98-1021">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="90e98-1022">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1022">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="90e98-1023">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90e98-1023">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="90e98-1024">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="90e98-1024">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="90e98-1025">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) 名前空間に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="90e98-1025">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="90e98-1026">Razor Pages ページで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="90e98-1026">In a Razor Pages page:</span></span>

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

<span data-ttu-id="90e98-1027">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="90e98-1027">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="90e98-1028">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="90e98-1028">Add configuration from an external assembly</span></span>

<span data-ttu-id="90e98-1029"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="90e98-1029">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="90e98-1030">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90e98-1030">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90e98-1031">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="90e98-1031">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

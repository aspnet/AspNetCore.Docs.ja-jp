---
title: ASP.NET Core の構成
author: rick-anderson
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 9e744ec6d0f0dd72bded8284e98fd9ce53056b84
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057974"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="90aee-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="90aee-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="90aee-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="90aee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90aee-105">ASP.NET Core の構成は、1つまたは複数の[構成プロバイダー](#cp)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="90aee-106">構成プロバイダーは、以下のようなさまざまな構成ソースを使用して、キーと値のペアから構成データを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="90aee-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="90aee-107">*:::no-loc(appsettings.json):::* などの設定ファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-107">Settings files, such as *:::no-loc(appsettings.json):::*</span></span>
* <span data-ttu-id="90aee-108">環境変数</span><span class="sxs-lookup"><span data-stu-id="90aee-108">Environment variables</span></span>
* <span data-ttu-id="90aee-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90aee-109">Azure Key Vault</span></span>
* <span data-ttu-id="90aee-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="90aee-110">Azure App Configuration</span></span>
* <span data-ttu-id="90aee-111">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="90aee-111">Command-line arguments</span></span>
* <span data-ttu-id="90aee-112">インストール済みまたは作成済みのカスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="90aee-113">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-113">Directory files</span></span>
* <span data-ttu-id="90aee-114">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="90aee-114">In-memory .NET objects</span></span>

<span data-ttu-id="90aee-115">このトピックでは ASP.NET Core の構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="90aee-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="90aee-116">コンソール アプリでの構成の使用方法について詳しくは、[.NET 構成](/dotnet/core/extensions/configuration)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90aee-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="90aee-117">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="90aee-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="90aee-118">既定の構成</span><span class="sxs-lookup"><span data-stu-id="90aee-118">Default configuration</span></span>

<span data-ttu-id="90aee-119">[dotnet new](/dotnet/core/tools/dotnet-new) または Visual Studio で作成された ASP.NET Core の web アプリが、次のコードを生成します:</span><span class="sxs-lookup"><span data-stu-id="90aee-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="90aee-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="90aee-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) は:既存の `IConfiguration` をソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="90aee-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="90aee-122">既定の構成では、 [ホスト](#hvac)構成を追加し、 _アプリ_ 構成の最初のソースとして設定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="90aee-123">[JSON 構成プロバイダー](#file-configuration-provider)を使用する [:::no-loc(appsettings.json):::](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="90aee-123">[:::no-loc(appsettings.json):::](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="90aee-124">[JSON 構成プロバイダー](#file-configuration-provider)を使用する *appsettings.* `Environment`*json* 。</span><span class="sxs-lookup"><span data-stu-id="90aee-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="90aee-125">たとえば、 *appsettings*. ***Production\*\*_._json* および *appsettings*.\*\*\*Development** _._json\*。</span><span class="sxs-lookup"><span data-stu-id="90aee-125">For example, *appsettings*. ***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="90aee-126">`Development` 環境でアプリが実行される際の [App シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90aee-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="90aee-127">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="90aee-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="90aee-128">[コマンドライン構成プロバイダー](#command-line)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90aee-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="90aee-129">後から追加される構成プロバイダーは、それ以前のキー設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90aee-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="90aee-130">たとえば、`MyKey` が *:::no-loc(appsettings.json):::* と環境の両方で設定されている場合、環境の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-130">For example, if `MyKey` is set in both *:::no-loc(appsettings.json):::* and the environment, the environment value is used.</span></span> <span data-ttu-id="90aee-131">既定の構成プロバイダーを使用すると、[コマンドライン構成プロバイダー](#clcp) が他のすべてのプロバイダーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90aee-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="90aee-132">`CreateDefaultBuilder` の詳細については、[既定のビルダー設定](xref:fundamentals/host/generic-host#default-builder-settings)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="90aee-133">以下のコードでは、追加した順に、有効な構成プロバイダーが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90aee-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### :::no-loc(appsettings.json):::

<span data-ttu-id="90aee-134">次の *:::no-loc(appsettings.json):::* ファイルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="90aee-134">Consider the following *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="90aee-135">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90aee-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-136">既定の <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> は、以下の順序で構成を読み込みます:</span><span class="sxs-lookup"><span data-stu-id="90aee-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *:::no-loc(appsettings.json):::*
1. <span data-ttu-id="90aee-137">*appsettings.* `Environment` *.json* :たとえば、 *appsettings*. ***Production\*\*_._json* および *appsettings*.\*\*\*Development** _._json\* ファイル。</span><span class="sxs-lookup"><span data-stu-id="90aee-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*. ***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="90aee-138">ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="90aee-139">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="90aee-140">*appsettings*.`Environment`. *json* の値によって、 *:::no-loc(appsettings.json):::* 内のキーがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-140">*appsettings*.`Environment`. *json* values override keys in *:::no-loc(appsettings.json):::*.</span></span> <span data-ttu-id="90aee-141">たとえば、既定では次のようになります:</span><span class="sxs-lookup"><span data-stu-id="90aee-141">For example, by default:</span></span>

* <span data-ttu-id="90aee-142">開発中は、 *appsettings*.\* **Development** _._json\* 構成によって、 *:::no-loc(appsettings.json):::* で見つかった値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-142">In development, *appsettings*.\* **Development** _._json\* configuration overwrites values found in *:::no-loc(appsettings.json):::*.</span></span>
* <span data-ttu-id="90aee-143">運用環境では、 *appsettings*.\* **Production** _._json\* 構成によって、 *:::no-loc(appsettings.json):::* で見つかった値が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-143">In production, *appsettings*.\* **Production** _._json\* configuration overwrites values found in *:::no-loc(appsettings.json):::*.</span></span> <span data-ttu-id="90aee-144">たとえば、Azure にアプリをデプロイする場合。</span><span class="sxs-lookup"><span data-stu-id="90aee-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="90aee-145">オプションパターンを使用して、階層型の構成データをバインドします</span><span class="sxs-lookup"><span data-stu-id="90aee-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="90aee-146">[既定](#default)の構成を利用する場合、 [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) で *:::no-loc(appsettings.json):::* と *appsettings.* `Environment` *.json* ファイルを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-146">Using the [default](#default) configuration, the *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="90aee-147">アプリの開始\* **後** _に *:::no-loc(appsettings.json):::* と *appsettings.* `Environment` *.json* ファイルに加えられた変更は、[JSON 構成プロバイダー](#jcp) によって読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90aee-147">Changes made to the *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* file \* **after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="90aee-148">追加の JSON 構成ファイルを追加する方法の詳細については、このドキュメント中の「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="90aee-149">サービス コレクションの結合</span><span class="sxs-lookup"><span data-stu-id="90aee-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="90aee-150">セキュリティとシークレット マネージャー</span><span class="sxs-lookup"><span data-stu-id="90aee-150">Security and secret manager</span></span>

<span data-ttu-id="90aee-151">構成データのガイドライン:</span><span class="sxs-lookup"><span data-stu-id="90aee-151">Configuration data guidelines:</span></span>

<span data-ttu-id="90aee-152">_ 構成プロバイダーのコード内、またはプレーンテキストの構成ファイル内には、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="90aee-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="90aee-153">[シークレット マネージャー](xref:security/app-secrets) を使用すると、開発時にシークレットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="90aee-154">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="90aee-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="90aee-155">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="90aee-156">[既定](#default)では、 [シークレット マネージャー](xref:security/app-secrets)によって、構成設定が、 *:::no-loc(appsettings.json):::* および *appsettings.* `Environment` *.json* の後に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90aee-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="90aee-157">パスワードその他の機密データの格納については、次を参照してください：</span><span class="sxs-lookup"><span data-stu-id="90aee-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="90aee-158"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="90aee-159">シークレット マネージャーでは、[ファイル構成プロバイダー](#fcp)を使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="90aee-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="90aee-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="90aee-161">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="90aee-162">環境変数</span><span class="sxs-lookup"><span data-stu-id="90aee-162">Environment variables</span></span>

<span data-ttu-id="90aee-163">[既定](#default)の構成を使用すると、<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> によって、 *:::no-loc(appsettings.json):::* 、 *appsettings.* `Environment` *.json* 、および[シークレット マネージャー](xref:security/app-secrets)の読み取り後に、環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *:::no-loc(appsettings.json):::* , *appsettings.*`Environment`*.json* , and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="90aee-164">そのため、環境から読み取られたキー値によって、 *:::no-loc(appsettings.json):::* 、 *appsettings.* `Environment` *.json* 、およびシークレット マネージャーから読み取られた値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-164">Therefore, key values read from the environment override values read from *:::no-loc(appsettings.json):::* , *appsettings.*`Environment`*.json* , and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="90aee-165">次の `set` コマンドは：</span><span class="sxs-lookup"><span data-stu-id="90aee-165">The following `set` commands:</span></span>

* <span data-ttu-id="90aee-166">Windows で[ 上記の例 ](#appsettingsjson)の環境キーと値を設定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="90aee-167">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)を使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="90aee-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="90aee-168">`dotnet run` コマンドは、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="90aee-169">上記の環境設定は：</span><span class="sxs-lookup"><span data-stu-id="90aee-169">The preceding environment settings:</span></span>

* <span data-ttu-id="90aee-170">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="90aee-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="90aee-171">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="90aee-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="90aee-172">次の [setx](/windows-server/administration/windows-commands/setx) コマンドで、Windows 上の環境キーと値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="90aee-173">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="90aee-174">`/M` は、システム環境で変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="90aee-175">`/M` スイッチが使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="90aee-176">上記のコマンドによって、 *:::no-loc(appsettings.json):::* および *appsettings.* `Environment` *.json* がオーバーライドされるのをテストするには:</span><span class="sxs-lookup"><span data-stu-id="90aee-176">To test that the preceding commands override *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* :</span></span>

* <span data-ttu-id="90aee-177">Visual Studio の場合:Visual Studio を終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="90aee-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="90aee-178">CLI の場合:新しいコマンド ウィンドウを起動し、`dotnet run` を入力します。</span><span class="sxs-lookup"><span data-stu-id="90aee-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="90aee-179">環境変数のプレフィックスを指定する文字列を指定して <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します：</span><span class="sxs-lookup"><span data-stu-id="90aee-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="90aee-180">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="90aee-180">In the preceding code:</span></span>

* <span data-ttu-id="90aee-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` は[既定の構成プロバイダー](#default)の後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="90aee-182">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="90aee-183">`MyCustomPrefix_` プレフィックスを使用して設定された環境変数は、[既定の構成プロバイダー](#default)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90aee-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="90aee-184">これには、プレフィックスのない環境変数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="90aee-185">構成のキーと値のペアの読み取り時に、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="90aee-186">次のコマンドは、カスタム プレフィックスをテストします：</span><span class="sxs-lookup"><span data-stu-id="90aee-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="90aee-187">[既定の構成](#default)では、`DOTNET_` と `ASPNETCORE_` のプレフィックスが付いた環境変数とコマンド ライン引数を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="90aee-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="90aee-188">`DOTNET_` と `ASPNETCORE_` のプレフィックスは ASP.NET Core によって[ホストとアプリの構成](xref:fundamentals/host/generic-host#host-configuration)に使用されますが、ユーザーの構成には使用されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="90aee-189">ホストとアプリの構成の詳細については、「[.NET 汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="90aee-190">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの **新しいアプリケーション設定** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90aee-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="90aee-191">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="90aee-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="90aee-192">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="90aee-193">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-193">Exposed as environment variables.</span></span>

<span data-ttu-id="90aee-194">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="90aee-195">Azure データベース接続文字列の詳細については、「[接続文字列のプレフィックス](#constr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="90aee-196">launchSettings.json で設定された環境変数</span><span class="sxs-lookup"><span data-stu-id="90aee-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="90aee-197">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="90aee-198">コマンド ライン</span><span class="sxs-lookup"><span data-stu-id="90aee-198">Command-line</span></span>

<span data-ttu-id="90aee-199">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> は、以下の構成ソースの後にコマンド ライン引数のキーと値のペアから構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="90aee-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="90aee-200">*:::no-loc(appsettings.json):::* および *appsettings*.`Environment`. *json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="90aee-200">*:::no-loc(appsettings.json):::* and *appsettings*.`Environment`. *json* files.</span></span>
* <span data-ttu-id="90aee-201">開発環境の [App シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90aee-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90aee-202">環境変数。</span><span class="sxs-lookup"><span data-stu-id="90aee-202">Environment variables.</span></span>

<span data-ttu-id="90aee-203">[既定](#default)では、コマンド ラインで設定された構成値は、他のすべての構成プロバイダーで設定された構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90aee-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="90aee-204">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="90aee-204">Command-line arguments</span></span>

<span data-ttu-id="90aee-205">次のコマンドは `=` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="90aee-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="90aee-206">次のコマンドは `/` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="90aee-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="90aee-207">次のコマンドは `--` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="90aee-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="90aee-208">キーの値:</span><span class="sxs-lookup"><span data-stu-id="90aee-208">The key value:</span></span>

* <span data-ttu-id="90aee-209">`=` の後に続ける必要があります。または、値がスペースの後にある場合は、キーのプレフィックスが `--` または `/` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="90aee-210">`=` を使用する場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="90aee-211">たとえば、`MySetting=` のようにします。</span><span class="sxs-lookup"><span data-stu-id="90aee-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="90aee-212">同じコマンド内では、`=` を使用するコマンド ライン引数のキーと値のペアを、スペースを使用するキーと値のペアと混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="90aee-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="90aee-213">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="90aee-213">Switch mappings</span></span>

<span data-ttu-id="90aee-214">スイッチ マッピングでは、 **キー** 名の置換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="90aee-215"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換するディクショナリを提供します。</span><span class="sxs-lookup"><span data-stu-id="90aee-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="90aee-216">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="90aee-217">ディクショナリ中にコマンド ライン キーが見つかった場合は、そのディクショナリの値が返され、キーと値のペアがアプリの構成に設定されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="90aee-218">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="90aee-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="90aee-219">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="90aee-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="90aee-220">スイッチは `-` または `--`で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="90aee-221">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="90aee-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="90aee-222">スイッチ マッピング ディクショナリを使用するには、それを `AddCommandLine` の呼び出しに渡します：</span><span class="sxs-lookup"><span data-stu-id="90aee-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="90aee-223">次のコードは、置換されたキーのキー値を示しています：</span><span class="sxs-lookup"><span data-stu-id="90aee-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-224">次のコマンドを実行して、キーの置換をテストできます：</span><span class="sxs-lookup"><span data-stu-id="90aee-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="90aee-225">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="90aee-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="90aee-226">`CreateDefaultBuilder` メソッドの `AddCommandLine` 呼び出しには、マップされたスイッチが含まれていないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="90aee-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90aee-227">解決策は、`CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="90aee-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="90aee-228">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="90aee-228">Hierarchical configuration data</span></span>

<span data-ttu-id="90aee-229">構成 API では、構成キーの区切り記号を使用して階層データをフラット化することにより、階層型の構成データの読み取りが行われます。</span><span class="sxs-lookup"><span data-stu-id="90aee-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="90aee-230">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *:::no-loc(appsettings.json):::* ファイルが含まれます:</span><span class="sxs-lookup"><span data-stu-id="90aee-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="90aee-231">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="90aee-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-232">階層型の構成データを読み取る方法としては、オプション パターンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="90aee-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="90aee-233">詳細については、このドキュメント中の「[階層型の構成データをバインドする](#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="90aee-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="90aee-235">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="90aee-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="90aee-236">構成キーと値</span><span class="sxs-lookup"><span data-stu-id="90aee-236">Configuration keys and values</span></span>

<span data-ttu-id="90aee-237">構成キー:</span><span class="sxs-lookup"><span data-stu-id="90aee-237">Configuration keys:</span></span>

* <span data-ttu-id="90aee-238">構成キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-238">Are case-insensitive.</span></span> <span data-ttu-id="90aee-239">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="90aee-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="90aee-240">キーと値が複数の構成プロバイダーで設定されている場合は、最後に追加されたプロバイダーの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="90aee-241">詳細については、「[既定の構成](#default)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="90aee-242">階層キー</span><span class="sxs-lookup"><span data-stu-id="90aee-242">Hierarchical keys</span></span>
  * <span data-ttu-id="90aee-243">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="90aee-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="90aee-244">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="90aee-245">ダブル アンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロン `:` に自動で変換されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="90aee-246">Azure Key Vault では、階層型のキーは区切り記号に `--` を使用します。</span><span class="sxs-lookup"><span data-stu-id="90aee-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="90aee-247">シークレットがアプリの構成に読み込まれると、[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)によって `--` が `:` に自動的に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="90aee-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="90aee-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="90aee-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90aee-249">配列のバインドについては、「[配列をクラスにバインドする](#boa)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="90aee-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="90aee-250">構成値:</span><span class="sxs-lookup"><span data-stu-id="90aee-250">Configuration values:</span></span>

* <span data-ttu-id="90aee-251">構成値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="90aee-251">Are strings.</span></span>
* <span data-ttu-id="90aee-252">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="90aee-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="90aee-253">構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-253">Configuration providers</span></span>

<span data-ttu-id="90aee-254">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="90aee-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="90aee-255">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-255">Provider</span></span> | <span data-ttu-id="90aee-256">以下から構成を提供します</span><span class="sxs-lookup"><span data-stu-id="90aee-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="90aee-257">Azure Key Vault 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="90aee-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90aee-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="90aee-259">Azure App Configuration プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="90aee-260">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="90aee-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="90aee-261">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="90aee-262">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="90aee-262">Command-line parameters</span></span> |
| [<span data-ttu-id="90aee-263">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="90aee-264">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="90aee-264">Custom source</span></span> |
| [<span data-ttu-id="90aee-265">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="90aee-266">環境変数</span><span class="sxs-lookup"><span data-stu-id="90aee-266">Environment variables</span></span> |
| [<span data-ttu-id="90aee-267">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="90aee-268">INI、JSON、および XML ファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="90aee-269">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="90aee-270">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-270">Directory files</span></span> |
| [<span data-ttu-id="90aee-271">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="90aee-272">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="90aee-272">In-memory collections</span></span> |
| [<span data-ttu-id="90aee-273">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="90aee-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="90aee-274">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-274">File in the user profile directory</span></span> |

<span data-ttu-id="90aee-275">構成ソースは、構成プロバイダーで指定された順序で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90aee-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="90aee-276">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="90aee-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="90aee-277">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="90aee-277">A typical sequence of configuration providers is:</span></span>

1. *:::no-loc(appsettings.json):::*
1. <span data-ttu-id="90aee-278">*appsettings*.`Environment`. *json*</span><span class="sxs-lookup"><span data-stu-id="90aee-278">*appsettings*.`Environment`. *json*</span></span>
1. [<span data-ttu-id="90aee-279">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="90aee-279">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="90aee-280">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="90aee-280">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="90aee-281">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90aee-281">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="90aee-282">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするには、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="90aee-282">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="90aee-283">上記の一連のプロバイダーは、[既定の構成](#default)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-283">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="90aee-284">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="90aee-284">Connection string prefixes</span></span>

<span data-ttu-id="90aee-285">構成 API には、4つの接続文字列環境変数に対する特別なプロセスルールがあります。</span><span class="sxs-lookup"><span data-stu-id="90aee-285">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="90aee-286">これらの接続文字列は、アプリ環境用の Azure 接続文字列の構成に含まれています。</span><span class="sxs-lookup"><span data-stu-id="90aee-286">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="90aee-287">表に示されたプレフィックスを持つ環境変数は、[既定の構成](#default) を使用するとき、または `AddEnvironmentVariables` にプレフィックスが指定されていない場合に、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-287">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="90aee-288">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="90aee-288">Connection string prefix</span></span> | <span data-ttu-id="90aee-289">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-289">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="90aee-290">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-290">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="90aee-291">MySQL</span><span class="sxs-lookup"><span data-stu-id="90aee-291">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="90aee-292">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="90aee-292">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="90aee-293">SQL Server</span><span class="sxs-lookup"><span data-stu-id="90aee-293">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="90aee-294">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="90aee-294">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="90aee-295">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-295">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="90aee-296">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="90aee-296">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="90aee-297">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="90aee-297">Environment variable key</span></span> | <span data-ttu-id="90aee-298">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="90aee-298">Converted configuration key</span></span> | <span data-ttu-id="90aee-299">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="90aee-299">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90aee-300">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-300">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90aee-301">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90aee-301">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90aee-302">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="90aee-302">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90aee-303">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90aee-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90aee-304">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90aee-304">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90aee-305">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90aee-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90aee-306">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90aee-306">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="90aee-307">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-307">File configuration provider</span></span>

<span data-ttu-id="90aee-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="90aee-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="90aee-309">以下の構成プロバイダーは `FileConfigurationProvider` から派生したものです：</span><span class="sxs-lookup"><span data-stu-id="90aee-309">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="90aee-310">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-310">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="90aee-311">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-311">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="90aee-312">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-312">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="90aee-313">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-313">INI configuration provider</span></span>

<span data-ttu-id="90aee-314"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-314">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="90aee-315">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="90aee-315">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="90aee-316">上記のコードでは、 *MyIniConfig.ini* と *MyIniConfig*.`Environment`. *ini* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="90aee-316">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`. *ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="90aee-317">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-317">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="90aee-318">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="90aee-318">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90aee-319">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyIniConfig* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="90aee-319">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="90aee-320">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90aee-320">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="90aee-321">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-321">JSON configuration provider</span></span>

<span data-ttu-id="90aee-322"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、JSON ファイルのキーと値のペアから構成が読み込みまれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-322">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="90aee-323">オーバーロードでは、次の指定ができます：</span><span class="sxs-lookup"><span data-stu-id="90aee-323">Overloads can specify:</span></span>

* <span data-ttu-id="90aee-324">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="90aee-324">Whether the file is optional.</span></span>
* <span data-ttu-id="90aee-325">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="90aee-325">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="90aee-326">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="90aee-326">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="90aee-327">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="90aee-327">The preceding code:</span></span>

* <span data-ttu-id="90aee-328">次のオプションを使用して、 *MyConfig.json* ファイルを読み込むように JSON 構成プロバイダーを構成します：</span><span class="sxs-lookup"><span data-stu-id="90aee-328">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="90aee-329">`optional: true`:ファイルは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="90aee-329">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="90aee-330">`reloadOnChange: true` は、次のとおりです。変更が保存されると、ファイルが再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-330">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="90aee-331">*MyConfig.json* ファイルの前に [既定の構成プロバイダー](#default)を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90aee-331">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="90aee-332">[環境変数構成プロバイダー](#evcp) および [コマンド ライン構成プロバイダー](#clcp)を含む、既定の構成プロバイダーでの *MyConfig.json* ファイルのオーバーライドの設定。</span><span class="sxs-lookup"><span data-stu-id="90aee-332">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90aee-333">通常は、 [環境変数構成プロバイダー](#evcp)および [コマンドライン構成プロバイダー](#clcp)で設定されている値をオーバーライドするカスタム JSON ファイルは\* **必要ありません** _。</span><span class="sxs-lookup"><span data-stu-id="90aee-333">You typically \* **don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90aee-334">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="90aee-334">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="90aee-335">上記のコードでは、_MyConfig.json\* および *MyConfig*.`Environment`. *json* ファイル内の設定は:</span><span class="sxs-lookup"><span data-stu-id="90aee-335">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`. *json* files:</span></span>

* <span data-ttu-id="90aee-336">*:::no-loc(appsettings.json):::* および *appsettings*.`Environment`. *json* ファイル内の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90aee-336">Override settings in the *:::no-loc(appsettings.json):::* and *appsettings*.`Environment`. *json* files.</span></span>
* <span data-ttu-id="90aee-337">[環境変数の構成プロバイダー](#evcp)と[コマンドライン構成プロバイダー](#clcp)の設定によってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-337">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90aee-338">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *MyConfig.json* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="90aee-338">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="90aee-339">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90aee-339">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="90aee-340">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-340">XML configuration provider</span></span>

<span data-ttu-id="90aee-341"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-341">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="90aee-342">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="90aee-342">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="90aee-343">上記のコードでは、 *MyXMLFile.xml* と *MyXMLFile*.`Environment`. *xml* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="90aee-343">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`. *xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="90aee-344">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-344">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="90aee-345">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="90aee-345">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90aee-346">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyXMLFile.xml* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="90aee-346">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="90aee-347">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90aee-347">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-348">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="90aee-348">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="90aee-349">以下のコードでは、前の構成ファイルを読み取って、キーと値を表示します：</span><span class="sxs-lookup"><span data-stu-id="90aee-349">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-350">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-350">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="90aee-351">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-351">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90aee-352">key:attribute</span><span class="sxs-lookup"><span data-stu-id="90aee-352">key:attribute</span></span>
* <span data-ttu-id="90aee-353">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="90aee-353">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="90aee-354">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-354">Key-per-file configuration provider</span></span>

<span data-ttu-id="90aee-355"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-355">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="90aee-356">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="90aee-356">The key is the file name.</span></span> <span data-ttu-id="90aee-357">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-357">The value contains the file's contents.</span></span> <span data-ttu-id="90aee-358">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-358">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="90aee-359">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-359">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="90aee-360">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-360">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="90aee-361">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-361">Overloads permit specifying:</span></span>

* <span data-ttu-id="90aee-362">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="90aee-362">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="90aee-363">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="90aee-363">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="90aee-364">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-364">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="90aee-365">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-365">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="90aee-366">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-366">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="90aee-367">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-367">Memory configuration provider</span></span>

<span data-ttu-id="90aee-368"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-368">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="90aee-369">次のコードでは、構成システムにメモリコ レクションが追加されています：</span><span class="sxs-lookup"><span data-stu-id="90aee-369">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="90aee-370">[サンプルのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の以下のコードでは、上記の構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="90aee-370">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-371">上記のコードでは、[既定の構成プロバイダー](#default)の後に `config.AddInMemoryCollection(Dict)` が追加されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-371">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="90aee-372">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-372">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="90aee-373">`MemoryConfigurationProvider` を使用した別の例については、[配列をバインド](#boa)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-373">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="90aee-374">GetValue</span><span class="sxs-lookup"><span data-stu-id="90aee-374">GetValue</span></span>

<span data-ttu-id="90aee-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを使用して、構成から単一の値を抽出し、それを指定した型に変換します：</span><span class="sxs-lookup"><span data-stu-id="90aee-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-376">上記のコードでは、`NumberKey` が構成中に見つからない場合には `99` の既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-376">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="90aee-377">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="90aee-377">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="90aee-378">以下の例では、次の *MySubsection.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="90aee-378">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="90aee-379">以下のコードでは、 *MySubsection セクション* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="90aee-379">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="90aee-380">GetSection</span><span class="sxs-lookup"><span data-stu-id="90aee-380">GetSection</span></span>

<span data-ttu-id="90aee-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) は、指定されたサブセクションのキーを持つ構成サブセクションを返します。</span><span class="sxs-lookup"><span data-stu-id="90aee-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="90aee-382">次のコードは、`section1` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="90aee-382">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-383">次のコードは、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="90aee-383">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-384">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-384">`GetSection` never returns `null`.</span></span> <span data-ttu-id="90aee-385">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-385">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="90aee-386">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-386">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="90aee-387"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-387">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="90aee-388">GetChildren と Exists</span><span class="sxs-lookup"><span data-stu-id="90aee-388">GetChildren and Exists</span></span>

<span data-ttu-id="90aee-389">次のコードは、[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) を呼び出し、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="90aee-389">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-390">上記のコードは、[ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を呼び出し、セクションが存在することを確認します：</span><span class="sxs-lookup"><span data-stu-id="90aee-390">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="90aee-391">配列をバインドする</span><span class="sxs-lookup"><span data-stu-id="90aee-391">Bind an array</span></span>

<span data-ttu-id="90aee-392">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) は、構成キーの配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="90aee-392">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90aee-393">数値のキー セグメントを公開する配列形式は、すべて [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="90aee-393">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="90aee-394">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)の *MyArray.json* について考えます：</span><span class="sxs-lookup"><span data-stu-id="90aee-394">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="90aee-395">次のコードでは、 *MyArray.json* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="90aee-395">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="90aee-396">次のコードでは、構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="90aee-396">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-397">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="90aee-397">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="90aee-398">上記の出力では、インデックス3の値が `value40` になります。これは *MyArray. json* の `"4": "value40",` に対応しています。</span><span class="sxs-lookup"><span data-stu-id="90aee-398">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="90aee-399">このバインドされた配列インデックスは連続的であり、構成キーインデックスにバインドされていません。</span><span class="sxs-lookup"><span data-stu-id="90aee-399">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="90aee-400">構成バインダーは、バインドされたオブジェクトに null 値をバインドしたり、null エントリを作成したりはできません</span><span class="sxs-lookup"><span data-stu-id="90aee-400">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="90aee-401">次のコードでは、<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドで `array:entries` 構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="90aee-401">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="90aee-402">次のコードでは、`arrayDict` `Dictionary` の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="90aee-402">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-403">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="90aee-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="90aee-404">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-404">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="90aee-405">配列を含む構成データがバインドされている場合、構成キーの配列インデックスは、オブジェクト作成時の構成データの反復のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-405">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="90aee-406">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-406">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="90aee-407">インデックス&num;3 に不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、インデックス&num;3のキーと値のペアを読み取る構成プロバイダーによってサプライできます。</span><span class="sxs-lookup"><span data-stu-id="90aee-407">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="90aee-408">サンプルダウンロードの、次の *Value3.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="90aee-408">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="90aee-409">次のコードには、 *Value3.json* と `arrayDict` `Dictionary` の構成が含まれています：</span><span class="sxs-lookup"><span data-stu-id="90aee-409">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="90aee-410">次のコードは、上記の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="90aee-410">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-411">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="90aee-411">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="90aee-412">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-412">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="90aee-413">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-413">Custom configuration provider</span></span>

<span data-ttu-id="90aee-414">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="90aee-414">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="90aee-415">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="90aee-415">The provider has the following characteristics:</span></span>

* <span data-ttu-id="90aee-416">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-416">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="90aee-417">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-417">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="90aee-418">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90aee-418">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="90aee-419">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="90aee-419">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="90aee-420">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-420">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="90aee-421">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="90aee-421">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="90aee-422">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-422">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="90aee-423">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="90aee-423">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="90aee-424">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-424">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="90aee-425"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="90aee-425">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="90aee-426">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-426">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="90aee-427"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="90aee-427">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="90aee-428">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="90aee-428">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="90aee-429">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-429">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="90aee-430">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-430">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="90aee-431">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-431">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="90aee-432">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-432">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="90aee-433">次のコードでは、 *Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="90aee-433">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="90aee-434">起動時の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="90aee-434">Access configuration in Startup</span></span>

<span data-ttu-id="90aee-435">次のコードでは `Startup` メソッドの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="90aee-435">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="90aee-436">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90aee-436">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="90aee-437">:::no-loc(Razor)::: ページの構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="90aee-437">Access configuration in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="90aee-438">次のコードでは :::no-loc(Razor)::: ページの構成データが表示されます:</span><span class="sxs-lookup"><span data-stu-id="90aee-438">The following code displays configuration data in a :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="90aee-439">次のコードでは、`MyOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="90aee-439">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="90aee-440">次のマークアップは、[`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: ディレクティブを使用して、オプションの値を解決して表示します。</span><span class="sxs-lookup"><span data-stu-id="90aee-440">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="90aee-441">MVC ビューファイルの構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="90aee-441">Access configuration in a MVC view file</span></span>

<span data-ttu-id="90aee-442">次のコードでは、MVC ビューの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="90aee-442">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="90aee-443">デリゲートでオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="90aee-443">Configure options with a delegate</span></span>

<span data-ttu-id="90aee-444">デリゲートで構成されたオプションは、構成プロバイダーで設定された値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="90aee-444">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="90aee-445">サンプル アプリの例 2 では、デリゲートでオプションを構成しています。</span><span class="sxs-lookup"><span data-stu-id="90aee-445">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="90aee-446">次のコードでは、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-446">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="90aee-447">デリゲートを利用して `MyOptions` の値が構成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-447">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="90aee-448">このコードには、次のオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-448">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="90aee-449">先の例では、値 `Option1` と `Option2` が *:::no-loc(appsettings.json):::* で指定されてから、構成されているデリゲートによりオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-449">In the preceding example, the values of `Option1` and `Option2` are specified in *:::no-loc(appsettings.json):::* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="90aee-450">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="90aee-450">Host versus app configuration</span></span>

<span data-ttu-id="90aee-451">アプリを構成して起動する前に、" *ホスト* " を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="90aee-451">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="90aee-452">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="90aee-452">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="90aee-453">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="90aee-453">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="90aee-454">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-454">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="90aee-455">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90aee-455">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="90aee-456">既定のホスト構成</span><span class="sxs-lookup"><span data-stu-id="90aee-456">Default host configuration</span></span>

<span data-ttu-id="90aee-457">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-457">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="90aee-458">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-458">Host configuration is provided from:</span></span>
  * <span data-ttu-id="90aee-459">[環境変数構成プロバイダー](#environment-variables)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="90aee-459">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="90aee-460">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-460">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="90aee-461">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90aee-461">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="90aee-462">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="90aee-462">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="90aee-463">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-463">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="90aee-464">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="90aee-464">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="90aee-465">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="90aee-465">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="90aee-466">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="90aee-466">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="90aee-467">その他の構成</span><span class="sxs-lookup"><span data-stu-id="90aee-467">Other configuration</span></span>

<span data-ttu-id="90aee-468">このトピックは、" *アプリの構成* " のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="90aee-468">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="90aee-469">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-469">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="90aee-470">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-470">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="90aee-471"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="90aee-471">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="90aee-472">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="90aee-472">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="90aee-473">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-473">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="90aee-474">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-474">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="90aee-475">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90aee-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="90aee-476">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="90aee-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="90aee-477"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="90aee-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="90aee-478">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90aee-479">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="90aee-479">Additional resources</span></span>

* [<span data-ttu-id="90aee-480">構成のソースコード</span><span class="sxs-lookup"><span data-stu-id="90aee-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90aee-481">ASP.NET Core でのアプリの構成は、" *構成プロバイダー* " によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="90aee-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="90aee-482">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="90aee-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="90aee-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90aee-483">Azure Key Vault</span></span>
* <span data-ttu-id="90aee-484">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="90aee-484">Azure App Configuration</span></span>
* <span data-ttu-id="90aee-485">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="90aee-485">Command-line arguments</span></span>
* <span data-ttu-id="90aee-486">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="90aee-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="90aee-487">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-487">Directory files</span></span>
* <span data-ttu-id="90aee-488">環境変数</span><span class="sxs-lookup"><span data-stu-id="90aee-488">Environment variables</span></span>
* <span data-ttu-id="90aee-489">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="90aee-489">In-memory .NET objects</span></span>
* <span data-ttu-id="90aee-490">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-490">Settings files</span></span>

<span data-ttu-id="90aee-491">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="90aee-492">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="90aee-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="90aee-493">" *オプション パターン* " は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="90aee-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="90aee-494">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="90aee-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="90aee-495">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="90aee-496">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="90aee-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="90aee-497">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="90aee-497">Host versus app configuration</span></span>

<span data-ttu-id="90aee-498">アプリを構成して起動する前に、" *ホスト* " を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="90aee-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="90aee-499">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="90aee-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="90aee-500">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="90aee-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="90aee-501">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="90aee-502">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90aee-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="90aee-503">その他の構成</span><span class="sxs-lookup"><span data-stu-id="90aee-503">Other configuration</span></span>

<span data-ttu-id="90aee-504">このトピックは、" *アプリの構成* " のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="90aee-504">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="90aee-505">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="90aee-506">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="90aee-507"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="90aee-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="90aee-508">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="90aee-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="90aee-509">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="90aee-510">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90aee-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="90aee-511">既定の構成</span><span class="sxs-lookup"><span data-stu-id="90aee-511">Default configuration</span></span>

<span data-ttu-id="90aee-512">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="90aee-513">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="90aee-514">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="90aee-515">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="90aee-516">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="90aee-517">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="90aee-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="90aee-518">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="90aee-519">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90aee-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="90aee-520">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="90aee-521">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="90aee-521">*:::no-loc(appsettings.json):::* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="90aee-522">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json* 。</span><span class="sxs-lookup"><span data-stu-id="90aee-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="90aee-523">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90aee-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="90aee-524">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="90aee-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="90aee-525">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="90aee-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="90aee-526">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="90aee-526">Security</span></span>

<span data-ttu-id="90aee-527">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="90aee-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="90aee-528">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="90aee-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="90aee-529">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="90aee-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="90aee-530">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="90aee-531">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="90aee-532"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="90aee-533">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="90aee-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="90aee-534">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="90aee-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="90aee-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="90aee-536">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="90aee-537">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="90aee-537">Hierarchical configuration data</span></span>

<span data-ttu-id="90aee-538">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="90aee-539">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="90aee-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="90aee-540">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="90aee-541">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="90aee-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-542">section0:key0</span></span>
* <span data-ttu-id="90aee-543">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-543">section0:key1</span></span>
* <span data-ttu-id="90aee-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-544">section1:key0</span></span>
* <span data-ttu-id="90aee-545">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-545">section1:key1</span></span>

<span data-ttu-id="90aee-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="90aee-547">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="90aee-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="90aee-548">規約</span><span class="sxs-lookup"><span data-stu-id="90aee-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="90aee-549">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-549">Sources and providers</span></span>

<span data-ttu-id="90aee-550">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90aee-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="90aee-551">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="90aee-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="90aee-552">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="90aee-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="90aee-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="90aee-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> を :::no-loc(Razor)::: Pages <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a :::no-loc(Razor)::: Pages <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="90aee-555">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="90aee-556">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="90aee-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="90aee-557">キー</span><span class="sxs-lookup"><span data-stu-id="90aee-557">Keys</span></span>

<span data-ttu-id="90aee-558">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="90aee-559">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-559">Keys are case-insensitive.</span></span> <span data-ttu-id="90aee-560">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="90aee-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="90aee-561">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="90aee-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="90aee-562">重複する JSON キーの詳細については、「[こちらの GitHub のイシュー](https://github.com/dotnet/extensions/issues/2381)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="90aee-563">階層キー</span><span class="sxs-lookup"><span data-stu-id="90aee-563">Hierarchical keys</span></span>
  * <span data-ttu-id="90aee-564">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="90aee-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="90aee-565">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="90aee-566">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="90aee-567">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="90aee-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="90aee-568">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="90aee-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="90aee-569"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="90aee-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90aee-570">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="90aee-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="90aee-571">値</span><span class="sxs-lookup"><span data-stu-id="90aee-571">Values</span></span>

<span data-ttu-id="90aee-572">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="90aee-573">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="90aee-573">Values are strings.</span></span>
* <span data-ttu-id="90aee-574">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="90aee-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="90aee-575">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-575">Providers</span></span>

<span data-ttu-id="90aee-576">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="90aee-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="90aee-577">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-577">Provider</span></span> | <span data-ttu-id="90aee-578">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="90aee-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="90aee-579">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) (" *セキュリティ* " トピック)</span><span class="sxs-lookup"><span data-stu-id="90aee-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) ( *Security* topics)</span></span> | <span data-ttu-id="90aee-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90aee-580">Azure Key Vault</span></span> |
| <span data-ttu-id="90aee-581">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="90aee-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="90aee-582">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="90aee-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="90aee-583">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="90aee-584">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="90aee-584">Command-line parameters</span></span> |
| [<span data-ttu-id="90aee-585">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="90aee-586">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="90aee-586">Custom source</span></span> |
| [<span data-ttu-id="90aee-587">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="90aee-588">環境変数</span><span class="sxs-lookup"><span data-stu-id="90aee-588">Environment variables</span></span> |
| [<span data-ttu-id="90aee-589">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="90aee-590">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="90aee-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="90aee-591">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="90aee-592">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-592">Directory files</span></span> |
| [<span data-ttu-id="90aee-593">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="90aee-594">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="90aee-594">In-memory collections</span></span> |
| <span data-ttu-id="90aee-595">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (" *セキュリティ* " トピック)</span><span class="sxs-lookup"><span data-stu-id="90aee-595">[User secrets (Secret Manager)](xref:security/app-secrets) ( *Security* topics)</span></span> | <span data-ttu-id="90aee-596">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="90aee-596">File in the user profile directory</span></span> |

<span data-ttu-id="90aee-597">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90aee-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="90aee-598">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="90aee-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="90aee-599">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="90aee-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="90aee-600">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="90aee-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="90aee-601">ファイル ( *:::no-loc(appsettings.json):::* 、 *appsettings.{Environment}.json* 。ここで、`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="90aee-601">Files ( *:::no-loc(appsettings.json):::* , *appsettings.{Environment}.json* , where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="90aee-602">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90aee-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="90aee-603">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="90aee-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="90aee-604">環境変数</span><span class="sxs-lookup"><span data-stu-id="90aee-604">Environment variables</span></span>
1. <span data-ttu-id="90aee-605">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="90aee-605">Command-line arguments</span></span>

<span data-ttu-id="90aee-606">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="90aee-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="90aee-607">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90aee-608">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="90aee-609">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="90aee-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="90aee-610">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="90aee-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="90aee-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="90aee-612">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="90aee-613">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="90aee-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="90aee-614">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="90aee-615">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="90aee-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="90aee-616">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="90aee-617">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="90aee-617">Consume configuration during app startup</span></span>

<span data-ttu-id="90aee-618">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="90aee-619">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="90aee-620">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="90aee-621"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="90aee-622">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90aee-623">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="90aee-624">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90aee-625">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90aee-626">*:::no-loc(appsettings.json):::* および *appsettings.{Environment}.json* ファイルからの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="90aee-626">Optional configuration from *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="90aee-627">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90aee-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90aee-628">環境変数。</span><span class="sxs-lookup"><span data-stu-id="90aee-628">Environment variables.</span></span>

<span data-ttu-id="90aee-629">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="90aee-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="90aee-630">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="90aee-631">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="90aee-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="90aee-632">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="90aee-633">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="90aee-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90aee-634">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="90aee-635">**例**</span><span class="sxs-lookup"><span data-stu-id="90aee-635">**Example**</span></span>

<span data-ttu-id="90aee-636">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="90aee-637">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="90aee-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="90aee-638">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="90aee-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="90aee-639">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="90aee-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90aee-640">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="90aee-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="90aee-641">引数</span><span class="sxs-lookup"><span data-stu-id="90aee-641">Arguments</span></span>

<span data-ttu-id="90aee-642">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="90aee-643">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="90aee-644">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="90aee-644">Key prefix</span></span>               | <span data-ttu-id="90aee-645">例</span><span class="sxs-lookup"><span data-stu-id="90aee-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="90aee-646">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="90aee-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="90aee-647">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="90aee-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="90aee-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="90aee-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="90aee-649">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="90aee-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="90aee-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="90aee-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="90aee-651">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="90aee-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="90aee-652">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="90aee-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="90aee-653">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="90aee-653">Switch mappings</span></span>

<span data-ttu-id="90aee-654">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="90aee-655"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="90aee-656">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="90aee-657">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="90aee-658">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="90aee-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="90aee-659">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="90aee-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="90aee-660">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="90aee-661">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="90aee-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="90aee-662">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="90aee-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="90aee-663">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="90aee-664">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="90aee-665">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="90aee-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="90aee-666">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90aee-667">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="90aee-668">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="90aee-669">Key</span><span class="sxs-lookup"><span data-stu-id="90aee-669">Key</span></span>       | <span data-ttu-id="90aee-670">値</span><span class="sxs-lookup"><span data-stu-id="90aee-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="90aee-671">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="90aee-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="90aee-672">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="90aee-673">Key</span><span class="sxs-lookup"><span data-stu-id="90aee-673">Key</span></span>               | <span data-ttu-id="90aee-674">[値]</span><span class="sxs-lookup"><span data-stu-id="90aee-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="90aee-675">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="90aee-676"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="90aee-677">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="90aee-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="90aee-679">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="90aee-680">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="90aee-681">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90aee-682">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90aee-683">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="90aee-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="90aee-684">*:::no-loc(appsettings.json):::* および *appsettings.{Environment}.json* ファイルからの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="90aee-684">Optional configuration from *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="90aee-685">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90aee-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90aee-686">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="90aee-686">Command-line arguments.</span></span>

<span data-ttu-id="90aee-687">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="90aee-688">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="90aee-689">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="90aee-690">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="90aee-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="90aee-691">**例**</span><span class="sxs-lookup"><span data-stu-id="90aee-691">**Example**</span></span>

<span data-ttu-id="90aee-692">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="90aee-693">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="90aee-693">Run the sample app.</span></span> <span data-ttu-id="90aee-694">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="90aee-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90aee-695">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="90aee-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="90aee-696">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="90aee-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="90aee-697">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="90aee-698">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="90aee-699">アプリで使用できるすべての環境変数を公開する場合は、 *Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="90aee-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="90aee-700">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="90aee-700">Prefixes</span></span>

<span data-ttu-id="90aee-701">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="90aee-702">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="90aee-703">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="90aee-704">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="90aee-705">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90aee-706">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="90aee-706">**Connection string prefixes**</span></span>

<span data-ttu-id="90aee-707">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="90aee-708">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="90aee-709">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="90aee-709">Connection string prefix</span></span> | <span data-ttu-id="90aee-710">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="90aee-711">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="90aee-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="90aee-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="90aee-713">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="90aee-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="90aee-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="90aee-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="90aee-715">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="90aee-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="90aee-716">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="90aee-717">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="90aee-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="90aee-718">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="90aee-718">Environment variable key</span></span> | <span data-ttu-id="90aee-719">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="90aee-719">Converted configuration key</span></span> | <span data-ttu-id="90aee-720">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="90aee-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90aee-721">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90aee-722">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90aee-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90aee-723">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="90aee-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90aee-724">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90aee-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90aee-725">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90aee-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90aee-726">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90aee-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90aee-727">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90aee-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="90aee-728">**例**</span><span class="sxs-lookup"><span data-stu-id="90aee-728">**Example**</span></span>

<span data-ttu-id="90aee-729">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="90aee-730">名前: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="90aee-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="90aee-731">値: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="90aee-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="90aee-732">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90aee-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="90aee-733">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-733">File Configuration Provider</span></span>

<span data-ttu-id="90aee-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="90aee-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="90aee-735">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="90aee-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="90aee-736">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="90aee-737">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="90aee-738">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="90aee-739">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-739">INI Configuration Provider</span></span>

<span data-ttu-id="90aee-740"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="90aee-741">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90aee-742">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="90aee-743">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="90aee-744">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="90aee-744">Whether the file is optional.</span></span>
* <span data-ttu-id="90aee-745">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="90aee-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90aee-746">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90aee-747">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90aee-748">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="90aee-748">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="90aee-749">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90aee-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-750">section0:key0</span></span>
* <span data-ttu-id="90aee-751">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-751">section0:key1</span></span>
* <span data-ttu-id="90aee-752">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="90aee-752">section1:subsection:key</span></span>
* <span data-ttu-id="90aee-753">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="90aee-753">section2:subsection0:key</span></span>
* <span data-ttu-id="90aee-754">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="90aee-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="90aee-755">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-755">JSON Configuration Provider</span></span>

<span data-ttu-id="90aee-756"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="90aee-757">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90aee-758">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="90aee-759">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="90aee-759">Whether the file is optional.</span></span>
* <span data-ttu-id="90aee-760">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="90aee-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90aee-761">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90aee-762">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90aee-763">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="90aee-764">*:::no-loc(appsettings.json):::* : このファイルは最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90aee-764">*:::no-loc(appsettings.json):::* : This file is read first.</span></span> <span data-ttu-id="90aee-765">ファイルの環境バージョンを使用すると、 *:::no-loc(appsettings.json):::* ファイルによって指定された値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="90aee-765">The environment version of the file can override the values provided by the *:::no-loc(appsettings.json):::* file.</span></span>
* <span data-ttu-id="90aee-766">*appsettings.{Environment}.json* :ファイルの環境バージョンは、 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-766">*appsettings.{Environment}.json* : The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="90aee-767">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90aee-768">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90aee-769">環境変数。</span><span class="sxs-lookup"><span data-stu-id="90aee-769">Environment variables.</span></span>
* <span data-ttu-id="90aee-770">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="90aee-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90aee-771">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="90aee-771">Command-line arguments.</span></span>

<span data-ttu-id="90aee-772">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="90aee-773">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、 *appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="90aee-774">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、 *:::no-loc(appsettings.json):::* および *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* :</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90aee-775">**例**</span><span class="sxs-lookup"><span data-stu-id="90aee-775">**Example**</span></span>

<span data-ttu-id="90aee-776">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="90aee-777">`AddJsonFile` の最初の呼び出しを行うと、 *:::no-loc(appsettings.json):::* から構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-777">The first call to `AddJsonFile` loads configuration from *:::no-loc(appsettings.json):::* :</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/:::no-loc(appsettings.json):::)]

* <span data-ttu-id="90aee-778">`AddJsonFile` の 2 回目の呼び出しでは、 *appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="90aee-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="90aee-779">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="90aee-780">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="90aee-780">Run the sample app.</span></span> <span data-ttu-id="90aee-781">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="90aee-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90aee-782">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="90aee-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="90aee-783">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="90aee-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="90aee-784">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="90aee-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="90aee-785">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="90aee-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="90aee-786">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="90aee-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="90aee-787">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="90aee-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="90aee-788">*appsettings.Development.json* 内の設定によって、 *:::no-loc(appsettings.json):::* 内の設定はオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="90aee-788">The settings in the *appsettings.Development.json* no longer override the settings in *:::no-loc(appsettings.json):::*.</span></span> <span data-ttu-id="90aee-789">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="90aee-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="90aee-790">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-790">XML Configuration Provider</span></span>

<span data-ttu-id="90aee-791"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="90aee-792">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90aee-793">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="90aee-794">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="90aee-794">Whether the file is optional.</span></span>
* <span data-ttu-id="90aee-795">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="90aee-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90aee-796">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90aee-797">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="90aee-798">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="90aee-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="90aee-799">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90aee-800">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-800">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="90aee-801">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90aee-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-802">section0:key0</span></span>
* <span data-ttu-id="90aee-803">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-803">section0:key1</span></span>
* <span data-ttu-id="90aee-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-804">section1:key0</span></span>
* <span data-ttu-id="90aee-805">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-805">section1:key1</span></span>

<span data-ttu-id="90aee-806">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="90aee-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="90aee-807">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90aee-808">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-808">section:section0:key:key0</span></span>
* <span data-ttu-id="90aee-809">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-809">section:section0:key:key1</span></span>
* <span data-ttu-id="90aee-810">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-810">section:section1:key:key0</span></span>
* <span data-ttu-id="90aee-811">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-811">section:section1:key:key1</span></span>

<span data-ttu-id="90aee-812">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="90aee-813">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90aee-814">key:attribute</span><span class="sxs-lookup"><span data-stu-id="90aee-814">key:attribute</span></span>
* <span data-ttu-id="90aee-815">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="90aee-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="90aee-816">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="90aee-817"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="90aee-818">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="90aee-818">The key is the file name.</span></span> <span data-ttu-id="90aee-819">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-819">The value contains the file's contents.</span></span> <span data-ttu-id="90aee-820">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="90aee-821">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="90aee-822">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="90aee-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="90aee-823">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="90aee-824">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="90aee-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="90aee-825">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="90aee-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="90aee-826">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="90aee-827">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="90aee-828">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="90aee-829">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-829">Memory Configuration Provider</span></span>

<span data-ttu-id="90aee-830"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="90aee-831">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90aee-832">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="90aee-833">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="90aee-834">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="90aee-835">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="90aee-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="90aee-836">GetValue</span></span>

<span data-ttu-id="90aee-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを持つ構成から単一の値を抽出し、指定したコレクション以外の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="90aee-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="90aee-838">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="90aee-838">An overload accepts a default value.</span></span>

<span data-ttu-id="90aee-839">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="90aee-839">The following example:</span></span>

* <span data-ttu-id="90aee-840">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="90aee-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="90aee-841">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="90aee-842">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="90aee-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="90aee-843">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="90aee-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="90aee-844">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="90aee-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="90aee-845">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="90aee-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="90aee-846">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="90aee-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="90aee-847">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="90aee-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-848">section0:key0</span></span>
* <span data-ttu-id="90aee-849">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-849">section0:key1</span></span>
* <span data-ttu-id="90aee-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-850">section1:key0</span></span>
* <span data-ttu-id="90aee-851">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-851">section1:key1</span></span>
* <span data-ttu-id="90aee-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="90aee-853">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="90aee-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="90aee-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="90aee-855">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="90aee-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="90aee-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="90aee-856">GetSection</span></span>

<span data-ttu-id="90aee-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="90aee-858">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="90aee-859">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="90aee-860">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="90aee-861">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="90aee-862">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="90aee-863">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="90aee-864"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="90aee-865">GetChildren</span><span class="sxs-lookup"><span data-stu-id="90aee-865">GetChildren</span></span>

<span data-ttu-id="90aee-866">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="90aee-867">既存</span><span class="sxs-lookup"><span data-stu-id="90aee-867">Exists</span></span>

<span data-ttu-id="90aee-868">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="90aee-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="90aee-869">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="90aee-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="90aee-870">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="90aee-870">Bind to an object graph</span></span>

<span data-ttu-id="90aee-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="90aee-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="90aee-872">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="90aee-873">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます ( *Models/TvShow.cs* )。</span><span class="sxs-lookup"><span data-stu-id="90aee-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes ( *Models/TvShow.cs* ):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="90aee-874">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="90aee-875">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="90aee-876">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="90aee-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="90aee-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="90aee-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="90aee-878">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="90aee-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="90aee-879">次のコードは、上記の例で `Get<T>` を使用する方法を示します：</span><span class="sxs-lookup"><span data-stu-id="90aee-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="90aee-880">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="90aee-880">Bind an array to a class</span></span>

<span data-ttu-id="90aee-881">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="90aee-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="90aee-882"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="90aee-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90aee-883">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="90aee-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="90aee-884">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-884">Binding is provided by convention.</span></span> <span data-ttu-id="90aee-885">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="90aee-886">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="90aee-886">**In-memory array processing**</span></span>

<span data-ttu-id="90aee-887">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="90aee-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="90aee-888">Key</span><span class="sxs-lookup"><span data-stu-id="90aee-888">Key</span></span>             | <span data-ttu-id="90aee-889">[値]</span><span class="sxs-lookup"><span data-stu-id="90aee-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="90aee-890">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="90aee-890">array:entries:0</span></span> | <span data-ttu-id="90aee-891">value0</span><span class="sxs-lookup"><span data-stu-id="90aee-891">value0</span></span> |
| <span data-ttu-id="90aee-892">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="90aee-892">array:entries:1</span></span> | <span data-ttu-id="90aee-893">value1</span><span class="sxs-lookup"><span data-stu-id="90aee-893">value1</span></span> |
| <span data-ttu-id="90aee-894">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="90aee-894">array:entries:2</span></span> | <span data-ttu-id="90aee-895">value2</span><span class="sxs-lookup"><span data-stu-id="90aee-895">value2</span></span> |
| <span data-ttu-id="90aee-896">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="90aee-896">array:entries:4</span></span> | <span data-ttu-id="90aee-897">value4</span><span class="sxs-lookup"><span data-stu-id="90aee-897">value4</span></span> |
| <span data-ttu-id="90aee-898">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="90aee-898">array:entries:5</span></span> | <span data-ttu-id="90aee-899">value5</span><span class="sxs-lookup"><span data-stu-id="90aee-899">value5</span></span> |

<span data-ttu-id="90aee-900">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="90aee-901">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="90aee-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="90aee-902">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="90aee-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="90aee-903">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="90aee-904">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="90aee-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="90aee-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用することもできます。これにより、コードがよりコンパクトになります：</span><span class="sxs-lookup"><span data-stu-id="90aee-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="90aee-906">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="90aee-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="90aee-907">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="90aee-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="90aee-908">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="90aee-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="90aee-909">0</span><span class="sxs-lookup"><span data-stu-id="90aee-909">0</span></span>                            | <span data-ttu-id="90aee-910">value0</span><span class="sxs-lookup"><span data-stu-id="90aee-910">value0</span></span>                       |
| <span data-ttu-id="90aee-911">1</span><span class="sxs-lookup"><span data-stu-id="90aee-911">1</span></span>                            | <span data-ttu-id="90aee-912">value1</span><span class="sxs-lookup"><span data-stu-id="90aee-912">value1</span></span>                       |
| <span data-ttu-id="90aee-913">2</span><span class="sxs-lookup"><span data-stu-id="90aee-913">2</span></span>                            | <span data-ttu-id="90aee-914">value2</span><span class="sxs-lookup"><span data-stu-id="90aee-914">value2</span></span>                       |
| <span data-ttu-id="90aee-915">3</span><span class="sxs-lookup"><span data-stu-id="90aee-915">3</span></span>                            | <span data-ttu-id="90aee-916">value4</span><span class="sxs-lookup"><span data-stu-id="90aee-916">value4</span></span>                       |
| <span data-ttu-id="90aee-917">4</span><span class="sxs-lookup"><span data-stu-id="90aee-917">4</span></span>                            | <span data-ttu-id="90aee-918">value5</span><span class="sxs-lookup"><span data-stu-id="90aee-918">value5</span></span>                       |

<span data-ttu-id="90aee-919">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="90aee-920">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="90aee-921">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="90aee-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="90aee-922">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="90aee-923">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="90aee-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="90aee-924">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="90aee-924">*missing_value.json* :</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="90aee-925">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="90aee-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="90aee-926">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="90aee-927">Key</span><span class="sxs-lookup"><span data-stu-id="90aee-927">Key</span></span>             | <span data-ttu-id="90aee-928">値</span><span class="sxs-lookup"><span data-stu-id="90aee-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="90aee-929">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="90aee-929">array:entries:3</span></span> | <span data-ttu-id="90aee-930">value3</span><span class="sxs-lookup"><span data-stu-id="90aee-930">value3</span></span> |

<span data-ttu-id="90aee-931">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="90aee-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="90aee-932">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="90aee-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="90aee-933">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="90aee-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="90aee-934">0</span><span class="sxs-lookup"><span data-stu-id="90aee-934">0</span></span>                            | <span data-ttu-id="90aee-935">value0</span><span class="sxs-lookup"><span data-stu-id="90aee-935">value0</span></span>                       |
| <span data-ttu-id="90aee-936">1</span><span class="sxs-lookup"><span data-stu-id="90aee-936">1</span></span>                            | <span data-ttu-id="90aee-937">value1</span><span class="sxs-lookup"><span data-stu-id="90aee-937">value1</span></span>                       |
| <span data-ttu-id="90aee-938">2</span><span class="sxs-lookup"><span data-stu-id="90aee-938">2</span></span>                            | <span data-ttu-id="90aee-939">value2</span><span class="sxs-lookup"><span data-stu-id="90aee-939">value2</span></span>                       |
| <span data-ttu-id="90aee-940">3</span><span class="sxs-lookup"><span data-stu-id="90aee-940">3</span></span>                            | <span data-ttu-id="90aee-941">value3</span><span class="sxs-lookup"><span data-stu-id="90aee-941">value3</span></span>                       |
| <span data-ttu-id="90aee-942">4</span><span class="sxs-lookup"><span data-stu-id="90aee-942">4</span></span>                            | <span data-ttu-id="90aee-943">value4</span><span class="sxs-lookup"><span data-stu-id="90aee-943">value4</span></span>                       |
| <span data-ttu-id="90aee-944">5</span><span class="sxs-lookup"><span data-stu-id="90aee-944">5</span></span>                            | <span data-ttu-id="90aee-945">value5</span><span class="sxs-lookup"><span data-stu-id="90aee-945">value5</span></span>                       |

<span data-ttu-id="90aee-946">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="90aee-946">**JSON array processing**</span></span>

<span data-ttu-id="90aee-947">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="90aee-948">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="90aee-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="90aee-949">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90aee-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="90aee-950">Key</span><span class="sxs-lookup"><span data-stu-id="90aee-950">Key</span></span>                     | <span data-ttu-id="90aee-951">値</span><span class="sxs-lookup"><span data-stu-id="90aee-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="90aee-952">json_array:key</span><span class="sxs-lookup"><span data-stu-id="90aee-952">json_array:key</span></span>          | <span data-ttu-id="90aee-953">valueA</span><span class="sxs-lookup"><span data-stu-id="90aee-953">valueA</span></span> |
| <span data-ttu-id="90aee-954">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="90aee-954">json_array:subsection:0</span></span> | <span data-ttu-id="90aee-955">valueB</span><span class="sxs-lookup"><span data-stu-id="90aee-955">valueB</span></span> |
| <span data-ttu-id="90aee-956">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="90aee-956">json_array:subsection:1</span></span> | <span data-ttu-id="90aee-957">valueC</span><span class="sxs-lookup"><span data-stu-id="90aee-957">valueC</span></span> |
| <span data-ttu-id="90aee-958">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="90aee-958">json_array:subsection:2</span></span> | <span data-ttu-id="90aee-959">valueD</span><span class="sxs-lookup"><span data-stu-id="90aee-959">valueD</span></span> |

<span data-ttu-id="90aee-960">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="90aee-961">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="90aee-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="90aee-962">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="90aee-963">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="90aee-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="90aee-964">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="90aee-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="90aee-965">0</span><span class="sxs-lookup"><span data-stu-id="90aee-965">0</span></span>                                   | <span data-ttu-id="90aee-966">valueB</span><span class="sxs-lookup"><span data-stu-id="90aee-966">valueB</span></span>                              |
| <span data-ttu-id="90aee-967">1</span><span class="sxs-lookup"><span data-stu-id="90aee-967">1</span></span>                                   | <span data-ttu-id="90aee-968">valueC</span><span class="sxs-lookup"><span data-stu-id="90aee-968">valueC</span></span>                              |
| <span data-ttu-id="90aee-969">2</span><span class="sxs-lookup"><span data-stu-id="90aee-969">2</span></span>                                   | <span data-ttu-id="90aee-970">valueD</span><span class="sxs-lookup"><span data-stu-id="90aee-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="90aee-971">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90aee-971">Custom configuration provider</span></span>

<span data-ttu-id="90aee-972">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="90aee-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="90aee-973">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="90aee-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="90aee-974">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="90aee-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="90aee-975">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="90aee-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="90aee-976">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="90aee-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="90aee-977">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="90aee-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="90aee-978">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="90aee-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="90aee-979">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="90aee-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="90aee-980">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-980">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="90aee-981">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="90aee-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="90aee-982">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-982">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="90aee-983"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="90aee-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="90aee-984">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-984">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="90aee-985"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="90aee-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="90aee-986">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="90aee-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="90aee-987">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-987">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="90aee-988">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="90aee-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="90aee-989">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="90aee-989">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="90aee-990">次のコードでは、 *Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="90aee-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="90aee-991">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="90aee-991">Access configuration during startup</span></span>

<span data-ttu-id="90aee-992">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="90aee-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="90aee-993">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="90aee-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="90aee-994">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="90aee-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="90aee-995">:::no-loc(Razor)::: Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="90aee-995">Access configuration in a :::no-loc(Razor)::: Pages page or MVC view</span></span>

<span data-ttu-id="90aee-996">:::no-loc(Razor)::: Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) 名前空間に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="90aee-996">To access configuration settings in a :::no-loc(Razor)::: Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="90aee-997">:::no-loc(Razor)::: Pages ページで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="90aee-997">In a :::no-loc(Razor)::: Pages page:</span></span>

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
    <h1>Access configuration in a :::no-loc(Razor)::: Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="90aee-998">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="90aee-998">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="90aee-999">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="90aee-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="90aee-1000"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="90aee-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="90aee-1001">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90aee-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90aee-1002">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="90aee-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
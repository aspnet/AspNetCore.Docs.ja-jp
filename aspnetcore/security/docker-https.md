---
title: HTTPS 経由で Docker を使用して ASP.NET Core イメージをホストする
author: rick-anderson
description: HTTPS 経由で Docker を使用して ASP.NET Core イメージをホストする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: 63d6e220c0f28e552207039c1649041bfdf4a0d4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059677"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="2d122-103">HTTPS 経由で Docker を使用して ASP.NET Core イメージをホストする</span><span class="sxs-lookup"><span data-stu-id="2d122-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="2d122-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2d122-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2d122-105">[既定では、](./enforcing-ssl.md)ASP.NET Core で HTTPS が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2d122-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="2d122-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) は、信頼、id、および暗号化のための [証明書](https://en.wikipedia.org/wiki/Public_key_certificate) に依存します。</span><span class="sxs-lookup"><span data-stu-id="2d122-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="2d122-107">このドキュメントでは、HTTPS で事前に構築されたコンテナーイメージを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2d122-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="2d122-108">開発シナリオについては、「 [HTTPS 経由の Docker を使用した ASP.NET Core アプリケーションの開発](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d122-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="2d122-109">このサンプルでは、docker [17.06](https://docs.docker.com/release-notes/docker-ce) 以降の [docker クライアント](https://www.docker.com/products/docker)が必要です。</span><span class="sxs-lookup"><span data-stu-id="2d122-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2d122-110">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="2d122-110">Prerequisites</span></span>

<span data-ttu-id="2d122-111">このドキュメントの一部の手順では、 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="2d122-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="2d122-112">証明書</span><span class="sxs-lookup"><span data-stu-id="2d122-112">Certificates</span></span>

<span data-ttu-id="2d122-113">ドメインの[運用ホスト](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)には、[証明機関](https://wikipedia.org/wiki/Certificate_authority)からの証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="2d122-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="2d122-114">[Let's Encrypt](https://letsencrypt.org/) は、無料の証明書を提供する証明機関です。</span><span class="sxs-lookup"><span data-stu-id="2d122-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="2d122-115">このドキュメントでは、事前に構築されたイメージをホストするために [自己署名の開発証明書](https://en.wikipedia.org/wiki/Self-signed_certificate) を使用 `localhost` します。</span><span class="sxs-lookup"><span data-stu-id="2d122-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="2d122-116">手順は、実稼働証明書の使用に似ています。</span><span class="sxs-lookup"><span data-stu-id="2d122-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="2d122-117">実稼働証明書の場合:</span><span class="sxs-lookup"><span data-stu-id="2d122-117">For production certs:</span></span>

* <span data-ttu-id="2d122-118">`dotnet dev-certs`ツールは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2d122-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="2d122-119">手順で使用した場所に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2d122-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="2d122-120">任意の場所を使用できますが、証明書をサイトディレクトリ内に格納することはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="2d122-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="2d122-121">次のセクションに記載されている手順では、Docker のコマンドラインオプションを使用して証明書をコンテナーにマウントし `-v` ます。</span><span class="sxs-lookup"><span data-stu-id="2d122-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="2d122-122">Dockerfile でコマンドを使用してコンテナーイメージに証明書を追加することもでき `COPY` ますが、この方法はお勧めしません。 *Dockerfile*</span><span class="sxs-lookup"><span data-stu-id="2d122-122">You could add certificates into container images with a `COPY` command in a *Dockerfile* , but it's not recommended.</span></span> <span data-ttu-id="2d122-123">証明書をイメージにコピーすることは、次の理由から推奨されません。</span><span class="sxs-lookup"><span data-stu-id="2d122-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="2d122-124">開発者の証明書を使用したテストで同じイメージを使用するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="2d122-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="2d122-125">実稼働証明書を使用してホストする場合、同じイメージを使用するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="2d122-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="2d122-126">証明書の公開には大きなリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="2d122-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="2d122-127">HTTPS を使用した既成のコンテナーイメージの実行</span><span class="sxs-lookup"><span data-stu-id="2d122-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="2d122-128">オペレーティングシステムの構成については、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="2d122-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="2d122-129">Linux コンテナーを使用した Windows</span><span class="sxs-lookup"><span data-stu-id="2d122-129">Windows using Linux containers</span></span>

<span data-ttu-id="2d122-130">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="2d122-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2d122-131">上記のコマンドで、を `{ password here }` パスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2d122-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2d122-132">コマンドシェルで HTTPS 用に構成された ASP.NET Core でコンテナーイメージを実行します。</span><span class="sxs-lookup"><span data-stu-id="2d122-132">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2d122-133">[PowerShell](/powershell/scripting/overview)を使用する場合は、を `%USERPROFILE%` に置き換え `$env:USERPROFILE` ます。</span><span class="sxs-lookup"><span data-stu-id="2d122-133">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="2d122-134">パスワードは、証明書に使用されているパスワードと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d122-134">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="2d122-135">macOS または Linux</span><span class="sxs-lookup"><span data-stu-id="2d122-135">macOS or Linux</span></span>

<span data-ttu-id="2d122-136">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="2d122-136">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2d122-137">`dotnet dev-certs https --trust` は、macOS と Windows でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2d122-137">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="2d122-138">ディストリビューションでサポートされている方法で、Linux 上の証明書を信頼する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d122-138">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="2d122-139">ブラウザーで証明書を信頼する必要があると考えられます。</span><span class="sxs-lookup"><span data-stu-id="2d122-139">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="2d122-140">上記のコマンドで、を `{ password here }` パスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2d122-140">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="2d122-141">HTTPS 用に構成された ASP.NET Core でコンテナーイメージを実行します。</span><span class="sxs-lookup"><span data-stu-id="2d122-141">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2d122-142">パスワードは、証明書に使用されているパスワードと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d122-142">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="2d122-143">Windows コンテナーを使用した windows</span><span class="sxs-lookup"><span data-stu-id="2d122-143">Windows using Windows containers</span></span>

<span data-ttu-id="2d122-144">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="2d122-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="2d122-145">上記のコマンドで、を `{ password here }` パスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2d122-145">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="2d122-146">[PowerShell](/powershell/scripting/overview)を使用する場合は、を `%USERPROFILE%` に置き換え `$env:USERPROFILE` ます。</span><span class="sxs-lookup"><span data-stu-id="2d122-146">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="2d122-147">HTTPS 用に構成された ASP.NET Core でコンテナーイメージを実行します。</span><span class="sxs-lookup"><span data-stu-id="2d122-147">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="2d122-148">パスワードは、証明書に使用されているパスワードと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d122-148">The password must match the password used for the certificate.</span></span> <span data-ttu-id="2d122-149">[PowerShell](/powershell/scripting/overview)を使用する場合は、を `%USERPROFILE%` に置き換え `$env:USERPROFILE` ます。</span><span class="sxs-lookup"><span data-stu-id="2d122-149">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
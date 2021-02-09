---
title: .NET での gRPC でサポートされているプラットフォーム
author: jamesnk
description: .NET での gRPC でサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530165"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="54709-103">.NET での gRPC でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="54709-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="54709-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="54709-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="54709-105">この記事では、.NET で gRPC を使用するための要件と、サポートされているプラットフォームについて説明します。</span><span class="sxs-lookup"><span data-stu-id="54709-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="54709-106">gRPC では、HTTP/2 で利用できる高度な機能が活用されます。</span><span class="sxs-lookup"><span data-stu-id="54709-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="54709-107">HTTP/2 はどこでもサポートされているわけではありませんが、gRPC では、HTTP/1.1 を使用した 2 番目のワイヤ形式を利用できます。</span><span class="sxs-lookup"><span data-stu-id="54709-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="54709-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - HTTP/2 による gRPC は、一般的な gRPC の使用方法です。</span><span class="sxs-lookup"><span data-stu-id="54709-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="54709-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web では、HTTP/1.1 と互換性を持つように gRPC プロトコルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="54709-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="54709-110">gRPC-Web はより多くの場所で使用できます。特に、ブラウザー アプリから呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="54709-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="54709-111">2 つの高度な gRPC 機能がサポートされなくなりました。クライアント ストリーミングと双方向ストリーミングです。</span><span class="sxs-lookup"><span data-stu-id="54709-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="54709-112">.NET での gRPC では、両方のワイヤ形式がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="54709-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="54709-113">既定では、HTTP/2 による gRPC が使用されます。</span><span class="sxs-lookup"><span data-stu-id="54709-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="54709-114">gRPC-Web の設定方法について詳しくは、「<xref:grpc/browser>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54709-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="54709-115">デバイスの要件</span><span class="sxs-lookup"><span data-stu-id="54709-115">Device requirements</span></span>

<span data-ttu-id="54709-116">.NET での gRPC では、.NET Core でサポートされているすべてのデバイスがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="54709-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="54709-117">Windows</span><span class="sxs-lookup"><span data-stu-id="54709-117">Windows</span></span>
> * <span data-ttu-id="54709-118">Linux</span><span class="sxs-lookup"><span data-stu-id="54709-118">Linux</span></span>
> * <span data-ttu-id="54709-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="54709-119">macOS&dagger;</span></span>
> * <span data-ttu-id="54709-120">ブラウザー&Dagger;</span><span class="sxs-lookup"><span data-stu-id="54709-120">Browsers&Dagger;</span></span>

<span data-ttu-id="54709-121">&dagger;[macOS では、HTTPS を使用した ASP.NET Core アプリのホストがサポートされていません](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="54709-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="54709-122">macOS 上の gRPC クライアントでは、HTTPS を使用するリモート サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="54709-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="54709-123">&Dagger;Blazor WebAssembly アプリでは、gRPC-Web を使用して gRPC サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="54709-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="54709-124">ASP.NET Core サーバーの要件</span><span class="sxs-lookup"><span data-stu-id="54709-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="54709-125">gRPC サービスは、すべての組み込み ASP.NET Core サーバー上でホストできます。</span><span class="sxs-lookup"><span data-stu-id="54709-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="54709-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="54709-126">Kestrel</span></span>
> * <span data-ttu-id="54709-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="54709-127">TestServer</span></span>
> * <span data-ttu-id="54709-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="54709-128">IIS&dagger;</span></span>
> * <span data-ttu-id="54709-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="54709-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="54709-130">&dagger;IIS では、.NET 5 および Windows 10 ビルド 20241 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="54709-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="54709-131">&Dagger;HTTP.sys では、.NET 5 および Windows 10 ビルド 19529 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="54709-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="54709-132">gRPC を実行するために ASP.NET Core サーバーを構成する方法の詳細については、<xref:grpc/aspnetcore#server-options> に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54709-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="54709-133">.NET バージョンの要件</span><span class="sxs-lookup"><span data-stu-id="54709-133">.NET version requirements</span></span>

<span data-ttu-id="54709-134">.NET での gRPC では、.NET Core 3 および .NET 5 以降がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="54709-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="54709-135">.NET 5 以降</span><span class="sxs-lookup"><span data-stu-id="54709-135">.NET 5 or later</span></span>
> * <span data-ttu-id="54709-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="54709-136">.NET Core 3</span></span>

<span data-ttu-id="54709-137">.NET での gRPC では、.NET Framework および Xamarin での実行はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="54709-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="54709-138">[gRPC C# コアライブラリ](https://grpc.io/docs/languages/csharp/quickstart/)は、.NET Framework と Xamarin がサポートされているサード パーティ製のライブラリです。</span><span class="sxs-lookup"><span data-stu-id="54709-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="54709-139">gRPC C-コアは、Microsoft によってサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="54709-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="54709-140">Azure サービス</span><span class="sxs-lookup"><span data-stu-id="54709-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="54709-141">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="54709-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="54709-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="54709-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="54709-143">&dagger;Azure App Service では、HTTP/2 による gRPC のホストがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="54709-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="54709-144">gRPC-Web は互換性のある代替手段です。</span><span class="sxs-lookup"><span data-stu-id="54709-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="54709-145">Azure App Service では、HTTP/2 を使用した gRPC のサポートを向上させるための作業が進行中です。</span><span class="sxs-lookup"><span data-stu-id="54709-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="54709-146">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/AspNetCore/issues/9020)します。</span><span class="sxs-lookup"><span data-stu-id="54709-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54709-147">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="54709-147">Additional resources</span></span>

* [<span data-ttu-id="54709-148">gRPC C# コアライブラリ</span><span class="sxs-lookup"><span data-stu-id="54709-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)

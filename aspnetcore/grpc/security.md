---
title: gRPC での ASP.NET Core のセキュリティに関する考慮事項
author: jamesnk
description: ASP.NET Core のセキュリティに関する gRPC での考慮事項について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: 45ac0916a368cf68f4d40e14298a7628446989ee
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252813"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="37397-103">gRPC での ASP.NET Core のセキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="37397-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="37397-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="37397-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="37397-105">この記事では、.NET Core を使用する gRPC をセキュリティで保護することについて説明します。</span><span class="sxs-lookup"><span data-stu-id="37397-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="37397-106">トランスポート セキュリティ</span><span class="sxs-lookup"><span data-stu-id="37397-106">Transport security</span></span>

<span data-ttu-id="37397-107">gRPC のメッセージは、HTTP/2 を使用して送受信されます。</span><span class="sxs-lookup"><span data-stu-id="37397-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="37397-108">以下のことが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="37397-108">We recommend:</span></span>

* <span data-ttu-id="37397-109">[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用して、運用環境にある gRPC アプリ内のメッセージをセキュリティで保護します。</span><span class="sxs-lookup"><span data-stu-id="37397-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="37397-110">gRPC サービスは、セキュリティで保護されたポートでのみリッスンと応答を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="37397-110">gRPC services should only listen and respond over secured ports.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="37397-111">TLS は Kestrel で構成されます。</span><span class="sxs-lookup"><span data-stu-id="37397-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="37397-112">Kestrel エンドポイントの構成について詳しくは、[Kestrel のエンドポイント構成](xref:fundamentals/servers/kestrel/endpoints)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="37397-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>
::: moniker-end

::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="37397-113">TLS は Kestrel で構成されます。</span><span class="sxs-lookup"><span data-stu-id="37397-113">TLS is configured in Kestrel.</span></span> <span data-ttu-id="37397-114">Kestrel エンドポイントの構成について詳しくは、[Kestrel のエンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="37397-114">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
::: moniker-end

## <a name="exceptions"></a><span data-ttu-id="37397-115">例外</span><span class="sxs-lookup"><span data-stu-id="37397-115">Exceptions</span></span>

<span data-ttu-id="37397-116">例外メッセージは、通常、クライアントに公開してはいけない機密データと見なされます。</span><span class="sxs-lookup"><span data-stu-id="37397-116">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="37397-117">既定では、gRPC は、gRPC サービスによってスローされた例外の詳細をクライアントに送信しません。</span><span class="sxs-lookup"><span data-stu-id="37397-117">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="37397-118">クライアントはその代わりに、エラーが発生したことを示す一般的なメッセージを受信します。</span><span class="sxs-lookup"><span data-stu-id="37397-118">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="37397-119">クライアントへの例外メッセージの配信は、(開発やテストなどでは) [EnableDetailedErrors](xref:grpc/configuration#configure-services-options) を使用してオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="37397-119">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="37397-120">例外メッセージは、運用環境のアプリではクライアントに公開しないでください。</span><span class="sxs-lookup"><span data-stu-id="37397-120">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="37397-121">メッセージ サイズの制限</span><span class="sxs-lookup"><span data-stu-id="37397-121">Message size limits</span></span>

<span data-ttu-id="37397-122">gRPC のクライアントやサービスへの受信メッセージはメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="37397-122">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="37397-123">メッセージ サイズの制限は、gRPC で過剰なリソースが消費されないようにするためのメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="37397-123">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="37397-124">gRPC では、メッセージごとのサイズ制限を利用して受信メッセージと送信メッセージを管理します。</span><span class="sxs-lookup"><span data-stu-id="37397-124">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="37397-125">既定で、gRPC では受信メッセージは 4 MB に制限されます。</span><span class="sxs-lookup"><span data-stu-id="37397-125">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="37397-126">送信メッセージに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="37397-126">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="37397-127">サーバーでは、`AddGrpc` を使用して、アプリ内のすべてのサービスに対して gRPC メッセージの制限を構成できます。</span><span class="sxs-lookup"><span data-stu-id="37397-127">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="37397-128">`AddServiceOptions<TService>` を使用して、個々のサービスに対する制限を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="37397-128">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="37397-129">メッセージ サイズ制限の構成の詳細については、[gRPC の構成](xref:grpc/configuration)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="37397-129">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="37397-130">クライアント証明書の検証</span><span class="sxs-lookup"><span data-stu-id="37397-130">Client certificate validation</span></span>

<span data-ttu-id="37397-131">[クライアント証明書](https://tools.ietf.org/html/rfc5246#section-7.4.4)は、接続が確立されるときに最初に検証されます。</span><span class="sxs-lookup"><span data-stu-id="37397-131">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="37397-132">既定で、Kestrel では接続のクライアント証明書の追加の検証は実行されません。</span><span class="sxs-lookup"><span data-stu-id="37397-132">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="37397-133">クライアント証明書によって保護されている gRPC サービスでは、[Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) パッケージの使用が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="37397-133">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="37397-134">ASP.NET Core の証明書認証では、クライアント証明書に対して、以下を含む追加の検証が実行されます。</span><span class="sxs-lookup"><span data-stu-id="37397-134">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="37397-135">証明書に有効な拡張キー使用法 (EKU) がある</span><span class="sxs-lookup"><span data-stu-id="37397-135">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="37397-136">有効期間内である</span><span class="sxs-lookup"><span data-stu-id="37397-136">Is within its validity period</span></span>
* <span data-ttu-id="37397-137">証明書の失効について確認する</span><span class="sxs-lookup"><span data-stu-id="37397-137">Check certificate revocation</span></span>

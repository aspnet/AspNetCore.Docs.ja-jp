---
title: ASP.NET Core の認証サンプル
author: rick-anderson
description: ASP.NET Core リポジトリの認証サンプルへのリンクを提供します。
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110119"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="15aea-103">ASP.NET Core の認証サンプル</span><span class="sxs-lookup"><span data-stu-id="15aea-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="15aea-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="15aea-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="15aea-105">[ASP.NET Core リポジトリ](https://github.com/dotnet/aspnetcore)には、次の認証サンプル (分岐) が含まれてい `main` ます。</span><span class="sxs-lookup"><span data-stu-id="15aea-105">The [ASP.NET Core repository](https://github.com/dotnet/aspnetcore) contains the following authentication samples (`main` branch):</span></span>

* [<span data-ttu-id="15aea-106">要求の変換</span><span class="sxs-lookup"><span data-stu-id="15aea-106">Claims transformation</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="15aea-107">[Cookie 認証](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="15aea-107">[Cookie authentication](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="15aea-108">カスタム承認エラー応答</span><span class="sxs-lookup"><span data-stu-id="15aea-108">Custom authorization failure response</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [<span data-ttu-id="15aea-109">カスタムポリシープロバイダー-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="15aea-109">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="15aea-110">動的な認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="15aea-110">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="15aea-111">[外部要求](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="15aea-111">[External claims](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="15aea-112">cookie要求に基づいて、別の認証スキームを選択する</span><span class="sxs-lookup"><span data-stu-id="15aea-112">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="15aea-113">静的ファイルへのアクセスを制限する</span><span class="sxs-lookup"><span data-stu-id="15aea-113">Restricts access to static files</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a><span data-ttu-id="15aea-114">サンプルを入手して実行する</span><span class="sxs-lookup"><span data-stu-id="15aea-114">Obtain and run the samples</span></span>

<span data-ttu-id="15aea-115">この記事に記載されているサンプルリンクは、ASP.NET Core の今後のリリースのサンプルを提供します。</span><span class="sxs-lookup"><span data-stu-id="15aea-115">The sample links provided in this article provide samples for the upcoming release of ASP.NET Core.</span></span> <span data-ttu-id="15aea-116">現在のリリースまたは以前のリリースのサンプルを取得するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="15aea-116">To obtain a sample for the current release or a prior release, perform the following steps:</span></span>

* <span data-ttu-id="15aea-117">[ASP.NET Core リポジトリ](https://github.com/dotnet/aspnetcore)のリリースブランチを選択します () https://github.com/dotnet/aspnetcore) 。</span><span class="sxs-lookup"><span data-stu-id="15aea-117">Select the release branch of the [ASP.NET Core repository](https://github.com/dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore).</span></span> <span data-ttu-id="15aea-118">たとえば、分岐には `release/5.0` ASP.NET Core 5.0 リリースのサンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="15aea-118">For example, the `release/5.0` branch contains the samples for the ASP.NET Core 5.0 release.</span></span>
* <span data-ttu-id="15aea-119">ASP.NET Core リポジトリを複製またはダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="15aea-119">Clone or download the ASP.NET Core repository.</span></span>
* <span data-ttu-id="15aea-120">ローカルシステムで、ASP.NET Core リポジトリの複製に一致する [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) バージョンがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="15aea-120">On your local system, verify installation of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="15aea-121">フォルダー内のサンプルに移動 `aspnetcore/src/Security/samples` し、 [ `dotnet run` コマンド](/dotnet/core/tools/dotnet-run)を使用してサンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="15aea-121">Navigate to a sample in `aspnetcore/src/Security/samples` folder and run the sample with the [`dotnet run` command](/dotnet/core/tools/dotnet-run).</span></span>

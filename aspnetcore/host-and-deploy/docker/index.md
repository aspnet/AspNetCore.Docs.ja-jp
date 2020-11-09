---
title: Docker コンテナーで ASP.NET Core をホストする
author: rick-anderson
description: Docker コンテナーで ASP.NET Core アプリをホストする方法についてのリソースへのリンクを検出します。
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 6b4b011314be2481e6e71d7782fff6ee99cedb9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059781"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="c9a20-103">Docker コンテナーで ASP.NET Core をホストする</span><span class="sxs-lookup"><span data-stu-id="c9a20-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="c9a20-104">以下の記事では、Docker での ASP.NET Core アプリのホスティングについて説明されています。</span><span class="sxs-lookup"><span data-stu-id="c9a20-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="c9a20-105">コンテナーと Docker の概要</span><span class="sxs-lookup"><span data-stu-id="c9a20-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="c9a20-106">コンテナリゼーションは、ソフトウェア開発のアプローチであり、アプリケーションまたはサービス、その依存関係、その構成がコンテナー イメージとしてどのようにパッケージ化されるかを示します。</span><span class="sxs-lookup"><span data-stu-id="c9a20-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="c9a20-107">イメージは、テストしてからホストに展開することができます。</span><span class="sxs-lookup"><span data-stu-id="c9a20-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="c9a20-108">Docker について</span><span class="sxs-lookup"><span data-stu-id="c9a20-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="c9a20-109">オープン ソース プロジェクトの Docker が、クラウドまたはオンプレミスで実行できる移植可能な自己完結型のコンテナーとしてアプリの展開を自動化するしくみを説明します。</span><span class="sxs-lookup"><span data-stu-id="c9a20-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="c9a20-110">Docker に関する用語</span><span class="sxs-lookup"><span data-stu-id="c9a20-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="c9a20-111">Docker 技術の用語と定義について学習します。</span><span class="sxs-lookup"><span data-stu-id="c9a20-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="c9a20-112">Docker のコンテナー、イメージ、およびレジストリ</span><span class="sxs-lookup"><span data-stu-id="c9a20-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="c9a20-113">環境全体での一貫性のある展開のため、Docker コンテナー イメージがイメージ レジストリに保存されるしくみを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9a20-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="c9a20-114"><xref:host-and-deploy/docker/building-net-docker-images> ASP.NET Core アプリをビルドし、Docker で動作させる方法について学習します。</span><span class="sxs-lookup"><span data-stu-id="c9a20-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="c9a20-115">Microsoft によって管理される Docker イメージを確認し、ユース ケースを調査します。</span><span class="sxs-lookup"><span data-stu-id="c9a20-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="c9a20-116">Visual Studio コンテナー ツール</span><span class="sxs-lookup"><span data-stu-id="c9a20-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="c9a20-117">Docker for Windows 上での .NET Framework または .NET Core のいずれかをターゲットとする ASP.NET Core アプリのビルド、デバッグ、および実行が Visual Studio によってどのようにサポートされるかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c9a20-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="c9a20-118">Windows と Linux の両方のコンテナーがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c9a20-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="c9a20-119">Azure Container Registry に発行する</span><span class="sxs-lookup"><span data-stu-id="c9a20-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="c9a20-120">Visual Studio Container Tools 拡張機能を使用して、PowerShell を使用する Azure で Docker ホストに ASP.NET Core アプリを展開する方法を確認します。</span><span class="sxs-lookup"><span data-stu-id="c9a20-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="c9a20-121">プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する</span><span class="sxs-lookup"><span data-stu-id="c9a20-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="c9a20-122">プロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c9a20-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="c9a20-123">プロキシ経由で要求を渡すと、スキームやクライアントの IP アドレスなど、元の要求に関する情報が不明になることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="c9a20-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="c9a20-124">その場合、要求に関する情報をアプリに手動で転送しなければならないことがあります。</span><span class="sxs-lookup"><span data-stu-id="c9a20-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="c9a20-125">[Docker と小さなコンテナーを使用しての GC](xref:performance/memory#sc) 小さなコンテナーを使用しての GC の選択について。</span><span class="sxs-lookup"><span data-stu-id="c9a20-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>
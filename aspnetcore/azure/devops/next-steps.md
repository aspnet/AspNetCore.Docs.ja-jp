---
title: 次のステップ - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: ASP.NET Core および Azure を使用した DevOps のための追加のラーニング パス。
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056596"
---
# <a name="next-steps"></a><span data-ttu-id="a16a5-103">次の手順</span><span class="sxs-lookup"><span data-stu-id="a16a5-103">Next steps</span></span>

<span data-ttu-id="a16a5-104">このガイドでは ASP.NET Core サンプル アプリ用の DevOps パイプラインを作成しました。</span><span class="sxs-lookup"><span data-stu-id="a16a5-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="a16a5-105">おめでとうございます! </span><span class="sxs-lookup"><span data-stu-id="a16a5-105">Congratulations!</span></span> <span data-ttu-id="a16a5-106">Azure App Service に ASP.NET Core Web アプリを発行し、変更の継続的インテグレーションを自動化する方法についてご理解いただけたと思います。</span><span class="sxs-lookup"><span data-stu-id="a16a5-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="a16a5-107">Web ホスティングと DevOps 以外にも、Azure には、ASP.NET Core の開発者に役立つ数多くのサービスとしてのプラットフォーム (PaaS) サービスがあります。</span><span class="sxs-lookup"><span data-stu-id="a16a5-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="a16a5-108">このセクションでは、いくつかの最も一般的に使用されるサービスの概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="a16a5-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="a16a5-109">ストレージとデータベース</span><span class="sxs-lookup"><span data-stu-id="a16a5-109">Storage and databases</span></span>

<span data-ttu-id="a16a5-110">[Redis Cache](/azure/redis-cache/) は、高スループットで、待機時間の短い、サービスとして使用できるデータ キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="a16a5-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="a16a5-111">これは、ページ出力のキャッシュ、データベース要求の削減、アプリの複数のインスタンスにわたって ASP.NET Core セッション状態の提供に使用できます。</span><span class="sxs-lookup"><span data-stu-id="a16a5-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="a16a5-112">[Azure Storage](/azure/storage/) は、Azure の非常にスケーラブルなクラウド ストレージです。</span><span class="sxs-lookup"><span data-stu-id="a16a5-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="a16a5-113">開発者は、信頼性の高いメッセージ キューに対して [Queue Storage](/azure/storage/queues/storage-queues-introduction) を利用できます。また、[Table Storage](/azure/storage/tables/table-storage-overview) は、大規模な半構造化データ セットを使用した迅速な開発のために設計された NoSQL キー値ストアです。</span><span class="sxs-lookup"><span data-stu-id="a16a5-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="a16a5-114">[Azure SQL Database](/azure/sql-database/) は、Microsoft SQL Server エンジンを使用して、使い慣れたリレーショナル データベース機能をサービスとして提供します。</span><span class="sxs-lookup"><span data-stu-id="a16a5-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="a16a5-115">[Cosmos DB](/azure/cosmos-db/) は、グローバル分散型のマルチモデル NoSQL データベース サービスです。</span><span class="sxs-lookup"><span data-stu-id="a16a5-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="a16a5-116">SQL API (旧称 DocumentDB)、Cassandra、MongoDB など、複数の API を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a16a5-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="a16a5-117">[Azure Active Directory](/azure/active-directory/) と [Azure Active Directory B2C](/azure/active-directory-b2c/) はどちらも ID サービスです。</span><span class="sxs-lookup"><span data-stu-id="a16a5-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="a16a5-118">Azure Active Directory は、エンタープライズ シナリオ向けに設計されており、Azure AD B2B (企業間) コラボレーションを可能にします。一方、Azure Active Directory B2C は、ソーシャル ネットワークのサインインなど、企業と消費者間のシナリオを対象としています。</span><span class="sxs-lookup"><span data-stu-id="a16a5-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="a16a5-119">携帯</span><span class="sxs-lookup"><span data-stu-id="a16a5-119">Mobile</span></span>

<span data-ttu-id="a16a5-120">[Notification Hubs](/azure/notification-hubs/) は、さまざまな種類のデバイスで実行されているアプリに何百万ものメッセージを迅速に送信するための、マルチプラットフォームのスケーラブルなプッシュ通知エンジンです。</span><span class="sxs-lookup"><span data-stu-id="a16a5-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="a16a5-121">Web インフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="a16a5-121">Web infrastructure</span></span>

<span data-ttu-id="a16a5-122">[Azure Container Service](/azure/aks/) では、コンテナー オーケストレーションの専門知識がなくても、コンテナー化されたアプリを迅速かつ簡単にデプロイおよび管理できるように、ホストされている Kubernetes 環境を管理します。</span><span class="sxs-lookup"><span data-stu-id="a16a5-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="a16a5-123">[Azure Search](/azure/search/) は、プライベートの異種コンテンツに対してエンタープライズ検索ソリューションを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a16a5-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="a16a5-124">[Service Fabric](/azure/service-fabric/) は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのパッケージ化、デプロイ、管理を簡単に行うことができる分散システム プラットフォームです。</span><span class="sxs-lookup"><span data-stu-id="a16a5-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>

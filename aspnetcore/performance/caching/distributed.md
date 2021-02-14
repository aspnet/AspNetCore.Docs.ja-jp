---
title: ASP.NET Core での分散キャッシュ
author: rick-anderson
description: 特にクラウドまたはサーバーファーム環境で、ASP.NET Core 分散キャッシュを使用してアプリのパフォーマンスとスケーラビリティを向上させる方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 6f89046f2e1805111dd81b3282253a72a7c6ea09
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281013"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="94ea1-103">ASP.NET Core での分散キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="94ea1-104">[Mohsin Nasir](https://github.com/mohsinnasir)と[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="94ea1-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94ea1-105">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="94ea1-106">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="94ea1-107">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="94ea1-108">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="94ea1-109">は、複数のサーバーに対する複数の要求にわたっ *て一貫し* ています。</span><span class="sxs-lookup"><span data-stu-id="94ea1-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="94ea1-110">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="94ea1-111">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="94ea1-111">Doesn't use local memory.</span></span>

<span data-ttu-id="94ea1-112">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="94ea1-113">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="94ea1-114">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="94ea1-115">どの実装が選択されているかにかかわらず、アプリはインターフェイスを使用してキャッシュと対話し <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="94ea1-116">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="94ea1-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94ea1-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="94ea1-117">Prerequisites</span></span>

<span data-ttu-id="94ea1-118">SQL Server 分散キャッシュを使用するには、パッケージ [への参照を追加します](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) 。</span><span class="sxs-lookup"><span data-stu-id="94ea1-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="94ea1-119">Redis 分散キャッシュを使用するには、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="94ea1-120">NCache 分散キャッシュを使用するには、パッケージ参照を [Ncache. Microsoft. Extensions. cache-control. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="94ea1-121">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="94ea1-121">IDistributedCache interface</span></span>

<span data-ttu-id="94ea1-122">インターフェイスには、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="94ea1-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="94ea1-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 文字列キーを受け取り、 `byte[]` キャッシュ内に存在する場合は、キャッシュされた項目を配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="94ea1-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : 文字列キーを使用して、キャッシュに項目 (配列として `byte[]` ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="94ea1-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>: は、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> キーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="94ea1-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="94ea1-127">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="94ea1-127">Establish distributed caching services</span></span>

<span data-ttu-id="94ea1-128">のの実装を <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> に登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="94ea1-129">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="94ea1-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="94ea1-130">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="94ea1-131">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="94ea1-132">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="94ea1-133">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="94ea1-134">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-134">Distributed Memory Cache</span></span>

<span data-ttu-id="94ea1-135">分散メモリキャッシュ ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) は、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 項目をメモリに格納するのフレームワーク提供の実装です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="94ea1-136">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="94ea1-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="94ea1-137">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="94ea1-138">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="94ea1-139">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="94ea1-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="94ea1-140">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="94ea1-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="94ea1-141">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="94ea1-142">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="94ea1-143">このサンプルアプリでは、の開発環境でアプリを実行するときに、分散メモリキャッシュを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="94ea1-144">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="94ea1-145">分散型 SQL Server キャッシュの実装 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) を使用すると、分散キャッシュで SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="94ea1-146">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、ツールを使用し `sql-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="94ea1-147">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="94ea1-148">コマンドを実行して SQL Server にテーブルを作成し `sql-cache create` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="94ea1-149">SQL Server インスタンス ()、 `Data Source` データベース ( `Initial Catalog` )、スキーマ (など `dbo` )、テーブル名 (など) を指定し `TestCache` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="94ea1-150">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="94ea1-151">ツールによって作成されたテーブルには、 `sql-cache` 次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="94ea1-153">アプリでは、ではなく、のインスタンスを使用してキャッシュ値を操作する必要があり <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="94ea1-154">このサンプルアプリは、 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> の開発環境以外で実装されてい `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="94ea1-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>通常、(および必要に応じ <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> て <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または appsettings に格納され *appsettings.json* / *ます)。ENVIRONMENT} json* ファイル)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="94ea1-156">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="94ea1-157">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-157">Distributed Redis Cache</span></span>

<span data-ttu-id="94ea1-158">[Redis](https://redis.io/) は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span>  <span data-ttu-id="94ea1-159">Azure でホストされる ASP.NET Core アプリの [Azure Redis Cache](https://azure.microsoft.com/services/cache/) を構成し、ローカル開発に Azure Redis Cache を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-159">You can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app, and use an Azure Redis Cache for local development.</span></span>

<span data-ttu-id="94ea1-160">アプリは、インスタンス () を使用してキャッシュの実装を構成し <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>).</span></span>

<span data-ttu-id="94ea1-161">詳細については、「[Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="94ea1-161">For more information, see [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span></span>

<span data-ttu-id="94ea1-162">ローカル Redis cache に対する別のアプローチについては、 [この GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/19542) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="94ea1-162">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/19542) for a discussion on alternative approaches to a local Redis cache.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="94ea1-163">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-163">Distributed NCache Cache</span></span>

<span data-ttu-id="94ea1-164">[Ncache](https://github.com/Alachisoft/NCache) は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="94ea1-164">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="94ea1-165">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-165">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="94ea1-166">ローカルコンピューターに NCache をインストールして構成する方法については、「 [はじめに Guide For Windows (.net および .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="94ea1-166">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="94ea1-167">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="94ea1-167">To configure NCache:</span></span>

1. <span data-ttu-id="94ea1-168">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-168">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="94ea1-169">[Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-169">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="94ea1-170">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-170">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="94ea1-171">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="94ea1-171">Use the distributed cache</span></span>

<span data-ttu-id="94ea1-172">インターフェイスを使用するには <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、アプリの任意のコンストラクターからのインスタンスを要求し <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-172">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="94ea1-173">インスタンスは、 [依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-173">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="94ea1-174">サンプルアプリが起動すると、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> がに挿入され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-174">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="94ea1-175">現在の時刻はを使用してキャッシュされ <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> ます (詳細については、「 [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-175">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="94ea1-176">サンプルアプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、 `IndexModel` インデックスページで使用するために、に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-176">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="94ea1-177">インデックスページが読み込まれるたびに、キャッシュがでキャッシュされた時間についてチェックされ `OnGetAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-177">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="94ea1-178">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-178">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="94ea1-179">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた *時間* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-179">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="94ea1-180">[キャッシュされた時間の **リセット** ] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-180">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="94ea1-181">このボタンをクリックすると、ハンドラーメソッドがトリガーさ `OnPostResetCachedTime` れます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-181">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="94ea1-182">インスタンスに対してシングルトンまたはスコープ設定された有効期間を使用する必要はありません <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (少なくとも組み込み実装の場合)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-182">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="94ea1-183">また、DI を使用する代わりに、必要に応じてインスタンスを作成することもできます <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が、コードでインスタンスを作成すると、コードのテストが難しくなり、 [明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-183">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="94ea1-184">推奨事項</span><span class="sxs-lookup"><span data-stu-id="94ea1-184">Recommendations</span></span>

<span data-ttu-id="94ea1-185">アプリに最適なの実装を決定する際には、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="94ea1-185">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="94ea1-186">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="94ea1-186">Existing infrastructure</span></span>
* <span data-ttu-id="94ea1-187">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="94ea1-187">Performance requirements</span></span>
* <span data-ttu-id="94ea1-188">コスト</span><span class="sxs-lookup"><span data-stu-id="94ea1-188">Cost</span></span>
* <span data-ttu-id="94ea1-189">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="94ea1-189">Team experience</span></span>

<span data-ttu-id="94ea1-190">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-190">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="94ea1-191">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-191">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="94ea1-192">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-192">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="94ea1-193">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-193">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="94ea1-194">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-194">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="94ea1-195">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-195">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94ea1-196">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="94ea1-196">Additional resources</span></span>

* [<span data-ttu-id="94ea1-197">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-197">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="94ea1-198">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="94ea1-198">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="94ea1-199">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94ea1-199">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="94ea1-200">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-200">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="94ea1-201">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-201">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="94ea1-202">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-202">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="94ea1-203">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-203">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="94ea1-204">は、複数のサーバーに対する複数の要求にわたっ *て一貫し* ています。</span><span class="sxs-lookup"><span data-stu-id="94ea1-204">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="94ea1-205">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-205">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="94ea1-206">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="94ea1-206">Doesn't use local memory.</span></span>

<span data-ttu-id="94ea1-207">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-207">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="94ea1-208">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-208">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="94ea1-209">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-209">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="94ea1-210">どの実装が選択されているかにかかわらず、アプリはインターフェイスを使用してキャッシュと対話し <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-210">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="94ea1-211">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="94ea1-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94ea1-212">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="94ea1-212">Prerequisites</span></span>

<span data-ttu-id="94ea1-213">SQL Server 分散キャッシュを使用するには、 [メタパッケージ](xref:fundamentals/metapackage-app) を参照するか、AspNetCore [パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) 参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-213">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="94ea1-214">Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app) を参照し、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-214">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="94ea1-215">Redis パッケージはパッケージに含まれていない `Microsoft.AspNetCore.App` ため、プロジェクトファイルで redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-215">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="94ea1-216">NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app) を参照し、パッケージ参照を ncache.. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-216">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="94ea1-217">NCache パッケージはパッケージに含まれていない `Microsoft.AspNetCore.App` ため、プロジェクトファイル内で個別に NCache パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-217">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="94ea1-218">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="94ea1-218">IDistributedCache interface</span></span>

<span data-ttu-id="94ea1-219">インターフェイスには、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="94ea1-219">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="94ea1-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 文字列キーを受け取り、 `byte[]` キャッシュ内に存在する場合は、キャッシュされた項目を配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="94ea1-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : 文字列キーを使用して、キャッシュに項目 (配列として `byte[]` ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="94ea1-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>: は、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> キーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="94ea1-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="94ea1-224">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="94ea1-224">Establish distributed caching services</span></span>

<span data-ttu-id="94ea1-225">のの実装を <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> に登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-225">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="94ea1-226">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="94ea1-226">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="94ea1-227">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-227">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="94ea1-228">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-228">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="94ea1-229">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-229">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="94ea1-230">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-230">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="94ea1-231">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-231">Distributed Memory Cache</span></span>

<span data-ttu-id="94ea1-232">分散メモリキャッシュ ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) は、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 項目をメモリに格納するのフレームワーク提供の実装です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-232">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="94ea1-233">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="94ea1-233">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="94ea1-234">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-234">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="94ea1-235">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-235">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="94ea1-236">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="94ea1-236">In development and testing scenarios.</span></span>
* <span data-ttu-id="94ea1-237">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="94ea1-237">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="94ea1-238">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-238">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="94ea1-239">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-239">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="94ea1-240">このサンプルアプリでは、の開発環境でアプリを実行するときに、分散メモリキャッシュを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-240">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="94ea1-241">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-241">Distributed SQL Server Cache</span></span>

<span data-ttu-id="94ea1-242">分散型 SQL Server キャッシュの実装 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) を使用すると、分散キャッシュで SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-242">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="94ea1-243">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、ツールを使用し `sql-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-243">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="94ea1-244">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-244">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="94ea1-245">コマンドを実行して SQL Server にテーブルを作成し `sql-cache create` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-245">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="94ea1-246">SQL Server インスタンス ()、 `Data Source` データベース ( `Initial Catalog` )、スキーマ (など `dbo` )、テーブル名 (など) を指定し `TestCache` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-246">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="94ea1-247">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-247">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="94ea1-248">ツールによって作成されたテーブルには、 `sql-cache` 次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-248">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="94ea1-250">アプリでは、ではなく、のインスタンスを使用してキャッシュ値を操作する必要があり <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-250">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="94ea1-251">このサンプルアプリは、 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> の開発環境以外で実装されてい `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-251">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="94ea1-252"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>通常、(および必要に応じ <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> て <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または appsettings に格納され *appsettings.json* / *ます)。ENVIRONMENT} json* ファイル)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-252">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="94ea1-253">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-253">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="94ea1-254">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-254">Distributed Redis Cache</span></span>

<span data-ttu-id="94ea1-255">[Redis](https://redis.io/) は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-255">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="94ea1-256">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの [Azure Redis Cache](https://azure.microsoft.com/services/cache/) を構成できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-256">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="94ea1-257">アプリでは、 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> の開発以外の環境でインスタンス () を使用して、キャッシュの実装を構成し <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-257">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="94ea1-258">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-258">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="94ea1-259">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-259">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="94ea1-260">`redis-server`コマンドプロンプトからを実行します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-260">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="94ea1-261">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-261">Distributed NCache Cache</span></span>

<span data-ttu-id="94ea1-262">[Ncache](https://github.com/Alachisoft/NCache) は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="94ea1-262">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="94ea1-263">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-263">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="94ea1-264">ローカルコンピューターに NCache をインストールして構成する方法については、「 [はじめに Guide For Windows (.net および .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="94ea1-264">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="94ea1-265">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="94ea1-265">To configure NCache:</span></span>

1. <span data-ttu-id="94ea1-266">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-266">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="94ea1-267">[Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-267">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="94ea1-268">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-268">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="94ea1-269">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="94ea1-269">Use the distributed cache</span></span>

<span data-ttu-id="94ea1-270">インターフェイスを使用するには <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、アプリの任意のコンストラクターからのインスタンスを要求し <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-270">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="94ea1-271">インスタンスは、 [依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-271">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="94ea1-272">サンプルアプリが起動すると、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> がに挿入され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-272">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="94ea1-273">現在の時刻はを使用してキャッシュされ <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> ます (詳細については、「 [Web Host: IApplicationLifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-273">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="94ea1-274">サンプルアプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、 `IndexModel` インデックスページで使用するために、に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-274">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="94ea1-275">インデックスページが読み込まれるたびに、キャッシュがでキャッシュされた時間についてチェックされ `OnGetAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-275">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="94ea1-276">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-276">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="94ea1-277">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた *時間* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-277">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="94ea1-278">[キャッシュされた時間の **リセット** ] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-278">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="94ea1-279">このボタンをクリックすると、ハンドラーメソッドがトリガーさ `OnPostResetCachedTime` れます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-279">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="94ea1-280">インスタンスに対してシングルトンまたはスコープ設定された有効期間を使用する必要はありません <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (少なくとも組み込み実装の場合)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-280">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="94ea1-281">また、DI を使用する代わりに、必要に応じてインスタンスを作成することもできます <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が、コードでインスタンスを作成すると、コードのテストが難しくなり、 [明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-281">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="94ea1-282">推奨事項</span><span class="sxs-lookup"><span data-stu-id="94ea1-282">Recommendations</span></span>

<span data-ttu-id="94ea1-283">アプリに最適なの実装を決定する際には、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="94ea1-283">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="94ea1-284">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="94ea1-284">Existing infrastructure</span></span>
* <span data-ttu-id="94ea1-285">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="94ea1-285">Performance requirements</span></span>
* <span data-ttu-id="94ea1-286">コスト</span><span class="sxs-lookup"><span data-stu-id="94ea1-286">Cost</span></span>
* <span data-ttu-id="94ea1-287">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="94ea1-287">Team experience</span></span>

<span data-ttu-id="94ea1-288">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-288">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="94ea1-289">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-289">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="94ea1-290">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-290">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="94ea1-291">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-291">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="94ea1-292">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-292">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="94ea1-293">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-293">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94ea1-294">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="94ea1-294">Additional resources</span></span>

* [<span data-ttu-id="94ea1-295">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-295">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="94ea1-296">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="94ea1-296">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="94ea1-297">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94ea1-297">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="94ea1-298">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-298">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="94ea1-299">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-299">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="94ea1-300">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-300">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="94ea1-301">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-301">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="94ea1-302">は、複数のサーバーに対する複数の要求にわたっ *て一貫し* ています。</span><span class="sxs-lookup"><span data-stu-id="94ea1-302">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="94ea1-303">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-303">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="94ea1-304">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="94ea1-304">Doesn't use local memory.</span></span>

<span data-ttu-id="94ea1-305">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-305">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="94ea1-306">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-306">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="94ea1-307">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-307">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="94ea1-308">どの実装が選択されているかにかかわらず、アプリはインターフェイスを使用してキャッシュと対話し <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-308">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="94ea1-309">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="94ea1-309">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94ea1-310">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="94ea1-310">Prerequisites</span></span>

<span data-ttu-id="94ea1-311">SQL Server 分散キャッシュを使用するには、 [メタパッケージ](xref:fundamentals/metapackage-app) を参照するか、AspNetCore [パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) 参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-311">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="94ea1-312">Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app) を参照し [て、パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 参照をパッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-312">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="94ea1-313">Redis パッケージはパッケージに含まれていない `Microsoft.AspNetCore.App` ため、プロジェクトファイルで redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-313">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="94ea1-314">NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app) を参照し、パッケージ参照を ncache.. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-314">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="94ea1-315">NCache パッケージはパッケージに含まれていない `Microsoft.AspNetCore.App` ため、プロジェクトファイル内で個別に NCache パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-315">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="94ea1-316">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="94ea1-316">IDistributedCache interface</span></span>

<span data-ttu-id="94ea1-317">インターフェイスには、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="94ea1-317">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="94ea1-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 文字列キーを受け取り、 `byte[]` キャッシュ内に存在する場合は、キャッシュされた項目を配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="94ea1-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : 文字列キーを使用して、キャッシュに項目 (配列として `byte[]` ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="94ea1-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>: は、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> キーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="94ea1-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="94ea1-322">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="94ea1-322">Establish distributed caching services</span></span>

<span data-ttu-id="94ea1-323">のの実装を <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> に登録 `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-323">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="94ea1-324">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="94ea1-324">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="94ea1-325">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-325">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="94ea1-326">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-326">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="94ea1-327">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-327">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="94ea1-328">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-328">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="94ea1-329">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-329">Distributed Memory Cache</span></span>

<span data-ttu-id="94ea1-330">分散メモリキャッシュ ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) は、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 項目をメモリに格納するのフレームワーク提供の実装です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-330">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="94ea1-331">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="94ea1-331">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="94ea1-332">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-332">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="94ea1-333">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-333">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="94ea1-334">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="94ea1-334">In development and testing scenarios.</span></span>
* <span data-ttu-id="94ea1-335">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="94ea1-335">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="94ea1-336">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-336">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="94ea1-337">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-337">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="94ea1-338">このサンプルアプリでは、の開発環境でアプリを実行するときに、分散メモリキャッシュを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-338">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="94ea1-339">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-339">Distributed SQL Server Cache</span></span>

<span data-ttu-id="94ea1-340">分散型 SQL Server キャッシュの実装 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) を使用すると、分散キャッシュで SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-340">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="94ea1-341">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、ツールを使用し `sql-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-341">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="94ea1-342">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-342">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="94ea1-343">コマンドを実行して SQL Server にテーブルを作成し `sql-cache create` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-343">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="94ea1-344">SQL Server インスタンス ()、 `Data Source` データベース ( `Initial Catalog` )、スキーマ (など `dbo` )、テーブル名 (など) を指定し `TestCache` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-344">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="94ea1-345">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-345">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="94ea1-346">ツールによって作成されたテーブルには、 `sql-cache` 次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-346">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="94ea1-348">アプリでは、ではなく、のインスタンスを使用してキャッシュ値を操作する必要があり <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-348">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="94ea1-349">このサンプルアプリは、 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> の開発環境以外で実装されてい `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-349">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="94ea1-350"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>通常、(および必要に応じ <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> て <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または appsettings に格納され *appsettings.json* / *ます)。ENVIRONMENT} json* ファイル)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-350">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="94ea1-351">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-351">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="94ea1-352">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-352">Distributed Redis Cache</span></span>

<span data-ttu-id="94ea1-353">[Redis](https://redis.io/) は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-353">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="94ea1-354">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの [Azure Redis Cache](https://azure.microsoft.com/services/cache/) を構成できます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-354">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="94ea1-355">アプリは、インスタンス () を使用してキャッシュの実装を構成し <xref:Microsoft.Extensions.Caching.Redis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-355">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="94ea1-356">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-356">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="94ea1-357">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-357">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="94ea1-358">`redis-server`コマンドプロンプトからを実行します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-358">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="94ea1-359">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="94ea1-359">Distributed NCache Cache</span></span>

<span data-ttu-id="94ea1-360">[Ncache](https://github.com/Alachisoft/NCache) は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="94ea1-360">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="94ea1-361">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-361">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="94ea1-362">ローカルコンピューターに NCache をインストールして構成する方法については、「 [はじめに Guide For Windows (.net および .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="94ea1-362">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="94ea1-363">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="94ea1-363">To configure NCache:</span></span>

1. <span data-ttu-id="94ea1-364">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-364">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="94ea1-365">[Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-365">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="94ea1-366">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-366">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="94ea1-367">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="94ea1-367">Use the distributed cache</span></span>

<span data-ttu-id="94ea1-368">インターフェイスを使用するには <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、アプリの任意のコンストラクターからのインスタンスを要求し <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-368">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="94ea1-369">インスタンスは、 [依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-369">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="94ea1-370">サンプルアプリが起動すると、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> がに挿入され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-370">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="94ea1-371">現在の時刻はを使用してキャッシュされ <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> ます (詳細については、「 [Web Host: IApplicationLifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-371">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="94ea1-372">サンプルアプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、 `IndexModel` インデックスページで使用するために、に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-372">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="94ea1-373">インデックスページが読み込まれるたびに、キャッシュがでキャッシュされた時間についてチェックされ `OnGetAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-373">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="94ea1-374">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-374">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="94ea1-375">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた *時間* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-375">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="94ea1-376">[キャッシュされた時間の **リセット** ] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-376">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="94ea1-377">このボタンをクリックすると、ハンドラーメソッドがトリガーさ `OnPostResetCachedTime` れます。</span><span class="sxs-lookup"><span data-stu-id="94ea1-377">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="94ea1-378">インスタンスに対してシングルトンまたはスコープ設定された有効期間を使用する必要はありません <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (少なくとも組み込み実装の場合)。</span><span class="sxs-lookup"><span data-stu-id="94ea1-378">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="94ea1-379">また、DI を使用する代わりに、必要に応じてインスタンスを作成することもできます <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が、コードでインスタンスを作成すると、コードのテストが難しくなり、 [明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-379">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="94ea1-380">推奨事項</span><span class="sxs-lookup"><span data-stu-id="94ea1-380">Recommendations</span></span>

<span data-ttu-id="94ea1-381">アプリに最適なの実装を決定する際には、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="94ea1-381">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="94ea1-382">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="94ea1-382">Existing infrastructure</span></span>
* <span data-ttu-id="94ea1-383">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="94ea1-383">Performance requirements</span></span>
* <span data-ttu-id="94ea1-384">コスト</span><span class="sxs-lookup"><span data-stu-id="94ea1-384">Cost</span></span>
* <span data-ttu-id="94ea1-385">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="94ea1-385">Team experience</span></span>

<span data-ttu-id="94ea1-386">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-386">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="94ea1-387">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="94ea1-387">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="94ea1-388">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-388">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="94ea1-389">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="94ea1-389">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="94ea1-390">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="94ea1-390">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="94ea1-391">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="94ea1-391">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94ea1-392">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="94ea1-392">Additional resources</span></span>

* [<span data-ttu-id="94ea1-393">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="94ea1-393">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="94ea1-394">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="94ea1-394">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="94ea1-395">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94ea1-395">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

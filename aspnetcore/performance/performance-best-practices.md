---
title: ASP.NET Core パフォーマンスのベストプラクティス
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスを向上させ、一般的なパフォーマンスの問題を回避するためのヒントです。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: a3fc398569fafefc0b4634e80433a5d4e0e1b4ff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061003"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="31adc-103">ASP.NET Core パフォーマンスのベストプラクティス</span><span class="sxs-lookup"><span data-stu-id="31adc-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="31adc-104">作成者: [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="31adc-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="31adc-105">この記事では、ASP.NET Core のパフォーマンスのベストプラクティスに関するガイドラインを示します。</span><span class="sxs-lookup"><span data-stu-id="31adc-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="31adc-106">積極的にキャッシュ</span><span class="sxs-lookup"><span data-stu-id="31adc-106">Cache aggressively</span></span>

<span data-ttu-id="31adc-107">キャッシュについては、このドキュメントのいくつかの部分で説明します。</span><span class="sxs-lookup"><span data-stu-id="31adc-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="31adc-108">詳細については、「<xref:performance/caching/response>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="31adc-109">ホットコードパスについて</span><span class="sxs-lookup"><span data-stu-id="31adc-109">Understand hot code paths</span></span>

<span data-ttu-id="31adc-110">このドキュメントでは、 *ホットコードパス* は、頻繁に呼び出される、実行時間の大半が発生するコードパスとして定義されています。</span><span class="sxs-lookup"><span data-stu-id="31adc-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="31adc-111">ホットコードパスは、通常、アプリのスケールアウトとパフォーマンスを制限し、このドキュメントのいくつかの部分で説明されています。</span><span class="sxs-lookup"><span data-stu-id="31adc-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="31adc-112">ブロック呼び出しを回避する</span><span class="sxs-lookup"><span data-stu-id="31adc-112">Avoid blocking calls</span></span>

<span data-ttu-id="31adc-113">ASP.NET Core アプリは、多数の要求を同時に処理するように設計する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="31adc-114">非同期 Api を使用すると、スレッドの小さなプールで、ブロックしている呼び出しを待機せずに、数千の同時要求を処理することができます。</span><span class="sxs-lookup"><span data-stu-id="31adc-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="31adc-115">実行時間の長い同期タスクが完了するのを待機するのではなく、スレッドが別の要求で作業できます。</span><span class="sxs-lookup"><span data-stu-id="31adc-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="31adc-116">ASP.NET Core アプリのパフォーマンスに関する一般的な問題は、非同期の呼び出しをブロックしていることです。</span><span class="sxs-lookup"><span data-stu-id="31adc-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="31adc-117">多くの同期ブロッキング呼び出しは、 [スレッドプールの枯渇](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) と応答時間の低下につながります。</span><span class="sxs-lookup"><span data-stu-id="31adc-117">Many synchronous blocking calls lead to [Thread Pool starvation](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) and degraded response times.</span></span>

<span data-ttu-id="31adc-118">**実行** しない:</span><span class="sxs-lookup"><span data-stu-id="31adc-118">**Do not** :</span></span>

* <span data-ttu-id="31adc-119">[Task. Wait](/dotnet/api/system.threading.tasks.task.wait)または[task. Result](/dotnet/api/system.threading.tasks.task-1.result)を呼び出して、非同期実行をブロックします。</span><span class="sxs-lookup"><span data-stu-id="31adc-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="31adc-120">共通コードパスのロックを取得します。</span><span class="sxs-lookup"><span data-stu-id="31adc-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="31adc-121">ASP.NET Core アプリは、コードを並列実行するように設計されている場合に最もパフォーマンスが高くなります。</span><span class="sxs-lookup"><span data-stu-id="31adc-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>
* <span data-ttu-id="31adc-122">タスクを呼び出し [ます。実行](/dotnet/api/system.threading.tasks.task.run) してすぐに待機します。</span><span class="sxs-lookup"><span data-stu-id="31adc-122">Call [Task.Run](/dotnet/api/system.threading.tasks.task.run) and immediately await it.</span></span> <span data-ttu-id="31adc-123">ASP.NET Core は、通常のスレッドプールのスレッドで既にアプリコードを実行しているため、タスクを呼び出すと、余分な不要なスレッドプールスケジュールが生成されます。</span><span class="sxs-lookup"><span data-stu-id="31adc-123">ASP.NET Core already runs app code on normal Thread Pool threads, so calling Task.Run only results in extra unnecessary Thread Pool scheduling.</span></span> <span data-ttu-id="31adc-124">スケジュールされたコードがスレッドをブロックする場合でも、タスクを実行しても、そのようなことはできません。</span><span class="sxs-lookup"><span data-stu-id="31adc-124">Even if the scheduled code would block a thread, Task.Run does not prevent that.</span></span>

<span data-ttu-id="31adc-125">**操作** :</span><span class="sxs-lookup"><span data-stu-id="31adc-125">**Do** :</span></span>

* <span data-ttu-id="31adc-126">[ホットコードパス](#understand-hot-code-paths)を非同期にします。</span><span class="sxs-lookup"><span data-stu-id="31adc-126">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="31adc-127">非同期 API が使用可能な場合は、データアクセス、i/o、長時間実行される操作 Api を非同期に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="31adc-127">Call data access, I/O, and long-running operations APIs asynchronously if an asynchronous API is available.</span></span> <span data-ttu-id="31adc-128">同期 API を非同期にするには、Run **を使用しないでください** [。](/dotnet/api/system.threading.tasks.task.run)</span><span class="sxs-lookup"><span data-stu-id="31adc-128">Do **not** use [Task.Run](/dotnet/api/system.threading.tasks.task.run) to make a synchronous API asynchronous.</span></span>
* <span data-ttu-id="31adc-129">コントローラー/ :::no-loc(Razor)::: ページのアクションを非同期にします。</span><span class="sxs-lookup"><span data-stu-id="31adc-129">Make controller/:::no-loc(Razor)::: Page actions asynchronous.</span></span> <span data-ttu-id="31adc-130">非同期 [/await](/dotnet/csharp/programming-guide/concepts/async/) パターンを活用するために、呼び出し履歴全体が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="31adc-130">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="31adc-131">[Perfview](https://github.com/Microsoft/perfview)などのプロファイラーを使用して、[スレッドプール](/windows/desktop/procthread/thread-pools)に頻繁に追加されるスレッドを見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="31adc-131">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="31adc-132">イベントは、スレッド `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` プールに追加されたスレッドを示します。</span><span class="sxs-lookup"><span data-stu-id="31adc-132">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="31adc-133">IEnumerable \<T> または IAsyncEnumerable を返します\<T></span><span class="sxs-lookup"><span data-stu-id="31adc-133">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="31adc-134">`IEnumerable<T>`アクションから戻ると、シリアライザーによって同期コレクションの反復処理が行われます。</span><span class="sxs-lookup"><span data-stu-id="31adc-134">Returning `IEnumerable<T>` from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="31adc-135">その結果、呼び出しがブロックされ、スレッド プールが枯渇する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-135">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="31adc-136">同期列挙を回避するには、を使用して列挙可能なを `ToListAsync` 返します。</span><span class="sxs-lookup"><span data-stu-id="31adc-136">To avoid synchronous enumeration, use `ToListAsync` before returning the enumerable.</span></span>

<span data-ttu-id="31adc-137">ASP.NET Core 3.0 以降では、を `IAsyncEnumerable<T>` 非同期的に列挙するの代替手段としてを使用でき `IEnumerable<T>` ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-137">Beginning with ASP.NET Core 3.0, `IAsyncEnumerable<T>` can be used as an alternative to `IEnumerable<T>` that enumerates asynchronously.</span></span> <span data-ttu-id="31adc-138">詳細については、「 [コントローラーアクションの戻り値の型](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-138">For more information, see [Controller action return types](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).</span></span>

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="31adc-139">大きなオブジェクトの割り当てを最小限にする</span><span class="sxs-lookup"><span data-stu-id="31adc-139">Minimize large object allocations</span></span>

<span data-ttu-id="31adc-140">[.Net Core ガベージコレクター](/dotnet/standard/garbage-collection/)は、ASP.NET Core アプリで自動的にメモリの割り当てと解放を管理します。</span><span class="sxs-lookup"><span data-stu-id="31adc-140">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="31adc-141">自動ガベージコレクションは、一般に、メモリが解放される方法とタイミングについて開発者が心配する必要がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="31adc-141">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="31adc-142">ただし、参照されていないオブジェクトをクリーンアップすると CPU 時間がかかるため、開発者は [ホットコードパス](#understand-hot-code-paths)内のオブジェクトの割り当てを最小限に抑える必要があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-142">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="31adc-143">ガベージコレクションは、大きなオブジェクト (> 85 K バイト) で特にコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="31adc-143">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="31adc-144">ラージオブジェクトは [大きなオブジェクトヒープ](/dotnet/standard/garbage-collection/large-object-heap) に格納され、クリーンアップするには、完全な (ジェネレーション 2) ガベージコレクションが必要です。</span><span class="sxs-lookup"><span data-stu-id="31adc-144">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="31adc-145">ジェネレーション0およびジェネレーション1のコレクションとは異なり、ジェネレーション2のコレクションでは、アプリの実行を一時的に中断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-145">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="31adc-146">大きなオブジェクトを頻繁に割り当てたり割り当て解除したりすると、パフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-146">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="31adc-147">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="31adc-147">Recommendations:</span></span>

* <span data-ttu-id="31adc-148">頻繁に使用されるラージオブジェクトをキャッシュすること **を検討してください。**</span><span class="sxs-lookup"><span data-stu-id="31adc-148">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="31adc-149">大きなオブジェクトをキャッシュすると、コストのかかる割り当てを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="31adc-149">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="31adc-150">大きな配列を格納する **には、** [arraypool \<T>](/dotnet/api/system.buffers.arraypool-1)を使用してバッファーをプールします。</span><span class="sxs-lookup"><span data-stu-id="31adc-150">**Do** pool buffers by using an [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="31adc-151">[ホットコードパス](#understand-hot-code-paths)には、有効期間が短い大きなオブジェクトを多数割り当て **ない** でください。</span><span class="sxs-lookup"><span data-stu-id="31adc-151">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="31adc-152">前述のようなメモリの問題は、 [Perfview](https://github.com/Microsoft/perfview) でガベージコレクション (GC) の統計を確認して調べることによって診断できます。</span><span class="sxs-lookup"><span data-stu-id="31adc-152">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="31adc-153">ガベージコレクションの一時停止時刻。</span><span class="sxs-lookup"><span data-stu-id="31adc-153">Garbage collection pause time.</span></span>
* <span data-ttu-id="31adc-154">ガベージコレクションで消費されるプロセッサ時間の割合。</span><span class="sxs-lookup"><span data-stu-id="31adc-154">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="31adc-155">ジェネレーション0、1、および2のガベージコレクションの数。</span><span class="sxs-lookup"><span data-stu-id="31adc-155">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="31adc-156">詳細については、「 [ガベージコレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-156">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access-and-io"></a><span data-ttu-id="31adc-157">データアクセスと i/o を最適化する</span><span class="sxs-lookup"><span data-stu-id="31adc-157">Optimize data access and I/O</span></span>

<span data-ttu-id="31adc-158">多くの場合、データストアやその他のリモートサービスとのやり取りは、ASP.NET Core アプリの最も低速な部分です。</span><span class="sxs-lookup"><span data-stu-id="31adc-158">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="31adc-159">効率的なパフォーマンスを利用するには、データの読み取りと書き込みを効率的に行うことが重要です。</span><span class="sxs-lookup"><span data-stu-id="31adc-159">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="31adc-160">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="31adc-160">Recommendations:</span></span>

* <span data-ttu-id="31adc-161">すべてのデータアクセス Api を非同期 **に呼び出します** 。</span><span class="sxs-lookup"><span data-stu-id="31adc-161">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="31adc-162">必要以上に多くのデータを取得 **しないで** ください。</span><span class="sxs-lookup"><span data-stu-id="31adc-162">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="31adc-163">現在の HTTP 要求に必要なデータだけを返すクエリを記述します。</span><span class="sxs-lookup"><span data-stu-id="31adc-163">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="31adc-164">少し古いデータが許容される場合は、データベースまたはリモートサービスから取得した頻繁にアクセスされるデータをキャッシュすること **を検討してください。**</span><span class="sxs-lookup"><span data-stu-id="31adc-164">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="31adc-165">シナリオに応じて、 [Memorycache](xref:performance/caching/memory) または [microsoft.web.distributedcache](xref:performance/caching/distributed)を使用します。</span><span class="sxs-lookup"><span data-stu-id="31adc-165">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="31adc-166">詳細については、「<xref:performance/caching/response>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-166">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="31adc-167">ネットワークラウンドトリップ **を最小限に** 抑えます。</span><span class="sxs-lookup"><span data-stu-id="31adc-167">**Do** minimize network round trips.</span></span> <span data-ttu-id="31adc-168">目的は、複数の呼び出しではなく、1回の呼び出しで必要なデータを取得することです。</span><span class="sxs-lookup"><span data-stu-id="31adc-168">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="31adc-169">読み取り専用の目的でデータにアクセスする場合 **は** 、Entity Framework Core で [追跡なしのクエリ](/ef/core/querying/tracking#no-tracking-queries)を使用します。</span><span class="sxs-lookup"><span data-stu-id="31adc-169">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="31adc-170">EF Core は、追跡しないクエリの結果をより効率的に返すことができます。</span><span class="sxs-lookup"><span data-stu-id="31adc-170">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="31adc-171">(、、またはステートメントを使用して) LINQ クエリのフィルター処理と集計を **実行** し、 `.Where` `.Select` `.Sum` データベースによってフィルター処理が実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="31adc-171">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="31adc-172">EF Core に **よってクライアント** 上の一部のクエリ演算子が解決されるため、クエリの実行効率が悪くなることがあります。</span><span class="sxs-lookup"><span data-stu-id="31adc-172">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="31adc-173">詳細については、「 [クライアント評価のパフォーマンスの問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-173">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="31adc-174">コレクションに対してプロジェクションクエリを使用しないでください。これにより、"N + 1" 個の SQL クエリが実行される可能性が **あり** ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-174">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="31adc-175">詳細については、「 [相関サブクエリの最適化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-175">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="31adc-176">高スケールアプリのパフォーマンスを向上させる方法については、「 [EF High performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-176">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="31adc-177">DbContext プール</span><span class="sxs-lookup"><span data-stu-id="31adc-177">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="31adc-178">明示的にコンパイルされたクエリ</span><span class="sxs-lookup"><span data-stu-id="31adc-178">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="31adc-179">コードベースをコミットする前に、上記の高パフォーマンスアプローチの影響を測定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="31adc-179">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="31adc-180">コンパイル済みクエリの複雑さが増えると、パフォーマンスの向上には合わない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-180">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="31adc-181">[Application Insights](/azure/application-insights/app-insights-overview)またはプロファイリングツールでデータにアクセスするために費やされた時間を確認することで、クエリの問題を検出できます。</span><span class="sxs-lookup"><span data-stu-id="31adc-181">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="31adc-182">ほとんどのデータベースでは、頻繁に実行されるクエリに関する統計も利用できます。</span><span class="sxs-lookup"><span data-stu-id="31adc-182">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="31adc-183">HttpClientFactory を使用して HTTP 接続をプールする</span><span class="sxs-lookup"><span data-stu-id="31adc-183">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="31adc-184">[Httpclient](/dotnet/api/system.net.http.httpclient)はインターフェイスを実装してい `IDisposable` ますが、再利用できるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="31adc-184">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="31adc-185">閉じら `HttpClient` れたインスタンスは、短時間にわたって状態でソケットを開いたままに `TIME_WAIT` します。</span><span class="sxs-lookup"><span data-stu-id="31adc-185">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="31adc-186">オブジェクトを作成および破棄するコードパス `HttpClient` が頻繁に使用される場合、アプリは使用可能なソケットを消費する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-186">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="31adc-187">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) は、この問題の解決策として ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="31adc-187">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="31adc-188">これは、パフォーマンスと信頼性を最適化するために、プール HTTP 接続を処理します。</span><span class="sxs-lookup"><span data-stu-id="31adc-188">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="31adc-189">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="31adc-189">Recommendations:</span></span>

* <span data-ttu-id="31adc-190">インスタンスを直接作成して破棄 **しないで** ください `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="31adc-190">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="31adc-191">インスタンスを取得するに **は** 、 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)を使用し `HttpClient` ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-191">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="31adc-192">詳細については、「[HttpClientFactory を使用して回復力の高い HTTP 要求を実装する](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-192">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="31adc-193">共通コードパスを高速に保つ</span><span class="sxs-lookup"><span data-stu-id="31adc-193">Keep common code paths fast</span></span>

<span data-ttu-id="31adc-194">すべてのコードが高速になるようにします。</span><span class="sxs-lookup"><span data-stu-id="31adc-194">You want all of your code to be fast.</span></span> <span data-ttu-id="31adc-195">頻繁に呼び出されるコードパスは、最適化するうえで最も重要なものです。</span><span class="sxs-lookup"><span data-stu-id="31adc-195">Frequently-called code paths are the most critical to optimize.</span></span> <span data-ttu-id="31adc-196">次の設定があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-196">These include:</span></span>

* <span data-ttu-id="31adc-197">アプリケーションの要求処理パイプラインのミドルウェアコンポーネント (特に、ミドルウェアはパイプラインの早い段階で実行されます)。</span><span class="sxs-lookup"><span data-stu-id="31adc-197">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="31adc-198">これらのコンポーネントは、パフォーマンスに大きな影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="31adc-198">These components have a large impact on performance.</span></span>
* <span data-ttu-id="31adc-199">要求ごとに、または要求ごとに複数回実行されるコード。</span><span class="sxs-lookup"><span data-stu-id="31adc-199">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="31adc-200">たとえば、カスタムログ、承認ハンドラー、または一時的なサービスの初期化などです。</span><span class="sxs-lookup"><span data-stu-id="31adc-200">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="31adc-201">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="31adc-201">Recommendations:</span></span>

* <span data-ttu-id="31adc-202">実行時間の長いタスクでカスタムミドルウェアコンポーネントを使用 **しない** でください。</span><span class="sxs-lookup"><span data-stu-id="31adc-202">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="31adc-203">[ホットコードパス](#understand-hot-code-paths)を特定するには、 [Visual Studio 診断ツール](/visualstudio/profiling/profiling-feature-tour)や [perfview](https://github.com/Microsoft/perfview)などのパフォーマンスプロファイリングツール **を使用し** ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-203">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="31adc-204">HTTP 要求の外部で実行時間の長いタスクを完了する</span><span class="sxs-lookup"><span data-stu-id="31adc-204">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="31adc-205">ASP.NET Core アプリに対するほとんどの要求は、必要なサービスを呼び出すコントローラーまたはページモデルによって処理され、HTTP 応答を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="31adc-205">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="31adc-206">長時間実行されるタスクが含まれている一部の要求では、要求-応答プロセス全体を非同期にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="31adc-206">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="31adc-207">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="31adc-207">Recommendations:</span></span>

* <span data-ttu-id="31adc-208">通常の HTTP 要求処理の一部として、長時間実行されるタスクが完了するまで待機しない **で** ください。</span><span class="sxs-lookup"><span data-stu-id="31adc-208">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="31adc-209">[バックグラウンドサービス](xref:fundamentals/host/hosted-services)で長時間実行される要求や、 [Azure 関数](/azure/azure-functions/)を使用したアウトプロセスを処理すること **を検討してください。**</span><span class="sxs-lookup"><span data-stu-id="31adc-209">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="31adc-210">特に、CPU を集中的に使用するタスクには、アウトプロセスの完了が役立ちます。</span><span class="sxs-lookup"><span data-stu-id="31adc-210">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="31adc-211">クライアントとの非同期通信には、などのリアルタイム通信オプション **を使用し** [:::no-loc(SignalR):::](xref:signalr/introduction) ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-211">**Do** use real-time communication options, such as [:::no-loc(SignalR):::](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="31adc-212">クライアント資産の縮小</span><span class="sxs-lookup"><span data-stu-id="31adc-212">Minify client assets</span></span>

<span data-ttu-id="31adc-213">複雑なフロントエンドを使用する ASP.NET Core アプリは、多くの JavaScript、CSS、またはイメージファイルに頻繁に対応します。</span><span class="sxs-lookup"><span data-stu-id="31adc-213">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="31adc-214">初期読み込み要求のパフォーマンスは、次の方法で改善できます。</span><span class="sxs-lookup"><span data-stu-id="31adc-214">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="31adc-215">バンドル。複数のファイルを1つに結合します。</span><span class="sxs-lookup"><span data-stu-id="31adc-215">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="31adc-216">縮小。空白とコメントを削除することによってファイルのサイズを縮小します。</span><span class="sxs-lookup"><span data-stu-id="31adc-216">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="31adc-217">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="31adc-217">Recommendations:</span></span>

* <span data-ttu-id="31adc-218">クライアント資産のバンドルと縮小には ASP.NET Core の [組み込みサポート](xref:client-side/bundling-and-minification)**を使用し** ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-218">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="31adc-219">複雑なクライアント資産管理には、 [Webpack](https://webpack.js.org/)などの他のサードパーティ製ツールを使用すること **を検討してください。**</span><span class="sxs-lookup"><span data-stu-id="31adc-219">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="31adc-220">応答を圧縮する</span><span class="sxs-lookup"><span data-stu-id="31adc-220">Compress responses</span></span>

 <span data-ttu-id="31adc-221">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="31adc-221">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="31adc-222">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="31adc-222">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="31adc-223">詳細については、「 [応答の圧縮](xref:performance/response-compression)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-223">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="31adc-224">最新の ASP.NET Core リリースを使用する</span><span class="sxs-lookup"><span data-stu-id="31adc-224">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="31adc-225">ASP.NET Core の新しいリリースにはそれぞれ、パフォーマンスが向上しています。</span><span class="sxs-lookup"><span data-stu-id="31adc-225">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="31adc-226">.NET Core と ASP.NET Core での最適化により、新しいバージョンの方が以前のバージョンより高い値になります。</span><span class="sxs-lookup"><span data-stu-id="31adc-226">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="31adc-227">たとえば、.NET Core 2.1 では、コンパイルされた正規表現と享受を[スパン \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay)からサポートするようになりました。</span><span class="sxs-lookup"><span data-stu-id="31adc-227">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [Span\<T>](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay).</span></span> <span data-ttu-id="31adc-228">ASP.NET Core 2.2 では、HTTP/2 のサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="31adc-228">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="31adc-229">[ASP.NET Core 3.0](xref:aspnetcore-3.0) では、メモリ使用量を減らし、スループットを向上させる多くの機能強化が加えられています。</span><span class="sxs-lookup"><span data-stu-id="31adc-229">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="31adc-230">パフォーマンスが優先される場合は、現在のバージョンの ASP.NET Core にアップグレードすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-230">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="31adc-231">例外を最小化する</span><span class="sxs-lookup"><span data-stu-id="31adc-231">Minimize exceptions</span></span>

<span data-ttu-id="31adc-232">例外はめったに発生しません。</span><span class="sxs-lookup"><span data-stu-id="31adc-232">Exceptions should be rare.</span></span> <span data-ttu-id="31adc-233">例外のスローとキャッチは、他のコードフローパターンと比べて低速です。</span><span class="sxs-lookup"><span data-stu-id="31adc-233">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="31adc-234">このため、通常のプログラムフローを制御するために例外を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="31adc-234">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="31adc-235">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="31adc-235">Recommendations:</span></span>

* <span data-ttu-id="31adc-236">特に [ホットコードパス](#understand-hot-code-paths)では、通常のプログラムフローの手段として例外をスローまたはキャッチし **ない** ようにします。</span><span class="sxs-lookup"><span data-stu-id="31adc-236">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="31adc-237">例外を発生させる条件を検出して処理するには、アプリにロジック **を含めます** 。</span><span class="sxs-lookup"><span data-stu-id="31adc-237">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="31adc-238">例外的または予期しない条件の例外 **をスローまた** はキャッチします。</span><span class="sxs-lookup"><span data-stu-id="31adc-238">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="31adc-239">Application Insights などのアプリ診断ツールを使用すると、アプリケーションでのパフォーマンスに影響する可能性のある一般的な例外を識別できます。</span><span class="sxs-lookup"><span data-stu-id="31adc-239">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="31adc-240">パフォーマンスと信頼性</span><span class="sxs-lookup"><span data-stu-id="31adc-240">Performance and reliability</span></span>

<span data-ttu-id="31adc-241">次のセクションでは、パフォーマンスのヒントと既知の信頼性の問題と解決策について説明します。</span><span class="sxs-lookup"><span data-stu-id="31adc-241">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="31adc-242">HttpRequest/Httpresponse.cache 本体で同期読み取りまたは書き込みを回避する</span><span class="sxs-lookup"><span data-stu-id="31adc-242">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="31adc-243">ASP.NET Core の i/o はすべて非同期です。</span><span class="sxs-lookup"><span data-stu-id="31adc-243">All I/O in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="31adc-244">サーバーは、 `Stream` 同期と非同期の両方のオーバーロードを持つインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="31adc-244">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="31adc-245">スレッドプールのスレッドがブロックされないようにするために、非同期のものを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="31adc-245">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="31adc-246">ブロックしているスレッドは、スレッドプールの枯渇につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-246">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="31adc-247">**この** 操作は避けてください。次の例では、を使用し <xref:System.IO.StreamReader.ReadToEnd*> ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-247">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="31adc-248">このメソッドは、現在のスレッドが結果を待機するのをブロックします。</span><span class="sxs-lookup"><span data-stu-id="31adc-248">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="31adc-249">非同期的な [同期](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="31adc-249">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="31adc-250">上記のコードでは、は、 `Get` HTTP 要求の本体全体をメモリに同期的に読み取ります。</span><span class="sxs-lookup"><span data-stu-id="31adc-250">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="31adc-251">クライアントが低速でアップロードしている場合、アプリは非同期で同期を実行しています。</span><span class="sxs-lookup"><span data-stu-id="31adc-251">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="31adc-252">Kestrel では同期読み取りがサポート **されてい** ないため、アプリは非同期経由で同期します。</span><span class="sxs-lookup"><span data-stu-id="31adc-252">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="31adc-253">次の **手順を実行します。** 次の例では、を使用して、 <xref:System.IO.StreamReader.ReadToEndAsync*> 読み取り中にスレッドをブロックしません。</span><span class="sxs-lookup"><span data-stu-id="31adc-253">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="31adc-254">上記のコードは、HTTP 要求本文全体を非同期的にメモリに読み込みます。</span><span class="sxs-lookup"><span data-stu-id="31adc-254">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="31adc-255">要求が大きい場合、HTTP 要求本文全体をメモリに読み込むと、メモリ不足 (OOM) 状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-255">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="31adc-256">OOM を使用すると、サービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-256">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="31adc-257">詳細については、このドキュメントの「 [大きな要求本文または応答本文をメモリに読み込む](#arlb) ことを避ける」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-257">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="31adc-258">次の **手順を実行します。** 次の例は、バッファーされていない要求本文を使用する完全な非同期です。</span><span class="sxs-lookup"><span data-stu-id="31adc-258">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="31adc-259">上記のコードは、要求本文を非同期的にシリアル化解除して C# オブジェクトにします。</span><span class="sxs-lookup"><span data-stu-id="31adc-259">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="31adc-260">要求で ReadFormAsync を優先します。フォーム</span><span class="sxs-lookup"><span data-stu-id="31adc-260">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="31adc-261">`HttpContext.Request.Form` ではなく `HttpContext.Request.ReadFormAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="31adc-261">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="31adc-262">`HttpContext.Request.Form` は、次の条件で安全に読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="31adc-262">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="31adc-263">フォームは、の呼び出しによって読み取られました。 `ReadFormAsync`</span><span class="sxs-lookup"><span data-stu-id="31adc-263">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="31adc-264">キャッシュされたフォーム値はを使用して読み取られています `HttpContext.Request.Form`</span><span class="sxs-lookup"><span data-stu-id="31adc-264">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="31adc-265">**この** 操作は避けてください。次の例では、 `HttpContext.Request.Form` を使用します。</span><span class="sxs-lookup"><span data-stu-id="31adc-265">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="31adc-266">`HttpContext.Request.Form`[非同期の同期](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)を使用し、スレッドプールの枯渇につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-266">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="31adc-267">次の **手順を実行します。** 次の例では、を使用して `HttpContext.Request.ReadFormAsync` フォーム本文を非同期に読み取ります。</span><span class="sxs-lookup"><span data-stu-id="31adc-267">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="31adc-268">大きな要求本文または応答本文をメモリに読み込むことは避けてください</span><span class="sxs-lookup"><span data-stu-id="31adc-268">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="31adc-269">.NET では、85 KB を超えるすべてのオブジェクト割り当ては、大きなオブジェクトヒープ ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) で終了します。</span><span class="sxs-lookup"><span data-stu-id="31adc-269">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="31adc-270">ラージオブジェクトは、次の2つの方法でコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="31adc-270">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="31adc-271">新しく割り当てられたラージオブジェクトのメモリをクリアする必要があるため、割り当てコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="31adc-271">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="31adc-272">CLR は、新しく割り当てられたすべてのオブジェクトのメモリがクリアされることを保証します。</span><span class="sxs-lookup"><span data-stu-id="31adc-272">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="31adc-273">LOH は、ヒープの残りの部分で収集されます。</span><span class="sxs-lookup"><span data-stu-id="31adc-273">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="31adc-274">LOH には、フル [ガベージコレクション](/dotnet/standard/garbage-collection/fundamentals) または [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations)が必要です。</span><span class="sxs-lookup"><span data-stu-id="31adc-274">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="31adc-275">この [ブログ投稿](https://adamsitnik.com/Array-Pool/#the-problem) では、問題について簡潔に説明しています。</span><span class="sxs-lookup"><span data-stu-id="31adc-275">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="31adc-276">ラージオブジェクトが割り当てられると、ジェネレーション2のオブジェクトとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="31adc-276">When a large object is allocated, it's marked as Gen 2 object.</span></span> <span data-ttu-id="31adc-277">小さいオブジェクトの場合は Gen 0 として生成されません。</span><span class="sxs-lookup"><span data-stu-id="31adc-277">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="31adc-278">結果として、LOH でメモリが不足していると、LOH だけでなく、マネージヒープ全体が GC によってクリーンアップされます。</span><span class="sxs-lookup"><span data-stu-id="31adc-278">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="31adc-279">そのため、LOH を含む Gen 0、Gen 1、Gen 2 をクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="31adc-279">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="31adc-280">これはフルガベージコレクションと呼ばれ、最も時間のかかるガベージコレクションです。</span><span class="sxs-lookup"><span data-stu-id="31adc-280">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="31adc-281">多くのアプリケーションでは、許容される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-281">For many applications, it can be acceptable.</span></span> <span data-ttu-id="31adc-282">しかし、高パフォーマンスの web サーバーでは、平均的な web 要求を処理するために大量のメモリバッファーが必要になることはありません (ソケットからの読み取り、圧縮解除、JSON & のデコードなど)。</span><span class="sxs-lookup"><span data-stu-id="31adc-282">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="31adc-283">大規模な要求または応答の本文を1つのまたはに格納するネイティブ `byte[]` `string` :</span><span class="sxs-lookup"><span data-stu-id="31adc-283">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="31adc-284">LOH の領域がすぐに不足する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-284">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="31adc-285">完全な Gc が実行されているため、アプリのパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-285">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="31adc-286">同期データ処理 API を使用した作業</span><span class="sxs-lookup"><span data-stu-id="31adc-286">Working with a synchronous data processing API</span></span>

<span data-ttu-id="31adc-287">同期読み取りと書き込みのみをサポートするシリアライザー/デシリアライザーを使用する場合 (たとえば、  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span><span class="sxs-lookup"><span data-stu-id="31adc-287">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="31adc-288">シリアライザー/デシリアライザーに渡す前に、データをメモリに非同期的にバッファーします。</span><span class="sxs-lookup"><span data-stu-id="31adc-288">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="31adc-289">要求が大きい場合、メモリ不足 (OOM) 状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-289">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="31adc-290">OOM を使用すると、サービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-290">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="31adc-291">詳細については、このドキュメントの「 [大きな要求本文または応答本文をメモリに読み込む](#arlb) ことを避ける」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-291">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="31adc-292">ASP.NET Core 3.0 は <xref:System.Text.Json> 、JSON のシリアル化に既定でを使用します。</span><span class="sxs-lookup"><span data-stu-id="31adc-292">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="31adc-293"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="31adc-293"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="31adc-294">非同期で JSON の読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="31adc-294">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="31adc-295">UTF-8 テキスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="31adc-295">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="31adc-296">通常、`Newtonsoft.Json` よりパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="31adc-296">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="31adc-297">フィールドに IHttpContextAccessor を格納しない</span><span class="sxs-lookup"><span data-stu-id="31adc-297">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="31adc-298">[IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)は、 `HttpContext` 要求スレッドからアクセスされたときに、アクティブな要求のを返します。</span><span class="sxs-lookup"><span data-stu-id="31adc-298">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="31adc-299">を `IHttpContextAccessor.HttpContext` フィールドまたは変数に格納することはでき **ません** 。</span><span class="sxs-lookup"><span data-stu-id="31adc-299">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="31adc-300">**この** 操作は避けてください。次の例では、を `HttpContext` フィールドに格納し、後でそれを使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="31adc-300">**Do not do this:** The following example stores the `HttpContext` in a field and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="31adc-301">上記のコードでは、コンストラクター内で null または正しくないが頻繁にキャプチャされ `HttpContext` ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-301">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="31adc-302">次の **手順を実行します。** 次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="31adc-302">**Do this:** The following example:</span></span>

* <span data-ttu-id="31adc-303">を <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> フィールドに格納します。</span><span class="sxs-lookup"><span data-stu-id="31adc-303">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="31adc-304">は、 `HttpContext` 正しい時刻にフィールドを使用し、をチェックし `null` ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-304">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="31adc-305">複数のスレッドから HttpContext にアクセスしない</span><span class="sxs-lookup"><span data-stu-id="31adc-305">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="31adc-306">`HttpContext` はスレッドセーフでは *ありません* 。</span><span class="sxs-lookup"><span data-stu-id="31adc-306">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="31adc-307">`HttpContext`複数のスレッドから同時にアクセスすると、ハング、クラッシュ、データ破損などの未定義の動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-307">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="31adc-308">**この** 操作は避けてください。次の例では、3つの並列要求を行い、発信 HTTP 要求の前後に受信要求パスをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="31adc-308">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="31adc-309">要求パスは、複数のスレッドから同時にアクセスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-309">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="31adc-310">次の **手順を実行します。** 次の例では、3つの並列要求を行う前に、受信要求からすべてのデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="31adc-310">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="31adc-311">要求の完了後に HttpContext を使用しない</span><span class="sxs-lookup"><span data-stu-id="31adc-311">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="31adc-312">`HttpContext` は、ASP.NET Core パイプラインにアクティブな HTTP 要求がある限り有効です。</span><span class="sxs-lookup"><span data-stu-id="31adc-312">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="31adc-313">ASP.NET Core パイプライン全体は、すべての要求を実行するデリゲートの非同期チェーンです。</span><span class="sxs-lookup"><span data-stu-id="31adc-313">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="31adc-314">`Task`このチェーンから返されたが完了すると、 `HttpContext` がリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="31adc-314">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="31adc-315">**この** 操作は避けてください。次の例では、 `async void` 最初のに到達したときに HTTP 要求が完了するように、を使用して `await` います。</span><span class="sxs-lookup"><span data-stu-id="31adc-315">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="31adc-316">これは、ASP.NET Core アプリでは **常** に不適切な方法です。</span><span class="sxs-lookup"><span data-stu-id="31adc-316">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="31adc-317">`HttpResponse`HTTP 要求が完了した後に、にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="31adc-317">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="31adc-318">プロセスをクラッシュさせる。</span><span class="sxs-lookup"><span data-stu-id="31adc-318">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="31adc-319">次の **手順を実行します。** 次の例では、を `Task` フレームワークに返します。そのため、アクションが完了するまで HTTP 要求は完了しません。</span><span class="sxs-lookup"><span data-stu-id="31adc-319">**Do this:** The following example returns a `Task` to the framework, so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="31adc-320">バックグラウンドスレッドで HttpContext をキャプチャしない</span><span class="sxs-lookup"><span data-stu-id="31adc-320">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="31adc-321">**この** 操作は避けてください。次の例は、プロパティからをキャプチャしていることを示してい `HttpContext` `Controller` ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-321">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="31adc-322">作業項目は次のようになる可能性があるため、この方法は不適切です。</span><span class="sxs-lookup"><span data-stu-id="31adc-322">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="31adc-323">要求スコープの外部で実行します。</span><span class="sxs-lookup"><span data-stu-id="31adc-323">Run outside of the request scope.</span></span>
* <span data-ttu-id="31adc-324">間違ったを読み取ろうとしました `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="31adc-324">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="31adc-325">次の **手順を実行します。** 次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="31adc-325">**Do this:** The following example:</span></span>

* <span data-ttu-id="31adc-326">要求中にバックグラウンドタスクで必要なデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="31adc-326">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="31adc-327">コントローラーから何も参照していません。</span><span class="sxs-lookup"><span data-stu-id="31adc-327">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

<span data-ttu-id="31adc-328">バックグラウンドタスクはホステッドサービスとして実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-328">Background tasks should be implemented as hosted services.</span></span> <span data-ttu-id="31adc-329">詳細については、「[Background tasks with hosted services](xref:fundamentals/host/hosted-services)」(ホストされるタスクを使用するバックグラウンド タスク) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-329">For more information, see [Background tasks with hosted services](xref:fundamentals/host/hosted-services).</span></span>

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="31adc-330">バックグラウンドスレッドでコントローラーに挿入されたサービスをキャプチャしない</span><span class="sxs-lookup"><span data-stu-id="31adc-330">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="31adc-331">**この** 操作は避けてください。次の例は、アクションパラメーターからをキャプチャすることを示してい `DbContext` `Controller` ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-331">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="31adc-332">これは不適切な方法です。</span><span class="sxs-lookup"><span data-stu-id="31adc-332">This is a bad practice.</span></span>  <span data-ttu-id="31adc-333">この作業項目は、要求スコープの外部で実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31adc-333">The work item could run outside of the request scope.</span></span> <span data-ttu-id="31adc-334">は `ContosoDbContext` 要求に対してスコープが設定され、結果としてが生成され `ObjectDisposedException` ます。</span><span class="sxs-lookup"><span data-stu-id="31adc-334">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="31adc-335">次の **手順を実行します。** 次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="31adc-335">**Do this:** The following example:</span></span>

* <span data-ttu-id="31adc-336"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>バックグラウンド作業項目のスコープを作成するために、を挿入します。</span><span class="sxs-lookup"><span data-stu-id="31adc-336">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="31adc-337">`IServiceScopeFactory` はシングルトンです。</span><span class="sxs-lookup"><span data-stu-id="31adc-337">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="31adc-338">バックグラウンドスレッドで新しい依存関係挿入スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="31adc-338">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="31adc-339">コントローラーから何も参照していません。</span><span class="sxs-lookup"><span data-stu-id="31adc-339">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="31adc-340">は、受信要求からをキャプチャしません `ContosoDbContext` 。</span><span class="sxs-lookup"><span data-stu-id="31adc-340">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="31adc-341">次の強調表示されたコード。</span><span class="sxs-lookup"><span data-stu-id="31adc-341">The following highlighted code:</span></span>

* <span data-ttu-id="31adc-342">バックグラウンド操作の有効期間のスコープを作成し、そこからサービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="31adc-342">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="31adc-343">は `ContosoDbContext` 、正しいスコープからを使用します。</span><span class="sxs-lookup"><span data-stu-id="31adc-343">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="31adc-344">応答本文が開始された後に状態コードまたはヘッダーを変更しない</span><span class="sxs-lookup"><span data-stu-id="31adc-344">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="31adc-345">ASP.NET Core は、HTTP 応答の本文をバッファーしません。</span><span class="sxs-lookup"><span data-stu-id="31adc-345">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="31adc-346">最初に応答が書き込まれたとき:</span><span class="sxs-lookup"><span data-stu-id="31adc-346">The first time the response is written:</span></span>

* <span data-ttu-id="31adc-347">ヘッダーは、本文のそのチャンクと共にクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="31adc-347">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="31adc-348">応答ヘッダーを変更することはできなくなりました。</span><span class="sxs-lookup"><span data-stu-id="31adc-348">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="31adc-349">**この** 操作は避けてください。次のコードでは、応答が既に開始された後に応答ヘッダーを追加しようとしています。</span><span class="sxs-lookup"><span data-stu-id="31adc-349">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="31adc-350">前のコードでは、 `context.Response.Headers["test"] = "test value";` が応答に書き込まれた場合、は例外をスロー `next()` します。</span><span class="sxs-lookup"><span data-stu-id="31adc-350">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="31adc-351">次の **手順を実行します。** 次の例では、ヘッダーを変更する前に、HTTP 応答が開始されたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="31adc-351">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="31adc-352">次の **手順を実行します。** 次の例では、を使用して、 `HttpResponse.OnStarting` 応答ヘッダーをクライアントにフラッシュする前にヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="31adc-352">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="31adc-353">応答が開始されていないかどうかを確認すると、応答ヘッダーが書き込まれる直前に呼び出されるコールバックを登録できます。</span><span class="sxs-lookup"><span data-stu-id="31adc-353">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="31adc-354">応答が開始されていないかどうかを確認しています:</span><span class="sxs-lookup"><span data-stu-id="31adc-354">Checking if the response has not started:</span></span>

* <span data-ttu-id="31adc-355">ヘッダーをジャストインタイムで追加またはオーバーライドする機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="31adc-355">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="31adc-356">では、パイプライン内の次のミドルウェアに関する知識は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="31adc-356">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="31adc-357">応答本文への書き込みを既に開始している場合は、next () を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="31adc-357">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="31adc-358">コンポーネントは、応答を処理および操作できる場合にのみ呼び出されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="31adc-358">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>

## <a name="use-in-process-hosting-with-iis"></a><span data-ttu-id="31adc-359">IIS でインプロセスホスティングを使用する</span><span class="sxs-lookup"><span data-stu-id="31adc-359">Use In-process hosting with IIS</span></span>

<span data-ttu-id="31adc-360">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="31adc-360">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="31adc-361">インプロセスホスティングでは、要求がループバックアダプターを介してプロキシされないため、アウトプロセスホスティングよりパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="31adc-361">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="31adc-362">ループバックアダプターは、同じコンピューターに送信されるネットワークトラフィックを返すネットワークインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="31adc-362">The loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="31adc-363">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="31adc-363">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="31adc-364">プロジェクトは、既定では ASP.NET Core 3.0 以降のインプロセスホスティングモデルに設定されます。</span><span class="sxs-lookup"><span data-stu-id="31adc-364">Projects default to the in-process hosting model in ASP.NET Core 3.0 and later.</span></span>

<span data-ttu-id="31adc-365">詳細については、「 [IIS を使用した Windows での ASP.NET Core のホスト](xref:host-and-deploy/iis/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31adc-365">For more information, see [Host ASP.NET Core on Windows with IIS](xref:host-and-deploy/iis/index)</span></span>
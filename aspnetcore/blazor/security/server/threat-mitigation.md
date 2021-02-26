---
title: ASP.NET Core Blazor Server の脅威の緩和のガイダンス
author: guardrex
description: Blazor Server アプリに対するセキュリティ上の脅威を緩和する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 524a1f85838d083ed2f01afd96601bcd5e6ce12b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280979"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a><span data-ttu-id="d8c55-103">ASP.NET Core Blazor Server の脅威の緩和のガイダンス</span><span class="sxs-lookup"><span data-stu-id="d8c55-103">Threat mitigation guidance for ASP.NET Core Blazor Server</span></span>

<span data-ttu-id="d8c55-104">Blazor Server アプリでは、サーバーとクライアントが長期間のリレーションシップを維持する、"*ステートフル*" なデータ処理モデルが採用されています。</span><span class="sxs-lookup"><span data-stu-id="d8c55-104">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="d8c55-105">永続的な状態は[回線](xref:blazor/state-management)によって維持されます。これは、長期間続く可能性がある接続にまたがることがあります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-105">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="d8c55-106">ユーザーが Blazor Server サイトにアクセスすると、サーバーによってサーバーのメモリに回線が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-106">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="d8c55-107">ユーザーが UI のボタンを選択したときなどに、この回線によって、レンダリングするコンテンツがブラウザーに指示されたり、イベントへの応答が行われたりします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-107">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="d8c55-108">これらのアクションを実行するために、回線によって、ユーザーのブラウザーで JavaScript 関数が呼び出され、サーバー上で .NET メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-108">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="d8c55-109">この JavaScript ベースの双方向の対話は、[JavaScript 相互運用 (JS 相互運用)](xref:blazor/call-javascript-from-dotnet) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-109">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="d8c55-110">JS 相互運用はインターネット経由で行われ、クライアントではリモート ブラウザーが使用されるため、Blazor Server アプリではほとんどの Web アプリのセキュリティの問題が共有されています。</span><span class="sxs-lookup"><span data-stu-id="d8c55-110">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="d8c55-111">このトピックでは、Blazor Server アプリに対する一般的な脅威について説明し、インターネットに接続されたアプリに重点を置いた脅威の緩和のガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-111">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="d8c55-112">企業ネットワーク内やイントラネット内などの制約された環境では、緩和ガイダンスの一部は、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-112">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="d8c55-113">制約された環境には適用されません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-113">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="d8c55-114">制約された環境はセキュリティ リスクが低いため、実装する価値がありません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-114">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-and-shared-state"></a><span data-ttu-id="d8c55-115">Blazor と共有状態</span><span class="sxs-lookup"><span data-stu-id="d8c55-115">Blazor and shared state</span></span>

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a><span data-ttu-id="d8c55-116">リソースの枯渇</span><span class="sxs-lookup"><span data-stu-id="d8c55-116">Resource exhaustion</span></span>

<span data-ttu-id="d8c55-117">リソースの枯渇は、クライアントがサーバーとやり取りし、サーバーが過剰にリソースを消費する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-117">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="d8c55-118">過剰なリソースの消費が主に影響するのは次のものです。</span><span class="sxs-lookup"><span data-stu-id="d8c55-118">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="d8c55-119">CPU</span><span class="sxs-lookup"><span data-stu-id="d8c55-119">CPU</span></span>](#cpu)
* [<span data-ttu-id="d8c55-120">メモリ</span><span class="sxs-lookup"><span data-stu-id="d8c55-120">Memory</span></span>](#memory)
* [<span data-ttu-id="d8c55-121">クライアント接続</span><span class="sxs-lookup"><span data-stu-id="d8c55-121">Client connections</span></span>](#client-connections)

<span data-ttu-id="d8c55-122">サービス拒否 (DoS) 攻撃は、通常、アプリまたはサーバーのリソースを枯渇させようとします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-122">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="d8c55-123">しかし、リソースの枯渇が必ずしもシステムへの攻撃の結果であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-123">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="d8c55-124">たとえば、ユーザーの要求が多いことで、有限のリソースが使い果たされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-124">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="d8c55-125">DoS については、「[サービス拒否 (DoS) 攻撃](#denial-of-service-dos-attacks)」のセクションで詳しく説明しています。</span><span class="sxs-lookup"><span data-stu-id="d8c55-125">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="d8c55-126">データベースやファイル ハンドル (ファイルの読み取りと書き込みに使用される) など、Blazor フレームワークの外部のリソースでも、リソースの枯渇が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-126">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="d8c55-127">詳細については、「<xref:performance/performance-best-practices>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-127">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="d8c55-128">CPU</span><span class="sxs-lookup"><span data-stu-id="d8c55-128">CPU</span></span>

<span data-ttu-id="d8c55-129">CPU の枯渇は、1 つ以上のクライアントが大量の CPU 処理を実行するようサーバーに強制した場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-129">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="d8c55-130">例として、"*フィボナッチ数*" を計算する Blazor Server アプリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="d8c55-130">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="d8c55-131">フィボナッチ数は、フィボナッチ数列から生成されます。この数列の各数は、先行する 2 つの数の合計です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-131">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="d8c55-132">答えに達するまでに必要な作業量は、数列の長さと初期値のサイズによって異なります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-132">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="d8c55-133">アプリでクライアントの要求に制限を設けていない場合、CPU 負荷の高い計算によって CPU の時間が占有され、他のタスクのパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-133">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="d8c55-134">過剰なリソース消費は、可用性に影響を与えるセキュリティ上の懸念事項です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-134">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="d8c55-135">CPU の枯渇は、すべての公開アプリに関する懸念事項です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-135">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="d8c55-136">通常の Web アプリでは、セーフガードとして要求と接続がタイムアウトになりますが、Blazor Server アプリには同じセーフガードが用意されていません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-136">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="d8c55-137">CPU を集中的に使用する作業を実行する前に、Blazor Server アプリに適切なチェックと制限を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-137">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="d8c55-138">メモリ</span><span class="sxs-lookup"><span data-stu-id="d8c55-138">Memory</span></span>

<span data-ttu-id="d8c55-139">メモリの枯渇は、1 つ以上のクライアントが大量のメモリを消費するようサーバーに強制した場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-139">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="d8c55-140">例として、項目のリストを受け入れて表示するコンポーネントがある Blazor サーバー側アプリについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="d8c55-140">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="d8c55-141">Blazor アプリで許可される項目の数またはクライアントにレンダリングされる項目の数が制限されていない場合、メモリを集中的に使用する処理とレンダリングで、サーバーのメモリが占有され、パフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-141">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="d8c55-142">サーバーがクラッシュしたり、クラッシュしたかのように遅くなったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-142">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="d8c55-143">サーバー上のメモリの枯渇のシナリオに関連する項目のリストを保持および表示するために、次のシナリオを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-143">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="d8c55-144">`List<MyItem>` プロパティまたはフィールドの項目は、サーバーのメモリを使用します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-144">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="d8c55-145">アプリで項目のリストを無制限に拡張することが許可されていると、サーバーでメモリが不足する危険性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-145">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="d8c55-146">メモリが不足すると、現在のセッションが終了 (クラッシュ) し、そのサーバー インスタンス内のすべての同時セッションでメモリ不足の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-146">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="d8c55-147">このシナリオが発生しないようにするには、同時ユーザーに項目の制限を課すデータ構造をアプリで使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-147">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="d8c55-148">レンダリングにページング スキームが使用されていない場合、サーバーにより、UI に表示されないオブジェクトに対して追加のメモリが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-148">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="d8c55-149">項目の数に制限がないと、メモリの需要によって使用可能なサーバー メモリが枯渇する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-149">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="d8c55-150">このシナリオを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-150">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="d8c55-151">レンダリング時にページ分割されたリストを使用します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-151">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="d8c55-152">最初の 100 から 1,000 項目のみを表示し、表示された項目以外の項目を検索するには、検索条件を入力するようユーザーに求めます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-152">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="d8c55-153">より高度なレンダリング シナリオでは、"*仮想化*" をサポートするリストまたはグリッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-153">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="d8c55-154">仮想化を使用すると、リストには、現在ユーザーに表示されている項目のサブセットのみがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-154">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="d8c55-155">ユーザーが UI のスクロールバーを操作すると、コンポーネントでは表示に必要な項目だけがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-155">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="d8c55-156">現時点で、表示に必要ない項目は、セカンダリ ストレージに保持できます。これが理想的な方法です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-156">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="d8c55-157">表示されていない項目をメモリに保持することもできますが、これはあまり理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-157">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="d8c55-158">Blazor Server アプリには、WPF、Windows フォーム、Blazor WebAssembly など、ステートフル アプリ用の他の UI フレームワークと同様のプログラミング モデルが用意されています。</span><span class="sxs-lookup"><span data-stu-id="d8c55-158">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="d8c55-159">主な違いは、いくつかの UI フレームワークでは、アプリによって消費されるメモリがクライアントに属し、その個々のクライアントのみに影響することです。</span><span class="sxs-lookup"><span data-stu-id="d8c55-159">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="d8c55-160">たとえば、Blazor WebAssembly アプリは、完全にクライアント上で実行され、クライアントのメモリ リソースのみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-160">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="d8c55-161">Blazor Server のシナリオでは、アプリによって消費されるメモリは、サーバーに属し、サーバー インスタンス上のクライアント間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-161">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="d8c55-162">サーバー側のメモリ要求は、すべての Blazor Server アプリにとっての考慮事項です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-162">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="d8c55-163">ただし、ほとんどの Web アプリはステートレスであり、要求の処理中に使用されたメモリは、応答が返されると解放されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-163">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="d8c55-164">一般的な推奨事項として、クライアント接続を保持する他のサーバー側アプリと同じように、クライアントが解放されているメモリ容量を割り当てられないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-164">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="d8c55-165">Blazor Server アプリによって消費されるメモリは、1 つの要求よりも長い時間保持されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-165">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="d8c55-166">開発中は、プロファイラーを使用したり、トレースをキャプチャしてクライアントのメモリ要求を評価したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-166">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="d8c55-167">プロファイラーまたはトレースでは、特定のクライアントに割り当てられたメモリはキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-167">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="d8c55-168">開発時に特定のクライアントのメモリ使用量をキャプチャするには、ダンプをキャプチャし、ユーザーの回線をルートとするすべてのオブジェクトのメモリ要求を調べます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-168">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="d8c55-169">クライアント接続</span><span class="sxs-lookup"><span data-stu-id="d8c55-169">Client connections</span></span>

<span data-ttu-id="d8c55-170">接続の枯渇は、1 つ以上のクライアントがサーバーへのコンカレント接続を多数開いていて、他のクライアントが新しい接続を確立できない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-170">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="d8c55-171">Blazor クライアントは、セッションごとに 1 つの接続を確立し、ブラウザー ウィンドウが開いている間は接続を開いたままにします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-171">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="d8c55-172">すべての接続を維持するサーバーに対する要求は、Blazor アプリに固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-172">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="d8c55-173">接続の永続的な性質と Blazor Server アプリのステートフルな性質を考えると、接続の枯渇はアプリの可用性に対するリスクが高くなります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-173">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="d8c55-174">既定では、Blazor Server アプリのユーザーあたりの接続数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-174">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="d8c55-175">アプリで接続制限が必要な場合は、次の方法の 1 つまたは複数を実行します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-175">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="d8c55-176">認証を要求します。これにより、承認されていないユーザーがアプリに接続する能力が自然に制限されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-176">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="d8c55-177">このシナリオを有効にするには、ユーザーが新しいユーザーを自由にプロビジョニングできないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-177">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="d8c55-178">ユーザーあたりの接続数を制限します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-178">Limit the number of connections per user.</span></span> <span data-ttu-id="d8c55-179">接続の制限は、次の方法で実現できます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-179">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="d8c55-180">正当なユーザーにアプリへのアクセスを許可するようにご注意ください (たとえば、クライアントの IP アドレスに基づいて接続の制限が確立されている場合)。</span><span class="sxs-lookup"><span data-stu-id="d8c55-180">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="d8c55-181">アプリ レベル:</span><span class="sxs-lookup"><span data-stu-id="d8c55-181">At the app level:</span></span>
    * <span data-ttu-id="d8c55-182">エンドポイント ルーティングの拡張性。</span><span class="sxs-lookup"><span data-stu-id="d8c55-182">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="d8c55-183">アプリに接続し、ユーザーごとにアクティブなセッションを追跡するには、認証を要求する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-183">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="d8c55-184">制限に達したら新しいセッションを拒否する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-184">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="d8c55-185">プロキシを使用したアプリへのプロキシ WebSocket 接続 (クライアントからアプリへの接続を多重化する [Azure SignalR Service](/azure/azure-signalr/signalr-overview) など)。</span><span class="sxs-lookup"><span data-stu-id="d8c55-185">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="d8c55-186">これにより、1 つのクライアントで確立できるよりも多くの接続容量がアプリに提供されるため、クライアントがサーバーへの接続を使い果たすことを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-186">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="d8c55-187">サーバー レベル: アプリの前にプロキシ/ゲートウェイを使用します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-187">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="d8c55-188">たとえば [Azure Front Door](/azure/frontdoor/front-door-overview) を使用すると、アプリへの Web トラフィックのグローバルなルーティングを定義、管理、監視することができ、Blazor Server アプリがロング ポーリングを使用するように構成されている場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-188">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app and works when Blazor Server apps are configured to use Long Polling.</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="d8c55-189">ロング ポーリングは Blazor Server アプリ用にサポートされていますが、[WebSocket が推奨されるトランスポート プロトコル](xref:blazor/host-and-deploy/server#azure-signalr-service)です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-189">Although Long Polling is supported for Blazor Server apps, [WebSockets is the recommended transport protocol](xref:blazor/host-and-deploy/server#azure-signalr-service).</span></span> <span data-ttu-id="d8c55-190">現時点では、[Azure Front Door](/azure/frontdoor/front-door-overview) では WebSocket はサポートされていませんが、WebSocket のサポートは今後のサービスのリリースに向けて検討中です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-190">[Azure Front Door](/azure/frontdoor/front-door-overview) doesn't support WebSockets at this time, but support for WebSockets is under consideration for a future release of the service.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="d8c55-191">サービス拒否 (DoS) 攻撃</span><span class="sxs-lookup"><span data-stu-id="d8c55-191">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="d8c55-192">サービス拒否 (DoS) 攻撃では、クライアントがサーバーにそのリソースを 1 つ以上使い果たさせることで、アプリを利用できなくします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-192">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="d8c55-193">Blazor Server アプリには、既定の制限があり、DoS 攻撃から保護するために <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> で設定される他の ASP.NET Core や SignalR の制限に依存しています。</span><span class="sxs-lookup"><span data-stu-id="d8c55-193">Blazor Server apps include default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks that are set on <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>:</span></span>

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>
* <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>

<span data-ttu-id="d8c55-194">詳細および構成コーディングの例については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-194">For more information and configuration coding examples, see the following articles:</span></span>

* <xref:blazor/fundamentals/signalr>
* <xref:signalr/configuration>

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="d8c55-195">ブラウザー (クライアント) との対話</span><span class="sxs-lookup"><span data-stu-id="d8c55-195">Interactions with the browser (client)</span></span>

<span data-ttu-id="d8c55-196">クライアントは、JS 相互運用イベントのディスパッチとレンダリングの完了を通じてサーバーと対話します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-196">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="d8c55-197">JS 相互運用通信は、JavaScript と .NET の間で双方向に行われます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-197">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="d8c55-198">ブラウザー イベントは、非同期方式でクライアントからサーバーにディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-198">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="d8c55-199">サーバーは、必要に応じて UI を非同期に再レンダリングして応答します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-199">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="d8c55-200">.NET から呼び出される JavaScript 関数</span><span class="sxs-lookup"><span data-stu-id="d8c55-200">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="d8c55-201">.NET メソッドから JavaScript への呼び出しの場合:</span><span class="sxs-lookup"><span data-stu-id="d8c55-201">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="d8c55-202">すべての呼び出しには、構成可能なタイムアウトがあり、失敗すると、呼び出し元に <xref:System.OperationCanceledException> が返されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-202">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="d8c55-203">呼び出し (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) の既定のタイムアウトは 1 分です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-203">There's a default timeout for the calls (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) of one minute.</span></span> <span data-ttu-id="d8c55-204">この制限を構成するには、<xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls> に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-204">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="d8c55-205">キャンセル トークンを指定して、呼び出しごとに取り消しを制御できます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-205">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="d8c55-206">可能な限り既定の呼び出しタイムアウトを使用し、キャンセル トークンが指定されている場合は、クライアントへの呼び出しに時間制限を設けます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-206">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="d8c55-207">JavaScript 呼び出しの結果は信頼できません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-207">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="d8c55-208">ブラウザーで実行されている Blazor アプリ クライアントは、呼び出す JavaScript 関数を検索します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-208">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="d8c55-209">関数が呼び出され、結果またはエラーのいずれかが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-209">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="d8c55-210">悪意のあるクライアントは次のことを試みる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-210">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="d8c55-211">JavaScript 関数からエラーを返すことによって、アプリで問題を発生させる。</span><span class="sxs-lookup"><span data-stu-id="d8c55-211">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="d8c55-212">JavaScript 関数から予期しない結果を返すことによって、サーバーで意図しない動作を誘発させる。</span><span class="sxs-lookup"><span data-stu-id="d8c55-212">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="d8c55-213">上記のシナリオを防止するために、次の予防措置を講じてください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-213">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="d8c55-214">呼び出し中に発生する可能性のあるエラーを考慮するために、[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメント内に JS 相互運用呼び出しをラップします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-214">Wrap JS interop calls within [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="d8c55-215">詳細については、「<xref:blazor/fundamentals/handle-errors#javascript-interop>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-215">For more information, see <xref:blazor/fundamentals/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="d8c55-216">アクションを実行する前に、JS 相互運用の呼び出しから返されたデータ (エラー メッセージを含む) を検証します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-216">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="d8c55-217">ブラウザーから呼び出される .NET メソッド</span><span class="sxs-lookup"><span data-stu-id="d8c55-217">.NET methods invoked from the browser</span></span>

<span data-ttu-id="d8c55-218">JavaScript から .NET メソッドへの呼び出しを信頼しないでください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-218">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="d8c55-219">.NET メソッドが JavaScript に公開されている場合は、.NET メソッドの呼び出し方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-219">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="d8c55-220">JavaScript に公開されているすべての .NET メソッドは、アプリのパブリック エンドポイントと同様に扱います。</span><span class="sxs-lookup"><span data-stu-id="d8c55-220">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="d8c55-221">入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-221">Validate input.</span></span>
    * <span data-ttu-id="d8c55-222">値が想定される範囲内であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-222">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="d8c55-223">ユーザーが要求された操作を実行するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-223">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="d8c55-224">.NET メソッドの呼び出しの一部として、過剰な量のリソースを割り当てないでください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-224">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="d8c55-225">たとえば、チェックを実行し、CPU とメモリの使用量に制限を設けます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-225">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="d8c55-226">静的メソッドとインスタンス メソッドは JavaScript クライアントに公開できることを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-226">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="d8c55-227">設計で適切な制約を使用して状態の共有を行う必要がある場合を除き、セッション間で状態を共有しないでください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-227">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="d8c55-228">依存関係の挿入 (DI) によって最初に作成された `DotNetReference` オブジェクトを介して公開されるインスタンス メソッドの場合、オブジェクトはスコープ付きオブジェクトとして登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-228">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="d8c55-229">これは、Blazor Server アプリが使用するすべての DI サービスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-229">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="d8c55-230">静的メソッドの場合は、アプリが明示的にサーバー インスタンス上のすべてのユーザー間で状態を明示的に共有している場合を除き、クライアントにスコープを設定できない状態を確立しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-230">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="d8c55-231">ユーザーが指定したデータをパラメーターで JavaScript の呼び出しに渡さないでください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-231">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="d8c55-232">どうしてもパラメーターでデータを渡す必要がある場合は、[クロスサイト スクリプティング (XSS)](#cross-site-scripting-xss) の脆弱性を取り込むことなく、JavaScript コードでデータの引き渡しを処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-232">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="d8c55-233">たとえば、要素の `innerHTML` プロパティを設定することによって、ユーザー指定のデータをドキュメント オブジェクト モデル (DOM) に書き込まないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-233">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="d8c55-234">[コンテンツ セキュリティ ポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) を使用して、`eval` およびその他の安全でない JavaScript プリミティブを無効にすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-234">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="d8c55-235">フレームワークのディスパッチ実装の上に .NET 呼び出しのカスタム ディスパッチを実装しないでください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-235">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="d8c55-236">ブラウザーに .NET メソッドを公開することは高度なシナリオであるため、一般的な Blazor 開発にはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-236">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="d8c55-237">イベント</span><span class="sxs-lookup"><span data-stu-id="d8c55-237">Events</span></span>

<span data-ttu-id="d8c55-238">イベントは、Blazor Server アプリへのエントリ ポイントを提供します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-238">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="d8c55-239">Web アプリでエンドポイントを保護する場合と同じ規則が、Blazor Server アプリのイベント処理に適用されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-239">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="d8c55-240">悪意のあるクライアントが、イベントのペイロードとして送信したい任意のデータを送信する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-240">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="d8c55-241">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-241">For example:</span></span>

* <span data-ttu-id="d8c55-242">`<select>` の変更イベントでは、アプリがクライアントに提示したオプションに含まれていない値が送信されることがあります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-242">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="d8c55-243">`<input>` は、クライアント側の検証をバイパスして、テキスト データをサーバーに送信することがあります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-243">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="d8c55-244">アプリが処理する任意のイベントのデータをアプリで検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-244">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="d8c55-245">Blazor フレームワークの[フォーム コンポーネント](xref:blazor/forms-validation)では、基本的な検証が実行されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-245">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="d8c55-246">アプリでカスタム フォーム コンポーネントを使用する場合は、必要に応じてカスタム コードを記述してイベント データを検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-246">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

<span data-ttu-id="d8c55-247">Blazor Server イベントは非同期であるため、アプリが新しいレンダリングを生成することによって反応するまでに、複数のイベントをサーバーにディスパッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-247">Blazor Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="d8c55-248">これには、考慮すべきセキュリティへの影響がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-248">This has some security implications to consider.</span></span> <span data-ttu-id="d8c55-249">アプリでのクライアント アクションの制限は、イベント ハンドラー内で実行する必要があり、現在レンダリングされているビュー状態に依存してはいけません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-249">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="d8c55-250">ユーザーがカウンターを最大 3 回インクリメントできるようにするカウンター コンポーネントを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="d8c55-250">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="d8c55-251">カウンターをインクリメントするボタンは、条件付きで `count` の値に基づいています。</span><span class="sxs-lookup"><span data-stu-id="d8c55-251">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="d8c55-252">クライアントは、フレームワークがこのコンポーネントの新しいレンダリングを生成する前に、1 つ以上のインクリメント イベントをディスパッチできます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-252">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="d8c55-253">その結果、UI によってすぐにボタンが削除されないため、ユーザーは `count` を "*3 回以上*" インクリメントできます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-253">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="d8c55-254">3 回の `count` のインクリメントの制限を実現するための正しい方法を次の例で示します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-254">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="d8c55-255">ハンドラー内に `if (count < 3) { ... }` チェックを追加することによって、`count` をインクリメントするかどうかは、現在のアプリの状態に基づいて決定されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-255">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="d8c55-256">この決定は、前の例とは異なり、一時的に古くなっている可能性がある UI の状態に基づいていません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-256">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="d8c55-257">複数のディスパッチに対するガード</span><span class="sxs-lookup"><span data-stu-id="d8c55-257">Guard against multiple dispatches</span></span>

<span data-ttu-id="d8c55-258">イベント コールバックによって、長時間実行される操作 (外部サービスまたはデータベースからのデータのフェッチなど) が非同期に呼び出される場合は、ガードの使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-258">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="d8c55-259">ガードを使用すると、視覚的なフィードバックを使用して、操作の進行中に、ユーザーが複数の操作をキューに入れることを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-259">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="d8c55-260">次のコンポーネント コードでは、`GetForecastAsync` がサーバーからデータを取得する間に、`isLoading` を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-260">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="d8c55-261">`isLoading` が `true` の間は、このボタンは UI では無効になります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-261">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="d8c55-262">前の例で示したガード パターンは、バックグラウンド操作が `async`-`await` パターンで非同期に実行される場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-262">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="d8c55-263">早期にキャンセルして、破棄後の使用を回避する</span><span class="sxs-lookup"><span data-stu-id="d8c55-263">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="d8c55-264">コンポーネントが破棄される場合は、「[複数のディスパッチに対するガード](#guard-against-multiple-dispatches)」セクションで説明したガードの使用に加えて、<xref:System.Threading.CancellationToken> を使用して長時間実行される操作を取り消すことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-264">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="d8c55-265">このアプローチには、コンポーネントで "*破棄後の使用*" を回避するという追加の利点があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-265">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="d8c55-266">大量のデータを生成するイベントを回避する</span><span class="sxs-lookup"><span data-stu-id="d8c55-266">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="d8c55-267">`oninput` や `onscroll` などの一部の DOM イベントでは、大量のデータが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-267">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="d8c55-268">Blazor サーバー アプリでは、これらのイベントを使用しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-268">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="d8c55-269">セキュリティに関するその他のガイダンス</span><span class="sxs-lookup"><span data-stu-id="d8c55-269">Additional security guidance</span></span>

<span data-ttu-id="d8c55-270">ASP.NET Core アプリをセキュリティで保護するためのガイダンスが、Blazor Server アプリに適用されます。詳細については、以降のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-270">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="d8c55-271">ログ記録と機密データ</span><span class="sxs-lookup"><span data-stu-id="d8c55-271">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="d8c55-272">HTTPS を使用した転送中の情報の保護</span><span class="sxs-lookup"><span data-stu-id="d8c55-272">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* [<span data-ttu-id="d8c55-273">クロスサイト スクリプティング (XSS)</span><span class="sxs-lookup"><span data-stu-id="d8c55-273">Cross-site scripting (XSS)</span></span>](#cross-site-scripting-xss)
* [<span data-ttu-id="d8c55-274">クロスオリジン保護</span><span class="sxs-lookup"><span data-stu-id="d8c55-274">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="d8c55-275">クリック ジャッキング</span><span class="sxs-lookup"><span data-stu-id="d8c55-275">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="d8c55-276">オープン リダイレクト</span><span class="sxs-lookup"><span data-stu-id="d8c55-276">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="d8c55-277">ログ記録と機密データ</span><span class="sxs-lookup"><span data-stu-id="d8c55-277">Logging and sensitive data</span></span>

<span data-ttu-id="d8c55-278">クライアントとサーバー間の JS 相互運用操作は、<xref:Microsoft.Extensions.Logging.ILogger> インスタンスと共にサーバーのログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-278">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> <span data-ttu-id="d8c55-279">Blazor では、実際のイベントや JS 相互運用の入力や出力などの機密情報のログ記録が回避されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-279">Blazor avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="d8c55-280">サーバーでエラーが発生すると、フレームワークによってクライアントに通知され、セッションが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-280">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="d8c55-281">既定では、クライアントは、ブラウザーの開発者ツールで見られる一般的なエラー メッセージを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-281">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="d8c55-282">クライアント側のエラーには、呼び出し履歴は含まれず、エラーの原因についての詳細は提供されませんが、サーバー ログにはこのような情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d8c55-282">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="d8c55-283">開発目的で、[詳細なエラーを有効にする](xref:blazor/fundamentals/handle-errors#blazor-server-detailed-circuit-errors)ことによって、機密性の高いエラー情報をクライアントが利用できるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-283">For development purposes, sensitive error information can be made available to the client by [enabling detailed errors](xref:blazor/fundamentals/handle-errors#blazor-server-detailed-circuit-errors).</span></span>

> [!WARNING]
> <span data-ttu-id="d8c55-284">インターネット上でクライアントにエラー情報を公開することは、常に回避すべきセキュリティ リスクです。</span><span class="sxs-lookup"><span data-stu-id="d8c55-284">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="d8c55-285">HTTPS を使用した転送中の情報の保護</span><span class="sxs-lookup"><span data-stu-id="d8c55-285">Protect information in transit with HTTPS</span></span>

<span data-ttu-id="d8c55-286">Blazor Server では、クライアントとサーバー間の通信に SignalR が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-286">Blazor Server uses SignalR for communication between the client and the server.</span></span> <span data-ttu-id="d8c55-287">Blazor Server では通常、SignalR がネゴシエートするトランスポート (通常は WebSocket) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-287">Blazor Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

<span data-ttu-id="d8c55-288">Blazor Server では、サーバーとクライアントの間で送信されるデータの整合性と機密性は保証されません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-288">Blazor Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="d8c55-289">常に HTTPS を使用します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-289">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="d8c55-290">クロスサイト スクリプティング (XSS)</span><span class="sxs-lookup"><span data-stu-id="d8c55-290">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="d8c55-291">クロスサイトス クリプティング (XSS) を使用すると、承認されていないパーティが、ブラウザーのコンテキストで任意のロジックを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-291">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="d8c55-292">侵害されたアプリが、クライアントで任意のコードを実行する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-292">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="d8c55-293">脆弱性を利用して、サーバーに対して多くの悪意のある操作が実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-293">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="d8c55-294">偽のイベントや無効なイベントをサーバーにディスパッチする。</span><span class="sxs-lookup"><span data-stu-id="d8c55-294">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="d8c55-295">失敗または無効なレンダリング完了をディスパッチする。</span><span class="sxs-lookup"><span data-stu-id="d8c55-295">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="d8c55-296">レンダリング完了のディスパッチを回避する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-296">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="d8c55-297">JavaScript から相互運用呼び出しを .NET にディスパッチする。</span><span class="sxs-lookup"><span data-stu-id="d8c55-297">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="d8c55-298">.NET から JavaScript への相互運用呼び出しの応答を変更する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-298">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="d8c55-299">JS 相互運用の結果に .NET をディスパッチすることを回避する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-299">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="d8c55-300">Blazor Server フレームワークでは、前述のいくつかの脅威から保護するためのステップが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-300">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="d8c55-301">クライアントがレンダリング バッチを確認していない場合は、新しい UI 更新の生成を停止します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-301">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="d8c55-302"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> で構成されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-302">Configured with <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="d8c55-303">クライアントからの応答を受信せずに、1 分後に .NET から JavaScript への呼び出しをタイムアウトにします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-303">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="d8c55-304"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> で構成されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-304">Configured with <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="d8c55-305">JS 相互運用中にブラウザーからのすべての入力に対して、次の基本的な検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-305">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="d8c55-306">.NET 参照は有効であり、.NET メソッドで予期される型であること。</span><span class="sxs-lookup"><span data-stu-id="d8c55-306">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="d8c55-307">データが不正な形式ではないこと。</span><span class="sxs-lookup"><span data-stu-id="d8c55-307">The data isn't malformed.</span></span>
  * <span data-ttu-id="d8c55-308">メソッドの正しい数の引数がペイロードに存在していること。</span><span class="sxs-lookup"><span data-stu-id="d8c55-308">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="d8c55-309">メソッドを呼び出す前に、引数または結果が正しく逆シリアル化できること。</span><span class="sxs-lookup"><span data-stu-id="d8c55-309">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="d8c55-310">次に示すディスパッチされたイベントから、ブラウザーからのすべての入力に対して、次の基本的な検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-310">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="d8c55-311">イベントに有効な型があること。</span><span class="sxs-lookup"><span data-stu-id="d8c55-311">The event has a valid type.</span></span>
  * <span data-ttu-id="d8c55-312">イベントのデータを逆シリアル化できること。</span><span class="sxs-lookup"><span data-stu-id="d8c55-312">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="d8c55-313">イベントに関連付けられているイベント ハンドラーがあること。</span><span class="sxs-lookup"><span data-stu-id="d8c55-313">There's an event handler associated with the event.</span></span>

<span data-ttu-id="d8c55-314">脅威から保護し、適切なアクションを実行するには、フレームワークが実装するセーフガードに加えて、アプリが開発者によってコーディングされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-314">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="d8c55-315">イベントを処理するときは常にデータを検証します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-315">Always validate data when handling events.</span></span>
* <span data-ttu-id="d8c55-316">無効なデータの受信時に適切なアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-316">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="d8c55-317">データを無視して戻ります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-317">Ignore the data and return.</span></span> <span data-ttu-id="d8c55-318">これにより、アプリは要求の処理を続行できます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-318">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="d8c55-319">アプリは、入力が不正であり、正当なクライアントによって生成されたものではないと判断すると、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-319">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="d8c55-320">例外をスローすると、回線が破棄され、セッションが終了します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-320">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="d8c55-321">ログに含まれるレンダリング バッチの完了によって提供されるエラー メッセージは信頼しないでください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-321">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="d8c55-322">このエラーはクライアントによって提供され、クライアントが侵害されている可能性があるため、通常は信頼できません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-322">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="d8c55-323">JavaScript と .NET メソッドの間では、どちらの方向でも JS 相互運用呼び出しの入力を信頼しないでください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-323">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="d8c55-324">引数または結果が正しく逆シリアル化された場合でも、引数と結果の内容が有効であることを検証するのはアプリの役割です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-324">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="d8c55-325">XSS の脆弱性が存在するには、アプリがレンダリングされたページにユーザー入力を組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-325">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> <span data-ttu-id="d8c55-326">Blazor Server コンポーネントでは、`.razor` ファイルのマークアップが手続き型 C# ロジックに変換されるコンパイル時のステップが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-326">Blazor Server components execute a compile-time step where the markup in a `.razor` file is transformed into procedural C# logic.</span></span> <span data-ttu-id="d8c55-327">実行時に、C# ロジックによって、要素、テキスト、および子コンポーネントを記述する "*レンダリング ツリー*" が構築されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-327">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="d8c55-328">これは、JavaScript 命令のシーケンスを通じてブラウザーの DOM に適用されます (または、プリレンダリングの場合は HTML にシリアル化されます)。</span><span class="sxs-lookup"><span data-stu-id="d8c55-328">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="d8c55-329">通常の Razor 構文 (`@someStringValue` など) を使用してレンダリングされたユーザー入力では、XSS 脆弱性は公開されません。これは、Razor 構文が、テキストのみを書き込むことができるコマンドを使用して DOM に追加されるためです。</span><span class="sxs-lookup"><span data-stu-id="d8c55-329">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="d8c55-330">値に HTML マークアップが含まれている場合でも、値は静的なテキストとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-330">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="d8c55-331">プリレンダリング時に、出力は HTML エンコードされ、コンテンツも静的テキストとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-331">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="d8c55-332">スクリプト タグは許可されていないため、アプリのコンポーネント レンダリング ツリーに含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-332">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="d8c55-333">コンポーネントのマークアップにスクリプト タグが含まれていると、コンパイル時のエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-333">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="d8c55-334">コンポーネントの作成者は、Razor を使用せずに C# でコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-334">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="d8c55-335">コンポーネントの作成者は、出力の生成時に適切な API を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-335">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="d8c55-336">たとえば、`builder.AddContent(0, someUserSuppliedString)` を使用します。`builder.AddMarkupContent(0, someUserSuppliedString)` は使用 "*しないでください*"。後者では XSS 脆弱性が生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-336">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="d8c55-337">XSS 攻撃からの保護の一環として、[コンテンツ セキュリティ ポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) などの XSS 軽減策を実装することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-337">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="d8c55-338">詳細については、「<xref:security/cross-site-scripting>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-338">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="d8c55-339">クロスオリジン保護</span><span class="sxs-lookup"><span data-stu-id="d8c55-339">Cross-origin protection</span></span>

<span data-ttu-id="d8c55-340">クロスオリジン攻撃では、サーバーに対してアクションを実行する別のオリジンのクライアントが関与します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-340">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="d8c55-341">悪意のあるアクションは通常、GET 要求またはフォーム POST (クロスサイト リクエスト フォージェリ (CSRF)) ですが、悪意のある WebSocket を開くことも可能です。</span><span class="sxs-lookup"><span data-stu-id="d8c55-341">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> <span data-ttu-id="d8c55-342">Blazor Server アプリでは、[ハブ プロトコルを使用する他の SignalR アプリが提供するのと同じ保証](xref:signalr/security)を提供します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-342">Blazor Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* <span data-ttu-id="d8c55-343">クロスオリジンを防ぐ追加の手段を講じない限り、Blazor Server アプリにアクセスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-343">Blazor Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="d8c55-344">クロスオリジンのアクセスを無効にするには、CORS ミドルウェアをパイプラインに追加し、<xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> を Blazor エンドポイント メタデータに追加することによって、エンドポイントで CORS を無効にするか、[クロスオリジン リソース共有に SignalR を構成する](xref:signalr/security#cross-origin-resource-sharing)ことによって、許可されるオリジンのセットを制限します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-344">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="d8c55-345">CORS が有効になっている場合、CORS の構成によっては、アプリを保護するために追加のステップが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-345">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="d8c55-346">CORS がグローバルに有効になっている場合、Blazor Server ハブに対する CORS を無効にするには、エンドポイント ルート ビルダーで <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> を呼び出した後、エンドポイントのメタデータに <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> メタデータを追加します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-346">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> metadata to the endpoint metadata after calling <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> on the endpoint route builder.</span></span>

<span data-ttu-id="d8c55-347">詳細については、「<xref:security/anti-request-forgery>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-347">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="d8c55-348">クリック ジャッキング</span><span class="sxs-lookup"><span data-stu-id="d8c55-348">Click-jacking</span></span>

<span data-ttu-id="d8c55-349">クリック ジャッキングでは、ユーザーをだまして攻撃を受けているサイトでアクションを実行させるために、サイトを異なるオリジンのサイト内の `<iframe>` としてレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="d8c55-349">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="d8c55-350">`<iframe>` 内でのレンダリングからアプリを保護するには、[コンテンツ セキュリティ ポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) と `X-Frame-Options` ヘッダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-350">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="d8c55-351">詳細については、[MDN Web ドキュメント:X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-351">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="d8c55-352">オープン リダイレクト</span><span class="sxs-lookup"><span data-stu-id="d8c55-352">Open redirects</span></span>

<span data-ttu-id="d8c55-353">Blazor Server アプリ セッションが開始されると、セッションの開始時に送信される URL の基本的な検証がサーバーによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-353">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="d8c55-354">フレームワークでは、回線を確立する前に、ベース URL が現在の URL の親であることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-354">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="d8c55-355">フレームワークでは、追加のチェックは実行されません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-355">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="d8c55-356">クライアントでユーザーがリンクを選択すると、リンクの URL がサーバーに送信され、これにより実行するアクションが決まります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-356">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="d8c55-357">たとえば、アプリがクライアント側のナビゲーションを実行したり、新しい場所に移動するようにブラウザーに指示したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-357">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="d8c55-358">コンポーネントでも、<xref:Microsoft.AspNetCore.Components.NavigationManager> を使用して、プログラムによってナビゲーション要求をトリガーすることができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-358">Components can also trigger navigation requests programatically through the use of <xref:Microsoft.AspNetCore.Components.NavigationManager>.</span></span> <span data-ttu-id="d8c55-359">このようなシナリオでは、アプリがクライアント側のナビゲーションを実行したり、新しい場所に移動するようにブラウザーに指示したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-359">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="d8c55-360">コンポーネントでは、次のことを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-360">Components must:</span></span>

* <span data-ttu-id="d8c55-361">ナビゲーション呼び出しの引数の一部としてのユーザー入力の使用を避ける。</span><span class="sxs-lookup"><span data-stu-id="d8c55-361">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="d8c55-362">引数を検証して、ターゲットがアプリで許可されていることを保証する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-362">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="d8c55-363">そうしないと、悪意のあるユーザーが、ブラウザーを攻撃者が制御するサイトに強制的に移動させる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-363">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="d8c55-364">このシナリオでは、攻撃者はアプリをだまして <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> メソッドの呼び出しの一部としてユーザー入力を使用させます。</span><span class="sxs-lookup"><span data-stu-id="d8c55-364">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="d8c55-365">このアドバイスは、アプリの一部としてリンクをレンダリングする場合にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="d8c55-365">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="d8c55-366">可能であれば、相対リンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-366">If possible, use relative links.</span></span>
* <span data-ttu-id="d8c55-367">絶対リンクをページに含める前に、リンク先が有効であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="d8c55-367">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="d8c55-368">詳細については、「<xref:security/preventing-open-redirects>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d8c55-368">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="d8c55-369">セキュリティ チェックリスト</span><span class="sxs-lookup"><span data-stu-id="d8c55-369">Security checklist</span></span>

<span data-ttu-id="d8c55-370">次のセキュリティの考慮事項のリストは、包括的なものではありません。</span><span class="sxs-lookup"><span data-stu-id="d8c55-370">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="d8c55-371">イベントからの引数を検証する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-371">Validate arguments from events.</span></span>
* <span data-ttu-id="d8c55-372">JS 相互運用呼び出しからの入力と結果を検証する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-372">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="d8c55-373">.NET から JS への相互運用呼び出しに対してユーザー入力を使用しないようにする (または事前に検証する)。</span><span class="sxs-lookup"><span data-stu-id="d8c55-373">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="d8c55-374">クライアントが、解放されたメモリ容量を割り当てないようにする。</span><span class="sxs-lookup"><span data-stu-id="d8c55-374">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="d8c55-375">コンポーネント内のデータ。</span><span class="sxs-lookup"><span data-stu-id="d8c55-375">Data within the component.</span></span>
  * <span data-ttu-id="d8c55-376">クライアントに返された `DotNetObject` 参照。</span><span class="sxs-lookup"><span data-stu-id="d8c55-376">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="d8c55-377">複数のディスパッチに対するガード。</span><span class="sxs-lookup"><span data-stu-id="d8c55-377">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="d8c55-378">コンポーネントが破棄されるときに、実行時間の長い操作をキャンセルする。</span><span class="sxs-lookup"><span data-stu-id="d8c55-378">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="d8c55-379">大量のデータを生成するイベントを回避する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-379">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="d8c55-380"><xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> の呼び出しの一部としてユーザー入力を使用することは避け、避けられない場合は、最初に許可されたオリジンのセットに対して URL のユーザー入力を検証する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-380">Avoid using user input as part of calls to <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="d8c55-381">承認は、UI の状態に基づいてではなく、コンポーネントの状態からのみ判断する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-381">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="d8c55-382">XSS 攻撃から保護するために[コンテンツ セキュリティ ポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) の使用を検討する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-382">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="d8c55-383">クリック ジャッキングから保護するため、CSP と [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) の使用を検討する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-383">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="d8c55-384">CORS を有効にする場合や Blazor アプリの CORS を明示的に無効にする場合は、CORS 設定が適切であることを確認する。</span><span class="sxs-lookup"><span data-stu-id="d8c55-384">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="d8c55-385">Blazor アプリのサーバー側の制限が、許容できないレベルのリスクなしに、許容できるユーザー エクスペリエンスを提供することを保証するためにテストする。</span><span class="sxs-lookup"><span data-stu-id="d8c55-385">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>

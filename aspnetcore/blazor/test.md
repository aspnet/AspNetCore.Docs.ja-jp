---
title: ASP.NET Core Blazor のコンポーネントをテストする
author: guardrex
description: Blazor アプリのコンポーネントをテストする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 67ebfcd322ae08acf2fddae9bd6101f13fa77e7e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280706"
---
# <a name="test-components-in-aspnet-core-blazor"></a><span data-ttu-id="4cb59-103">ASP.NET Core Blazor のコンポーネントをテストする</span><span class="sxs-lookup"><span data-stu-id="4cb59-103">Test components in ASP.NET Core Blazor</span></span>

<span data-ttu-id="4cb59-104">作成者: [Egil Hansen](https://egilhansen.com/)</span><span class="sxs-lookup"><span data-stu-id="4cb59-104">By: [Egil Hansen](https://egilhansen.com/)</span></span>

<span data-ttu-id="4cb59-105">テストは、安定し、保守性に優れたソフトウェアを構築する上で重要な作業です。</span><span class="sxs-lookup"><span data-stu-id="4cb59-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="4cb59-106">Blazor コンポーネントをテストするために、"*テスト対象のコンポーネント*" (CUT) に対して次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="4cb59-107">テスト用の関連する入力でレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="4cb59-108">実行されるテストの種類によっては、操作または変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4cb59-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="4cb59-109">たとえば、ボタンの `onclick` イベントなどのイベント ハンドラーがトリガーされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4cb59-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="4cb59-110">予想される値について検査されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="4cb59-111">テスト アプローチ</span><span class="sxs-lookup"><span data-stu-id="4cb59-111">Test approaches</span></span>

<span data-ttu-id="4cb59-112">Blazor のコンポーネントをテストするための一般的な 2 つのアプローチは、エンド ツー エンド (E2E) テストと単体テストです。</span><span class="sxs-lookup"><span data-stu-id="4cb59-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="4cb59-113">**単体テスト**: [単体テスト](/dotnet/core/testing/)は、次の機能を提供する単体テスト ライブラリを使用して記述されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="4cb59-114">コンポーネントのレンダリング。</span><span class="sxs-lookup"><span data-stu-id="4cb59-114">Component rendering.</span></span>
  * <span data-ttu-id="4cb59-115">コンポーネントの出力と状態の検査。</span><span class="sxs-lookup"><span data-stu-id="4cb59-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="4cb59-116">イベント ハンドラーとライフ サイクル メソッドのトリガー。</span><span class="sxs-lookup"><span data-stu-id="4cb59-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="4cb59-117">コンポーネントの動作が正しいことのアサーション。</span><span class="sxs-lookup"><span data-stu-id="4cb59-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="4cb59-118">[bUnit](https://github.com/egil/bUnit) は、Razor コンポーネントの単体テストを実現するライブラリの一例です。</span><span class="sxs-lookup"><span data-stu-id="4cb59-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="4cb59-119">**E2E テスト**: テスト ランナーでは、CUT を含む Blazor アプリを実行し、ブラウザー インスタンスを自動化します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="4cb59-120">テスト ツールでは、ブラウザーを介して CUT を検査および操作します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="4cb59-121">[Selenium](https://github.com/SeleniumHQ/selenium) は、Blazor アプリで使用できる E2E テスト フレームワークの一例です。</span><span class="sxs-lookup"><span data-stu-id="4cb59-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="4cb59-122">単体テストでは、Blazor コンポーネント (Razor/C#) のみが関係します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="4cb59-123">サービスや JS 相互運用などの外部の依存関係は、複製する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4cb59-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="4cb59-124">E2E テストでは、Blazor コンポーネントとそのすべての補助インフラストラクチャ (CSS、JS、DOM、ブラウザー API など) がテストに含まれます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="4cb59-125">"*テスト スコープ*" では、テストの範囲を説明します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="4cb59-126">通常、テスト スコープは、テストの速度に影響します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="4cb59-127">単体テストは、アプリのサブシステムのサブセットに対して実行され、通常、ミリ秒単位で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="4cb59-128">E2E テストでは、アプリのサブシステムの広範なグループがテストされ、完了するまで数秒かかることがあります。</span><span class="sxs-lookup"><span data-stu-id="4cb59-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="4cb59-129">単体テストでは、CUT のインスタンスにもアクセスでき、コンポーネントの内部状態の検査と検証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="4cb59-130">通常、これは、E2E テストでは行うことができません。</span><span class="sxs-lookup"><span data-stu-id="4cb59-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="4cb59-131">コンポーネントの環境に関しては、E2E テストでは、検証を開始する前に、予想される環境状態に達していることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4cb59-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="4cb59-132">そうしなければ、結果は予測できません。</span><span class="sxs-lookup"><span data-stu-id="4cb59-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="4cb59-133">単体テストでは、CUT のレンダリングとテストのライフ サイクルはより統合されるため、テストの安定性が向上します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="4cb59-134">E2E テストでは、複数のプロセス、ネットワークとディスクの I/O、その他のサブシステムのアクティビティを起動する必要があり、多くの場合、これによってテストの信頼性が低下します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="4cb59-135">通常、単体テストは、このような種類の問題とは無関係です。</span><span class="sxs-lookup"><span data-stu-id="4cb59-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="4cb59-136">次の表は、この 2 つのテスト アプローチの違いをまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="4cb59-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="4cb59-137">機能</span><span class="sxs-lookup"><span data-stu-id="4cb59-137">Capability</span></span>                       | <span data-ttu-id="4cb59-138">単体テスト</span><span class="sxs-lookup"><span data-stu-id="4cb59-138">Unit testing</span></span>                     | <span data-ttu-id="4cb59-139">E2E テスト</span><span class="sxs-lookup"><span data-stu-id="4cb59-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="4cb59-140">テスト スコープ</span><span class="sxs-lookup"><span data-stu-id="4cb59-140">Test scope</span></span>                       | <span data-ttu-id="4cb59-141">Blazor component (Razor/C#) のみ</span><span class="sxs-lookup"><span data-stu-id="4cb59-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="4cb59-142">Blazor component (Razor/C#) と CSS/JS</span><span class="sxs-lookup"><span data-stu-id="4cb59-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="4cb59-143">テストの実行時間</span><span class="sxs-lookup"><span data-stu-id="4cb59-143">Test execution time</span></span>              | <span data-ttu-id="4cb59-144">ミリ秒</span><span class="sxs-lookup"><span data-stu-id="4cb59-144">Milliseconds</span></span>                     | <span data-ttu-id="4cb59-145">Seconds</span><span class="sxs-lookup"><span data-stu-id="4cb59-145">Seconds</span></span>                                 |
| <span data-ttu-id="4cb59-146">コンポーネント インスタンスへのアクセス</span><span class="sxs-lookup"><span data-stu-id="4cb59-146">Access to the component instance</span></span> | <span data-ttu-id="4cb59-147">はい</span><span class="sxs-lookup"><span data-stu-id="4cb59-147">Yes</span></span>                              | <span data-ttu-id="4cb59-148">いいえ</span><span class="sxs-lookup"><span data-stu-id="4cb59-148">No</span></span>                                      |
| <span data-ttu-id="4cb59-149">環境依存</span><span class="sxs-lookup"><span data-stu-id="4cb59-149">Sensitive to the environment</span></span>     | <span data-ttu-id="4cb59-150">いいえ</span><span class="sxs-lookup"><span data-stu-id="4cb59-150">No</span></span>                               | <span data-ttu-id="4cb59-151">はい</span><span class="sxs-lookup"><span data-stu-id="4cb59-151">Yes</span></span>                                     |
| <span data-ttu-id="4cb59-152">[信頼性]</span><span class="sxs-lookup"><span data-stu-id="4cb59-152">Reliability</span></span>                      | <span data-ttu-id="4cb59-153">信頼性が高い</span><span class="sxs-lookup"><span data-stu-id="4cb59-153">More reliable</span></span>                    | <span data-ttu-id="4cb59-154">信頼性が低い</span><span class="sxs-lookup"><span data-stu-id="4cb59-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="4cb59-155">最適なテスト アプローチを選択する</span><span class="sxs-lookup"><span data-stu-id="4cb59-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="4cb59-156">実行するテストの種類を選択する際には、シナリオを考慮します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="4cb59-157">次の表では、いくつかのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="4cb59-158">シナリオ</span><span class="sxs-lookup"><span data-stu-id="4cb59-158">Scenario</span></span> | <span data-ttu-id="4cb59-159">推奨されるアプローチ</span><span class="sxs-lookup"><span data-stu-id="4cb59-159">Suggested approach</span></span> | <span data-ttu-id="4cb59-160">解説</span><span class="sxs-lookup"><span data-stu-id="4cb59-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="4cb59-161">JS 相互運用ロジックのないコンポーネント</span><span class="sxs-lookup"><span data-stu-id="4cb59-161">Component without JS interop logic</span></span> | <span data-ttu-id="4cb59-162">単体テスト</span><span class="sxs-lookup"><span data-stu-id="4cb59-162">Unit testing</span></span> | <span data-ttu-id="4cb59-163">Blazor コンポーネント内に JS 相互運用への依存関係がない場合、JS または DOM API にアクセスせずにコンポーネントをテストできます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="4cb59-164">このシナリオでは、単体テストを選択することにデメリットはありません。</span><span class="sxs-lookup"><span data-stu-id="4cb59-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="4cb59-165">単純な JS 相互運用ロジックがあるコンポーネント</span><span class="sxs-lookup"><span data-stu-id="4cb59-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="4cb59-166">単体テスト</span><span class="sxs-lookup"><span data-stu-id="4cb59-166">Unit testing</span></span> | <span data-ttu-id="4cb59-167">コンポーネントでは、JS 相互運用を使用して、DOM に対してクエリを実行したり、アニメーションをトリガーしたりするのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="4cb59-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="4cb59-168"><xref:Microsoft.JSInterop.IJSRuntime> インターフェイスを使用して JS 相互運用を簡単に複製できるので、このシナリオでは、通常、単体テストをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4cb59-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="4cb59-169">複雑な JS コードに依存するコンポーネント</span><span class="sxs-lookup"><span data-stu-id="4cb59-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="4cb59-170">単体テストと個別の JS テスト</span><span class="sxs-lookup"><span data-stu-id="4cb59-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="4cb59-171">コンポーネントで、JS 相互運用を使用して大きなまたは複雑な JS ライブラリを呼び出すが、Blazor コンポーネントと JS ライブラリ間の対話は単純な場合、最適なアプローチは、コンポーネントと JS ライブラリまたはコードを 2 つの個別のパーツと見なして、それぞれを個別にテストすることです。</span><span class="sxs-lookup"><span data-stu-id="4cb59-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="4cb59-172">単体テスト ライブラリを使用して Blazor コンポーネントをテストし、JS テスト ライブラリを使用して JS をテストします。</span><span class="sxs-lookup"><span data-stu-id="4cb59-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="4cb59-173">ブラウザー DOM の JS 操作に依存するロジックのあるコンポーネント</span><span class="sxs-lookup"><span data-stu-id="4cb59-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="4cb59-174">E2E テスト</span><span class="sxs-lookup"><span data-stu-id="4cb59-174">E2E testing</span></span> | <span data-ttu-id="4cb59-175">コンポーネントの機能が JS および DOM の JS の操作に依存する場合、1 回の E2E テストで、JS と Blazor コードの両方をまとめて検証します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="4cb59-176">これは、Blazor フレームワーク開発者が、Blazorのブラウザー レンダリング ロジックで採用しているアプローチで、C# と JS コードが密結合されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="4cb59-177">ブラウザーで Blazor コンポーネントをレンダリングするには、C# と JS コードが連携する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4cb59-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="4cb59-178">複製が困難な依存関係を持つサードパーティ製コンポーネント ライブラリに依存するコンポーネント</span><span class="sxs-lookup"><span data-stu-id="4cb59-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="4cb59-179">E2E テスト</span><span class="sxs-lookup"><span data-stu-id="4cb59-179">E2E testing</span></span> | <span data-ttu-id="4cb59-180">コンポーネントの機能が、JS 相互運用など、複製が困難な依存関係を持つサードパーティ製コンポーネント ライブラリに依存している場合、E2E テストは、そのコンポーネントをテストするための唯一のオプションになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4cb59-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="4cb59-181">bUnit でコンポーネントをテストする</span><span class="sxs-lookup"><span data-stu-id="4cb59-181">Test components with bUnit</span></span>

<span data-ttu-id="4cb59-182">Blazor用の公式の Microsoft テスト フレームワークはありませんが、コミュニティ主導のプロジェクト [bUnit](https://github.com/egil/bUnit) では、Blazor コンポーネントの単体テストを行うための便利な方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="4cb59-183">bUnit はサードパーティ製のテスト ライブラリであり、Microsoft がサポートおよび保守するものではありません。</span><span class="sxs-lookup"><span data-stu-id="4cb59-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="4cb59-184">bUnit は、[MSTest](/dotnet/core/testing/unit-testing-with-mstest)、[NUnit](https://nunit.org/)、[xUnit](https://xunit.github.io/) などの汎用テスト フレームワークで動作します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="4cb59-185">これらのテスト フレームワークでは、bUnit テストは通常の単体テストと同様の外観になります。</span><span class="sxs-lookup"><span data-stu-id="4cb59-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="4cb59-186">汎用のテスト フレームワークと統合された bUnit テストは、通常、次の機能を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="4cb59-187">[Visual Studio テスト エクスプローラー](/visualstudio/test/run-unit-tests-with-test-explorer)。</span><span class="sxs-lookup"><span data-stu-id="4cb59-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="4cb59-188">コマンド シェル内の [`dotnet test`](/dotnet/core/tools/dotnet-test) CLI コマンド。</span><span class="sxs-lookup"><span data-stu-id="4cb59-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="4cb59-189">自動化された DevOps テスト パイプライン。</span><span class="sxs-lookup"><span data-stu-id="4cb59-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="4cb59-190">テストの概念とテストの実装は、異なるテスト フレームワークでも類似していますが、同一ではありません。</span><span class="sxs-lookup"><span data-stu-id="4cb59-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="4cb59-191">ガイダンスについては、テスト フレームワークのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cb59-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="4cb59-192">次の例は、Blazor プロジェクト テンプレートに基づくアプリ内の `Counter` コンポーネントに対する bUnit テストの構造を示します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="4cb59-193">`Counter` コンポーネントでは、ユーザーがページで選択するボタンに基づいてカウンターを表示し、増分します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="4cb59-194">次の bUnit テストでは、ボタンが選択されたときに CUT のカウンターが正しく増分されるかどうかを検証します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="4cb59-195">テストの各ステップでは、次のアクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="4cb59-196">*Arrange*: bUnit の `TestContext` を使用して、`Counter` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="4cb59-197">CUT の段落要素 (`<p>`) が検出され、`paraElm` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="4cb59-198">*Act*: ボタンの要素 (`<button>`) が配置され、`Click` を呼び出して選択されます。これにより、カウンターが増分され、段落タグ (`<p>`) の内容が更新されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="4cb59-199">段落要素のテキスト コンテンツは、`TextContent` を呼び出すことによって取得されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="4cb59-200">*Assert*: テキスト コンテンツで `MarkupMatches` が呼び出され、予想される文字列 `Current count: 1` と一致するかどうかが検証されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="4cb59-201">`MarkupMatches` Assert メソッドは、通常の文字列比較アサーション (たとえば、`Assert.Equal("Current count: 1", paraElmText);`) とは異なり、`MarkupMatches` は、入力と予想される HTML マークアップのセマンティック比較を実行します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="4cb59-202">セマンティック比較では、HTML セマンティクスが認識されます。つまり、意味のない空白のようなものは無視されます。</span><span class="sxs-lookup"><span data-stu-id="4cb59-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="4cb59-203">これにより、テストの安定性が向上します。</span><span class="sxs-lookup"><span data-stu-id="4cb59-203">This results in more stable tests.</span></span> <span data-ttu-id="4cb59-204">詳細については、「[Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison)」 (HTML のセマンティック比較のカスタマイズ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cb59-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4cb59-205">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="4cb59-205">Additional resources</span></span>

* <span data-ttu-id="4cb59-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/) (bUnit の概要): bUnit の説明には、テスト プロジェクトの作成、テスト フレームワーク パッケージの参照、テストの構築と実行に関するガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4cb59-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>

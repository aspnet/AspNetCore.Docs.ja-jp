---
title: ASP.NET Core Blazor アプリのエラーを処理する
author: guardrex
description: ASP.NET Core Blazor で、ハンドルされない例外を Blazor で管理する方法と、エラーを検出して処理するアプリを開発する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: f7cac477e2c5bca54e24ae3faeadff9b51bdcd0f
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101062"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="2ee51-103">ASP.NET Core Blazor アプリのエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="2ee51-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="2ee51-104">この記事では、ハンドルされない例外を Blazor で管理する方法と、エラーを検出して処理するアプリを開発する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="2ee51-105">開発中の詳細なエラー</span><span class="sxs-lookup"><span data-stu-id="2ee51-105">Detailed errors during development</span></span>

<span data-ttu-id="2ee51-106">開発中に Blazor アプリが正常に機能していない場合、アプリからの詳細なエラー情報を受け取ることで、問題のトラブルシューティングと修正に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="2ee51-107">エラーが発生すると Blazor アプリによって画面の下部に金色のバーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-107">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="2ee51-108">開発中は、金色のバーによってブラウザー コンソールが表示され、そこで例外を確認できます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-108">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="2ee51-109">実稼働環境では、金色のバーによって、エラーが発生したことがユーザーに通知され、ブラウザーの更新が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-109">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="2ee51-110">このエラー処理エクスペリエンスの UI は、Blazor プロジェクト テンプレートの一部です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-110">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="2ee51-111">Blazor WebAssembly アプリでは、`wwwroot/index.html` ファイルでエクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="2ee51-112">Blazor Server アプリでは、`Pages/_Host.cshtml` ファイルでエクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-112">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="2ee51-113">`blazor-error-ui` 要素は、Blazor テンプレート (`wwwroot/css/app.css` または `wwwroot/css/site.css`) に含まれるスタイルによって非表示にされ、エラーが発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-113">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="2ee51-114">Blazor Server の詳細な回線エラー</span><span class="sxs-lookup"><span data-stu-id="2ee51-114">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="2ee51-115">クライアント側のエラーには、呼び出し履歴は含まれず、エラーの原因についての詳細は提供されませんが、サーバー ログにはこのような情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2ee51-115">Client-side errors don't include the callstack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="2ee51-116">開発目的で、詳細なエラーを有効にすることによって、機密性の高い回線エラー情報をクライアントが利用できるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-116">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="2ee51-117">次の方法を使用して Blazor Server の詳細なエラーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-117">Enable Blazor Server detailed errors using the following approaches:</span></span>

* <span data-ttu-id="2ee51-118"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="2ee51-118"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="2ee51-119">`DetailedErrors` 構成キーを `true` に設定します。これはアプリの Development 設定ファイル (`appsettings.Development.json`) で設定できます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-119">The `DetailedErrors` configuration key set to `true`, which can be set in the app's Development settings file (`appsettings.Development.json`).</span></span> <span data-ttu-id="2ee51-120">このキーは、値を `true` にした `ASPNETCORE_DETAILEDERRORS` 環境変数を使用することで設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-120">The key can also be set using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true`.</span></span>
* <span data-ttu-id="2ee51-121">[SignalR のサーバー側ログ記録](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) を [Debug](xref:Microsoft.Extensions.Logging.LogLevel) または [Trace](xref:Microsoft.Extensions.Logging.LogLevel) に設定して、SignalR の詳細なログを記録することができます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-121">[SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) can be set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="2ee51-122">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="2ee51-122">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> [!WARNING]
> <span data-ttu-id="2ee51-123">インターネット上でクライアントにエラー情報を公開することは、常に回避すべきセキュリティ リスクです。</span><span class="sxs-lookup"><span data-stu-id="2ee51-123">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="2ee51-124">ハンドルされない例外に対して Blazor Server アプリがどのように反応するか</span><span class="sxs-lookup"><span data-stu-id="2ee51-124">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="2ee51-125">Blazor Server はステートフルなフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="2ee51-125">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="2ee51-126">ユーザーはアプリを操作するときに、*回線* と呼ばれる、サーバーへの接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-126">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="2ee51-127">回線では、アクティブなコンポーネント インスタンスに加えて、次のような状態の他の多くの側面が保持されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-127">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="2ee51-128">コンポーネントの表示される最新の出力。</span><span class="sxs-lookup"><span data-stu-id="2ee51-128">The most recent rendered output of components.</span></span>
* <span data-ttu-id="2ee51-129">クライアント側のイベントによってトリガーされる可能性がある、イベント処理デリゲートの現在のセット。</span><span class="sxs-lookup"><span data-stu-id="2ee51-129">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="2ee51-130">ユーザーが複数のブラウザー タブでアプリを開いた場合は、独立した回線が複数あります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-130">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="2ee51-131">Blazor は、ハンドルされない例外が発生した回線に対して、ほとんどの例外を致命的として処理します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-131">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="2ee51-132">ハンドルされない例外のために回線が終了された場合、ユーザーはページを再読み込みして新しい回線を作成するだけで、アプリの操作を続行できます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-132">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="2ee51-133">他のユーザーや他のブラウザー タブの回線である、終了された回線以外の回線は影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="2ee51-133">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="2ee51-134">このシナリオは、クラッシュするデスクトップ アプリに似ています。</span><span class="sxs-lookup"><span data-stu-id="2ee51-134">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="2ee51-135">クラッシュしたアプリを再起動する必要がありますが、他のアプリは影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="2ee51-135">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="2ee51-136">回線は、以下の理由でハンドルされない例外が発生したときに終了されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-136">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="2ee51-137">ハンドルされない例外によって、回線が未定義の状態のままになることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-137">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="2ee51-138">ハンドルされない例外の後は、アプリの通常動作を保証できません。</span><span class="sxs-lookup"><span data-stu-id="2ee51-138">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="2ee51-139">回線が存続している場合は、アプリにセキュリティの脆弱性が表れることがあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-139">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="2ee51-140">ハンドルされない例外を開発者コードで管理する</span><span class="sxs-lookup"><span data-stu-id="2ee51-140">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="2ee51-141">エラー後にアプリを続行するには、アプリがエラー処理ロジックを備えている必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-141">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="2ee51-142">この記事の後のセクションでは、ハンドルされない例外の潜在的原因について説明します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-142">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="2ee51-143">運用環境では、フレームワークの例外メッセージやスタック トレースを UI に表示しないでください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-143">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="2ee51-144">例外メッセージやスタック トレースを表示すると以下の可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-144">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="2ee51-145">エンド ユーザーに機密情報が開示される。</span><span class="sxs-lookup"><span data-stu-id="2ee51-145">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="2ee51-146">悪意のあるユーザーが、アプリ、サーバー、またはネットワークのセキュリティを侵害する可能性のある脆弱性をアプリの中で発見する助けになる。</span><span class="sxs-lookup"><span data-stu-id="2ee51-146">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="2ee51-147">永続的プロバイダーを使用してエラーをログに記録する</span><span class="sxs-lookup"><span data-stu-id="2ee51-147">Log errors with a persistent provider</span></span>

<span data-ttu-id="2ee51-148">ハンドルされない例外が発生した場合、例外は、サービス コンテナー内に構成されている <xref:Microsoft.Extensions.Logging.ILogger> インスタンスにログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-148">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="2ee51-149">既定では、Blazor アプリは、コンソール ログ プロバイダーを使用してコンソール出力にログを記録します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-149">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="2ee51-150">ログ サイズやログのローテーションを管理するプロバイダーを使用して、より永続的な場所にログを記録することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-150">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="2ee51-151">詳細については、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-151">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="2ee51-152">開発中、Blazor は通常、デバッグの助けになるよう、ブラウザーのコンソールに例外の完全な詳細情報を送信します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-152">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="2ee51-153">運用環境では、ブラウザー コンソールの詳細なエラーは既定で無効になっています。つまり、エラーはクライアントに送信されませんが、例外の完全な詳細はサーバー側でログに記録され続けます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-153">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="2ee51-154">詳細については、「<xref:fundamentals/error-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-154">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="2ee51-155">ログに記録するインシデントと、ログに記録されるインシデントの重大度レベルを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-155">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="2ee51-156">悪意のあるユーザーが、意図的にエラーをトリガーできる可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-156">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="2ee51-157">たとえば、製品の詳細を表示するコンポーネントの URL に不明な `ProductId` が指定されているエラーのインシデントは、ログに記録しないようにします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-157">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="2ee51-158">ログ記録では、すべてのエラーを重大度の高いインシデントとして扱わないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-158">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="2ee51-159">詳細については、「<xref:blazor/fundamentals/logging>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-159">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="2ee51-160">エラーが発生する可能性のある場所</span><span class="sxs-lookup"><span data-stu-id="2ee51-160">Places where errors may occur</span></span>

<span data-ttu-id="2ee51-161">ハンドルされない例外は、以下のいずれかの場所で、フレームワークとアプリ コードによってトリガーされることがあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-161">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="2ee51-162">コンポーネントのインスタンス化</span><span class="sxs-lookup"><span data-stu-id="2ee51-162">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="2ee51-163">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="2ee51-163">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="2ee51-164">レンダリング ロジック</span><span class="sxs-lookup"><span data-stu-id="2ee51-164">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="2ee51-165">イベント ハンドラー</span><span class="sxs-lookup"><span data-stu-id="2ee51-165">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="2ee51-166">コンポーネントの廃棄</span><span class="sxs-lookup"><span data-stu-id="2ee51-166">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="2ee51-167">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="2ee51-167">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="2ee51-168">Blazor Server の再レンダリング</span><span class="sxs-lookup"><span data-stu-id="2ee51-168">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="2ee51-169">上記のハンドルされない例外については、この記事の後続のセクションで説明しています。</span><span class="sxs-lookup"><span data-stu-id="2ee51-169">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="2ee51-170">コンポーネントのインスタンス化</span><span class="sxs-lookup"><span data-stu-id="2ee51-170">Component instantiation</span></span>

<span data-ttu-id="2ee51-171">Blazor によってコンポーネントのインスタンスが作成されるとき:</span><span class="sxs-lookup"><span data-stu-id="2ee51-171">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="2ee51-172">コンポーネントのコンストラクターが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-172">The component's constructor is invoked.</span></span>
* <span data-ttu-id="2ee51-173">[`@inject`](xref:mvc/views/razor#inject) ディレクティブ、または [`[Inject]` 属性](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)を介して、コンポーネントのコンストラクターに渡されるシングルトン以外の DI サービスのコンストラクターが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-173">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="2ee51-174">実行されたいずれかのコンストラクターまたは任意の `[Inject]` プロパティのセッターがハンドルされない例外をスローすると、Blazor Server の回線が失敗します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-174">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="2ee51-175">フレームワークではコンポーネントをインスタンス化できないため、この例外は致命的です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-175">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="2ee51-176">コンストラクターのロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して、例外をトラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-176">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="2ee51-177">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="2ee51-177">Lifecycle methods</span></span>

<span data-ttu-id="2ee51-178">コンポーネントのライフサイクルの間、Blazor によって以下の[ライフサイクル メソッド](xref:blazor/components/lifecycle)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-178">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="2ee51-179">いずれかのライフサイクル メソッドが同期的または非同期的に例外をスローした場合、例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-179">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="2ee51-180">コンポーネントでライフサイクル メソッドのエラーに対処するには、エラー処理ロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-180">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="2ee51-181"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> によって製品を取得するメソッドを呼び出す次の例では:</span><span class="sxs-lookup"><span data-stu-id="2ee51-181">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="2ee51-182">`ProductRepository.GetProductByIdAsync` メソッドでスローされた例外は [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-182">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="2ee51-183">`catch` ブロックの実行時には:</span><span class="sxs-lookup"><span data-stu-id="2ee51-183">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="2ee51-184">`loadFailed` が `true` に設定されます。これがユーザーにエラー メッセージを表示するために使われます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-184">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="2ee51-185">エラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-185">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

### <a name="rendering-logic"></a><span data-ttu-id="2ee51-186">レンダリング ロジック</span><span class="sxs-lookup"><span data-stu-id="2ee51-186">Rendering logic</span></span>

<span data-ttu-id="2ee51-187">`.razor` コンポーネント ファイル内の宣言マークアップは、<xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> と呼ばれる C# メソッドにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-187">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="2ee51-188">コンポーネントがレンダリングされるときには、<xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> が実行されて、レンダリングされたコンポーネントの要素、テキスト、および子コンポーネントを記述するデータ構造が構築されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-188">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="2ee51-189">レンダリング ロジックは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-189">Rendering logic can throw an exception.</span></span> <span data-ttu-id="2ee51-190">このシナリオの例は、`@someObject.PropertyName` が評価されても `@someObject` が `null` であるときに発生しています。</span><span class="sxs-lookup"><span data-stu-id="2ee51-190">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="2ee51-191">レンダリング ロジックによってスローされるハンドルされない例外は、Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-191">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="2ee51-192">レンダリング ロジックで null 参照例外が発生しないようにするには、そのメンバーにアクセスする前に `null` オブジェクトかどうかを調べます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-192">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="2ee51-193">次の例では、`person.Address` が `null` の場合は `person.Address` プロパティにアクセスしません。</span><span class="sxs-lookup"><span data-stu-id="2ee51-193">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="2ee51-194">上記のコードは、`person` が `null` でないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="2ee51-194">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="2ee51-195">多くの場合、コードの構造によって、コンポーネントがレンダリングされる時点でオブジェクトの存在が保証されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-195">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="2ee51-196">そのような場合は、レンダリング ロジックで `null` かどうかを調べる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2ee51-196">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="2ee51-197">前の例では、`person` が存在することを保証できるのは、コンポーネントがインスタンス化されるときに `person` が作成されるためです。</span><span class="sxs-lookup"><span data-stu-id="2ee51-197">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="2ee51-198">イベント ハンドラー</span><span class="sxs-lookup"><span data-stu-id="2ee51-198">Event handlers</span></span>

<span data-ttu-id="2ee51-199">クライアント側のコードでは、以下を使用して、イベント ハンドラーが作成されるときに C# コードの呼び出しをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-199">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="2ee51-200">その他の `@on...` 属性</span><span class="sxs-lookup"><span data-stu-id="2ee51-200">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="2ee51-201">これらのシナリオでは、イベント ハンドラー コードによって、ハンドルされない例外がスローされることがあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-201">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="2ee51-202">イベント ハンドラーがハンドルされない例外をスローした場合 (たとえば、データベース クエリが失敗した)、例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-202">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="2ee51-203">アプリが外部の理由で失敗する可能性のあるコードを呼び出す場合は、エラー処理とログ記録を含む [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して、例外をトラップします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-203">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="2ee51-204">ユーザー コードで例外のトラップと処理が行われない場合は、フレームワークによって例外がログに記録され、回線が終了されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-204">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="2ee51-205">コンポーネントの廃棄</span><span class="sxs-lookup"><span data-stu-id="2ee51-205">Component disposal</span></span>

<span data-ttu-id="2ee51-206">たとえばユーザーが別のページに移動したため、コンポーネントが UI から削除されることがあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-206">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="2ee51-207"><xref:System.IDisposable?displayProperty=fullName> を実装しているコンポーネントが UI から削除されると、フレームワークにより、コンポーネントの <xref:System.IDisposable.Dispose%2A> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-207">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="2ee51-208">コンポーネントの `Dispose` メソッドがハンドルされない例外をスローした場合、例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-208">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="2ee51-209">破棄のロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して、例外をトラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-209">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="2ee51-210">コンポーネントの破棄の詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-210">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="2ee51-211">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="2ee51-211">JavaScript interop</span></span>

<span data-ttu-id="2ee51-212"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> を使用すると、.NET コードによって、ユーザーのブラウザーで JavaScript ランタイムの非同期呼び出しを行えます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-212"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="2ee51-213"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> を使用するエラー処理には、以下の条件が適用されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-213">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="2ee51-214"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> の呼び出しが同期的に失敗した場合は、.NET 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-214">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="2ee51-215">たとえば、指定された引数をシリアル化できないため、<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> の呼び出しが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-215">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="2ee51-216">開発者コードで例外をキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-216">Developer code must catch the exception.</span></span> <span data-ttu-id="2ee51-217">イベント ハンドラーまたはコンポーネントのライフサイクル メソッドのアプリ コードで例外が処理されない場合、結果の例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-217">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="2ee51-218"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> の呼び出しが非同期に失敗した場合、.NET <xref:System.Threading.Tasks.Task> が失敗します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-218">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="2ee51-219">たとえば、JavaScript 側のコードが例外をスローしたり、`rejected` として完了した `Promise` を返したりするために、<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> の呼び出しが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-219">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="2ee51-220">開発者コードで例外をキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-220">Developer code must catch the exception.</span></span> <span data-ttu-id="2ee51-221">[`await`](/dotnet/csharp/language-reference/keywords/await) 演算子を使用する場合は、エラー処理とログ記録を含む [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントでメソッド呼び出しをラップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-221">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="2ee51-222">そうしないと、失敗したコードにより、Blazor Server 回線にとって致命的なハンドルされない例外が発生する結果となります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-222">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="2ee51-223">既定では、<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> の呼び出しは一定の期間内に完了する必要があります。そうでないと呼び出しがタイムアウトになります。既定のタイムアウト期間は 1 分です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-223">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="2ee51-224">タイムアウトにより、完了メッセージを送り返さないネットワーク接続や JavaScript コードでの損失からコードを保護します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-224">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="2ee51-225">呼び出しがタイムアウトになった場合、結果の <xref:System.Threading.Tasks> は <xref:System.OperationCanceledException> で失敗します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-225">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="2ee51-226">ログ記録を使用して例外をトラップし、処理します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-226">Trap and process the exception with logging.</span></span>

<span data-ttu-id="2ee51-227">同様に、JavaScript コードを使用して、[`[JSInvokable]` 属性](xref:blazor/call-dotnet-from-javascript)によって示される .NET メソッドの呼び出しを開始できます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-227">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="2ee51-228">ハンドルされない例外が、これらの .NET メソッドでスローされた場合:</span><span class="sxs-lookup"><span data-stu-id="2ee51-228">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="2ee51-229">例外は Blazor Server 回線にとって致命的なものとして扱われません。</span><span class="sxs-lookup"><span data-stu-id="2ee51-229">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="2ee51-230">JavaScript 側の `Promise` が拒否されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-230">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="2ee51-231">.NET 側か、メソッド呼び出しの JavaScript 側か、どちらでエラー処理コードを使用するかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-231">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="2ee51-232">詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-232">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a><span data-ttu-id="2ee51-233">Blazor Server のプリレンダリング</span><span class="sxs-lookup"><span data-stu-id="2ee51-233">Blazor Server prerendering</span></span>

<span data-ttu-id="2ee51-234">レンダリングされた HTML マークアップがユーザーの初期 HTTP 要求の一部として返されるように、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を使用して Blazor コンポーネントを事前レンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-234">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="2ee51-235">これは以下によって機能します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-235">This works by:</span></span>

* <span data-ttu-id="2ee51-236">同じページに含まれるすべての事前レンダリング コンポーネントに対する新しい回線を作成する。</span><span class="sxs-lookup"><span data-stu-id="2ee51-236">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="2ee51-237">初期 HTML を生成する。</span><span class="sxs-lookup"><span data-stu-id="2ee51-237">Generating the initial HTML.</span></span>
* <span data-ttu-id="2ee51-238">ユーザーのブラウザーが同じサーバーに戻る SignalR 接続を確立するまで、回線を `disconnected` として扱う。</span><span class="sxs-lookup"><span data-stu-id="2ee51-238">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="2ee51-239">接続が確立されると、回線でのインタラクティビティが再開され、コンポーネントの HTML マークアップが更新されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-239">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="2ee51-240">ライフサイクル メソッドやレンダリング ロジックの実行中など、事前レンダリングでいずれかのコンポーネントからハンドルされない例外がスローされた場合:</span><span class="sxs-lookup"><span data-stu-id="2ee51-240">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="2ee51-241">例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="2ee51-241">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="2ee51-242">その例外は、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> タグ ヘルパーの呼び出し履歴から破棄されます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-242">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="2ee51-243">そのため、開発者コードによって例外が明示的にキャッチされない限り、HTTP 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-243">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="2ee51-244">事前レンダリングが失敗する通常の状況では、コンポーネントのビルドとレンダリングを続行しても意味がありません。これは、動作中のコンポーネントはレンダリングできないためです。</span><span class="sxs-lookup"><span data-stu-id="2ee51-244">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="2ee51-245">事前レンダリング中に発生する可能性のあるエラーに耐えるには、例外をスローする可能性のあるコンポーネント内にエラー処理ロジックを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-245">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="2ee51-246">エラー処理とログ記録を含む [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-246">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="2ee51-247">[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメント内に <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> タグ ヘルパーをラップするのではなく、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> タグ ヘルパーによってレンダリングされるコンポーネント内にエラー処理ロジックを配置します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-247">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="2ee51-248">高度なシナリオ</span><span class="sxs-lookup"><span data-stu-id="2ee51-248">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="2ee51-249">再帰的レンダリング</span><span class="sxs-lookup"><span data-stu-id="2ee51-249">Recursive rendering</span></span>

<span data-ttu-id="2ee51-250">コンポーネントは、再帰的に入れ子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-250">Components can be nested recursively.</span></span> <span data-ttu-id="2ee51-251">これは、再帰的なデータ構造を表現する場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-251">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="2ee51-252">たとえば `TreeNode` コンポーネントで、ノードの子ごとにより多くの `TreeNode` コンポーネントをレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-252">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="2ee51-253">再帰的にレンダリングする場合は、無限の再帰となるようなコーディング パターンは回避します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-253">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="2ee51-254">循環が含まれるデータ構造は再帰的にレンダリングしないでください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-254">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="2ee51-255">たとえば、子にそれ自体が含まれるツリー ノードはレンダリングしないでください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-255">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="2ee51-256">循環を含むひと続きのレイアウトは作成しないでください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-256">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="2ee51-257">たとえば、レイアウトがそれ自体であるレイアウトは作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-257">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="2ee51-258">エンドユーザーが、悪意のあるデータ入力や JavaScript の相互運用呼び出しを通して、再帰による不変 (ルール) を犯さないようにします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-258">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="2ee51-259">レンダリング中の無限ループ:</span><span class="sxs-lookup"><span data-stu-id="2ee51-259">Infinite loops during rendering:</span></span>

* <span data-ttu-id="2ee51-260">レンダリング プロセスが永久に続行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-260">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="2ee51-261">これは終了しないループを作成するのと同じです。</span><span class="sxs-lookup"><span data-stu-id="2ee51-261">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="2ee51-262">これらのシナリオでは、影響を受けた Blazor Server 回線が失敗し、スレッドでは通常、以下のことが試みられます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-262">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="2ee51-263">オペレーティング システムで許されている限りの CPU 時間を無期限に消費します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-263">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="2ee51-264">サーバー メモリの量を無制限に消費します。</span><span class="sxs-lookup"><span data-stu-id="2ee51-264">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="2ee51-265">メモリを無制限に使用することは、すべての反復処理で、終了しないループによってコレクションにエントリが追加されるシナリオと同じです。</span><span class="sxs-lookup"><span data-stu-id="2ee51-265">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="2ee51-266">無限の再帰パターンを回避するには、再帰的なレンダリング コードに適切な停止条件が含まれるようにします。</span><span class="sxs-lookup"><span data-stu-id="2ee51-266">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="2ee51-267">カスタム レンダリング ツリーのロジック</span><span class="sxs-lookup"><span data-stu-id="2ee51-267">Custom render tree logic</span></span>

<span data-ttu-id="2ee51-268">ほとんどの Blazor コンポーネントは `.razor` ファイルとして実装され、出力をレンダリングするために <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 上で動作するロジックを生成するためにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-268">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="2ee51-269">開発者は、手続き型の C# コードを使用して <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> ロジックを手動で実装できます。</span><span class="sxs-lookup"><span data-stu-id="2ee51-269">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="2ee51-270">詳細については、「<xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-270">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="2ee51-271">手動のレンダー ツリー ビルダー ロジックの使用は、高度で安全ではないシナリオと考えられています。一般のコンポーネント開発には推奨されません。</span><span class="sxs-lookup"><span data-stu-id="2ee51-271">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="2ee51-272"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> コードが記述される場合は、開発者がコードの正確性を保証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-272">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="2ee51-273">たとえば、開発者は以下のことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-273">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="2ee51-274"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> と <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> の呼び出しが正しく調整されている。</span><span class="sxs-lookup"><span data-stu-id="2ee51-274">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="2ee51-275">正しい場所にのみ属性が追加される。</span><span class="sxs-lookup"><span data-stu-id="2ee51-275">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="2ee51-276">手動レンダー ツリー ビルダーのロジックが正しくないと、クラッシュ、サーバーのハング、セキュリティ脆弱性など、不特定の未定義の動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2ee51-276">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="2ee51-277">手動のレンダリング ツリー ビルダー ロジックは、アセンブリ コードや MSIL 命令を手動で記述するのと同じレベルの複雑さと、同じレベルの *危険性* を伴うことを考慮に入れてください。</span><span class="sxs-lookup"><span data-stu-id="2ee51-277">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>

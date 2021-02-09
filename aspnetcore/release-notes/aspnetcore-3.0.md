---
title: ASP.NET Core 3.0 の新機能
author: rick-anderson
description: ASP.NET Core 3.0 の新機能について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: 161d6aeefe1882b86ce97cdcfa7e5b23048693f0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217506"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="6aab5-103">ASP.NET Core 3.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="6aab5-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="6aab5-104">この記事では、ASP.NET Core 3.0 の最も大きな変更点について説明します。また、その変更点のドキュメントへのリンクも示します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

<span data-ttu-id="6aab5-105">Blazor は、.NET を使って対話型のクライアント側 Web UI を構築するための ASP.NET Core の新しいフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-105">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="6aab5-106">JavaScript の代わりに C# を使って、優れた対話型 UI を作成します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="6aab5-107">.NET で記述された、サーバー側とクライアント側のアプリのロジックを共有します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="6aab5-108">モバイル ブラウザーを含めた広範なブラウザーのサポートのために、HTML および CSS として UI をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="6aab5-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="6aab5-109">Blazor フレームワークでサポートされるシナリオ:</span><span class="sxs-lookup"><span data-stu-id="6aab5-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="6aab5-110">再利用可能な UI コンポーネント (Razor コンポーネント)</span><span class="sxs-lookup"><span data-stu-id="6aab5-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="6aab5-111">クライアント側のルーティング</span><span class="sxs-lookup"><span data-stu-id="6aab5-111">Client-side routing</span></span>
* <span data-ttu-id="6aab5-112">コンポーネントのレイアウト</span><span class="sxs-lookup"><span data-stu-id="6aab5-112">Component layouts</span></span>
* <span data-ttu-id="6aab5-113">依存関係の挿入のサポート</span><span class="sxs-lookup"><span data-stu-id="6aab5-113">Support for dependency injection</span></span>
* <span data-ttu-id="6aab5-114">フォームと検証</span><span class="sxs-lookup"><span data-stu-id="6aab5-114">Forms and validation</span></span>
* <span data-ttu-id="6aab5-115">Razor クラス ライブラリを使用したコンポーネント ライブラリの構築</span><span class="sxs-lookup"><span data-stu-id="6aab5-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="6aab5-116">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="6aab5-116">JavaScript interop</span></span>

<span data-ttu-id="6aab5-117">詳細については、「<xref:blazor/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-117">For more information, see <xref:blazor/index>.</span></span>

### Blazor Server

<span data-ttu-id="6aab5-118">Blazor では、UI の更新プログラムを適用する方法からコンポーネントのレンダリング ロジックが分離されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-118">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="6aab5-119">Blazor Server では、ASP.NET Core アプリでサーバー上の Razor コンポーネントをホストするためのサポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-119">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="6aab5-120">UI の更新は SignalR 接続を介して処理されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-120">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="6aab5-121">Blazor Server は ASP.NET Core 3.0 でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-121">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a><span data-ttu-id="6aab5-122">Blazor WebAssembly (プレビュー)</span><span class="sxs-lookup"><span data-stu-id="6aab5-122">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="6aab5-123">Blazor アプリは、WebAssembly ベースの .NET ランタイムを使用してブラウザーで直接実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-123">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="6aab5-124">Blazor WebAssembly はプレビュー段階であり、ASP.NET Core 3.0 ではサポートされて "*いません*"。</span><span class="sxs-lookup"><span data-stu-id="6aab5-124">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="6aab5-125">Blazor WebAssembly は、ASP.NET Core の今後のリリースでサポートされる予定です。</span><span class="sxs-lookup"><span data-stu-id="6aab5-125">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="6aab5-126">Razor のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="6aab5-126">Razor components</span></span>

<span data-ttu-id="6aab5-127">Blazor アプリはコンポーネントから構築されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-127">Blazor apps are built from components.</span></span> <span data-ttu-id="6aab5-128">コンポーネントは、ページ、ダイアログ、フォームなどのユーザー インターフェイス (UI) の自己完結型チャンクです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-128">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="6aab5-129">コンポーネントは、UI レンダリング ロジックとクライアント側のイベント ハンドラーを定義する通常の .NET クラスです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-129">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="6aab5-130">JavaScript を使用せずに、機能豊富な対話型 Web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-130">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="6aab5-131">通常、Blazor のコンポーネントは、HTML と C# が自然に融合している Razor 構文を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-131">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="6aab5-132">Razor コンポーネントは、両方とも Razor を使用するという点で Razor Pages および MVC ビューに似ています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-132">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="6aab5-133">要求 - 応答モデルに基づくページやビューとは異なり、コンポーネントは特に UI コンポジションを処理するために使われます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-133">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="6aab5-134">gRPC</span><span class="sxs-lookup"><span data-stu-id="6aab5-134">gRPC</span></span>

<span data-ttu-id="6aab5-135">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="6aab5-135">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="6aab5-136">広く普及している高パフォーマンス RPC (リモート プロシージャ コール) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-136">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="6aab5-137">API 開発に対して独特のコントラクト優先のアプローチを提供します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-137">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="6aab5-138">次のような最新テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-138">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="6aab5-139">HTTP/2 (転送用)。</span><span class="sxs-lookup"><span data-stu-id="6aab5-139">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="6aab5-140">プロトコル バッファー (インターフェイス記述言語として)。</span><span class="sxs-lookup"><span data-stu-id="6aab5-140">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="6aab5-141">バイナリ シリアル化形式。</span><span class="sxs-lookup"><span data-stu-id="6aab5-141">Binary serialization format.</span></span>
* <span data-ttu-id="6aab5-142">次のような機能があります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-142">Provides features such as:</span></span>

  * <span data-ttu-id="6aab5-143">認証</span><span class="sxs-lookup"><span data-stu-id="6aab5-143">Authentication</span></span>
  * <span data-ttu-id="6aab5-144">双方向のストリーミングおよびフロー制御。</span><span class="sxs-lookup"><span data-stu-id="6aab5-144">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="6aab5-145">キャンセルおよびタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="6aab5-145">Cancellation and timeouts.</span></span>

<span data-ttu-id="6aab5-146">ASP.NET Core 3.0 の gRPC 機能には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-146">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="6aab5-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore):gRPC サービスをホストするための ASP.NET Core フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="6aab5-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="6aab5-148">ASP.NET Core 上の gRPC は、ログ記録、依存関係の挿入 (DI)、認証、承認など、標準の ASP.NET Core 機能と完全に統合されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-148">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="6aab5-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client):使い慣れた `HttpClient` に基づいて構築される .NET Core 用の gRPC クライアント。</span><span class="sxs-lookup"><span data-stu-id="6aab5-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="6aab5-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): `HttpClientFactory` と gRPC クライアントの統合。</span><span class="sxs-lookup"><span data-stu-id="6aab5-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="6aab5-151">詳細については、「<xref:grpc/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-151">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="6aab5-152">移行の手順については、[SignalR コードの更新](xref:migration/22-to-30#signalr)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-152">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="6aab5-153">現在、SignalR は `System.Text.Json` を使用して JSON メッセージのシリアル化および逆シリアル化を行います。</span><span class="sxs-lookup"><span data-stu-id="6aab5-153">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="6aab5-154">`Newtonsoft.Json` ベースのシリアライザーを復元する手順については、「[Newtonsoft.Json に切り替える](xref:migration/22-to-30#switch-to-newtonsoftjson)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-154">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="6aab5-155">SignalR 対応の JavaScript および .NET クライアントには、自動再接続のサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-155">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="6aab5-156">既定では、クライアントはすぐに再接続を試行し、必要に応じて 2 秒後、10 秒後、30 秒後に再試行します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-156">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="6aab5-157">クライアントが正常に再接続すると、新しい接続 ID を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-157">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="6aab5-158">自動再接続はオプトインです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-158">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="6aab5-159">再接続の間隔は、ミリ秒単位の間隔を配列で渡すことによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-159">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="6aab5-160">カスタム実装を渡すと、再接続間隔を完全に制御することができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-160">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="6aab5-161">最後の再接続間隔の後で再接続に失敗した場合:</span><span class="sxs-lookup"><span data-stu-id="6aab5-161">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="6aab5-162">クライアントは接続がオフラインであると見なします。</span><span class="sxs-lookup"><span data-stu-id="6aab5-162">The client considers the connection is offline.</span></span>
* <span data-ttu-id="6aab5-163">クライアントは再接続の試行を停止します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-163">The client stops trying to reconnect.</span></span>

<span data-ttu-id="6aab5-164">再接続の試行中に、再接続が試行されていることをユーザーに通知するようにアプリ UI を更新します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-164">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="6aab5-165">接続が中断されたときに UI にフィードバックを提供するため、次のイベント ハンドラーを含むように SignalR クライアント API が拡張されました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-165">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="6aab5-166">`onreconnecting`:これによって、開発者が UI を無効にしたり、アプリがオフラインであることをユーザーに知らせたりすることができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-166">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="6aab5-167">`onreconnected`:これによって、接続が再確立したときに開発者が UI を更新できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-167">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="6aab5-168">次のコードでは `onreconnecting` を使用して、接続の試行中に UI を更新します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-168">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="6aab5-169">次のコードでは `onreconnected` を使用して、接続時に UI を更新します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-169">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="6aab5-170">SignalR 3.0 以降では、ハブ メソッドが承認を必要とする場合に、承認ハンドラーにカスタム リソースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-170">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="6aab5-171">リソースは `HubInvocationContext` のインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-171">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="6aab5-172">`HubInvocationContext` には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-172">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="6aab5-173">呼び出されているハブ メソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="6aab5-173">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="6aab5-174">ハブ メソッドに対する引数。</span><span class="sxs-lookup"><span data-stu-id="6aab5-174">Arguments to the hub method.</span></span>

<span data-ttu-id="6aab5-175">複数の組織が Azure Active Directory を使用してサインインできるチャット ルーム アプリの例について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-175">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="6aab5-176">Microsoft アカウントを持つユーザーは誰でもサインインしてチャットできますが、ユーザーを利用禁止にしたりユーザーのチャット履歴を表示したりできるのは所有している組織のメンバーのみです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-176">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="6aab5-177">アプリを使用すると、特定のユーザーに対して特定の機能を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-177">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="6aab5-178">上記のコードでは、`DomainRestrictedRequirement` がカスタムの `IAuthorizationRequirement` として機能します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-178">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="6aab5-179">`HubInvocationContext` リソース パラメーターが渡されているため、内部ロジックで以下を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-179">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="6aab5-180">ハブが呼び出されているコンテキストを調べます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-180">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="6aab5-181">個々のハブ メソッドの実行をユーザーに許可するかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-181">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="6aab5-182">個々のハブ メソッドは、コードが実行時にチェックするポリシーの名前でマークできます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-182">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="6aab5-183">クライアントが個々のハブ メソッドを呼び出そうとすると、`DomainRestrictedRequirement` ハンドラーが実行され、メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-183">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="6aab5-184">この方法に基づいて、`DomainRestrictedRequirement` が次のようにアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-184">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="6aab5-185">ログインしているすべてのユーザーが `SendMessage` メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-185">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="6aab5-186">`@jabbr.net` 電子メール アドレスを使用してログインしたユーザーのみが、ユーザーの履歴を表示できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-186">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="6aab5-187">`bob42@jabbr.net` のみが、ユーザーをチャット ルーム利用禁止にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-187">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="6aab5-188">`DomainRestricted` ポリシーの作成には以下が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-188">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="6aab5-189">*Startup.cs* に新しいポリシーを追加します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-189">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="6aab5-190">カスタムの `DomainRestrictedRequirement` 要件をパラメーターとして指定します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-190">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="6aab5-191">承認ミドルウェアを使用して `DomainRestricted` を登録します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-191">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="6aab5-192">SignalR ハブでは[エンドポイント ルーティング](xref:fundamentals/routing)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-192">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="6aab5-193">SignalR ハブ接続は以前は明示的に実行されました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-193">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="6aab5-194">以前のバージョンでは、開発者はさまざまな場所でコントローラー、Razor ページ、ハブを関連付ける必要がありました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-194">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="6aab5-195">明示的な接続によって、ほぼ同一のルーティング セグメントがいくつも生成されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-195">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="6aab5-196">SignalR 3.0 ハブは、エンドポイント ルーティングを介してルーティングできます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-196">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="6aab5-197">エンドポイント ルーティングでは、通常、すべてのルーティングを `UseRouting` に構成できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-197">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="6aab5-198">ASP.NET Core 3.0 SignalR で追加されたもの:</span><span class="sxs-lookup"><span data-stu-id="6aab5-198">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="6aab5-199">クライアントとサーバー間のストリーミング。</span><span class="sxs-lookup"><span data-stu-id="6aab5-199">Client-to-server streaming.</span></span> <span data-ttu-id="6aab5-200">クライアントとサーバー間のストリーミングでは、サーバー側メソッドが `IAsyncEnumerable<T>` または `ChannelReader<T>` のインスタンスを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-200">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="6aab5-201">次 C# の例では、ハブの `UploadStream` メソッドがクライアントから文字列のストリームを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-201">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="6aab5-202">.NET クライアント アプリは、`IAsyncEnumerable<T>` または `ChannelReader<T>` インスタンスを、上記の `UploadStream` Hub メソッドの `stream` 引数として渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-202">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="6aab5-203">`for` ループが完了し、ローカル関数が終了した後で、ストリームの完了が送信されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-203">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="6aab5-204">JavaScript クライアント アプリは、上記の `UploadStream` ハブ メソッドの `stream` 引数のために SignalR `Subject` (または [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-204">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="6aab5-205">JavaScript コードで `subject.next` メソッドを使用すると、文字列がキャプチャされてからサーバーへの送信準備が整うように文字列を処理できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-205">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="6aab5-206">直前の 2 つのスニペットのようなコードを使用して、リアルタイム ストリーミング エクスペリエンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-206">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="6aab5-207">新しい JSON シリアル化</span><span class="sxs-lookup"><span data-stu-id="6aab5-207">New JSON serialization</span></span>

<span data-ttu-id="6aab5-208">現在、ASP.NET Core 3.0 では、JSON シリアル化のために既定で <xref:System.Text.Json> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-208">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="6aab5-209">非同期で JSON の読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="6aab5-209">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="6aab5-210">UTF-8 テキスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-210">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="6aab5-211">通常、`Newtonsoft.Json` よりパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-211">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="6aab5-212">Json.NET を ASP.NET Core 3.0 に追加するには、「[Newtonsoft.Json ベースの JSON 形式のサポートを追加する](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-212">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="6aab5-213">新しい Razor ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="6aab5-213">New Razor directives</span></span>

<span data-ttu-id="6aab5-214">次の一覧には、新しい Razor ディレクティブが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-214">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="6aab5-215">[`@attribute`](xref:mvc/views/razor#attribute):`@attribute` ディレクティブでは、指定された属性が生成されたページまたはビューのクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-215">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="6aab5-216">たとえば、`@attribute [Authorize]` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6aab5-216">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="6aab5-217">[`@implements`](xref:mvc/views/razor#implements):`@implements` ディレクティブでは、生成されたクラスのインターフェイスが実装されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-217">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="6aab5-218">たとえば、`@implements IDisposable` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6aab5-218">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="6aab5-219">IdentityServer4 では、Web API と SPA の認証と承認がサポートされています</span><span class="sxs-lookup"><span data-stu-id="6aab5-219">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="6aab5-220">ASP.NET Core 3.0 では、Web API 認証のサポートの使用により、シングルページ アプリ (SPA) での認証が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-220">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="6aab5-221">ユーザーを認証および格納するための ASP.NET Core Identity が [IdentityServer4](https://identityserver.io/) と組み合わされ、OpenID Connect が実装されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-221">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="6aab5-222">IdentityServer4 は、ASP.NET Core 3.0 用の OpenID Connect および OAuth 2.0 フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-222">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="6aab5-223">これにより、次のセキュリティ機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-223">It enables the following security features:</span></span>

* <span data-ttu-id="6aab5-224">サービスとしての認証 (AaaS)</span><span class="sxs-lookup"><span data-stu-id="6aab5-224">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="6aab5-225">複数のアプリケーションの種類でのシングル サインオン/オフ (SSO)</span><span class="sxs-lookup"><span data-stu-id="6aab5-225">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="6aab5-226">API のアクセス制御</span><span class="sxs-lookup"><span data-stu-id="6aab5-226">Access control for APIs</span></span>
* <span data-ttu-id="6aab5-227">Federation Gateway</span><span class="sxs-lookup"><span data-stu-id="6aab5-227">Federation Gateway</span></span>

<span data-ttu-id="6aab5-228">詳細については、[IdentityServer4 のドキュメント](http://docs.identityserver.io/en/latest/index.html)または「[SPAs の認証と承認](xref:security/authentication/identity/spa)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-228">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="6aab5-229">証明書および Kerberos 認証</span><span class="sxs-lookup"><span data-stu-id="6aab5-229">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="6aab5-230">証明書認証には以下が必要です。</span><span class="sxs-lookup"><span data-stu-id="6aab5-230">Certificate authentication requires:</span></span>

* <span data-ttu-id="6aab5-231">証明書を受け入れるようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-231">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="6aab5-232">`Startup.Configure` に認証ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-232">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="6aab5-233">`Startup.ConfigureServices` に証明書認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-233">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="6aab5-234">証明書認証のオプションには次の機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-234">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="6aab5-235">自己署名された証明書を受け入れる。</span><span class="sxs-lookup"><span data-stu-id="6aab5-235">Accept self-signed certificates.</span></span>
* <span data-ttu-id="6aab5-236">証明書の失効を確認する。</span><span class="sxs-lookup"><span data-stu-id="6aab5-236">Check for certificate revocation.</span></span>
* <span data-ttu-id="6aab5-237">提供された証明書に適切な使用フラグが含まれていることを確認する。</span><span class="sxs-lookup"><span data-stu-id="6aab5-237">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="6aab5-238">既定のユーザー プリンシパルは、証明書のプロパティで構成されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-238">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="6aab5-239">ユーザー プリンシパルには、プリンシパルの補完または置換を可能にするイベントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-239">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="6aab5-240">詳細については、「<xref:security/authentication/certauth>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-240">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="6aab5-241">[Windows 認証](/windows-server/security/windows-authentication/windows-authentication-overview)は、Linux および macOS に拡張されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-241">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="6aab5-242">以前のバージョンでは、Windows 認証は [IIS](xref:host-and-deploy/iis/index) と [HTTP.sys](xref:fundamentals/servers/httpsys) に限定されていました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-242">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="6aab5-243">ASP.NET Core 3.0 では、[Kestrel](xref:fundamentals/servers/kestrel) は、Windows、Linux、および macOS 上で、Windows ドメインに参加しているホストに対して Negotiate、[Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)、および [NTLM](/windows-server/security/kerberos/ntlm-overview) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-243">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="6aab5-244">これらの認証スキームの Kestrel サポートは、[Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-244">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="6aab5-245">他の認証サービスと同様に、認証アプリ全体を構成してからサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-245">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="6aab5-246">ホストの要件:</span><span class="sxs-lookup"><span data-stu-id="6aab5-246">Host requirements:</span></span>

* <span data-ttu-id="6aab5-247">Windows ホストでは、アプリをホストするユーザー アカウントに[サービス プリンシパル名](/windows/win32/ad/service-principal-names) (SPN) を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-247">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="6aab5-248">Linux および macOS マシンは、ドメインに参加している必要があります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-248">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="6aab5-249">Web プロセス用に SPN を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-249">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="6aab5-250">[キータブ ファイル](/archive/blogs/pie/all-you-need-to-know-about-keytab-files)をホスト マシン上で生成して構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6aab5-250">[Keytab files](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="6aab5-251">詳細については、「<xref:security/authentication/windowsauth>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-251">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="6aab5-252">テンプレートの変更</span><span class="sxs-lookup"><span data-stu-id="6aab5-252">Template changes</span></span>

<span data-ttu-id="6aab5-253">Web UI テンプレート (Razor Pages、コントローラーとビューを含む MVC) では、以下が削除されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-253">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="6aab5-254">cookie 同意 UI は含まれなくなりました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-254">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="6aab5-255">ASP.NET Core 3.0 テンプレートで生成されるアプリで cookie 同意機能を有効にするには、「<xref:security/gdpr>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-255">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="6aab5-256">スクリプトと関連する静的アセットは、CDN を使用する代わりに、ローカル ファイルとして参照されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-256">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="6aab5-257">詳細については、「[現在、スクリプトと関連する静的アセットは、現在の環境に基づいた CDN を使用する代わりに、ローカル ファイルとして参照される (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-257">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="6aab5-258">Angular テンプレートは、Angular 8 を使用するように更新されました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-258">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="6aab5-259">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="6aab5-259">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="6aab5-260">Visual Studio の新しいテンプレート オプションによって、ページとビューのテンプレート サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-260">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="6aab5-261">コマンド シェルでテンプレートから RCL を作成するときは、`--support-pages-and-views` オプション (`dotnet new razorclasslib --support-pages-and-views`) を渡します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-261">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="6aab5-262">汎用ホスト</span><span class="sxs-lookup"><span data-stu-id="6aab5-262">Generic Host</span></span>

<span data-ttu-id="6aab5-263">ASP.NET Core 3.0 テンプレートでは <xref:fundamentals/host/generic-host> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-263">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="6aab5-264">以前のバージョンでは <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-264">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="6aab5-265">.NET Core 汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) を使用すると、ASP.NET Core アプリと、Web 固有ではない他のサーバー シナリオの統合が強化されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-265">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="6aab5-266">詳細については、「[HostBuilder による WebHostBuilder の置き換え](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-266">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="6aab5-267">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="6aab5-267">Host configuration</span></span>

<span data-ttu-id="6aab5-268">ASP.NET Core 3.0 リリースよりも前には、`ASPNETCORE_` というプレフィックスが付いた環境変数が Web ホストのホスト構成用に読み込まれました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-268">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="6aab5-269">3\.0 では、`AddEnvironmentVariables` が使用され、`DOTNET_` というプレフィックスが付いた環境変数が `CreateDefaultBuilder` でのホスト構成用に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-269">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="6aab5-270">Startup コンストラクター挿入の変更</span><span class="sxs-lookup"><span data-stu-id="6aab5-270">Changes to Startup constructor injection</span></span>

<span data-ttu-id="6aab5-271">汎用ホストでは、`Startup` コンストラクター挿入で次の型しかサポートされません。</span><span class="sxs-lookup"><span data-stu-id="6aab5-271">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="6aab5-272">すべてのサービスを `Startup.Configure` メソッドへの引数として直接挿入することは引き続きできます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-272">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="6aab5-273">詳細については、「[汎用ホストによる Startup コンストラクター挿入の制限 (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-273">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="6aab5-274">Kestrel</span><span class="sxs-lookup"><span data-stu-id="6aab5-274">Kestrel</span></span>

* <span data-ttu-id="6aab5-275">Kestrel 構成は、汎用ホストへの移行に対応するように更新されました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-275">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="6aab5-276">3\.0 では、Kestrel は `ConfigureWebHostDefaults` によって提供される Web ホスト ビルダー上で構成されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-276">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="6aab5-277">接続アダプターは Kestrel から削除され、接続ミドルウェアで置き換えられました。これは ASP.NET Core パイプラインの HTTP ミドルウェアに似ていますが下位レベルの接続用です。</span><span class="sxs-lookup"><span data-stu-id="6aab5-277">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="6aab5-278">Kestrel トランスポート層は、`Connections.Abstractions` のパブリック インターフェイスとして公開されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-278">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="6aab5-279">ヘッダーとトレーラーのあいまいさは、末尾のヘッダーを新しいコレクションに移動することによって解決されました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-279">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="6aab5-280">同期 I/O の API (`HttpRequest.Body.Read` など) は、アプリのクラッシュにつながるスレッドの不足の一般的な原因です。</span><span class="sxs-lookup"><span data-stu-id="6aab5-280">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="6aab5-281">3\.0 では、`AllowSynchronousIO` は既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-281">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="6aab5-282">詳細については、「<xref:migration/22-to-30#kestrel>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-282">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="6aab5-283">既定で有効な HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6aab5-283">HTTP/2 enabled by default</span></span>

<span data-ttu-id="6aab5-284">HTTP/2 は、Kestrel では HTTPS エンドポイントに対して既定で有効です。</span><span class="sxs-lookup"><span data-stu-id="6aab5-284">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="6aab5-285">IIS または HTTP.sys での HTTP/2 サポートは、オペレーティング システムでサポートされる場合に有効です。</span><span class="sxs-lookup"><span data-stu-id="6aab5-285">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="6aab5-286">要求時の EventCounter</span><span class="sxs-lookup"><span data-stu-id="6aab5-286">EventCounters on request</span></span>

<span data-ttu-id="6aab5-287">ホスティング EventSource `Microsoft.AspNetCore.Hosting` は、受信要求に関連する次の新しい種類の <xref:System.Diagnostics.Tracing.EventCounter> を出力します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-287">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="6aab5-288">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="6aab5-288">Endpoint routing</span></span>

<span data-ttu-id="6aab5-289">エンドポイント ルーティングによってフレームワーク (MVC など) がミドルウェアとうまく連携できるようになりますが、これが次のように強化されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-289">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="6aab5-290">ミドルウェアとエンドポイントの順序を `Startup.Configure` の要求処理パイプラインで構成できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-290">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="6aab5-291">エンドポイントとミドルウェアは、正常性チェックなどの他の ASP.NET Core ベースのテクノロジに対して適切に構成できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-291">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="6aab5-292">エンドポイントは、ミドルウェアと MVC の両方に、CORS や承認などのポリシーを実装できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-292">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="6aab5-293">フィルターおよび属性をコントローラーのメソッドに設定できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-293">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="6aab5-294">詳細については、「<xref:fundamentals/routing#routing-basics>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-294">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="6aab5-295">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="6aab5-295">Health Checks</span></span>

<span data-ttu-id="6aab5-296">正常性チェックでは、汎用ホストとのエンドポイント ルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-296">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="6aab5-297">`Startup.Configure` で、エンドポイントの URL または相対パスを使用して、エンドポイント ビルダーで `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-297">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="6aab5-298">正常性チェックのエンドポイントは以下を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-298">Health Checks endpoints can:</span></span>

* <span data-ttu-id="6aab5-299">1 つまたは複数の許可されたホスト/ポートを指定する。</span><span class="sxs-lookup"><span data-stu-id="6aab5-299">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="6aab5-300">承認を必要とする。</span><span class="sxs-lookup"><span data-stu-id="6aab5-300">Require authorization.</span></span>
* <span data-ttu-id="6aab5-301">CORS を必要とする。</span><span class="sxs-lookup"><span data-stu-id="6aab5-301">Require CORS.</span></span>

<span data-ttu-id="6aab5-302">詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-302">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="6aab5-303">HttpContext のパイプ</span><span class="sxs-lookup"><span data-stu-id="6aab5-303">Pipes on HttpContext</span></span>

<span data-ttu-id="6aab5-304">現在、<xref:System.IO.Pipelines> API を使用して、要求本文の読み取りと応答本文の書き込みを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-304">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="6aab5-305">次に、</span><span class="sxs-lookup"><span data-stu-id="6aab5-305">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="6aab5-306">`HttpRequest.BodyReader` プロパティは、要求本文を読み取るために使用できる <xref:System.IO.Pipelines.PipeReader> を提供します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-306">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="6aab5-307">次に、</span><span class="sxs-lookup"><span data-stu-id="6aab5-307">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="6aab5-308">`HttpResponse.BodyWriter` プロパティは、応答本文を書き込むために使用できる <xref:System.IO.Pipelines.PipeWriter> を提供します。</span><span class="sxs-lookup"><span data-stu-id="6aab5-308">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="6aab5-309">`HttpRequest.BodyReader` は、`HttpRequest.Body` ストリームに類似しています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-309">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="6aab5-310">`HttpResponse.BodyWriter` は `HttpResponse.Body` ストリームに類似しています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-310">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="6aab5-311">IIS でのエラー報告の向上</span><span class="sxs-lookup"><span data-stu-id="6aab5-311">Improved error reporting in IIS</span></span>

<span data-ttu-id="6aab5-312">IIS で ASP.NET Core アプリをホストする際の起動エラーで生成される診断データが豊富になりました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-312">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="6aab5-313">これらのエラーは、該当する場合は常にスタック トレースと共に Windows イベント ログに報告されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-313">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="6aab5-314">さらに、すべての警告、エラー、および未処理の例外が、Windows イベント ログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-314">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="6aab5-315">ワーカー サービスとワーカー SDK</span><span class="sxs-lookup"><span data-stu-id="6aab5-315">Worker Service and Worker SDK</span></span>

<span data-ttu-id="6aab5-316">.NET Core 3.0 では、新しいワーカー サービス アプリ テンプレートが導入されました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-316">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="6aab5-317">このテンプレートは、.NET Core で長期サービスを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-317">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="6aab5-318">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="6aab5-318">For more information, see:</span></span>

* [<span data-ttu-id="6aab5-319">Windows サービスとしての .NET Core ワーカー</span><span class="sxs-lookup"><span data-stu-id="6aab5-319">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="6aab5-320">Forwarded Headers Middleware の機能強化</span><span class="sxs-lookup"><span data-stu-id="6aab5-320">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="6aab5-321">ASP.NET Core の以前のバージョンでは、Azure Linux にデプロイした場合、または IIS 以外のリバース プロキシの背後にデプロイした場合に、<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> と <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> の呼び出しで問題が発生していました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-321">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="6aab5-322">以前のバージョンの修正方法は、「[Linux および非 IIS のリバース プロキシのスキームを転送する](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)」に記載されてます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-322">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="6aab5-323">このシナリオは ASP.NET Core 3.0 では修正されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-323">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="6aab5-324">ホストによって [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) が有効化されるのは、`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されているときです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-324">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="6aab5-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` はコンテナー イメージで `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="6aab5-326">パフォーマンスの向上</span><span class="sxs-lookup"><span data-stu-id="6aab5-326">Performance improvements</span></span>

<span data-ttu-id="6aab5-327">ASP.NET Core 3.0 には、メモリ使用量を減らしてスループットを向上させる多くの機能強化が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-327">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="6aab5-328">スコープ サービスで組み込み依存関係挿入コンテナーを使用する場合のメモリ使用量の削減。</span><span class="sxs-lookup"><span data-stu-id="6aab5-328">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="6aab5-329">ミドルウェアのシナリオやルーティングを含む、フレームワーク全体での割り当ての削減。</span><span class="sxs-lookup"><span data-stu-id="6aab5-329">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="6aab5-330">WebSocket 接続のメモリ使用量の削減。</span><span class="sxs-lookup"><span data-stu-id="6aab5-330">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="6aab5-331">HTTPS 接続でのメモリの削減とスループットの向上。</span><span class="sxs-lookup"><span data-stu-id="6aab5-331">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="6aab5-332">最適化された完全非同期の新 JSON シリアライザー。</span><span class="sxs-lookup"><span data-stu-id="6aab5-332">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="6aab5-333">フォーム解析でのメモリ使用量の削減とスループットの向上。</span><span class="sxs-lookup"><span data-stu-id="6aab5-333">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="6aab5-334">.NET Core 3.0 のみで実行する ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="6aab5-334">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="6aab5-335">ASP.NET Core 3.0 以降、.NET Framework はサポートされるターゲット フレームワークではなくなりました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-335">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="6aab5-336">.NET Framework をターゲットとするプロジェクトは、[.NET Core 2.1 LTS リリース](https://dotnet.microsoft.com/download/dotnet-core/2.1)を使用して完全にサポートされた状態で続行できます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-336">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="6aab5-337">ほとんどの ASP.NET Core 2.1.x 関連パッケージは、.NET Core 2.1 の 3 年の LTS 期間が終わっても無期限にサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-337">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="6aab5-338">移行の詳細については、「[.NET Framework から .NET Core にコードを移植する](/dotnet/core/porting/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-338">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="6aab5-339">ASP.NET Core 共有フレームワークの使用</span><span class="sxs-lookup"><span data-stu-id="6aab5-339">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="6aab5-340">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれる ASP.NET Core 3.0 共有フレームワークでは、プロジェクト ファイル内の明示的な `<PackageReference />` 要素を必要としなくなりました。</span><span class="sxs-lookup"><span data-stu-id="6aab5-340">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="6aab5-341">プロジェクト ファイル内で `Microsoft.NET.Sdk.Web` SDK を使用すると、共有フレームワークが自動的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="6aab5-341">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="6aab5-342">ASP.NET Core 共有フレームワークから削除されたアセンブリ</span><span class="sxs-lookup"><span data-stu-id="6aab5-342">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="6aab5-343">ASP.NET Core 3.0 共有フレームワークから削除された重要なアセンブリは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6aab5-343">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="6aab5-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET)。</span><span class="sxs-lookup"><span data-stu-id="6aab5-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="6aab5-345">Json.NET を ASP.NET Core 3.0 に追加するには、「[Newtonsoft.Json ベースの JSON 形式のサポートを追加する](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-345">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="6aab5-346">ASP.NET Core 3.0 では JSON の読み取りと書き込みのために `System.Text.Json` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="6aab5-346">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="6aab5-347">詳細については、このドキュメントの「[新しい JSON シリアル化](#new-json-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-347">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="6aab5-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6aab5-348">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="6aab5-349">共有フレームワークから削除されたすべてのアセンブリの一覧については、「[Microsoft.AspNetCore.App 3.0 から削除されるアセンブリ](https://github.com/dotnet/AspNetCore/issues/3755)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-349">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="6aab5-350">この変更の動機については、「[Microsoft.AspNetCore.App 3.0 に対する重大な変更](https://github.com/aspnet/Announcements/issues/325)」および「[ASP.NET Core 3.0 の変更点を初めて見て](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6aab5-350">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->

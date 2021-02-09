---
title: ASP.NET Core で IHttpClientFactory を使用して HTTP 要求を行う
author: stevejgordon
description: IHttpClientFactory インターフェイスを使用して、ASP.NET Core の論理 HttpClient インスタンスを管理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057331"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="dc7b2-103">ASP.NET Core で IHttpClientFactory を使用して HTTP 要求を行う</span><span class="sxs-lookup"><span data-stu-id="dc7b2-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc7b2-104">作成者: [Kirk Larkin](https://github.com/serpent5)、[Steve Gordon](https://github.com/stevejgordon)、[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="dc7b2-105">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="dc7b2-106">`IHttpClientFactory` には次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="dc7b2-107">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-108">たとえば、[GitHub](https://github.com/) にアクセスするために、*github* という名前のクライアントを登録して構成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="dc7b2-109">一般的なアクセスのために、既定のクライアントを登録できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="dc7b2-110">`HttpClient` でのハンドラーのデリゲートにより、送信ミドルウェアの概念が体系化されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="dc7b2-111">`HttpClient` でのハンドラーのデリゲートを利用するために、Polly ベースのミドルウェアに対する拡張機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="dc7b2-112">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="dc7b2-113">自動管理により、`HttpClient` の有効期間を手動で管理するときの一般的な DNS (ドメイン ネーム システム) の問題が発生しなくなります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="dc7b2-114">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="dc7b2-115">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="dc7b2-116">このトピックのバージョンのサンプル コードでは、HTTP 応答で返された JSON コンテンツを、<xref:System.Text.Json> を使用して逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="dc7b2-117">`Json.NET` および `ReadAsAsync<T>` を使用するサンプルについては、バージョン セレクターを使用して、このトピックの 2.x バージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="dc7b2-118">利用パターン</span><span class="sxs-lookup"><span data-stu-id="dc7b2-118">Consumption patterns</span></span>

<span data-ttu-id="dc7b2-119">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="dc7b2-120">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="dc7b2-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="dc7b2-121">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="dc7b2-122">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="dc7b2-123">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="dc7b2-124">どの方法が最善かは、アプリの要件によって異なります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="dc7b2-125">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="dc7b2-125">Basic usage</span></span>

<span data-ttu-id="dc7b2-126">`IHttpClientFactory` は、`AddHttpClient` を呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="dc7b2-127">`IHttpClientFactory` は、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用して要求できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dc7b2-128">次のコードでは、`IHttpClientFactory` を使用して `HttpClient` インスタンスを作成しています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="dc7b2-129">前の例のように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングするのに適した方法です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="dc7b2-130">`HttpClient` の使用方法に対する影響はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="dc7b2-131">既存のアプリで `HttpClient` のインスタンスが作成されている場所を、<xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="dc7b2-132">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-132">Named clients</span></span>

<span data-ttu-id="dc7b2-133">名前付きクライアントは、次の場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="dc7b2-134">アプリで、多くの異なる `HttpClient` を使用する必要がある。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="dc7b2-135">多くの `HttpClient` の構成が異なる。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="dc7b2-136">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="dc7b2-137">上記のコードでは、クライアントに次のものが構成されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="dc7b2-138">ベース アドレス `https://api.github.com/`。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="dc7b2-139">GitHub API を使用するために必要な 2 つのヘッダー。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="dc7b2-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="dc7b2-140">CreateClient</span></span>

<span data-ttu-id="dc7b2-141"><xref:System.Net.Http.IHttpClientFactory.CreateClient*> が呼び出されるたびに、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="dc7b2-142">`HttpClient` の新しいインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="dc7b2-143">構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-143">The configuration action is called.</span></span>

<span data-ttu-id="dc7b2-144">名前付きクライアントを作成するには、その名前を `CreateClient` に渡します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="dc7b2-145">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="dc7b2-146">クライアントに構成されているベース アドレスが使用されるため、コードではパスを渡すだけで済みます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="dc7b2-147">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-147">Typed clients</span></span>

<span data-ttu-id="dc7b2-148">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="dc7b2-148">Typed clients:</span></span>

* <span data-ttu-id="dc7b2-149">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="dc7b2-150">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="dc7b2-151">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="dc7b2-152">たとえば、単一の型指定されたクライアントは、次のために使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="dc7b2-153">単一のバックエンド エンドポイント用。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="dc7b2-154">エンドポイントを処理するすべてのロジックをカプセル化するため。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="dc7b2-155">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="dc7b2-156">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="dc7b2-157">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-157">In the preceding code:</span></span>

* <span data-ttu-id="dc7b2-158">構成が型指定されたクライアントに移動されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="dc7b2-159">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="dc7b2-160">`HttpClient` の機能を公開する API 固有のメソッドを作成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="dc7b2-161">たとえば、`GetAspNetDocsIssues` メソッドでは、未解決の問題を取得するためのコードがカプセル化されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="dc7b2-162">次のコードでは、`Startup.ConfigureServices` 内で <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> を呼び出して、型指定されたクライアント クラスを登録しています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="dc7b2-163">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="dc7b2-164">上記のコードで、`AddHttpClient` は `GitHubService` を一時的なサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="dc7b2-165">この登録では、ファクトリ メソッドを使用して次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="dc7b2-166">`HttpClient` のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="dc7b2-167">`GitHubService` のインスタンスを作成し、`HttpClient` のインスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="dc7b2-168">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="dc7b2-169">型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="dc7b2-170">`HttpClient` は、型指定されたクライアント内にカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="dc7b2-171">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="dc7b2-172">上記のコードでは、`HttpClient` はプライベート フィールドに格納されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="dc7b2-173">`HttpClient` へのアクセスは、パブリック `GetRepos` メソッドによって行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="dc7b2-174">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-174">Generated clients</span></span>

<span data-ttu-id="dc7b2-175">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などのサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="dc7b2-176">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="dc7b2-177">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="dc7b2-178">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="dc7b2-179">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="dc7b2-180">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="dc7b2-181">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="dc7b2-182">POST、PUT、DELETE の要求を行う</span><span class="sxs-lookup"><span data-stu-id="dc7b2-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="dc7b2-183">前の例では、すべての HTTP 要求で GET HTTP 動詞が使用されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="dc7b2-184">`HttpClient` では、次のような他の HTTP 動詞もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="dc7b2-185">POST</span><span class="sxs-lookup"><span data-stu-id="dc7b2-185">POST</span></span>
* <span data-ttu-id="dc7b2-186">PUT</span><span class="sxs-lookup"><span data-stu-id="dc7b2-186">PUT</span></span>
* <span data-ttu-id="dc7b2-187">Del</span><span class="sxs-lookup"><span data-stu-id="dc7b2-187">DELETE</span></span>
* <span data-ttu-id="dc7b2-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="dc7b2-188">PATCH</span></span>

<span data-ttu-id="dc7b2-189">サポートされている HTTP 動詞の一覧については、「<xref:System.Net.Http.HttpMethod>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="dc7b2-190">次の例は、HTTP POST 要求の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="dc7b2-191">上記のコードの `CreateItemAsync` メソッドは:</span><span class="sxs-lookup"><span data-stu-id="dc7b2-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="dc7b2-192">`System.Text.Json` を使用して `TodoItem` パラメーターを JSON にシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="dc7b2-193">これは、<xref:System.Text.Json.JsonSerializerOptions> のインスタンスを使用して、シリアル化プロセスを構成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="dc7b2-194">HTTP 要求の本文で送信するためにシリアル化された JSON をパッケージ化する <xref:System.Net.Http.StringContent> のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="dc7b2-195"><xref:System.Net.Http.HttpClient.PostAsync%2A> を呼び出して、指定した URL に JSON の内容を送信します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="dc7b2-196">これは [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) に追加される相対 URL です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="dc7b2-197">応答状態コードが成功を示していない場合に、<xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> を呼び出して例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="dc7b2-198">`HttpClient` は、他の種類のコンテンツもサポートしています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="dc7b2-199">たとえば、<xref:System.Net.Http.MultipartContent> と<xref:System.Net.Http.StreamContent> です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="dc7b2-200">サポートされているコンテンツの一覧については、「<xref:System.Net.Http.HttpContent>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="dc7b2-201">HTTP PUT 要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="dc7b2-202">上記のコードは、POST の例によく似ています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="dc7b2-203">`SaveItemAsync` メソッドは `PostAsync` ではなく <xref:System.Net.Http.HttpClient.PutAsync%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="dc7b2-204">HTTP DELETE 要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="dc7b2-205">上記のコードの `DeleteItemAsync` メソッドは <xref:System.Net.Http.HttpClient.DeleteAsync%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="dc7b2-206">HTTP DELETE 要求には通常、本文が含まれていないため、`DeleteAsync` メソッドは `HttpContent` のインスタンスを受け入れるオーバーロードを提供しません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="dc7b2-207">`HttpClient` でのさまざまな HTTP 動詞の使用の詳細については、「<xref:System.Net.Http.HttpClient>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="dc7b2-208">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="dc7b2-208">Outgoing request middleware</span></span>

<span data-ttu-id="dc7b2-209">`HttpClient` は、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="dc7b2-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="dc7b2-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="dc7b2-211">各名前付きクライアントに適用するハンドラーの定義が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="dc7b2-212">送信要求ミドルウェア パイプラインを構築するための複数のハンドラーの登録とチェーン化がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="dc7b2-213">これらの各ハンドラーでは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="dc7b2-214">このパターンは次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-214">This pattern:</span></span>
  
    * <span data-ttu-id="dc7b2-215">ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="dc7b2-216">次のような HTTP 要求に関する横断的な問題を管理するためのメカニズムが提供されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="dc7b2-217">キャッシュ</span><span class="sxs-lookup"><span data-stu-id="dc7b2-217">caching</span></span>
      * <span data-ttu-id="dc7b2-218">エラー処理</span><span class="sxs-lookup"><span data-stu-id="dc7b2-218">error handling</span></span>
      * <span data-ttu-id="dc7b2-219">シリアル化</span><span class="sxs-lookup"><span data-stu-id="dc7b2-219">serialization</span></span>
      * <span data-ttu-id="dc7b2-220">ログ</span><span class="sxs-lookup"><span data-stu-id="dc7b2-220">logging</span></span>

<span data-ttu-id="dc7b2-221">デリゲート ハンドラーを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-221">To create a delegating handler:</span></span>

* <span data-ttu-id="dc7b2-222"><xref:System.Net.Http.DelegatingHandler> から派生します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="dc7b2-223"><xref:System.Net.Http.DelegatingHandler.SendAsync*> をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="dc7b2-224">パイプライン内の次のハンドラーに要求を渡す前に、コードを実行します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="dc7b2-225">上記のコードでは、`X-API-KEY` ヘッダーが要求に含まれているかどうかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="dc7b2-226">`X-API-KEY` がない場合は、<xref:System.Net.HttpStatusCode.BadRequest> が返されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="dc7b2-227"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> を使用して `HttpClient` の構成に複数のハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="dc7b2-228">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="dc7b2-229">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="dc7b2-230">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="dc7b2-231">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="dc7b2-232">送信要求ミドルウェアで DI を使用する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="dc7b2-233">`IHttpClientFactory` によって新しいデリゲート ハンドラーが作成される際、ハンドラーのコンストラクター パラメーターを満たすために DI が使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="dc7b2-234">`IHttpClientFactory` により、ハンドラーごとに **個別の** DI スコープが作成されます。これにより、ハンドラーによって "*スコープ付き*" サービスが使用されるときに、予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="dc7b2-235">たとえば、識別子 `OperationId` を持つ操作としてタスクを表す、次のインターフェイスとその実装について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="dc7b2-236">その名前が示すように、`IOperationScoped` は "*スコープ付き*" 有効期間を使用して DI に登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="dc7b2-237">次のデリゲート ハンドラーでは、`IOperationScoped` を使用して、送信要求用の `X-OPERATION-ID` ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="dc7b2-238">[`HttpRequestsSample` ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] で、`/Operation` に移動してページを更新します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="dc7b2-239">要求スコープの値は要求ごとに変更されますが、ハンドラーのスコープの値は 5 秒ごとに変更されるだけです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="dc7b2-240">ハンドラーは、任意のスコープのサービスに依存することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="dc7b2-241">ハンドラーが依存するサービスは、ハンドラーが破棄されるときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="dc7b2-242">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="dc7b2-243">[HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) を使用して、ハンドラーにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="dc7b2-244"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="dc7b2-245">データを渡すカスタムの <xref:System.Threading.AsyncLocal`1> ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="dc7b2-246">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-246">Use Polly-based handlers</span></span>

<span data-ttu-id="dc7b2-247">`IHttpClientFactory` は、サードパーティのライブラリ [Polly](https://github.com/App-vNext/Polly) と統合します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="dc7b2-248">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="dc7b2-249">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="dc7b2-250">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-251">Polly の拡張機能では、クライアントへの Polly ベースのハンドラーの追加がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="dc7b2-252">Polly では、[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="dc7b2-253">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-253">Handle transient faults</span></span>

<span data-ttu-id="dc7b2-254">通常、外部 HTTP を呼び出すときに発生する障害は、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="dc7b2-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> では、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="dc7b2-256">`AddTransientHttpErrorPolicy` で構成されたポリシーでは、次の応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="dc7b2-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="dc7b2-257">HTTP 5xx</span></span>
* <span data-ttu-id="dc7b2-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="dc7b2-258">HTTP 408</span></span>

<span data-ttu-id="dc7b2-259">`AddTransientHttpErrorPolicy` では、可能性のある一時的障害を表すエラーを処理するために構成された `PolicyBuilder` オブジェクトへのアクセスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="dc7b2-260">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="dc7b2-261">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="dc7b2-262">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-262">Dynamically select policies</span></span>

<span data-ttu-id="dc7b2-263">Polly ベースのハンドラーを追加するための拡張メソッドが提供されています (<xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> など)。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="dc7b2-264">次の `AddPolicyHandler` のオーバーロードでは、要求が検査されて、適用するポリシーが決定されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="dc7b2-265">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="dc7b2-266">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="dc7b2-267">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="dc7b2-268">Polly のポリシーは入れ子にするのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="dc7b2-269">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="dc7b2-269">In the preceding example:</span></span>

* <span data-ttu-id="dc7b2-270">2 つのハンドラーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-270">Two handlers are added.</span></span>
* <span data-ttu-id="dc7b2-271">1 つ目のハンドラーでは、<xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> を使用して再試行ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="dc7b2-272">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="dc7b2-273">2 つ目の `AddTransientHttpErrorPolicy` の呼び出しでは、サーキット ブレーカー ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="dc7b2-274">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="dc7b2-275">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="dc7b2-276">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="dc7b2-277">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="dc7b2-278">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="dc7b2-279">次のコードの内容は以下のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-279">In the following code:</span></span>

* <span data-ttu-id="dc7b2-280">"regular" ポリシーと "long" ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="dc7b2-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> では、レジストリから "regular" ポリシーと "long" ポリシーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="dc7b2-282">`IHttpClientFactory` と Polly の統合の詳細については、[Polly に関する wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="dc7b2-283">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="dc7b2-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="dc7b2-284">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="dc7b2-285"><xref:System.Net.Http.HttpMessageHandler> は、名前付きクライアントごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="dc7b2-286">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="dc7b2-287">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="dc7b2-288">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="dc7b2-289">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="dc7b2-290">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="dc7b2-291">また、一部のハンドラーでは接続が無期限に開かれており、DNS (ドメイン ネーム システム) の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="dc7b2-292">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="dc7b2-293">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="dc7b2-294">通常、`HttpClient` インスタンスは、破棄する必要の **ない** .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="dc7b2-295">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="dc7b2-296">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="dc7b2-297">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="dc7b2-298">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="dc7b2-299">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="dc7b2-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="dc7b2-300">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="dc7b2-301">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="dc7b2-302">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="dc7b2-303">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="dc7b2-304">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="dc7b2-305">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="dc7b2-306">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="dc7b2-307">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="dc7b2-308">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-309">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="dc7b2-310">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="dc7b2-311">Cookies</span><span class="sxs-lookup"><span data-stu-id="dc7b2-311">Cookies</span></span>

<span data-ttu-id="dc7b2-312">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="dc7b2-313">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="dc7b2-314">cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="dc7b2-315">自動的な cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="dc7b2-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="dc7b2-316">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="dc7b2-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="dc7b2-317"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="dc7b2-318">ログの記録</span><span class="sxs-lookup"><span data-stu-id="dc7b2-318">Logging</span></span>

<span data-ttu-id="dc7b2-319">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="dc7b2-320">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="dc7b2-321">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="dc7b2-322">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="dc7b2-323">たとえば、*MyNamedClient* という名前のクライアントでは、"System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler" というカテゴリでメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="dc7b2-324">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="dc7b2-325">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="dc7b2-326">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="dc7b2-327">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="dc7b2-328">*MyNamedClient* の例では、これらのメッセージはログ カテゴリ "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler" でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="dc7b2-329">要求では、他のすべてのハンドラーが実行された後、要求が送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="dc7b2-330">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="dc7b2-331">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="dc7b2-332">これには、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="dc7b2-333">ログのカテゴリにクライアントの名前を含めると、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="dc7b2-334">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="dc7b2-335">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="dc7b2-336">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="dc7b2-337"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="dc7b2-338">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="dc7b2-339">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="dc7b2-340">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="dc7b2-341">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="dc7b2-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="dc7b2-342">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="dc7b2-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="dc7b2-343">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-343">In the following example:</span></span>

* <span data-ttu-id="dc7b2-344"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="dc7b2-345">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="dc7b2-346">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="dc7b2-347">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="dc7b2-348">ヘッダー伝達ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="dc7b2-348">Header propagation middleware</span></span>

<span data-ttu-id="dc7b2-349">ヘッダー伝達は、受信要求から送信 HTTP クライアント要求に HTTP ヘッダーを伝達するための ASP.NET Core ミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="dc7b2-350">ヘッダー伝達を使用するには、次を行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-350">To use header propagation:</span></span>

* <span data-ttu-id="dc7b2-351">[Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="dc7b2-352">`Startup` でミドルウェアと `HttpClient` を構成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="dc7b2-353">クライアントで、構成されたヘッダーが送信要求に含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="dc7b2-354">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dc7b2-354">Additional resources</span></span>

* [<span data-ttu-id="dc7b2-355">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="dc7b2-356">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="dc7b2-357">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="dc7b2-358">.NET で JSON のシリアル化と逆シリアル化を行う方法</span><span class="sxs-lookup"><span data-stu-id="dc7b2-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="dc7b2-359">寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="dc7b2-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="dc7b2-360">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="dc7b2-361">次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-361">It offers the following benefits:</span></span>

* <span data-ttu-id="dc7b2-362">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-363">たとえば、*github* クライアントを登録して、[GitHub](https://github.com/) にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="dc7b2-364">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="dc7b2-365">`HttpClient` でのハンドラーのデリゲートにより送信ミドルウェアの概念を体系化し、Polly ベースのミドルウェアでそれを利用するための拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="dc7b2-366">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="dc7b2-367">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="dc7b2-368">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="dc7b2-369">利用パターン</span><span class="sxs-lookup"><span data-stu-id="dc7b2-369">Consumption patterns</span></span>

<span data-ttu-id="dc7b2-370">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="dc7b2-371">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="dc7b2-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="dc7b2-372">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="dc7b2-373">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="dc7b2-374">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="dc7b2-375">これらの間に厳密な優劣はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="dc7b2-376">どの方法が最善かは、アプリの制約に依存します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="dc7b2-377">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="dc7b2-377">Basic usage</span></span>

<span data-ttu-id="dc7b2-378">`IHttpClientFactory` は、`Startup.ConfigureServices` メソッドの内部で `IServiceCollection` の `AddHttpClient` 拡張メソッドを呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="dc7b2-379">登録が済むと、コードは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用してサービスを挿入できる任意の場所で、`IHttpClientFactory` を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dc7b2-380">`IHttpClientFactory` を使用して、`HttpClient` インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="dc7b2-381">このように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングする優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="dc7b2-382">`HttpClient` の使用方法に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="dc7b2-383">現在 `HttpClient` インスタンスが作成されている場所で、それを <xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="dc7b2-384">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-384">Named clients</span></span>

<span data-ttu-id="dc7b2-385">それぞれ構成が異なる多数の `HttpClient` をアプリで個別に使用する必要がある場合は、**名前付きクライアント** を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="dc7b2-386">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="dc7b2-387">上記のコードでは、*github* という名前を指定して `AddHttpClient` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="dc7b2-388">このクライアントには既定の構成がいくつか適用されています。つまり、GitHub API を使用するために必要なベース アドレスと 2 つのヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="dc7b2-389">`CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが作成されて、構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="dc7b2-390">名前付きクライアントを使用するには、文字列パラメーターを `CreateClient` に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="dc7b2-391">作成するクライアントの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="dc7b2-392">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="dc7b2-393">クライアントに構成されているベース アドレスが使用されるため、パスだけを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="dc7b2-394">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-394">Typed clients</span></span>

<span data-ttu-id="dc7b2-395">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="dc7b2-395">Typed clients:</span></span>

* <span data-ttu-id="dc7b2-396">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="dc7b2-397">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="dc7b2-398">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="dc7b2-399">たとえば、単一の型指定されたクライアントを単一のバックエンド エンドポイントに対して使用し、そのエンドポイントを操作するすべてのロジックをカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="dc7b2-400">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="dc7b2-401">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="dc7b2-402">上記のコードでは、構成が型指定されたクライアントに移動されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="dc7b2-403">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="dc7b2-404">`HttpClient` 機能を公開する API 固有のメソッドを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="dc7b2-405">`GetAspNetDocsIssues` メソッドは、GitHub リポジトリで最新の未解決の問題をクエリして解析するために必要なコードをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="dc7b2-406">型指定されたクライアントを登録するには、ジェネリック <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 拡張メソッドを `Startup.ConfigureServices` 内で使用して、型指定されたクライアントのクラスを指定します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="dc7b2-407">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="dc7b2-408">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="dc7b2-409">好みに応じて、型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="dc7b2-410">型指定されたクライアントの内部に `HttpClient` を完全にカプセル化することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="dc7b2-411">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドとして提供することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="dc7b2-412">上記のコードでは、`HttpClient` はプライベート フィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="dc7b2-413">外部呼び出しを行うすべてのアクセスは、`GetRepos` メソッドを通して行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="dc7b2-414">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-414">Generated clients</span></span>

<span data-ttu-id="dc7b2-415">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などの他のサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="dc7b2-416">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="dc7b2-417">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="dc7b2-418">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="dc7b2-419">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-419">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="dc7b2-420">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-420">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="dc7b2-421">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="dc7b2-422">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="dc7b2-422">Outgoing request middleware</span></span>

<span data-ttu-id="dc7b2-423">`HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="dc7b2-424">`IHttpClientFactory` を使用すると、各名前付きクライアントに適用するハンドラーを簡単に定義できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="dc7b2-425">複数のハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="dc7b2-426">各ハンドラーは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="dc7b2-427">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="dc7b2-428">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="dc7b2-429">ハンドラーを作成するには、<xref:System.Net.Http.DelegatingHandler> の派生クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="dc7b2-430">パイプライン内の次のハンドラーに要求を渡す前にコードを実行するように、`SendAsync` メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="dc7b2-431">上記のコードでは、基本的なハンドラーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="dc7b2-432">このコードは、`X-API-KEY` ヘッダーが要求に含まれていたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="dc7b2-433">ヘッダーがない場合、HTTP 呼び出しを行わずに適切な応答を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="dc7b2-434">登録時には、1 つまたは複数のハンドラーを `HttpClient` の構成に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="dc7b2-435">このタスクは、<xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> の拡張メソッドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="dc7b2-436">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="dc7b2-437">`IHttpClientFactory` では、ハンドラーごとに個別の DI スコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="dc7b2-438">ハンドラーは、任意のスコープのサービスに自由に依存することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="dc7b2-439">ハンドラーが依存するサービスは、ハンドラーが破棄されるときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="dc7b2-440">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="dc7b2-441">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="dc7b2-442">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="dc7b2-443">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="dc7b2-444">`HttpRequestMessage.Properties` を使ってデータをハンドラーに渡します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="dc7b2-445">`IHttpContextAccessor` を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="dc7b2-446">データを渡すカスタムの `AsyncLocal` ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="dc7b2-447">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-447">Use Polly-based handlers</span></span>

<span data-ttu-id="dc7b2-448">`IHttpClientFactory` は、[Polly](https://github.com/App-vNext/Polly) という名前の人気のあるサードパーティ製ライブラリと統合します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="dc7b2-449">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="dc7b2-450">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="dc7b2-451">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-452">Polly の拡張機能は、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-452">The Polly extensions:</span></span>

* <span data-ttu-id="dc7b2-453">クライアントへの Polly ベースのハンドラーの追加がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="dc7b2-454">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージのインストール後に使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="dc7b2-455">このパッケージは、ASP.NET Core 共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="dc7b2-456">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-456">Handle transient faults</span></span>

<span data-ttu-id="dc7b2-457">外部 HTTP を呼び出す際に発生する一般的な障害のほとんどは、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="dc7b2-458">`AddTransientHttpErrorPolicy` という便利な拡張メソッドが含まれており、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="dc7b2-459">この拡張メソッドで構成されたポリシーは、`HttpRequestException`、HTTP 5xx 応答、および HTTP 408 応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="dc7b2-460">`AddTransientHttpErrorPolicy` 拡張メソッドは、`Startup.ConfigureServices` 内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc7b2-461">この拡張メソッドは、可能性のある一時的障害を表すエラーを処理するように構成された `PolicyBuilder` オブジェクトへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="dc7b2-462">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="dc7b2-463">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="dc7b2-464">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-464">Dynamically select policies</span></span>

<span data-ttu-id="dc7b2-465">Polly ベースのハンドラーを追加するために使用できる追加の拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="dc7b2-466">そのような拡張メソッドの 1 つは `AddPolicyHandler` であり、複数のオーバーロードを持ちます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="dc7b2-467">1 つのオーバーロードでは、適用するポリシーを定義するときに要求を検査できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="dc7b2-468">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="dc7b2-469">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="dc7b2-470">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="dc7b2-471">Polly ポリシーを入れ子にして拡張機能を提供するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="dc7b2-472">前の例では、2 つのハンドラーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="dc7b2-473">最初のハンドラーは、`AddTransientHttpErrorPolicy` 拡張メソッドを使用して再試行ポリシーを追加します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="dc7b2-474">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="dc7b2-475">`AddTransientHttpErrorPolicy` の 2 番目の呼び出しでは、サーキット ブレーカー ポリシーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="dc7b2-476">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="dc7b2-477">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="dc7b2-478">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="dc7b2-479">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="dc7b2-480">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="dc7b2-481">提供されている拡張メソッドを使用することで、レジストリからポリシーを使用してハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="dc7b2-482">上記のコードでは、`PolicyRegistry` が `ServiceCollection` に追加されるときに 2 つのポリシーが登録されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="dc7b2-483">レジストリからポリシーを使用するには、適用するポリシーの名前を渡して `AddPolicyHandlerFromRegistry` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="dc7b2-484">`IHttpClientFactory` および Polly の統合について詳しくは、[Polly の Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="dc7b2-485">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="dc7b2-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="dc7b2-486">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="dc7b2-487">名前付きクライアントごとに <xref:System.Net.Http.HttpMessageHandler> が存在します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="dc7b2-488">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="dc7b2-489">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="dc7b2-490">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="dc7b2-491">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="dc7b2-492">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="dc7b2-493">また、一部のハンドラーは接続を無期限に開いており、DNS の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="dc7b2-494">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="dc7b2-495">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="dc7b2-496">オーバーライドするには、クライアント作成時に返された `IHttpClientBuilder` で <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="dc7b2-497">クライアントを破棄する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="dc7b2-498">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="dc7b2-499">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-500">通常、`HttpClient` インスタンスは、破棄を必要としない .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="dc7b2-501">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="dc7b2-502">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="dc7b2-503">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="dc7b2-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="dc7b2-504">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="dc7b2-505">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="dc7b2-506">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="dc7b2-507">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="dc7b2-508">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="dc7b2-509">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="dc7b2-510">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="dc7b2-511">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="dc7b2-512">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-513">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="dc7b2-514">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="dc7b2-515">Cookies</span><span class="sxs-lookup"><span data-stu-id="dc7b2-515">Cookies</span></span>

<span data-ttu-id="dc7b2-516">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="dc7b2-517">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="dc7b2-518">cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="dc7b2-519">自動的な cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="dc7b2-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="dc7b2-520">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="dc7b2-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="dc7b2-521"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="dc7b2-522">ログの記録</span><span class="sxs-lookup"><span data-stu-id="dc7b2-522">Logging</span></span>

<span data-ttu-id="dc7b2-523">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="dc7b2-524">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="dc7b2-525">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="dc7b2-526">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="dc7b2-527">たとえば、*MyNamedClient* という名前のクライアントは、カテゴリ `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` でメッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="dc7b2-528">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="dc7b2-529">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="dc7b2-530">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="dc7b2-531">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="dc7b2-532">*MyNamedClient* の例では、それらのメッセージはログ カテゴリ `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` に対して記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="dc7b2-533">要求では、他のすべてのハンドラーが実行した後、要求がネットワークに送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="dc7b2-534">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="dc7b2-535">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="dc7b2-536">これには、たとえば、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="dc7b2-537">ログのカテゴリにクライアントの名前を含めると、必要に応じて、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="dc7b2-538">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="dc7b2-539">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="dc7b2-540">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="dc7b2-541"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="dc7b2-542">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="dc7b2-543">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="dc7b2-544">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="dc7b2-545">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="dc7b2-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="dc7b2-546">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="dc7b2-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="dc7b2-547">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-547">In the following example:</span></span>

* <span data-ttu-id="dc7b2-548"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="dc7b2-549">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="dc7b2-550">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="dc7b2-551">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="dc7b2-552">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dc7b2-552">Additional resources</span></span>

* [<span data-ttu-id="dc7b2-553">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="dc7b2-554">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="dc7b2-555">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="dc7b2-556">寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="dc7b2-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="dc7b2-557">アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="dc7b2-558">次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-558">It offers the following benefits:</span></span>

* <span data-ttu-id="dc7b2-559">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-560">たとえば、*github* クライアントを登録して、[GitHub](https://github.com/) にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="dc7b2-561">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="dc7b2-562">`HttpClient` でのハンドラーのデリゲートにより送信ミドルウェアの概念を体系化し、Polly ベースのミドルウェアでそれを利用するための拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="dc7b2-563">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="dc7b2-564">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="dc7b2-565">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dc7b2-566">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-566">Prerequisites</span></span>

<span data-ttu-id="dc7b2-567">.NET Framework をターゲットとするプロジェクトでは、[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet パッケージのインストールが必要です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="dc7b2-568">.NET Core をターゲットとし、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するプロジェクトには、既に `Microsoft.Extensions.Http` パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="dc7b2-569">利用パターン</span><span class="sxs-lookup"><span data-stu-id="dc7b2-569">Consumption patterns</span></span>

<span data-ttu-id="dc7b2-570">アプリで `IHttpClientFactory` を使用するには複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="dc7b2-571">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="dc7b2-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="dc7b2-572">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="dc7b2-573">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="dc7b2-574">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="dc7b2-575">これらの間に厳密な優劣はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="dc7b2-576">どの方法が最善かは、アプリの制約に依存します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="dc7b2-577">基本的な使用方法</span><span class="sxs-lookup"><span data-stu-id="dc7b2-577">Basic usage</span></span>

<span data-ttu-id="dc7b2-578">`IHttpClientFactory` は、`Startup.ConfigureServices` メソッドの内部で `IServiceCollection` の `AddHttpClient` 拡張メソッドを呼び出すことによって登録できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="dc7b2-579">登録が済むと、コードは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用してサービスを挿入できる任意の場所で、`IHttpClientFactory` を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dc7b2-580">`IHttpClientFactory` を使用して、`HttpClient` インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="dc7b2-581">このように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングする優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="dc7b2-582">`HttpClient` の使用方法に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="dc7b2-583">現在 `HttpClient` インスタンスが作成されている場所で、それを <xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="dc7b2-584">名前付きクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-584">Named clients</span></span>

<span data-ttu-id="dc7b2-585">それぞれ構成が異なる多数の `HttpClient` をアプリで個別に使用する必要がある場合は、**名前付きクライアント** を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="dc7b2-586">名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="dc7b2-587">上記のコードでは、*github* という名前を指定して `AddHttpClient` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="dc7b2-588">このクライアントには既定の構成がいくつか適用されています。つまり、GitHub API を使用するために必要なベース アドレスと 2 つのヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="dc7b2-589">`CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが作成されて、構成アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="dc7b2-590">名前付きクライアントを使用するには、文字列パラメーターを `CreateClient` に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="dc7b2-591">作成するクライアントの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="dc7b2-592">上記のコードでは、要求でホスト名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="dc7b2-593">クライアントに構成されているベース アドレスが使用されるため、パスだけを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="dc7b2-594">型指定されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-594">Typed clients</span></span>

<span data-ttu-id="dc7b2-595">型指定されたクライアント:</span><span class="sxs-lookup"><span data-stu-id="dc7b2-595">Typed clients:</span></span>

* <span data-ttu-id="dc7b2-596">キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="dc7b2-597">クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="dc7b2-598">特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="dc7b2-599">たとえば、単一の型指定されたクライアントを単一のバックエンド エンドポイントに対して使用し、そのエンドポイントを操作するすべてのロジックをカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="dc7b2-600">DI に対応しており、アプリ内の必要な場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="dc7b2-601">型指定されたクライアントは、そのコンストラクターで `HttpClient` パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="dc7b2-602">上記のコードでは、構成が型指定されたクライアントに移動されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="dc7b2-603">`HttpClient` オブジェクトは、パブリック プロパティとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="dc7b2-604">`HttpClient` 機能を公開する API 固有のメソッドを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="dc7b2-605">`GetAspNetDocsIssues` メソッドは、GitHub リポジトリで最新の未解決の問題をクエリして解析するために必要なコードをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="dc7b2-606">型指定されたクライアントを登録するには、ジェネリック <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 拡張メソッドを `Startup.ConfigureServices` 内で使用して、型指定されたクライアントのクラスを指定します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="dc7b2-607">型指定されたクライアントは、DI で一時的として登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="dc7b2-608">型指定されたクライアントは、直接挿入して使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="dc7b2-609">好みに応じて、型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="dc7b2-610">型指定されたクライアントの内部に `HttpClient` を完全にカプセル化することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="dc7b2-611">プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドとして提供することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="dc7b2-612">上記のコードでは、`HttpClient` はプライベート フィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="dc7b2-613">外部呼び出しを行うすべてのアクセスは、`GetRepos` メソッドを通して行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="dc7b2-614">生成されたクライアント</span><span class="sxs-lookup"><span data-stu-id="dc7b2-614">Generated clients</span></span>

<span data-ttu-id="dc7b2-615">`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などの他のサードパーティ製ライブラリと組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="dc7b2-616">Refit は、.NET 用の REST ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="dc7b2-617">REST API をライブ インターフェイスに変換します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="dc7b2-618">インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="dc7b2-619">インターフェイスと応答は、外部の API とその応答を表すように定義されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-619">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="dc7b2-620">型指定されたクライアントを追加し、Refit を使用して実装を生成できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-620">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="dc7b2-621">定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="dc7b2-622">送信要求ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="dc7b2-622">Outgoing request middleware</span></span>

<span data-ttu-id="dc7b2-623">`HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="dc7b2-624">`IHttpClientFactory` を使用すると、各名前付きクライアントに適用するハンドラーを簡単に定義できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="dc7b2-625">複数のハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="dc7b2-626">各ハンドラーは、送信要求の前と後に処理を実行できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="dc7b2-627">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="dc7b2-628">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="dc7b2-629">ハンドラーを作成するには、<xref:System.Net.Http.DelegatingHandler> の派生クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="dc7b2-630">パイプライン内の次のハンドラーに要求を渡す前にコードを実行するように、`SendAsync` メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="dc7b2-631">上記のコードでは、基本的なハンドラーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="dc7b2-632">このコードは、`X-API-KEY` ヘッダーが要求に含まれていたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="dc7b2-633">ヘッダーがない場合、HTTP 呼び出しを行わずに適切な応答を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="dc7b2-634">登録時には、1 つまたは複数のハンドラーを `HttpClient` の構成に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="dc7b2-635">このタスクは、<xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> の拡張メソッドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="dc7b2-636">上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="dc7b2-637">ハンドラーは、スコープではなく、一時的なサービスとして DI で登録する **必要があります**。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="dc7b2-638">ハンドラーがスコープ付きサービスとして登録されており、ハンドラーが依存するサービスを破棄できる場合:</span><span class="sxs-lookup"><span data-stu-id="dc7b2-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="dc7b2-639">ハンドラーのサービスは、ハンドラーがスコープ外に出る前に破棄されることがあります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="dc7b2-640">破棄されたハンドラー サービスが原因でハンドラーが失敗します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="dc7b2-641">登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="dc7b2-642">実行する順序で、複数のハンドラーを登録することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="dc7b2-643">最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="dc7b2-644">次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="dc7b2-645">`HttpRequestMessage.Properties` を使ってデータをハンドラーに渡します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="dc7b2-646">`IHttpContextAccessor` を使って現在の要求にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="dc7b2-647">データを渡すカスタムの `AsyncLocal` ストレージ オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="dc7b2-648">Polly ベースのハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-648">Use Polly-based handlers</span></span>

<span data-ttu-id="dc7b2-649">`IHttpClientFactory` は、[Polly](https://github.com/App-vNext/Polly) という名前の人気のあるサードパーティ製ライブラリと統合します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="dc7b2-650">Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="dc7b2-651">開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="dc7b2-652">構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-653">Polly の拡張機能は、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-653">The Polly extensions:</span></span>

* <span data-ttu-id="dc7b2-654">クライアントへの Polly ベースのハンドラーの追加がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="dc7b2-655">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージのインストール後に使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="dc7b2-656">このパッケージは、ASP.NET Core 共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="dc7b2-657">一時的な障害を処理する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-657">Handle transient faults</span></span>

<span data-ttu-id="dc7b2-658">外部 HTTP を呼び出す際に発生する一般的な障害のほとんどは、一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="dc7b2-659">`AddTransientHttpErrorPolicy` という便利な拡張メソッドが含まれており、一時的なエラーを処理するためのポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="dc7b2-660">この拡張メソッドで構成されたポリシーは、`HttpRequestException`、HTTP 5xx 応答、および HTTP 408 応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="dc7b2-661">`AddTransientHttpErrorPolicy` 拡張メソッドは、`Startup.ConfigureServices` 内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc7b2-662">この拡張メソッドは、可能性のある一時的障害を表すエラーを処理するように構成された `PolicyBuilder` オブジェクトへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="dc7b2-663">上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="dc7b2-664">失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="dc7b2-665">ポリシーを動的に選択する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-665">Dynamically select policies</span></span>

<span data-ttu-id="dc7b2-666">Polly ベースのハンドラーを追加するために使用できる追加の拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="dc7b2-667">そのような拡張メソッドの 1 つは `AddPolicyHandler` であり、複数のオーバーロードを持ちます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="dc7b2-668">1 つのオーバーロードでは、適用するポリシーを定義するときに要求を検査できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="dc7b2-669">上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="dc7b2-670">他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="dc7b2-671">複数の Polly ハンドラーを追加する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="dc7b2-672">Polly ポリシーを入れ子にして拡張機能を提供するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="dc7b2-673">前の例では、2 つのハンドラーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="dc7b2-674">最初のハンドラーは、`AddTransientHttpErrorPolicy` 拡張メソッドを使用して再試行ポリシーを追加します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="dc7b2-675">失敗した要求は 3 回まで再試行されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="dc7b2-676">`AddTransientHttpErrorPolicy` の 2 番目の呼び出しでは、サーキット ブレーカー ポリシーが追加されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="dc7b2-677">試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="dc7b2-678">サーキット ブレーカー ポリシーはステートフルです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="dc7b2-679">このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="dc7b2-680">Polly レジストリからポリシーを追加する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="dc7b2-681">定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="dc7b2-682">提供されている拡張メソッドを使用することで、レジストリからポリシーを使用してハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="dc7b2-683">上記のコードでは、`PolicyRegistry` が `ServiceCollection` に追加されるときに 2 つのポリシーが登録されています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="dc7b2-684">レジストリからポリシーを使用するには、適用するポリシーの名前を渡して `AddPolicyHandlerFromRegistry` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="dc7b2-685">`IHttpClientFactory` および Polly の統合について詳しくは、[Polly の Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="dc7b2-686">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="dc7b2-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="dc7b2-687">`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="dc7b2-688">名前付きクライアントごとに <xref:System.Net.Http.HttpMessageHandler> が存在します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="dc7b2-689">ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="dc7b2-690">`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="dc7b2-691">新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="dc7b2-692">通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="dc7b2-693">必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="dc7b2-694">また、一部のハンドラーは接続を無期限に開いており、DNS の変更にハンドラーが対応できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="dc7b2-695">ハンドラーの既定の有効期間は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="dc7b2-696">名前付きクライアントごとに、既定値をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="dc7b2-697">オーバーライドするには、クライアント作成時に返された `IHttpClientBuilder` で <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="dc7b2-698">クライアントを破棄する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="dc7b2-699">破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="dc7b2-700">`IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-701">通常、`HttpClient` インスタンスは、破棄を必要としない .NET オブジェクトとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="dc7b2-702">`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="dc7b2-703">`IHttpClientFactory` に移行した後は、このパターンは不要になります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="dc7b2-704">IHttpClientFactory の代替手段</span><span class="sxs-lookup"><span data-stu-id="dc7b2-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="dc7b2-705">DI 対応のアプリ内で `IHttpClientFactory` を使用すれば、次のことを回避できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="dc7b2-706">`HttpMessageHandler` インスタンスをプールすることによるリソース枯渇の問題。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="dc7b2-707">一定の間隔で `HttpMessageHandler` インスタンスを循環させることによって発生する古くなった DNS の問題。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="dc7b2-708">有効期間の長い <xref:System.Net.Http.SocketsHttpHandler> インスタンスを使用して、上記の問題を解決する別の方法があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="dc7b2-709">アプリの起動時に `SocketsHttpHandler` インスタンスを作成し、アプリの有効期間中、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="dc7b2-710">DNS の更新時間に基づいて、<xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> を適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="dc7b2-711">必要に応じて `new HttpClient(handler, disposeHandler: false)` を使用して `HttpClient` インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="dc7b2-712">上記の方法を使用すると、`IHttpClientFactory` が同様の方法で解決するリソース管理の問題を解決できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="dc7b2-713">`SocketsHttpHandler` を使用すると、`HttpClient` インスタンス間で接続を共有できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="dc7b2-714">この共有によってソケットの枯渇が防止されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="dc7b2-715">`SocketsHttpHandler` では、古くなった DNS の問題を回避するために `PooledConnectionLifetime` に従って接続を循環されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="dc7b2-716">Cookies</span><span class="sxs-lookup"><span data-stu-id="dc7b2-716">Cookies</span></span>

<span data-ttu-id="dc7b2-717">`HttpMessageHandler` インスタンスをプールすると、`CookieContainer` オブジェクトが共有されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="dc7b2-718">予期せぬ `CookieContainer` オブジェクト共有があると、多くの場合、コードは不適切なものとなります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="dc7b2-719">cookie を必要とするアプリの場合は、次のいずれかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="dc7b2-720">自動的な cookie 処理の無効化</span><span class="sxs-lookup"><span data-stu-id="dc7b2-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="dc7b2-721">`IHttpClientFactory` の回避</span><span class="sxs-lookup"><span data-stu-id="dc7b2-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="dc7b2-722"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> を呼び出して、自動的な cookie 処理を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="dc7b2-723">ログの記録</span><span class="sxs-lookup"><span data-stu-id="dc7b2-723">Logging</span></span>

<span data-ttu-id="dc7b2-724">`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="dc7b2-725">既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="dc7b2-726">要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="dc7b2-727">各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="dc7b2-728">たとえば、*MyNamedClient* という名前のクライアントは、カテゴリ `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` でメッセージをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="dc7b2-729">*LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="dc7b2-730">要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="dc7b2-731">応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="dc7b2-732">ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="dc7b2-733">*MyNamedClient* の例では、それらのメッセージはログ カテゴリ `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` に対して記録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="dc7b2-734">要求では、他のすべてのハンドラーが実行した後、要求がネットワークに送信される直前に、これが行われます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="dc7b2-735">応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="dc7b2-736">パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="dc7b2-737">これには、たとえば、要求ヘッダーや応答状態コードへの変更を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="dc7b2-738">ログのカテゴリにクライアントの名前を含めると、必要に応じて、特定の名前付きクライアントでログをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="dc7b2-739">HttpMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="dc7b2-740">クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="dc7b2-741">名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="dc7b2-742"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="dc7b2-743">デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="dc7b2-744">コンソール アプリで IHttpClientFactory を使用する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="dc7b2-745">コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="dc7b2-746">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="dc7b2-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="dc7b2-747">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="dc7b2-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="dc7b2-748">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-748">In the following example:</span></span>

* <span data-ttu-id="dc7b2-749"><xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="dc7b2-750">`MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="dc7b2-751">`HttpClient` は Web ページを取得する目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="dc7b2-752">`Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="dc7b2-753">ヘッダー伝達ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="dc7b2-753">Header propagation middleware</span></span>

<span data-ttu-id="dc7b2-754">ヘッダー伝達は、受信要求から送信 HTTP クライアント要求に HTTP ヘッダーを伝達するためのコミュニティでサポートされたミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="dc7b2-755">ヘッダー伝達を使用するには、次を行います。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-755">To use header propagation:</span></span>

* <span data-ttu-id="dc7b2-756">パッケージ [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) のコミュニティでサポートされているポートを参照します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="dc7b2-757">ASP.NET Core 3.1 以降では、[Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="dc7b2-758">`Startup` でミドルウェアと `HttpClient` を構成します。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="dc7b2-759">クライアントで、構成されたヘッダーが送信要求に含まれます。</span><span class="sxs-lookup"><span data-stu-id="dc7b2-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="dc7b2-760">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dc7b2-760">Additional resources</span></span>

* [<span data-ttu-id="dc7b2-761">HttpClientFactory を使用して回復力の高い HTTP 要求を実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="dc7b2-762">HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="dc7b2-763">サーキット ブレーカー パターンを実装する</span><span class="sxs-lookup"><span data-stu-id="dc7b2-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

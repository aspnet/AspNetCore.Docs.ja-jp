---
title: SignalRJava クライアントの ASP.NET Core
author: mikaelm12
description: ASP.NET Core Java クライアントの使用方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: da6876e0540579dac5fb9e92362b38a398bca4d5
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972081"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="87dbd-103">SignalRJava クライアントの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87dbd-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="87dbd-104">作成者: [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="87dbd-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="87dbd-105">Java クライアントを使用すると、 SignalR Android アプリを含む java コードから ASP.NET Core サーバーに接続できます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="87dbd-106">[JavaScript クライアント](xref:signalr/javascript-client)や[.net クライアント](xref:signalr/dotnet-client)と同様に、Java クライアントを使用すると、メッセージを受信してハブにリアルタイムで送信することができます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="87dbd-107">Java クライアントは ASP.NET Core 2.2 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="87dbd-108">この記事で参照されているサンプルの Java コンソールアプリは、java クライアントを使用し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="87dbd-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="87dbd-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="87dbd-110">SignalRJava クライアントパッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="87dbd-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="87dbd-111">*Signalr* の JAR ファイルを使用すると、クライアントはハブに接続でき SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="87dbd-112">最新の JAR ファイルのバージョン番号を確認するには、 [Maven の検索結果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="87dbd-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="87dbd-113">Gradle を使用する場合は、 `dependencies` *Gradle* ファイルのセクションに次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="87dbd-114">Maven を使用する場合は、 `<dependencies>` *pom.xml* ファイルの要素内に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="87dbd-115">ハブへの接続</span><span class="sxs-lookup"><span data-stu-id="87dbd-115">Connect to a hub</span></span>

<span data-ttu-id="87dbd-116">を確立するには、を `HubConnection` `HubConnectionBuilder` 使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="87dbd-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="87dbd-117">ハブの URL とログレベルは、接続の作成時に構成できます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="87dbd-118">前のメソッドのいずれかを呼び出すことによって、必要なオプションを構成 `HubConnectionBuilder` `build` します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="87dbd-119">との接続を開始 `start` します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="87dbd-120">クライアントからのハブメソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="87dbd-120">Call hub methods from client</span></span>

<span data-ttu-id="87dbd-121">を呼び出すと、 `send` ハブメソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="87dbd-122">ハブメソッドの名前と、ハブメソッドで定義されているすべての引数をに渡し `send` ます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="87dbd-123">クライアントからのハブメソッドの呼び出しは、Azure SignalR サービスを *既定* のモードで使用している場合にのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="87dbd-124">詳細については、「 [よく寄せられる質問 (azure Signalr GitHub リポジトリ)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="87dbd-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="87dbd-125">ハブからクライアントメソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="87dbd-125">Call client methods from hub</span></span>

<span data-ttu-id="87dbd-126">`hubConnection.on`ハブが呼び出すことができるクライアント上のメソッドを定義するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="87dbd-127">ビルドの後、接続を開始する前に、メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="87dbd-128">ログ記録の追加</span><span class="sxs-lookup"><span data-stu-id="87dbd-128">Add logging</span></span>

<span data-ttu-id="87dbd-129">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="87dbd-130">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="87dbd-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="87dbd-131">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="87dbd-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="87dbd-132">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="87dbd-133">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="87dbd-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="87dbd-134">Android 開発ノート</span><span class="sxs-lookup"><span data-stu-id="87dbd-134">Android development notes</span></span>

<span data-ttu-id="87dbd-135">クライアント機能の Android SDK 互換性については SignalR 、ターゲット Android SDK バージョンを指定するときに、次の項目を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="87dbd-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="87dbd-136">SignalRJava クライアントは、ANDROID API レベル16以降で実行されます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="87dbd-137">Azure サービスでは SignalR TLS 1.2 が必要であり、SHA-1 ベース [の SignalR ](/azure/azure-signalr/signalr-overview) 暗号スイートはサポートされていないため、azure サービスを介して接続するには Android API レベル20以降が必要になります。</span><span class="sxs-lookup"><span data-stu-id="87dbd-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="87dbd-138">Android では、API レベル20で [SHA-256 (およびそれ以降) の暗号スイートのサポートが追加されました](https://developer.android.com/reference/javax/net/ssl/SSLSocket) 。</span><span class="sxs-lookup"><span data-stu-id="87dbd-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="87dbd-139">ベアラートークン認証を構成する</span><span class="sxs-lookup"><span data-stu-id="87dbd-139">Configure bearer token authentication</span></span>

<span data-ttu-id="87dbd-140">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)に "アクセストークンファクトリ" を提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="87dbd-141">[WithAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="87dbd-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="87dbd-142">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="87dbd-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="87dbd-143">既知の制限事項</span><span class="sxs-lookup"><span data-stu-id="87dbd-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="87dbd-144">JSON プロトコルのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="87dbd-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="87dbd-145">トランスポートフォールバックおよびサーバー送信イベントトランスポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="87dbd-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="87dbd-146">JSON プロトコルのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="87dbd-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="87dbd-147">Websocket トランスポートのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="87dbd-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="87dbd-148">ストリーミングはまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="87dbd-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="87dbd-149">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="87dbd-149">Additional resources</span></span>

* [<span data-ttu-id="87dbd-150">Java API リファレンス</span><span class="sxs-lookup"><span data-stu-id="87dbd-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="87dbd-151">Azure SignalR サービスのサーバーレスドキュメント</span><span class="sxs-lookup"><span data-stu-id="87dbd-151">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

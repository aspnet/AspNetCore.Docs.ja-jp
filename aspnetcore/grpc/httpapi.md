---
title: gRPC から JSON Web API を作成する
author: jamesnk
description: gRPC サービス用の JSON HTTP API を作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 78247a9b775ec8c9a3bc4a58209f09e5b714c07d
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217519"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="75784-103">gRPC から JSON Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="75784-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="75784-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="75784-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="75784-105">gRPC HTTP API は、コミットされた製品ではなく、試験段階のプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="75784-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="75784-106">次の目的があります。</span><span class="sxs-lookup"><span data-stu-id="75784-106">We want to:</span></span>
>
> * <span data-ttu-id="75784-107">gRPC サービス用の JSON Web API を作成するための方法が機能することをテストする。</span><span class="sxs-lookup"><span data-stu-id="75784-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="75784-108">.NET 開発者にとってこの方法が有効かどうかのフィードバックを得る。</span><span class="sxs-lookup"><span data-stu-id="75784-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="75784-109">開発者が望み、生産性が向上するものを構築するために、[フィードバックをお寄せください](https://github.com/grpc/grpc-dotnet/issues/167)。</span><span class="sxs-lookup"><span data-stu-id="75784-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="75784-110">gRPC は、アプリ間で通信するための最新の方法です。</span><span class="sxs-lookup"><span data-stu-id="75784-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="75784-111">gRPC は、HTTP/2、ストリーミング、Protobuf、メッセージ コントラクトを使用して、高パフォーマンスのリアルタイム サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="75784-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="75784-112">gRPC の制限事項の 1 つが、すべてのプラットフォームで使用できるわけではないということです。</span><span class="sxs-lookup"><span data-stu-id="75784-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="75784-113">ブラウザーでは HTTP/2 が完全にサポートされていないため、REST と JSON が、ブラウザー アプリにデータを取り込む主な方法になります。</span><span class="sxs-lookup"><span data-stu-id="75784-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="75784-114">gRPC にはベネフィットがありますが、REST と JSON にも最新のアプリで重要な役割があります。</span><span class="sxs-lookup"><span data-stu-id="75784-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="75784-115">gRPC "\***および** _" JSON Web API を構築すると、アプリ開発に不要なオーバーヘッドが発生します。</span><span class="sxs-lookup"><span data-stu-id="75784-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="75784-116">このドキュメントでは、gRPC サービスを使用して JSON Web API を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="75784-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="75784-117">gRPC HTTP API</span><span class="sxs-lookup"><span data-stu-id="75784-117">gRPC HTTP API</span></span>

<span data-ttu-id="75784-118">gRPC HTTP API は、gRPC サービス用の RESTful JSON API を作成する ASP.NET Core 向けの試験段階の拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="75784-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="75784-119">構成が完了すると、gRPC HTTP API により、アプリが、使い慣れた HTTP の次のような概念を使用して gRPC サービスを呼び出せるようになります。</span><span class="sxs-lookup"><span data-stu-id="75784-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="75784-120">_ HTTP 動詞</span><span class="sxs-lookup"><span data-stu-id="75784-120">_ HTTP verbs</span></span>
* <span data-ttu-id="75784-121">URL パラメーター バインディング</span><span class="sxs-lookup"><span data-stu-id="75784-121">URL parameter binding</span></span>
* <span data-ttu-id="75784-122">JSON の要求/応答</span><span class="sxs-lookup"><span data-stu-id="75784-122">JSON requests/responses</span></span>

<span data-ttu-id="75784-123">サービスの呼び出しには、gRPC も引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="75784-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="75784-124">使用法</span><span class="sxs-lookup"><span data-stu-id="75784-124">Usage</span></span>

1. <span data-ttu-id="75784-125">[Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="75784-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="75784-126">`AddGrpcHttpApi` で *Startup.cs* にサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="75784-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="75784-127">[google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) と [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) のファイルをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="75784-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="75784-128">HTTP のバインディングとルートを使用して、 *.proto* ファイル内の gRPC メソッドに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="75784-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

[!code-protobuf[](~/grpc/httpapi/greet.proto?highlight=3,9-11)]

<span data-ttu-id="75784-129">`SayHello` gRPC メソッドを gRPC+Protobuf として、そして HTTP API として呼び出すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="75784-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="75784-130">要求: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="75784-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="75784-131">応答: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="75784-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="75784-132">サーバー ログには、gRPC サービスによって HTTP 呼び出しが実行されることが示されます。</span><span class="sxs-lookup"><span data-stu-id="75784-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="75784-133">gRPC HTTP API は、受信 HTTP 要求を gRPC メッセージにマップし、応答メッセージを JSON に変換します。</span><span class="sxs-lookup"><span data-stu-id="75784-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="75784-134">これが基本的な例です。</span><span class="sxs-lookup"><span data-stu-id="75784-134">This is a basic example.</span></span> <span data-ttu-id="75784-135">よりカスタマイズしたオプションについては、「[HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75784-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="enable-swaggeropenapi-support"></a><span data-ttu-id="75784-136">Swagger/OpenAPI のサポートを有効にする</span><span class="sxs-lookup"><span data-stu-id="75784-136">Enable Swagger/OpenAPI support</span></span>

<span data-ttu-id="75784-137">Swagger (OpenAPI) は REST API を記述するための仕様であり、言語に依存しません。</span><span class="sxs-lookup"><span data-stu-id="75784-137">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="75784-138">gRPC HTTP API は、RESTful gRPC サービスの Swagger エンドポイントを生成するために、[Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) と統合することができます。</span><span class="sxs-lookup"><span data-stu-id="75784-138">gRPC HTTP API can integrate with [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) to generate a Swagger endpoint for RESTful gRPC services.</span></span> <span data-ttu-id="75784-139">その後、Swagger エンドポイントを [Swagger UI](https://swagger.io/swagger-ui/) やその他のツールで使用できます。</span><span class="sxs-lookup"><span data-stu-id="75784-139">The Swagger endpoint can then be used with [Swagger UI](https://swagger.io/swagger-ui/) and other tooling.</span></span>

<span data-ttu-id="75784-140">gRPC HTTP API で Swagger を有効にするには:</span><span class="sxs-lookup"><span data-stu-id="75784-140">To enable Swagger with gRPC HTTP API:</span></span>

1. <span data-ttu-id="75784-141">[Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="75784-141">Add a package reference to [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span></span>
2. <span data-ttu-id="75784-142">*Startup.cs* で Swashbuckle を構成します。</span><span class="sxs-lookup"><span data-stu-id="75784-142">Configure Swashbuckle in *Startup.cs*.</span></span> <span data-ttu-id="75784-143">`AddGrpcSwagger` メソッドにより、gRPC HTTP API エンドポイントを含めるように Swashbuckle が構成されます。</span><span class="sxs-lookup"><span data-stu-id="75784-143">The `AddGrpcSwagger` method configures Swashbuckle to include gRPC HTTP API endpoints.</span></span>

[!code-csharp[](~/grpc/httpapi/Startup.cs?name=snippet_1&highlight=6-10,15-19)]

<span data-ttu-id="75784-144">Swashbuckle によって RESTful gRPC サービスの Swagger が生成されることを確認するには、アプリを起動して Swagger UI ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="75784-144">To confirm that Swashbuckle is generating Swagger for the RESTful gRPC services, start the app and navigate to the Swagger UI page:</span></span>

![Swagger UI](~/grpc/httpapi/static/swaggerui.png)

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="75784-146">gRPC HTTP API と gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="75784-146">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="75784-147">gRPC HTTP API と gRPC-Web の両方で、gRPC サービスをブラウザーから呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="75784-147">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="75784-148">ただし、それぞれの動作は次のように異なります。</span><span class="sxs-lookup"><span data-stu-id="75784-148">However, the way each does this is different:</span></span>

* <span data-ttu-id="75784-149">gRPC-Web では、ブラウザー アプリが gRPC-Web クライアントと Protobuf を使用してブラウザーから gRPC サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="75784-149">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="75784-150">gRPC-Web では、ブラウザー アプリで gRPC クライアントを生成する必要があります。また、小さい Protobuf メッセージをすばやく送信できるという利点があります。</span><span class="sxs-lookup"><span data-stu-id="75784-150">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="75784-151">gRPC HTTP API を使用すると、JSON と RESTful API のように、ブラウザー アプリで gRPC サービスを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="75784-151">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="75784-152">ブラウザー アプリで gRPC クライアントを生成する必要はありません。gRPC について知る必要もありません。</span><span class="sxs-lookup"><span data-stu-id="75784-152">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="75784-153">生成されたクライアントは gRPC HTTP API 用には作成されません。</span><span class="sxs-lookup"><span data-stu-id="75784-153">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="75784-154">前の `Greeter` サービスは、ブラウザーの JavaScript API を使用して呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="75784-154">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="75784-155">試験段階の状態</span><span class="sxs-lookup"><span data-stu-id="75784-155">Experimental status</span></span>

<span data-ttu-id="75784-156">gRPC HTTP API は試験段階です。</span><span class="sxs-lookup"><span data-stu-id="75784-156">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="75784-157">完全ではなく、サポートもされていません。</span><span class="sxs-lookup"><span data-stu-id="75784-157">It is not complete and it is not supported.</span></span> <span data-ttu-id="75784-158">私たちは、このテクノロジと、アプリ開発者が gRPC と JSON サービスを同時に作成できるようになる機能に関心を持っています。</span><span class="sxs-lookup"><span data-stu-id="75784-158">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="75784-159">gRPC HTTP API を完成させることは確約していません。</span><span class="sxs-lookup"><span data-stu-id="75784-159">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="75784-160">私たちは gRPC HTTP API に対する開発者の関心を測りたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="75784-160">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="75784-161">gRPC HTTP API に興味がある場合は、[フィードバックをお寄せください](https://github.com/grpc/grpc-dotnet/issues/167)。</span><span class="sxs-lookup"><span data-stu-id="75784-161">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="75784-162">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="75784-162">grpc-gateway</span></span>

<span data-ttu-id="75784-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) は、gRPC サービスから RESTful JSON API を作成するための別のテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="75784-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="75784-164">同じ *.proto* の注釈を使用して、HTTP の概念を gRPC サービスにマップします。</span><span class="sxs-lookup"><span data-stu-id="75784-164">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="75784-165">grpc-gateway と gRPC HTTP API の最大の違いは、grpc-gateway ではコード生成を使用してリバースプロキシ サーバーが作成されることです。</span><span class="sxs-lookup"><span data-stu-id="75784-165">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="75784-166">リバースプロキシでは、RESTful 呼び出しが gRPC に変換され、gRPC サービスに送信されます。</span><span class="sxs-lookup"><span data-stu-id="75784-166">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="75784-167">grpc-gateway のインストールと使用については、[grpc-gateway の README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75784-167">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75784-168">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="75784-168">Additional resources</span></span>

* [<span data-ttu-id="75784-169">google.api.HttpRule のドキュメント</span><span class="sxs-lookup"><span data-stu-id="75784-169">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>

---
title: .NET Core の gRPC の概要
author: juntaoluo
description: Kestrel サーバーと ASP.NET Core の gRPC サービスについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
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
uid: grpc/index
ms.openlocfilehash: 80f44e3845cc1e3c87d5d657807a318eb65e6c6f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059898"
---
# <a name="introduction-to-grpc-on-net-core"></a>.NET Core の gRPC の概要

作成者: [John Luo](https://github.com/juntaoluo)、[James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) は言語に依存しない高性能なリモート プロシージャ コール (RPC) フレームワークです。

gRPC の主な利点:
* 最新の高性能軽量 RPC フレームワーク。
* 既定でプロトコル バッファーを使用する契約優先の API 開発。言語に依存しない実装を可能にします。
* 厳密に型指定されたサーバーとクライアントを生成する目的で、さまざまな言語で利用できるツール。
* クライアント、サーバー、双方向ストリーミング呼び出しをサポートします。
* Protobuf バイナリ シリアル化でネットワークの使用率を減らします。

以上の利点から gRPC は以下に最適です。
* 効率性が重要となる軽量のマイクロサービス。
* 開発に複数の言語が必要になる多言語システム。
* ストリーミングの要求または応答を処理する必要があるポイントツーポイントのリアルタイム サービス。

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a>.proto ファイルに対する C# ツール サポート

gRPC では、API 開発に対してコントラクト優先のアプローチが使われます。 サービスとメッセージは、 *\*.proto* ファイル内で定義されます。

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

サービス、クライアント、およびメッセージの .NET 型は、プロジェクトに *\*.proto* ファイルを含めることで自動的に生成されます。

* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) パッケージにパッケージ参照を追加します。
* `<Protobuf>` 項目グループに *\*.proto* ファイルを追加します。

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

gRPC ツール サポートについて詳しくは、「<xref:grpc/basics>」をご覧ください。

## <a name="grpc-services-on-aspnet-core"></a>ASP.NET Core での gRPC サービス

gRPC サービスは ASP.NET Core でホストできます。 サービスは、ログ記録、依存関係の注入 (DI)、認証、承認などの一般的な ASP.NET Core 機能と完全に統合されています。

gRPC サービスのプロジェクト テンプレートには、スターター サービスが用意されています。

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService` は `GreeterBase` 型を継承します。これは *\*.proto* ファイル内の `Greeter` サービスから生成されます。 サービスは、 *Startup.cs* 内でクライアントがアクセスできるようになります。

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

ASP.NET Core での gRPC サービスについて詳しくは、「<xref:grpc/aspnetcore>」をご覧ください。

## <a name="call-grpc-services-with-a-net-client"></a>.NET クライアントを使用して gRPC サービスを呼び出す

gRPC クライアントは、 [ *\*.proto* ファイルから生成される](xref:grpc/basics#generated-c-assets)具象的なクライアントの種類です。 具象 gRPC クライアントには、 *\*.proto* ファイル内の gRPC サービスに変換するためのメソッドが含まれます。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

gRPC クライアントはチャネルを使って作成され、これは gRPC サービスへの長期接続を表します。 チャネルは `GrpcChannel.ForAddress` を使って作成できます。

クライアントの作成と、さまざまなサービス メソッドの呼び出しについて詳しくは、「<xref:grpc/client>」をご覧ください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>

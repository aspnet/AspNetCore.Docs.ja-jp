---
title: C# を使用した gRPC サービス
author: juntaoluo
description: GRPC サービスを記述する場合は、基本的な概念を説明しますC#します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: ce2682848dc6a81293545c27f0be779e12a3a600
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "59515634"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="cf470-103">C と gRPC サービス\#</span><span class="sxs-lookup"><span data-stu-id="cf470-103">gRPC services with C\#</span></span>

<span data-ttu-id="cf470-104">このドキュメントでは、書き込みに必要な概念を示しています。 [gRPC](https://grpc.io/docs/guides/)アプリC#します。</span><span class="sxs-lookup"><span data-stu-id="cf470-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="cf470-105">ここで取り上げるトピックは、両方に適用[C core](https://grpc.io/blog/grpc-stacks)-gRPC のベースと ASP.NET Core ベースのアプリ。</span><span class="sxs-lookup"><span data-stu-id="cf470-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="cf470-106">proto ファイル</span><span class="sxs-lookup"><span data-stu-id="cf470-106">proto file</span></span>

<span data-ttu-id="cf470-107">gRPC は、API の開発をコントラクト優先のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf470-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="cf470-108">プロトコル バッファー (protobuf) は、既定ではインターフェイスのデザイン言語 (IDL) としてを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf470-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="cf470-109">*.Proto*ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="cf470-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="cf470-110">GRPC サービスの定義。</span><span class="sxs-lookup"><span data-stu-id="cf470-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="cf470-111">クライアントとサーバー間で送信されるメッセージ。</span><span class="sxs-lookup"><span data-stu-id="cf470-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="cf470-112">Protobuf ファイルの構文の詳細については、次を参照してください。、[公式ドキュメント (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)します。</span><span class="sxs-lookup"><span data-stu-id="cf470-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="cf470-113">たとえば、 *greet.proto*ファイルで使用される[gRPC service の概要](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="cf470-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="cf470-114">定義、`Greeter`サービス。</span><span class="sxs-lookup"><span data-stu-id="cf470-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="cf470-115">`Greeter`サービスを定義、`SayHello`呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf470-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="cf470-116">`SayHello` 送信、`HelloRequest`メッセージの送受信、`HelloResponse`メッセージ。</span><span class="sxs-lookup"><span data-stu-id="cf470-116">`SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:</span></span>

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcStart/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="cf470-117">C に .proto ファイルを追加\#アプリ</span><span class="sxs-lookup"><span data-stu-id="cf470-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="cf470-118">*.Proto*プロジェクトに追加することによってファイルが含まれて、`<Protobuf>`項目グループ。</span><span class="sxs-lookup"><span data-stu-id="cf470-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="cf470-119">C#.Proto ファイルのツールのサポート</span><span class="sxs-lookup"><span data-stu-id="cf470-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="cf470-120">ツール パッケージ[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)を生成するために必要なC#から資産 *.proto*ファイル。</span><span class="sxs-lookup"><span data-stu-id="cf470-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="cf470-121">生成された資産 (ファイル):</span><span class="sxs-lookup"><span data-stu-id="cf470-121">The generated assets (files):</span></span>

* <span data-ttu-id="cf470-122">ときに生成されます - 必要な場合に各プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="cf470-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="cf470-123">プロジェクトに追加またはソース管理にチェックインされません。</span><span class="sxs-lookup"><span data-stu-id="cf470-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="cf470-124">ビルドの成果物に含まれているは、 *obj*ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="cf470-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="cf470-125">このパッケージは、サーバーとクライアントの両方のプロジェクトで必要です。</span><span class="sxs-lookup"><span data-stu-id="cf470-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="cf470-126">`Grpc.Tools` Visual Studio でパッケージ マネージャーを使用または追加して追加することができます、`<PackageReference>`プロジェクト ファイル。</span><span class="sxs-lookup"><span data-stu-id="cf470-126">`Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=1&range=16)]

<span data-ttu-id="cf470-127">ツール パッケージがないと依存関係がマークされているため、実行時に必要な`PrivateAssets="All"`します。</span><span class="sxs-lookup"><span data-stu-id="cf470-127">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

## <a name="generated-c-assets"></a><span data-ttu-id="cf470-128">生成されたC#資産</span><span class="sxs-lookup"><span data-stu-id="cf470-128">Generated C# assets</span></span>

<span data-ttu-id="cf470-129">ツール パッケージを生成、C#型が含まれるで定義されているメッセージを表す *.proto*ファイル。</span><span class="sxs-lookup"><span data-stu-id="cf470-129">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="cf470-130">サーバー側の資産に対して、サービスの抽象基本型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cf470-130">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="cf470-131">基本データ型には、gRPC の呼び出しに含まれるすべての定義が含まれています、 *.proto*ファイル。</span><span class="sxs-lookup"><span data-stu-id="cf470-131">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="cf470-132">この基本型から派生し、gRPC の呼び出しのロジックを実装するサービスの具象実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="cf470-132">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="cf470-133">`greet.proto`、抽象前に説明した例では、`GreeterBase`バーチャル マシンを含む型`SayHello`メソッドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cf470-133">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="cf470-134">具象実装`GreeterService`メソッドをオーバーライドし、gRPC の呼び出しを処理するロジックを実装します。</span><span class="sxs-lookup"><span data-stu-id="cf470-134">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="cf470-135">クライアント側の資産に対してクライアントを具象型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cf470-135">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="cf470-136">呼び出し、gRPC、 *.proto*ファイルは、メソッドを呼び出すことができる、具象型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="cf470-136">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="cf470-137">`greet.proto`、先ほどを具体的に説明した例では、`GreeterClient`型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cf470-137">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="cf470-138">呼び出す`GreeterClient.SayHello`gRPC の呼び出し、サーバーを開始します。</span><span class="sxs-lookup"><span data-stu-id="cf470-138">Call `GreeterClient.SayHello` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Client/Program.cs?highlight=9-11&name=snippet)]

<span data-ttu-id="cf470-139">既定では、サーバーとクライアントの資産を生成して、各 *.proto*ファイルに含まれる、`<Protobuf>`項目グループ。</span><span class="sxs-lookup"><span data-stu-id="cf470-139">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="cf470-140">サーバー資産のみがサーバー プロジェクトで生成されたことを確認する、`GrpcServices`属性に設定されて`Server`します。</span><span class="sxs-lookup"><span data-stu-id="cf470-140">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

<span data-ttu-id="cf470-141">同様に、属性に設定されて`Client`クライアント プロジェクトでします。</span><span class="sxs-lookup"><span data-stu-id="cf470-141">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf470-142">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cf470-142">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
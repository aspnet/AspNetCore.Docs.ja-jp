---
title: ASP.NET Core でのファイル プロバイダー
author: rick-anderson
description: ASP.NET Core がファイル プロバイダーを使用してファイル システムへのアクセスを抽象化する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
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
uid: fundamentals/file-providers
ms.openlocfilehash: 16e5ead9898125c804da4d60322510474201d897
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059443"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="d9dd7-103">ASP.NET Core でのファイル プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d9dd7-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="d9dd7-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d9dd7-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d9dd7-105">ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="d9dd7-106">ファイル プロバイダーは、ASP.NET Core フレームワーク全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="d9dd7-107">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-107">For example:</span></span>

* <span data-ttu-id="d9dd7-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> では、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)と [Web ルート](xref:fundamentals/index#web-root)が `IFileProvider` 型として公開されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="d9dd7-109">[静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="d9dd7-110">[:::no-loc(Razor):::](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-110">[:::no-loc(Razor):::](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="d9dd7-111">.NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="d9dd7-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="d9dd7-113">ファイル プロバイダーのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="d9dd7-113">File Provider interfaces</span></span>

<span data-ttu-id="d9dd7-114">プライマリ インターフェイスは <xref:Microsoft.Extensions.FileProviders.IFileProvider> です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="d9dd7-115">`IFileProvider` では次のためのメソッドが公開されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="d9dd7-116">ファイルの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="d9dd7-117">ディレクトリの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="d9dd7-118">変更通知を設定します (<xref:Microsoft.Extensions.Primitives.IChangeToken> を使用)。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="d9dd7-119">`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="d9dd7-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (バイト単位)</span><span class="sxs-lookup"><span data-stu-id="d9dd7-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="d9dd7-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> の日付</span><span class="sxs-lookup"><span data-stu-id="d9dd7-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="d9dd7-122"><xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> メソッドを使用して、ファイルから情報を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="d9dd7-123">*FileProviderSample* サンプル アプリでは、 [依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="d9dd7-124">ファイル プロバイダーの実装</span><span class="sxs-lookup"><span data-stu-id="d9dd7-124">File Provider implementations</span></span>

<span data-ttu-id="d9dd7-125">次の表に、`IFileProvider` の実装の一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="d9dd7-126">実装</span><span class="sxs-lookup"><span data-stu-id="d9dd7-126">Implementation</span></span> | <span data-ttu-id="d9dd7-127">説明</span><span class="sxs-lookup"><span data-stu-id="d9dd7-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="d9dd7-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="d9dd7-129">その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリへのアクセスを結合するために使用します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="d9dd7-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="d9dd7-131">アセンブリに埋め込まれているファイルにアクセスする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="d9dd7-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="d9dd7-133">システムの物理ファイルにアクセスするために使用します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="d9dd7-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-134">PhysicalFileProvider</span></span>

<span data-ttu-id="d9dd7-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> は、物理ファイル システムへのアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="d9dd7-136">`PhysicalFileProvider` では、<xref:System.IO.File?displayProperty=fullName> 型が使用され (物理プロバイダーの場合)、すべてのパスのスコープが 1 つのディレクトリとその子ディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="d9dd7-137">このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="d9dd7-138">`PhysicalFileProvider` を作成して使用する最も一般的なシナリオは、[依存関係の挿入](xref:fundamentals/dependency-injection)を通してコンストラクターで `IFileProvider` を要求する場合です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d9dd7-139">このプロバイダーを直接インスタンス化するときは、絶対ディレクトリ パスが要求され、そのプロバイダーを使用して行われるすべての要求のベース パスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="d9dd7-140">glob パターンはディレクトリ パスではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="d9dd7-141">次のコードは、`PhysicalFileProvider` を使用してディレクトリの内容とファイルの情報を取得する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="d9dd7-142">前の例の型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-142">Types in the preceding example:</span></span>

* <span data-ttu-id="d9dd7-143">`provider` は `IFileProvider` です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="d9dd7-144">`contents` は `IDirectoryContents` です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="d9dd7-145">`fileInfo` は `IFileInfo` です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="d9dd7-146">ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="d9dd7-147">glob パターンを `GetFileInfo` メソッドに渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="d9dd7-148">ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="d9dd7-149">*FileProviderSample* サンプル アプリでは、<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> を使用して `Startup.ConfigureServices` メソッドにプロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="d9dd7-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="d9dd7-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> は、アセンブリに埋め込まれたファイルにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="d9dd7-152">`ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="d9dd7-153">埋め込みファイルのマニフェストを生成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="d9dd7-154">[Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="d9dd7-155">`<GenerateEmbeddedFilesManifest>` プロパティを `true`に設定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="d9dd7-156">[\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して列挙するファイルを指定します:</span><span class="sxs-lookup"><span data-stu-id="d9dd7-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="d9dd7-157">[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="d9dd7-158">*FileProviderSample* サンプル アプリでは `ManifestEmbeddedFileProvider` が作成され、現在実行しているアセンブリがそのコンストラクターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="d9dd7-159">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d9dd7-159">*Startup.cs* :</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="d9dd7-160">追加のオーバーロードを使用すると、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="d9dd7-161">相対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-161">Specify a relative file path.</span></span>
* <span data-ttu-id="d9dd7-162">ファイルのスコープを最終変更日に設定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="d9dd7-163">埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="d9dd7-164">オーバーロード</span><span class="sxs-lookup"><span data-stu-id="d9dd7-164">Overload</span></span> | <span data-ttu-id="d9dd7-165">説明</span><span class="sxs-lookup"><span data-stu-id="d9dd7-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="d9dd7-166">必要に応じて相対パスのパラメーター `root` を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="d9dd7-167">`root` を指定して、<xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> の呼び出しのスコープを指定したパス以下のリソースに設定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="d9dd7-168">必要に応じて、相対パス パラメーター `root` および日付パラメーター `lastModified` (<xref:System.DateTimeOffset>) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="d9dd7-169">`lastModified` の日付では、<xref:Microsoft.Extensions.FileProviders.IFileProvider> によって返される <xref:Microsoft.Extensions.FileProviders.IFileInfo> インスタンスの最終更新日のスコープを設定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="d9dd7-170">必要に応じて、相対パス `root`、日付 `lastModified`、`manifestName` パラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="d9dd7-171">`manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="d9dd7-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-172">CompositeFileProvider</span></span>

<span data-ttu-id="d9dd7-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="d9dd7-174">`CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="d9dd7-175">*FileProviderSample* サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` により、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="d9dd7-176">次のコードは、プロジェクトの `Startup.ConfigureServices` メソッドにあります。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="d9dd7-177">変更の監視</span><span class="sxs-lookup"><span data-stu-id="d9dd7-177">Watch for changes</span></span>

<span data-ttu-id="d9dd7-178"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> メソッドでは、変更がないかどうか 1 つ以上のファイルまたはディレクトリを監視するシナリオを提供します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="d9dd7-179">`Watch` メソッド:</span><span class="sxs-lookup"><span data-stu-id="d9dd7-179">The `Watch` method:</span></span>

* <span data-ttu-id="d9dd7-180">ファイル パス文字列を指定できます。これにより、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="d9dd7-181"><xref:Microsoft.Extensions.Primitives.IChangeToken> を返します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="d9dd7-182">生成される変更トークンでは次のものが公開されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="d9dd7-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>:このプロパティを調べることで、変更があったかどうかを判断できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="d9dd7-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>:指定したパス文字列に対して変更が検出されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="d9dd7-185">各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="d9dd7-186">定数の監視を有効にするには、<xref:System.Threading.Tasks.TaskCompletionSource`1> を使用するか (以下を参照)、変更への応答として `IChangeToken` インスタンスを再作成します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="d9dd7-187">*WatchConsole* サンプル アプリでは、 *TextFiles* ディレクトリの *.txt* ファイルが変更されるたびに、メッセージが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="d9dd7-188">Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="d9dd7-189">`DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="d9dd7-190">glob パターン</span><span class="sxs-lookup"><span data-stu-id="d9dd7-190">Glob patterns</span></span>

<span data-ttu-id="d9dd7-191">ファイル システム パスは、" *glob (または globbing) パターン* " と呼ばれるワイルドカード パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="d9dd7-192">これらのパターンを使用して、ファイルのグループを指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="d9dd7-193">2 つのワイルドカード文字は、`*` と `**` です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="d9dd7-194">現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="d9dd7-195">照合はファイル パス内の `/` 文字および `.` 文字によって終了します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="d9dd7-196">複数のディレクトリ レベルにわたって任意の要素を照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="d9dd7-197">ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="d9dd7-198">次の表は、glob パターンの一般的な例を示しています。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="d9dd7-199">パターン</span><span class="sxs-lookup"><span data-stu-id="d9dd7-199">Pattern</span></span>  |<span data-ttu-id="d9dd7-200">説明</span><span class="sxs-lookup"><span data-stu-id="d9dd7-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="d9dd7-201">特定のディレクトリ内の特定のファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="d9dd7-202">特定のディレクトリ内の *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/:::no-loc(appsettings.json):::`|<span data-ttu-id="d9dd7-203">*directory* フォルダーのちょうど 1 つ下のレベルにあるディレクトリ内のすべての *:::no-loc(appsettings.json):::* ファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-203">Matches all *:::no-loc(appsettings.json):::* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="d9dd7-204">*directory* フォルダーの下の任意の場所にある、 *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d9dd7-205">ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="d9dd7-206">ファイル プロバイダーは、ASP.NET Core フレームワークの全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="d9dd7-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> では、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)と [Web ルート](xref:fundamentals/index#web-root)が `IFileProvider` 型として公開されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="d9dd7-208">[静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="d9dd7-209">[:::no-loc(Razor):::](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-209">[:::no-loc(Razor):::](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="d9dd7-210">.NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="d9dd7-211">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="d9dd7-212">ファイル プロバイダーのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="d9dd7-212">File Provider interfaces</span></span>

<span data-ttu-id="d9dd7-213">プライマリ インターフェイスは <xref:Microsoft.Extensions.FileProviders.IFileProvider> です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="d9dd7-214">`IFileProvider` では次のためのメソッドが公開されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="d9dd7-215">ファイルの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="d9dd7-216">ディレクトリの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="d9dd7-217">変更通知を設定します (<xref:Microsoft.Extensions.Primitives.IChangeToken> を使用)。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="d9dd7-218">`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="d9dd7-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (バイト単位)</span><span class="sxs-lookup"><span data-stu-id="d9dd7-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="d9dd7-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> の日付</span><span class="sxs-lookup"><span data-stu-id="d9dd7-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="d9dd7-221">[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) メソッドを使用してファイルから読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="d9dd7-222">サンプル アプリでは、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="d9dd7-223">ファイル プロバイダーの実装</span><span class="sxs-lookup"><span data-stu-id="d9dd7-223">File Provider implementations</span></span>

<span data-ttu-id="d9dd7-224">利用できる `IFileProvider` の実装は 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="d9dd7-225">実装</span><span class="sxs-lookup"><span data-stu-id="d9dd7-225">Implementation</span></span> | <span data-ttu-id="d9dd7-226">説明</span><span class="sxs-lookup"><span data-stu-id="d9dd7-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="d9dd7-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="d9dd7-228">システムの物理ファイルにアクセスするために、物理プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="d9dd7-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="d9dd7-230">アセンブリに埋め込まれているファイルにアクセスするために、マニフェストが埋め込まれたプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="d9dd7-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="d9dd7-232">コンポジット プロパイダーは、その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリに対するアクセスを結合する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="d9dd7-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-233">PhysicalFileProvider</span></span>

<span data-ttu-id="d9dd7-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> は、物理ファイル システムへのアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="d9dd7-235">`PhysicalFileProvider` では、<xref:System.IO.File?displayProperty=fullName> 型が使用され (物理プロバイダーの場合)、すべてのパスのスコープが 1 つのディレクトリとその子ディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="d9dd7-236">このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="d9dd7-237">`PhysicalFileProvider` を作成して使用する最も一般的なシナリオは、[依存関係の挿入](xref:fundamentals/dependency-injection)を通してコンストラクターで `IFileProvider` を要求する場合です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d9dd7-238">このプロバイダーを直接インスタンス化するときは、ディレクトリ パスが要求され、そのプロバイダーを使用して行われるすべての要求のベース パスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="d9dd7-239">次のコードでは、`PhysicalFileProvider` の作成方法と、これを使ってディレクトリのコンテンツとファイル情報を取得する方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="d9dd7-240">前の例の型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-240">Types in the preceding example:</span></span>

* <span data-ttu-id="d9dd7-241">`provider` は `IFileProvider` です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="d9dd7-242">`contents` は `IDirectoryContents` です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="d9dd7-243">`fileInfo` は `IFileInfo` です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="d9dd7-244">ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="d9dd7-245">ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="d9dd7-246">サンプル アプリの `Startup.ConfigureServices` クラスでは、[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) を使用してプロバイダーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="d9dd7-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="d9dd7-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> は、アセンブリに埋め込まれたファイルにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="d9dd7-249">`ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="d9dd7-250">埋め込みファイルのマニフェストを生成するには、`<GenerateEmbeddedFilesManifest>` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="d9dd7-251">[&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して埋め込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="d9dd7-252">[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="d9dd7-253">サンプル アプリでは `ManifestEmbeddedFileProvider` を作成して、現在実行しているアセンブリをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="d9dd7-254">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d9dd7-254">*Startup.cs* :</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="d9dd7-255">追加のオーバーロードを使用すると、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="d9dd7-256">相対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-256">Specify a relative file path.</span></span>
* <span data-ttu-id="d9dd7-257">ファイルのスコープを最終変更日に設定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="d9dd7-258">埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="d9dd7-259">オーバーロード</span><span class="sxs-lookup"><span data-stu-id="d9dd7-259">Overload</span></span> | <span data-ttu-id="d9dd7-260">説明</span><span class="sxs-lookup"><span data-stu-id="d9dd7-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="d9dd7-261">必要に応じて相対パスのパラメーター `root` を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="d9dd7-262">`root` を指定して、<xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> の呼び出しのスコープを指定したパス以下のリソースに設定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="d9dd7-263">必要に応じて、相対パス パラメーター `root` および日付パラメーター `lastModified` (<xref:System.DateTimeOffset>) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="d9dd7-264">`lastModified` の日付では、<xref:Microsoft.Extensions.FileProviders.IFileProvider> によって返される <xref:Microsoft.Extensions.FileProviders.IFileInfo> インスタンスの最終更新日のスコープを設定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="d9dd7-265">必要に応じて、相対パス `root`、日付 `lastModified`、`manifestName` パラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="d9dd7-266">`manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="d9dd7-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d9dd7-267">CompositeFileProvider</span></span>

<span data-ttu-id="d9dd7-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="d9dd7-269">`CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="d9dd7-270">サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` が、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="d9dd7-271">変更の監視</span><span class="sxs-lookup"><span data-stu-id="d9dd7-271">Watch for changes</span></span>

<span data-ttu-id="d9dd7-272">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) メソッドによって、1 つまたは複数のファイルやディレクトリに変更がないかどうか監視するシナリオが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="d9dd7-273">`Watch` にはパス文字列を指定できます。ここでは、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="d9dd7-274">`Watch` では <xref:Microsoft.Extensions.Primitives.IChangeToken> が返されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="d9dd7-275">変更トークンでは次のものが公開されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-275">The change token exposes:</span></span>

* <span data-ttu-id="d9dd7-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>:このプロパティを調べることで、変更があったかどうかを判断できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="d9dd7-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>:指定したパス文字列に対して変更が検出されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="d9dd7-278">各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="d9dd7-279">定数の監視を有効にするには、<xref:System.Threading.Tasks.TaskCompletionSource`1> を使用するか (以下を参照)、変更への応答として `IChangeToken` インスタンスを再作成します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="d9dd7-280">サンプル アプリでは、 *WatchConsole* コンソール アプリは、テキスト ファイルが変更されるたびにメッセージを表示するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="d9dd7-281">Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="d9dd7-282">`DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="d9dd7-283">glob パターン</span><span class="sxs-lookup"><span data-stu-id="d9dd7-283">Glob patterns</span></span>

<span data-ttu-id="d9dd7-284">ファイル システム パスは、" *glob (または globbing) パターン* " と呼ばれるワイルドカード パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="d9dd7-285">これらのパターンを使用して、ファイルのグループを指定します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="d9dd7-286">2 つのワイルドカード文字は、`*` と `**` です。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="d9dd7-287">現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="d9dd7-288">照合はファイル パス内の `/` 文字および `.` 文字によって終了します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="d9dd7-289">複数のディレクトリ レベルにわたって任意の要素を照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="d9dd7-290">ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="d9dd7-291">**glob パターンの例**</span><span class="sxs-lookup"><span data-stu-id="d9dd7-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="d9dd7-292">特定のディレクトリ内の特定のファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="d9dd7-293">特定のディレクトリ内の *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/:::no-loc(appsettings.json):::`**  
<span data-ttu-id="d9dd7-294">*directory* フォルダーのちょうど 1 つ下のレベルにあるディレクトリ内のすべての `:::no-loc(appsettings.json):::` ファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-294">Matches all `:::no-loc(appsettings.json):::` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="d9dd7-295">*directory* フォルダーの下の任意の場所にある、 *.txt* 拡張子を持つすべてのファイルを照合します。</span><span class="sxs-lookup"><span data-stu-id="d9dd7-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

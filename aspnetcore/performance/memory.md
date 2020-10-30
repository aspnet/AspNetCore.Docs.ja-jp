---
title: ASP.NET Core のメモリ管理とパターン
author: rick-anderson
description: ASP.NET Core でのメモリの管理方法とガベージコレクター (GC) の動作について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: 6d2a89ec7c64728bc585ad235293f2277f9a66f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061484"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>ASP.NET Core のメモリ管理とガベージコレクション (GC)

[Sébastien Ros](https://github.com/sebastienros)と[Rick Anderson](https://twitter.com/RickAndMSFT)

メモリ管理は、.NET などのマネージフレームワークでも複雑です。 メモリの問題を分析して理解することは困難な場合があります。 この記事の内容:

* 多くの *メモリリークが発生* し、GC が動作して *いない* 問題が発生しました。 これらの問題のほとんどは、.NET Core でのメモリ消費のしくみを理解していないか、測定方法を理解していないことによって発生しました。
* 問題のあるメモリ使用方法を示し、別のアプローチを提案します。

## <a name="how-garbage-collection-gc-works-in-net-core"></a>.NET Core でのガベージコレクション (GC) のしくみ

GC は、各セグメントが連続するメモリ範囲であるヒープセグメントを割り当てます。 ヒープに配置されたオブジェクトは、0、1、または2の3つのジェネレーションに分類されます。 生成によって、アプリケーションによって参照されなくなったマネージオブジェクトのメモリを GC が解放する頻度が決まります。 下位の番号付きジェネレーションは、GC の方が頻繁に行われます。

オブジェクトは、有効期間に基づいて、ある世代から別の世代に移動されます。 オブジェクトが長くなると、オブジェクトは上位世代に移動されます。 既に説明したように、より高い世代は GC の方が頻繁に発生します。 短期有効期間オブジェクトは常にジェネレーション0に残ります。 たとえば、web 要求の有効期間中に参照されるオブジェクトは、短時間で終了します。 一般に、アプリケーションレベル [シングルトン](xref:fundamentals/dependency-injection#service-lifetimes) は第2世代に移行します。

ASP.NET Core アプリが開始されると、GC は次のようになります。

* 初期ヒープセグメント用にメモリを予約します。
* ランタイムが読み込まれるときに、メモリの一部をコミットします。

前のメモリ割り当ては、パフォーマンス上の理由から実行されます。 パフォーマンス上の利点は、連続したメモリのヒープセグメントから取得されます。

### <a name="call-gccollect"></a>GC を呼び出します。収集

[GC を呼び出しています。](xref:System.GC.Collect*)明示的に収集:

* 運用 ASP.NET Core アプリでは **実行しないでください。**
* は、メモリリークを調査するときに便利です。
* 調査時に、GC によってすべての未解決のオブジェクトがメモリから削除されたことを確認し、メモリを測定します。

## <a name="analyzing-the-memory-usage-of-an-app"></a>アプリのメモリ使用量の分析

専用ツールは、メモリ使用量の分析に役立ちます。

- カウント (オブジェクト参照を)
- GC が CPU 使用率に与える影響を測定する
- 各世代に使用されるメモリ領域の測定

メモリ使用量を分析するには、次のツールを使用します。

* [dotnet](/dotnet/core/diagnostics/dotnet-trace): 運用コンピューターで使用できます。
* [Visual Studio デバッガーを使用せずにメモリ使用量を分析する](/visualstudio/profiling/memory-usage-without-debugging2)
* [Visual Studio でのメモリ使用のプロファイリング](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>メモリの問題の検出

タスクマネージャーを使用して、ASP.NET アプリが使用しているメモリの量を把握できます。 タスクマネージャーのメモリ値:

* ASP.NET プロセスによって使用されるメモリの量を表します。
* アプリの生きたオブジェクトや、ネイティブメモリ使用量などの他のメモリコンシューマーを含みます。

タスクマネージャーのメモリ値が無制限に増加し、フラット化されない場合、アプリにはメモリリークが発生します。 次のセクションでは、いくつかのメモリ使用パターンについて説明し、説明します。

## <a name="sample-display-memory-usage-app"></a>ディスプレイメモリ使用量アプリのサンプル

[Memoryleak サンプルアプリ](https://github.com/sebastienros/memoryleak)は GitHub で入手できます。 MemoryLeak アプリ:

* には、アプリのリアルタイムメモリおよび GC データを収集する診断コントローラーが含まれています。
* には、メモリおよび GC データを表示するインデックスページがあります。 インデックスページは、1秒ごとに更新されます。
* には、さまざまなメモリ読み込みパターンを提供する API コントローラーが含まれています。
* はサポートされているツールではありませんが、ASP.NET Core アプリのメモリ使用量パターンを表示するために使用できます。

MemoryLeak を実行します。 割り当てられたメモリは、GC が発生するまで徐々に増加します。 データをキャプチャするためのカスタムオブジェクトがツールによって割り当てられるため、メモリが増加します。 次の図は、Gen 0 GC が発生したときの MemoryLeak インデックスページを示しています。 API コントローラーからの API エンドポイントが呼び出されていないため、グラフには0個の RPS (1 秒あたりの要求数) が表示されます。

![前のグラフ](memory/_static/0RPS.png)

グラフには、メモリ使用量の2つの値が表示されます。

- 割り当て済み: マネージオブジェクトによって占有されているメモリの量
- [Working set](/windows/win32/memory/working-set): 現在物理メモリに常駐しているプロセスの仮想アドレス空間にあるページのセット。 表示される作業セットは、タスクマネージャーに表示される値と同じです。

### <a name="transient-objects"></a>一時オブジェクト

次の API は、10 KB の文字列インスタンスを作成し、それをクライアントに返します。 各要求では、新しいオブジェクトがメモリに割り当てられ、応答に書き込まれます。 文字列は .NET で UTF-16 文字として格納されるため、各文字はメモリ内で2バイトを取ります。

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

次のグラフは、の負荷が比較的小さい場合に生成され、メモリ割り当てが GC によってどのように影響されているかを示します。

![前のグラフ](memory/_static/bigstring.png)

前のグラフは次を示しています。

* 4K RPS (1 秒あたりの要求数)。
* ジェネレーション0の GC コレクションは、約2秒ごとに発生します。
* ワーキングセットは約 500 MB に固定されています。
* CPU は12% です。
* メモリ使用量と解放 (GC 経由) が安定しています。

次のグラフは、マシンで処理できる最大スループットで取得されます。

![前のグラフ](memory/_static/bigstring2.png)

前のグラフは次を示しています。

* 22K RPS
* ジェネレーション0の GC コレクションは1秒間に数回発生します。
* ジェネレーション1のコレクションがトリガーされるのは、アプリによって1秒あたりにかなり多くのメモリが割り当てられたためです。
* ワーキングセットは約 500 MB に固定されています。
* CPU は33% です。
* メモリ使用量と解放 (GC 経由) が安定しています。
* CPU (33%)は過剰に使用されていないため、ガベージコレクションは多くの割り当てを保持できます。

### <a name="workstation-gc-vs-server-gc"></a>ワークステーション GC とサーバー GC

.NET ガベージコレクターには、次の2つの異なるモードがあります。

* **WORKSTATION GC** : デスクトップ用に最適化されています。
* **サーバー GC** 。 ASP.NET Core アプリの既定の GC。 サーバーに合わせて最適化されます。

GC モードは、プロジェクトファイルまたは発行されたアプリの *runtimeconfig.js* ファイルで明示的に設定できます。 次のマークアップは、プロジェクトファイルの設定を示してい `ServerGarbageCollection` ます。

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

プロジェクトファイルでを変更するに `ServerGarbageCollection` は、アプリを再構築する必要があります。

**注:** サーバーのガベージコレクションは、コアが1つのマシンでは使用でき **ません** 。 詳細については、「<xref:System.Runtime.GCSettings.IsServerGC>」を参照してください。

次の図は、ワークステーション GC を使用した 5K RPS のメモリプロファイルを示しています。

![前のグラフ](memory/_static/workstation.png)

このグラフとサーバーのバージョンの違いは、次のとおりです。

- ワーキングセットは 500 MB から 70 MB になります。
- GC は、2秒ごとではなく、1秒あたり複数回ジェネレーション0のコレクションを行います。
- GC は 300 MB から 10 MB に減少します。

一般的な web サーバー環境では、CPU 使用率はメモリよりも重要であるため、サーバー GC の方が適しています。 メモリ使用率が高く、CPU 使用率が比較的低い場合、ワークステーションの GC のパフォーマンスが向上する可能性があります。 たとえば、メモリが不足している複数の web アプリをホストしている高密度です。

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>ドッカーと小さなコンテナを使用した GC

1台のコンピューターで複数のコンテナー化されたアプリが実行されている場合、ワークステーションの GC はサーバー GC よりも preformant になる可能性があります。 詳細については、「 [小さなコンテナーでのサーバー gc の実行](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) 」と「 [小さなコンテナーシナリオでサーバー gc を使用して実行する (パート 1-Gc ヒープのハードリミット)](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)」を参照してください。

### <a name="persistent-object-references"></a>永続的なオブジェクト参照

GC は参照されているオブジェクトを解放できません。 参照されていて、不要になったオブジェクトは、メモリリークを発生させます。 アプリがオブジェクトを頻繁に割り当てて、不要になった後にオブジェクトを解放できない場合は、メモリ使用量が時間の経過と共に増加します。

次の API は、10 KB の文字列インスタンスを作成し、それをクライアントに返します。 前の例との違いは、このインスタンスが静的メンバーによって参照されていることです。これは、コレクションでは使用できないことを意味します。

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

上記のコードでは次の操作が行われます。

* は、一般的なメモリリークの例です。
* 頻繁な呼び出しでは、例外によりプロセスがクラッシュするまで、アプリのメモリが増加し `OutOfMemory` ます。

![前のグラフ](memory/_static/eternal.png)

前の図では、次のようになります。

* エンドポイントをロードテストする `/api/staticstring` と、メモリが直線的に増加します。
* GC は、ジェネレーション2のコレクションを呼び出すことによって、メモリの負荷が増加したときにメモリの解放を試みます。
* GC では、リークしたメモリを解放できません。 割り当てられたワーキングセットは時間と共に増加します。

キャッシュなどの一部のシナリオでは、メモリ不足によってオブジェクト参照が強制的に解放されるまで、オブジェクト参照を保持する必要があります。 クラスは、 <xref:System.WeakReference> この種のキャッシュコードに使用できます。 `WeakReference`オブジェクトはメモリ負荷の下で収集されます。 の既定の実装では、が <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 使用さ `WeakReference` れます。

### <a name="native-memory"></a>ネイティブメモリ

.NET Core オブジェクトの中には、ネイティブメモリに依存しているものがあります。 GC でネイティブメモリを収集することはでき **ません** 。 ネイティブメモリを使用している .NET オブジェクトは、ネイティブコードを使用して解放する必要があります。

.NET には、 <xref:System.IDisposable> 開発者がネイティブメモリを解放できるようにするインターフェイスが用意されています。 <xref:System.IDisposable.Dispose*>が呼び出されていない場合でも、 `Dispose` [ファイナライザー](/dotnet/csharp/programming-guide/classes-and-structs/destructors)の実行時に、正しく実装されたクラスが呼び出されます。

次のコードがあるとします。

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[Physicalfileprovider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) はマネージクラスであるため、すべてのインスタンスが要求の最後に収集されます。

次の図は、API を継続的に呼び出すときのメモリプロファイルを示して `fileprovider` います。

![前のグラフ](memory/_static/fileprovider.png)

前のグラフは、このクラスの実装に関する明らかな問題を示しています。これは、メモリ使用量が増加し続けるためです。 これは、 [この問題](https://github.com/dotnet/aspnetcore/issues/3110)で追跡されている既知の問題です。

次のいずれかの方法で、ユーザーコードで同じリークが発生する可能性があります。

* クラスを正しく解放できません。
* `Dispose`破棄する依存オブジェクトのメソッドを呼び出そうとしていません。

### <a name="large-objects-heap"></a>ラージオブジェクトヒープ

メモリの割り当てや空きサイクルが頻繁に発生する場合は、特にメモリの大量のチャンクを割り当てるときにメモリをフラグメント化できます。 オブジェクトは、連続したメモリブロックで割り当てられます。 断片化を軽減するために、GC はメモリを解放するときに、メモリを最適化しようとします。 このプロセスは、 **圧縮** と呼ばれます。 圧縮には、オブジェクトの移動が含まれます。 大きなオブジェクトを移動すると、パフォーマンスが低下します。 このため、GC は大きなオブジェクト [ヒープ](/dotnet/standard/garbage-collection/large-object-heap)(LOH) と呼ばれる _大きな_ オブジェクト用に特別なメモリゾーンを作成します。 85000バイトを超えるオブジェクト (約 83 KB) は次のとおりです。

* LOH に配置されます。
* 圧縮されていません。
* ジェネレーション2の Gc 中に収集されます。

LOH がいっぱいになると、GC はジェネレーション2のコレクションをトリガーします。 ジェネレーション2のコレクション:

* は本質的に低速です。
* さらに、他のすべての世代でコレクションをトリガーするコストも発生します。

次のコードは、LOH を直ちに最適化します。

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

LOH の圧縮の詳細については、「」を参照してください <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> 。

.NET Core 3.0 以降を使用するコンテナーでは、LOH が自動的に圧縮されます。

次の API は、この動作を示しています。

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

次のグラフは、エンドポイントを呼び出したときの、最大負荷下でのメモリプロファイルを示してい `/api/loh/84975` ます。

![前のグラフ](memory/_static/loh1.png)

次のグラフは、エンドポイントの呼び出しのメモリプロファイルを示してい `/api/loh/84976` ます。 *1 バイトだけ* 割り当てられます。

![前のグラフ](memory/_static/loh2.png)

注: `byte[]` 構造体にはオーバーヘッドバイトがあります。 そのため、84976バイトは85000の制限をトリガーします。

前の2つのグラフを比較します。

* ワーキングセットは、2つのシナリオ (約 450 MB) で似ています。
* [LOH 要求 (84975 バイト)] の下には、ほとんどがジェネレーション0のコレクションが表示されます。
* LOH を超える要求では、定数ジェネレーション2のコレクションが生成されます。 ジェネレーション2のコレクションはコストが高くなります。 より多くの CPU が必要であり、スループットは約50% 低下します。

一時的なラージオブジェクトは、gen2 Gc を引き起こすため、特に問題になります。

最大のパフォーマンスを向上させるには、大きなオブジェクトの使用を最小限にする必要があります。 可能であれば、大きなオブジェクトを分割します。 たとえば、ASP.NET Core の [応答キャッシュ](xref:performance/caching/response) ミドルウェアでは、キャッシュエントリが85000バイト未満のブロックに分割されます。

次のリンクでは、オブジェクトを LOH の制限下に維持するための ASP.NET Core アプローチについて説明します。

* [ResponseCaching/Streams/StreamUtilities .cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

詳細については、次をご覧ください。

* [大きなオブジェクトヒープが漏れています](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [大きなオブジェクトヒープ](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

を誤って使用する <xref:System.Net.Http.HttpClient> と、リソースリークが発生する可能性があります。 データベース接続、ソケット、ファイルハンドルなどのシステムリソース:

* はメモリよりも不足しています。
* メモリよりもリークが発生した場合、より問題が発生します。

経験豊富な .NET 開発者は、を <xref:System.IDisposable.Dispose*> 実装するオブジェクトに対してを呼び出すことがわかって <xref:System.IDisposable> います。 を実装するオブジェクトを破棄 `IDisposable` しないと、メモリリークやシステムリソースのリークが発生します。

`HttpClient` は `IDisposable` を実装しますが、すべての呼び出しで破棄することはでき **ません** 。 代わりに、を `HttpClient` 再利用する必要があります。

次のエンドポイントは、  `HttpClient` すべての要求に対して新しいインスタンスを作成し、破棄します。

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

負荷の下で、次のエラーメッセージがログに記録されます。

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

`HttpClient`インスタンスが破棄されている場合でも、オペレーティングシステムによって実際のネットワーク接続が解放されるまでに時間がかかります。 新しい接続を継続的に作成することで、 _ポートの枯渇_ が発生します。 各クライアント接続には、独自のクライアントポートが必要です。

ポートの枯渇を防ぐ方法の1つは、同じインスタンスを再利用することです `HttpClient` 。

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

`HttpClient`インスタンスは、アプリが停止したときに解放されます。 この例では、すべての破棄可能なリソースを使用後に破棄する必要がないことを示しています。

インスタンスの有効期間をより適切に処理する方法については、次を参照してください `HttpClient` 。

* [HttpClient と有効期間の管理](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [HTTPClient ファクトリのブログ](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>オブジェクト プーリング

前の例では、 `HttpClient` インスタンスを静的にし、すべての要求で再利用する方法を示しました。 再利用すると、リソースが不足するのを防ぐことができます。

オブジェクトプール:

* 再利用パターンを使用します。
* は、作成に負荷がかかるオブジェクト向けに設計されています。

プールは、スレッド間で予約および解放できる事前に初期化されたオブジェクトのコレクションです。 プールでは、制限、事前定義されたサイズ、増加率などの割り当てルールを定義できます。

NuGet パッケージの [Microsoft extension. ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) には、このようなプールの管理に役立つクラスが含まれています。

次の API エンドポイントは、 `byte` 各要求に対してランダムな数値を格納するバッファーをインスタンス化します。

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

次のグラフは、中程度の負荷で前の API を呼び出したときに表示されます。

![前のグラフ](memory/_static/array.png)

前のグラフでは、ジェネレーション0のコレクションは1秒間に約1回発生します。

上記のコードは、 `byte` [arraypool \<T> ](xref:System.Buffers.ArrayPool`1)を使用してバッファーをプールすることによって最適化できます。 静的インスタンスは、要求間で再利用されます。

この方法の違いは、プールされたオブジェクトが API から返されることです。 これは次のことを意味します。

* オブジェクトは、メソッドから戻るとすぐにコントロールから除外されます。
* オブジェクトを解放することはできません。

オブジェクトの破棄を設定するには、次のようにします。

* プールされた配列を破棄可能なオブジェクトにカプセル化します。
* プールされたオブジェクトを [httpcontext.current](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)に登録します。

`RegisterForDispose` は、 `Dispose` HTTP 要求が完了したときにのみ解放されるように、ターゲットオブジェクトでを呼び出すことを処理します。

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

プールされていないバージョンと同じ負荷を適用すると、次のグラフが表示されます。

![前のグラフ](memory/_static/pooledarray.png)

主な違いは、バイトが割り当てられ、その結果、ジェネレーション0のコレクションがはるかに少ないことです。

## <a name="additional-resources"></a>その他の資料

* [ガベージ コレクション](/dotnet/standard/garbage-collection/)
* [同時実行ビジュアライザーを使用したさまざまな GC モードについて](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [大きなオブジェクトヒープが漏れています](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [大きなオブジェクトヒープ](/dotnet/standard/garbage-collection/large-object-heap)
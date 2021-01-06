---
title: ASP.NET Core での静的資産のバンドルと縮小
author: scottaddie
description: バンドルと縮小の手法を適用して、ASP.NET Core Web アプリケーションの静的リソースを最適化する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: 7dd11ceb7a7c01ce1042f50595013b7fe7f1cd5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054841"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>ASP.NET Core での静的資産のバンドルと縮小

作成者: [Scott Addie](https://twitter.com/Scott_Addie)、[David Pine](https://twitter.com/davidpine7)

この記事では、ASP.NET Core Web アプリでこれらの機能を使用する方法など、バンドルと縮小を適用する利点について説明します。

## <a name="what-is-bundling-and-minification"></a>バンドルと縮小とは

バンドルと縮小は、Web アプリに適用できる 2 種類のパフォーマンス最適化です。 バンドルと縮小を併用すると、サーバー要求の数が減り、要求される静的資産のサイズが小さくなるため、パフォーマンスが向上します。

バンドルと縮小を使用すると、主に最初のページ要求の読み込み時間が短縮されます。 Web ページが要求されると、ブラウザーによって静的資産 (JavaScript、CSS、および画像) がキャッシュされます。 そのため、同じ資産を要求する同じサイト上で、同じページまたは複数のページを要求する場合、バンドルと縮小を使用してもパフォーマンスは改善されません。 expires ヘッダーが資産に正しく設定されておらず、バンドルと縮小が使用されていない場合、ブラウザーの更新の間隔ヒューリスティックにより、数日後には資産が古くなっているとマークされます。 さらに、ブラウザーでは、各資産に対する検証要求が必要です。 この場合、最初のページ要求の後でも、バンドルと縮小によりパフォーマンスが向上します。

### <a name="bundling"></a>バンドル

バンドルでは、複数のファイルを単一のファイルに連結します。 バンドルを使用すると、Web ページなどの Web 資産のレンダリングに必要なサーバー要求の数を減らすことができます。 CSS、JavaScript など専用の個別のバンドルをいくつでも作成できます。ファイルが少なくなると、ブラウザーからサーバーへ、またはアプリケーションを提供するサービスからの HTTP 要求が少なくなることを意味します。 その結果、最初のページ読み込みのパフォーマンスが向上します。

### <a name="minification"></a>縮小

縮小を使用すると、機能を変更せずにコードから不要な文字を削除できます。 その結果、要求される資産 (CSS、画像、JavaScript ファイルなど) のサイズが大幅に減ります。 縮小の一般的な副作用として、変数名を 1 文字に短縮する機能や、コメントと不要な空白を削除する機能があります。

次の JavaScript 関数を考えてみます。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

縮小により、関数が次のように短縮されます。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

コメントと不要な空白の削除に加えて、次のパラメーターと変数名は次のように名前が変更されました。

元 | 名前の変更
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>バンドルと縮小の影響

次の表は、個別に資産を読み込む場合とバンドルと縮小を使用する場合の違いを示しています。

アクション | バンドルと縮小あり | バンドルと縮小なし | 変更
--- | :---: | :---: | :---:
ファイル要求  | 7   | 18     | 157%
転送済み (KB) | 156 | 264.68 | 70%
読み込み時間 (ミリ秒) | 885 | 2360   | 167%

ブラウザーは、HTTP 要求ヘッダーに関してかなり冗長です。 送信された合計バイト数メトリックは、バンドル時に大幅に減りました。 読み込み時間は大幅に改善されていますが、この例はローカルで実行されています。 ネットワークを経由で転送される資産にバンドルと縮小を使用すると、パフォーマンスが大幅に向上します。

## <a name="choose-a-bundling-and-minification-strategy"></a>バンドルと縮小の戦略を選択する

MVC および Razor Pages プロジェクト テンプレートには、JSON 構成ファイルで構成されるバンドルおよび縮小のためのソリューションが用意されています。 [Grunt](xref:client-side/using-grunt) タスク ランナーなどのサードパーティ ツールの場合、同じタスクを実行するにはもう少し複雑です。 サードパーティ製のツールは、リンティングや画像の最適化など、バンドルと縮小を超える処理が開発ワークフローに必要な場合に最適です。 設計時にバンドルと縮小を使用することで、アプリのデプロイ前に縮小されたファイルが作成されます。 デプロイ前のバンドルと縮小によって、サーバーの負荷が軽減されます。 ただし、設計時にバンドルと縮小を使用するとビルドの複雑さが増すので、静的ファイルでのみ機能することを認識することが重要です。

## <a name="configure-bundling-and-minification"></a>バンドルと縮小を構成する

> [!NOTE]
> これを機能させるには、[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet パッケージをプロジェクトに追加する必要があります。

::: moniker range="<= aspnetcore-2.0"

ASP.NET Core 2.0 以前では、MVC および Razor Pages プロジェクト テンプレートには、各バンドルのオプションが定義された *bundleconfig.json* 構成ファイルが用意されています。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2.1 以降では、*bundleconfig.json* という名前の新しい JSON ファイルを MVC または Razor Pages プロジェクトのルートに追加します。 開始点としてそのファイルに次の JSON を含めます。

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*bundleconfig.json* ファイルには、各バンドルのオプションが定義されています。 上記の例では、カスタムの JavaScript (*wwwroot/js/site.js*) とスタイルシート (*wwwroot/css/site.css*) ファイルに対して 1 つのバンドル構成が定義されています。

構成のオプションには、次のようなものがあります。

* `outputFileName`:出力するバンドル ファイルの名前。 *bundleconfig.json* ファイルからの相対パスを含めることができます。 **必須**
* `inputFiles`:まとめてバンドルするファイルの配列。 これらは、構成ファイルへの相対パスです。 **省略可能**、* 値が空の場合、空の出力ファイルになります。 [globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) パラメーターがサポートされます。
* `minify`:出力の種類の縮小オプション。 **省略可能**、*既定値 - `minify: { enabled: true }`*
  * 構成オプションは、出力ファイルの種類ごとに使用できます。
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`:生成されたファイルをプロジェクト ファイルに追加するかどうかを示すフラグ。 **省略可能**、*既定値 - false*
* `sourceMap`:バンドルされたファイルのソース マップを生成するかどうかを示すフラグ。 **省略可能**、*既定値 - false*
* `sourceMapRootPath`:生成されたソース マップ ファイルを格納するためのルート パス。

## <a name="add-files-to-workflow"></a>ワークフローにファイルを追加する

次のような新しい *custom.css* ファイルが追加される例を考えてみましょう。

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

*custom.css* を縮小し、それと *site.css* を *site.min.css* ファイルにバンドルするには、相対パスを *bundleconfig.json* に追加します。

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> または、次の glob パターンを使用できます。
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> この glob パターンはすべての CSS ファイルを照合し、縮小されたファイル パターンは除外されます。

アプリケーションをビルドします。 *site.min.css* を開き、ファイルの最後に *custom.css* のコンテンツが追加されていることに注意します。

## <a name="environment-based-bundling-and-minification"></a>環境ベースのバンドルと縮小

ベスト プラクティスとして、アプリのバンドルおよび縮小されたファイルを運用環境で使用することをお勧めします。 開発中は、元のファイルがあるので、アプリのデバッグが容易になります。

ビューで [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) を使用して、ページに含めるファイルを指定します。 Environment Tag Helper を使用すると、特定の[環境](xref:fundamentals/environments)で実行されている場合にのみコンテンツがレンダリングされます。

`Development` 環境で実行されている場合、次の `environment` タグを使用すると、未処理の CSS ファイルがレンダリングされます。

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

`Development` 以外の環境で実行されている場合に、次の `environment` タグを使用すると、バンドルおよび縮小された CSS ファイルがレンダリングされます。 たとえば、`Production` または `Staging` で実行すると、これらのスタイルシートのレンダリングがトリガーされます。

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Gulp から bundleconfig.json を使用する

アプリのバンドルおよび縮小ワークフローで追加の処理が必要になる場合があります。 たとえば、画像の最適化、キャッシュ バスティング、CDN 資産の処理などです。 これらの要件を満たすために、Gulp を使用するようにバンドルおよび縮小ワークフローを変換できます。

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Gulp を使用するようにバンドルおよび縮小ワークフローを手動で変換できます。

次の `devDependencies` を使用して *package.json* ファイルをプロジェクトのルートに追加します。

> [!WARNING]
> `gulp-uglify` モジュールは、ECMAScript (ES) 2015 / ES6 以降をサポートしていません。 ES2015 / ES6 以降を使用するには、`gulp-uglify` ではなく [gulp-terser](https://www.npmjs.com/package/gulp-terser) をインストールします。

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

*package.json* と同じレベルで次のコマンドを実行して、依存関係をインストールします。

```bash
npm i
```

グローバルな依存関係として Gulp CLI をインストールします。

```bash
npm i -g gulp-cli
```

次の *gulpfile.js* ファイルをプロジェクトのルートにコピーします。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Gulp タスクを実行する

Visual Studio でプロジェクトをビルドする前に Gulp 縮小タスクをトリガーするには:

1. [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet パッケージをインストールします。
1. 次の [MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)をプロジェクト ファイルに追加します。

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

この例では、`MyPreCompileTarget` ターゲット内で定義されたタスクは、事前に定義された `Build` ターゲットの前に実行されます。 次のような出力が Visual Studio の出力ウィンドウに表示されます。

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>その他の技術情報

* [Grunt の使用](xref:client-side/using-grunt)
* [複数の環境の使用](xref:fundamentals/environments)
* [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)

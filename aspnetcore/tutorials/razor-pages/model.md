---
title: 'パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する'
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 2。
ms.author: riande
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050928"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

このセクションでは、映画を管理するクラスが追加されます。 アプリのモデル クラスでは、[Entity Framework Core (EF Core)](/ef/core) を使用して、データベースを操作します。 EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッパー (O/RM) です。

このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。 これらは、データベースに格納されるデータのプロパティを定義します。

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>データ モデルの追加

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** 、 **[新しいフォルダー]** の順に選択します。 フォルダーに「 *Models* 」という名前を付けます。

*Models* フォルダーを右クリックします。 **[追加]**  >  **[クラス]** の順に選択します。 クラスに **Movie** と名前を付けます。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* という名前のフォルダーを追加します。
* *Movie.cs* という名前の *Models* フォルダーにクラスを追加します。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Solution Pad で、 **RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** 、 **[新しいフォルダー]** の順に選択します。フォルダーに「 *Models* 」という名前を付けます。
* *Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。
* **[新しいファイル]** ダイアログで次を実行します。

  * 左側のウィンドウで **[全般]** を選択します。
  * 中央ウィンドウで **[空のクラス]** を選択します。
  * クラスに **Movie** という名前を付け、 **[新規]** を選択します。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

プロジェクトをビルドして、コンパイル エラーがないことを確認します。

## <a name="scaffold-the-movie-model"></a>ムービー モデルのスキャフォールディング

このセクションでは、ムービー モデルがスキャフォールディングされます。 つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Pages/Movies* フォルダーを作成します。

* *Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。
* フォルダーに *Movies* という名前を付けます。

*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。

![前の手順からのイメージ。](model/_static/sca.png)

**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。

![前の手順からのイメージ。](model/_static/add_scaffold.png)

**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。

* **[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。
* **データ コンテキスト クラス** 行で、 **+** (プラス) 記号を選択し、生成された名前 RazorPagesMovie. **Models**.RazorPagesMovieContext を RazorPagesMovie. **Data**.RazorPagesMovieContext に変更します。 [この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。 これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。
* **[追加]** を選びます。

![前の手順からのイメージ。](model/_static/3/arp.png)

*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。
* スキャフォールディング ツールをインストールします。

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Windows の場合** :次のコマンドを実行します。

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **macOS および Linux の場合** :次のコマンドを実行します。

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

*Pages/Movies* フォルダーを作成します。

* *Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。
* フォルダーに *Movies* という名前を付けます。

*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[新しいスキャフォールディング]** の順に選択します。

![前の手順からのイメージ。](model/_static/scaMac.png)

**[新しいスキャフォールディング]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[次へ]** を選択します。

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。

* **[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択するか、または入力します。
* **データ コンテキスト クラス** 行で、新しいクラスの名前「RazorPagesMovie. **Data**.RazorPagesMovieContext」と入力します。 [この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。 これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。
* **[追加]** を選びます。

![前の手順からのイメージ。](model/_static/arpMac.png)

*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。

### <a name="add-ef-tools"></a>EF ツールの追加

次の .NET Core CLI コマンドを実行します。

```dotnetcli
dotnet tool install --global dotnet-ef
```

上記のコマンドを実行すると、.NET Core CLI の Entity Framework Core ツールが追加されます。

---

### <a name="files-created"></a>作成されたファイル

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

スキャフォールディングのプロセスが作成され、次のファイルが更新されます。

* *Pages/Movies* : 作成、削除、詳細、編集、インデックス。
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>更新済み

* *Startup.cs*

作成および更新されたファイルについては、次のセクションで説明します。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

スキャフォールディングのプロセスが作成され、次のファイルが更新されます。

* *Pages/Movies* : 作成、削除、詳細、編集、インデックス。
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>更新済み

* *Startup.cs*

作成および更新されたファイルについては、次のセクションで説明します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

スキャフォールディングのプロセスでは、次のファイルが作成されます。

* *Pages/Movies* : 作成、削除、詳細、編集、インデックス。

作成されたファイルについては、次のセクションで説明します。

---

<a name="pmc"></a>

## <a name="initial-migration"></a>最初の移行

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。

* 初期移行を追加します。
* 初期移行でデータベースを更新します。

**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

PMC で、次のコマンドを入力します。

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。 これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。 'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"

この警告は無視して構いません。後のチュートリアルで修正されます。

移行コマンドによって、最初のデータベース スキーマを作成するコードが生成されます。 このスキーマは、`DbContext` で指定されたモデルに基づきます。 `InitialCreate` 引数は移行の命名に使用されます。 任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。

`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。 この場合、`update` により、 *Migrations/\<time-stamp>_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>依存関係挿入に登録されるコンテキストを調べる

ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。 サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。 これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。 DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。

スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。

`Startup.ConfigureServices` メソッドを調べます。 強調表示された行は、スキャフォールダーによって追加されました。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。 データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。 データ コンテキストによって、データ モデルに含めるエンティティが指定されます。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。 Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。 エンティティはテーブル内の行に対応します。

[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。 ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Up` メソッドを調べます。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

`Up` メソッドを調べます。

---

<a name="test"></a>

### <a name="test-the-app"></a>アプリのテスト

* アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。

エラーが発生した場合は、次のようにします。

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[移行手順](#pmc)を失敗しました。

* **[作成]** リンクをテストします。

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > `Price` フィールドに小数点のコンマを入力できない場合があります。 小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。 グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。

* **[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。

次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。

## <a name="additional-resources"></a>その他の技術情報

> [!div class="step-by-step"]
> [前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor ページ](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。 ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。 このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。 EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。

このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。 これらは、データベースに格納されるデータのプロパティを定義します。

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>データ モデルの追加

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** 、 **[新しいフォルダー]** の順に選択します。 フォルダーに「 *Models* 」という名前を付けます。

*Models* フォルダーを右クリックします。 **[追加]**  >  **[クラス]** の順に選択します。 クラスに **Movie** と名前を付けます。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* という名前のフォルダーを追加します。
* *Movie.cs* という名前の *Models* フォルダーにクラスを追加します。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* ソリューション エクスプローラーで、 **RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** 、 **[新しいフォルダー]** の順に選択します。 フォルダーに「 *Models* 」という名前を付けます。
* *Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。
* **[新しいファイル]** ダイアログで次を実行します。

  * 左側のウィンドウで **[全般]** を選択します。
  * 中央ウィンドウで **[空のクラス]** を選択します。
  * クラスに **Movie** という名前を付け、 **[新規]** を選択します。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

プロジェクトをビルドして、コンパイル エラーがないことを確認します。

## <a name="scaffold-the-movie-model"></a>ムービー モデルのスキャフォールディング

このセクションでは、ムービー モデルがスキャフォールディングされます。 つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Pages/Movies* フォルダーを作成します。

* *Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。
* フォルダーに *Movies* という名前を付けます。

*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。

![前の手順からのイメージ。](model/_static/sca.png)

**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。

![前の手順からのイメージ。](model/_static/add_scaffold.png)

**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* **[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。
* **データ コンテキスト クラス** 行で、 **+** (プラス) 記号を選択し、生成された名前 **RazorPagesMovie.Models.RazorPagesMovieContext** を受け入れます。
* **[追加]** を選びます。

![前の手順からのイメージ。](model/_static/arp.png)

*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。

* **Windows の場合** :次のコマンドを実行します。

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **macOS および Linux の場合** :次のコマンドを実行します。

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

*Pages/Movies* フォルダーを作成します。

* *Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。
* フォルダーに *Movies* という名前を付けます。

*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。

![前の手順からのイメージ。](model/_static/scaMac.png)

**[新しいスキャフォールディングの追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。

* **[モデル クラス]** ドロップ ダウンで、 **[Movie]** を選択するか、または入力します。
* **データ コンテキスト クラス** 行で、 **[RazorPagesMovieContext]** を選択または入力します。これにより、新しい db コンテキスト クラスが正しい名前空間で作成されます。 このクラスでは、 **RazorPagesMovie.Models.RazorPagesMovieContext** となります。
* **[追加]** を選びます。

![前の手順からのイメージ。](model/_static/arpMac.png)

*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。

---

スキャフォールディングのプロセスが作成され、次のファイルが更新されます。

### <a name="files-created"></a>作成されたファイル

* *Pages/Movies* : 作成、削除、詳細、編集、インデックス。
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>更新されたファイル

* *Startup.cs*

作成および更新されたファイルについては、次のセクションで説明します。

<a name="pmc"></a>

## <a name="initial-migration"></a>最初の移行

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。

* 初期移行を追加します。
* 初期移行でデータベースを更新します。

**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

PMC で、次のコマンドを入力します。

```powershell
Add-Migration Initial
Update-Database
```

`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。 このスキーマは、`DbContext` で指定されたモデルに基づきます ( *RazorPagesMovieContext.cs* ファイル内)。 `InitialCreate` 引数は、移行の名前を指定するために使用されます。 任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。 詳細については、「<xref:data/ef-mvc/migrations>」を参照してください。

`Update-Database` コマンドによって、 *Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドが実行されます。 `Up` メソッドにより、データベースが作成されます。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> 上記のコマンドで次の警告が生成されます。" *エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。* " この警告は無視して構いません。後のチュートリアルで修正されます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>依存関係挿入に登録されるコンテキストを調べる

ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。 サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。 これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。 DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。

スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。

`Startup.ConfigureServices` メソッドを調べます。 強調表示された行は、スキャフォールダーによって追加されました。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。 データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。 データ コンテキストによって、データ モデルに含めるエンティティが指定されます。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。 Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。 エンティティはテーブル内の行に対応します。

[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。 ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Up` メソッドを調べます。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

`Up` メソッドを調べます。

---

<a name="test"></a>

### <a name="test-the-app"></a>アプリのテスト

* アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。

エラーが発生した場合は、次のようにします。

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[移行手順](#pmc)を失敗しました。

* **[作成]** リンクをテストします。

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > `Price` フィールドに小数点のコンマを入力できない場合があります。 小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。 グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。

* **[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。

次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。

## <a name="additional-resources"></a>その他の技術情報

> [!div class="step-by-step"]
> [前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor ページ](xref:tutorials/razor-pages/page)

::: moniker-end

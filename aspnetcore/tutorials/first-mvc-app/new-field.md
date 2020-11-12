---
title: パート 8、ASP.NET Core MVC アプリへの新しいフィールドの追加
author: rick-anderson
description: ASP.NET Core MVC のチュートリアル シリーズのパート 8。
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 84e344aabc9171020c0117d55eaf1a95e6b768db
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422536"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a>パート 8、ASP.NET Core MVC アプリへの新しいフィールドの追加

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。

* モデルに新しいフィールドを追加する。
* 新しいフィールドをデータベースに移行する。

EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。

* テーブルをデータベースに追加して、データベースのスキーマを追跡します。
* データベースが生成されたモデル クラスと同期されていることを確認します。 同期していない場合、EF は例外をスローします。 一貫性のないデータベース/コードの問題を簡単に見つけられます。

## <a name="add-a-rating-property-to-the-movie-model"></a>ムービー モデルへの評価プロパティの追加

`Rating` プロパティを *Models/Movie.cs* に追加します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?name=snippet)]

アプリをビルドする

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl + Shift + B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

コマンド ⌘ + B

------

新しいフィールドを `Movie` クラスに追加したので、この新しいプロパティが含まれるようにプロパティ バインディング リストを更新する必要があります。 *MoviesController.cs* で、アクション メソッドの `Create` と `Edit` の両方の `[Bind]` 属性を更新し、`Rating` プロパティが含まれるようにします。

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

ブラウザー ビューで新しい `Rating` プロパティを表示、作成、編集するためにビュー テンプレートを更新します。

*/Views/Movies/Index.cshtml* ファイルを編集し、`Rating` フィールドを追加します。

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Movies/Index.cshtml?highlight=28-49&range=12-51)]

::: moniker-end

*/Views/Movies/Create.cshtml* を `Rating` フィールドで更新します。

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio / Visual Studio for Mac](#tab/visual-studio+visual-studio-mac)

前の "form group" をコピー/貼り付けし、intelliSense にフィールドを更新させることができます。 IntelliSense は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)と連動します。

![開発者は、ビューの 2 番目のラベル要素で、asp-for の属性値に文字 R を入力しました。 Intellisense のコンテキスト メニューが表示され、[評価] を含む、利用可能なフィールドが表示されました。[評価] は一覧の中で自動的に強調表示されています。 開発者はこのフィールドをクリックするか、キーボードの Enter を押すと、値が [評価] に設定されます。](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

残りのテンプレートを更新します。

新しい列に値を提供するように、`SeedData` クラスを更新します。 下に変更のサンプルがありますが、`new Movie` ごとにこの変更を行ってください。

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。 ここで実行すると、次の `SqlException` がスローされます。

`SqlException: Invalid column name 'Rating'.`

このエラーは、更新された Movie モデル クラスが既存のデータベースの Movie テーブルのスキーマと異なるために発生します。 (データベース テーブルに `Rating` 列はありません)。

このエラーを解決するための手法がいくつかあります。

1. Entity Framework に、新しいモデル クラス スキーマに基づいてデータベースを自動的にドロップさせ、再作成させます。 この手法は、開発周期の早い段階で、テスト データベースで開発しているときに非常に便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。 ただし、欠点もあり、データベースの既存データが失われます。本稼働データベースではこの手法は推奨されません。 初期化子を利用し、データベースにテスト データを自動的に初期投入します。多くの場合、アプリケーション開発の手法として有益な方法です。 これは、初期の開発や SQLite を使用するときに適した方法です。

2. モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。 この手法の長所は、データが維持されることです。 この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。

3. Code First Migrations を使用して、データベース スキーマを更新します。

このチュートリアルでは、Code First Migrations を使用します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。

  ![PMC メニュー](adding-model/_static/pmc.png)

PMC で、次のコマンドを入力します。

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` コマンドは移行フレームワークに現在の `Movie` モデルを現在の `Movie` DB スキーマで調べ、DB を新しいモデルに移行するために必要なコードを作成します。

"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。 移行ファイルには意味のある名前を使用すると便利です。

DB 内のレコードをすべて削除すると、初期化メソッドで DB がシードされ、`Rating` フィールドが追加されします。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

データベースと以前の移行を削除し、移行を使ってデータベースを再作成します。

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

`dotnet ef migrations remove` によって最後の移行が削除されます。 複数の移行がある場合は、Migrations フォルダーを削除します。

---
<!-- End of VS tabs -->

アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。

> [!div class="step-by-step"]
> [前へ](search.md)
> [次へ](validation.md)

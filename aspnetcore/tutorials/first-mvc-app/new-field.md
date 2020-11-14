---
title: パート 8、ASP.NET Core MVC アプリへの新しいフィールドの追加
author: rick-anderson
description: ASP.NET Core MVC のチュートリアル シリーズのパート 8。
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 84e344aabc9171020c0117d55eaf1a95e6b768db
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422536"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="cd53a-103">パート 8、ASP.NET Core MVC アプリへの新しいフィールドの追加</span><span class="sxs-lookup"><span data-stu-id="cd53a-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="cd53a-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cd53a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cd53a-105">このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="cd53a-106">モデルに新しいフィールドを追加する。</span><span class="sxs-lookup"><span data-stu-id="cd53a-106">Add a new field to the model.</span></span>
* <span data-ttu-id="cd53a-107">新しいフィールドをデータベースに移行する。</span><span class="sxs-lookup"><span data-stu-id="cd53a-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="cd53a-108">EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="cd53a-109">テーブルをデータベースに追加して、データベースのスキーマを追跡します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="cd53a-110">データベースが生成されたモデル クラスと同期されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="cd53a-111">同期していない場合、EF は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="cd53a-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="cd53a-112">一貫性のないデータベース/コードの問題を簡単に見つけられます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="cd53a-113">ムービー モデルへの評価プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="cd53a-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="cd53a-114">`Rating` プロパティを *Models/Movie.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-114">Add a `Rating` property to *Models/Movie.cs* :</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?name=snippet)]

<span data-ttu-id="cd53a-115">アプリをビルドする</span><span class="sxs-lookup"><span data-stu-id="cd53a-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="cd53a-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd53a-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="cd53a-117">Ctrl + Shift + B</span><span class="sxs-lookup"><span data-stu-id="cd53a-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="cd53a-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cd53a-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cd53a-119">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cd53a-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cd53a-120">コマンド ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="cd53a-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="cd53a-121">新しいフィールドを `Movie` クラスに追加したので、この新しいプロパティが含まれるようにプロパティ バインディング リストを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cd53a-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="cd53a-122">*MoviesController.cs* で、アクション メソッドの `Create` と `Edit` の両方の `[Bind]` 属性を更新し、`Rating` プロパティが含まれるようにします。</span><span class="sxs-lookup"><span data-stu-id="cd53a-122">In *MoviesController.cs* , update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="cd53a-123">ブラウザー ビューで新しい `Rating` プロパティを表示、作成、編集するためにビュー テンプレートを更新します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="cd53a-124">*/Views/Movies/Index.cshtml* ファイルを編集し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Movies/Index.cshtml?highlight=28-49&range=12-51)]

::: moniker-end

<span data-ttu-id="cd53a-125">*/Views/Movies/Create.cshtml* を `Rating` フィールドで更新します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="cd53a-126">Visual Studio / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cd53a-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="cd53a-127">前の "form group" をコピー/貼り付けし、intelliSense にフィールドを更新させることができます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="cd53a-128">IntelliSense は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)と連動します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![開発者は、ビューの 2 番目のラベル要素で、asp-for の属性値に文字 R を入力しました。](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cd53a-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cd53a-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="cd53a-133">残りのテンプレートを更新します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-133">Update the remaining templates.</span></span>

<span data-ttu-id="cd53a-134">新しい列に値を提供するように、`SeedData` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="cd53a-135">下に変更のサンプルがありますが、`new Movie` ごとにこの変更を行ってください。</span><span class="sxs-lookup"><span data-stu-id="cd53a-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="cd53a-136">DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。</span><span class="sxs-lookup"><span data-stu-id="cd53a-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="cd53a-137">ここで実行すると、次の `SqlException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="cd53a-138">このエラーは、更新された Movie モデル クラスが既存のデータベースの Movie テーブルのスキーマと異なるために発生します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="cd53a-139">(データベース テーブルに `Rating` 列はありません)。</span><span class="sxs-lookup"><span data-stu-id="cd53a-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="cd53a-140">このエラーを解決するための手法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="cd53a-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="cd53a-141">Entity Framework に、新しいモデル クラス スキーマに基づいてデータベースを自動的にドロップさせ、再作成させます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="cd53a-142">この手法は、開発周期の早い段階で、テスト データベースで開発しているときに非常に便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="cd53a-143">ただし、欠点もあり、データベースの既存データが失われます。本稼働データベースではこの手法は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="cd53a-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="cd53a-144">初期化子を利用し、データベースにテスト データを自動的に初期投入します。多くの場合、アプリケーション開発の手法として有益な方法です。</span><span class="sxs-lookup"><span data-stu-id="cd53a-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="cd53a-145">これは、初期の開発や SQLite を使用するときに適した方法です。</span><span class="sxs-lookup"><span data-stu-id="cd53a-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="cd53a-146">モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="cd53a-147">この手法の長所は、データが維持されることです。</span><span class="sxs-lookup"><span data-stu-id="cd53a-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="cd53a-148">この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="cd53a-149">Code First Migrations を使用して、データベース スキーマを更新します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="cd53a-150">このチュートリアルでは、Code First Migrations を使用します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd53a-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd53a-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cd53a-152">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC メニュー](adding-model/_static/pmc.png)

<span data-ttu-id="cd53a-154">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="cd53a-155">`Add-Migration` コマンドは移行フレームワークに現在の `Movie` モデルを現在の `Movie` DB スキーマで調べ、DB を新しいモデルに移行するために必要なコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="cd53a-156">"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="cd53a-157">移行ファイルには意味のある名前を使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="cd53a-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="cd53a-158">DB 内のレコードをすべて削除すると、初期化メソッドで DB がシードされ、`Rating` フィールドが追加されします。</span><span class="sxs-lookup"><span data-stu-id="cd53a-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd53a-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cd53a-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="cd53a-160">データベースと以前の移行を削除し、移行を使ってデータベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-160">Delete the database and the previous migration and use migrations to re-create the database:</span></span>

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="cd53a-161">`dotnet ef migrations remove` によって最後の移行が削除されます。</span><span class="sxs-lookup"><span data-stu-id="cd53a-161">`dotnet ef migrations remove` removes the last migration.</span></span> <span data-ttu-id="cd53a-162">複数の移行がある場合は、Migrations フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-162">If there are more than one migration, delete the Migrations folder.</span></span>

---
<!-- End of VS tabs -->

<span data-ttu-id="cd53a-163">アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="cd53a-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cd53a-164">[前へ](search.md)
> [次へ](validation.md)</span><span class="sxs-lookup"><span data-stu-id="cd53a-164">[Previous](search.md)
[Next](validation.md)</span></span>

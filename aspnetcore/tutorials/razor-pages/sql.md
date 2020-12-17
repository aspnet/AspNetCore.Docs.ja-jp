---
title: パート 4、データベースの操作
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 4。
ms.author: riande
ms.date: 09/26/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 552a74016c281af248ce735c6ed6a5a55768a16a
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486245"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="90612-103">Razor ページのチュートリアル シリーズのパート 4。</span><span class="sxs-lookup"><span data-stu-id="90612-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="90612-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="90612-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="90612-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="90612-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="90612-106">`RazorPagesMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="90612-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="90612-107">データベース コンテキストは、*Startup.cs* の `ConfigureServices` メソッドで [依存性の注入](xref:fundamentals/dependency-injection)コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="90612-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-109">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="90612-110">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムでは `ConnectionString` キーが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90612-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="90612-111">ローカルで開発する場合は、構成によって *appsettings.json* ファイルから接続文字列が取得されます。</span><span class="sxs-lookup"><span data-stu-id="90612-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="90612-113">生成される接続文字列は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="90612-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-114">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="90612-115">アプリがテスト サーバーまたは運用サーバーにデプロイされると、環境変数を使用して接続文字列をテストまたは運用データベース サーバーに設定できます。</span><span class="sxs-lookup"><span data-stu-id="90612-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="90612-116">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90612-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="90612-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="90612-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="90612-119">LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。</span><span class="sxs-lookup"><span data-stu-id="90612-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="90612-120">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="90612-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="90612-121">LocalDB データベースでは、既定で `C:\Users\<user>\` ディレクトリに `*.mdf` ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90612-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="90612-122">**[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="90612-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![[View] メニュー](sql/_static/5/ssox.png)

1. <span data-ttu-id="90612-124">`Movie` テーブルを右クリックし、 **[デザイナーの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90612-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Movie テーブルに対して開かれたコンテキスト メニュー](sql/_static/5/design.png)

   ![デザイナーに開かれた Movie テーブル](sql/_static/dv.png)

   <span data-ttu-id="90612-127">`ID` の横のキー アイコンに注意してください。</span><span class="sxs-lookup"><span data-stu-id="90612-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="90612-128">既定では、EF で主キーに `ID` という名前のプロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90612-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="90612-129">`Movie` テーブルを右クリックし、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90612-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![開いた Movie テーブルにテーブル データが表示されています](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-131">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="90612-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="90612-132">SQLite</span></span>

<span data-ttu-id="90612-133">[SQLite](https://www.sqlite.org/) の Web サイトは次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="90612-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="90612-134">SQLite は埋め込みの全機能を備えた、自己完結型かつ高信頼性でパブリック ドメインの SQL データベース エンジンです。</span><span class="sxs-lookup"><span data-stu-id="90612-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="90612-135">SQLite は、世界で最も使用されているデータベース エンジンです。</span><span class="sxs-lookup"><span data-stu-id="90612-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="90612-136">SQLite データベースを管理および表示するためにダウンロードできるサードパーティ製ツールは多数あります。</span><span class="sxs-lookup"><span data-stu-id="90612-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="90612-137">以下の画像は [DB Browser for SQLite](https://sqlitebrowser.org/) のものです。</span><span class="sxs-lookup"><span data-stu-id="90612-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="90612-138">お気に入りの SQLite ツールがございましたら、そのツールの気に入っている点についてコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="90612-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Movie データベースを示している DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="90612-140">このチュートリアルでは、可能な場合は Entity Framework Core の "*移行*" 機能が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90612-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="90612-141">移行では、データ モデルの変更に合わせてデータベース スキーマが更新されます。</span><span class="sxs-lookup"><span data-stu-id="90612-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="90612-142">ただし、移行では EF Core プロバイダーでサポートされている種類の変更しか行うことができず、SQLite プロバイダーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="90612-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="90612-143">たとえば、列の追加はサポートされていますが、列の削除や変更はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="90612-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="90612-144">列を削除または変更するために移行を作成した場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。</span><span class="sxs-lookup"><span data-stu-id="90612-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="90612-145">これらの制限により、このチュートリアルでは SQLite のスキーマの変更に対しては移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="90612-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="90612-146">代わりに、スキーマが変更されると、データベースが削除され、再作成されます。</span><span class="sxs-lookup"><span data-stu-id="90612-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="90612-147">SQLite の制限に対する回避策としては、テーブル内の何かが変更されたときにテーブルの再構築を実行する移行コードを、手動で作成します。</span><span class="sxs-lookup"><span data-stu-id="90612-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="90612-148">テーブルの再構築には次の作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="90612-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="90612-149">新しいテーブルの作成。</span><span class="sxs-lookup"><span data-stu-id="90612-149">Creating a new table.</span></span>
>* <span data-ttu-id="90612-150">古いテーブルから新しいテーブルへのデータのコピー。</span><span class="sxs-lookup"><span data-stu-id="90612-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="90612-151">古いテーブルの削除。</span><span class="sxs-lookup"><span data-stu-id="90612-151">Dropping the old table.</span></span>
>* <span data-ttu-id="90612-152">新しいテーブルの名前変更。</span><span class="sxs-lookup"><span data-stu-id="90612-152">Renaming the new table.</span></span>
>
><span data-ttu-id="90612-153">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90612-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="90612-154">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="90612-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="90612-155">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="90612-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="90612-156">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="90612-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="90612-157">SQLite ALTER TABLE ステートメント</span><span class="sxs-lookup"><span data-stu-id="90612-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="90612-158">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="90612-158">Seed the database</span></span>

<span data-ttu-id="90612-159">次のコードを使用して、*Models* フォルダーに `SeedData` という名前の新しいクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="90612-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="90612-160">データベースにムービーがある場合、シード初期化子が返され、ムービーは追加されません。</span><span class="sxs-lookup"><span data-stu-id="90612-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="90612-161">シード初期化子の追加</span><span class="sxs-lookup"><span data-stu-id="90612-161">Add the seed initializer</span></span>

<span data-ttu-id="90612-162">*Program.cs* の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="90612-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="90612-163">以前のコードでは、`Main` メソッドが次のように変更されています。</span><span class="sxs-lookup"><span data-stu-id="90612-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="90612-164">依存関係挿入コンテナーからデータベース コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="90612-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="90612-165">`seedData.Initialize` メソッドを呼び出すと、データベース コンテキスト インスタンスに渡されます。</span><span class="sxs-lookup"><span data-stu-id="90612-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="90612-166">seed メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="90612-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="90612-167">[using ステートメント](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)を使用すると、コンテキストが確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="90612-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="90612-168">`Update-Database` が実行されていなかった場合、次の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="90612-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="90612-169">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="90612-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="90612-171">データベース内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="90612-171">Delete all the records in the database.</span></span> <span data-ttu-id="90612-172">ブラウザーの削除リンクを使用するか、[SSOX](xref:tutorials/razor-pages/new-field#ssox) から行います。</span><span class="sxs-lookup"><span data-stu-id="90612-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="90612-173">`Startup` クラスでメソッドを呼び出してアプリを強制的に初期化して、シード メソッドが実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="90612-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="90612-174">強制的に初期化するには、IIS Express を停止してから再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90612-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="90612-175">次の方法のいずれかを使用して、IIS を停止してから再起動します。</span><span class="sxs-lookup"><span data-stu-id="90612-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="90612-176">通知領域にある IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90612-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![IIS Express システム トレイ アイコン](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![コンテキスト メニュー](sql/_static/stopIIS.png)

   1. <span data-ttu-id="90612-179">非デバッグ モードでアプリが実行されている場合は、<kbd>F5</kbd> キーを押してデバッグ モードで実行します。</span><span class="sxs-lookup"><span data-stu-id="90612-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="90612-180">デバッグ モードでアプリが実行されている場合は、デバッガーを停止して、<kbd>F5</kbd> キーを押します。</span><span class="sxs-lookup"><span data-stu-id="90612-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-181">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="90612-182">データベース内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="90612-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="90612-183">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="90612-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="90612-184">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="90612-184">The app shows the seeded data:</span></span>

![ムービー データが表示されたブラウザーで開いている Movie アプリケーション](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="90612-186">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="90612-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="90612-187">[前へ:スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)
> [次:ページの更新](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="90612-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="90612-188">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="90612-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="90612-189">`RazorPagesMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="90612-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="90612-190">データベース コンテキストは、*Startup.cs* の `ConfigureServices` メソッドで [依存性の注入](xref:fundamentals/dependency-injection)コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="90612-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-192">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="90612-193">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムでは `ConnectionString` キーが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90612-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="90612-194">ローカルで開発する場合は、構成によって *appsettings.json* ファイルから接続文字列が取得されます。</span><span class="sxs-lookup"><span data-stu-id="90612-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="90612-196">生成される接続文字列は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="90612-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-197">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="90612-198">アプリがテスト サーバーまたは運用サーバーにデプロイされると、環境変数を使用して接続文字列をテストまたは運用データベース サーバーに設定できます。</span><span class="sxs-lookup"><span data-stu-id="90612-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="90612-199">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90612-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="90612-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="90612-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="90612-202">LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。</span><span class="sxs-lookup"><span data-stu-id="90612-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="90612-203">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="90612-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="90612-204">LocalDB データベースでは、既定で `C:\Users\<user>\` ディレクトリに `*.mdf` ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90612-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="90612-205">**[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="90612-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![[View] メニュー](sql/_static/ssox.png)

* <span data-ttu-id="90612-207">`Movie` テーブルを右クリックし、 **[デザイナーの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90612-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie テーブルに対して開かれたコンテキスト メニュー](sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](sql/_static/dv.png)

<span data-ttu-id="90612-210">`ID` の横のキー アイコンに注意してください。</span><span class="sxs-lookup"><span data-stu-id="90612-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="90612-211">既定では、EF で主キーに `ID` という名前のプロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90612-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="90612-212">`Movie` テーブルを右クリックし、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90612-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![開いた Movie テーブルにテーブル データが表示されています](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-214">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="90612-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="90612-215">SQLite</span></span>

<span data-ttu-id="90612-216">[SQLite](https://www.sqlite.org/) の Web サイトは次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="90612-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="90612-217">SQLite は埋め込みの全機能を備えた、自己完結型かつ高信頼性でパブリック ドメインの SQL データベース エンジンです。</span><span class="sxs-lookup"><span data-stu-id="90612-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="90612-218">SQLite は、世界で最も使用されているデータベース エンジンです。</span><span class="sxs-lookup"><span data-stu-id="90612-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="90612-219">SQLite データベースを管理および表示するためにダウンロードできるサードパーティ製ツールは多数あります。</span><span class="sxs-lookup"><span data-stu-id="90612-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="90612-220">以下の画像は [DB Browser for SQLite](https://sqlitebrowser.org/) のものです。</span><span class="sxs-lookup"><span data-stu-id="90612-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="90612-221">お気に入りの SQLite ツールがございましたら、そのツールの気に入っている点についてコメントしてください。</span><span class="sxs-lookup"><span data-stu-id="90612-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Movie データベースを示している DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="90612-223">このチュートリアルでは、可能な場合は Entity Framework Core の "*移行*" 機能が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90612-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="90612-224">移行では、データ モデルの変更に合わせてデータベース スキーマが更新されます。</span><span class="sxs-lookup"><span data-stu-id="90612-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="90612-225">ただし、移行では EF Core プロバイダーでサポートされている種類の変更しか行うことができず、SQLite プロバイダーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="90612-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="90612-226">たとえば、列の追加はサポートされていますが、列の削除や変更はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="90612-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="90612-227">列を削除または変更するために移行を作成した場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。</span><span class="sxs-lookup"><span data-stu-id="90612-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="90612-228">これらの制限により、このチュートリアルでは SQLite のスキーマの変更に対しては移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="90612-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="90612-229">代わりに、スキーマが変更されると、データベースが削除され、再作成されます。</span><span class="sxs-lookup"><span data-stu-id="90612-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="90612-230">SQLite の制限に対する回避策としては、テーブル内の何かが変更されたときにテーブルの再構築を実行する移行コードを、手動で作成します。</span><span class="sxs-lookup"><span data-stu-id="90612-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="90612-231">テーブルの再構築には次の作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="90612-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="90612-232">新しいテーブルの作成。</span><span class="sxs-lookup"><span data-stu-id="90612-232">Creating a new table.</span></span>
>* <span data-ttu-id="90612-233">古いテーブルから新しいテーブルへのデータのコピー。</span><span class="sxs-lookup"><span data-stu-id="90612-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="90612-234">古いテーブルの削除。</span><span class="sxs-lookup"><span data-stu-id="90612-234">Dropping the old table.</span></span>
>* <span data-ttu-id="90612-235">新しいテーブルの名前変更。</span><span class="sxs-lookup"><span data-stu-id="90612-235">Renaming the new table.</span></span>
>
><span data-ttu-id="90612-236">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90612-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="90612-237">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="90612-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="90612-238">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="90612-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="90612-239">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="90612-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="90612-240">SQLite ALTER TABLE ステートメント</span><span class="sxs-lookup"><span data-stu-id="90612-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="90612-241">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="90612-241">Seed the database</span></span>

<span data-ttu-id="90612-242">次のコードを使用して、*Models* フォルダーに `SeedData` という名前の新しいクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="90612-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="90612-243">データベースにムービーがある場合、シード初期化子が返され、ムービーは追加されません。</span><span class="sxs-lookup"><span data-stu-id="90612-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="90612-244">シード初期化子の追加</span><span class="sxs-lookup"><span data-stu-id="90612-244">Add the seed initializer</span></span>

<span data-ttu-id="90612-245">*Program.cs* の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="90612-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="90612-246">以前のコードでは、`Main` メソッドが次のように変更されています。</span><span class="sxs-lookup"><span data-stu-id="90612-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="90612-247">依存関係挿入コンテナーからデータベース コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="90612-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="90612-248">`seedData.Initialize` メソッドを呼び出すと、データベース コンテキスト インスタンスに渡されます。</span><span class="sxs-lookup"><span data-stu-id="90612-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="90612-249">seed メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="90612-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="90612-250">[using ステートメント](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)を使用すると、コンテキストが確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="90612-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="90612-251">`Update-Database` が実行されていなかった場合、次の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="90612-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="90612-252">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="90612-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="90612-254">データベース内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="90612-254">Delete all the records in the database.</span></span> <span data-ttu-id="90612-255">ブラウザーの削除リンクを使用するか、[SSOX](xref:tutorials/razor-pages/new-field#ssox) から行います。</span><span class="sxs-lookup"><span data-stu-id="90612-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="90612-256">`Startup` クラスでメソッドを呼び出してアプリを強制的に初期化して、シード メソッドが実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="90612-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="90612-257">強制的に初期化するには、IIS Express を停止してから再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90612-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="90612-258">次の方法のいずれかを使用して、IIS を停止してから再起動します。</span><span class="sxs-lookup"><span data-stu-id="90612-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="90612-259">通知領域にある IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="90612-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express システム トレイ アイコン](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](sql/_static/stopIIS.png)

    * <span data-ttu-id="90612-262">非デバッグ モードでアプリが実行されている場合は、<kbd>F5</kbd> キーを押してデバッグ モードで実行します。</span><span class="sxs-lookup"><span data-stu-id="90612-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="90612-263">デバッグ モードでアプリが実行されている場合は、デバッガーを停止して、<kbd>F5</kbd> キーを押します。</span><span class="sxs-lookup"><span data-stu-id="90612-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-264">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="90612-265">データベース内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="90612-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="90612-266">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="90612-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="90612-267">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="90612-267">The app shows the seeded data:</span></span>

![ムービー データが表示された、Chrome で開かれているムービー アプリケーション](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="90612-269">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="90612-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="90612-270">[前へ:スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)
> [次:ページの更新](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="90612-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90612-271">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="90612-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="90612-272">`RazorPagesMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="90612-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="90612-273">データベース コンテキストは、*Startup.cs* の `ConfigureServices` メソッドで [依存性の注入](xref:fundamentals/dependency-injection)コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="90612-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="90612-275">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="90612-276">`ConfigureServices` で使用されているメソッドの詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="90612-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="90612-277">[ASP.NET Core での `CookiePolicyOptions` 用の EU の一般データ保護規制 (GDPR) のサポート](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="90612-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="90612-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="90612-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="90612-279">ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムでは `ConnectionString` キーが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="90612-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="90612-280">ローカルで開発する場合は、構成によって *appsettings.json* ファイルから接続文字列が取得されます。</span><span class="sxs-lookup"><span data-stu-id="90612-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="90612-282">生成される接続文字列は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="90612-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="90612-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90612-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90612-284">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="90612-285">アプリがテスト サーバーまたは運用サーバーにデプロイされると、環境変数を使用して接続文字列をテストまたは運用データベース サーバーに設定できます。</span><span class="sxs-lookup"><span data-stu-id="90612-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="90612-286">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90612-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="90612-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="90612-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="90612-289">LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。</span><span class="sxs-lookup"><span data-stu-id="90612-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="90612-290">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="90612-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="90612-291">LocalDB データベースでは、既定で `C:/Users/<user/>` ディレクトリに `*.mdf` ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90612-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="90612-292">**[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="90612-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![[View] メニュー](sql/_static/ssox.png)

* <span data-ttu-id="90612-294">`Movie` テーブルを右クリックし、 **[デザイナーの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90612-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie テーブルで開かれたコンテキスト メニュー](sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](sql/_static/dv.png)

<span data-ttu-id="90612-297">`ID` の横のキー アイコンに注意してください。</span><span class="sxs-lookup"><span data-stu-id="90612-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="90612-298">既定では、EF で主キーに `ID` という名前のプロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="90612-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="90612-299">`Movie` テーブルを右クリックし、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="90612-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![開いた Movie テーブルにテーブル データが表示されています](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="90612-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90612-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90612-302">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="90612-303">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="90612-303">Seed the database</span></span>

<span data-ttu-id="90612-304">次のコードを使用して、*Models* フォルダーに `SeedData` という名前の新しいクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="90612-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="90612-305">データベースにムービーがある場合、シード初期化子が返され、ムービーは追加されません。</span><span class="sxs-lookup"><span data-stu-id="90612-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="90612-306">シード初期化子の追加</span><span class="sxs-lookup"><span data-stu-id="90612-306">Add the seed initializer</span></span>

<span data-ttu-id="90612-307">*Program.cs* の内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="90612-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="90612-308">以前のコードでは、`Main` メソッドが次のように変更されています。</span><span class="sxs-lookup"><span data-stu-id="90612-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="90612-309">依存関係挿入コンテナーからデータベース コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="90612-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="90612-310">`seedData.Initialize` メソッドを呼び出すと、データベース コンテキスト インスタンスに渡されます。</span><span class="sxs-lookup"><span data-stu-id="90612-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="90612-311">seed メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="90612-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="90612-312">[using ステートメント](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)を使用すると、コンテキストが確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="90612-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="90612-313">運用アプリは `Database.Migrate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="90612-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="90612-314">これは、`Update-Database` が実行されていないとき、前述のコードに追加され、次の例外を阻止します。</span><span class="sxs-lookup"><span data-stu-id="90612-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="90612-315">SqlException:Cannot open database "RazorPagesMovieContext-21" requested by the login. (SqlException: ログインで要求されている "RazorPagesMovieContext-21" データベースを開くことができませんでした。)</span><span class="sxs-lookup"><span data-stu-id="90612-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="90612-316">The login failed.\(ログインに失敗しました。\)</span><span class="sxs-lookup"><span data-stu-id="90612-316">The login failed.</span></span>
<span data-ttu-id="90612-317">Login failed for user 'user name'.\(ユーザー 'ユーザー名' はログインできませんでした。\)</span><span class="sxs-lookup"><span data-stu-id="90612-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="90612-318">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="90612-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90612-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90612-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="90612-320">データベース内のすべてのレコードを削除します。</span><span class="sxs-lookup"><span data-stu-id="90612-320">Delete all the records in the database.</span></span> <span data-ttu-id="90612-321">これはブラウザーの削除リンクで行うか、[SSOX](xref:tutorials/razor-pages/new-field#ssox) から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="90612-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="90612-322">`Startup` クラスでメソッドを呼び出してアプリを強制的に初期化して、シード メソッドが実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="90612-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="90612-323">強制的に初期化するには、IIS Express を停止してから再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90612-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="90612-324">これは次の方法のいずれかを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="90612-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="90612-325">通知領域にある IIS Express システム トレイ アイコンを右クリックし、 **[終了]** または **[サイトの停止]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="90612-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express システム トレイ アイコン](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](sql/_static/stopIIS.png)

    * <span data-ttu-id="90612-328">非デバッグ モードでアプリが実行されている場合は、<kbd>F5</kbd> キーを押してデバッグ モードで実行します。</span><span class="sxs-lookup"><span data-stu-id="90612-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="90612-329">デバッグ モードでアプリが実行されている場合は、デバッガーを停止して、<kbd>F5</kbd> キーを押します。</span><span class="sxs-lookup"><span data-stu-id="90612-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="90612-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90612-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="90612-331">データベース内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="90612-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="90612-332">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="90612-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90612-333">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="90612-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="90612-334">データベース内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。</span><span class="sxs-lookup"><span data-stu-id="90612-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="90612-335">アプリを停止および起動して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="90612-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="90612-336">アプリにシードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="90612-336">The app shows the seeded data:</span></span>

![ムービー データが表示された、Chrome で開かれているムービー アプリケーション](sql/_static/m55https.png)

<span data-ttu-id="90612-338">次のチュートリアルでは、データの表示をクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="90612-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90612-339">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="90612-339">Additional resources</span></span>

* [<span data-ttu-id="90612-340">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="90612-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="90612-341">[前へ:スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)
> [次:ページの更新](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="90612-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

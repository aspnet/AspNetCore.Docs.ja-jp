---
title: パート 7、新しいフィールドの追加
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 7。
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101147"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="3b66d-103">パート 7、ASP.NET Core で Razor ページでの新しいフィールドの追加</span><span class="sxs-lookup"><span data-stu-id="3b66d-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="3b66d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3b66d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3b66d-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3b66d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3b66d-106">このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="3b66d-107">モデルに新しいフィールドを追加する。</span><span class="sxs-lookup"><span data-stu-id="3b66d-107">Add a new field to the model.</span></span>
* <span data-ttu-id="3b66d-108">新しいフィールド スキーマの変更をデータベースに移行する。</span><span class="sxs-lookup"><span data-stu-id="3b66d-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="3b66d-109">EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="3b66d-110">データベースに [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) テーブルが追加され、データベースのスキーマが生成元のモデル クラスと同期しているかどうかが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="3b66d-111">モデル クラスがデータベースと同期されていない場合、EF から例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="3b66d-112">スキーマとモデルが同期中であることが自動的に検証されるようにすると、整合性のないデータベース コードの問題を発見しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="3b66d-113">ムービー モデルへの評価プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="3b66d-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="3b66d-114">*Models/Movie.cs* ファイルを開き、`Rating` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="3b66d-115">アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-115">Build the app.</span></span>

1. <span data-ttu-id="3b66d-116">*Pages/Movies/Index.cshtml* を編集し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="3b66d-117">次のページを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-117">Update the following pages:</span></span>
   1. <span data-ttu-id="3b66d-118">[削除] と [詳細] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="3b66d-119">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) を `Rating` フィールドを使用して更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="3b66d-120">[編集] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="3b66d-121">データベースを更新して新しいフィールドが含まれるようになるまでアプリは動作しません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="3b66d-122">データベースを更新せずにアプリを実行すると、次の `SqlException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="3b66d-123">`SqlException` 例外が発生するのは、更新された Movie モデル クラスが、データベースの Movie テーブルのスキーマと異なるためです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="3b66d-124">データベース テーブルに `Rating` 列はありません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="3b66d-125">このエラーを解決するための手法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="3b66d-126">Entity Framework に、新しいモデル クラス スキーマを使用してデータベースを自動的にドロップさせ、再作成させます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="3b66d-127">この手法は、開発周期の早い段階で便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="3b66d-128">これの欠点は、データベースで既存のデータが失われることです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="3b66d-129">実稼働データベースでこの方法は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="3b66d-130">スキーマの変更時にデータベースを削除し、初期化子を使用して、データベースにテスト データを自動的にシードすることは、多くの場合、アプリケーションを開発する上で有益な方法となります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="3b66d-131">モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="3b66d-132">この手法の長所は、データが維持されることです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="3b66d-133">この変更は手動で行うか、データベース変更スクリプトを作成して行います。</span><span class="sxs-lookup"><span data-stu-id="3b66d-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="3b66d-134">Code First Migrations を使用して、データベース スキーマを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="3b66d-135">このチュートリアルでは、Code First Migrations を利用します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="3b66d-136">新しい列に値を提供するように、`SeedData` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="3b66d-137">下に変更のサンプルがありますが、`new Movie` ブロックごとにこの変更を行ってください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="3b66d-138">[完成した SeedData.cs ファイル](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="3b66d-139">ソリューションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b66d-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b66d-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="3b66d-141">評価フィールドの移行を追加する</span><span class="sxs-lookup"><span data-stu-id="3b66d-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="3b66d-142">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="3b66d-143">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="3b66d-144">`Add-Migration` コマンドにより、フレームワークに次の指示があります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="3b66d-145">`Movie` モデルを、`Movie` データベースのスキーマと比較します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="3b66d-146">データベース スキーマを新しいモデルに移行するコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="3b66d-147">"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="3b66d-148">移行ファイルには意味のある名前を使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="3b66d-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="3b66d-149">`Update-Database` コマンドを使うと、データベースにスキーマ変更を適用し、既存のデータを保持するようにフレームワークに指示できます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="3b66d-150">データベース内のすべてのレコードを削除すると、初期化子はデータベースにデータを初期投入し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="3b66d-151">これはブラウザーの削除リンクで行うか、[Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="3b66d-152">別のオプションとしては、データベースを削除してから、移行を使用することで、データベースを再作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="3b66d-153">SSOX 内でデータベースを削除するには:</span><span class="sxs-lookup"><span data-stu-id="3b66d-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="3b66d-154">SSOX でデータベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="3b66d-155">データベースを右クリックし、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="3b66d-156">**[既存の接続を閉じる]** をチェックします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="3b66d-157">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-157">Select **OK**.</span></span>
1. <span data-ttu-id="3b66d-158">[PMC](xref:tutorials/razor-pages/new-field#pmc) でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b66d-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b66d-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="3b66d-160">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="3b66d-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="3b66d-161">このチュートリアルでは、可能な場合は Entity Framework Core の "*移行*" 機能を使います。</span><span class="sxs-lookup"><span data-stu-id="3b66d-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="3b66d-162">移行では、データ モデルの変更に合わせてデータベース スキーマが更新されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="3b66d-163">ただし、移行では EF Core プロバイダーでサポートされている種類の変更しか行うことができず、SQLite プロバイダーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="3b66d-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="3b66d-164">たとえば、列の追加はサポートされていますが、列の削除や変更はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="3b66d-165">列を削除または変更するために移行を作成した場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="3b66d-166">これらの制限により、このチュートリアルでは SQLite のスキーマの変更に対しては移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="3b66d-167">代わりに、スキーマを変更するときは、データベースを削除して再作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="3b66d-168">SQLite の制限に対する回避策としては、テーブル内の何かが変更されたときにテーブルの再構築を実行する移行コードを、手動で作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="3b66d-169">テーブルの再構築には次の作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="3b66d-170">新しいテーブルの作成。</span><span class="sxs-lookup"><span data-stu-id="3b66d-170">Creating a new table.</span></span>
>* <span data-ttu-id="3b66d-171">古いテーブルから新しいテーブルへのデータのコピー。</span><span class="sxs-lookup"><span data-stu-id="3b66d-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="3b66d-172">古いテーブルの削除。</span><span class="sxs-lookup"><span data-stu-id="3b66d-172">Dropping the old table.</span></span>
>* <span data-ttu-id="3b66d-173">新しいテーブルの名前変更。</span><span class="sxs-lookup"><span data-stu-id="3b66d-173">Renaming the new table.</span></span>
>
><span data-ttu-id="3b66d-174">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="3b66d-175">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="3b66d-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="3b66d-176">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="3b66d-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="3b66d-177">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="3b66d-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="3b66d-178">SQLite ALTER TABLE ステートメント</span><span class="sxs-lookup"><span data-stu-id="3b66d-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="3b66d-179">移行フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="3b66d-180">次のコマンドを使用して、データベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="3b66d-181">アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="3b66d-182">データベースがシード処理されない場合は、`SeedData.Initialize` メソッドでブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b66d-183">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3b66d-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3b66d-184">[前へ:検索の追加](xref:tutorials/razor-pages/search)
> [次へ:検証の追加](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="3b66d-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="3b66d-185">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3b66d-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3b66d-186">このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="3b66d-187">モデルに新しいフィールドを追加する。</span><span class="sxs-lookup"><span data-stu-id="3b66d-187">Add a new field to the model.</span></span>
* <span data-ttu-id="3b66d-188">新しいフィールド スキーマの変更をデータベースに移行する。</span><span class="sxs-lookup"><span data-stu-id="3b66d-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="3b66d-189">EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="3b66d-190">データベースに [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) テーブルが追加され、データベースのスキーマが生成元のモデル クラスと同期しているかどうかが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="3b66d-191">モデル クラスがデータベースと同期されていない場合、EF から例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="3b66d-192">スキーマとモデルが同期中であることが自動的に検証されるようにすると、整合性のないデータベース コードの問題を発見しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="3b66d-193">ムービー モデルへの評価プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="3b66d-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="3b66d-194">*Models/Movie.cs* ファイルを開き、`Rating` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="3b66d-195">アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-195">Build the app.</span></span>

1. <span data-ttu-id="3b66d-196">*Pages/Movies/Index.cshtml* を編集し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="3b66d-197">次のページを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-197">Update the following pages:</span></span>
   1. <span data-ttu-id="3b66d-198">[削除] と [詳細] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="3b66d-199">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) を `Rating` フィールドを使用して更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="3b66d-200">[編集] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="3b66d-201">データベースを更新して新しいフィールドが含まれるようになるまでアプリは動作しません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="3b66d-202">データベースを更新せずにアプリを実行すると、次の `SqlException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="3b66d-203">`SqlException` 例外が発生するのは、更新された Movie モデル クラスが、データベースの Movie テーブルのスキーマと異なるためです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="3b66d-204">データベース テーブルに `Rating` 列はありません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="3b66d-205">このエラーを解決するための手法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="3b66d-206">Entity Framework に、新しいモデル クラス スキーマを使用してデータベースを自動的にドロップさせ、再作成させます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="3b66d-207">この手法は、開発周期の早い段階で便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="3b66d-208">これの欠点は、データベースで既存のデータが失われることです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="3b66d-209">実稼働データベースでこの方法は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="3b66d-210">スキーマの変更時にデータベースを削除し、初期化子を使用して、データベースにテスト データを自動的にシードすることは、多くの場合、アプリケーションを開発する上で有益な方法となります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="3b66d-211">モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="3b66d-212">この手法の長所は、データが維持されることです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="3b66d-213">この変更は手動で行うか、データベース変更スクリプトを作成して行います。</span><span class="sxs-lookup"><span data-stu-id="3b66d-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="3b66d-214">Code First Migrations を使用して、データベース スキーマを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="3b66d-215">このチュートリアルでは、Code First Migrations を利用します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="3b66d-216">新しい列に値を提供するように、`SeedData` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="3b66d-217">下に変更のサンプルがありますが、`new Movie` ブロックごとにこの変更を行ってください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="3b66d-218">[完成した SeedData.cs ファイル](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="3b66d-219">ソリューションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b66d-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b66d-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="3b66d-221">評価フィールドの移行を追加する</span><span class="sxs-lookup"><span data-stu-id="3b66d-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="3b66d-222">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="3b66d-223">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="3b66d-224">`Add-Migration` コマンドにより、フレームワークに次の指示があります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="3b66d-225">`Movie` モデルを、`Movie` データベースのスキーマと比較します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="3b66d-226">データベース スキーマを新しいモデルに移行するコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="3b66d-227">"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="3b66d-228">移行ファイルには意味のある名前を使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="3b66d-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="3b66d-229">`Update-Database` コマンドを使うと、データベースにスキーマ変更を適用し、既存のデータを保持するようにフレームワークに指示できます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="3b66d-230">データベース内のすべてのレコードを削除すると、初期化子はデータベースにデータを初期投入し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="3b66d-231">これはブラウザーの削除リンクで行うか、[Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="3b66d-232">別のオプションとしては、データベースを削除してから、移行を使用することで、データベースを再作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="3b66d-233">SSOX 内でデータベースを削除するには:</span><span class="sxs-lookup"><span data-stu-id="3b66d-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="3b66d-234">SSOX でデータベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="3b66d-235">データベースを右クリックし、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="3b66d-236">**[既存の接続を閉じる]** をチェックします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="3b66d-237">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-237">Select **OK**.</span></span>
* <span data-ttu-id="3b66d-238">[PMC](xref:tutorials/razor-pages/new-field#pmc) でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b66d-239">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b66d-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="3b66d-240">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="3b66d-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="3b66d-241">このチュートリアルでは、可能な場合は Entity Framework Core の "*移行*" 機能を使います。</span><span class="sxs-lookup"><span data-stu-id="3b66d-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="3b66d-242">移行では、データ モデルの変更に合わせてデータベース スキーマが更新されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="3b66d-243">ただし、移行では EF Core プロバイダーでサポートされている種類の変更しか行うことができず、SQLite プロバイダーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="3b66d-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="3b66d-244">たとえば、列の追加はサポートされていますが、列の削除や変更はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="3b66d-245">列を削除または変更するために移行を作成した場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="3b66d-246">これらの制限により、このチュートリアルでは SQLite のスキーマの変更に対しては移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="3b66d-247">代わりに、スキーマを変更するときは、データベースを削除して再作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="3b66d-248">SQLite の制限に対する回避策としては、テーブル内の何かが変更されたときにテーブルの再構築を実行する移行コードを、手動で作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="3b66d-249">テーブルの再構築には次の作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="3b66d-250">新しいテーブルの作成。</span><span class="sxs-lookup"><span data-stu-id="3b66d-250">Creating a new table.</span></span>
>* <span data-ttu-id="3b66d-251">古いテーブルから新しいテーブルへのデータのコピー。</span><span class="sxs-lookup"><span data-stu-id="3b66d-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="3b66d-252">古いテーブルの削除。</span><span class="sxs-lookup"><span data-stu-id="3b66d-252">Dropping the old table.</span></span>
>* <span data-ttu-id="3b66d-253">新しいテーブルの名前変更。</span><span class="sxs-lookup"><span data-stu-id="3b66d-253">Renaming the new table.</span></span>
>
><span data-ttu-id="3b66d-254">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="3b66d-255">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="3b66d-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="3b66d-256">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="3b66d-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="3b66d-257">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="3b66d-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="3b66d-258">SQLite ALTER TABLE ステートメント</span><span class="sxs-lookup"><span data-stu-id="3b66d-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="3b66d-259">移行フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="3b66d-260">次のコマンドを使用して、データベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="3b66d-261">アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="3b66d-262">データベースがシード処理されない場合は、`SeedData.Initialize` メソッドでブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b66d-263">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3b66d-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3b66d-264">[前へ:検索の追加](xref:tutorials/razor-pages/search)
> [次へ:検証の追加](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="3b66d-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3b66d-265">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3b66d-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3b66d-266">このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="3b66d-267">モデルに新しいフィールドを追加する。</span><span class="sxs-lookup"><span data-stu-id="3b66d-267">Add a new field to the model.</span></span>
* <span data-ttu-id="3b66d-268">新しいフィールド スキーマの変更をデータベースに移行する。</span><span class="sxs-lookup"><span data-stu-id="3b66d-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="3b66d-269">EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="3b66d-270">データベースに [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) テーブルが追加され、データベースのスキーマが生成元のモデル クラスと同期しているかどうかが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="3b66d-271">モデル クラスがデータベースと同期されていない場合、EF から例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="3b66d-272">スキーマとモデルが同期中であることが自動的に検証されるようにすると、整合性のないデータベース コードの問題を発見しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="3b66d-273">ムービー モデルへの評価プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="3b66d-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="3b66d-274">*Models/Movie.cs* ファイルを開き、`Rating` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="3b66d-275">アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-275">Build the app.</span></span>

<span data-ttu-id="3b66d-276">*Pages/Movies/Index.cshtml* を編集し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="3b66d-277">次のページを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-277">Update the following pages:</span></span>

* <span data-ttu-id="3b66d-278">[削除] と [詳細] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="3b66d-279">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) を `Rating` フィールドを使用して更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="3b66d-280">[編集] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="3b66d-281">データベースを更新して新しいフィールドが含まれるようになるまでアプリは動作しません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="3b66d-282">ここでアプリが実行されると、アプリによって `SqlException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="3b66d-283">このエラーが表示されるのは、更新された Movie モデル クラスがデータベースの Movie テーブルのスキーマと異なるためです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="3b66d-284">データベース テーブルに `Rating` 列はありません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="3b66d-285">このエラーを解決するための手法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="3b66d-286">Entity Framework に、新しいモデル クラス スキーマを使用してデータベースを自動的にドロップさせ、再作成させます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="3b66d-287">この手法は、開発周期の早い段階で便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="3b66d-288">これの欠点は、データベースで既存のデータが失われることです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="3b66d-289">実稼働データベースでこの方法は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="3b66d-290">スキーマの変更時にデータベースを削除し、初期化子を使用して、データベースにテスト データを自動的にシードすることは、多くの場合、アプリケーションを開発する上で有益な方法となります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="3b66d-291">モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="3b66d-292">この手法の長所は、データが維持されることです。</span><span class="sxs-lookup"><span data-stu-id="3b66d-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="3b66d-293">この変更は手動で行うか、データベース変更スクリプトを作成して行います。</span><span class="sxs-lookup"><span data-stu-id="3b66d-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="3b66d-294">Code First Migrations を使用して、データベース スキーマを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="3b66d-295">このチュートリアルでは、Code First Migrations を利用します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="3b66d-296">新しい列に値を提供するように、`SeedData` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="3b66d-297">下に変更のサンプルがありますが、`new Movie` ブロックごとにこの変更を行ってください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="3b66d-298">[完成した SeedData.cs ファイル](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="3b66d-299">ソリューションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b66d-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b66d-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="3b66d-301">評価フィールドの移行を追加する</span><span class="sxs-lookup"><span data-stu-id="3b66d-301">Add a migration for the rating field</span></span>

<span data-ttu-id="3b66d-302">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="3b66d-303">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="3b66d-304">`Add-Migration` コマンドにより、フレームワークに次の指示があります。</span><span class="sxs-lookup"><span data-stu-id="3b66d-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="3b66d-305">`Movie` モデルを、`Movie` データベースのスキーマと比較します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="3b66d-306">データベース スキーマを新しいモデルに移行するコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="3b66d-307">"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="3b66d-308">移行ファイルには意味のある名前を使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="3b66d-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="3b66d-309">フレームワークは、データベースにスキーマ変更を適用するように、`Update-Database` コマンドから指示されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="3b66d-310">データベース内のすべてのレコードを削除すると、初期化子はデータベースにデータを初期投入し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="3b66d-311">これはブラウザーの削除リンクで行うか、[Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="3b66d-312">別のオプションとしては、データベースを削除してから、移行を使用することで、データベースを再作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="3b66d-313">SSOX 内でデータベースを削除するには:</span><span class="sxs-lookup"><span data-stu-id="3b66d-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="3b66d-314">SSOX でデータベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="3b66d-315">データベースを右クリックし、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="3b66d-316">**[既存の接続を閉じる]** をチェックします。</span><span class="sxs-lookup"><span data-stu-id="3b66d-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="3b66d-317">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-317">Select **OK**.</span></span>
* <span data-ttu-id="3b66d-318">[PMC](xref:tutorials/razor-pages/new-field#pmc) でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b66d-319">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b66d-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="3b66d-320">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="3b66d-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="3b66d-321">このチュートリアルでは、可能な場合は Entity Framework Core の "*移行*" 機能を使います。</span><span class="sxs-lookup"><span data-stu-id="3b66d-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="3b66d-322">移行では、データ モデルの変更に合わせてデータベース スキーマが更新されます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="3b66d-323">ただし、移行では EF Core プロバイダーでサポートされている種類の変更しか行うことができず、SQLite プロバイダーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="3b66d-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="3b66d-324">たとえば、列の追加はサポートされていますが、列の削除や変更はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="3b66d-325">列を削除または変更するために移行を作成した場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="3b66d-326">これらの制限により、このチュートリアルでは SQLite のスキーマの変更に対しては移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="3b66d-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="3b66d-327">代わりに、スキーマを変更するときは、データベースを削除して再作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="3b66d-328">SQLite の制限に対する回避策としては、テーブル内の何かが変更されたときにテーブルの再構築を実行する移行コードを、手動で作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="3b66d-329">テーブルの再構築には次の作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b66d-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="3b66d-330">新しいテーブルの作成。</span><span class="sxs-lookup"><span data-stu-id="3b66d-330">Creating a new table.</span></span>
>* <span data-ttu-id="3b66d-331">古いテーブルから新しいテーブルへのデータのコピー。</span><span class="sxs-lookup"><span data-stu-id="3b66d-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="3b66d-332">古いテーブルの削除。</span><span class="sxs-lookup"><span data-stu-id="3b66d-332">Dropping the old table.</span></span>
>* <span data-ttu-id="3b66d-333">新しいテーブルの名前変更。</span><span class="sxs-lookup"><span data-stu-id="3b66d-333">Renaming the new table.</span></span>
>
><span data-ttu-id="3b66d-334">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b66d-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="3b66d-335">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="3b66d-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="3b66d-336">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="3b66d-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="3b66d-337">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="3b66d-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="3b66d-338">SQLite ALTER TABLE ステートメント</span><span class="sxs-lookup"><span data-stu-id="3b66d-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="3b66d-339">データベースを削除し、移行を使ってデータベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="3b66d-340">データベースを削除するには、データベース ファイル (*MvcMovie.db*) を削除します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="3b66d-341">次に、`ef database update` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="3b66d-342">アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="3b66d-343">データベースがシード処理されない場合は、`SeedData.Initialize` メソッドでブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="3b66d-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b66d-344">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3b66d-344">Additional resources</span></span>

* [<span data-ttu-id="3b66d-345">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="3b66d-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="3b66d-346">[前へ:検索の追加](xref:tutorials/razor-pages/search)
> [次へ:検証の追加](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="3b66d-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
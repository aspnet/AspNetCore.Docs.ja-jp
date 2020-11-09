---
title: 'パート 6、ASP.NET Core の Razor ページと EF Core - 関連データの読み込み'
author: rick-anderson
description: 'Razor ページと Entity Framework チュートリアル シリーズのパート 6。'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061068"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="07406-103">パート 6、ASP.NET Core の Razor ページと EF Core - 関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="07406-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="07406-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="07406-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07406-105">このチュートリアルでは、関連データを読み取って表示する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="07406-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="07406-106">関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。</span><span class="sxs-lookup"><span data-stu-id="07406-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="07406-107">以下の図は、このチュートリアルの完成したページを示しています。</span><span class="sxs-lookup"><span data-stu-id="07406-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index30.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="07406-110">一括読み込み、明示的読み込み、遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="07406-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="07406-111">EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="07406-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="07406-112">[一括読み込み](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="07406-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="07406-113">一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。</span><span class="sxs-lookup"><span data-stu-id="07406-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="07406-114">エンティティが読み取られるときに、その関連データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="07406-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="07406-115">これは通常、必要なデータをすべて取得する 1 つの結合クエリになります。</span><span class="sxs-lookup"><span data-stu-id="07406-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="07406-116">EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="07406-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="07406-117">複数のクエリを発行することは、1 つの巨大なクエリよりも効率的である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="07406-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="07406-118">一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="07406-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="07406-120">一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。</span><span class="sxs-lookup"><span data-stu-id="07406-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="07406-121">メイン クエリに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="07406-121">One query for the main query</span></span> 
  * <span data-ttu-id="07406-122">読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="07406-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="07406-123">`Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。</span><span class="sxs-lookup"><span data-stu-id="07406-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="07406-124">"修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="07406-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="07406-125">`Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。</span><span class="sxs-lookup"><span data-stu-id="07406-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="07406-127">**注:** EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="07406-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="07406-128">ナビゲーション プロパティのデータが明示的に含まれ *ない* 場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="07406-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="07406-129">[明示的読み込み](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="07406-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="07406-130">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="07406-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="07406-131">必要なときに関連するデータを取得するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07406-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="07406-132">分離したクエリによる明示的読み込みにより、複数のクエリがデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="07406-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="07406-133">明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="07406-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="07406-134">明示的読み込みを行うには、`Load` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="07406-135">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="07406-135">For example:</span></span>

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="07406-137">[遅延読み込み](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="07406-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="07406-138">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="07406-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="07406-139">ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="07406-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="07406-140">初めてナビゲーション プロパティにアクセスされるたびに、クエリがデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="07406-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="07406-141">開発者が N + 1 パターンを使用して、親を読み込んで子を列挙する場合など、遅延読み込みでパフォーマンスが低下することがあります。</span><span class="sxs-lookup"><span data-stu-id="07406-141">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="07406-142">Course ページの作成</span><span class="sxs-lookup"><span data-stu-id="07406-142">Create Course pages</span></span>

<span data-ttu-id="07406-143">`Course` エンティティには、関連する `Department` エンティティを含むナビゲーション プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="07406-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="07406-145">コースに割り当てられている部署の名前を表示するには</span><span class="sxs-lookup"><span data-stu-id="07406-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="07406-146">関連する `Department` エンティティを `Course.Department` ナビゲーション プロパティに読み込みます。</span><span class="sxs-lookup"><span data-stu-id="07406-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="07406-147">`Department` エンティティの `Name` プロパティから名前を取得します。</span><span class="sxs-lookup"><span data-stu-id="07406-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="07406-148">Course ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="07406-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07406-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07406-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07406-150">次の例外を除き、「[Student ページのスキャフォールディング](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。</span><span class="sxs-lookup"><span data-stu-id="07406-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="07406-151">*Pages/Courses* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="07406-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="07406-152">モデル クラスに `Course` を使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="07406-153">新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07406-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07406-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07406-155">*Pages/Courses* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="07406-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="07406-156">次のコマンドを実行して、Course モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="07406-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="07406-157">**Windows の場合:**</span><span class="sxs-lookup"><span data-stu-id="07406-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="07406-158">**Linux または macOS の場合:**</span><span class="sxs-lookup"><span data-stu-id="07406-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="07406-159">*Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="07406-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="07406-160">スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。</span><span class="sxs-lookup"><span data-stu-id="07406-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="07406-161">`Include` メソッドが一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="07406-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="07406-162">アプリを実行し、 **[Courses]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="07406-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="07406-163">Department 列に `DepartmentID` が表示されますが、これには役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="07406-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="07406-164">部署名の表示</span><span class="sxs-lookup"><span data-stu-id="07406-164">Display the department name</span></span>

<span data-ttu-id="07406-165">次のコードを使用して Pages/Courses/Index.cshtml.cs を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="07406-166">上記のコードでは、`Course` プロパティを `Courses` に変更し、`AsNoTracking` を追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="07406-167">`AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="07406-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="07406-168">エンティティは現在のコンテキストでは更新されないため、追跡する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="07406-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="07406-169">次のコードを使用して *Pages/Courses/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="07406-170">スキャフォールディング コードに、次の変更が行われました。</span><span class="sxs-lookup"><span data-stu-id="07406-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="07406-171">`Course` プロパティ名は `Courses` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="07406-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="07406-172">`CourseID` プロパティ値を示す **Number** 列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="07406-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="07406-173">既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。</span><span class="sxs-lookup"><span data-stu-id="07406-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="07406-174">ただし、このケースでは、主キーは意味があります。</span><span class="sxs-lookup"><span data-stu-id="07406-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="07406-175">部門名が表示されるように、 **Department** 列を変更しました。</span><span class="sxs-lookup"><span data-stu-id="07406-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="07406-176">コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="07406-177">アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="07406-179">Select を使用した関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="07406-179">Loading related data with Select</span></span>

<span data-ttu-id="07406-180">`OnGetAsync` メソッドでは、`Include` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="07406-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="07406-181">`Select` メソッドは、必要な関連データだけを読み込む代替手段です。</span><span class="sxs-lookup"><span data-stu-id="07406-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="07406-182">`Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。</span><span class="sxs-lookup"><span data-stu-id="07406-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="07406-183">コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。</span><span class="sxs-lookup"><span data-stu-id="07406-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="07406-184">次のコードは、`Select` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="07406-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="07406-185">上記のコードではエンティティ型が返されないため、追跡は行われません。</span><span class="sxs-lookup"><span data-stu-id="07406-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="07406-186">EF の追跡の詳細については、「[追跡と追跡なしのクエリ](/ef/core/querying/tracking)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07406-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="07406-187">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="07406-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="07406-188">完全な例については、[IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07406-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="07406-189">Instructor ページの作成</span><span class="sxs-lookup"><span data-stu-id="07406-189">Create Instructor pages</span></span>

<span data-ttu-id="07406-190">このセクションでは、Instructor ページをスキャフォールディングし、関連する Courses と Enrollments を Instructors Index ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="07406-191">![Instructors/Index ページ](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="07406-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="07406-192">このページは、次の方法で関連データを読み取って表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="07406-193">インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。</span><span class="sxs-lookup"><span data-stu-id="07406-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="07406-194">`Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="07406-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="07406-195">`OfficeAssignment` エンティティには一括読み込みが使用されています。</span><span class="sxs-lookup"><span data-stu-id="07406-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="07406-196">一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。</span><span class="sxs-lookup"><span data-stu-id="07406-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="07406-197">この場合、インストラクターへのオフィスの割り当てが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="07406-198">ユーザーがインストラクターを選択すると、関連する `Course` エンティティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="07406-199">`Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="07406-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="07406-200">`Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。</span><span class="sxs-lookup"><span data-stu-id="07406-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="07406-201">このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="07406-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="07406-202">この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="07406-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="07406-203">ユーザーがコースを選択すると、`Enrollments` エンティティからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="07406-204">上の図では、受講者名とグレードが表示されています。</span><span class="sxs-lookup"><span data-stu-id="07406-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="07406-205">`Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="07406-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="07406-206">ビュー モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="07406-206">Create a view model</span></span>

<span data-ttu-id="07406-207">Instructors ページには、3 つの異なるテーブルからのデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="07406-208">3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが必要です。</span><span class="sxs-lookup"><span data-stu-id="07406-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="07406-209">次のコードを使用して、 *SchoolViewModels/InstructorIndexData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="07406-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="07406-210">Instructor ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="07406-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07406-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07406-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07406-212">次の例外を除き、「[Student ページのスキャフォールディング](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。</span><span class="sxs-lookup"><span data-stu-id="07406-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="07406-213">*Pages/Instructors* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="07406-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="07406-214">モデル クラスに `Instructor` を使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="07406-215">新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07406-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07406-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07406-217">*Pages/Instructors* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="07406-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="07406-218">次のコマンドを実行して、Instructor モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="07406-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="07406-219">**Windows の場合:**</span><span class="sxs-lookup"><span data-stu-id="07406-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="07406-220">**Linux または macOS の場合** :</span><span class="sxs-lookup"><span data-stu-id="07406-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="07406-221">更新する前にスキャフォールディング ページがどのように表示されるかを確認するには、アプリを実行し、Instructors ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="07406-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="07406-222">次のコードを使用して *Pages/Instructors/Index.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="07406-223">`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="07406-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="07406-224">*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。</span><span class="sxs-lookup"><span data-stu-id="07406-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="07406-225">このコードでは、次のナビゲーション プロパティに一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="07406-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="07406-226">`CourseAssignments` と `Course` に対する `Include` メソッドと `ThenInclude` メソッドの繰り返しに注意してください。</span><span class="sxs-lookup"><span data-stu-id="07406-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="07406-227">この繰り返しは、`Course` エンティティの 2 つのナビゲーション プロパティに一括読み込みを指定するために必要です。</span><span class="sxs-lookup"><span data-stu-id="07406-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="07406-228">次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。</span><span class="sxs-lookup"><span data-stu-id="07406-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="07406-229">選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="07406-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="07406-230">ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="07406-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="07406-231">`Where` メソッドはコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="07406-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="07406-232">ただし、この場合、フィルターによってエンティティが 1 つ選択されます。そのため、コレクションを 1 つの `Instructor` エンティティに変換する目的で `Single` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="07406-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="07406-233">`Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="07406-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="07406-234">`CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="07406-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="07406-236">コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="07406-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="07406-237">コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="07406-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="07406-238">代わりに、コレクションが空の場合に既定値 (この場合は null) を返す `SingleOrDefault` を使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="07406-239">次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="07406-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="07406-240">Instructors/Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="07406-240">Update the instructors Index page</span></span>

<span data-ttu-id="07406-241">次のコードを使用して *Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="07406-242">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="07406-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="07406-243">`page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="07406-244">`"{id:int?}"` はルート テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="07406-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="07406-245">ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。</span><span class="sxs-lookup"><span data-stu-id="07406-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="07406-246">たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="07406-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="07406-247">ページ ディレクティブが `@page "{id:int?}"` の場合、URL は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="07406-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="07406-248">`item.OfficeAssignment` が null ではない場合にのみ、`item.OfficeAssignment.Location` を表示する **Office** 列を追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="07406-249">これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。</span><span class="sxs-lookup"><span data-stu-id="07406-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="07406-250">インストラクターごとに担当したコースを表示する **Courses** 列を追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="07406-251">この Razor 構文の詳細については、「[明示的な行の遷移](xref:mvc/views/razor#explicit-line-transition)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07406-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="07406-252">選択したインストラクターとコースの `tr` 要素に `class="success"` を動的に追加するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="07406-253">これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="07406-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="07406-254">**Select** とラベル付けされる新しいハイパーリンクを追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-254">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="07406-255">このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="07406-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="07406-256">選択した Instructor のコースのテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="07406-257">選択したコースの学生登録のテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="07406-258">アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="07406-259">`OfficeAssignment` が null の場合、空のテーブル セルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="07406-260">インストラクターの **Select** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="07406-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="07406-261">行スタイルの変更とそのインストラクターに割り当てられたコースが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="07406-262">コースを選択して、登録済みの受講者とその成績のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-262">Select a course to see the list of enrolled students and their grades.</span></span>

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="07406-264">Single を使用する</span><span class="sxs-lookup"><span data-stu-id="07406-264">Using Single</span></span>

<span data-ttu-id="07406-265">`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="07406-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="07406-266">Where 条件を使った `Single` の使用は、個人の好みの問題です。</span><span class="sxs-lookup"><span data-stu-id="07406-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="07406-267">`Where` メソッドを使用しても利点はありません。</span><span class="sxs-lookup"><span data-stu-id="07406-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="07406-268">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="07406-268">Explicit loading</span></span>

<span data-ttu-id="07406-269">現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="07406-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="07406-270">ユーザーがコースの登録を表示することはほとんどないとします。</span><span class="sxs-lookup"><span data-stu-id="07406-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="07406-271">その場合、最適化は要求された場合にのみ登録データを読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="07406-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="07406-272">このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="07406-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="07406-273">次のコードを使用して *Pages/Instructors/Index.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="07406-274">上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。</span><span class="sxs-lookup"><span data-stu-id="07406-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="07406-275">コースが選択されると、明示的読み込みコードで以下が取得されます。</span><span class="sxs-lookup"><span data-stu-id="07406-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="07406-276">選択したコースの `Enrollment` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="07406-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="07406-277">各 `Enrollment` の `Student` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="07406-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="07406-278">上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="07406-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="07406-279">追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="07406-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="07406-280">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="07406-280">Test the app.</span></span> <span data-ttu-id="07406-281">ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。</span><span class="sxs-lookup"><span data-stu-id="07406-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="07406-282">次の手順</span><span class="sxs-lookup"><span data-stu-id="07406-282">Next steps</span></span>

<span data-ttu-id="07406-283">次のチュートリアルでは、関連データの更新方法を示します。</span><span class="sxs-lookup"><span data-stu-id="07406-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="07406-284">[前のチュートリアル](xref:data/ef-rp/complex-data-model)
>[次のチュートリアル](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="07406-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07406-285">このチュートリアルでは、関連データが読み取られ、表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="07406-286">関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。</span><span class="sxs-lookup"><span data-stu-id="07406-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="07406-287">解決できない問題が発生した場合は、[完成したアプリをダウンロードまたは表示](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)してください。</span><span class="sxs-lookup"><span data-stu-id="07406-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="07406-288">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="07406-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="07406-289">以下の図は、このチュートリアルの完成したページを示しています。</span><span class="sxs-lookup"><span data-stu-id="07406-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="07406-292">関連データの一括読み込み、明示的読み込み、遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="07406-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="07406-293">EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="07406-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="07406-294">[一括読み込み](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="07406-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="07406-295">一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。</span><span class="sxs-lookup"><span data-stu-id="07406-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="07406-296">エンティティが読み取られるときに、その関連データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="07406-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="07406-297">これは通常、必要なすべてのデータを取得する 1 つの結合クエリになります。</span><span class="sxs-lookup"><span data-stu-id="07406-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="07406-298">EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="07406-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="07406-299">複数のクエリを発行することで、1 つのクエリしかなかった EF6 の一部のクエリよりも、効率を高めることができます。</span><span class="sxs-lookup"><span data-stu-id="07406-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="07406-300">一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="07406-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="07406-302">一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。</span><span class="sxs-lookup"><span data-stu-id="07406-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="07406-303">メイン クエリに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="07406-303">One query for the main query</span></span> 
  * <span data-ttu-id="07406-304">読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="07406-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="07406-305">`Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。</span><span class="sxs-lookup"><span data-stu-id="07406-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="07406-306">"修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="07406-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="07406-307">`Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。</span><span class="sxs-lookup"><span data-stu-id="07406-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="07406-309">メモ:EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="07406-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="07406-310">ナビゲーション プロパティのデータが明示的に含まれ *ない* 場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="07406-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="07406-311">[明示的読み込み](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="07406-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="07406-312">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="07406-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="07406-313">必要なときに関連するデータを取得するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07406-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="07406-314">分離したクエリによる明示的読み込みにより、複数のクエリが DB に送信されます。</span><span class="sxs-lookup"><span data-stu-id="07406-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="07406-315">明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="07406-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="07406-316">明示的読み込みを行うには、`Load` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="07406-317">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="07406-317">For example:</span></span>

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="07406-319">[遅延読み込み](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="07406-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="07406-320">[遅延読み込みがバージョン 2.1 内の EF Core に追加されました](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="07406-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="07406-321">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="07406-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="07406-322">ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="07406-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="07406-323">初めてナビゲーション プロパティにアクセスされるたびに、クエリが DB に送信されます。</span><span class="sxs-lookup"><span data-stu-id="07406-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="07406-324">`Select` 演算子は必要な関連データのみを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="07406-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="07406-325">部門名を表示する Course ページを作成する</span><span class="sxs-lookup"><span data-stu-id="07406-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="07406-326">Course エンティティには、`Department` エンティティを含むナビゲーション プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="07406-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="07406-327">`Department` エンティティには、コースが割り当てられる部門が含まれています。</span><span class="sxs-lookup"><span data-stu-id="07406-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="07406-328">コースの一覧で割り当てられている部門の名前を表示するには:</span><span class="sxs-lookup"><span data-stu-id="07406-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="07406-329">`Department` エンティティから `Name` プロパティを取得します。</span><span class="sxs-lookup"><span data-stu-id="07406-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="07406-330">`Department` エンティティは `Course.Department` ナビゲーション プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="07406-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="07406-332">Course モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="07406-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07406-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07406-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="07406-334">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Course` を使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07406-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07406-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="07406-336">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="07406-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="07406-337">上記のコマンドは、`Course` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="07406-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="07406-338">Visual Studio でプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="07406-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="07406-339">*Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="07406-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="07406-340">スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。</span><span class="sxs-lookup"><span data-stu-id="07406-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="07406-341">`Include` メソッドが一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="07406-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="07406-342">アプリを実行し、 **[Courses]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="07406-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="07406-343">Department 列に `DepartmentID` が表示されますが、これには役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="07406-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="07406-344">`OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="07406-345">上のコードは `AsNoTracking` を追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="07406-346">`AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="07406-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="07406-347">これらのエンティティは現在のコンテキストでは更新されないため、追跡されません。</span><span class="sxs-lookup"><span data-stu-id="07406-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="07406-348">次の強調表示されているマークアップで *Pages/Courses/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="07406-349">スキャフォールディング コードに、次の変更が行われました。</span><span class="sxs-lookup"><span data-stu-id="07406-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="07406-350">見出しが Index から Courses に変更されました。</span><span class="sxs-lookup"><span data-stu-id="07406-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="07406-351">`CourseID` プロパティ値を示す **Number** 列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="07406-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="07406-352">既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。</span><span class="sxs-lookup"><span data-stu-id="07406-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="07406-353">ただし、このケースでは、主キーは意味があります。</span><span class="sxs-lookup"><span data-stu-id="07406-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="07406-354">部門名が表示されるように、 **Department** 列を変更しました。</span><span class="sxs-lookup"><span data-stu-id="07406-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="07406-355">コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="07406-356">アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="07406-358">Select を使用した関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="07406-358">Loading related data with Select</span></span>

<span data-ttu-id="07406-359">`OnGetAsync` メソッドは、`Include` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="07406-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="07406-360">`Select` 演算子は必要な関連データのみを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="07406-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="07406-361">`Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。</span><span class="sxs-lookup"><span data-stu-id="07406-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="07406-362">コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。</span><span class="sxs-lookup"><span data-stu-id="07406-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="07406-363">次のコードは、`Select` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="07406-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="07406-364">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="07406-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="07406-365">完全な例については、[IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07406-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="07406-366">コース登録を示す Instructors ページを作成する</span><span class="sxs-lookup"><span data-stu-id="07406-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="07406-367">このセクションでは、Instructors ページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="07406-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="07406-368">![Instructors/Index ページ](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="07406-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="07406-369">このページは、次の方法で関連データを読み取って表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="07406-370">インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。</span><span class="sxs-lookup"><span data-stu-id="07406-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="07406-371">`Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="07406-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="07406-372">`OfficeAssignment` エンティティには一括読み込みが使用されています。</span><span class="sxs-lookup"><span data-stu-id="07406-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="07406-373">一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。</span><span class="sxs-lookup"><span data-stu-id="07406-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="07406-374">この場合、インストラクターへのオフィスの割り当てが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="07406-375">ユーザーがインストラクターを選択 (上の図では Harui) すると、関連 `Course` エンティティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="07406-376">`Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="07406-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="07406-377">`Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。</span><span class="sxs-lookup"><span data-stu-id="07406-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="07406-378">このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="07406-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="07406-379">この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="07406-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="07406-380">ユーザーがコースを選択すると (上の図では Chemistry (化学))、`Enrollments` エンティティからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="07406-381">上の図では、受講者名とグレードが表示されています。</span><span class="sxs-lookup"><span data-stu-id="07406-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="07406-382">`Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="07406-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="07406-383">Instructor インデックス ビューのビュー モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="07406-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="07406-384">Instructors ページには、3 つの異なるテーブルからのデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="07406-385">3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="07406-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="07406-386">次のコードを使用して、 *SchoolViewModels* フォルダー内に *InstructorIndexData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="07406-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="07406-387">Instructor モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="07406-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07406-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07406-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="07406-389">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Instructor` を使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07406-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07406-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="07406-391">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="07406-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="07406-392">上記のコマンドは、`Instructor` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="07406-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="07406-393">アプリを実行し、Instructors ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="07406-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="07406-394">*Pages/Instructors/Index.cshtml.cs* を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="07406-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="07406-395">`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="07406-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="07406-396">*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。</span><span class="sxs-lookup"><span data-stu-id="07406-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="07406-397">クエリには次の 2 つが含まれています。</span><span class="sxs-lookup"><span data-stu-id="07406-397">The query has two includes:</span></span>

* <span data-ttu-id="07406-398">`OfficeAssignment`:[Instructors ビュー](#IP)に表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="07406-399">`CourseAssignments`:担当したコースを取り込みます。</span><span class="sxs-lookup"><span data-stu-id="07406-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="07406-400">Instructors/Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="07406-400">Update the instructors Index page</span></span>

<span data-ttu-id="07406-401">次のマークアップを使用して *Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="07406-402">上記のマークアップは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="07406-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="07406-403">`page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="07406-404">`"{id:int?}"` はルート テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="07406-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="07406-405">ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。</span><span class="sxs-lookup"><span data-stu-id="07406-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="07406-406">たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="07406-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="07406-407">ページ ディレクティブが `@page "{id:int?}"` の場合、上記の URL は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="07406-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="07406-408">ページ タイトルは **Instructors** です。</span><span class="sxs-lookup"><span data-stu-id="07406-408">Page title is **Instructors**.</span></span>
* <span data-ttu-id="07406-409">`item.OfficeAssignment` が null ではない場合にのみ `item.OfficeAssignment.Location` を表示する **Office** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="07406-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="07406-410">これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。</span><span class="sxs-lookup"><span data-stu-id="07406-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="07406-411">インストラクターごとに担当したコースを表示する **Courses** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="07406-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="07406-412">この Razor 構文の詳細については、「[明示的な行の遷移](xref:mvc/views/razor#explicit-line-transition)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07406-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="07406-413">選択したインストラクターの `tr` 要素に `class="success"` を動的に追加するコードを追加しました。</span><span class="sxs-lookup"><span data-stu-id="07406-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="07406-414">これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="07406-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="07406-415">**Select** とラベル付けされるハイパーリンクを追加しました。</span><span class="sxs-lookup"><span data-stu-id="07406-415">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="07406-416">このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="07406-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="07406-417">アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="07406-418">OfficeAssignment\` が null の場合、空のテーブル セルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07406-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="07406-419">**[Select]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="07406-419">Click on the **Select** link.</span></span> <span data-ttu-id="07406-420">行のスタイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="07406-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="07406-421">選択したインストラクターが担当するコースを追加する</span><span class="sxs-lookup"><span data-stu-id="07406-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="07406-422">次のコードを使用して、 *Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="07406-423">`public int CourseID { get; set; }` を追加します</span><span class="sxs-lookup"><span data-stu-id="07406-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="07406-424">更新されたクエリを確認します。</span><span class="sxs-lookup"><span data-stu-id="07406-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="07406-425">上記のクエリは `Department` エンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="07406-426">次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。</span><span class="sxs-lookup"><span data-stu-id="07406-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="07406-427">選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="07406-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="07406-428">ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="07406-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="07406-429">`Where` メソッドはコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="07406-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="07406-430">上記の `Where` メソッドでは、1 つの `Instructor` エンティティのみが返されます。</span><span class="sxs-lookup"><span data-stu-id="07406-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="07406-431">`Single` メソッドはコレクションを 1 つの `Instructor` エンティティに変換します。</span><span class="sxs-lookup"><span data-stu-id="07406-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="07406-432">`Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="07406-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="07406-433">`CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="07406-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="07406-435">コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="07406-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="07406-436">コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="07406-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="07406-437">代わりに、コレクションが空の場合に既定値を返す (この場合は null) `SingleOrDefault` を使用します。</span><span class="sxs-lookup"><span data-stu-id="07406-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="07406-438">空のコレクションで `SingleOrDefault` を使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="07406-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="07406-439">(null 参照で `Courses` プロパティを見つけようとすると) 例外になります。</span><span class="sxs-lookup"><span data-stu-id="07406-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="07406-440">例外メッセージに問題の原因が明確に示されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="07406-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="07406-441">次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="07406-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="07406-442">*Pages/Instructors/Index.cshtml* Razor ページの末尾に次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="07406-443">上記のマークアップは、インストラクターが選択されたときに、インストラクターに関連するコースのリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="07406-444">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="07406-444">Test the app.</span></span> <span data-ttu-id="07406-445">Instructors ページの **[Select]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="07406-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="07406-446">受講者データを表示する</span><span class="sxs-lookup"><span data-stu-id="07406-446">Show student data</span></span>

<span data-ttu-id="07406-447">このセクションでは、選択したコースの受講者データを表示するため、アプリが更新されます。</span><span class="sxs-lookup"><span data-stu-id="07406-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="07406-448">次のコードを使用して、 *Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドのクエリを更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="07406-449">*Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-449">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="07406-450">ファイルの末尾に次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="07406-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="07406-451">上記のマークアップは、選択したコースに登録されている受講者のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="07406-452">ページを更新し、インストラクターを選択します。</span><span class="sxs-lookup"><span data-stu-id="07406-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="07406-453">コースを選択して、登録済みの受講者とその成績のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="07406-453">Select a course to see the list of enrolled students and their grades.</span></span>

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="07406-455">Single を使用する</span><span class="sxs-lookup"><span data-stu-id="07406-455">Using Single</span></span>

<span data-ttu-id="07406-456">`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="07406-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="07406-457">上記の `Single` アプローチでは、`Where` を使用すること以上のメリットは提供されません。</span><span class="sxs-lookup"><span data-stu-id="07406-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="07406-458">一部の開発者は、`Single` アプローチ スタイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="07406-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="07406-459">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="07406-459">Explicit loading</span></span>

<span data-ttu-id="07406-460">現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="07406-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="07406-461">ユーザーがコースの登録を表示することはほとんどないとします。</span><span class="sxs-lookup"><span data-stu-id="07406-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="07406-462">その場合、最適化は要求された場合にのみ登録データを読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="07406-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="07406-463">このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="07406-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="07406-464">次のコードを使用して `OnGetAsync` を更新します。</span><span class="sxs-lookup"><span data-stu-id="07406-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="07406-465">上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。</span><span class="sxs-lookup"><span data-stu-id="07406-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="07406-466">コースが選択されると、強調表示されたコードが以下を取得します。</span><span class="sxs-lookup"><span data-stu-id="07406-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="07406-467">選択したコースの `Enrollment` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="07406-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="07406-468">各 `Enrollment` の `Student` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="07406-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="07406-469">上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="07406-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="07406-470">追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="07406-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="07406-471">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="07406-471">Test the app.</span></span> <span data-ttu-id="07406-472">ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。</span><span class="sxs-lookup"><span data-stu-id="07406-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="07406-473">次のチュートリアルでは、関連データの更新方法を示します。</span><span class="sxs-lookup"><span data-stu-id="07406-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07406-474">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="07406-474">Additional resources</span></span>

* [<span data-ttu-id="07406-475">このチュートリアルの YouTube バージョン (part1)</span><span class="sxs-lookup"><span data-stu-id="07406-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="07406-476">このチュートリアルの YouTube バージョン (part2)</span><span class="sxs-lookup"><span data-stu-id="07406-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="07406-477">[前へ](xref:data/ef-rp/complex-data-model)
>[次へ](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="07406-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

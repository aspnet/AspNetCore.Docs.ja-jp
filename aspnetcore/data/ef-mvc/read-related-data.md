---
title: 'チュートリアル: 関連データを読み取る - ASP.NET MVC と EF Core'
description: このチュートリアルでは、関連データ (Entity Framework がナビゲーション プロパティに読み込むデータ) の読み取りと表示を行います。
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 5f0e8efdf57e0a8ba5483680224d33ea5d60f06a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530152"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="3eb42-103">チュートリアル: 関連データを読み取る - ASP.NET MVC と EF Core</span><span class="sxs-lookup"><span data-stu-id="3eb42-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="3eb42-104">前のチュートリアルでは、School データ モデルを作成しました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="3eb42-105">このチュートリアルでは、関連データ (Entity Framework がナビゲーション プロパティに読み込むデータ) の読み取りと表示を行います。</span><span class="sxs-lookup"><span data-stu-id="3eb42-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="3eb42-106">以下の図は、使用するページを示しています。</span><span class="sxs-lookup"><span data-stu-id="3eb42-106">The following illustrations show the pages that you'll work with.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index.png)

<span data-ttu-id="3eb42-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3eb42-110">関連データを読み込む方法を学習する</span><span class="sxs-lookup"><span data-stu-id="3eb42-110">Learn how to load related data</span></span>
> * <span data-ttu-id="3eb42-111">Courses ページを作成する</span><span class="sxs-lookup"><span data-stu-id="3eb42-111">Create a Courses page</span></span>
> * <span data-ttu-id="3eb42-112">Instructors ページを作成する</span><span class="sxs-lookup"><span data-stu-id="3eb42-112">Create an Instructors page</span></span>
> * <span data-ttu-id="3eb42-113">明示的読み込みについて学習する</span><span class="sxs-lookup"><span data-stu-id="3eb42-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3eb42-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3eb42-114">Prerequisites</span></span>

* [<span data-ttu-id="3eb42-115">複合データ モデルの作成</span><span class="sxs-lookup"><span data-stu-id="3eb42-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="3eb42-116">関連データを読み込む方法を学習する</span><span class="sxs-lookup"><span data-stu-id="3eb42-116">Learn how to load related data</span></span>

<span data-ttu-id="3eb42-117">オブジェクト リレーショナル マッピング (ORM) ソフトウェア (Entity Framework など) では、次のように関連データをエンティティのナビゲーション プロパティに読み込むことができる方法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="3eb42-118">一括読み込み。</span><span class="sxs-lookup"><span data-stu-id="3eb42-118">Eager loading.</span></span> <span data-ttu-id="3eb42-119">エンティティが読み取られるときに、関連データがエンティティと共に取得されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="3eb42-120">これは通常、必要なデータをすべて取得する 1 つの結合クエリになります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="3eb42-121">`Include` メソッドと `ThenInclude` メソッドを使用して、Entity Framework Core で一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="3eb42-123">分離したクエリのデータの一部を取得することができ、EF によってナビゲーション プロパティが "修正されます"。</span><span class="sxs-lookup"><span data-stu-id="3eb42-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="3eb42-124">つまり、EF で、前に取得したエンティティのナビゲーション プロパティに属する、個別に取得したエンティティを自動的に追加します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="3eb42-125">関連データを取得するクエリの場合、リストやオブジェクトを返すメソッド (`ToList`、`Single` など) の代わりに、`Load` メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="3eb42-127">明示的読み込み。</span><span class="sxs-lookup"><span data-stu-id="3eb42-127">Explicit loading.</span></span> <span data-ttu-id="3eb42-128">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="3eb42-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3eb42-129">必要な場合に、関連データを取得するコードを記述します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="3eb42-130">分離したクエリによる一括読み込みのように、明示的読み込みでは、複数のクエリがデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="3eb42-131">明示的読み込みを使用すると、コードで読み込まれるナビゲーション プロパティを指定する点が異なります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="3eb42-132">Entity Framework Core 1.1 では、`Load` メソッドを使用して、明示的読み込みを実行できます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="3eb42-133">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-133">For example:</span></span>

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="3eb42-135">遅延読み込み。</span><span class="sxs-lookup"><span data-stu-id="3eb42-135">Lazy loading.</span></span> <span data-ttu-id="3eb42-136">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="3eb42-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3eb42-137">ただし、ナビゲーション プロパティに初めてアクセスしようとすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="3eb42-138">初めてナビゲーション プロパティからデータを取得しようとするたびに、クエリがデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="3eb42-139">Entity Framework Core 1.0 では、遅延読み込みはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="3eb42-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="3eb42-140">パフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="3eb42-140">Performance considerations</span></span>

<span data-ttu-id="3eb42-141">取得したすべてのエンティティの関連データが必要な場合は、通常、データベースに送信された 1 つのクエリの方が、取得した各エンティティに対する分離したクエリよりも効率的なため、一括読み込みを使用すると、より最適なパフォーマンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="3eb42-142">たとえば、各部門に関連コースが 10 個あるとします。</span><span class="sxs-lookup"><span data-stu-id="3eb42-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="3eb42-143">すべての関連データの一括読み込みは、1 つの (結合) クエリと 1 回のデータベースとのラウンド トリップだけになります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="3eb42-144">部門ごとのコースへの分離したクエリでは、データベースとのラウンド トリップが 11 回行われることになります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="3eb42-145">データベースとの余分なラウンド トリップは、特に待ち時間が長いときのパフォーマンスに悪影響をもたらします。</span><span class="sxs-lookup"><span data-stu-id="3eb42-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="3eb42-146">その一方で、一部のシナリオでは、分離したクエリがより効率的です。</span><span class="sxs-lookup"><span data-stu-id="3eb42-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="3eb42-147">1 つのクエリ内のすべての関連データの一括読み込みでは、SQL Server で効率的に処理できない、複雑な結合が生成される場合があります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="3eb42-148">または、処理しているエンティティのセットのサブセットのためだけにエンティティのナビゲーション プロパティにアクセスする必要がある場合、事前のすべてを取得する一括読み込みでは、必要以上にデータを取得するため、分離したクエリの方が適切に実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="3eb42-149">パフォーマンスが重要な場合、最適な選択を行うために、両方の方法でパフォーマンスをテストすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3eb42-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="3eb42-150">Courses ページを作成する</span><span class="sxs-lookup"><span data-stu-id="3eb42-150">Create a Courses page</span></span>

<span data-ttu-id="3eb42-151">Course エンティティには、コースが割り当てられている部門の Dpartment エンティティを含む、ナビゲーション プロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="3eb42-152">コースのリストに割り当てられた部門の名前を表示するには、`Course.Department` ナビゲーション プロパティにある Department エンティティから Name プロパティを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="3eb42-153">次の図に示すように、Students コントローラーに対して前に実行した **Entity Framework のスキャフォールディング機能を使用して、ビューと共に MVC コントローラー** に同じオプションを使用して、Course エンティティ型の CoursesController という名前のコントローラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Courses コントローラーを追加する](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="3eb42-155">*CoursesController.cs* を開いて、`Index` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="3eb42-156">自動スキャフォールディングでは、`Include` メソッドを使って、`Department` ナビゲーション プロパティに一括読み込みを指定しています。</span><span class="sxs-lookup"><span data-stu-id="3eb42-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="3eb42-157">`Index` メソッドを、Course エンティティ (`schoolContext` の代わりに `courses`) を返す `IQueryable` により適切な名前を使用する次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="3eb42-158">*Views/Courses/Index.cshtml* を開いて、テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="3eb42-159">変更が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="3eb42-159">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="3eb42-160">スキャフォールディング コードに、次の変更を行いました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="3eb42-161">見出しが Index から Courses に変更されました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="3eb42-162">`CourseID` プロパティ値を示す **Number** 列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="3eb42-163">既定では、主キーは、通常、エンド ユーザーにとって意味がないため、スキャフォールディングされません。</span><span class="sxs-lookup"><span data-stu-id="3eb42-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="3eb42-164">ただし、このケースでは、主キーは意味があり、表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="3eb42-165">部門名が表示されるように、**Department** 列を変更しました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="3eb42-166">コードは、`Department` ナビゲーション プロパティに読み込まれる Department エンティティの `Name` プロパティを表示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="3eb42-167">アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="3eb42-169">Instructors ページを作成する</span><span class="sxs-lookup"><span data-stu-id="3eb42-169">Create an Instructors page</span></span>

<span data-ttu-id="3eb42-170">このセクションでは、Instructors ページを表示するために、Instructor エンティティのコントローラーとビューを作成します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Instructors/Index ページ](read-related-data/_static/instructors-index.png)

<span data-ttu-id="3eb42-172">このページは、次の方法で関連データを読み取って表示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="3eb42-173">インストラクターのリストには、OfficeAssignment エンティティからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="3eb42-174">Instructor エンティティと OfficeAssignment エンティティは、一対ゼロまたは一対一リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="3eb42-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="3eb42-175">OfficeAssignment エンティティに一括読み込みを使用します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="3eb42-176">前述のように、通常、一括読み込みは、主テーブルで取得したすべての行の関連データが必要なときにより効率的です。</span><span class="sxs-lookup"><span data-stu-id="3eb42-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="3eb42-177">このケースでは、割り当てられたすべてのインストラクターのオフィスの割り当てを表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="3eb42-178">ユーザーがインストラクターを選択すると、関連する Course エンティティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="3eb42-179">Instructor エンティティと Course エンティティは、多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="3eb42-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="3eb42-180">Course エンティティとそれに関連する Department エンティティに一括読み込みを使用します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="3eb42-181">このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="3eb42-182">ただし、この例では、ナビゲーション プロパティにあるエンティティ内のナビゲーション プロパティに一括読み込みを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="3eb42-183">ユーザーがコースを選択すると、Enrollments エンティティ セットからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="3eb42-184">Course エンティティと Enrollment エンティティは、一対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="3eb42-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="3eb42-185">Enrollment エンティティとそれに関連する Student エンティティに分離したクエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="3eb42-186">Instructor インデックス ビューのビュー モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="3eb42-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="3eb42-187">Instructors ページには、3 つの異なるテーブルからデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="3eb42-188">そのため、テーブルの 1 つにデータを保持するごとに、3 つのプロパティを含むビュー モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="3eb42-189">*SchoolViewModels* フォルダー内に *InstructorIndexData.cs* を作成し、既存のコードを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="3eb42-190">Instructor コントローラーとビューを作成する</span><span class="sxs-lookup"><span data-stu-id="3eb42-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="3eb42-191">次の図に示すように、EF の読み取り/書き込みアクションで、Instructors コントローラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Instructors コントローラーを追加する](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="3eb42-193">*InstructorsController.cs* を開いて、ViewModels 名前空間に対する using ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="3eb42-194">Index メソッドを次のコードに置き換えて、関連データの一括読み込みを行い、ビュー モデルに配置します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="3eb42-195">メソッドでは、選択したインストラクターと選択したコースの ID 値を指定する、オプションのルート データ (`id`) とクエリ文字列パラメーター (`courseID`) を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="3eb42-196">パラメーターは、ページの **Select** ハイパーリンクによって指定されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="3eb42-197">このコードは、ビュー モデルのインスタンスを作成し、インストラクターのリストに配置することから始めます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="3eb42-198">コードでは、`Instructor.OfficeAssignment` と `Instructor.CourseAssignments` ナビゲーション プロパティに一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="3eb42-199">`CourseAssignments` プロパティ内で `Course` プロパティが読み込まれ、そのプロパティ内で `Enrollments` と `Department` プロパティが読み込まれ、各 `Enrollment` エンティティ内で `Student` プロパティが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="3eb42-200">ビューには常に OfficeAssignment エンティティが必要なため、同じクエリでフェッチする方が効率的です。</span><span class="sxs-lookup"><span data-stu-id="3eb42-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="3eb42-201">インストラクターが Web ページで選択されたときに、Course エンティティが必要なため、1 つのクエリが複数のクエリよりも適しているのは、ページに選択したコースを含めないよりも、含めて表示することの方が多い場合のみです。</span><span class="sxs-lookup"><span data-stu-id="3eb42-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="3eb42-202">`Course` から 2 つのプロパティが必要なため、コードでは `CourseAssignments` と `Course` を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="3eb42-203">`ThenInclude` 呼び出しの最初の文字列では、`CourseAssignment.Course`、`Course.Enrollments`、および `Enrollment.Student` を取得します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="3eb42-204">コードのその時点で、もう 1 つの `ThenInclude` は、必要としない `Student` のナビゲーション プロパティ用になります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="3eb42-205">ただし、`Include` を呼び出すと、`Instructor` プロパティを使ってやり直されるため、もう一度チェーンを順に移動する必要があります。今回は `Course.Enrollments` の代わりに `Course.Department` を指定しています。</span><span class="sxs-lookup"><span data-stu-id="3eb42-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="3eb42-206">次のコードは、インストラクターが選択されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="3eb42-207">選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="3eb42-208">ビュー モデルの `Courses` プロパティが Course エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="3eb42-209">`Where` メソッドはコレクションを返しますが、このケースでは、そのメソッドに渡された条件は、返されている Instructor エンティティの 1 つのみになります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="3eb42-210">`Single` メソッドでは、コレクションがエンティティの `CourseAssignments` プロパティへのアクセス権を付与する、1 つの Instructor エンティティに変換されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="3eb42-211">`CourseAssignments` プロパティには、関連する `Course` エンティティのみを必要とする、`CourseAssignment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="3eb42-212">コレクションに項目が 1 つのみであることがわかっている場合、コレクションで `Single` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="3eb42-213">コレクションが空になる場合、または複数の項目がある場合、Single メソッドは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="3eb42-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="3eb42-214">代わりに、コレクションが空の場合に既定値 (この場合は null) を返す `SingleOrDefault` を使用します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="3eb42-215">ただし、(null 参照で `Courses` プロパティを見つけようとして) 引き続き例外となる場合は、例外メッセージでは問題の原因があまり明確に示されません。</span><span class="sxs-lookup"><span data-stu-id="3eb42-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="3eb42-216">`Single` メソッドを呼び出す場合、個別に `Where` メソッドを呼び出す代わりに、Where 条件で渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="3eb42-217">これは次のコードの代わりに使用します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="3eb42-218">次に、コースが選択された場合、選択したコースはビュー モデルのコースのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="3eb42-219">次に、ビュー モデルの `Enrollments` プロパティが Enrollment エンティティと共にそのコースの `Enrollments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="3eb42-220">Instructor インデックス ビューを変更する</span><span class="sxs-lookup"><span data-stu-id="3eb42-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="3eb42-221">*Views/Instructors/Index.cshtml* で、テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="3eb42-222">変更が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="3eb42-222">The changes are highlighted.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

<span data-ttu-id="3eb42-223">既存のコードに次の変更を行いました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="3eb42-224">モデル クラスが `InstructorIndexData` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="3eb42-225">**Index** のページ タイトルが **Instructors** に変更されました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="3eb42-226">`item.OfficeAssignment` が null ではない場合にのみ `item.OfficeAssignment.Location` を表示する **Office** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="3eb42-227">(これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。)</span><span class="sxs-lookup"><span data-stu-id="3eb42-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="3eb42-228">インストラクターごとに担当したコースを表示する **Courses** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="3eb42-229">詳細については、Razor 構文記事の「[明示的な行の遷移](xref:mvc/views/razor#explicit-line-transition)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3eb42-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="3eb42-230">選択したインストラクターの `tr` 要素にブートストラップ CSS クラスを条件付きで追加するコードを追加しました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-230">Added code that conditionally adds a Bootstrap CSS class to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="3eb42-231">このクラスにより、選択した行の背景色が設定されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-231">This class sets a background color for the selected row.</span></span>

* <span data-ttu-id="3eb42-232">`Index` メソッドに送信される選択されたインストラクターの ID を発生させる、各行の他のリンクの直前に **Select** というラベルの新しいハイパーリンクを追加しました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="3eb42-233">アプリを実行し、 **[Instructors]** タブを選択します。関連する OfficeAssignment エンティティがない場合は、ページに関連する OfficeAssignment エンティティの Location プロパティと空の表のセルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![何も選択されていない Instructors/Index ページ](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="3eb42-235">*Views/Instructors/Index.cshtml* ファイルでは、テーブル要素を閉じた後に (ファイルの終わりに)、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="3eb42-236">このコードでは、インストラクターが選択されたときに、インストラクターに関連するコースのリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=63-98)]

<span data-ttu-id="3eb42-237">このコードでは、ビュー モデルの `Courses` プロパティを読み取り、コースのリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="3eb42-238">また、選択したコースの ID を `Index` アクション メソッドに送信する、**Select** ハイパーリンクも指定します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="3eb42-239">ページを更新し、インストラクターを選択します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="3eb42-240">選択したインストラクターに割り当てられたコースを表示するグリッドを表示し、各コースに割り当てられた部門の名前を表示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![インストラクターが選択された Instructors/Index ページ](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="3eb42-242">追加したコード ブロックの後に、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="3eb42-243">このコードは、コースを選択したときに、コースに登録されている受講者のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="3eb42-244">このコードは、コースに登録された受講生のリストを表示するために、ビュー モデルの Enrollments プロパティを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="3eb42-245">もう一度ページを更新し、インストラクターを選択します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="3eb42-246">次に、コースを選択して、登録済みの受講者とその成績のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="3eb42-248">明示的読み込みについて</span><span class="sxs-lookup"><span data-stu-id="3eb42-248">About explicit loading</span></span>

<span data-ttu-id="3eb42-249">*InstructorsController.cs* でインストラクターのリストを取得したときに、`CourseAssignments` ナビゲーション プロパティに一括読み込みを指定しました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="3eb42-250">ユーザーは選択したインストラクターとコースの登録内容をほとんど表示する必要がないとします。</span><span class="sxs-lookup"><span data-stu-id="3eb42-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="3eb42-251">その場合は、要求された場合にのみ、登録データを読み取る必要がある可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="3eb42-252">明示的読み込みを行う方法の例を表示するには、`Index` メソッドを次のコードに置き換えます。このコードは、Enrollments の一括読み込みを削除して、そのプロパティを明示的に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="3eb42-253">コードの変更が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="3eb42-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="3eb42-254">新しいコードでは、インストラクター エンティティを取得するコードから登録データを呼び出す *ThenInclude* メソッドを削除します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="3eb42-255">`AsNoTracking` も削除されます。</span><span class="sxs-lookup"><span data-stu-id="3eb42-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="3eb42-256">インストラクターとコースが選択された場合、強調表示されたコードで選択されたコードの Enrollment エンティティ、および各 Enrollment の Student エンティティを取得します。</span><span class="sxs-lookup"><span data-stu-id="3eb42-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="3eb42-257">アプリを実行して、Instructors/Index ページに移動すると、データを取得する方法を変更しているにもかかわらず、ページ上で表示される内容に変わりがないことがわかります。</span><span class="sxs-lookup"><span data-stu-id="3eb42-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="3eb42-258">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="3eb42-258">Get the code</span></span>

[<span data-ttu-id="3eb42-259">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="3eb42-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="3eb42-260">次の手順</span><span class="sxs-lookup"><span data-stu-id="3eb42-260">Next steps</span></span>

<span data-ttu-id="3eb42-261">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="3eb42-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3eb42-262">関連データを読み込む方法を学習した</span><span class="sxs-lookup"><span data-stu-id="3eb42-262">Learned how to load related data</span></span>
> * <span data-ttu-id="3eb42-263">Courses ページを作成した</span><span class="sxs-lookup"><span data-stu-id="3eb42-263">Created a Courses page</span></span>
> * <span data-ttu-id="3eb42-264">Instructors ページを作成した</span><span class="sxs-lookup"><span data-stu-id="3eb42-264">Created an Instructors page</span></span>
> * <span data-ttu-id="3eb42-265">明示的読み込みについて学習した</span><span class="sxs-lookup"><span data-stu-id="3eb42-265">Learned about explicit loading</span></span>

<span data-ttu-id="3eb42-266">関連データを更新する方法について学習するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="3eb42-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3eb42-267">関連データの更新</span><span class="sxs-lookup"><span data-stu-id="3eb42-267">Update related data</span></span>](update-related-data.md)

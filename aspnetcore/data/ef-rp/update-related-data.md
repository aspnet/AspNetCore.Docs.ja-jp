---
title: 'パート 7、ASP.NET Core の Razor ページと EF Core - 関連データの更新'
author: rick-anderson
description: 'Razor ページと Entity Framework チュートリアル シリーズのパート 7。'
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 3ec88a862697c540a1a98e733c31d76922f81f7c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060535"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="71dba-103">パート 7、ASP.NET Core の Razor ページと EF Core - 関連データの更新</span><span class="sxs-lookup"><span data-stu-id="71dba-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="71dba-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71dba-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71dba-105">このチュートリアルでは、関連データの更新方法を示します。</span><span class="sxs-lookup"><span data-stu-id="71dba-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="71dba-106">以下の図は、完成したページの一部を示しています。</span><span class="sxs-lookup"><span data-stu-id="71dba-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="71dba-107">![Course/Edit ページ](update-related-data/_static/course-edit30.png)
![Instructor/Edit ページ](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="71dba-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="71dba-108">Course/Create および Edit ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="71dba-109">Course/Create および Edit ページのスキャフォールディング コードには、部門 ID (整数) を表示する [部門] ドロップダウン リストがあります。</span><span class="sxs-lookup"><span data-stu-id="71dba-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="71dba-110">ドロップダウン リストには部門名が表示されるので、両方のページに部門名の一覧が必要です。</span><span class="sxs-lookup"><span data-stu-id="71dba-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="71dba-111">このリストを提供するには、Create および Edit ページに基底クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="71dba-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="71dba-112">Course の Create と Edit のために基底クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="71dba-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="71dba-113">次のコードで *Pages/Courses/DepartmentNamePageModel.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="71dba-114">上記のコードは、部門名のリストを格納するための [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) を作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="71dba-115">`selectedDepartment` を指定すると、`SelectList` でその部門が選択されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="71dba-116">Create と Edit のページ モデル クラスは、`DepartmentNamePageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="71dba-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="71dba-117">Course/Create ページ モデルを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-117">Update the Course Create page model</span></span>

<span data-ttu-id="71dba-118">コースは部門に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="71dba-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="71dba-119">Create ページと Edit ページの基底クラスからは、部門を選択するための `SelectList` が与えられます。</span><span class="sxs-lookup"><span data-stu-id="71dba-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="71dba-120">`SelectList` を使用するドロップダウン リストにより、`Course.DepartmentID` 外部キー (FK) プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="71dba-121">EF Core は `Course.DepartmentID` FK を使用して `Department` ナビゲーション プロパティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="71dba-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![コースの作成](update-related-data/_static/ddl30.png)

<span data-ttu-id="71dba-123">次のコードで *Pages/Courses/Create.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="71dba-124">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="71dba-124">The preceding code:</span></span>

* <span data-ttu-id="71dba-125">`DepartmentNamePageModel`から派生します。</span><span class="sxs-lookup"><span data-stu-id="71dba-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="71dba-126">`TryUpdateModelAsync` を使用して[過剰ポスティング](xref:data/ef-rp/crud#overposting)を防止します。</span><span class="sxs-lookup"><span data-stu-id="71dba-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="71dba-127">`ViewData["DepartmentID"]` を削除します。</span><span class="sxs-lookup"><span data-stu-id="71dba-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="71dba-128">基底クラスからの `DepartmentNameSL` は厳密に型指定されたモデルであり、Razor ページによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="71dba-129">厳密に型指定されたモデルは、弱く型指定されたモデルよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="71dba-130">詳細については、「[弱い型指定のデータ (ViewData と ViewBag)](xref:mvc/views/overview#VD_VB)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71dba-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-no-locrazor-page"></a><span data-ttu-id="71dba-131">Course Create Razor ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="71dba-132">次のコードで *Pages/Courses/Create.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="71dba-133">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="71dba-134">キャプションを **DepartmentID** から **Department** (部門) に変更します。</span><span class="sxs-lookup"><span data-stu-id="71dba-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="71dba-135">`"ViewBag.DepartmentID"` を `DepartmentNameSL` (基底クラスから) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="71dba-136">[Select Department]\(部門の選択\) オプションを追加します。</span><span class="sxs-lookup"><span data-stu-id="71dba-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="71dba-137">この変更により、部門がまだ選択されていないとき、最初の部門ではなく、[Select Department]\(部門の選択\) がドロップダウンに表示されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="71dba-138">部門が選択されていない場合に、検証メッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="71dba-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="71dba-139">Razor ページでは[選択タグ ヘルパー](xref:mvc/views/working-with-forms#the-select-tag-helper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="71dba-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="71dba-140">Create ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-140">Test the Create page.</span></span> <span data-ttu-id="71dba-141">Create ページには、部門 ID ではなく、部門名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="71dba-142">Course/Edit ページ モデルを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-142">Update the Course Edit page model</span></span>

<span data-ttu-id="71dba-143">次のコードで *Pages/Courses/Edit.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="71dba-144">変更は、Create ページ モデルで行われたものと似ています。</span><span class="sxs-lookup"><span data-stu-id="71dba-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="71dba-145">上記のコードでは、ドロップダウン リストでその部門を選択する `PopulateDepartmentsDropDownList` が部門 ID で渡されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-no-locrazor-page"></a><span data-ttu-id="71dba-146">Course Edit Razor ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="71dba-147">次のコードで *Pages/Courses/Edit.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="71dba-148">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="71dba-149">コース ID を表示します。</span><span class="sxs-lookup"><span data-stu-id="71dba-149">Displays the course ID.</span></span> <span data-ttu-id="71dba-150">通常、エンティティの主キー (PK) は表示されません。</span><span class="sxs-lookup"><span data-stu-id="71dba-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="71dba-151">PK は通常、ユーザーにとっては意味がありません。</span><span class="sxs-lookup"><span data-stu-id="71dba-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="71dba-152">このケースでは、PK はコース番号です。</span><span class="sxs-lookup"><span data-stu-id="71dba-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="71dba-153">[部門] ドロップダウンのキャプションを **DepartmentID** から **Department** に変更します。</span><span class="sxs-lookup"><span data-stu-id="71dba-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="71dba-154">`"ViewBag.DepartmentID"` を `DepartmentNameSL` (基底クラスから) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="71dba-155">このページには、コース番号の隠しフィールド (`<input type="hidden">`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="71dba-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="71dba-156">`<label>` タグ ヘルパーと `asp-for="Course.CourseID"` を追加しても、隠しフィールドの必要性はなくなりません。</span><span class="sxs-lookup"><span data-stu-id="71dba-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="71dba-157">`<input type="hidden">` は、ユーザーが **[保存]** をクリックしたときに、ポストされたデータに含まれるコース番号にとって必要です。</span><span class="sxs-lookup"><span data-stu-id="71dba-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="71dba-158">Course/Details および Delete ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="71dba-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) は、追跡が必要ない場合に、パフォーマンスを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="71dba-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="71dba-160">Course ページ モデルを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-160">Update the Course page models</span></span>

<span data-ttu-id="71dba-161">`AsNoTracking` を追加する次のコードで *Pages/Courses/Delete.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="71dba-162">*Pages/Courses/Details.cshtml.cs* ファイルで同じ変更を行います。</span><span class="sxs-lookup"><span data-stu-id="71dba-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a><span data-ttu-id="71dba-163">Course Razor ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-163">Update the Course Razor pages</span></span>

<span data-ttu-id="71dba-164">次のコードで *Pages/Courses/Delete.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="71dba-165">Details ページに同じ変更を行います。</span><span class="sxs-lookup"><span data-stu-id="71dba-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="71dba-166">Course ページをテストする</span><span class="sxs-lookup"><span data-stu-id="71dba-166">Test the Course pages</span></span>

<span data-ttu-id="71dba-167">Create、Edit、Details、Delete の各ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="71dba-168">Instructor/Create および Edit ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="71dba-169">インストラクターは、任意の数のコースを担当する場合があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="71dba-170">次の画像は、コース チェック ボックスの配列を含む Instructor/Edit ページを示しています。</span><span class="sxs-lookup"><span data-stu-id="71dba-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![コースが表示された Instructor/Edit ページ](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="71dba-172">チェック ボックスは、インストラクターが割り当てられるコースへの変更を可能にします。</span><span class="sxs-lookup"><span data-stu-id="71dba-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="71dba-173">データベース内のすべてのコースに対してチェック ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="71dba-174">インストラクターが割り当てられているコースが選択されています。</span><span class="sxs-lookup"><span data-stu-id="71dba-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="71dba-175">ユーザーはチェック ボックスをオンまたはオフにしてコースの割り当てを変更できます。</span><span class="sxs-lookup"><span data-stu-id="71dba-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="71dba-176">コースの数が非常に多い場合、別の UI が推奨されることもあります。</span><span class="sxs-lookup"><span data-stu-id="71dba-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="71dba-177">ただし、ここで示されている、多対多のリレーションシップを管理する方法が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="71dba-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="71dba-178">リレーションシップを作成または削除するには、結合エンティティを操作します。</span><span class="sxs-lookup"><span data-stu-id="71dba-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="71dba-179">割り当てられているコース データのクラスを作成する</span><span class="sxs-lookup"><span data-stu-id="71dba-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="71dba-180">次のコードを使用して、 *SchoolViewModels/AssignedCourseData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="71dba-181">`AssignedCourseData` クラスには、インストラクターに割り当てられたコースのチェック ボックスを作成するためのデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="71dba-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="71dba-182">Instructor ページ モデルの基底クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="71dba-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="71dba-183">*Pages/Instructors/InstructorCoursesPageModel.cs* 基底クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="71dba-184">`InstructorCoursesPageModel` は、Edit と Create のページ モデルに使用する基底クラスです。</span><span class="sxs-lookup"><span data-stu-id="71dba-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="71dba-185">`PopulateAssignedCourseData` は、`AssignedCourseDataList` に設定するためのすべての `Course` エンティティを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="71dba-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="71dba-186">コースごとに、コードは `CourseID`、タイトル、およびインストラクターがコースに割り当てられているかどうかを設定します。</span><span class="sxs-lookup"><span data-stu-id="71dba-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="71dba-187">[HashSet](/dotnet/api/system.collections.generic.hashset-1) は効率的な検索のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="71dba-188">Razor ページには Course エンティティのコレクションがないため、モデル バインダーは `CourseAssignments` ナビゲーション プロパティを自動的に更新できません。</span><span class="sxs-lookup"><span data-stu-id="71dba-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="71dba-189">モデル バインダーを使用する代わりに、新しい `UpdateInstructorCourses` メソッドで `CourseAssignments` ナビゲーション プロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="71dba-190">そのため、モデル バインドから `CourseAssignments` プロパティを除外する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="71dba-191">これを行うために、`TryUpdateModel` を呼び出すコードを変更する必要はありません。これは、プロパティが宣言されたオーバーロードを使用していて、`CourseAssignments` がインクルード リスト内にないためです。</span><span class="sxs-lookup"><span data-stu-id="71dba-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload with declared properties and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="71dba-192">チェック ボックスが選択されていない場合、`UpdateInstructorCourses` のコードは空のコレクションを使用して `CourseAssignments` ナビゲーション プロパティを初期化し、次を返します。</span><span class="sxs-lookup"><span data-stu-id="71dba-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="71dba-193">コードでは次に、データベースのすべてのコースをループ処理し、各コースがインストラクターに現在割り当てられているものか、ページで選択されているものかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="71dba-194">検索を効率化するため、最後の 2 つのコレクションが `HashSet` オブジェクトに格納されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="71dba-195">コースのチェック ボックスが選択されたが、そのコースが `Instructor.CourseAssignments`ナビゲーション プロパティにない場合、そのコースがナビゲーション プロパティ内のコレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="71dba-196">コースのチェック ボックスが選択さていないが、そのコースが `Instructor.CourseAssignments`ナビゲーション プロパティにある場合、そのコースがナビゲーション プロパティから削除されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="71dba-197">オフィスの場所を処理する</span><span class="sxs-lookup"><span data-stu-id="71dba-197">Handle office location</span></span>

<span data-ttu-id="71dba-198">編集ページで処理する必要があるもう 1 つのリレーションシップは、Instructor エンティティと `OfficeAssignment` エンティティの間に存在する 1 対 0 または 1 のリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="71dba-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="71dba-199">インストラクター編集コードでは、次のシナリオを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="71dba-200">ユーザーがオフィスの割り当てをクリアした場合、`OfficeAssignment` エンティティを削除する。</span><span class="sxs-lookup"><span data-stu-id="71dba-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="71dba-201">ユーザーがオフィスの割り当てを入力し、それが空だった場合、新しい `OfficeAssignment` エンティティを作成する。</span><span class="sxs-lookup"><span data-stu-id="71dba-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="71dba-202">ユーザーがオフィスの割り当てを変更した場合、`OfficeAssignment` エンティティを更新する。</span><span class="sxs-lookup"><span data-stu-id="71dba-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="71dba-203">Instructor/Edit ページ モデルを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="71dba-204">次のコードで *Pages/Instructors/Edit.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="71dba-205">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="71dba-205">The preceding code:</span></span>

* <span data-ttu-id="71dba-206">`OfficeAssignment`、`CourseAssignment`、`CourseAssignment.Course` ナビゲーション プロパティの一括読み込みを使用し、現在の `Instructor` エンティティをデータベースから取得します。</span><span class="sxs-lookup"><span data-stu-id="71dba-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="71dba-207">モデル バインダーからの値を使用して、取得した `Instructor` エンティティを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="71dba-208">`TryUpdateModel` は[過剰ポスティング](xref:data/ef-rp/crud#overposting)を防止します。</span><span class="sxs-lookup"><span data-stu-id="71dba-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="71dba-209">オフィスの場所が空白の場合は、`Instructor.OfficeAssignment` を null に設定します。</span><span class="sxs-lookup"><span data-stu-id="71dba-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="71dba-210">`Instructor.OfficeAssignment` が null の場合、`OfficeAssignment` テーブル内の関連する行が削除されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="71dba-211">`AssignedCourseData` ビュー モデル クラスを利用し、チェック ボックスの情報を提供する目的で、`OnGetAsync` で `PopulateAssignedCourseData` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="71dba-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="71dba-212">編集中の Instructor エンティティにチェック ボックスからの情報を適用する目的で、`OnPostAsync` で `UpdateInstructorCourses` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="71dba-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="71dba-213">`TryUpdateModel` が失敗した場合、`OnPostAsync` で `PopulateAssignedCourseData` と `UpdateInstructorCourses` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="71dba-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="71dba-214">このようなメソッド呼び出しにより、エラー メッセージと共に再表示されたとき、ページに入力された割り当て済みコース データが復元されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-no-locrazor-page"></a><span data-ttu-id="71dba-215">Instructor Edit Razor ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="71dba-216">次のコードで *Pages/Instructors/Edit.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="71dba-217">上記のコードでは、3 つの列を含む HTML テーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="71dba-218">各列には、チェック ボックスと、コース番号とタイトルを含むキャプションがあります。</span><span class="sxs-lookup"><span data-stu-id="71dba-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="71dba-219">チェック ボックスにはすべて、同じ名前 ("selectedCourses") が与えられます。</span><span class="sxs-lookup"><span data-stu-id="71dba-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="71dba-220">同じ名前を使用することで、これらをグループとして扱うようにモデル バインダーに通知します。</span><span class="sxs-lookup"><span data-stu-id="71dba-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="71dba-221">各チェック ボックスの value 属性は `CourseID` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="71dba-222">ページがポストされると、モデル バインダーは、選択されたチェック ボックスの `CourseID` 値のみで構成される配列を渡します。</span><span class="sxs-lookup"><span data-stu-id="71dba-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="71dba-223">チェック ボックスが最初にレンダリングされるときに、インストラクターに割り当てられているコースが選択されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="71dba-224">メモ:インストラクター コース データを編集するためにここで採用されている方法は、コースの数が限られている場合にはうまく機能します。</span><span class="sxs-lookup"><span data-stu-id="71dba-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="71dba-225">非常に大きいコレクションの場合、別の UI と別の更新方法の方が有効で効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="71dba-226">アプリを実行し、更新された Instructors/Edit ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="71dba-227">一部のコース割り当てを変更します。</span><span class="sxs-lookup"><span data-stu-id="71dba-227">Change some course assignments.</span></span> <span data-ttu-id="71dba-228">変更が Index ページに反映されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="71dba-229">Instructor/Create ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-229">Update the Instructor Create page</span></span>

<span data-ttu-id="71dba-230">Instructor Create ページ モデルと Razor ページを次のように Edit ページに似たコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="71dba-231">Instructors/Create ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="71dba-232">Instructor/Delete ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="71dba-233">次のコードで *Pages/Instructors/Delete.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="71dba-234">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="71dba-235">一括読み込みを `CourseAssignments` ナビゲーション プロパティに使用します。</span><span class="sxs-lookup"><span data-stu-id="71dba-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="71dba-236">`CourseAssignments` を含める必要があります。そうしないと、インストラクターが削除されたときに削除されません。</span><span class="sxs-lookup"><span data-stu-id="71dba-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="71dba-237">これらを読み取らなくても済むようにするには、データベースで連鎖削除を構成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="71dba-238">削除されるインストラクターが任意の部門の管理者として割り当てられている場合、インストラクターの割り当てをその部門から削除します。</span><span class="sxs-lookup"><span data-stu-id="71dba-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="71dba-239">アプリを実行し、Delete ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="71dba-240">次の手順</span><span class="sxs-lookup"><span data-stu-id="71dba-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="71dba-241">[前のチュートリアル](xref:data/ef-rp/read-related-data)
> [次のチュートリアル](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="71dba-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71dba-242">このチュートリアルでは、関連データの更新を示します。</span><span class="sxs-lookup"><span data-stu-id="71dba-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="71dba-243">解決できない問題が発生した場合は、[完成したアプリをダウンロードまたは表示](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)してください。</span><span class="sxs-lookup"><span data-stu-id="71dba-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="71dba-244">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="71dba-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="71dba-245">以下の図は、完成したページの一部を示しています。</span><span class="sxs-lookup"><span data-stu-id="71dba-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="71dba-246">![Course/Edit ページ](update-related-data/_static/course-edit.png)
![Instructor/Edit ページ](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="71dba-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="71dba-247">コースの Create (作成) ページと Edit (編集) ページを確認してテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="71dba-248">新しいコースを作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-248">Create a new course.</span></span> <span data-ttu-id="71dba-249">部門は、その名前ではなく主キー (整数) で選択されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="71dba-250">新しいコースを編集します。</span><span class="sxs-lookup"><span data-stu-id="71dba-250">Edit the new course.</span></span> <span data-ttu-id="71dba-251">テストが完了したら、新しいコースを削除します。</span><span class="sxs-lookup"><span data-stu-id="71dba-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="71dba-252">共通コードを共有する基底クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="71dba-252">Create a base class to share common code</span></span>

<span data-ttu-id="71dba-253">Courses/Create ページと Courses/Edit ページにはそれぞれ部門名のリストが必要です。</span><span class="sxs-lookup"><span data-stu-id="71dba-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="71dba-254">Create ページと Edit ページ用に *Pages/Courses/DepartmentNamePageModel.cshtml.cs* 基底クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="71dba-255">上記のコードは、部門名のリストを格納するための [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) を作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="71dba-256">`selectedDepartment` を指定すると、`SelectList` でその部門が選択されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="71dba-257">Create と Edit のページ モデル クラスは、`DepartmentNamePageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="71dba-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="71dba-258">Courses ページをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="71dba-258">Customize the Courses Pages</span></span>

<span data-ttu-id="71dba-259">新しいコース エンティティが作成されると、既存の部門とのリレーションシップが必要になります。</span><span class="sxs-lookup"><span data-stu-id="71dba-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="71dba-260">コースを作成しながら部門を追加するため、Create と Edit の基底クラスには選択する部門のドロップダウン リストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="71dba-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="71dba-261">ドロップダウン リストは、`Course.DepartmentID` 外部キー (FK) プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="71dba-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="71dba-262">EF Core は `Course.DepartmentID` FK を使用して `Department` ナビゲーション プロパティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="71dba-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![コースの作成](update-related-data/_static/ddl.png)

<span data-ttu-id="71dba-264">次のコードを使用して、Create ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="71dba-265">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="71dba-265">The preceding code:</span></span>

* <span data-ttu-id="71dba-266">`DepartmentNamePageModel`から派生します。</span><span class="sxs-lookup"><span data-stu-id="71dba-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="71dba-267">`TryUpdateModelAsync` を使用して[過剰ポスティング](xref:data/ef-rp/crud#overposting)を防止します。</span><span class="sxs-lookup"><span data-stu-id="71dba-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="71dba-268">`ViewData["DepartmentID"]` を `DepartmentNameSL` (基底クラスから) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="71dba-269">`ViewData["DepartmentID"]` は厳密に型指定された `DepartmentNameSL` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="71dba-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="71dba-270">厳密に型指定されたモデルは、弱く型指定されたモデルよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="71dba-271">詳細については、「[弱い型指定のデータ (ViewData と ViewBag)](xref:mvc/views/overview#VD_VB)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71dba-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="71dba-272">Courses/Create ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-272">Update the Courses Create page</span></span>

<span data-ttu-id="71dba-273">次のコードで *Pages/Courses/Create.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="71dba-274">上記のマークアップは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="71dba-275">キャプションを **DepartmentID** から **Department** (部門) に変更します。</span><span class="sxs-lookup"><span data-stu-id="71dba-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="71dba-276">`"ViewBag.DepartmentID"` を `DepartmentNameSL` (基底クラスから) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="71dba-277">[Select Department]\(部門の選択\) オプションを追加します。</span><span class="sxs-lookup"><span data-stu-id="71dba-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="71dba-278">この変更により、最初の部門ではなく、[Select Department]\(部門の選択\) がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="71dba-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="71dba-279">部門が選択されていない場合に、検証メッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="71dba-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="71dba-280">Razor ページでは[選択タグ ヘルパー](xref:mvc/views/working-with-forms#the-select-tag-helper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="71dba-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="71dba-281">Create ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-281">Test the Create page.</span></span> <span data-ttu-id="71dba-282">Create ページには、部門 ID ではなく、部門名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="71dba-283">Courses/Edit ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="71dba-284">次のコードで *Pages/Courses/Edit.cshtml.cs* のコードを置換します。</span><span class="sxs-lookup"><span data-stu-id="71dba-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="71dba-285">変更は、Create ページ モデルで行われたものと似ています。</span><span class="sxs-lookup"><span data-stu-id="71dba-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="71dba-286">上記のコードでは、ドロップダウン リストで指定された部門を選択する `PopulateDepartmentsDropDownList` が DepartmentID で渡されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="71dba-287">次のマークアップを使用して *Pages/Courses/Edit.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="71dba-288">上記のマークアップは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="71dba-289">コース ID を表示します。</span><span class="sxs-lookup"><span data-stu-id="71dba-289">Displays the course ID.</span></span> <span data-ttu-id="71dba-290">通常、エンティティの主キー (PK) は表示されません。</span><span class="sxs-lookup"><span data-stu-id="71dba-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="71dba-291">PK は通常、ユーザーにとっては意味がありません。</span><span class="sxs-lookup"><span data-stu-id="71dba-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="71dba-292">このケースでは、PK はコース番号です。</span><span class="sxs-lookup"><span data-stu-id="71dba-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="71dba-293">キャプションを **DepartmentID** から **Department** (部門) に変更します。</span><span class="sxs-lookup"><span data-stu-id="71dba-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="71dba-294">`"ViewBag.DepartmentID"` を `DepartmentNameSL` (基底クラスから) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="71dba-295">このページには、コース番号の隠しフィールド (`<input type="hidden">`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="71dba-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="71dba-296">`<label>` タグ ヘルパーと `asp-for="Course.CourseID"` を追加しても、隠しフィールドの必要性はなくなりません。</span><span class="sxs-lookup"><span data-stu-id="71dba-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="71dba-297">`<input type="hidden">` は、ユーザーが **[保存]** をクリックしたときに、ポストされたデータに含まれるコース番号にとって必要です。</span><span class="sxs-lookup"><span data-stu-id="71dba-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="71dba-298">更新されたコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-298">Test the updated code.</span></span> <span data-ttu-id="71dba-299">コースを作成、編集、および削除します。</span><span class="sxs-lookup"><span data-stu-id="71dba-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="71dba-300">AsNoTracking を Details (詳細) と Delete (削除) のページ モデルに追加する</span><span class="sxs-lookup"><span data-stu-id="71dba-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="71dba-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) は、追跡が必要ない場合に、パフォーマンスを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="71dba-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="71dba-302">`AsNoTracking` を Details と Delete のページ モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="71dba-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="71dba-303">次のコードは、更新された Delete ページ モデルを示しています。</span><span class="sxs-lookup"><span data-stu-id="71dba-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="71dba-304">*Pages/Courses/Details.cshtml.cs* ファイルで `OnGetAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="71dba-305">Delete ページと Details ページを変更する</span><span class="sxs-lookup"><span data-stu-id="71dba-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="71dba-306">次のマークアップを使用して、[削除] Razor ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="71dba-307">Details ページに同じ変更を行います。</span><span class="sxs-lookup"><span data-stu-id="71dba-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="71dba-308">Course ページをテストする</span><span class="sxs-lookup"><span data-stu-id="71dba-308">Test the Course pages</span></span>

<span data-ttu-id="71dba-309">Create、Edit、Details、Delete の各ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="71dba-310">Instructor ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-310">Update the instructor pages</span></span>

<span data-ttu-id="71dba-311">次のセクションでは、Instructor ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="71dba-312">オフィスの場所を追加する</span><span class="sxs-lookup"><span data-stu-id="71dba-312">Add office location</span></span>

<span data-ttu-id="71dba-313">インストラクター レコードを編集するときに、インストラクターのオフィスの割り当ての更新が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="71dba-314">`Instructor` エンティティには、`OfficeAssignment` エンティティとの一対ゼロまたは一対一のリレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="71dba-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="71dba-315">インストラクター コードは次を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-315">The instructor code must handle:</span></span>

* <span data-ttu-id="71dba-316">ユーザーがオフィスの割り当てをクリアした場合、`OfficeAssignment` エンティティを削除する。</span><span class="sxs-lookup"><span data-stu-id="71dba-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="71dba-317">ユーザーがオフィスの割り当てを入力し、それが空だった場合、新しい `OfficeAssignment` エンティティを作成する。</span><span class="sxs-lookup"><span data-stu-id="71dba-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="71dba-318">ユーザーがオフィスの割り当てを変更した場合、`OfficeAssignment` エンティティを更新する。</span><span class="sxs-lookup"><span data-stu-id="71dba-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="71dba-319">次のコードを使用して、Instructors/Edit ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="71dba-320">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="71dba-320">The preceding code:</span></span>

* <span data-ttu-id="71dba-321">`OfficeAssignment` ナビゲーション プロパティの一括読み込みを使用して、現在の `Instructor` エンティティをデータベースから取得します。</span><span class="sxs-lookup"><span data-stu-id="71dba-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="71dba-322">モデル バインダーからの値を使用して、取得した `Instructor` エンティティを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="71dba-323">`TryUpdateModel` は[過剰ポスティング](xref:data/ef-rp/crud#overposting)を防止します。</span><span class="sxs-lookup"><span data-stu-id="71dba-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="71dba-324">オフィスの場所が空白の場合は、`Instructor.OfficeAssignment` を null に設定します。</span><span class="sxs-lookup"><span data-stu-id="71dba-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="71dba-325">`Instructor.OfficeAssignment` が null の場合、`OfficeAssignment` テーブル内の関連する行が削除されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="71dba-326">Instructors/Edit ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-326">Update the instructor Edit page</span></span>

<span data-ttu-id="71dba-327">オフィスの場所で *Pages/Instructors/Edit.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="71dba-328">インストラクターのオフィスの場所を変更できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="71dba-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="71dba-329">Instructors/Edit ページにコースの割り当てを追加する</span><span class="sxs-lookup"><span data-stu-id="71dba-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="71dba-330">インストラクターは、任意の数のコースを担当する場合があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="71dba-331">このセクションでは、コースの割り当てを変更する機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="71dba-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="71dba-332">次の図は、更新された Instructors/Edit ページを示しています。</span><span class="sxs-lookup"><span data-stu-id="71dba-332">The following image shows the updated instructor Edit page:</span></span>

![コースが表示された Instructor/Edit ページ](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="71dba-334">`Course` と `Instructor` は、多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="71dba-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="71dba-335">リレーションシップの追加と削除を行うには、`CourseAssignments` 結合エンティティ セットからエンティティを追加および削除します。</span><span class="sxs-lookup"><span data-stu-id="71dba-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="71dba-336">チェック ボックスは、インストラクターが割り当てられるコースへの変更を有効にします。</span><span class="sxs-lookup"><span data-stu-id="71dba-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="71dba-337">データベース内のすべてのコースのチェック ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="71dba-338">インストラクターが割り当てられているコースがチェックされています。</span><span class="sxs-lookup"><span data-stu-id="71dba-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="71dba-339">ユーザーは、チェック ボックスをオンまたはオフにしてコースの割り当てを変更できます。</span><span class="sxs-lookup"><span data-stu-id="71dba-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="71dba-340">コースの数が非常に多い場合:</span><span class="sxs-lookup"><span data-stu-id="71dba-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="71dba-341">別のユーザー インターフェイスを使用して、コースを表示します。</span><span class="sxs-lookup"><span data-stu-id="71dba-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="71dba-342">結合エンティティを操作してリレーションシップを作成または削除するメソッドは変わりません。</span><span class="sxs-lookup"><span data-stu-id="71dba-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="71dba-343">Instructors/Create ページと Instructors/Edit ページをサポートするクラスを追加する</span><span class="sxs-lookup"><span data-stu-id="71dba-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="71dba-344">次のコードを使用して、 *SchoolViewModels/AssignedCourseData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="71dba-345">`AssignedCourseData` クラスには、インストラクターごとの割り当てられたコースのチェック ボックスを作成するデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="71dba-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="71dba-346">*Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* 基底クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="71dba-347">`InstructorCoursesPageModel` は、Edit と Create のページ モデルに使用する基底クラスです。</span><span class="sxs-lookup"><span data-stu-id="71dba-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="71dba-348">`PopulateAssignedCourseData` は、`AssignedCourseDataList` に設定するためのすべての `Course` エンティティを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="71dba-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="71dba-349">コースごとに、コードは `CourseID`、タイトル、およびインストラクターがコースに割り当てられているかどうかを設定します。</span><span class="sxs-lookup"><span data-stu-id="71dba-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="71dba-350">効率的な参照を作成するために [HashSet](/dotnet/api/system.collections.generic.hashset-1) が使用されています。</span><span class="sxs-lookup"><span data-stu-id="71dba-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="71dba-351">Instructors/Edit ページ モデル</span><span class="sxs-lookup"><span data-stu-id="71dba-351">Instructors Edit page model</span></span>

<span data-ttu-id="71dba-352">次のコードを使用して、Instructors/Edit ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="71dba-353">上記のコードでは、オフィスの割り当て変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="71dba-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="71dba-354">Instructor Razor ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="71dba-355">Visual Studio にコードを貼り付けると、改行がコードを分割するように変更されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="71dba-356">Ctrl キーを押しながら Z キーを 1 回押して、オート フォーマットを元に戻します。</span><span class="sxs-lookup"><span data-stu-id="71dba-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="71dba-357">Ctrl キーを押しながら Z キーを押すことで、改行がここに示されているように修正されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="71dba-358">インデントは完璧である必要はありませんが、`@:</tr><tr>`、`@:<td>`、`@:</td>`、および `@:</tr>` の行は、示されているようにそれぞれ 1 行にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="71dba-359">新しいコードのブロックを選択して、Tab キーを 3 回押して、新しいコードと既存のコードを並べます。</span><span class="sxs-lookup"><span data-stu-id="71dba-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="71dba-360">このバグの状態を報告または確認するには、[このリンク](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)を使用してください。</span><span class="sxs-lookup"><span data-stu-id="71dba-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="71dba-361">上記のコードでは、3 つの列を含む HTML テーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="71dba-362">各列には、チェック ボックスと、コース番号とタイトルを含むキャプションがあります。</span><span class="sxs-lookup"><span data-stu-id="71dba-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="71dba-363">チェック ボックスはすべて、同じ名前 ("selectedCourses") を持ちます。</span><span class="sxs-lookup"><span data-stu-id="71dba-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="71dba-364">同じ名前を使用することで、これらをグループとして扱うようにモデル バインダーに通知します。</span><span class="sxs-lookup"><span data-stu-id="71dba-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="71dba-365">各チェック ボックスの value 属性は `CourseID` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="71dba-366">ページがポストされると、モデル バインダーは、選択されたチェック ボックスの `CourseID` 値のみで構成される配列を渡します。</span><span class="sxs-lookup"><span data-stu-id="71dba-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="71dba-367">チェック ボックスが最初にレンダリングされるときに、インストラクターに割り当てられているコースが checked 属性を持ちます。</span><span class="sxs-lookup"><span data-stu-id="71dba-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="71dba-368">アプリを実行し、更新された Instructors/Edit ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="71dba-369">一部のコース割り当てを変更します。</span><span class="sxs-lookup"><span data-stu-id="71dba-369">Change some course assignments.</span></span> <span data-ttu-id="71dba-370">変更が Index ページに反映されます。</span><span class="sxs-lookup"><span data-stu-id="71dba-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="71dba-371">メモ:インストラクター コース データを編集するためにここで採用されている方法は、コースの数が限られている場合にはうまく機能します。</span><span class="sxs-lookup"><span data-stu-id="71dba-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="71dba-372">非常に大きいコレクションの場合、別の UI と別の更新方法の方が有効で効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="71dba-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="71dba-373">Instructors/Create ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-373">Update the instructors Create page</span></span>

<span data-ttu-id="71dba-374">次のコードを使用して、Instructors/Create ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="71dba-375">上記のコードは、 *Pages/Instructors/Edit.cshtml.cs* コードに似ています。</span><span class="sxs-lookup"><span data-stu-id="71dba-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="71dba-376">次のマークアップを使用して、Instructor Create Razor ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="71dba-377">Instructors/Create ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="71dba-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="71dba-378">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="71dba-378">Update the Delete page</span></span>

<span data-ttu-id="71dba-379">次のコードを使用して、Delete ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="71dba-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="71dba-380">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="71dba-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="71dba-381">一括読み込みを `CourseAssignments` ナビゲーション プロパティに使用します。</span><span class="sxs-lookup"><span data-stu-id="71dba-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="71dba-382">`CourseAssignments` を含める必要があります。そうしないと、インストラクターが削除されたときに削除されません。</span><span class="sxs-lookup"><span data-stu-id="71dba-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="71dba-383">これらを読み取らなくても済むようにするには、データベースで連鎖削除を構成します。</span><span class="sxs-lookup"><span data-stu-id="71dba-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="71dba-384">削除されるインストラクターが任意の部門の管理者として割り当てられている場合、インストラクターの割り当てをその部門から削除します。</span><span class="sxs-lookup"><span data-stu-id="71dba-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71dba-385">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="71dba-385">Additional resources</span></span>

* [<span data-ttu-id="71dba-386">このチュートリアルの YouTube バージョン (パート 1)</span><span class="sxs-lookup"><span data-stu-id="71dba-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="71dba-387">このチュートリアルの YouTube バージョン (パート 2)</span><span class="sxs-lookup"><span data-stu-id="71dba-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="71dba-388">[前へ](xref:data/ef-rp/read-related-data)
> [次へ](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="71dba-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

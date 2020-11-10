---
title: パート 4、ASP.NET Core MVC アプリにモデルを追加する
author: rick-anderson
description: ASP.NET Core MVC のチュートリアル シリーズのパート 4。
ms.author: riande
ms.date: 01/13/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 428d153cd94c882db16484a3009c86d1f9593538
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050902"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="02c40-103">パート 4、ASP.NET Core MVC アプリにモデルを追加する</span><span class="sxs-lookup"><span data-stu-id="02c40-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="02c40-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="02c40-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="02c40-105">このセクションでは、データベースのムービーを管理するクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="02c40-106">これらのクラスは、 **M** VC アプリの " **モ** デル" 部分です。</span><span class="sxs-lookup"><span data-stu-id="02c40-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="02c40-107">[Entity Framework Core](/ef/core) (EF Core) でこれらのクラスを使用して、データベースを操作します。</span><span class="sxs-lookup"><span data-stu-id="02c40-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="02c40-108">EF Core は、記述する必要があるデータ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="02c40-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="02c40-109">作成するモデル クラスは、EF Core に対する依存関係がないために、POCO クラス ( **P** lain- **O** ld **C** LR **O** bjects から) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="02c40-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="02c40-110">これらは単に、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="02c40-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="02c40-111">このチュートリアルでは、まずモデル クラスを記述し、EF コアによってデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="02c40-112">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="02c40-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02c40-114">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="02c40-115">ファイルに *Movie.cs* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="02c40-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02c40-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02c40-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02c40-117">*Movie.cs* という名前のファイルを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02c40-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="02c40-119">*Models* フォルダーを右クリックし、 **[追加]**  >  **[新しいクラス]**  >  **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="02c40-120">ファイルに *Movie.cs* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="02c40-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="02c40-121">次のコードで *Movie.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="02c40-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="02c40-122">`Movie` クラスには、データベースで主キー用に必要となる `Id` フィールドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="02c40-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="02c40-123">`ReleaseDate` の <xref:System.ComponentModel.DataAnnotations.DataType> 属性により、データの型 (`Date`) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="02c40-124">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="02c40-124">With this attribute:</span></span>

* <span data-ttu-id="02c40-125">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="02c40-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="02c40-126">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="02c40-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="02c40-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="02c40-128">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="02c40-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02c40-130">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC メニュー](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="02c40-132">PMC で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="02c40-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="02c40-133">上記のコマンドによって EF Core SQL Server プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="02c40-134">プロバイダー パッケージによって、依存関係として EF Core パッケージがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="02c40-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="02c40-135">追加のパッケージは、このチュートリアルの後半で、スキャフォールディングの手順で自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="02c40-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02c40-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02c40-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02c40-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="02c40-138">**[プロジェクト]** メニューから、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="02c40-139">右上の **[検索]** フィールドに「`Microsoft.EntityFrameworkCore.SQLite`」と入力し、 **Return** キーを押して検索します。</span><span class="sxs-lookup"><span data-stu-id="02c40-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="02c40-140">一致する NuGet パッケージを選択し、 **[パッケージの追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="02c40-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet パッケージを追加する](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="02c40-142">**[プロジェクトの選択]** ダイアログが、`MvcMovie` プロジェクトが選択された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="02c40-143">**[OK]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="02c40-143">Press the **Ok** button.</span></span>

<span data-ttu-id="02c40-144">**[ライセンスの同意]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="02c40-145">必要に応じてライセンスを確認し、 **[同意する]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="02c40-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="02c40-146">上記の手順を繰り返して、次の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="02c40-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="02c40-147">データベース コンテキスト クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="02c40-147">Create a database context class</span></span>

<span data-ttu-id="02c40-148">データベース コンテキスト クラスは、`Movie` モデルの EF Core 機能 (作成、読み取り、更新、削除) を調整するために必要です。</span><span class="sxs-lookup"><span data-stu-id="02c40-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="02c40-149">データベース コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生し、データ モデルに含めるエンティティを指定します。</span><span class="sxs-lookup"><span data-stu-id="02c40-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="02c40-150">*Data* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="02c40-150">Create a *Data* folder.</span></span>

<span data-ttu-id="02c40-151">次のコードで *Data/MvcMovieContext.cs* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="02c40-152">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="02c40-153">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="02c40-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="02c40-154">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="02c40-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="02c40-155">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="02c40-155">Register the database context</span></span>

<span data-ttu-id="02c40-156">ASP.NET Core には、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="02c40-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="02c40-157">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に DI に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="02c40-158">これらのサービスを必要とするコンポーネント (:::no-loc(Razor)::: Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-158">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="02c40-159">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="02c40-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="02c40-160">このセクションでは、DI コンテナーにデータベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="02c40-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="02c40-161">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="02c40-162">`Startup.ConfigureServices` で次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-164">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="02c40-165">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="02c40-166">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *:::no-loc(appsettings.json):::* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="02c40-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="02c40-167">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="02c40-167">Add a database connection string</span></span>

<span data-ttu-id="02c40-168">接続文字列を *:::no-loc(appsettings.json):::* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-168">Add a connection string to the *:::no-loc(appsettings.json):::* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-170">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="02c40-171">コンパイラ エラーのチェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="02c40-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="02c40-172">ムービー ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="02c40-172">Scaffold movie pages</span></span>

<span data-ttu-id="02c40-173">スキャフォールディング ツールを使用し、ムービー モデルの作成、読み取り、更新、削除の (CRUD) ページを生成します。</span><span class="sxs-lookup"><span data-stu-id="02c40-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02c40-175">*ソリューション エクスプローラー* で、 **Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![前述の手順を参照](adding-model/_static/add_controller21.png)

<span data-ttu-id="02c40-177">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用したビューがある MVC コントローラー]、[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[スキャフォールディングを追加] ダイアログ](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="02c40-179">**[コントローラーの追加]** ダイアログ ボックスを完了します。</span><span class="sxs-lookup"><span data-stu-id="02c40-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="02c40-180">**モデル クラス:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="02c40-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="02c40-181">**データ コンテキスト クラス:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="02c40-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![[データの追加] コンテキスト](adding-model/_static/dc3.png)

* <span data-ttu-id="02c40-183">**ビュー:** 各オプションの既定値をオンにします。</span><span class="sxs-lookup"><span data-stu-id="02c40-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="02c40-184">**コントローラー名:** 既定の *MoviesController* のままにします。</span><span class="sxs-lookup"><span data-stu-id="02c40-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="02c40-185">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-185">Select **Add**</span></span>

<span data-ttu-id="02c40-186">Visual Studio では、次が作成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-186">Visual Studio creates:</span></span>

* <span data-ttu-id="02c40-187">ムービー コントローラー ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="02c40-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="02c40-188">作成、削除、詳細、編集、およびインデックス ページ用の :::no-loc(Razor)::: ビュー ファイル ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="02c40-188">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="02c40-189">このようなファイルの自動作成は、" *スキャフォールディング* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="02c40-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="02c40-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02c40-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="02c40-191">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="02c40-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="02c40-192">Linux で、スキャフォールディング ツールのパスをエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="02c40-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="02c40-193">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="02c40-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02c40-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02c40-195">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="02c40-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="02c40-196">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="02c40-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="02c40-197">データベースが存在しないため、スキャフォールディング ページをまだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="02c40-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="02c40-198">アプリを実行し、 **[Movie App]** リンクをクリックすると、 *[データベースを開けません]* または *[そのようなテーブルはありません:Movie* ] というエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="02c40-199">最初の移行</span><span class="sxs-lookup"><span data-stu-id="02c40-199">Initial migration</span></span>

<span data-ttu-id="02c40-200">EF Core [移行](xref:data/ef-mvc/migrations)機能を使用し、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="02c40-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="02c40-201">移行は、データ モデルに合わせてデータベースを作成したり、更新したりできる一連のツールです。</span><span class="sxs-lookup"><span data-stu-id="02c40-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02c40-203">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="02c40-204">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="02c40-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="02c40-205">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 移行ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="02c40-206">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="02c40-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="02c40-207">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="02c40-208">これは最初の移行であるため、生成されたクラスには、データベース スキーマを作成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="02c40-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="02c40-209">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="02c40-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="02c40-210">`Update-Database`:前のコマンドで作成された最新の移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="02c40-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="02c40-211">このコマンドにより *Migrations/{time-stamp}_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="02c40-212">データベース更新コマンドにより、次の警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="02c40-213">エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="02c40-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="02c40-214">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="02c40-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="02c40-215">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="02c40-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="02c40-216">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-217">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="02c40-218">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="02c40-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="02c40-219">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 移行ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="02c40-220">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="02c40-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="02c40-221">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="02c40-222">これは最初の移行であるため、生成されたクラスには、データベース スキーマを作成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="02c40-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="02c40-223">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます ( *Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="02c40-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="02c40-224">`ef database update`:前のコマンドで作成された最新の移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="02c40-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="02c40-225">このコマンドにより *Migrations/{time-stamp}_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="02c40-226">InitialCreate クラス</span><span class="sxs-lookup"><span data-stu-id="02c40-226">The InitialCreate class</span></span>

<span data-ttu-id="02c40-227">*Migrations/{timestamp}_InitialCreate.cs* 移行ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="02c40-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="02c40-228">`Up` メソッドにより Movie テーブルが作成され、主キーとして `Id` が構成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="02c40-229">`Down` メソッドにより、`Up` 移行で行われたスキーマ変更が元に戻ります。</span><span class="sxs-lookup"><span data-stu-id="02c40-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="02c40-230">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="02c40-230">Test the app</span></span>

* <span data-ttu-id="02c40-231">アプリを実行し、 **[Movie App]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="02c40-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="02c40-232">次のような例外が表示された場合:</span><span class="sxs-lookup"><span data-stu-id="02c40-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-234">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="02c40-235">[移行手順](#migration)を実行しなかった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="02c40-236">**Create** ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="02c40-236">Test the **Create** page.</span></span> <span data-ttu-id="02c40-237">データを入力して送信します。</span><span class="sxs-lookup"><span data-stu-id="02c40-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="02c40-238">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="02c40-239">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="02c40-240">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="02c40-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="02c40-241">**[編集]** 、 **[詳細]** 、 **[削除]** の各ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="02c40-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="02c40-242">コントローラーの依存関係挿入</span><span class="sxs-lookup"><span data-stu-id="02c40-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02c40-244">*Controllers/MoviesController.cs* ファイルを開いて、コンストラクターを調べます。</span><span class="sxs-lookup"><span data-stu-id="02c40-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="02c40-245">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="02c40-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="02c40-246">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-247">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="02c40-248">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="02c40-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="02c40-249">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="02c40-250">開発用に SQLite を、運用環境に SQL Server を使用する</span><span class="sxs-lookup"><span data-stu-id="02c40-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="02c40-251">SQLite が選択されている場合、テンプレートで生成されたコードは開発の準備ができています。</span><span class="sxs-lookup"><span data-stu-id="02c40-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="02c40-252">次のコードでは、スタートアップに <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="02c40-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="02c40-253">`IWebHostEnvironment` が挿入されているので、`ConfigureServices` では開発用に SQLite を、運用環境に SQL Server を使用できます。</span><span class="sxs-lookup"><span data-stu-id="02c40-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="02c40-254">厳密に型指定されたモデルと @model キーワード</span><span class="sxs-lookup"><span data-stu-id="02c40-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="02c40-255">コントローラーで `ViewData` ディクショナリを使ってビューにデータまたはオブジェクトを渡す方法を前に示しました。</span><span class="sxs-lookup"><span data-stu-id="02c40-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="02c40-256">`ViewData` ディクショナリは動的オブジェクトであり、ビューに情報を渡すための便利な遅延バインディングの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="02c40-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="02c40-257">MVC にも、厳密に型指定されたモデル オブジェクトをビューに渡す機能があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="02c40-258">この厳密に型指定された方法では、コンパイル時にコードを確認できます。</span><span class="sxs-lookup"><span data-stu-id="02c40-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="02c40-259">スキャフォールディング メカニズムでは、`MoviesController` のクラスとビューで、この手法 (つまり、厳密に型指定されたモデルを渡しました) が使用されました。</span><span class="sxs-lookup"><span data-stu-id="02c40-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="02c40-260">*Controllers/MoviesController.cs* ファイルで生成された `Details` メソッドを調べてください。</span><span class="sxs-lookup"><span data-stu-id="02c40-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="02c40-261">通常、`id` パラメーターはルート データとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="02c40-262">たとえば、`https://localhost:5001/movies/details/1` は次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="02c40-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="02c40-263">コントローラーを `movies` コントローラーに (最初の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="02c40-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="02c40-264">アクションを `details` に (2 番目の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="02c40-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="02c40-265">ID を 1 に (最後の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="02c40-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="02c40-266">次のようにクエリ文字列で `id` を渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="02c40-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="02c40-267">ID 値が指定されない場合、`id` パラメーターは [null 許容型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="02c40-268">ルート データまたはクエリ文字列の値と一致するムービー エンティティを選択するため、[ラムダ式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)が `FirstOrDefaultAsync` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="02c40-269">ムービーが見つかった場合、`Movie` モデルのインスタンスが `Details` ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="02c40-270">*Views/Movies/Details.cshtml* ファイルの内容を確認してください。</span><span class="sxs-lookup"><span data-stu-id="02c40-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="02c40-271">ビュー ファイルの一番上にある `@model` ステートメントにより、ビューで求められるオブジェクトの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="02c40-272">ムービー コントローラーが作成されたとき、次の `@model` ステートメントが含まれました。</span><span class="sxs-lookup"><span data-stu-id="02c40-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="02c40-273">この `@model` ディレクティブにより、コントローラーでビューに渡されたムービーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="02c40-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="02c40-274">`Model` オブジェクトは厳密に型指定されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="02c40-275">たとえば、 *Details.cshtml* ビューでは、コードで厳密に型指定された `Model` オブジェクトを使って、`DisplayNameFor` および `DisplayFor` HTML ヘルパーに各ムービー フィールドを渡しています。</span><span class="sxs-lookup"><span data-stu-id="02c40-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="02c40-276">`Create` および `Edit` のメソッドとビューも、`Movie` モデル オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="02c40-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="02c40-277">Movies コントローラーの *Index.cshtml* ビューと `Index` メソッドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="02c40-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="02c40-278">コードで `View` メソッドを呼び出すときの `List` オブジェクトの作成方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="02c40-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="02c40-279">コードでは、この `Movies` リストを `Index` アクション メソッドからビューに渡しています。</span><span class="sxs-lookup"><span data-stu-id="02c40-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="02c40-280">ムービー コントローラーが作成されたとき、スキャフォールディングにより、 *Index.cshtml* ファイルの一番上に次の `@model` ステートメントが含まれました。</span><span class="sxs-lookup"><span data-stu-id="02c40-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="02c40-281">`@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーのリストにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="02c40-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="02c40-282">たとえば、 *Index.cshtml* ビューのコードでは、`foreach` ステートメントを使って厳密に型指定された `Model` オブジェクトのムービーをループ処理しています。</span><span class="sxs-lookup"><span data-stu-id="02c40-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="02c40-283">`Model` オブジェクトは厳密に型指定されているので (`IEnumerable<Movie>` オブジェクトとして)、ループ内の各項目は `Movie` として型指定されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="02c40-284">これには、コンパイル時にコードを確認できるなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02c40-285">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="02c40-285">Additional resources</span></span>

* [<span data-ttu-id="02c40-286">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="02c40-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="02c40-287">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="02c40-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="02c40-288">[前のチュートリアル ビューの追加](adding-view.md)
> [次のチュートリアル SQL の使用](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="02c40-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="02c40-289">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="02c40-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02c40-291">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="02c40-292">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="02c40-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-293">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="02c40-294">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="02c40-295">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="02c40-295">Scaffold the movie model</span></span>

<span data-ttu-id="02c40-296">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="02c40-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="02c40-297">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02c40-299">*ソリューション エクスプローラー* で、 **Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-299">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![前述の手順を参照](adding-model/_static/add_controller21.png)

<span data-ttu-id="02c40-301">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用したビューがある MVC コントローラー]、[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[スキャフォールディングを追加] ダイアログ](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="02c40-303">**[コントローラーの追加]** ダイアログ ボックスを完了します。</span><span class="sxs-lookup"><span data-stu-id="02c40-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="02c40-304">**モデル クラス:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="02c40-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="02c40-305">**データ コンテキスト クラス:** **+** アイコンを選択して、既定の **MvcMovie.Models.MvcMovieContext** を追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![[データの追加] コンテキスト](adding-model/_static/dc.png)

* <span data-ttu-id="02c40-307">**ビュー:** 各オプションの既定値をオンにします。</span><span class="sxs-lookup"><span data-stu-id="02c40-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="02c40-308">**コントローラー名:** 既定の *MoviesController* のままにします。</span><span class="sxs-lookup"><span data-stu-id="02c40-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="02c40-309">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-309">Select **Add**</span></span>

![[コントローラーの追加] ダイアログ](adding-model/_static/add_controller2.png)

<span data-ttu-id="02c40-311">Visual Studio では、次が作成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-311">Visual Studio creates:</span></span>

* <span data-ttu-id="02c40-312">Entity Framework Core の [データベース コンテキスト クラス](xref:data/ef-mvc/intro#create-the-database-context)( *Data/MvcMovieContext.cs* )</span><span class="sxs-lookup"><span data-stu-id="02c40-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="02c40-313">ムービー コントローラー ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="02c40-313">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="02c40-314">作成、削除、詳細、編集、およびインデックス ページ用の :::no-loc(Razor)::: ビュー ファイル ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="02c40-314">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="02c40-315">データベース コンテキストと [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (作成、読み取り、更新、および削除) アクション メソッドとビューの自動作成は、 *スキャフォールディング* と言います。</span><span class="sxs-lookup"><span data-stu-id="02c40-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02c40-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02c40-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="02c40-317">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="02c40-317">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="02c40-318">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="02c40-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="02c40-319">Linux で、スキャフォールディング ツールのパスをエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="02c40-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="02c40-320">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="02c40-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02c40-321">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02c40-322">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="02c40-322">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="02c40-323">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="02c40-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="02c40-324">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="02c40-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="02c40-325">アプリを実行し、 **[MVC Movie]\(MVC ムービー\)** リンクをクリックすると、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPool:::no-loc(Identity)::: identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-327">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="02c40-328">データベースを作成する必要があり、それには EF Core [移行](xref:data/ef-mvc/migrations)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="02c40-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="02c40-329">移行では、データ モデルに一致するデータベースを作成し、データ モデルの変更時にデータベース スキーマを更新することができます。</span><span class="sxs-lookup"><span data-stu-id="02c40-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="02c40-330">最初の移行</span><span class="sxs-lookup"><span data-stu-id="02c40-330">Initial migration</span></span>

<span data-ttu-id="02c40-331">このセクションでは、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="02c40-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="02c40-332">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="02c40-332">Add an initial migration.</span></span>
* <span data-ttu-id="02c40-333">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="02c40-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="02c40-335">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="02c40-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC メニュー](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="02c40-337">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="02c40-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="02c40-338">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="02c40-339">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="02c40-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="02c40-340">`Initial` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="02c40-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="02c40-341">任意の名前を使用できますが、慣例により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="02c40-342">詳細については、「<xref:data/ef-mvc/migrations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="02c40-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="02c40-343">`Update-Database` コマンドは、データベースを作成する、 *Migrations/{time-stamp}_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="02c40-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-344">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="02c40-345">`ef migrations add InitialCreate` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="02c40-346">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます ( *Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="02c40-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="02c40-347">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="02c40-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="02c40-348">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="02c40-349">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="02c40-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="02c40-350">ASP.NET Core には、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="02c40-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="02c40-351">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に DI に登録されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="02c40-352">これらのサービスを必要とするコンポーネント (:::no-loc(Razor)::: Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-352">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="02c40-353">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="02c40-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02c40-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02c40-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02c40-355">スキャフォールディング ツールが自動的に DB コンテキストを作成し、それを DI コンテナーに登録します。</span><span class="sxs-lookup"><span data-stu-id="02c40-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="02c40-356">次の `Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="02c40-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="02c40-357">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="02c40-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="02c40-358">`MvcMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="02c40-359">データ コンテキスト (`MvcMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="02c40-360">データ コンテキストによって、データ モデルに含めるエンティティが指定されます:</span><span class="sxs-lookup"><span data-stu-id="02c40-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="02c40-361">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="02c40-362">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="02c40-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="02c40-363">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="02c40-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="02c40-364">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="02c40-365">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *:::no-loc(appsettings.json):::* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="02c40-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="02c40-366">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="02c40-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="02c40-367">DB コンテキストを作成し、それを DI コンテナーに登録しました。</span><span class="sxs-lookup"><span data-stu-id="02c40-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="02c40-368">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="02c40-368">Test the app</span></span>

* <span data-ttu-id="02c40-369">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="02c40-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="02c40-370">次のようなデータベースの例外が表示された場合:</span><span class="sxs-lookup"><span data-stu-id="02c40-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="02c40-371">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="02c40-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="02c40-372">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="02c40-372">Test the **Create** link.</span></span> <span data-ttu-id="02c40-373">データを入力して送信します。</span><span class="sxs-lookup"><span data-stu-id="02c40-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="02c40-374">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="02c40-375">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="02c40-376">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="02c40-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="02c40-377">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="02c40-377">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="02c40-378">次の `Startup` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="02c40-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="02c40-379">上のコードで強調表示されている部分は、[依存性の注入](xref:fundamentals/dependency-injection)コンテナーに追加されているムービー データベース コンテキストを示します。</span><span class="sxs-lookup"><span data-stu-id="02c40-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="02c40-380">`services.AddDbContext<MvcMovieContext>(options =>` では、使用するデータベースと接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="02c40-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="02c40-381">`=>` は[ラムダ演算子](/dotnet/articles/csharp/language-reference/operators/lambda-operator)です。</span><span class="sxs-lookup"><span data-stu-id="02c40-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="02c40-382">*Controllers/MoviesController.cs* ファイルを開いて、コンストラクターを調べます。</span><span class="sxs-lookup"><span data-stu-id="02c40-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="02c40-383">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="02c40-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="02c40-384">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="02c40-385">厳密に型指定されたモデルと @model キーワード</span><span class="sxs-lookup"><span data-stu-id="02c40-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="02c40-386">コントローラーで `ViewData` ディクショナリを使ってビューにデータまたはオブジェクトを渡す方法を前に示しました。</span><span class="sxs-lookup"><span data-stu-id="02c40-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="02c40-387">`ViewData` ディクショナリは動的オブジェクトであり、ビューに情報を渡すための便利な遅延バインディングの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="02c40-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="02c40-388">MVC にも、厳密に型指定されたモデル オブジェクトをビューに渡す機能があります。</span><span class="sxs-lookup"><span data-stu-id="02c40-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="02c40-389">この厳密に型指定された方法を使うと、コンパイル時のコードのチェックが向上します。</span><span class="sxs-lookup"><span data-stu-id="02c40-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="02c40-390">スキャフォールディング メカニズムは、メソッドとビューを作成するときに、`MoviesController` クラスとビューでこの方法 (つまり、厳密に型指定されたモデルを渡すこと) を使いました。</span><span class="sxs-lookup"><span data-stu-id="02c40-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="02c40-391">*Controllers/MoviesController.cs* ファイルで生成された `Details` メソッドを調べてください。</span><span class="sxs-lookup"><span data-stu-id="02c40-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="02c40-392">通常、`id` パラメーターはルート データとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="02c40-393">たとえば、`https://localhost:5001/movies/details/1` は次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="02c40-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="02c40-394">コントローラーを `movies` コントローラーに (最初の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="02c40-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="02c40-395">アクションを `details` に (2 番目の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="02c40-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="02c40-396">ID を 1 に (最後の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="02c40-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="02c40-397">次のようにクエリ文字列で `id` を渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="02c40-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="02c40-398">ID 値が指定されない場合、`id` パラメーターは [null 許容型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="02c40-399">ルート データまたはクエリ文字列の値と一致するムービー エンティティを選択するため、[ラムダ式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)が `FirstOrDefaultAsync` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="02c40-400">ムービーが見つかった場合、`Movie` モデルのインスタンスが `Details` ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="02c40-401">*Views/Movies/Details.cshtml* ファイルの内容を確認してください。</span><span class="sxs-lookup"><span data-stu-id="02c40-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="02c40-402">ビュー ファイルの先頭に `@model` ステートメントを含めることで、ビューが期待するオブジェクトの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="02c40-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="02c40-403">ムービー コントローラーを作成したとき、 *Details.cshtml* ファイルの先頭に次の `@model` ステートメントが自動的に追加されています。</span><span class="sxs-lookup"><span data-stu-id="02c40-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="02c40-404">この `@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="02c40-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="02c40-405">たとえば、 *Details.cshtml* ビューでは、コードで厳密に型指定された `Model` オブジェクトを使って、`DisplayNameFor` および `DisplayFor` HTML ヘルパーに各ムービー フィールドを渡しています。</span><span class="sxs-lookup"><span data-stu-id="02c40-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="02c40-406">`Create` および `Edit` のメソッドとビューも、`Movie` モデル オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="02c40-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="02c40-407">Movies コントローラーの *Index.cshtml* ビューと `Index` メソッドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="02c40-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="02c40-408">コードで `View` メソッドを呼び出すときの `List` オブジェクトの作成方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="02c40-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="02c40-409">コードでは、この `Movies` リストを `Index` アクション メソッドからビューに渡しています。</span><span class="sxs-lookup"><span data-stu-id="02c40-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="02c40-410">ムービー コントローラーを作成したとき、スキャフォールディングによって *Index.cshtml* ファイルの先頭に `@model` ステートメントが自動的に追加されています。</span><span class="sxs-lookup"><span data-stu-id="02c40-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="02c40-411">`@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーのリストにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="02c40-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="02c40-412">たとえば、 *Index.cshtml* ビューのコードでは、`foreach` ステートメントを使って厳密に型指定された `Model` オブジェクトのムービーをループ処理しています。</span><span class="sxs-lookup"><span data-stu-id="02c40-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="02c40-413">`Model` オブジェクトは厳密に型指定されているので (`IEnumerable<Movie>` オブジェクトとして)、ループ内の各項目は `Movie` として型指定されます。</span><span class="sxs-lookup"><span data-stu-id="02c40-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="02c40-414">それ以外の利点としては、コンパイル時にコードのチェックが行われます。</span><span class="sxs-lookup"><span data-stu-id="02c40-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02c40-415">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="02c40-415">Additional resources</span></span>

* [<span data-ttu-id="02c40-416">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="02c40-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="02c40-417">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="02c40-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="02c40-418">[前のチュートリアル ビューの追加](adding-view.md)
> [次のチュートリアル データベースの使用](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="02c40-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end

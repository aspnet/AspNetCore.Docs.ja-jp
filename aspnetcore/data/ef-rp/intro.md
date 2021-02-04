---
title: ASP.NET Core での Entity Framework Core を使用した Razor Pages - チュートリアル 1/8
author: rick-anderson
description: Entity Framework Core を使用して Razor Pages アプリを作成する方法について説明します
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: e797228d498ec9b269e718c099a0532bbb4504d3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530243"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="2bb14-103">ASP.NET Core での Entity Framework Core を使用した Razor Pages - チュートリアル 1/8</span><span class="sxs-lookup"><span data-stu-id="2bb14-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="2bb14-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2bb14-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2bb14-105">これは、[ASP.NET Core Razor Pages](xref:razor-pages/index) アプリでの Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="2bb14-106">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="2bb14-107">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="2bb14-108">このチュートリアルでは、コード ファーストのアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="2bb14-109">データベース ファーストのアプローチを使用してこのチュートリアルを実行する方法の詳細については、[こちらの Github イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/16897)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="2bb14-110">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="2bb14-111">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2bb14-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2bb14-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2bb14-112">Prerequisites</span></span>

* <span data-ttu-id="2bb14-113">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="2bb14-116">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="2bb14-116">Database engines</span></span>

<span data-ttu-id="2bb14-117">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="2bb14-118">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="2bb14-119">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2bb14-120">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2bb14-120">Troubleshooting</span></span>

<span data-ttu-id="2bb14-121">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="2bb14-122">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="2bb14-123">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="2bb14-123">The sample app</span></span>

<span data-ttu-id="2bb14-124">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="2bb14-125">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="2bb14-126">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-126">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="2bb14-129">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="2bb14-130">このチュートリアルでは、UI をカスタマイズする方法ではなく、EF Core と ASP.NET Core の使用方法について主に説明します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="2bb14-131">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="2bb14-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2bb14-133">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="2bb14-134">**[新しいプロジェクトの作成]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="2bb14-135">**[新しいプロジェクトの構成]** ダイアログで、 **[プロジェクト名]** に「`ContosoUniversity`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="2bb14-136">コードをコピーするときに各`namespace`が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="2bb14-137">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="2bb14-137">Select **Create**.</span></span>
1. <span data-ttu-id="2bb14-138">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで、次のものを選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="2bb14-139">ドロップダウンで **[.NET Core]** と **[ASP.NET Core 5.0]**</span><span class="sxs-lookup"><span data-stu-id="2bb14-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="2bb14-140">**ASP.NET Core Web アプリ**。</span><span class="sxs-lookup"><span data-stu-id="2bb14-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="2bb14-141">**[作成]** 
      ![[新しい ASP.NET Core プロジェクト] ダイアログ](~/data/ef-rp/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="2bb14-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-rp/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2bb14-143">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="2bb14-144">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="2bb14-145">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="2bb14-145">Set up the site style</span></span>

<span data-ttu-id="2bb14-146">次のコードをコピーして、*Pages/Shared/_Layout.cshtml* ファイルに貼り付けます: </span><span class="sxs-lookup"><span data-stu-id="2bb14-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="2bb14-147">このレイアウト ファイルによってサイト ヘッダー、フッター、およびメニューが設定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="2bb14-148">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2bb14-149">"ContosoUniversity" をいずれも "Contoso University" へ変更。</span><span class="sxs-lookup"><span data-stu-id="2bb14-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="2bb14-150">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-150">There are three occurrences.</span></span>
* <span data-ttu-id="2bb14-151">**[ホーム]** および **[プライバシー]** メニュー エントリが削除されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="2bb14-152">**[バージョン情報]** 、 **[学生]** 、 **[コース]** 、 **[講師]** 、および **[部門]** のエントリが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="2bb14-153">*Pages/Index.cshtml* で、ファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="2bb14-154">上記のコードでは、ASP.NET Core に関するテキストがこのアプリに関するテキストに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="2bb14-155">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="2bb14-156">データ モデル</span><span class="sxs-lookup"><span data-stu-id="2bb14-156">The data model</span></span>

<span data-ttu-id="2bb14-157">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-157">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="2bb14-159">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="2bb14-160">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-160">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="2bb14-162">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="2bb14-163">次のコードを使用して、*Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="2bb14-164">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="2bb14-165">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="2bb14-166">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="2bb14-167">詳細については、[EF Core - キー](/ef/core/modeling/keys?tabs=data-annotations)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="2bb14-168">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="2bb14-169">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="2bb14-170">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="2bb14-171">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="2bb14-172">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="2bb14-173">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="2bb14-174">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="2bb14-175">StudentID は、Enrollment テーブルの *外部キー* です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="2bb14-176">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="2bb14-177">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="2bb14-178">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="2bb14-179">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-179">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="2bb14-181">以下のコードを使用して、*Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="2bb14-182">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="2bb14-183">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="2bb14-184">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="2bb14-185">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="2bb14-186">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="2bb14-187">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="2bb14-188">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="2bb14-189">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="2bb14-190">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="2bb14-191">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="2bb14-192">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="2bb14-193">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="2bb14-194">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="2bb14-195">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="2bb14-196">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="2bb14-197">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-197">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="2bb14-199">以下のコードを使用して、*Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="2bb14-200">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="2bb14-201">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="2bb14-202">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="2bb14-203">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="2bb14-204">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2bb14-204">Scaffold Student pages</span></span>

<span data-ttu-id="2bb14-205">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="2bb14-206">EF Core `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="2bb14-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="2bb14-207">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="2bb14-208">これは <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="2bb14-209">`Student` エンティティの作成、読み取り、更新、および削除 (CRUD) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="2bb14-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bb14-211">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="2bb14-212">**ソリューション エクスプローラー** で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2bb14-213">**[新しいスキャフォールディング アイテムの追加]** ダイアログで次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="2bb14-214">左側のタブで、 **[インストール済み] > [共通] > [Razor Pages]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="2bb14-215">**[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="2bb14-216">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="2bb14-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="2bb14-217">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="2bb14-218">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="2bb14-219">データ コンテキスト名が、`ContosoUniversityContext` ではなく `SchoolContext` で終わるように変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="2bb14-220">更新されたコンテキスト名: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="2bb14-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
    * <span data-ttu-id="2bb14-221">**[追加]** 選択してデータ コンテキスト クラスの追加を完了します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-221">Select **Add** to finish adding the data context class.</span></span>
   * <span data-ttu-id="2bb14-222">**[追加]** を選択して、 **[Razor Pages の追加]** ダイアログを終了します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-222">Select **Add** to finish the **Add Razor Pages** dialog.</span></span>

<span data-ttu-id="2bb14-223">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-223">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2bb14-225">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-225">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="2bb14-226">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-226">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="2bb14-227">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-227">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="2bb14-228">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-228">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="2bb14-229">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-229">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="2bb14-230">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-230">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="2bb14-231">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="2bb14-231">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="2bb14-232">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="2bb14-232">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="2bb14-233">前の手順が失敗した場合は、プロジェクトをビルドし、スキャフォールディング手順を再試行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-233">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="2bb14-234">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-234">The scaffolding process:</span></span>

* <span data-ttu-id="2bb14-235">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-235">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="2bb14-236">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-236">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="2bb14-237">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-237">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="2bb14-238">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-238">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="2bb14-239">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-239">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="2bb14-240">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-240">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="2bb14-241">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-241">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="2bb14-242">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-242">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="2bb14-243">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-243">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="2bb14-244">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="2bb14-244">Database connection string</span></span>

<span data-ttu-id="2bb14-245">スキャフォールディング ツールを使用すると、 *appsettings.json* ファイルに接続文字列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-245">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2bb14-247">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-247">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="2bb14-248">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-248">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="2bb14-249">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-249">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2bb14-251">SQLite 接続文字列を *CU.db* に短縮します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-251">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="2bb14-252">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="2bb14-252">Update the database context class</span></span>

<span data-ttu-id="2bb14-253">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-253">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="2bb14-254">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-254">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2bb14-255">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-255">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="2bb14-256">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-256">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="2bb14-257">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-257">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="2bb14-258">上記のコードは、単数形の `DbSet<Student> Student` から複数形の `DbSet<Student> Students` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-258">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="2bb14-259">Razor Pages のコードが新しい `DBSet` 名と一致するようにするには、`_context.Student.` から</span><span class="sxs-lookup"><span data-stu-id="2bb14-259">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="2bb14-260">`_context.Students.` へとグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-260">to: `_context.Students.`</span></span>

<span data-ttu-id="2bb14-261">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-261">There are 8 occurrences.</span></span>

<span data-ttu-id="2bb14-262">エンティティ セットには複数のエンティティが含まれているため、開発者の多くは `DBSet` プロパティ名を複数形にすることを好みます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-262">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="2bb14-263">強調表示されたコード:</span><span class="sxs-lookup"><span data-stu-id="2bb14-263">The highlighted code:</span></span>

* <span data-ttu-id="2bb14-264">エンティティ セットごとに [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-264">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="2bb14-265">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="2bb14-265">In EF Core terminology:</span></span>
  * <span data-ttu-id="2bb14-266">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-266">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="2bb14-267">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-267">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="2bb14-268"><xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="2bb14-268">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="2bb14-269">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="2bb14-269">`OnModelCreating`:</span></span>
  * <span data-ttu-id="2bb14-270">`SchoolContext` が初期化されたとき、ただしモデルがロックダウンされてコンテキストの初期化に使用される前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-270">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="2bb14-271">チュートリアルの後半で `Student` エンティティが他のエンティティへの参照を取得することから、必須となります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-271">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="2bb14-272">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="2bb14-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2bb14-273">Startup.cs</span></span>

<span data-ttu-id="2bb14-274">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2bb14-275">サービス (`SchoolContext` など) は、アプリの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-275">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="2bb14-276">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-276">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="2bb14-277">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2bb14-278">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="2bb14-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2bb14-280">次の強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="2bb14-280">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2bb14-282">スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-282">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2bb14-283">運用データベースの使用の詳細については、「[開発用に SQLite を、運用環境に SQL Server を使用する](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-283">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="2bb14-284">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2bb14-285">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="2bb14-286">データベース例外フィルターを追加する</span><span class="sxs-lookup"><span data-stu-id="2bb14-286">Add the database exception filter</span></span>

<span data-ttu-id="2bb14-287">次のコードに示すように、`ConfigureServices` に <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> を追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-287">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-288">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="2bb14-289">[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-289">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="2bb14-290">PMC で、次のように入力して NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-290">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="2bb14-292">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet パッケージには Entity Framework Core のエラー ページ用の ASP.NET Core ミドルウェアが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-292">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="2bb14-293">このミドルウェアは、Entity Framework Core の移行に関するエラーを検出して診断するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-293">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="2bb14-294">`AddDatabaseDeveloperPageExceptionFilter` により、[開発環境](xref:fundamentals/environments)で役に立つエラー情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-294">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="2bb14-295">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="2bb14-295">Create the database</span></span>

<span data-ttu-id="2bb14-296">データベースが存在しない場合は、*Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-296">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="2bb14-297">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-297">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="2bb14-298">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-298">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="2bb14-299">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-299">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="2bb14-300">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-300">Delete the database.</span></span> <span data-ttu-id="2bb14-301">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-301">Any existing data is lost.</span></span>
* <span data-ttu-id="2bb14-302">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-302">Change the data model.</span></span> <span data-ttu-id="2bb14-303">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-303">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="2bb14-304">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-304">Run the app.</span></span>
* <span data-ttu-id="2bb14-305">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-305">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="2bb14-306">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-306">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="2bb14-307">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-307">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="2bb14-308">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-308">When that is the case, use migrations.</span></span>

<span data-ttu-id="2bb14-309">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-309">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="2bb14-310">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-310">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="2bb14-311">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2bb14-311">Test the app</span></span>

* <span data-ttu-id="2bb14-312">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-312">Run the app.</span></span>
* <span data-ttu-id="2bb14-313">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-313">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="2bb14-314">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-314">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="2bb14-315">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="2bb14-315">Seed the database</span></span>

<span data-ttu-id="2bb14-316">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-316">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="2bb14-317">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-317">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="2bb14-318">次のコードを使用して、*Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-318">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="2bb14-319">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-319">The code checks if there are any students in the database.</span></span> <span data-ttu-id="2bb14-320">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-320">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="2bb14-321">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-321">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="2bb14-322">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-322">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-323">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-323">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2bb14-324">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-324">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="2bb14-325">`Y` で応答すると、データベースが削除されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-325">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-326">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-326">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2bb14-327">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-327">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="2bb14-328">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-328">Restart the app.</span></span>
* <span data-ttu-id="2bb14-329">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-329">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="2bb14-330">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="2bb14-330">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-331">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bb14-332">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-332">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="2bb14-333">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-333">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="2bb14-334">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-334">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="2bb14-335">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-335">Expand the **Tables** node.</span></span>
* <span data-ttu-id="2bb14-336">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-336">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="2bb14-337">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-337">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-338">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-338">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2bb14-339">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-339">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="2bb14-340">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-340">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="2bb14-341">非同期コード</span><span class="sxs-lookup"><span data-stu-id="2bb14-341">Asynchronous code</span></span>

<span data-ttu-id="2bb14-342">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-342">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="2bb14-343">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-343">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="2bb14-344">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-344">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="2bb14-345">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-345">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="2bb14-346">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-346">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="2bb14-347">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-347">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="2bb14-348">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-348">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="2bb14-349">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-349">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="2bb14-350">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-350">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="2bb14-351">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-351">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="2bb14-352">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="2bb14-352">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="2bb14-353">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="2bb14-353">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="2bb14-354">戻り値の型 `Task` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-354">The `Task` return type represents ongoing work.</span></span>
* <span data-ttu-id="2bb14-355">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-355">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="2bb14-356">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-356">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="2bb14-357">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-357">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="2bb14-358">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-358">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="2bb14-359">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="2bb14-359">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="2bb14-360">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-360">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="2bb14-361">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-361">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="2bb14-362">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-362">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="2bb14-363">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-363">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="2bb14-364">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-364">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="2bb14-365">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-365">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="2bb14-366">パフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="2bb14-366">Performance considerations</span></span>

<span data-ttu-id="2bb14-367">一般に、Web ページで任意の数の行を読み込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-367">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="2bb14-368">クエリでは、ページングまたは制限アプローチを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-368">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="2bb14-369">たとえば、上記のクエリでは `Take` を使用して、返される行を制限できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-369">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2bb14-370">ビューで大きいテーブルを列挙すると、列挙の途中でデータベース例外が発生した場合、部分的に構築された HTTP 200 応答が返される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-370">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="2bb14-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> の既定値は 1024 です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="2bb14-372">次のコードでは、`MaxModelBindingCollectionSize` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-372">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2bb14-373">ページングについては、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-373">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2bb14-374">次の手順</span><span class="sxs-lookup"><span data-stu-id="2bb14-374">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2bb14-375">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="2bb14-375">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2bb14-376">これは、[ASP.NET Core Razor Pages](xref:razor-pages/index) アプリでの Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-376">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="2bb14-377">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-377">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="2bb14-378">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-378">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="2bb14-379">このチュートリアルでは、コード ファーストのアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-379">The tutorial uses the code first approach.</span></span> <span data-ttu-id="2bb14-380">データベース ファーストのアプローチを使用してこのチュートリアルを実行する方法の詳細については、[こちらの Github イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/16897)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-380">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="2bb14-381">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-381">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="2bb14-382">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2bb14-382">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2bb14-383">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2bb14-383">Prerequisites</span></span>

* <span data-ttu-id="2bb14-384">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-384">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-385">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-385">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="2bb14-387">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="2bb14-387">Database engines</span></span>

<span data-ttu-id="2bb14-388">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-388">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="2bb14-389">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-389">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="2bb14-390">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-390">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2bb14-391">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2bb14-391">Troubleshooting</span></span>

<span data-ttu-id="2bb14-392">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-392">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="2bb14-393">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-393">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="2bb14-394">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="2bb14-394">The sample app</span></span>

<span data-ttu-id="2bb14-395">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-395">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="2bb14-396">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-396">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="2bb14-397">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-397">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="2bb14-400">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-400">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="2bb14-401">このチュートリアルでは、UI をカスタマイズする方法ではなく、主に EF Core の使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-401">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="2bb14-402">ページの上部にあるリンクを使用して、完成したプロジェクトのソース コードを取得します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-402">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="2bb14-403">*cu30* フォルダーには、チュートリアルの ASP.NET Core 3.0 バージョン用のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-403">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="2bb14-404">チュートリアル 1-7 のコードの状態を反映するファイルは、*cu30snapshots* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-404">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-405">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2bb14-406">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="2bb14-406">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="2bb14-407">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-407">Build the project.</span></span>
* <span data-ttu-id="2bb14-408">パッケージ マネージャー コンソール (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-408">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="2bb14-409">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-409">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2bb14-411">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="2bb14-411">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="2bb14-412">*ContosoUniversity.csproj* を削除し、*ContosoUniversitySQLite.csproj* の名前を *ContosoUniversity.csproj* に変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-412">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="2bb14-413">*Program.cs* で、`StartupSQLite` が使用されるように `#define Startup` をコメント アウトします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-413">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="2bb14-414">*appSettings.json* を削除し、*appSettingsSQLite.json* の名前を *appSettings.json* に変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-414">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="2bb14-415">*Migrations* フォルダーを削除し、*MigrationsSQL* の名前を *Migrations* に変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-415">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="2bb14-416">`#if SQLiteVersion` のグローバル検索を実行し、`#if SQLiteVersion` と関連する `#endif` ステートメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-416">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="2bb14-417">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-417">Build the project.</span></span>
* <span data-ttu-id="2bb14-418">プロジェクト フォルダーのコマンド プロンプトで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-418">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="2bb14-419">SQLite ツールで、次の SQL ステートメントを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-419">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="2bb14-420">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-420">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="2bb14-421">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="2bb14-421">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bb14-423">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-423">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2bb14-424">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-424">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2bb14-425">プロジェクトに *ContosoUniversity* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-425">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="2bb14-426">コードをコピーして貼り付けるときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-426">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="2bb14-427">ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-427">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-428">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-428">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2bb14-429">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-429">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="2bb14-430">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-430">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="2bb14-431">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="2bb14-431">Set up the site style</span></span>

<span data-ttu-id="2bb14-432">*Pages/Shared/_Layout.cshtml* を更新して、サイト ヘッダー、フッター、およびメニューを設定します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-432">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="2bb14-433">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-433">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="2bb14-434">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-434">There are three occurrences.</span></span>

* <span data-ttu-id="2bb14-435">**Home** メニューと **Privacy** メニューのエントリを削除し、**About**、**Students**、**Courses**、**Instructors**、**Departments** のエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-435">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="2bb14-436">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-436">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="2bb14-437">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET Core に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-437">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="2bb14-438">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-438">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="2bb14-439">データ モデル</span><span class="sxs-lookup"><span data-stu-id="2bb14-439">The data model</span></span>

<span data-ttu-id="2bb14-440">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-440">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="2bb14-442">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-442">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="2bb14-443">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-443">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="2bb14-445">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-445">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="2bb14-446">次のコードを使用して、*Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-446">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="2bb14-447">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-447">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="2bb14-448">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-448">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="2bb14-449">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-449">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="2bb14-450">詳細については、[EF Core - キー](/ef/core/modeling/keys?tabs=data-annotations)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-450">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="2bb14-451">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-451">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="2bb14-452">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-452">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="2bb14-453">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-453">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="2bb14-454">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-454">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="2bb14-455">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-455">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="2bb14-456">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-456">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="2bb14-457">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-457">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="2bb14-458">StudentID は、Enrollment テーブルの *外部キー* です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-458">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="2bb14-459">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-459">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="2bb14-460">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-460">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="2bb14-461">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-461">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="2bb14-462">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-462">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="2bb14-464">以下のコードを使用して、*Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-464">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="2bb14-465">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-465">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="2bb14-466">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-466">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="2bb14-467">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-467">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="2bb14-468">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-468">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="2bb14-469">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-469">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="2bb14-470">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-470">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="2bb14-471">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-471">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="2bb14-472">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-472">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="2bb14-473">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-473">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="2bb14-474">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-474">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="2bb14-475">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-475">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="2bb14-476">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-476">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="2bb14-477">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-477">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="2bb14-478">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-478">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="2bb14-479">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-479">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="2bb14-480">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-480">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="2bb14-482">以下のコードを使用して、*Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-482">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="2bb14-483">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-483">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="2bb14-484">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-484">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="2bb14-485">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-485">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="2bb14-486">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-486">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="2bb14-487">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2bb14-487">Scaffold Student pages</span></span>

<span data-ttu-id="2bb14-488">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-488">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="2bb14-489">EF Core *コンテキスト* クラス。</span><span class="sxs-lookup"><span data-stu-id="2bb14-489">An EF Core *context* class.</span></span> <span data-ttu-id="2bb14-490">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-490">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="2bb14-491">これは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-491">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="2bb14-492">`Student` エンティティの作成、読み取り、更新、および削除 (CRUD) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="2bb14-492">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-493">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bb14-494">*Pages* フォルダー内に *Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-494">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="2bb14-495">**ソリューション エクスプローラー** で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-495">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2bb14-496">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-496">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="2bb14-497">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="2bb14-497">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="2bb14-498">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-498">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="2bb14-499">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-499">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="2bb14-500">データ コンテキスト名を *ContosoUniversity.Models.ContosoUniversityContext* から *ContosoUniversity.Data.SchoolContext* に変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-500">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="2bb14-501">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-501">Select **Add**.</span></span>

<span data-ttu-id="2bb14-502">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-502">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-503">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-503">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2bb14-504">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-504">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="2bb14-505">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-505">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="2bb14-506">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-506">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="2bb14-507">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-507">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="2bb14-508">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-508">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="2bb14-509">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-509">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="2bb14-510">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="2bb14-510">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="2bb14-511">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="2bb14-511">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="2bb14-512">前の手順で問題が発生した場合は、プロジェクトをビルドし、スキャフォールディング ステップを再試行してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-512">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="2bb14-513">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-513">The scaffolding process:</span></span>

* <span data-ttu-id="2bb14-514">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-514">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="2bb14-515">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-515">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="2bb14-516">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-516">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="2bb14-517">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-517">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="2bb14-518">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-518">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="2bb14-519">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-519">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="2bb14-520">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-520">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="2bb14-521">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-521">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="2bb14-522">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-522">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="2bb14-523">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="2bb14-523">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-524">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-524">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2bb14-525">*appsettings.json* ファイルでは、接続文字列 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-525">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="2bb14-526">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="2bb14-527">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-527">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-528">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-528">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2bb14-529">*CU.db* という名前の SQLite データベース ファイルを指すように接続文字列を変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-529">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="2bb14-530">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="2bb14-530">Update the database context class</span></span>

<span data-ttu-id="2bb14-531">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-531">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="2bb14-532">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-532">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2bb14-533">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-533">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="2bb14-534">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-534">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="2bb14-535">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-535">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="2bb14-536">強調表示されたコードによって、各エンティティ セットの [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-536">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="2bb14-537">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="2bb14-537">In EF Core terminology:</span></span>

* <span data-ttu-id="2bb14-538">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-538">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="2bb14-539">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-539">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2bb14-540">エンティティ セットには複数のエンティティが含まれているため、DBSet プロパティは複数形の名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-540">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="2bb14-541">スキャフォールディング ツールによって `Student` DBSet を作成したので、このステップでこれを複数形の `Students` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-541">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="2bb14-542">Razor Pages のコードを新しい DBSet 名と一致させるには、プロジェクト全体で `_context.Student` を `_context.Students` にグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-542">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="2bb14-543">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-543">There are 8 occurrences.</span></span>

<span data-ttu-id="2bb14-544">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-544">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="2bb14-545">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2bb14-545">Startup.cs</span></span>

<span data-ttu-id="2bb14-546">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-546">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2bb14-547">サービス (EF Core データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-547">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2bb14-548">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-548">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2bb14-549">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-549">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2bb14-550">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="2bb14-550">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-551">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-551">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bb14-552">`ConfigureServices` では、強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="2bb14-552">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-553">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-553">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2bb14-554">`ConfigureServices` では、スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-554">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="2bb14-555">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-555">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2bb14-556">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-556">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="2bb14-557">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="2bb14-557">Create the database</span></span>

<span data-ttu-id="2bb14-558">データベースが存在しない場合は、*Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-558">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="2bb14-559">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-559">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="2bb14-560">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-560">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="2bb14-561">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-561">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="2bb14-562">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-562">Delete the database.</span></span> <span data-ttu-id="2bb14-563">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-563">Any existing data is lost.</span></span>
* <span data-ttu-id="2bb14-564">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-564">Change the data model.</span></span> <span data-ttu-id="2bb14-565">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-565">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="2bb14-566">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-566">Run the app.</span></span>
* <span data-ttu-id="2bb14-567">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-567">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="2bb14-568">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-568">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="2bb14-569">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-569">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="2bb14-570">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-570">When that is the case, use migrations.</span></span>

<span data-ttu-id="2bb14-571">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-571">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="2bb14-572">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-572">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="2bb14-573">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2bb14-573">Test the app</span></span>

* <span data-ttu-id="2bb14-574">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-574">Run the app.</span></span>
* <span data-ttu-id="2bb14-575">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-575">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="2bb14-576">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-576">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="2bb14-577">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="2bb14-577">Seed the database</span></span>

<span data-ttu-id="2bb14-578">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-578">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="2bb14-579">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-579">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="2bb14-580">次のコードを使用して、*Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-580">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="2bb14-581">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-581">The code checks if there are any students in the database.</span></span> <span data-ttu-id="2bb14-582">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-582">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="2bb14-583">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-583">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="2bb14-584">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-584">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-585">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-585">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2bb14-586">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-586">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-587">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-587">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2bb14-588">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-588">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="2bb14-589">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-589">Restart the app.</span></span>

* <span data-ttu-id="2bb14-590">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-590">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="2bb14-591">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="2bb14-591">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-592">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-592">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bb14-593">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-593">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="2bb14-594">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-594">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="2bb14-595">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-595">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="2bb14-596">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-596">Expand the **Tables** node.</span></span>
* <span data-ttu-id="2bb14-597">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-597">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="2bb14-598">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-598">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-599">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-599">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2bb14-600">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-600">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="2bb14-601">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-601">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="2bb14-602">非同期コード</span><span class="sxs-lookup"><span data-stu-id="2bb14-602">Asynchronous code</span></span>

<span data-ttu-id="2bb14-603">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-603">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="2bb14-604">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-604">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="2bb14-605">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-605">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="2bb14-606">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-606">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="2bb14-607">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-607">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="2bb14-608">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-608">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="2bb14-609">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-609">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="2bb14-610">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-610">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="2bb14-611">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-611">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="2bb14-612">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-612">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="2bb14-613">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="2bb14-613">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="2bb14-614">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="2bb14-614">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="2bb14-615">戻り値の型 `Task<T>` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-615">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="2bb14-616">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-616">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="2bb14-617">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-617">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="2bb14-618">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-618">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="2bb14-619">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-619">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="2bb14-620">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="2bb14-620">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="2bb14-621">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-621">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="2bb14-622">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-622">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="2bb14-623">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-623">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="2bb14-624">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-624">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="2bb14-625">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-625">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="2bb14-626">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-626">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2bb14-627">次の手順</span><span class="sxs-lookup"><span data-stu-id="2bb14-627">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2bb14-628">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="2bb14-628">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2bb14-629">Contoso University のサンプル Web アプリでは、Entity Framework (EF) Core を使用して ASP.NET Core Razor Pages アプリを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-629">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="2bb14-630">このサンプル アプリは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-630">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="2bb14-631">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-631">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="2bb14-632">このページは、Contoso University のサンプル アプリを作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-632">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="2bb14-633">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-633">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="2bb14-634">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2bb14-634">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2bb14-635">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2bb14-635">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-636">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-636">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-637">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-637">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="2bb14-638">[Razor Pages](xref:razor-pages/index) に関する知識。</span><span class="sxs-lookup"><span data-stu-id="2bb14-638">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="2bb14-639">そのプログラミング経験をお持ちでない場合は、このシリーズを始める前に [Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するチュートリアルを完了してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-639">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2bb14-640">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2bb14-640">Troubleshooting</span></span>

<span data-ttu-id="2bb14-641">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-641">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="2bb14-642">ヘルプが必要なときは、[StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) について質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-642">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="2bb14-643">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="2bb14-643">The Contoso University web app</span></span>

<span data-ttu-id="2bb14-644">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-644">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="2bb14-645">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-645">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="2bb14-646">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-646">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

<span data-ttu-id="2bb14-649">このサイトの UI スタイルは、組み込みのテンプレートによって生成されるものに類似しています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-649">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="2bb14-650">このチュートリアルでは、UI ではなく、EF Core と Razor ページに重点を置きます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-650">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="2bb14-651">ContosoUniversity Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2bb14-651">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-652">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-652">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bb14-653">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-653">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2bb14-654">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-654">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="2bb14-655">プロジェクトに **ContosoUniversity** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-655">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="2bb14-656">このプロジェクトに *ContosoUniversity* という名前を付けることは重要です。そうすることでコードをコピーしたり貼り付けるときに名前空間が一致します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-656">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="2bb14-657">ドロップダウン リストで **[ASP.NET Core 2.1]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-657">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="2bb14-658">前の手順の画像については、[Razor Web アプリの作成](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-658">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="2bb14-659">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-659">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-660">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-660">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="2bb14-661">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="2bb14-661">Set up the site style</span></span>

<span data-ttu-id="2bb14-662">変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-662">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="2bb14-663">*Pages/Shared/_Layout.cshtml* に次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-663">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="2bb14-664">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-664">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="2bb14-665">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-665">There are three occurrences.</span></span>

* <span data-ttu-id="2bb14-666">メニュー エントリとして「**Students**」、「**Courses**」、「**Instructors**」、「**Departments**」を追加し、「**Contact**」を削除します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-666">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="2bb14-667">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-667">The changes are highlighted.</span></span> <span data-ttu-id="2bb14-668">(マークアップは一部表示されて *いません*。)</span><span class="sxs-lookup"><span data-stu-id="2bb14-668">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="2bb14-669">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-669">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="2bb14-670">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="2bb14-670">Create the data model</span></span>

<span data-ttu-id="2bb14-671">Contoso University アプリのエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-671">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="2bb14-672">次の 3 つのエンティティから始めます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-672">Start with the following three entities:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="2bb14-674">`Student` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-674">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="2bb14-675">`Course` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-675">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="2bb14-676">1 人の学生をさまざまな講座に登録できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-676">A student can enroll in any number of courses.</span></span> <span data-ttu-id="2bb14-677">1 つの講座に任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-677">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="2bb14-678">次のセクションでは、エンティティごとにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-678">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="2bb14-679">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-679">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="2bb14-681">*Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-681">Create a *Models* folder.</span></span> <span data-ttu-id="2bb14-682">以下のコードを使用して、*Models* フォルダーで、*Student.cs* という名前のクラス ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-682">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="2bb14-683">`ID` プロパティは、このクラスに相当するデータベース (DB) テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-683">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="2bb14-684">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-684">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="2bb14-685">`classnameID` の `classname` はクラスの名前です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-685">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="2bb14-686">代わりの自動的に認識される主キーは、前の例の `StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-686">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="2bb14-687">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-687">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="2bb14-688">ナビゲーション プロパティは、このエンティティに関連する他のエンティティにリンクします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-688">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="2bb14-689">この例では、`Student entity` の `Enrollments` プロパティによって、その `Student` に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-689">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="2bb14-690">たとえば、DB 内のある Student 行に 2 つ関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-690">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="2bb14-691">関連する `Enrollment` 行とは、その学生の主キー値を `StudentID` 列に含む行です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-691">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="2bb14-692">たとえば、ID=1 の学生には、`Enrollment` テーブルに行が 2 つあるとします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-692">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="2bb14-693">その `Enrollment` テーブルには、`StudentID` = 1 の行が 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-693">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="2bb14-694">`StudentID` は `Enrollment` テーブルの外部キーであり、`Student` テーブルでその学生を指定します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-694">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="2bb14-695">あるナビゲーション プロパティが複数のエンティティを保持できる場合、そのナビゲーション プロパティは `ICollection<T>` のようなリスト型にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-695">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="2bb14-696">`ICollection<T>` を指定するか、`List<T>` や `HashSet<T>` のような型を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-696">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="2bb14-697">`ICollection<T>` を使用する場合、EF Core で `HashSet<T>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-697">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="2bb14-698">複数のエンティティを保持するナビゲーション プロパティは、多対多または一対多の関係から発生します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-698">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="2bb14-699">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-699">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="2bb14-701">以下のコードを使用して、*Models* フォルダーで、*Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-701">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="2bb14-702">`EnrollmentID` プロパティは主キーです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-702">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="2bb14-703">このエンティティでは、`Student` エンティティのような `ID` ではなく、`classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-703">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="2bb14-704">一般的に、開発者は 1 つのパターンを選択し、データ モデル全体でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-704">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="2bb14-705">後のチュートリアルでは、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-705">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="2bb14-706">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-706">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="2bb14-707">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-707">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="2bb14-708">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-708">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="2bb14-709">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-709">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="2bb14-710">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-710">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="2bb14-711">`Student` エンティティは、複数の `Enrollment` エンティティが含まれる `Student.Enrollments` ナビゲーション プロパティとは異なります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-711">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="2bb14-712">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-712">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="2bb14-713">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-713">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="2bb14-714">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-714">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="2bb14-715">たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーは `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-715">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="2bb14-716">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-716">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="2bb14-717">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-717">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="2bb14-718">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="2bb14-718">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="2bb14-720">以下のコードを使用して、*Models* フォルダーで、*Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-720">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="2bb14-721">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-721">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="2bb14-722">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-722">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="2bb14-723">`DatabaseGenerated` 属性によって、アプリで主キーを指定できます。DB に生成させるのではありません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-723">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="2bb14-724">Student モデルをスキャホールディングする</span><span class="sxs-lookup"><span data-stu-id="2bb14-724">Scaffold the student model</span></span>

<span data-ttu-id="2bb14-725">このセクションでは、Student モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-725">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="2bb14-726">つまり、スキャフォールディング ツールにより、Student モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-726">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="2bb14-727">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-727">Build the project.</span></span>
* <span data-ttu-id="2bb14-728">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-728">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-729">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-729">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2bb14-730">**ソリューション エクスプローラー** で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-730">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2bb14-731">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-731">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="2bb14-732">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-732">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2bb14-733">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-733">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="2bb14-734">**[データ コンテキスト クラス]** 行で **+** (+) 記号を選択し、生成された名前を **ContosoUniversity.Models.SchoolContext** に変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-734">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="2bb14-735">**[データ コンテキスト クラス]** ドロップダウンで、 **[ContosoUniversity.Models.SchoolContext]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-735">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="2bb14-736">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-736">Select **Add**.</span></span>

![CRUD ダイアログ](intro/_static/s1.png)

<span data-ttu-id="2bb14-738">前の手順に問題がある場合は、「[ムービー モデルのスキャフォールディング](xref:tutorials/razor-pages/model#scaffold-the-movie-model)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-738">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-739">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-739">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2bb14-740">次のコマンドを実行して、Student モデルをスキャホールディングします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-740">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="2bb14-741">スキャフォールディングのプロセスが作成され、次のファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-741">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2bb14-742">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="2bb14-742">Files created</span></span>

* <span data-ttu-id="2bb14-743">*Pages/Students* 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="2bb14-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="2bb14-744">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2bb14-744">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="2bb14-745">ファイルの更新</span><span class="sxs-lookup"><span data-stu-id="2bb14-745">File updates</span></span>

* <span data-ttu-id="2bb14-746">*Startup.cs*:このファイルに対する変更を次のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-746">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="2bb14-747">*appsettings.json* :ローカル データベースへの接続に使用される接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-747">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2bb14-748">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="2bb14-748">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2bb14-749">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-749">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2bb14-750">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-750">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2bb14-751">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-751">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2bb14-752">db コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-752">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2bb14-753">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-753">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2bb14-754">*Startup.cs* の `ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-754">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="2bb14-755">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="2bb14-755">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="2bb14-756">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-756">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2bb14-757">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-757">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="2bb14-758">main を更新する</span><span class="sxs-lookup"><span data-stu-id="2bb14-758">Update main</span></span>

<span data-ttu-id="2bb14-759">*Program.cs* で、次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-759">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="2bb14-760">依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-760">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="2bb14-761">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-761">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="2bb14-762">`EnsureCreated` メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-762">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="2bb14-763">次は、更新された *Program.cs* ファイルのコードです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-763">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="2bb14-764">`EnsureCreated` で、コンテキストのデータベースが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-764">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="2bb14-765">存在する場合、アクションは何も実行されません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-765">If it exists, no action is taken.</span></span> <span data-ttu-id="2bb14-766">存在しない場合は、データベースとそのすべてのスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-766">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="2bb14-767">`EnsureCreated` は、データベースの作成に移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-767">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="2bb14-768">`EnsureCreated` で作成されたデータベースは、後で移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-768">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="2bb14-769">アプリの起動時に `EnsureCreated` が呼び出され、次のワークフローが可能になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-769">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="2bb14-770">DB を削除します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-770">Delete the DB.</span></span>
* <span data-ttu-id="2bb14-771">DB スキーマを変更します (たとえば、`EmailAddress` フィールドを追加します)。</span><span class="sxs-lookup"><span data-stu-id="2bb14-771">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="2bb14-772">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-772">Run the app.</span></span>
* <span data-ttu-id="2bb14-773">`EnsureCreated` によって、`EmailAddress` 列を含む DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-773">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="2bb14-774">`EnsureCreated` は、スキーマが急速に進化している開発の早期に便利です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-774">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="2bb14-775">このチュートリアルの後半では、DB は削除され、移行が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-775">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="2bb14-776">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2bb14-776">Test the app</span></span>

<span data-ttu-id="2bb14-777">アプリを実行し、cookie ポリシーに同意します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-777">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="2bb14-778">このアプリで個人情報が保持されることはありません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-778">This app doesn't keep personal information.</span></span> <span data-ttu-id="2bb14-779">cookie ポリシーについては、[EU の一般的なデータ保護規制 (GDPR) のサポート](xref:security/gdpr)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-779">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="2bb14-780">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-780">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="2bb14-781">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-781">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="2bb14-782">SchoolContext DB コンテキストを確認する</span><span class="sxs-lookup"><span data-stu-id="2bb14-782">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="2bb14-783">所与のデータ モデルの EF Core 機能を調整するメイン クラスは、DB コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-783">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="2bb14-784">データ コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-784">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2bb14-785">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-785">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="2bb14-786">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-786">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="2bb14-787">次のコードを使用して *SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-787">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="2bb14-788">強調表示されたコードによって、各エンティティ セットの [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-788">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="2bb14-789">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="2bb14-789">In EF Core terminology:</span></span>

* <span data-ttu-id="2bb14-790">エンティティ セットは通常、DB テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-790">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="2bb14-791">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-791">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2bb14-792">`DbSet<Enrollment>` と `DbSet<Course>` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-792">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="2bb14-793">EF Core にはそれらが暗黙的に含まれます。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-793">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="2bb14-794">このチュートリアルでは、`SchoolContext` に `DbSet<Enrollment>` と `DbSet<Course>` を残します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-794">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="2bb14-795">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="2bb14-795">SQL Server Express LocalDB</span></span>

<span data-ttu-id="2bb14-796">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-796">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="2bb14-797">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-797">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="2bb14-798">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-798">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="2bb14-799">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* DB ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-799">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="2bb14-800">テスト データで DB を初期化するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="2bb14-800">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="2bb14-801">EF Core によって空の DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-801">EF Core creates an empty DB.</span></span> <span data-ttu-id="2bb14-802">このセクションでは、それにテスト データを入力するために `Initialize` メソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-802">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="2bb14-803">*Data* フォルダーで、*DbInitializer.cs* という名前の新しいクラス ファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-803">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="2bb14-804">メモ:上のコードでは、名前空間 (`namespace ContosoUniversity.Models`) に `Data` ではなく `Models` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="2bb14-804">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="2bb14-805">`Models` はスキャフォールディング機能によって生成されたコードと一致します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-805">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="2bb14-806">詳細については、[こちらの GitHub のスキャフォールディングの問題](https://github.com/aspnet/Scaffolding/issues/822)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-806">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="2bb14-807">このコードは、DB に学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-807">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="2bb14-808">DB に学生が存在しない場合、テスト データを使用して DB が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-808">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="2bb14-809">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-809">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="2bb14-810">`EnsureCreated` メソッドは、DB のコンテキストに合わせて DB を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-810">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="2bb14-811">DB が存在する場合、`EnsureCreated` は DB を変更することなく戻ってきます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-811">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="2bb14-812">*Program.cs* で、`Initialize` を呼び出すように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-812">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="2bb14-813">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2bb14-813">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2bb14-814">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-814">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2bb14-815">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2bb14-815">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2bb14-816">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-816">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="2bb14-817">DB を表示する</span><span class="sxs-lookup"><span data-stu-id="2bb14-817">View the DB</span></span>

<span data-ttu-id="2bb14-818">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-818">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="2bb14-819">したがって、データベース名は "SchoolContext-{GUID}" になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-819">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="2bb14-820">GUID は、ユーザーごとに異なります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-820">The GUID will be different for each user.</span></span>
<span data-ttu-id="2bb14-821">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-821">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="2bb14-822">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2bb14-822">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="2bb14-823">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-823">Expand the **Tables** node.</span></span>

<span data-ttu-id="2bb14-824">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-824">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="2bb14-825">非同期コード</span><span class="sxs-lookup"><span data-stu-id="2bb14-825">Asynchronous code</span></span>

<span data-ttu-id="2bb14-826">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-826">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="2bb14-827">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-827">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="2bb14-828">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-828">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="2bb14-829">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-829">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="2bb14-830">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-830">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="2bb14-831">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-831">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="2bb14-832">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="2bb14-832">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="2bb14-833">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-833">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="2bb14-834">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-834">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="2bb14-835">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-835">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="2bb14-836">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="2bb14-836">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="2bb14-837">返された [Task](/dotnet/api/system.threading.tasks.task) オブジェクトを自動作成する。</span><span class="sxs-lookup"><span data-stu-id="2bb14-837">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="2bb14-838">詳細については、[Task の戻り値の型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-838">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="2bb14-839">暗黙の戻り値の型 `Task` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-839">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="2bb14-840">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-840">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="2bb14-841">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-841">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="2bb14-842">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="2bb14-842">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="2bb14-843">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="2bb14-843">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="2bb14-844">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="2bb14-844">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="2bb14-845">クエリやコマンドを DB に送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-845">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="2bb14-846">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2bb14-846">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="2bb14-847">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="2bb14-847">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="2bb14-848">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-848">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="2bb14-849">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-849">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="2bb14-850">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bb14-850">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="2bb14-851">次のチュートリアルでは、基本的な CRUD (作成、読み取り、更新、削除) の操作について説明します。</span><span class="sxs-lookup"><span data-stu-id="2bb14-851">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="2bb14-852">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2bb14-852">Additional resources</span></span>

* [<span data-ttu-id="2bb14-853">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="2bb14-853">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="2bb14-854">次へ</span><span class="sxs-lookup"><span data-stu-id="2bb14-854">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end

---
title: パート 2、ASP.NET Core の Razor Pages と EF Core - CRUD
author: rick-anderson
description: Razor Pages と Entity Framework チュートリアル シリーズのパート 2。
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/crud
ms.openlocfilehash: c5b9be64ea30cce7a3178bfbb244ef893e9639d2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053866"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a>パート 2、ASP.NET Core の Razor Pages と EF Core - CRUD

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

このチュートリアルでは、スキャフォールディング CRUD (作成、読み取り、更新、削除) コードのレビューとカスタマイズを行います。

## <a name="no-repository"></a>リポジトリがない

一部の開発者は、サービス レイヤーまたはリポジトリ パターンを使用して、UI (Razor Pages) とデータ アクセス層との間に抽象化レイヤーを作成しています。 このチュートリアルでは、これは行いません。 複雑さを最小限に抑え、チュートリアルの焦点を EF Core に置くために、EF Core コードがページ モデル クラスに直接追加されます。 

## <a name="update-the-details-page"></a>Details ページを更新する

Students ページのスキャフォールディング コードには、登録データが含まれていません。 このセクションでは、Details ページに登録を追加します。

### <a name="read-enrollments"></a>登録の読み取り

学生の登録データをページに表示するには、そのデータを読み取る必要があります。 *Pages/Students/Details.cshtml.cs* のスキャフォールディング コードでは、Enrollment データを含まない Student データのみが読み取られます。

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

`OnGetAsync` メソッドを次のコードに置き換えて、選択した学生の登録データを読み取ります。 変更が強調表示されます。

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) メソッドと [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。 これらのメソッドは、[関連データの読み込み](xref:data/ef-rp/read-related-data)のチュートリアルで詳しく検討します。

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) メソッドは、返されたエンティティが現在のコンテキストで更新されないシナリオでパフォーマンスを改善します。 `AsNoTracking` は、このチュートリアルで後述します。

### <a name="display-enrollments"></a>登録の表示

*Pages/Students/Details.cshtml* 内のコードを次のコードに置き換えて、登録のリストを表示します。 変更が強調表示されます。

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

上記のコードは、`Enrollments` ナビゲーション プロパティ内でエンティティをループ処理します。 登録ごとに、コースのタイトルとグレードが表示されます。 コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。

アプリを実行し、 **[Students]** タブを選択し、学生用の **[詳細]** リンクをクリックします。 選択した受講者のコースとグレードの一覧が表示されます。

### <a name="ways-to-read-one-entity"></a>1 つのエンティティを読み取る方法

生成されたコードでは、[FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) を使用して 1 つのエンティティを読み取ります。 このメソッドでは、何も見つからない場合は null が返されます。それ以外の場合は、クエリのフィルター条件を満たす最初の行が返されます。 `FirstOrDefaultAsync` は、通常、次の代替手段よりも適しています。

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - クエリ フィルターを満たす複数のエンティティがある場合に、例外をスローします。 クエリによって複数の行が返される可能性があるかどうかを判断するため、`SingleOrDefaultAsync` は複数の行をフェッチしようとします。 一意のキーを検索する場合と同様に、クエリが 1 つのエンティティだけを返すことができる場合は、この追加作業は不要です。
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 主キー (PK) を持つエンティティを検索します。 PK を持つエンティティがコンテキストによって追跡されている場合、データベースに対する要求がなくても該当するエンティティが返されます。 このメソッドは、単一のエンティティを検索するように最適化されていますが、`FindAsync` を使用して `Include` を呼び出すことはできません。  したがって、関連データが必要な場合は、`FirstOrDefaultAsync` を選択することをお勧めします。

### <a name="route-data-vs-query-string"></a>ルート データとクエリ文字列

Details ページの URL は `https://localhost:<port>/Students/Details?id=1` です。 エンティティの主キー値がクエリ文字列に含まれています。 ルート データのキー値を渡すことを好む開発者もいます。`https://localhost:<port>/Students/Details/1` 詳細については、「[生成されたコードの更新](xref:tutorials/razor-pages/da1#update-the-generated-code)」を参照してください。

## <a name="update-the-create-page"></a>Create ページを更新する

Create ページのスキャフォールディングされた `OnPostAsync` コードは、[過剰ポスティング](#overposting)に対して脆弱です。 *Pages/Students/Create.cshtml.cs* の `OnPostAsync` メソッドを次のコードに置き換えます。

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

上記のコードでは、Student オブジェクトを作成し、ポストされたフォーム フィールドを使用して Student オブジェクトのプロパティを更新します。 [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) メソッド:

* [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) の [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) プロパティからポストされたフォーム値を使用します。
* リストされたプロパティのみを更新します (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。
* "student" のプレフィックスを持つフォーム フィールドを検索します。 たとえば、`Student.FirstMidName` のようにします。 大文字と小文字の区別はありません。
* [モデル バインド](xref:mvc/models/model-binding) システムを使用して、文字列からフォーム値を `Student` モデル内の型に変換します。 たとえば、`EnrollmentDate` は `DateTime` に変換されます。

アプリを実行し、Create ページをテストする student エンティティを作成します。

## <a name="overposting"></a>過剰ポスティング

ポストされた値を持つフィールドを更新するために `TryUpdateModel` を使用することは、過剰ポスティングの防止につながり、セキュリティ上のベスト プラクティスとなります。 たとえば、Student エンティティには、この Web ページで更新または追加できない `Secret` プロパティが含まれています。

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

アプリの作成または更新の Razor ページに `Secret` フィールドが含まれていない場合でも、ハッカーは過剰ポスティングによって `Secret` 値を設定することが可能です。 ハッカーは、Fiddler などのツールを使用するか、または何らかの JavaScript を作成して、`Secret` フォーム値をポストすることが可能です。 元のコードでは、Student インスタンスの作成時にモデル バインダーによって使用されるフィールドを制限していません。

`Secret` フォーム フィールドに対してハッカーが指定した値はいずれも、データベース内で更新されます。 次の図は、ポストされたフォームの値に、値 "OverPost" が含まれる `Secret` フィールドを追加している Fiddler ツールを示しています。

![Fiddler による Secret フィールドの追加](../ef-mvc/crud/_static/fiddler.png)

挿入された行の `Secret` プロパティに値 "OverPost" が正常に追加されています。 これは、アプリ デザイナーで、Create ページで `Secret` プロパティが設定されることを想定していない場合でも発生します。

### <a name="view-model"></a>ビュー モデル

ビュー モデルは、過剰ポスティングを防ぐもう 1 つの方法を提供します。

アプリケーション モデルは、しばしばドメイン モデルと呼ばれます。 ドメイン モデルには、通常、データベース内の対応するエンティティによって必要とされるすべてのプロパティが含まれています。 ビュー モデルには、Create ページなどの UI ページで必要なプロパティのみが含まれています。

一部のアプリでは、ビュー モデルに加えて、Razor Pages のページ モデル クラスとブラウザーとの間でデータを渡すためにバインド モデルまたは入力モデルも使用します。 

次の `StudentVM` ビュー モデルを考えてみます。

[!code-csharp[Main](intro/samples/cu50/ViewModels/StudentVM.cs?name=snippet)]

次のコードでは、`StudentVM` ビュー モデルを使用して新しい受講生を作成します。

[!code-csharp[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml.cs?name=snippet)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) メソッドでは、このオブジェクトの値を設定するために、別の [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) オブジェクトから値を読み取ります。 `SetValues` では一致するプロパティ名が使用されます。 ビューモデルには、次の種類があります。

* モデルの種類に関連付けられる必要はありません。
* 一致するプロパティを持つ必要があります。

`StudentVM` を使用するには、Create ページで、`Student` ではなく `StudentVM` を使用する必要があります。

[!code-cshtml[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml)]

## <a name="update-the-edit-page"></a>[編集] ページを更新する

*Pages/Students/Edit.cshtml.cs* で、`OnGetAsync` メソッドと `OnPostAsync` メソッドを次のコードに置き換えます。

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

コードの変更は [作成] ページに似ています。ただし、次のようないくつかの例外があります。

* `FirstOrDefaultAsync` は、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) に置換されています。 関連データを含める必要がない場合は、`FindAsync` の方が効率的です。
* `OnPostAsync` には `id` パラメーターがあります。
* 現在の学生は、空の学生を作成するのではなく、データベースからフェッチされます。

アプリを実行し、学生を作成して編集することでアプリをテストします。

## <a name="entity-states"></a>エンティティの状態

データベース コンテキストは、メモリ内のエンティティが、データベース内の対応する行と同期状態にあるかどうかを追跡します。 この追跡情報により、[SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに何が起こっているかを特定できます。 たとえば、新しいエンティティが [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) メソッドに渡されたとき、そのエンティティの状態は [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) に設定されます。 `SaveChangesAsync` が呼び出されると、データベース コンテキストによって SQL の `INSERT` コマンドが発行されます。

エンティティは、[次のいずれかの状態](/dotnet/api/microsoft.entityframeworkcore.entitystate)になる可能性があります。

* `Added`:エンティティはデータベースにまだ存在しません。 `SaveChanges` メソッドでは、`INSERT` ステートメントが発行されます。

* `Unchanged`:このエンティティでは変更を保存する必要がありません。 エンティティがこの状態になるのは、エンティティがデータベースから読み取られた場合です。

* `Modified`:エンティティのプロパティ値の一部またはすべてが変更されています。 `SaveChanges` メソッドでは、`UPDATE` ステートメントが発行されます。

* `Deleted`:エンティティには削除のマークが付けられています。 `SaveChanges` メソッドでは、`DELETE` ステートメントが発行されます。

* `Detached`:エンティティはデータベース コンテキストによって追跡されていません。

デスクトップ アプリにおいて、通常、状態の変更は自動的に設定されます。 エンティティが読み取られ、変更が加えられると、エンティティの状態は自動的に `Modified` に変更されます。 `SaveChanges` を呼び出すと、変更されたプロパティのみを更新する SQL `UPDATE` ステートメントが生成されます。

Web アプリにおいて、エンティティを読み取り、データを表示する `DbContext` は、ページが表示された後で破棄されます。 ページの `OnPostAsync` メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが使用されます。 その新しいコンテキスト内のエンティティの再読み取りを行うと、デスクトップの処理がシミュレートされます。

## <a name="update-the-delete-page"></a>[削除] ページを更新する

このセクションでは、`SaveChanges` の呼び出しが失敗すると、カスタム エラー メッセージが実装されます。

*Pages/Students/Delete.cshtml.cs* のコードを次のコードに置き換えます。 変更が強調表示されています。

[!code-csharp[Main](intro/samples/cu50/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=12-14,22,30-33,45-99)]

上記のコードでは、省略可能なパラメーター `saveChangesError` を `OnGetAsync` メソッド シグネチャに追加します。 `saveChangesError` は、受講者オブジェクトの削除に失敗した後で、メソッドが呼び出されたかどうかを示します。 一時的なネットワークの問題により、削除操作が失敗する可能性があります。 データベースがクラウド内にある場合は、一時的なネットワーク エラーが発生する可能性が高くなります。 Delete ページの `OnGetAsync` が UI から呼び出された場合、`saveChangesError` パラメーターは `false` です。 削除操作が失敗したために、`OnPostAsync` によって `OnGetAsync` が呼び出された場合、`saveChangesError` パラメーターは `true` です。

`OnPostAsync` メソッドは、選択されたエンティティを取得し、[Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) メソッドを呼び出して、エンティティの状態を `Deleted` に設定します。 `SaveChanges` が呼び出された場合、SQL の `DELETE` コマンドが生成されます。 `Remove` が失敗した場合:

* データベース例外がキャッチされます。
* [削除] ページの `OnGetAsync` メソッドが、`saveChangesError=true` を指定して呼び出されます。

Delete Razor ページ ( *Pages/Students/Delete.cshtml* ) にエラー メッセージを追加します。

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

アプリを実行し、学生を削除して、Delete ページをテストします。

## <a name="next-steps"></a>次の手順

> [!div class="step-by-step"]
> [前のチュートリアル](xref:data/ef-rp/intro)
> [次のチュートリアル](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

このチュートリアルでは、スキャフォールディング CRUD (作成、読み取り、更新、削除) コードのレビューとカスタマイズを行います。

## <a name="no-repository"></a>リポジトリがない

一部の開発者は、サービス レイヤーまたはリポジトリ パターンを使用して、UI (Razor Pages) とデータ アクセス層との間に抽象化レイヤーを作成しています。 このチュートリアルでは、これは行いません。 複雑さを最小限に抑え、チュートリアルの焦点を EF Core に置くために、EF Core コードがページ モデル クラスに直接追加されます。 

## <a name="update-the-details-page"></a>Details ページを更新する

Students ページのスキャフォールディング コードには、登録データが含まれていません。 このセクションでは、Details ページに登録が追加されます。

### <a name="read-enrollments"></a>登録の読み取り

このページに学生の登録データを表示するには、その登録データの読み取りが行われる必要があります。 *Pages/Students/Details.cshtml.cs* のスキャフォールディング コードでは、Enrollment データを含まない Student データのみが読み取られます。

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

`OnGetAsync` メソッドを次のコードに置き換えて、選択した学生の登録データを読み取ります。 変更が強調表示されます。

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) メソッドと [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。 これらのメソッドは、[関連データの読み込み](xref:data/ef-rp/read-related-data)のチュートリアルで詳しく検討します。

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) メソッドは、返されたエンティティが現在のコンテキストで更新されないシナリオでパフォーマンスを改善します。 `AsNoTracking` は、このチュートリアルで後述します。

### <a name="display-enrollments"></a>登録の表示

*Pages/Students/Details.cshtml* 内のコードを次のコードに置き換えて、登録のリストを表示します。 変更が強調表示されます。

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

上記のコードは、`Enrollments` ナビゲーション プロパティ内でエンティティをループ処理します。 登録ごとに、コースのタイトルとグレードが表示されます。 コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。

アプリを実行し、 **[Students]** タブを選択し、学生用の **[詳細]** リンクをクリックします。 選択した受講者のコースとグレードの一覧が表示されます。

### <a name="ways-to-read-one-entity"></a>1 つのエンティティを読み取る方法

生成されたコードでは、[FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) を使用して 1 つのエンティティを読み取ります。 このメソッドでは、何も見つからない場合は null が返されます。それ以外の場合は、クエリのフィルター条件を満たす最初の行が返されます。 `FirstOrDefaultAsync` は、通常、次の代替手段よりも適しています。

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - クエリ フィルターを満たす複数のエンティティがある場合に、例外をスローします。 クエリによって複数の行が返される可能性があるかどうかを判断するため、`SingleOrDefaultAsync` は複数の行をフェッチしようとします。 一意のキーを検索する場合と同様に、クエリが 1 つのエンティティだけを返すことができる場合は、この追加作業は不要です。
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 主キー (PK) を持つエンティティを検索します。 PK を持つエンティティがコンテキストによって追跡されている場合、データベースに対する要求がなくても該当するエンティティが返されます。 このメソッドは、単一のエンティティを検索するように最適化されていますが、`FindAsync` を使用して `Include` を呼び出すことはできません。  したがって、関連データが必要な場合は、`FirstOrDefaultAsync` を選択することをお勧めします。

### <a name="route-data-vs-query-string"></a>ルート データとクエリ文字列

Details ページの URL は `https://localhost:<port>/Students/Details?id=1` です。 エンティティの主キー値がクエリ文字列に含まれています。 ルート データのキー値を渡すことを好む開発者もいます。`https://localhost:<port>/Students/Details/1` 詳細については、「[生成されたコードの更新](xref:tutorials/razor-pages/da1#update-the-generated-code)」を参照してください。

## <a name="update-the-create-page"></a>Create ページを更新する

Create ページのスキャフォールディングされた `OnPostAsync` コードは、[過剰ポスティング](#overposting)に対して脆弱です。 *Pages/Students/Create.cshtml.cs* の `OnPostAsync` メソッドを次のコードに置き換えます。

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

上記のコードでは、Student オブジェクトを作成し、ポストされたフォーム フィールドを使用して Student オブジェクトのプロパティを更新します。 [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) メソッド:

* [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) の [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) プロパティからポストされたフォーム値を使用します。
* リストされたプロパティのみを更新します (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。
* "student" のプレフィックスを持つフォーム フィールドを検索します。 たとえば、`Student.FirstMidName` のようにします。 大文字と小文字の区別はありません。
* [モデル バインド](xref:mvc/models/model-binding) システムを使用して、文字列からフォーム値を `Student` モデル内の型に変換します。 たとえば、`EnrollmentDate` は DateTime に変換する必要があります。

アプリを実行し、Create ページをテストする student エンティティを作成します。

## <a name="overposting"></a>過剰ポスティング

ポストされた値を持つフィールドを更新するために `TryUpdateModel` を使用することは、過剰ポスティングの防止につながり、セキュリティ上のベスト プラクティスとなります。 たとえば、Student エンティティには、この Web ページで更新または追加できない `Secret` プロパティが含まれています。

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

アプリの作成または更新の Razor ページに `Secret` フィールドが含まれていない場合でも、ハッカーは過剰ポスティングによって `Secret` 値を設定することが可能です。 ハッカーは、Fiddler などのツールを使用するか、または何らかの JavaScript を作成して、`Secret` フォーム値をポストすることが可能です。 元のコードでは、Student インスタンスの作成時にモデル バインダーによって使用されるフィールドを制限していません。

`Secret` フォーム フィールドに対してハッカーが指定した値はいずれも、データベース内で更新されます。 次の図では、Fiddler ツールを使用して、ポストされたフォームの値に `Secret` フィールド (値 "OverPost" を含む) が追加されています。

![Fiddler による Secret フィールドの追加](../ef-mvc/crud/_static/fiddler.png)

挿入された行の `Secret` プロパティに値 "OverPost" が正常に追加されています。 これは、アプリ デザイナーで、Create ページで `Secret` プロパティが設定されることを想定していない場合でも発生します。

### <a name="view-model"></a>ビュー モデル

ビュー モデルは、過剰ポスティングを防ぐもう 1 つの方法を提供します。

アプリケーション モデルは、しばしばドメイン モデルと呼ばれます。 ドメイン モデルには、通常、データベース内の対応するエンティティによって必要とされるすべてのプロパティが含まれています。 ビュー モデルには、使用する UI に必要なプロパティのみが含まれています (たとえば、Create ページ)。

一部のアプリでは、ビュー モデルに加えて、Razor Pages のページ モデル クラスとブラウザーとの間でデータを渡すためにバインド モデルまたは入力モデルも使用します。 

次の `Student` ビュー モデルを考えてみます。

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

次のコードでは、`StudentVM` ビュー モデルを使用して新しい受講生を作成します。

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) メソッドでは、このオブジェクトの値を設定するために、別の [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) オブジェクトから値を読み取ります。 `SetValues` では一致するプロパティ名が使用されます。 ビュー モデルの型はモデルの型に関連している必要はなく、プロパティが一致している必要があるだけです。

`StudentVM` を使用するには、`Student` ではなく `StudentVM` を使用するように [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) を更新する必要があります。

## <a name="update-the-edit-page"></a>[編集] ページを更新する

*Pages/Students/Edit.cshtml.cs* で、`OnGetAsync` メソッドと `OnPostAsync` メソッドを次のコードに置き換えます。

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

コードの変更は [作成] ページに似ています。ただし、次のようないくつかの例外があります。

* `FirstOrDefaultAsync` は、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) に置換されています。 関連データを含める必要がない場合は、`FindAsync` の方が効率的です。
* `OnPostAsync` には `id` パラメーターがあります。
* 現在の学生は、空の学生を作成するのではなく、データベースからフェッチされます。

アプリを実行し、学生を作成して編集することでアプリをテストします。

## <a name="entity-states"></a>エンティティの状態

データベース コンテキストは、メモリ内のエンティティが、データベース内の対応する行と同期状態にあるかどうかを追跡します。 この追跡情報により、[SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに何が起こっているかを特定できます。 たとえば、新しいエンティティが [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) メソッドに渡されたとき、そのエンティティの状態は [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) に設定されます。 `SaveChangesAsync` が呼び出されると、データベース コンテキストは SQL の INSERT コマンドを発行します。

エンティティは、[次のいずれかの状態](/dotnet/api/microsoft.entityframeworkcore.entitystate)になる可能性があります。

* `Added`:エンティティはデータベースにまだ存在しません。 `SaveChanges` メソッドは INSERT ステートメントを発行します。

* `Unchanged`:このエンティティでは変更を保存する必要がありません。 エンティティがこの状態になるのは、エンティティがデータベースから読み取られた場合です。

* `Modified`:エンティティのプロパティ値の一部またはすべてが変更されています。 `SaveChanges` メソッドは UPDATE ステートメントを発行します。

* `Deleted`:エンティティには削除のマークが付けられています。 `SaveChanges` メソッドは DELETE ステートメントを発行します。

* `Detached`:エンティティはデータベース コンテキストによって追跡されていません。

デスクトップ アプリにおいて、通常、状態の変更は自動的に設定されます。 エンティティが読み取られ、変更が加えられると、エンティティの状態は自動的に `Modified` に変更されます。 `SaveChanges` を呼び出すと、変更されたプロパティのみを更新する SQL UPDATE ステートメントが生成されます。

Web アプリにおいて、エンティティを読み取り、データを表示する `DbContext` は、ページが表示された後で破棄されます。 ページの `OnPostAsync` メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが使用されます。 その新しいコンテキスト内のエンティティの再読み取りを行うと、デスクトップの処理がシミュレートされます。

## <a name="update-the-delete-page"></a>[削除] ページを更新する

このセクションでは、`SaveChanges` の呼び出しが失敗したときにカスタム エラー メッセージを実装します。

*Pages/Students/Delete.cshtml.cs* のコードを次のコードに置き換えます。 変更が強調表示されます (`using` ステートメントのクリーンアップ以外)。

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

上記のコードでは、省略可能なパラメーター `saveChangesError` を `OnGetAsync` メソッド シグネチャに追加します。 `saveChangesError` は、受講者オブジェクトの削除に失敗した後で、メソッドが呼び出されたかどうかを示します。 一時的なネットワークの問題により、削除操作が失敗する可能性があります。 データベースがクラウド内にある場合は、一時的なネットワーク エラーが発生する可能性が高くなります。 Delete ページの `OnGetAsync` が UI から呼び出された場合、`saveChangesError` パラメーターは false です。 `OnPostAsync` によって `OnGetAsync` が呼び出された場合 (削除操作が失敗したため)、`saveChangesError` パラメーターは true です。

`OnPostAsync` メソッドは、選択されたエンティティを取得し、[Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) メソッドを呼び出して、エンティティの状態を `Deleted` に設定します。 `SaveChanges` が呼び出されると、SQL DELETE コマンドが生成されます。 `Remove` が失敗した場合:

* データベース例外がキャッチされます。
* [削除] ページの `OnGetAsync` メソッドが、`saveChangesError=true` を指定して呼び出されます。

[削除] Razor ページ ( *Pages/Students/Delete.cshtml* ) にエラー メッセージを追加します。

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

アプリを実行し、学生を削除して、Delete ページをテストします。

## <a name="next-steps"></a>次の手順

> [!div class="step-by-step"]
> [前のチュートリアル](xref:data/ef-rp/intro)
> [次のチュートリアル](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

このチュートリアルでは、スキャフォールディング CRUD (作成、読み取り、更新、削除) コードのレビューとカスタマイズを行います。

複雑さを最小限に抑え、これらのチュートリアルを通して主眼を EF Core に置くために、EF Core コードはページ モデルで使用されています。 一部の開発者は、サービス レイヤーまたはリポジトリ パターンを使用して、UI (Razor Pages) とデータ アクセス層との間に抽象化レイヤーを作成しています。

このチュートリアルでは、 *Students* フォルダー内の [作成]、[編集]、[削除]、[詳細] の各 Razor Pages を確認します。

スキャフォールディング コードでは、[作成]、[編集]、[削除] ページに対して次のパターンを使用します。

* 要求されたデータを HTTP GET メソッド `OnGetAsync` を使用して取得および表示します。
* データに加えた変更を HTTP POST メソッド `OnPostAsync` を使用して保存します。

[インデックス] および [詳細] ページでは、要求されたデータを HTTP GET メソッド `OnGetAsync` を使用して取得および表示します。

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync と FirstOrDefaultAsync の比較

生成されたコードでは [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) を使用しています。これは一般に [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) よりも好まれています。

 `FirstOrDefaultAsync` は、1 つのエンティティをフェッチする際に `SingleOrDefaultAsync` よりも効率的です。

* クエリから返されるエンティティが 1 つ以下であることを、コードで検証する必要がない。
* `SingleOrDefaultAsync` がより多くのデータをフェッチし、不要な作業を行う。
* フィルター部に適合するエンティティが複数存在する場合に `SingleOrDefaultAsync` によって例外がスローされる。
* フィルター部に適合するエンティティが複数存在する場合に `FirstOrDefaultAsync` によって例外がスローされない。

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

スキャフォールディング コードの多くでは、`FirstOrDefaultAsync` ではなく、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) を使用することができます。

`FindAsync`:

* 主キー (PK) を持つエンティティを検索します。 PK を持つエンティティがコンテキストによって追跡されている場合、DB に対する要求がなくても該当するエンティティが返されます。
* 単純で簡潔です。
* 単一のエンティティを検索するように最適化されます。
* 状況によってはパフォーマンス上の利点がありますが、通常の Web アプリではほとんど利点がありません。
* [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) ではなく、[FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) を暗黙的に使用します。

ただし、`Include` で他のエンティティを含める場合、`FindAsync` は適切ではなくなります。 つまり、アプリの進行状況に応じて、`FindAsync` を止めてクエリに移行することが必要な場合があります。

## <a name="customize-the-details-page"></a>Details ページをカスタマイズする

`Pages/Students` ページを参照します。 **[編集]** 、 **[詳細]** 、 **[削除]** の各リンクは、 *Pages/Students/Index.cshtml* ファイルで [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

アプリを実行し、 **[詳細]** リンクを選択します。 URL の形式は、 `http://localhost:5000/Students/Details?id=2` です。 クエリ文字列 (`?id=2`) によって受講者 ID が渡されます。

`"{id:int}"` ルート テンプレートを使用するには、[編集]、[詳細]、[削除] の Razor ページを更新します。 これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。

整数ルート値を **含まない** 、"{id:int}" ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。 たとえば、 `http://localhost:5000/Students/Details` は 404 エラーを返します。 ID を省略するには、次のように `?` をルート制約に追加します。

 ```cshtml
@page "{id:int?}"
```

アプリを実行し、[詳細] リンクをクリックし、URL がルート データとして ID を渡していることを確認します ( `http://localhost:5000/Students/Details/2` )。

`@page` から `@page "{id:int}"` への変更をグローバルに行わないでください。これを行うと、[ホーム] ページと [作成] ページへのリンクが解除されます。

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>関連データを追加する

Students インデックス ページのスキャフォールディング コードには、`Enrollments` プロパティが含まれていません。 このセクションでは、`Enrollments` コレクションのコンテンツが [詳細] ページに表示されます。

*Pages/Students/Details.cshtml.cs* の `OnGetAsync` メソッドでは、`FirstOrDefaultAsync` メソッドを使用して単一の `Student` エンティティを取得します。 次の強調表示されたコードを追加します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) メソッドと [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。 これらのメソッドは、データの読み取りに関連するチュートリアルで詳しく検討します。

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) メソッドは、返されたエンティティが現在のコンテキストで更新されない場合のシナリオでパフォーマンスを改善します。 `AsNoTracking` は、このチュートリアルで後述します。

### <a name="display-related-enrollments-on-the-details-page"></a>[詳細] ページに関連する登録を表示する

*Pages/Students/Details.cshtml* を開きます。 登録の一覧を表示するために、次の強調表示されたコードを追加します。

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

コードを貼り付けた後でコードのインデントが乱れた場合は、CTRL + D + K キーを押してそれを修正します。

上記のコードは、`Enrollments` ナビゲーション プロパティ内でエンティティをループ処理します。 登録ごとに、コースのタイトルとグレードが表示されます。 コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。

アプリを実行し、 **[Students]** タブを選択し、学生用の **[詳細]** リンクをクリックします。 選択した受講者のコースとグレードの一覧が表示されます。

## <a name="update-the-create-page"></a>[作成] ページを更新する

次のコードを使用して、 *Pages/Students/Create.cshtml.cs* 内の `OnPostAsync` メソッドを更新します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

次の [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) コードを検討します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

上記のコードで、`TryUpdateModelAsync<Student>` は、[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) の [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) プロパティからのポストされたフォームの値を使用して `emptyStudent` オブジェクトの更新を試みます。 `TryUpdateModelAsync` は、リストされたプロパティのみを更新します (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。

上記のサンプルについて:

* 2 番目の引数 (`"student", // Prefix`) には、値を検索するためのプレフィックスが使用されています。 大文字と小文字の区別はありません。
* ポストされたフォームの値は、[モデル バインディング](xref:mvc/models/model-binding) を使用して `Student` モデル内の型に変換されます。

<a id="overpost"></a>

### <a name="overposting"></a>過剰ポスティング

ポストされた値を持つフィールドを更新するために `TryUpdateModel` を使用することは、過剰ポスティングの防止につながり、セキュリティ上のベスト プラクティスとなります。 たとえば、Student エンティティには、この Web ページで更新または追加できない `Secret` プロパティが含まれています。

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

アプリの作成または更新の Razor ページに `Secret` フィールドが含まれていない場合でも、ハッカーは過剰ポスティングによって `Secret` 値を設定することが可能です。 ハッカーは、Fiddler などのツールを使用するか、または何らかの JavaScript を作成して、`Secret` フォーム値をポストすることが可能です。 元のコードでは、Student インスタンスの作成時にモデル バインダーによって使用されるフィールドを制限していません。

`Secret` フォーム フィールドに対してハッカーが指定した値はいずれも、DB 内で更新されます。 次の図では、Fiddler ツールを使用して、ポストされたフォームの値に `Secret` フィールド (値 "OverPost" を含む) が追加されています。

![Fiddler による Secret フィールドの追加](../ef-mvc/crud/_static/fiddler.png)

挿入された行の `Secret` プロパティに値 "OverPost" が正常に追加されています。 アプリ デザイナーは、[作成] ページで `Secret` プロパティが設定されることを想定していませんでした。

<a name="vm"></a>

### <a name="view-model"></a>ビュー モデル

ビュー モデルには、通常、アプリケーションで使用されるモデルに含まれるプロパティのサブセットが含まれています。 アプリケーション モデルは、しばしばドメイン モデルと呼ばれます。 ドメイン モデルには、通常、データベース内の対応するエンティティによって必要とされるすべてのプロパティが含まれています。 ビュー モデルには、UI レイヤーで必要なプロパティのみが含まれています (たとえば、[作成] ページ)。 一部のアプリでは、ビュー モデルに加えて、Razor Pages のページ モデル クラスとブラウザーとの間でデータを渡すためにバインド モデルまたは入力モデルも使用します。 次の `Student` ビュー モデルを考えてみます。

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

ビュー モデルは、過剰ポスティングを防ぐもう 1 つの方法を提供します。 ビュー モデルには、表示または更新されるプロパティのみが含まれています。

次のコードでは、`StudentVM` ビュー モデルを使用して新しい受講生を作成します。

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) メソッドでは、このオブジェクトの値を設定するために、別の [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) オブジェクトから値を読み取ります。 `SetValues` では一致するプロパティ名が使用されます。 ビュー モデルの型はモデルの型に関連している必要はなく、プロパティが一致している必要があるだけです。

`StudentVM` を使用するには、`Student` ではなく `StudentVM` を使用するように [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) を更新する必要があります。

Razor Pages で、`PageModel` 派生クラスはビュー モデルです。

## <a name="update-the-edit-page"></a>[編集] ページを更新する

[編集] ページのページ モデルを更新します。 大きな変更点が強調表示されています。

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

コードの変更は [作成] ページに似ています。ただし、次のようないくつかの例外があります。

* `OnPostAsync` には省略可能な `id` パラメーターがあります。
* 現在の受講者は、空の受講者を作成するのではなく、DB からフェッチされます。
* `FirstOrDefaultAsync` は、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) に置換されています。 主キーからエンティティを選択する場合は、`FindAsync` をお勧めします。 詳細については、「[FindAsync](#FindAsync)」を参照してください。

### <a name="test-the-edit-and-create-pages"></a>[編集] ページと [作成] ページをテストする

いくつかの受講者エンティティを作成して編集します。

## <a name="entity-states"></a>エンティティの状態

DB コンテキストは、メモリ内のエンティティが、DB 内の対応する行と同期状態にあるかどうかを追跡します。 DB コンテキストの同期情報から、[SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに何が起こっているかを特定できます。 たとえば、新しいエンティティが [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) メソッドに渡されたとき、そのエンティティの状態は [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) に設定されます。 `SaveChangesAsync` が呼び出されると、DB コンテキストは SQL の INSERT コマンドを発行します。

エンティティは、[次のいずれかの状態](/dotnet/api/microsoft.entityframeworkcore.entitystate)になる可能性があります。

* `Added`:エンティティは DB にまだ存在しません。 `SaveChanges` メソッドは INSERT ステートメントを発行します。

* `Unchanged`:このエンティティでは変更を保存する必要がありません。 エンティティがこの状態になるのは、エンティティが DB から読み取られた場合です。

* `Modified`:エンティティのプロパティ値の一部またはすべてが変更されています。 `SaveChanges` メソッドは UPDATE ステートメントを発行します。

* `Deleted`:エンティティには削除のマークが付けられています。 `SaveChanges` メソッドは DELETE ステートメントを発行します。

* `Detached`:エンティティは DB コンテキストによって追跡されていません。

デスクトップ アプリにおいて、通常、状態の変更は自動的に設定されます。 エンティティが読み取られ、変更が加えられると、エンティティの状態は自動的に `Modified` に変更されます。 `SaveChanges` を呼び出すと、変更されたプロパティのみを更新する SQL UPDATE ステートメントが生成されます。

Web アプリにおいて、エンティティを読み取り、データを表示する `DbContext` は、ページが表示された後で破棄されます。 ページの `OnPostAsync` メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが使用されます。 その新しいコンテキスト内のエンティティの再読み取りを行うと、デスクトップの処理がシミュレートされます。

## <a name="update-the-delete-page"></a>[削除] ページを更新する

このセクションでは、`SaveChanges` の呼び出しが失敗したときにカスタム エラー メッセージを実装するためのコードが追加されます。 考えられるエラー メッセージを含められるように文字列を追加します。

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

`OnGetAsync` メソッドを次のコードで置き換えます。

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

上記のコードには、省略可能なパラメーター `saveChangesError` が含まれています。 `saveChangesError` は、受講者オブジェクトの削除に失敗した後で、メソッドが呼び出されたかどうかを示します。 一時的なネットワークの問題により、削除操作が失敗する可能性があります。 一時的なネットワーク エラーが高い可能性でクラウド内に発生しています。 [削除] ページの `OnGetAsync` が UI から呼び出された場合、`saveChangesError` は false です。 `OnPostAsync` によって `OnGetAsync` が呼び出された場合 (削除操作が失敗したため)、`saveChangesError` パラメーターは true です。

### <a name="the-delete-pages-onpostasync-method"></a>[削除] ページの OnPostAsync メソッド

`OnPostAsync` を次のコードに置き換えます。

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

上記のコードでは、選択されたエンティティを取得してから、[Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) メソッドを呼び出してエンティティの状態を `Deleted` に設定しています。 `SaveChanges` が呼び出されると、SQL DELETE コマンドが生成されます。 `Remove` が失敗した場合:

* DB 例外がキャッチされます。
* [削除] ページの `OnGetAsync` メソッドが、`saveChangesError=true` を指定して呼び出されます。

### <a name="update-the-delete-no-locrazor-page"></a>[削除] Razor ページを更新する

次の強調表示されたエラー メッセージを [削除] Razor ページに追加します。
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

[削除] をテストします。

## <a name="common-errors"></a>一般的なエラー

[受講者]/[インデックス] またはその他のリンクが機能しません。

Razor ページに正しい `@page` ディレクティブが含まれていることを確認します。 たとえば、Students/Index Razor ページにルート テンプレートを含めることは **できません** 。

```cshtml
@page "{id:int}"
```

各 Razor ページには、`@page` ディレクティブを含める必要があります。



## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [前へ](xref:data/ef-rp/intro)
> [次へ](xref:data/ef-rp/sort-filter-page)

::: moniker-end

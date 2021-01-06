---
title: パート 8、ASP.NET Core の Razor ページと EF Core - コンカレンシー
author: rick-anderson
description: Razor Pages と Entity Framework チュートリアル シリーズのパート 8。
ms.author: riande
ms.custom: mvc
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053996"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="c78a3-103">パート 8、ASP.NET Core の Razor ページと EF Core - コンカレンシー</span><span class="sxs-lookup"><span data-stu-id="c78a3-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="c78a3-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="c78a3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c78a3-105">このチュートリアルでは、複数のユーザーがエンティティをコンカレントに更新するときの競合の処理方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="c78a3-106">コンカレンシーの競合</span><span class="sxs-lookup"><span data-stu-id="c78a3-106">Concurrency conflicts</span></span>

<span data-ttu-id="c78a3-107">コンカレンシーの競合は、次のような場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="c78a3-108">ユーザーがエンティティの Edit ページに移動した場合。</span><span class="sxs-lookup"><span data-stu-id="c78a3-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="c78a3-109">最初のユーザーの変更がデータベースに書き込まれる前に、別のユーザーが同じエンティティを更新した場合。</span><span class="sxs-lookup"><span data-stu-id="c78a3-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="c78a3-110">コンカレンシーの検出が有効になっていない場合は、最後に行われたデータベースの更新により、他のユーザーの変更が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="c78a3-111">このリスクが許容可能な場合は、コンカレンシーのプログラミングのコストが利点を上回る可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="c78a3-112">ペシミスティック コンカレンシー (ロック)</span><span class="sxs-lookup"><span data-stu-id="c78a3-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="c78a3-113">コンカレンシーの競合を防ぐ方法の 1 つは、データベースのロックを使用することです。</span><span class="sxs-lookup"><span data-stu-id="c78a3-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="c78a3-114">これはペシミスティック コンカレンシーと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="c78a3-115">アプリでは、更新予定のデータベース行を読み取る前に、ロックを要求します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="c78a3-116">行が更新アクセス用にロックされた後は、最初のロックが解除されるまで、他のユーザーはその行をロックできなくなります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="c78a3-117">ロックの利用には短所があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="c78a3-118">プログラムが複雑になり、ユーザー数が増えるにつれてパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="c78a3-119">Entity Framework Core にはそのサポートは組み込まれておらず、このチュートリアルでは実装方法を説明しません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="c78a3-120">オプティミスティック コンカレンシー</span><span class="sxs-lookup"><span data-stu-id="c78a3-120">Optimistic concurrency</span></span>

<span data-ttu-id="c78a3-121">オプティミスティック コンカレンシーでは、コンカレンシーの競合の発生が許可され、それが発生した場合に適切に対処されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="c78a3-122">たとえば、Jane が Department Edit ページにアクセスし、English 部署の予算を $350,000.00 から $0.00 に変更するとします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![予算を 0 に変更する](concurrency/_static/change-budget30.png)

<span data-ttu-id="c78a3-124">Jane が **[保存]** をクリックする前に John が同じページにアクセスし、[開始日] フィールドを 9/1/2007 から 9/1/2013 に変更します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![開始日を 2013 に変更する](concurrency/_static/change-date30.png)

<span data-ttu-id="c78a3-126">Jane が最初に **[保存]** をクリックすると変更が反映されます。これは、ブラウザーの Index ページに予算として 0 が表示されるためです。</span><span class="sxs-lookup"><span data-stu-id="c78a3-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="c78a3-127">John が Edit ページの **[保存]** をクリックします。このとき、予算は $350,000.00 と表示されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="c78a3-128">この後の動作は、コンカレンシーの競合の処理方法によって決定されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="c78a3-129">ユーザーが変更したプロパティを追跡記録し、それに該当する列だけをデータベースで更新できます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="c78a3-130">このシナリオでは、データは失われません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="c78a3-131">2 人のユーザーが別のプロパティを更新しました。</span><span class="sxs-lookup"><span data-stu-id="c78a3-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="c78a3-132">今度誰かが English 部署を閲覧すると、Jane の変更と John の変更が両方とも表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="c78a3-133">この更新方法では、データが失われる原因となる競合の数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="c78a3-134">このアプローチにはいくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="c78a3-135">競合する変更が同じプロパティに加えられた場合、データの損失を回避することはできません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="c78a3-136">Web アプリでは、一般的に実用的ではありません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="c78a3-137">フェッチされたすべての値と新しい値を追跡するために、かなりのステータスを維持することが必要になります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="c78a3-138">大量のステータスを保守管理することになると、アプリケーションのパフォーマンスに影響が出ます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="c78a3-139">エンティティでのコンカレンシーの検出と比較して、アプリは複雑になります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="c78a3-140">John の変更で Jane の変更を上書きするように指定できます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="c78a3-141">今度誰かが English 部署を閲覧すると、9/1/2013 の日付と、フェッチされた $350,000.00 の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="c78a3-142">このアプローチは *Client Wins* (クライアント側に合わせる) シナリオまたは *Last in Wins* (最終書き込み者優先) シナリオと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="c78a3-143">(クライアントからの値がすべて、データ ストアの値より優先されます。)コンカレンシー処理に対してコーディングを行わない場合、自動的にクライアント側に合わせられます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="c78a3-144">データベースで John の変更が更新されないようにできます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="c78a3-145">通常、アプリの動作は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-145">Typically, the app would:</span></span>

  * <span data-ttu-id="c78a3-146">エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-146">Display an error message.</span></span>
  * <span data-ttu-id="c78a3-147">データの現在のステータスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="c78a3-148">ユーザーが変更を再適用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="c78a3-149">これは *Store Wins* (ストア側に合わせる) シナリオと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="c78a3-150">(クライアントが送信した値よりデータストアの値が優先されます。)このチュートリアルでは、Store Wins シナリオを実装します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="c78a3-151">この手法では、変更が上書きされるとき、それが必ずユーザーに警告されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="c78a3-152">EF Core での競合の検出</span><span class="sxs-lookup"><span data-stu-id="c78a3-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="c78a3-153">EF Core では、競合が検出されると `DbConcurrencyException` 例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="c78a3-154">競合検出が有効になるように、データ モデルを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="c78a3-155">競合検出を有効にするためのオプションには次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="c78a3-156">Update コマンドと Delete コマンドの Where 句で[コンカレンシー トークン](/ef/core/modeling/concurrency)として構成されている列の元の値を含むように、EF Core を構成します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="c78a3-157">`SaveChanges` が呼び出されると、<xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> 属性で注釈付けされたプロパティの元の値が Where 句によって検索されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="c78a3-158">行が最初に読み取られてからいずれかのコンカレンシー トークン プロパティが変更されている場合、Update ステートメントでは更新する行が検索されません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="c78a3-159">EF Core では、それがコンカレンシーの競合として解釈されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="c78a3-160">データベース テーブルに列がたくさんある場合、この手法では結果的に大量の Where 句が出現し、大量の状態が必要になります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="c78a3-161">そのため、この手法は一般的には推奨されません。このチュートリアルでも利用しません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="c78a3-162">行が変更されたタイミングを判断するトラッキング列をデータベース テーブルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="c78a3-163">SQL Server データベースでは、トラッキング列のデータ型は `rowversion` です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="c78a3-164">`rowversion` 値は連続番号であり、行が更新されるたびに増えます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="c78a3-165">Update または Delete コマンドでは、Where 句にトラッキング列の元の値が含まれます (元の行バージョン番号)。</span><span class="sxs-lookup"><span data-stu-id="c78a3-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="c78a3-166">更新対象の行が別のユーザーによって変更されている場合、`rowversion` 列の値は元の値とは異なります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="c78a3-167">その場合、Update ステートメントまたは Delete ステートメントでは、Where 句のために更新する行を見つけることができません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="c78a3-168">Update または Delete コマンドによって影響を受ける行がない場合、EF Core ではコンカレンシー例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="c78a3-169">トラッキング プロパティを追加する</span><span class="sxs-lookup"><span data-stu-id="c78a3-169">Add a tracking property</span></span>

<span data-ttu-id="c78a3-170">*Models/Department.cs* で、RowVersion という名前のトラッキング プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="c78a3-171"><xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 属性により、列がコンカレンシー トラッキング列として識別されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="c78a3-172">fluent API は、トラッキング プロパティを指定する別の方法です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="c78a3-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c78a3-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c78a3-174">SQL Server データベースでは、エンティティ プロパティの `[Timestamp]` 属性はバイト配列として定義されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="c78a3-175">結果として、列は DELETE および UPDATE の WHERE 句に含まれるようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="c78a3-176">データベースの列の型を、[rowversion](/sql/t-sql/data-types/rowversion-transact-sql) に設定します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="c78a3-177">データベースによって、行が更新されるたびに増える、連続する行バージョン番号が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="c78a3-178">`Update` または `Delete` コマンドの `Where` 句には、フェッチされた行バージョンの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="c78a3-179">フェッチされた後で更新された行が変更された場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="c78a3-180">現在の行バージョンの値は、フェッチされた値と一致しません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="c78a3-181">`Where` 句ではフェッチされた行バージョンの値が検索されるので、`Update` または `Delete` コマンドでは行は見つかりません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="c78a3-182">`DbUpdateConcurrencyException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="c78a3-183">次のコードは、Department 名が更新されたときに、EF Core によって生成された T-SQL ステートメントの一部です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="c78a3-184">上の強調表示されたコードには、`RowVersion` を含む `WHERE` 句があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="c78a3-185">データベースの `RowVersion` が `RowVersion` パラメーター (`@p2`) と一致しない場合、行は更新されません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="c78a3-186">次の強調表示されたコードは、1 つの行のみが更新されたことを検証する T-SQL です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="c78a3-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) では、最後のステートメントの影響を受けた行数を返します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="c78a3-188">更新された行がない場合、EF Core では `DbUpdateConcurrencyException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c78a3-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c78a3-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c78a3-190">SQLite データベースでは、エンティティ プロパティの `[Timestamp]` 属性はバイト配列として定義されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="c78a3-191">結果として、列は DELETE および UPDATE の WHERE 句に含まれるようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="c78a3-192">BLOB 列の型にマップされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="c78a3-193">行が更新されるたびに、データ ベーストリガーでは、新しいランダムなバイト配列で RowVersion 列が更新されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="c78a3-194">`Update` または `Delete` コマンドの `Where` 句には、RowVersion 列のフェッチされた値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="c78a3-195">フェッチされた後で更新された行が変更された場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="c78a3-196">現在の行バージョンの値は、フェッチされた値と一致しません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="c78a3-197">`Where` 句では元の行バージョンの値が検索されるので、`Update` または `Delete` コマンドでは行は見つかりません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="c78a3-198">`DbUpdateConcurrencyException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="c78a3-199">データベースを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-199">Update the database</span></span>

<span data-ttu-id="c78a3-200">`RowVersion` プロパティを追加すると、移行を必要とするデータ モデルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="c78a3-201">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="c78a3-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c78a3-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c78a3-203">PMC で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c78a3-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c78a3-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c78a3-205">端末で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="c78a3-206">このコマンドは、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="c78a3-206">This command:</span></span>

* <span data-ttu-id="c78a3-207">*Migrations/{time stamp}_RowVersion.cs* 移行ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="c78a3-208">*Migrations/SchoolContextModelSnapshot.cs* ファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="c78a3-209">更新により、次の強調表示されたコードが `BuildModel` メソッドに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="c78a3-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c78a3-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c78a3-211">PMC で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c78a3-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c78a3-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c78a3-213">`Migrations/<timestamp>_RowVersion.cs` ファイルを開き、強調表示されているコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="c78a3-214">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-214">The preceding code:</span></span>

  * <span data-ttu-id="c78a3-215">ランダムな BLOB 値を使用して既存の行を更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="c78a3-216">行が更新されるたびに RowVersion 列をランダムな BLOB 値に設定するデータベース トリガーを追加します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="c78a3-217">端末で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="c78a3-218">Department ページをスキャフォールディングする</span><span class="sxs-lookup"><span data-stu-id="c78a3-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c78a3-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c78a3-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c78a3-220">次の例外を除き、「[Student ページをスキャフォールディングする](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。</span><span class="sxs-lookup"><span data-stu-id="c78a3-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="c78a3-221">*Pages/Departments* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="c78a3-222">モデル クラスに `Department` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="c78a3-223">新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c78a3-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c78a3-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c78a3-225">*Pages/Departments* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="c78a3-226">次のコマンドを実行して、Department ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="c78a3-227">**Windows の場合:**</span><span class="sxs-lookup"><span data-stu-id="c78a3-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="c78a3-228">**Linux または macOS の場合:**</span><span class="sxs-lookup"><span data-stu-id="c78a3-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="c78a3-229">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="c78a3-230">Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-230">Update the Index page</span></span>

<span data-ttu-id="c78a3-231">スキャフォールディング ツールにより Index ページに `RowVersion` 列が作成されましたが、そのフィールドは運用アプリには表示されません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="c78a3-232">このチュートリアルでは、コンカレンシーの処理がどのように動作するのかを示すため、`RowVersion` の最後のバイトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="c78a3-233">最後のバイトは、それ自体では一意であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="c78a3-234">*Pages\Departments\Index.cshtml* ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="c78a3-235">Department で Index を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="c78a3-236">`RowVersion` が含まれるコードを、バイト配列の最後のバイトだけが表示されるように変更します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="c78a3-237">FirstMidName を FullName で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="c78a3-238">次のコードでは、更新されたページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="c78a3-239">Edit ページ モデルの更新</span><span class="sxs-lookup"><span data-stu-id="c78a3-239">Update the Edit page model</span></span>

<span data-ttu-id="c78a3-240">次のコードで *Pages\Departments\Edit.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="c78a3-241"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> は、`OnGet` メソッドでフェッチされたときのエンティティの `rowVersion` 値で更新されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="c78a3-242">EF Core では、WHERE 句に元の `RowVersion` 値が含まれる、SQL の UPDATE コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="c78a3-243">UPDATE コマンドの影響を受ける行がない場合 (元の `RowVersion` 値が含まれる行がない)、`DbUpdateConcurrencyException` 例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="c78a3-244">前の強調表示されたコードでは、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="c78a3-245">`Department.RowVersion` の値は、Edit ページに対する Get 要求でもともとフェッチされたエンティティのものです。</span><span class="sxs-lookup"><span data-stu-id="c78a3-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="c78a3-246">その値は、編集対象のエンティティが表示される Razor ページの非表示フィールドによって、`OnPost` メソッドに提供されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="c78a3-247">非表示フィールドの値は、モデル バインダーによって `Department.RowVersion` にコピーされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="c78a3-248">`OriginalValue` は、EF Core によって Where 句で使用されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="c78a3-249">強調表示されたコード行が実行される前の `OriginalValue` の値は、このメソッドで `FirstOrDefaultAsync` が呼び出されたときのデータベース内の値であり、これは Edit ページに表示されたものと異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="c78a3-250">強調表示されたコードにより、EF Core では SQL UPDATE ステートメントの Where 句で表示された `RowVersion` エンティティの元の `Department` の値が確実に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="c78a3-251">コンカレンシー エラーが発生すると、次の強調表示されたコードにより、クライアントの値 (このメソッドにポストされた値) とデータベースの値が取得されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="c78a3-252">次のコードによって、`OnPostAsync` にポストされたのとは異なるデータベースの値がある各列に、カスタム エラー メッセージが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="c78a3-253">次の強調表示されたコードによって、データベースから取得された新しい値に `RowVersion` 値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="c78a3-254">次にユーザーが **[保存]** をクリックしたとき、Edit ページが最後に表示されたときのコンカレンシー エラーのみが検出されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="c78a3-255">`ModelState` の `RowVersion` 値が古いため、`ModelState.Remove` ステートメントが必要になります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="c78a3-256">Razor ページでは、どちらも存在する場合は、フィールドの `ModelState` 値がモデル プロパティ値より優先されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="c78a3-257">[編集] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-257">Update the Edit page</span></span>

<span data-ttu-id="c78a3-258">*Pages/Departments/Edit.cshtml* を次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="c78a3-259">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-259">The preceding code:</span></span>

* <span data-ttu-id="c78a3-260">`page` ディレクティブを `@page` から `@page "{id:int}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="c78a3-261">非表示の行バージョンが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-261">Adds a hidden row version.</span></span> <span data-ttu-id="c78a3-262">ポストバックによって値がバインドされるように、`RowVersion` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="c78a3-263">デバッグのために、`RowVersion` の最後のバイトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="c78a3-264">`ViewData` を厳密に型指定された `InstructorNameSL` と置換します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="c78a3-265">Edit ページでのコンカレンシーの競合のテスト</span><span class="sxs-lookup"><span data-stu-id="c78a3-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="c78a3-266">English 部署の 2 つの Edit のブラウザー インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="c78a3-267">アプリを実行し、部署を選択します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="c78a3-268">English 部署の **[編集]** ハイパーリンクを右クリックし、 **[新しいタブで開く]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="c78a3-269">最初のタブの English 部署の **[編集]** ハイパーリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="c78a3-270">2 つのブラウザー タブに同じ情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="c78a3-271">最初のブラウザー タブの名前を変更し、 **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-271">Change the name in the first browser tab and click **Save**.</span></span>

![変更後の Department Edit ページ 1](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="c78a3-273">Index ページが、値が変更され、rowVersion インジケーターが更新され、ブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="c78a3-274">更新された rowVersion インジケーターに注意してください。これは、他方のタブの 2 番目のポストバックに表示されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="c78a3-275">2 番目のブラウザー タブで別のフィールドを変更します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-275">Change a different field in the second browser tab.</span></span>

![変更後の Department Edit ページ 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="c78a3-277">**[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-277">Click **Save**.</span></span> <span data-ttu-id="c78a3-278">データベースの値と一致しないすべてのフィールドに、エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-278">You see error messages for all fields that don't match the database values:</span></span>

![Department Edit ページのエラー メッセージ](concurrency/_static/edit-error30.png)

<span data-ttu-id="c78a3-280">このブラウザー ウィンドウでは、Name フィールドの変更は意図されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="c78a3-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="c78a3-281">Name フィールドに、現在の値 (言語) をコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="c78a3-282">タブを終了します。クライアント側の検証によって、エラー メッセージが削除されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="c78a3-283">**[保存]** をもう一度クリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-283">Click **Save** again.</span></span> <span data-ttu-id="c78a3-284">2 番目のブラウザー タブに入力した値が保存されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="c78a3-285">Index ページで、保存した値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="c78a3-286">Delete ページ モデルを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-286">Update the Delete page model</span></span>

<span data-ttu-id="c78a3-287">*Pages/Departments/Delete.cshtml.cs* を次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="c78a3-288">フェッチ後にエンティティに変更があった場合、Delete ページによってコンカレンシーの競合が検出されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="c78a3-289">`Department.RowVersion` は、エンティティがフェッチされたときの行バージョンです。</span><span class="sxs-lookup"><span data-stu-id="c78a3-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="c78a3-290">EF Core が SQL DELETE コマンドを作成するとき、それには `RowVersion` 句が含まれる WHERE 句が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="c78a3-291">SQL DELETE コマンドで影響を受ける行がゼロの場合、次が発生します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="c78a3-292">SQL DELETE コマンドの `RowVersion` がデータベースの `RowVersion` と一致しません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="c78a3-293">DbUpdateConcurrencyException 例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="c78a3-294">`OnGetAsync` が `concurrencyError` と共に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="c78a3-295">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-295">Update the Delete page</span></span>

<span data-ttu-id="c78a3-296">次のコードを使用して、*Pages/Departments/Delete.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="c78a3-297">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="c78a3-298">`page` ディレクティブを `@page` から `@page "{id:int}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="c78a3-299">エラー メッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-299">Adds an error message.</span></span>
* <span data-ttu-id="c78a3-300">**[管理者]** フィールドで FirstMidName が FullName に変更されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="c78a3-301">最後のバイトを表示するよう、`RowVersion` を変更します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="c78a3-302">非表示の行バージョンが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-302">Adds a hidden row version.</span></span> <span data-ttu-id="c78a3-303">ポストバックによって値がバインドされるように、`RowVersion` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="c78a3-304">コンカレンシーの競合をテストする</span><span class="sxs-lookup"><span data-stu-id="c78a3-304">Test concurrency conflicts</span></span>

<span data-ttu-id="c78a3-305">テスト部署を作成します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-305">Create a test department.</span></span>

<span data-ttu-id="c78a3-306">テスト部署の 2 つの Delete のブラウザー インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="c78a3-307">アプリを実行し、部署を選択します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="c78a3-308">テスト部署の **[削除]** ハイパーリンクを右クリックし、 **[新しいタブで開く]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="c78a3-309">テスト部署の **[編集]** ハイパーリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="c78a3-310">2 つのブラウザー タブに同じ情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="c78a3-311">最初のブラウザー タブで予算を変更し、 **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="c78a3-312">Index ページが、値が変更され、rowVersion インジケーターが更新され、ブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="c78a3-313">更新された rowVersion インジケーターに注意してください。これは、他方のタブの 2 番目のポストバックに表示されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="c78a3-314">2 番目のタブから、テスト部署を削除します。データベースからの現在の値で、コンカレンシー エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="c78a3-315">**[削除]** をクリックすると、`RowVersion` が更新されていない限り、エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c78a3-316">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c78a3-316">Additional resources</span></span>

* [<span data-ttu-id="c78a3-317">コンカレンシー トークン</span><span class="sxs-lookup"><span data-stu-id="c78a3-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="c78a3-318">EF Core のコンカレンシーの処理</span><span class="sxs-lookup"><span data-stu-id="c78a3-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="c78a3-319">ASP.NET Core 2.x ソースのデバッグ</span><span class="sxs-lookup"><span data-stu-id="c78a3-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="c78a3-320">次の手順</span><span class="sxs-lookup"><span data-stu-id="c78a3-320">Next steps</span></span>

<span data-ttu-id="c78a3-321">これは、シリーズの最後のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="c78a3-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="c78a3-322">[このチュートリアル シリーズの MVC バージョン](xref:data/ef-mvc/index)では、その他のトピックについて説明されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c78a3-323">前のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="c78a3-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c78a3-324">このチュートリアルでは、複数のユーザーがエンティティをコンカレントに更新するときの競合の処理方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="c78a3-325">解決できない問題が発生した場合は、[完成したアプリをダウンロードまたは表示](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)してください。</span><span class="sxs-lookup"><span data-stu-id="c78a3-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="c78a3-326">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c78a3-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="c78a3-327">コンカレンシーの競合</span><span class="sxs-lookup"><span data-stu-id="c78a3-327">Concurrency conflicts</span></span>

<span data-ttu-id="c78a3-328">コンカレンシーの競合は、次のような場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="c78a3-329">ユーザーがエンティティの Edit ページに移動した場合。</span><span class="sxs-lookup"><span data-stu-id="c78a3-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="c78a3-330">最初のユーザーの変更がデータベースに書き込まれる前に、別のユーザーが同じエンティティを更新した場合。</span><span class="sxs-lookup"><span data-stu-id="c78a3-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="c78a3-331">コンカレンシーの検出が無効のとき、コンカレント更新が発生すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="c78a3-332">最後の更新が有効になります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-332">The last update wins.</span></span> <span data-ttu-id="c78a3-333">つまり、最後に更新された値がデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="c78a3-334">現在の更新の最初のものは失われます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="c78a3-335">オプティミスティック コンカレンシー</span><span class="sxs-lookup"><span data-stu-id="c78a3-335">Optimistic concurrency</span></span>

<span data-ttu-id="c78a3-336">オプティミスティック コンカレンシーでは、コンカレンシーの競合の発生が許可され、それが発生した場合に適切に対処されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="c78a3-337">たとえば、Jane が Department Edit ページにアクセスし、English 部署の予算を $350,000.00 から $0.00 に変更するとします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![予算を 0 に変更する](concurrency/_static/change-budget.png)

<span data-ttu-id="c78a3-339">Jane が **[保存]** をクリックする前に John が同じページにアクセスし、[開始日] フィールドを 9/1/2007 から 9/1/2013 に変更します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![開始日を 2013 に変更する](concurrency/_static/change-date.png)

<span data-ttu-id="c78a3-341">Jane が **[保存]** を先にクリックすると、ブラウザーの Index ページには、Jane の変更が反映されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![予算が 0 に変更された](concurrency/_static/budget-zero.png)

<span data-ttu-id="c78a3-343">John が Edit ページの **[保存]** をクリックします。このとき、予算は $350,000.00 と表示されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="c78a3-344">この後の動作は、コンカレンシーの競合の処理方法によって決定します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="c78a3-345">オプティミスティック コンカレンシーには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="c78a3-346">ユーザーが変更したプロパティを追跡記録し、それに該当する列だけをデータベースで更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="c78a3-347">このシナリオでは、データは失われません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="c78a3-348">2 人のユーザーが別のプロパティを更新しました。</span><span class="sxs-lookup"><span data-stu-id="c78a3-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="c78a3-349">今度誰かが English 部署を閲覧すると、Jane の変更と John の変更が両方とも表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="c78a3-350">この更新方法では、データが失われる原因となる競合の数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="c78a3-351">この方法の特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c78a3-351">This approach:</span></span>
 
  * <span data-ttu-id="c78a3-352">競合する変更が同じプロパティに加えられた場合、データの損失を回避することはできません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="c78a3-353">Web アプリでは、一般的に実用的ではありません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="c78a3-354">フェッチされたすべての値と新しい値を追跡するために、かなりのステータスを維持することが必要になります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="c78a3-355">大量のステータスを保守管理することになると、アプリケーションのパフォーマンスに影響が出ます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="c78a3-356">エンティティでのコンカレンシーの検出と比較して、アプリは複雑になります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="c78a3-357">John の変更で Jane の変更を上書きするように指定できます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="c78a3-358">今度誰かが English 部署を閲覧すると、9/1/2013 の日付と、フェッチされた $350,000.00 の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="c78a3-359">このアプローチは *Client Wins* (クライアント側に合わせる) シナリオまたは *Last in Wins* (最終書き込み者優先) シナリオと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="c78a3-360">(クライアントからの値がすべて、データ ストアの値より優先されます。)コンカレンシー処理に対してコーディングを行わない場合、自動的にクライアント側に合わせられます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="c78a3-361">データベースで John の変更が更新されないようにできます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="c78a3-362">通常、アプリの動作は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-362">Typically, the app would:</span></span>

  * <span data-ttu-id="c78a3-363">エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-363">Display an error message.</span></span>
  * <span data-ttu-id="c78a3-364">データの現在のステータスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="c78a3-365">ユーザーが変更を再適用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="c78a3-366">これは *Store Wins* (ストア側に合わせる) シナリオと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="c78a3-367">(クライアントが送信した値よりデータストアの値が優先されます。)このチュートリアルでは、Store Wins シナリオを実装します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="c78a3-368">この手法では、変更が上書きされるとき、それが必ずユーザーに警告されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="c78a3-369">コンカレンシーの処理</span><span class="sxs-lookup"><span data-stu-id="c78a3-369">Handling concurrency</span></span> 

<span data-ttu-id="c78a3-370">プロパティが[コンカレンシー トークン](/ef/core/modeling/concurrency)として構成されている場合、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="c78a3-371">EF Core によって、フェッチ後にプロパティが変更されていないことが確認されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="c78a3-372">このチェックは、[SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) または [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに発生します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="c78a3-373">フェッチ後にプロパティが変更されていると、[DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="c78a3-374">データベースとデータ モデルは、`DbUpdateConcurrencyException` のスローをサポートするように構成される必要があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="c78a3-375">プロパティのコンカレンシーの競合の検出</span><span class="sxs-lookup"><span data-stu-id="c78a3-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="c78a3-376">コンカレンシーの競合は、[ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) 属性を使用し、プロパティ レベルで検出できます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="c78a3-377">この属性は、モデルの複数のプロパティに適用できます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="c78a3-378">詳細については、「[データの注釈 - ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c78a3-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="c78a3-379">このチュートリアルでは、`[ConcurrencyCheck]` 属性は使用しません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="c78a3-380">行のコンカレンシーの競合の検出</span><span class="sxs-lookup"><span data-stu-id="c78a3-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="c78a3-381">コンカレンシーの競合を検出するために、モデルに [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 追跡列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="c78a3-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="c78a3-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="c78a3-382">`rowversion` :</span></span>

* <span data-ttu-id="c78a3-383">SQL Server 専用です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-383">Is SQL Server specific.</span></span> <span data-ttu-id="c78a3-384">他のデータベースには、似たような機能がない場合があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="c78a3-385">データベースからフェッチされた以降、エンティティに変更がないことを決定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="c78a3-386">データベースによって、行が更新されるたびに増える、連続する `rowversion` 番号値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="c78a3-387">`Update` または `Delete` コマンドの `Where` 句には、フェッチされた `rowversion` の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="c78a3-388">更新された行が変更された場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="c78a3-389">`rowversion` がフェッチされた値と一致しなくなります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="c78a3-390">`Where` 句にフェッチされた `rowversion` が含まれるので、`Update` または `Delete` コマンドでは行が検索されません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="c78a3-391">`DbUpdateConcurrencyException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="c78a3-392">EF Core では、`Update` または `Delete` コマンドで行が更新されない場合、コンカレンシー競合がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="c78a3-393">Department エンティティにトラッキング プロパティを追加する</span><span class="sxs-lookup"><span data-stu-id="c78a3-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="c78a3-394">*Models/Department.cs* で、RowVersion という名前のトラッキング プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="c78a3-395">[Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) 属性では、この列は、`Update` および `Delete` コマンドの `Where` 句に含まれることを指定します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="c78a3-396">前のバージョンの SQL Server では、SQL `rowversion` 型に取って代わられる前、SQL `timestamp` というデータ型が使用されていたため、この属性は `Timestamp` と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="c78a3-397">Fluent API でも、トラッキング プロパティを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="c78a3-398">次のコードは、Department 名が更新されたときに、EF Core によって生成された T-SQL ステートメントの一部です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="c78a3-399">上の強調表示されたコードには、`RowVersion` を含む `WHERE` 句があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="c78a3-400">データベースの `RowVersion` が `RowVersion` パラメーター (`@p2`) と一致しない場合、行は更新されません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="c78a3-401">次の強調表示されたコードは、1 つの行のみが更新されたことを検証する T-SQL です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="c78a3-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) では、最後のステートメントの影響を受けた行数を返します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="c78a3-403">更新された行がない場合、EF Core は `DbUpdateConcurrencyException` をスローします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="c78a3-404">Visual Studio の出力ウィンドウでは、EF Core が生成する T-SQL を確認できます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="c78a3-405">データベースの更新</span><span class="sxs-lookup"><span data-stu-id="c78a3-405">Update the DB</span></span>

<span data-ttu-id="c78a3-406">`RowVersion` プロパティを追加すると、移行を必要とするデータベース モデルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="c78a3-407">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-407">Build the project.</span></span> <span data-ttu-id="c78a3-408">コマンド ウィンドウに次を入力します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="c78a3-409">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-409">The preceding commands:</span></span>

* <span data-ttu-id="c78a3-410">*Migrations/{time stamp}_RowVersion.cs* 移行ファイルが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="c78a3-411">*Migrations/SchoolContextModelSnapshot.cs* ファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="c78a3-412">更新により、次の強調表示されたコードが `BuildModel` メソッドに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="c78a3-413">データベースを更新するために移行が実行されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="c78a3-414">部署モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c78a3-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c78a3-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c78a3-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="c78a3-416">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-student-pages)」の手順に従い、モデル クラスの `Department` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c78a3-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c78a3-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="c78a3-418">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="c78a3-419">上記のコマンドは、`Department` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="c78a3-420">Visual Studio でプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="c78a3-421">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="c78a3-422">Departments Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-422">Update the Departments Index page</span></span>

<span data-ttu-id="c78a3-423">スキャフォールディング エンジンにより Index ページに `RowVersion` 列が作成されましたが、このフィールドは表示すべきではありません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="c78a3-424">このチュートリアルでは、コンカレンシーを理解するために、`RowVersion` の最後のバイトが表示されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="c78a3-425">最後のバイトは、一意であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="c78a3-426">実際のアプリでは、`RowVersion` や `RowVersion` の最後のバイトは表示されません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="c78a3-427">Index ページを更新するために、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-427">Update the Index page:</span></span>

* <span data-ttu-id="c78a3-428">Department で Index を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="c78a3-429">`RowVersion` の最後のバイトで、`RowVersion` を含むマークアップを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="c78a3-430">FirstMidName を FullName で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="c78a3-431">次のマークアップは、更新されたページを示しています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="c78a3-432">Edit ページ モデルの更新</span><span class="sxs-lookup"><span data-stu-id="c78a3-432">Update the Edit page model</span></span>

<span data-ttu-id="c78a3-433">次のコードで *Pages\Departments\Edit.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="c78a3-434">コンカレンシーの問題を検出するために、フェッチされたエンティティの [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) が `rowVersion` 値で更新されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="c78a3-435">EF Core では、WHERE 句に元の `RowVersion` 値が含まれる、SQL の UPDATE コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="c78a3-436">UPDATE コマンドの影響を受ける行がない場合 (元の `RowVersion` 値が含まれる行がない)、`DbUpdateConcurrencyException` 例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="c78a3-437">上のコードでは、エンティティがフェッチされたときの値は、`Department.RowVersion` です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="c78a3-438">このメソッドで `FirstOrDefaultAsync` が呼び出されましたときのデータベース内の値は、`OriginalValue` です。</span><span class="sxs-lookup"><span data-stu-id="c78a3-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="c78a3-439">次のコードによって、クライアントの値 (このメソッドにポストされた値) とデータベースの値が取得されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="c78a3-440">次のコードによって、`OnPostAsync` にポストされたのとは異なるデータベースの値がある各列に、カスタム エラー メッセージが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="c78a3-441">次の強調表示されたコードによって、データベースから取得された新しい値に `RowVersion` 値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="c78a3-442">次にユーザーが **[保存]** をクリックしたとき、Edit ページが最後に表示されたときのコンカレンシー エラーのみが検出されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="c78a3-443">`ModelState` の `RowVersion` 値が古いため、`ModelState.Remove` ステートメントが必要になります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="c78a3-444">Razor ページでは、どちらも存在する場合は、フィールドの `ModelState` 値がモデル プロパティ値より優先されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="c78a3-445">[編集] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-445">Update the Edit page</span></span>

<span data-ttu-id="c78a3-446">次のマークアップを使用して *Pages/Departments/Edit.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="c78a3-447">上のマークアップでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-447">The preceding markup:</span></span>

* <span data-ttu-id="c78a3-448">`page` ディレクティブを `@page` から `@page "{id:int}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="c78a3-449">非表示の行バージョンが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-449">Adds a hidden row version.</span></span> <span data-ttu-id="c78a3-450">ポストバックが値をバインドするように、`RowVersion` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="c78a3-451">デバッグのために、`RowVersion` の最後のバイトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="c78a3-452">`ViewData` を厳密に型指定された `InstructorNameSL` と置換します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="c78a3-453">Edit ページでのコンカレンシーの競合のテスト</span><span class="sxs-lookup"><span data-stu-id="c78a3-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="c78a3-454">English 部署の 2 つの Edit のブラウザー インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="c78a3-455">アプリを実行し、部署を選択します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="c78a3-456">English 部署の **[編集]** ハイパーリンクを右クリックし、 **[新しいタブで開く]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="c78a3-457">最初のタブの English 部署の **[編集]** ハイパーリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="c78a3-458">2 つのブラウザー タブに同じ情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="c78a3-459">最初のブラウザー タブの名前を変更し、 **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-459">Change the name in the first browser tab and click **Save**.</span></span>

![変更後の Department Edit ページ 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="c78a3-461">Index ページが、値が変更され、rowVersion インジケーターが更新され、ブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="c78a3-462">更新された rowVersion インジケーターに注意してください。これは、他方のタブの 2 番目のポストバックに表示されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="c78a3-463">2 番目のブラウザー タブで別のフィールドを変更します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-463">Change a different field in the second browser tab.</span></span>

![変更後の Department Edit ページ 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="c78a3-465">**[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-465">Click **Save**.</span></span> <span data-ttu-id="c78a3-466">データベースの値と一致しないすべてのフィールドに、エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-466">You see error messages for all fields that don't match the DB values:</span></span>

![Department Edit ページのエラー メッセージ](concurrency/_static/edit-error.png)

<span data-ttu-id="c78a3-468">このブラウザー ウィンドウでは、Name フィールドの変更は意図されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="c78a3-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="c78a3-469">Name フィールドに、現在の値 (言語) をコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="c78a3-470">タブを終了します。クライアント側の検証によって、エラー メッセージが削除されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-470">Tab out. Client-side validation removes the error message.</span></span>

![Department Edit ページのエラー メッセージ](concurrency/_static/cv.png)

<span data-ttu-id="c78a3-472">**[保存]** をもう一度クリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-472">Click **Save** again.</span></span> <span data-ttu-id="c78a3-473">2 番目のブラウザー タブに入力した値が保存されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="c78a3-474">Index ページで、保存した値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="c78a3-475">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-475">Update the Delete page</span></span>

<span data-ttu-id="c78a3-476">次のコードを使用して、[削除] ページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="c78a3-477">フェッチ後にエンティティに変更があった場合、Delete ページによってコンカレンシーの競合が検出されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="c78a3-478">`Department.RowVersion` は、エンティティがフェッチされたときの行バージョンです。</span><span class="sxs-lookup"><span data-stu-id="c78a3-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="c78a3-479">EF Core が SQL DELETE コマンドを作成するとき、それには `RowVersion` 句が含まれる WHERE 句が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="c78a3-480">SQL DELETE コマンドで影響を受ける行がゼロの場合、次が発生します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="c78a3-481">SQL DELETE コマンドの `RowVersion` がデータベースの `RowVersion` と一致しません。</span><span class="sxs-lookup"><span data-stu-id="c78a3-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="c78a3-482">DbUpdateConcurrencyException 例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="c78a3-483">`OnGetAsync` が `concurrencyError` と共に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="c78a3-484">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="c78a3-484">Update the Delete page</span></span>

<span data-ttu-id="c78a3-485">次のコードを使用して、*Pages/Departments/Delete.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="c78a3-486">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="c78a3-487">`page` ディレクティブを `@page` から `@page "{id:int}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="c78a3-488">エラー メッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-488">Adds an error message.</span></span>
* <span data-ttu-id="c78a3-489">**[管理者]** フィールドで FirstMidName が FullName に変更されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="c78a3-490">最後のバイトを表示するよう、`RowVersion` を変更します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="c78a3-491">非表示の行バージョンが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-491">Adds a hidden row version.</span></span> <span data-ttu-id="c78a3-492">ポストバックが値をバインドするように、`RowVersion` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c78a3-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="c78a3-493">Delete ページでのコンカレンシーの競合のテスト</span><span class="sxs-lookup"><span data-stu-id="c78a3-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="c78a3-494">テスト部署を作成します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-494">Create a test department.</span></span>

<span data-ttu-id="c78a3-495">テスト部署の 2 つの Delete のブラウザー インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="c78a3-496">アプリを実行し、部署を選択します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="c78a3-497">テスト部署の **[削除]** ハイパーリンクを右クリックし、 **[新しいタブで開く]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c78a3-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="c78a3-498">テスト部署の **[編集]** ハイパーリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="c78a3-499">2 つのブラウザー タブに同じ情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="c78a3-500">最初のブラウザー タブで予算を変更し、 **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c78a3-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="c78a3-501">Index ページが、値が変更され、rowVersion インジケーターが更新され、ブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="c78a3-502">更新された rowVersion インジケーターに注意してください。これは、他方のタブの 2 番目のポストバックに表示されています。</span><span class="sxs-lookup"><span data-stu-id="c78a3-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="c78a3-503">2 番目のタブから、テスト部署を削除します。データベースからの現在の値で、コンカレンシー エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="c78a3-504">**[削除]** をクリックすると、`RowVersion` が更新されていない限り、エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="c78a3-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="c78a3-505">データ モデルを継承する方法については、「[継承](xref:data/ef-mvc/inheritance)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c78a3-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="c78a3-506">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c78a3-506">Additional resources</span></span>

* [<span data-ttu-id="c78a3-507">コンカレンシー トークン</span><span class="sxs-lookup"><span data-stu-id="c78a3-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="c78a3-508">EF Core のコンカレンシーの処理</span><span class="sxs-lookup"><span data-stu-id="c78a3-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="c78a3-509">このチュートリアルの YouTube バージョン (コンカレンシーの競合の処理)</span><span class="sxs-lookup"><span data-stu-id="c78a3-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="c78a3-510">このチュートリアルの YouTube バージョン (パート 2)</span><span class="sxs-lookup"><span data-stu-id="c78a3-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="c78a3-511">このチュートリアルの YouTube バージョン (パート 3)</span><span class="sxs-lookup"><span data-stu-id="c78a3-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="c78a3-512">前へ</span><span class="sxs-lookup"><span data-stu-id="c78a3-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end


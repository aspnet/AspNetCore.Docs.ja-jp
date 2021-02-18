---
title: ASP.NET Core Blazor Server と Entity Framework Core (EFCore)
author: JeremyLikness
description: Blazor Server アプリで EF Core を使用するためのガイダンスです。
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 6fc8913640a0a8d506e2c00002912897edbfd826
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280480"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="07c46-103">ASP.NET Core Blazor Server と Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="07c46-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="07c46-104">Blazor Server はステートフル アプリ フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="07c46-104">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="07c46-105">アプリではサーバーへの継続的な接続が維持され、ユーザーの状態は "*回線*" 内のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-105">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="07c46-106">ユーザー状態の一例として、回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータがあります。</span><span class="sxs-lookup"><span data-stu-id="07c46-106">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="07c46-107">Blazor Server が提供する独自のアプリケーション モデルでは、Entity Framework Core を使用するための特別なアプローチが必要です。</span><span class="sxs-lookup"><span data-stu-id="07c46-107">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="07c46-108">この記事では、Blazor Server アプリでの EF Core について説明します。</span><span class="sxs-lookup"><span data-stu-id="07c46-108">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="07c46-109">Blazor WebAssembly アプリは、ほとんどの直接データベース接続が防止される WebAssembly サンドボックス内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-109">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="07c46-110">Blazor WebAssembly での EF Core の実行については、この記事では扱いません。</span><span class="sxs-lookup"><span data-stu-id="07c46-110">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="07c46-111">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="07c46-111">Sample app</span></span></h2>

<span data-ttu-id="07c46-112">このサンプル アプリは、EF Core を使用する Blazor Server アプリのリファレンスとして作成されました。</span><span class="sxs-lookup"><span data-stu-id="07c46-112">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="07c46-113">サンプル アプリには、並べ替えとフィルター処理、削除、追加、更新の各操作を行うグリッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="07c46-113">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="07c46-114">このサンプルは、EF Core を使用してオプティミスティック同時実行制御を処理する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="07c46-114">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="07c46-115">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="07c46-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="07c46-116">サンプルにはローカルの [SQLite](https://www.sqlite.org/index.html) データベースが使用されているため、どのプラットフォームでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="07c46-116">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="07c46-117">このサンプルでは、生成された SQL クエリを表示するようにデータベースのログも構成されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-117">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="07c46-118">これは `appsettings.Development.json` で構成されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-118">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="07c46-119">グリッド、追加、および表示の各コンポーネントでは、操作ごとにコンテキストが作成される "操作ごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-119">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="07c46-120">編集コンポーネントでは、コンポーネントごとにコンテキストが作成される "コンポーネントごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-120">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="07c46-121">このトピックのコード例には、表示されていない名前空間とサービスを必要とするものもあります。</span><span class="sxs-lookup"><span data-stu-id="07c46-121">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="07c46-122">Razor の例に必要な [`@using`](xref:mvc/views/razor#using) および [`@inject`](xref:mvc/views/razor#inject) ディレクティブを含む完全に機能するコードを検査するには、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07c46-122">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="07c46-123">データベース アクセス</span><span class="sxs-lookup"><span data-stu-id="07c46-123">Database access</span></span></h2>

<span data-ttu-id="07c46-124">EF Core では、[データベース アクセスを構成](/ef/core/miscellaneous/configuring-dbcontext)し、"[*作業単位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)" として機能する手段として <xref:Microsoft.EntityFrameworkCore.DbContext> を利用しています。</span><span class="sxs-lookup"><span data-stu-id="07c46-124">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="07c46-125">EF Core には、既定でコンテキストを "*スコープ*" サービスとして登録する ASP.NET Core アプリの <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-125">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="07c46-126">Blazor Server アプリでは、インスタンスがユーザーの回線内のコンポーネント全体で共有されるため、スコープ サービスの登録が問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="07c46-126">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="07c46-127"><xref:Microsoft.EntityFrameworkCore.DbContext> はスレッド セーフではなく、同時に使用するように設計されていません。</span><span class="sxs-lookup"><span data-stu-id="07c46-127"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="07c46-128">次の理由により、既存の有効期間は不適切です。</span><span class="sxs-lookup"><span data-stu-id="07c46-128">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="07c46-129">**[Singleton]\(シングルトン\)** の場合、アプリのすべてのユーザーで状態が共有され、不適切な同時使用につながります。</span><span class="sxs-lookup"><span data-stu-id="07c46-129">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="07c46-130">**[範囲指定]** (既定値) の場合、同じユーザーのコンポーネント間で同様の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="07c46-130">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="07c46-131">**[一時的]** の場合、要求ごとに新しいインスタンスが生成されます。ただし、コンポーネントの有効期間が長くなる可能性があるため、意図したよりも時間のかかるコンテキストになります。</span><span class="sxs-lookup"><span data-stu-id="07c46-131">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="07c46-132">以下の推奨事項は、Blazor Server アプリで EF Core を使用する上で一貫したアプローチを提供するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-132">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="07c46-133">既定では、操作ごとに 1 つのコンテキストを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="07c46-133">By default, consider using one context per operation.</span></span> <span data-ttu-id="07c46-134">コンテキストは、高速でオーバーヘッドの少ないインスタンス化を目的として設計されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-134">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="07c46-135">フラグを使用して、複数の同時操作を防止します。</span><span class="sxs-lookup"><span data-stu-id="07c46-135">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="07c46-136">`try` ブロックの `Loading = true;` 行の後に操作を配置します。</span><span class="sxs-lookup"><span data-stu-id="07c46-136">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="07c46-137">EF Core の[変更追跡](/ef/core/querying/tracking)または[同時実行制御](/ef/core/saving/concurrency)を利用する、より長期間の操作の場合は、[コンポーネントの有効期間にコンテキストの範囲を限定します](#scope-to-the-component-lifetime-5x)。</span><span class="sxs-lookup"><span data-stu-id="07c46-137">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="07c46-138">新しい DbContext インスタンス</span><span class="sxs-lookup"><span data-stu-id="07c46-138">New DbContext instances</span></span></h3>

<span data-ttu-id="07c46-139">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを作成する最も簡単な方法は、`new` を使用して新しいインスタンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="07c46-139">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="07c46-140">ただし、追加の依存関係を解決する必要があるシナリオがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="07c46-140">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="07c46-141">たとえば、[`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) を使用してコンテキストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="07c46-141">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="07c46-142">依存関係を持つ新しい <xref:Microsoft.EntityFrameworkCore.DbContext> を作成するには、ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="07c46-142">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="07c46-143">EF Core 5.0 以降には、新しいコンテキストを作成するためのファクトリが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="07c46-143">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="07c46-144">次の例では、[SQLite](https://www.sqlite.org/index.html) を構成し、データのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="07c46-144">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="07c46-145">このコードでは、[拡張メソッド (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) を使用して、DI のデータベース ファクトリを構成し、既定のオプションを指定しています。</span><span class="sxs-lookup"><span data-stu-id="07c46-145">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="07c46-146">ファクトリはコンポーネントに挿入され、新しいインスタンスを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-146">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="07c46-147">たとえば、`Pages/Index.razor` では次のようにします。</span><span class="sxs-lookup"><span data-stu-id="07c46-147">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="07c46-148">`Wrapper` は、`GridWrapper` コンポーネントへの[コンポーネント参照](xref:blazor/components/index#capture-references-to-components)です。</span><span class="sxs-lookup"><span data-stu-id="07c46-148">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="07c46-149">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)の `Index` コンポーネント (`Pages/Index.razor`) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07c46-149">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="07c46-150">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスは、[[ASP.NET Core の Identity モデル]](xref:security/authentication/customize_identity_model) を使用する場合など、`DbContext` ごとに接続文字列を構成できるファクトリを使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="07c46-150">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="07c46-151">コンポーネントの有効期間の範囲</span><span class="sxs-lookup"><span data-stu-id="07c46-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="07c46-152">コンポーネントの有効期間中は存在する <xref:Microsoft.EntityFrameworkCore.DbContext> を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="07c46-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="07c46-153">これにより、それを[作業単位](https://martinfowler.com/eaaCatalog/unitOfWork.html)として使用し、変更の追跡や同時実行の解決などの組み込み機能を利用することができます。</span><span class="sxs-lookup"><span data-stu-id="07c46-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="07c46-154">ファクトリーを使用してコンテキストを作成し、コンポーネントの有効期間中はそれを追跡できます。</span><span class="sxs-lookup"><span data-stu-id="07c46-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="07c46-155">まず <xref:System.IDisposable> を実装し、`Pages/EditContact.razor` に示すようにファクトリを挿入します。</span><span class="sxs-lookup"><span data-stu-id="07c46-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="07c46-156">サンプル アプリでは、コンポーネントが破棄されるときに、コンテキストも確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="07c46-157">最後に、[`OnInitializedAsync`](xref:blazor/components/lifecycle) は新しいコンテキストを作成するためにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="07c46-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="07c46-158">サンプル アプリでは、[`OnInitializedAsync`](xref:blazor/components/lifecycle) を使用して同じメソッドで連絡先を読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="07c46-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="07c46-159">機密データのログ記録を有効にする</span><span class="sxs-lookup"><span data-stu-id="07c46-159">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="07c46-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> では、例外メッセージおよびフレームワークのログにアプリケーション データが含まれます。</span><span class="sxs-lookup"><span data-stu-id="07c46-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="07c46-161">ログに記録されるデータには、エンティティ インスタンスのプロパティに割り当てられた値と、データベースに送信されたコマンドのパラメーター値を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="07c46-161">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="07c46-162"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> を使用したデータのログ記録は **セキュリティ リスク** です。データベースに対して実行された SQL ステートメントをログに記録するときに、パスワードやその他の個人を特定できる情報 (PII) が公開される可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="07c46-162">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="07c46-163"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> は、開発とテストのためにのみ有効にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="07c46-163">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="07c46-164">Blazor Server はステートフル アプリ フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="07c46-164">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="07c46-165">アプリではサーバーへの継続的な接続が維持され、ユーザーの状態は "*回線*" 内のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-165">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="07c46-166">ユーザー状態の一例として、回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータがあります。</span><span class="sxs-lookup"><span data-stu-id="07c46-166">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="07c46-167">Blazor Server が提供する独自のアプリケーション モデルでは、Entity Framework Core を使用するための特別なアプローチが必要です。</span><span class="sxs-lookup"><span data-stu-id="07c46-167">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="07c46-168">この記事では、Blazor Server アプリでの EF Core について説明します。</span><span class="sxs-lookup"><span data-stu-id="07c46-168">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="07c46-169">Blazor WebAssembly アプリは、ほとんどの直接データベース接続が防止される WebAssembly サンドボックス内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-169">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="07c46-170">Blazor WebAssembly での EF Core の実行については、この記事では扱いません。</span><span class="sxs-lookup"><span data-stu-id="07c46-170">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="07c46-171">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="07c46-171">Sample app</span></span></h2>

<span data-ttu-id="07c46-172">このサンプル アプリは、EF Core を使用する Blazor Server アプリのリファレンスとして作成されました。</span><span class="sxs-lookup"><span data-stu-id="07c46-172">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="07c46-173">サンプル アプリには、並べ替えとフィルター処理、削除、追加、更新の各操作を行うグリッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="07c46-173">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="07c46-174">このサンプルは、EF Core を使用してオプティミスティック同時実行制御を処理する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="07c46-174">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="07c46-175">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="07c46-175">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="07c46-176">サンプルにはローカルの [SQLite](https://www.sqlite.org/index.html) データベースが使用されているため、どのプラットフォームでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="07c46-176">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="07c46-177">このサンプルでは、生成された SQL クエリを表示するようにデータベースのログも構成されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-177">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="07c46-178">これは `appsettings.Development.json` で構成されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-178">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="07c46-179">グリッド、追加、および表示の各コンポーネントでは、操作ごとにコンテキストが作成される "操作ごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-179">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="07c46-180">編集コンポーネントでは、コンポーネントごとにコンテキストが作成される "コンポーネントごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-180">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="07c46-181">このトピックのコード例には、表示されていない名前空間とサービスを必要とするものもあります。</span><span class="sxs-lookup"><span data-stu-id="07c46-181">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="07c46-182">Razor の例に必要な [`@using`](xref:mvc/views/razor#using) および [`@inject`](xref:mvc/views/razor#inject) ディレクティブを含む完全に機能するコードを検査するには、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07c46-182">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="07c46-183">データベース アクセス</span><span class="sxs-lookup"><span data-stu-id="07c46-183">Database access</span></span></h2>

<span data-ttu-id="07c46-184">EF Core では、[データベース アクセスを構成](/ef/core/miscellaneous/configuring-dbcontext)し、"[*作業単位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)" として機能する手段として <xref:Microsoft.EntityFrameworkCore.DbContext> を利用しています。</span><span class="sxs-lookup"><span data-stu-id="07c46-184">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="07c46-185">EF Core には、既定でコンテキストを "*スコープ*" サービスとして登録する ASP.NET Core アプリの <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-185">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="07c46-186">Blazor Server アプリでは、インスタンスがユーザーの回線内のコンポーネント全体で共有されるため、これは問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="07c46-186">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="07c46-187"><xref:Microsoft.EntityFrameworkCore.DbContext> はスレッド セーフではなく、同時に使用するように設計されていません。</span><span class="sxs-lookup"><span data-stu-id="07c46-187"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="07c46-188">次の理由により、既存の有効期間は不適切です。</span><span class="sxs-lookup"><span data-stu-id="07c46-188">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="07c46-189">**[Singleton]\(シングルトン\)** の場合、アプリのすべてのユーザーで状態が共有され、不適切な同時使用につながります。</span><span class="sxs-lookup"><span data-stu-id="07c46-189">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="07c46-190">**[範囲指定]** (既定値) の場合、同じユーザーのコンポーネント間で同様の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="07c46-190">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="07c46-191">**[一時的]** の場合、要求ごとに新しいインスタンスが生成されます。ただし、コンポーネントの有効期間が長くなる可能性があるため、意図したよりも時間のかかるコンテキストになります。</span><span class="sxs-lookup"><span data-stu-id="07c46-191">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="07c46-192">以下の推奨事項は、Blazor Server アプリで EF Core を使用する上で一貫したアプローチを提供するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-192">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="07c46-193">既定では、操作ごとに 1 つのコンテキストを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="07c46-193">By default, consider using one context per operation.</span></span> <span data-ttu-id="07c46-194">コンテキストは、高速でオーバーヘッドの少ないインスタンス化を目的として設計されています。</span><span class="sxs-lookup"><span data-stu-id="07c46-194">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="07c46-195">フラグを使用して、複数の同時操作を防止します。</span><span class="sxs-lookup"><span data-stu-id="07c46-195">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="07c46-196">`try` ブロックの `Loading = true;` 行の後に操作を配置します。</span><span class="sxs-lookup"><span data-stu-id="07c46-196">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="07c46-197">EF Core の[変更追跡](/ef/core/querying/tracking)または[同時実行制御](/ef/core/saving/concurrency)を利用する、より長期間の操作の場合は、[コンポーネントの有効期間にコンテキストの範囲を限定します](#scope-to-the-component-lifetime-3x)。</span><span class="sxs-lookup"><span data-stu-id="07c46-197">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="07c46-198">新しい DbContext インスタンス</span><span class="sxs-lookup"><span data-stu-id="07c46-198">New DbContext instances</span></span></h3>

<span data-ttu-id="07c46-199">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを作成する最も簡単な方法は、`new` を使用して新しいインスタンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="07c46-199">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="07c46-200">ただし、追加の依存関係を解決する必要があるシナリオがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="07c46-200">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="07c46-201">たとえば、[`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) を使用してコンテキストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="07c46-201">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="07c46-202">依存関係を持つ新しい <xref:Microsoft.EntityFrameworkCore.DbContext> を作成するには、ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="07c46-202">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="07c46-203">このサンプル アプリでは、`Data/DbContextFactory.cs` で独自のファクトリを実装しています。</span><span class="sxs-lookup"><span data-stu-id="07c46-203">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="07c46-204">前のファクトリは、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="07c46-204">In the preceding factory:</span></span>

* <span data-ttu-id="07c46-205">すべての依存関係は、サービス プロバイダーを介して <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> によって満たされています。</span><span class="sxs-lookup"><span data-stu-id="07c46-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="07c46-206">`IDbContextFactory` は、EF Core ASP.NET Core 5.0 以降で使用できます。そのため、インターフェイスは、[ASP.NET Core 3.x 用のサンプル アプリに実装されます](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)。</span><span class="sxs-lookup"><span data-stu-id="07c46-206">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="07c46-207">次の例では、[SQLite](https://www.sqlite.org/index.html) を構成し、データのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="07c46-207">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="07c46-208">このコードでは、拡張メソッドを使用して、DI のデータベース ファクトリを構成し、既定のオプションを指定しています。</span><span class="sxs-lookup"><span data-stu-id="07c46-208">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="07c46-209">ファクトリはコンポーネントに挿入され、新しいインスタンスを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-209">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="07c46-210">たとえば、`Pages/Index.razor` では次のようにします。</span><span class="sxs-lookup"><span data-stu-id="07c46-210">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="07c46-211">`Wrapper` は、`GridWrapper` コンポーネントへの[コンポーネント参照](xref:blazor/components/index#capture-references-to-components)です。</span><span class="sxs-lookup"><span data-stu-id="07c46-211">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="07c46-212">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)の `Index` コンポーネント (`Pages/Index.razor`) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07c46-212">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="07c46-213">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスは、[ASP.NET Core の Identity モデル])(xref:security/authentication/customize_identity_model) を使用する場合など、`DbContext` ごとに接続文字列を構成できるファクトリを使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="07c46-213">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="07c46-214">コンポーネントの有効期間の範囲</span><span class="sxs-lookup"><span data-stu-id="07c46-214">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="07c46-215">コンポーネントの有効期間中は存在する <xref:Microsoft.EntityFrameworkCore.DbContext> を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="07c46-215">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="07c46-216">これにより、それを[作業単位](https://martinfowler.com/eaaCatalog/unitOfWork.html)として使用し、変更の追跡や同時実行の解決などの組み込み機能を利用することができます。</span><span class="sxs-lookup"><span data-stu-id="07c46-216">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="07c46-217">ファクトリーを使用してコンテキストを作成し、コンポーネントの有効期間中はそれを追跡できます。</span><span class="sxs-lookup"><span data-stu-id="07c46-217">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="07c46-218">まず <xref:System.IDisposable> を実装し、`Pages/EditContact.razor` に示すようにファクトリを挿入します。</span><span class="sxs-lookup"><span data-stu-id="07c46-218">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="07c46-219">サンプル アプリでは、コンポーネントが破棄されるときに、コンテキストも確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="07c46-219">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="07c46-220">最後に、[`OnInitializedAsync`](xref:blazor/components/lifecycle) は新しいコンテキストを作成するためにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="07c46-220">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="07c46-221">サンプル アプリでは、[`OnInitializedAsync`](xref:blazor/components/lifecycle) を使用して同じメソッドで連絡先を読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="07c46-221">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="07c46-222">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="07c46-222">In the preceding example:</span></span>

* <span data-ttu-id="07c46-223">`Busy` を `true` に設定すると、非同期操作が開始されることがあります。</span><span class="sxs-lookup"><span data-stu-id="07c46-223">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="07c46-224">`Busy` を `false` に戻すと、非同期操作は完了するはずです。</span><span class="sxs-lookup"><span data-stu-id="07c46-224">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="07c46-225">追加のエラー処理ロジックを `catch` ブロックに配置します。</span><span class="sxs-lookup"><span data-stu-id="07c46-225">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="07c46-226">機密データのログ記録を有効にする</span><span class="sxs-lookup"><span data-stu-id="07c46-226">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="07c46-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> では、例外メッセージおよびフレームワークのログにアプリケーション データが含まれます。</span><span class="sxs-lookup"><span data-stu-id="07c46-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="07c46-228">ログに記録されるデータには、エンティティ インスタンスのプロパティに割り当てられた値と、データベースに送信されたコマンドのパラメーター値を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="07c46-228">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="07c46-229"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> を使用したデータのログ記録は **セキュリティ リスク** です。データベースに対して実行された SQL ステートメントをログに記録するときに、パスワードやその他の個人を特定できる情報 (PII) が公開される可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="07c46-229">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="07c46-230"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> は、開発とテストのためにのみ有効にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="07c46-230">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="07c46-231">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="07c46-231">Additional resources</span></span>

* [<span data-ttu-id="07c46-232">EF Core のドキュメント</span><span class="sxs-lookup"><span data-stu-id="07c46-232">EF Core documentation</span></span>](/ef/)

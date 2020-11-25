---
title: ASP.NET Core での Razor ページの概要
author: Rick-Anderson
description: ASP.NET Core の Razor Pages を使用して、ページのコーディングに重点を置いたシナリオをより簡略化して、MVC を使用する場合よりも生産性を高める方法について説明します。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: bc5f3c85d7ace120dec8369f4550a097b4f258ce
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417644"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="dba02-103">ASP.NET Core での Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="dba02-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="dba02-104">[Rick Anderson](https://twitter.com/RickAndMSFT) および [Ryan Nowak](https://github.com/rynowak) 著</span><span class="sxs-lookup"><span data-stu-id="dba02-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="dba02-105">Razor ページを利用することで、ページのコーディングに今まで以上に集中できます。また、コントローラーとビューを使用する場合より生産的になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="dba02-106">モデル ビュー コントローラーのアプローチを使用するチュートリアルをお探しの場合は、「[Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc)」 (ASP.NET Core MVC の概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="dba02-107">このドキュメントでは、Razor ページの概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="dba02-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="dba02-108">手順を追って説明するチュートリアルではありません。</span><span class="sxs-lookup"><span data-stu-id="dba02-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="dba02-109">セクションの一部を理解できない場合は、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="dba02-110">ASP.NET Core の概要については、「[ASP.NET Core の概要](xref:index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dba02-111">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="dba02-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="dba02-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba02-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba02-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba02-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba02-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dba02-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="dba02-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba02-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba02-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba02-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba02-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dba02-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="dba02-118">Razor ページ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="dba02-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba02-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba02-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dba02-120">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba02-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba02-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dba02-122">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="dba02-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba02-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dba02-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dba02-124">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="dba02-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="dba02-125">Razor Pages</span></span>

<span data-ttu-id="dba02-126">Razor ページは "*Startup.cs*" で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="dba02-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="dba02-127">基本ページを検討します。<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="dba02-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="dba02-128">上記のコードは、コントローラーとビューを含んだ ASP.NET Core アプリで使われる [Razor ビュー ファイル](xref:tutorials/first-mvc-app/adding-view)によく似ています。</span><span class="sxs-lookup"><span data-stu-id="dba02-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="dba02-129">違いは [`@page`](xref:mvc/views/razor#page) ディレクティブにあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="dba02-130">`@page` はファイルを MVC アクションにします。つまり、コントローラーを経由せずに要求を直接処理します。</span><span class="sxs-lookup"><span data-stu-id="dba02-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="dba02-131">`@page` はページ上で最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="dba02-132">`@page` はその他の [Razor](xref:mvc/views/razor) コンストラクトの動作に影響します。</span><span class="sxs-lookup"><span data-stu-id="dba02-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="dba02-133">Razor ページのファイル名には " *.cshtml*" サフィックスが付きます。</span><span class="sxs-lookup"><span data-stu-id="dba02-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="dba02-134">`PageModel` クラスを使用している類似したページが、次の 2 つのファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="dba02-135">*Pages/Index2.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="dba02-136">*Pages/Index2.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="dba02-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="dba02-137">規則により、`PageModel` クラス ファイルは、Razor ページ ファイルと同じ名前に " *.cs*" が付加された名前になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="dba02-138">たとえば、上の Razor ページは "*Pages/Index2.cshtml*" になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="dba02-139">`PageModel` クラスを含むファイル名は、*Pages/Index2.cshtml.cs* になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="dba02-140">URL パスのページへの関連付けは、ファイル システム内のページの場所によって決定されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="dba02-141">次の表に、Razor ページ パスと一致 URL を示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="dba02-142">ファイル名とパス</span><span class="sxs-lookup"><span data-stu-id="dba02-142">File name and path</span></span>               | <span data-ttu-id="dba02-143">一致 URL</span><span class="sxs-lookup"><span data-stu-id="dba02-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="dba02-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="dba02-145">`/` または `/Index`</span><span class="sxs-lookup"><span data-stu-id="dba02-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="dba02-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="dba02-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="dba02-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="dba02-149">`/Store` または `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="dba02-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="dba02-150">メモ:</span><span class="sxs-lookup"><span data-stu-id="dba02-150">Notes:</span></span>

* <span data-ttu-id="dba02-151">既定では、ランタイムが "*Pages*" フォルダー内で Razor ページ ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="dba02-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="dba02-152">`Index` は、URL にページが含まれない場合の既定のページになります。</span><span class="sxs-lookup"><span data-stu-id="dba02-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="dba02-153">基本フォームを作成する</span><span class="sxs-lookup"><span data-stu-id="dba02-153">Write a basic form</span></span>

<span data-ttu-id="dba02-154">Razor ページは、アプリの構築時に Web ブラウザーで使用される一般的なパターンを実装しやすくするために設計されています。</span><span class="sxs-lookup"><span data-stu-id="dba02-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="dba02-155">[モデル バインド](xref:mvc/models/model-binding)、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、および HTML ヘルパーはすべて、Razor ページ クラスで定義されたプロパティで "*機能します*"。</span><span class="sxs-lookup"><span data-stu-id="dba02-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="dba02-156">`Contact` モデルの基本的な "お問い合わせ" フォームを実装するページを考察します。</span><span class="sxs-lookup"><span data-stu-id="dba02-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="dba02-157">このドキュメントのサンプルでは、[Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) ファイルで `DbContext` が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="dba02-158">インメモリ データベースには、`Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="dba02-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="dba02-159">データ モデル:</span><span class="sxs-lookup"><span data-stu-id="dba02-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="dba02-160">db コンテキスト:</span><span class="sxs-lookup"><span data-stu-id="dba02-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="dba02-161">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="dba02-162">*Pages/Create.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="dba02-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="dba02-163">規則により、`PageModel` クラスは `<PageName>Model` と呼ばれ、ページと同じ名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="dba02-164">`PageModel` クラスでは、ページの表示からロジックを分離できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="dba02-165">これは、ページに送信される要求のページ ハンドラーと、ページのレンダリングに使用されるデータを定義します。</span><span class="sxs-lookup"><span data-stu-id="dba02-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="dba02-166">この分離により可能になること:</span><span class="sxs-lookup"><span data-stu-id="dba02-166">This separation allows:</span></span>

* <span data-ttu-id="dba02-167">[依存関係の挿入](xref:fundamentals/dependency-injection)によるページの依存関係の管理。</span><span class="sxs-lookup"><span data-stu-id="dba02-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="dba02-168">単体テスト</span><span class="sxs-lookup"><span data-stu-id="dba02-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="dba02-169">このページには、(ユーザーがフォームを投稿したときに) `POST` 要求で実行される `OnPostAsync` "*ハンドラー メソッド*" があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="dba02-170">任意の HTTP 動詞のハンドラー メソッドを追加できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="dba02-171">最も一般的なハンドラーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dba02-171">The most common handlers are:</span></span>

* <span data-ttu-id="dba02-172">ページに必要な状態を初期化するための `OnGet`。</span><span class="sxs-lookup"><span data-stu-id="dba02-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="dba02-173">前のコードでは、`OnGet` メソッドにより "*CreateModel.cshtml*" Razor ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="dba02-174">フォームの送信を処理するための `OnPost`。</span><span class="sxs-lookup"><span data-stu-id="dba02-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="dba02-175">`Async` 名前付けサフィックスは省略可能ですが、非同期関数の規則でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="dba02-176">上記のコードは、Razor ページでは一般的です。</span><span class="sxs-lookup"><span data-stu-id="dba02-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="dba02-177">コントローラーとビューを利用する ASP.NET アプリに慣れている場合:</span><span class="sxs-lookup"><span data-stu-id="dba02-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="dba02-178">前の例の `OnPostAsync` コードは、一般的なコントローラー コードに似ています。</span><span class="sxs-lookup"><span data-stu-id="dba02-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="dba02-179">[モデル バインド](xref:mvc/models/model-binding)、[検証](xref:mvc/models/validation)、アクションの結果など、MVC プリミティブのほとんどは Controllers ページや Razor ページと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="dba02-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="dba02-180">上記の `OnPostAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="dba02-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="dba02-181">`OnPostAsync` の基本的な流れは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dba02-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="dba02-182">検証エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="dba02-182">Check for validation errors.</span></span>

* <span data-ttu-id="dba02-183">エラーがない場合は、データを保存し、リダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="dba02-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="dba02-184">エラーがある場合は、検証メッセージとともにページをもう一度表示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="dba02-185">多くの場合、検証エラーはクライアントで検出され、サーバーには送信されません。</span><span class="sxs-lookup"><span data-stu-id="dba02-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="dba02-186">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="dba02-187">*Pages/Create.cshtml* からレンダリングされた HTML:</span><span class="sxs-lookup"><span data-stu-id="dba02-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="dba02-188">前のコードで投稿したフォーム:</span><span class="sxs-lookup"><span data-stu-id="dba02-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="dba02-189">有効なデータ:</span><span class="sxs-lookup"><span data-stu-id="dba02-189">With valid data:</span></span>

  * <span data-ttu-id="dba02-190">`OnPostAsync` ハンドラー メソッドにより <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> ヘルパー メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="dba02-191">`RedirectToPage` は <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="dba02-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="dba02-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="dba02-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="dba02-193">はアクションの結果です。</span><span class="sxs-lookup"><span data-stu-id="dba02-193">Is an action result.</span></span>
    * <span data-ttu-id="dba02-194">は、(コントローラーやビューで使用される) `RedirectToAction` や`RedirectToRoute` に似ています。</span><span class="sxs-lookup"><span data-stu-id="dba02-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="dba02-195">はページ用にカスタマイズされています。</span><span class="sxs-lookup"><span data-stu-id="dba02-195">Is customized for pages.</span></span> <span data-ttu-id="dba02-196">上記のサンプルでは、ルート インデックス ページ (`/Index`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="dba02-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="dba02-197">`RedirectToPage` については、「[ページの URL の生成](#url_gen)」セクションで詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="dba02-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="dba02-198">サーバーに検証エラーが渡される:</span><span class="sxs-lookup"><span data-stu-id="dba02-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="dba02-199">`OnPostAsync` ハンドラー メソッドにより <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> ヘルパー メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="dba02-200">`Page` は <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="dba02-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="dba02-201">`Page` を返すのは、コントローラーのアクションが `View` を返す方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="dba02-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="dba02-202">`PageResult` はハンドラー メソッドの既定の戻り値の型です。</span><span class="sxs-lookup"><span data-stu-id="dba02-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="dba02-203">`void` を返すハンドラー メソッドがページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="dba02-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="dba02-204">前の例では、値のないフォームが投稿された結果、[ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) から false が返されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="dba02-205">このサンプルでは、検証エラーはクライアントに表示されません。</span><span class="sxs-lookup"><span data-stu-id="dba02-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="dba02-206">検証エラーの処理については、このドキュメントの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="dba02-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="dba02-207">クライアント側の検証で検証エラーが検出される:</span><span class="sxs-lookup"><span data-stu-id="dba02-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="dba02-208">データはサーバーに投稿されて **いません**。</span><span class="sxs-lookup"><span data-stu-id="dba02-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="dba02-209">クライアント側の検証については、このドキュメントの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="dba02-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="dba02-210">`Customer` プロパティは [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性を使用してモデル バインドにオプトインします。</span><span class="sxs-lookup"><span data-stu-id="dba02-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="dba02-211">`[BindProperty]` は、クライアントが変更するべきではないプロパティを含むモデルで使用 **しないで** ください。</span><span class="sxs-lookup"><span data-stu-id="dba02-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="dba02-212">詳細については、「[過剰ポスティング](xref:data/ef-rp/crud#overposting)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dba02-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="dba02-213">既定では、Razor ページはプロパティを非 `GET` 動詞とのみバインドします。</span><span class="sxs-lookup"><span data-stu-id="dba02-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="dba02-214">プロパティにバインドすると、HTTP データをモデル型に変換する目的でコードを記述する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="dba02-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="dba02-215">同じプロパティを使用してバインドすることでコードを減らし、フィールド (`<input asp-for="Customer.Name">`) からレンダリングして入力を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="dba02-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="dba02-216">*Pages/Create.cshtml* ビュー ファイルのレビュー:</span><span class="sxs-lookup"><span data-stu-id="dba02-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="dba02-217">前のコードでは、[入力タグ ヘルパー](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` によって HTML `<input>` 要素が `Customer.Name` モデル式にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="dba02-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="dba02-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) でタグ ヘルパーを使用可能にします。</span><span class="sxs-lookup"><span data-stu-id="dba02-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="dba02-219">ホーム ページ</span><span class="sxs-lookup"><span data-stu-id="dba02-219">The home page</span></span>

<span data-ttu-id="dba02-220">*Index.cshtml* はホーム ページです。</span><span class="sxs-lookup"><span data-stu-id="dba02-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="dba02-221">関連付けられた `PageModel` クラス (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="dba02-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="dba02-222">*Index.cshtml* ファイルには、次のマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="dba02-223">`<a /a>` [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)では、`asp-route-{value}` 属性を使用して編集ページへのリンクが生成されました。</span><span class="sxs-lookup"><span data-stu-id="dba02-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="dba02-224">リンクには、連絡先 ID とともにルート データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="dba02-225">たとえば、`https://localhost:5001/Edit/1` のようにします。</span><span class="sxs-lookup"><span data-stu-id="dba02-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="dba02-226">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="dba02-227">*Index.cshtml* ファイルには、各顧客の連絡先の削除ボタンを作成するマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="dba02-228">レンダリングされた HTML:</span><span class="sxs-lookup"><span data-stu-id="dba02-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="dba02-229">HTML で削除ボタンがレンダリングされる場合、その [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) には次のパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="dba02-230">`asp-route-id` 属性によって指定された顧客の連絡先 ID。</span><span class="sxs-lookup"><span data-stu-id="dba02-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="dba02-231">`asp-page-handler` 属性によって指定された `handler`。</span><span class="sxs-lookup"><span data-stu-id="dba02-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="dba02-232">ボタンが選択されると、フォームの `POST` 要求がサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="dba02-233">慣例により、ハンドラー メソッドの名前はスキーム `OnPost[handler]Async` に従った `handler` パラメーターの値に基づいて選択されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="dba02-234">この例では `handler` が `delete` であるため、`OnPostDeleteAsync` ハンドラー メソッドを使用して `POST` 要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="dba02-235">`asp-page-handler` が `remove` などの別の値に設定されている場合、名前が `OnPostRemoveAsync` のハンドラー メソッドが選択されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="dba02-236">`OnPostDeleteAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="dba02-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="dba02-237">クエリ文字列から `id` を取得します。</span><span class="sxs-lookup"><span data-stu-id="dba02-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="dba02-238">`FindAsync` を使用してデータベースから顧客の連絡先を照会します。</span><span class="sxs-lookup"><span data-stu-id="dba02-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="dba02-239">顧客の連絡先が見つからない場合、それは削除されており、データベースが更新されています。</span><span class="sxs-lookup"><span data-stu-id="dba02-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="dba02-240">ルート インデックス ページ (`/Index`) にリダイレクトされるように、<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dba02-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="dba02-241">Edit.cshtml ファイル</span><span class="sxs-lookup"><span data-stu-id="dba02-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="dba02-242">最初の行には `@page "{id:int}"` ディレクティブが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="dba02-243">ルーティングの制約 `"{id:int}"` は、`int` ルート データを含むページへの要求を受け入れるようにページに指示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="dba02-244">ページへの要求に `int` に変換できるルート データが含まれていない場合は、ランタイムで HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="dba02-245">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="dba02-246">*Edit.cshtml.cs* ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="dba02-247">検証</span><span class="sxs-lookup"><span data-stu-id="dba02-247">Validation</span></span>

<span data-ttu-id="dba02-248">検証規則:</span><span class="sxs-lookup"><span data-stu-id="dba02-248">Validation rules:</span></span>

* <span data-ttu-id="dba02-249">はモデル クラスで指定 (宣言) されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="dba02-250">はアプリ内のあらゆる場所で適用されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="dba02-251"><xref:System.ComponentModel.DataAnnotations> 名前空間には、クラスまたはプロパティに宣言的に適用される一連の組み込みの検証属性があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="dba02-252">また、DataAnnotations には、書式設定を支援し、どの検証を行わない [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) のような書式設定属性もあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="dba02-253">`Customer` モデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="dba02-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="dba02-254">次の *Create.cshtml* ビュー ファイルを使用:</span><span class="sxs-lookup"><span data-stu-id="dba02-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="dba02-255">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="dba02-255">The preceding code:</span></span>

* <span data-ttu-id="dba02-256">jQuery と jQuery 検証スクリプトが含まれます。</span><span class="sxs-lookup"><span data-stu-id="dba02-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="dba02-257">`<div />` と `<span />` [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使用して次を有効にします。</span><span class="sxs-lookup"><span data-stu-id="dba02-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="dba02-258">クライアント側の検証。</span><span class="sxs-lookup"><span data-stu-id="dba02-258">Client-side validation.</span></span>
  * <span data-ttu-id="dba02-259">検証エラー レンダリング。</span><span class="sxs-lookup"><span data-stu-id="dba02-259">Validation error rendering.</span></span>

* <span data-ttu-id="dba02-260">次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="dba02-261">名前値なしで Create フォームを投稿すると、このフォームに "The Name field is required." (名前フィールドは必須です。) というエラー メッセージ</span><span class="sxs-lookup"><span data-stu-id="dba02-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="dba02-262">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-262">on the form.</span></span> <span data-ttu-id="dba02-263">JavaScript がクライアントで有効になっている場合、サーバーに投稿されず、エラーがブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="dba02-264">`[StringLength(10)]` 属性によって、レンダリングされた HTML で `data-val-length-max="10"` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="dba02-265">`data-val-length-max` により、指定の最大長を超える入力がブラウザーで禁止されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="dba02-266">[Fiddler](https://www.telerik.com/fiddler) のようなツールを投稿の編集と返信に使用する場合:</span><span class="sxs-lookup"><span data-stu-id="dba02-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="dba02-267">名前の長さ値が 10 を超えています。</span><span class="sxs-lookup"><span data-stu-id="dba02-267">With the name longer than 10.</span></span>
* <span data-ttu-id="dba02-268">"The field Name must be a string with a maximum length of 10." (名前フィールドは最大長が 10 の文字列になります。) というエラー メッセージが</span><span class="sxs-lookup"><span data-stu-id="dba02-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="dba02-269">返されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-269">is returned.</span></span>

<span data-ttu-id="dba02-270">次の `Movie` モデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="dba02-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="dba02-271">検証属性では、適用対象のモデル プロパティに適用する動作が指定されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="dba02-272">`Required` および `MinimumLength` 属性は、プロパティに値が必要であることを示します。ただし、この検証を満たすためにユーザーが空白を入力することは禁止されていません。</span><span class="sxs-lookup"><span data-stu-id="dba02-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="dba02-273">`RegularExpression` 属性は、入力できる文字を制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="dba02-274">上のコード "Genre" では:</span><span class="sxs-lookup"><span data-stu-id="dba02-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="dba02-275">文字のみを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-275">Must only use letters.</span></span>
  * <span data-ttu-id="dba02-276">最初の文字は大文字にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="dba02-277">空白、数字、特殊文字は使用できません。</span><span class="sxs-lookup"><span data-stu-id="dba02-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="dba02-278">`RegularExpression` "評価":</span><span class="sxs-lookup"><span data-stu-id="dba02-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="dba02-279">最初の文字が大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="dba02-280">後続のスペースでは、特殊文字と数字が使用できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="dba02-281">"PG-13" は評価に対して有効ですが、"Genre" に対しては失敗します。</span><span class="sxs-lookup"><span data-stu-id="dba02-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="dba02-282">`Range` 属性は、指定した範囲内に値を制限します。</span><span class="sxs-lookup"><span data-stu-id="dba02-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="dba02-283">`StringLength` 属性により、文字列プロパティの最大長が設定されます。任意で最小長も設定できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="dba02-284">値の型 (`decimal`、`int`、`float`、`DateTime` など) は本質的に必須ではなく、`[Required]` 属性を必要としません。</span><span class="sxs-lookup"><span data-stu-id="dba02-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="dba02-285">`Movie` モデルの [作成] ページには、エラーと無効な値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![複数 jQuery クライアント側検証エラーが表示されたムービー ビュー フォーム](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="dba02-287">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="dba02-287">For more information, see:</span></span>

* [<span data-ttu-id="dba02-288">Movie アプリに検証を追加する</span><span class="sxs-lookup"><span data-stu-id="dba02-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="dba02-289">[ASP.NET Core のモデル検証](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="dba02-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="dba02-290">OnGet ハンドラー フォールバックを使用した HEAD 要求の処理</span><span class="sxs-lookup"><span data-stu-id="dba02-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="dba02-291">`HEAD` 要求により、特定のリソースのヘッダーを取得できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="dba02-292">`GET` 要求とは異なり、`HEAD` 要求から応答本文は返されません。</span><span class="sxs-lookup"><span data-stu-id="dba02-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="dba02-293">通常、`HEAD` 要求に対して `OnHead` ハンドラーが作成され、呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="dba02-294">`OnHead` ハンドラーが定義されていない場合、Razor ページは `OnGet` ハンドラーの呼び出しにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="dba02-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="dba02-295">XSRF/CSRF および Razor ページ</span><span class="sxs-lookup"><span data-stu-id="dba02-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="dba02-296">Razor ページは、[偽造防止検証](xref:security/anti-request-forgery)によって保護されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="dba02-297">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) により HTML フォーム要素に偽造防止トークンが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="dba02-298">Razor ページでのレイアウト、パーシャル、テンプレート、およびタグ ヘルパーの使用</span><span class="sxs-lookup"><span data-stu-id="dba02-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="dba02-299">ページは、Razor ビュー エンジンのすべての機能で動作します。</span><span class="sxs-lookup"><span data-stu-id="dba02-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="dba02-300">レイアウト、パーシャル、テンプレート、タグ ヘルパー、" *_ViewStart.cshtml*"、" *_ViewImports.cshtml*" は、従来の Razor ビューの場合と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="dba02-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="dba02-301">これらの機能の一部を利用してこのページをまとめてみましょう。</span><span class="sxs-lookup"><span data-stu-id="dba02-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="dba02-302">[レイアウト ページ](xref:mvc/views/layout)を *Pages/Shared/_Layout.cshtml* に追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="dba02-303">[レイアウト](xref:mvc/views/layout)は次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="dba02-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="dba02-304">(ページでレイアウトを止めない限り) 各ページのレイアウトを制御します。</span><span class="sxs-lookup"><span data-stu-id="dba02-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="dba02-305">JavaScript やスタイルシートなどの HTML 構造をインポートします。</span><span class="sxs-lookup"><span data-stu-id="dba02-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="dba02-306">`@RenderBody()` が呼び出されるところで Razor ページの内容が表示されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="dba02-307">詳細については、[レイアウトに関するページ](xref:mvc/views/layout)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="dba02-308">[Layout](xref:mvc/views/layout#specifying-a-layout) プロパティは *Pages/_ViewStart.cshtml* で設定されています。</span><span class="sxs-lookup"><span data-stu-id="dba02-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="dba02-309">レイアウトは、*Pages/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="dba02-310">ページは現在のページと同じフォルダーから開始して、階層的に他のビュー (レイアウト、テンプレート、パーシャル) を検索します。</span><span class="sxs-lookup"><span data-stu-id="dba02-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="dba02-311">"*Pages/Shared*" フォルダー内のレイアウトは、"*Pages*" フォルダー配下の任意の Razor ページから使用できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="dba02-312">レイアウト ファイルは *Pages/Shared* フォルダーに入ります。</span><span class="sxs-lookup"><span data-stu-id="dba02-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="dba02-313">レイアウト ファイルを *Views/Shared* フォルダー内に配置 **しない** ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dba02-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="dba02-314">*Views/Shared* は MVC ビュー パターンです。</span><span class="sxs-lookup"><span data-stu-id="dba02-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="dba02-315">Razor ページは、パス規則ではなく、フォルダー階層に依存することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="dba02-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="dba02-316">Razor ページからのビュー検索には、"*Pages*" フォルダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="dba02-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="dba02-317">MVC コントローラーで使用されているレイアウト、テンプレート、パーシャルと、従来の Razor ビューは "*機能します*"。</span><span class="sxs-lookup"><span data-stu-id="dba02-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="dba02-318">*Pages/_ViewImports.cshtml* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="dba02-319">`@namespace` はこのチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="dba02-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="dba02-320">`@addTagHelper` ディレクティブにより、[組み込みタグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/Index)が *Pages* フォルダー内のすべてのページにもたらされます。</span><span class="sxs-lookup"><span data-stu-id="dba02-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="dba02-321">ページに設定される `@namespace` ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="dba02-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="dba02-322">`@namespace` ディレクティブは、ページの名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="dba02-323">`@model` ディレクティブには、名前空間を含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dba02-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="dba02-324">`@namespace` ディレクティブが *_ViewImports.cshtml* に含まれていると、指定した名前空間が `@namespace` ディレクティブをインポートするページで生成された名前空間のプレフィックスを提供します。</span><span class="sxs-lookup"><span data-stu-id="dba02-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="dba02-325">生成された名前空間の残りの部分 (サフィックスの部分) は、 *_ViewImports.cshtml* を含むフォルダーとページを含むフォルダー間のドットで区切られた相対パスです。</span><span class="sxs-lookup"><span data-stu-id="dba02-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="dba02-326">たとえば、`PageModel` クラス *Pages/Customers/Edit.cshtml.cs* は名前空間を明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="dba02-327">*Pages/_ViewImports.cshtml* ファイルは次の名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="dba02-328">"*Pages/Customers/Edit.cshtml*" Razor ページの生成された名前空間は、`PageModel` クラスと同じです。</span><span class="sxs-lookup"><span data-stu-id="dba02-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="dba02-329">`@namespace` は "*従来の Razor ビューでも機能します。* "</span><span class="sxs-lookup"><span data-stu-id="dba02-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="dba02-330">*Pages/Create.cshtml* ビュー ファイル を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="dba02-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="dba02-331">更新後の *Pages/Create.cshtml* ビュー ファイル、 *_ViewImports.cshtml*、前のレイアウト ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="dba02-332">前のコードでは、 *_ViewImports.cshtml* によって名前空間とタグ ヘルパーがインポートされました。</span><span class="sxs-lookup"><span data-stu-id="dba02-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="dba02-333">レイアウト ファイルによって JavaScript ファイルがインポートされました。</span><span class="sxs-lookup"><span data-stu-id="dba02-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="dba02-334">[Razor ページのスタート プロジェクト](#rpvs17)には、クライアント側の検証をフックする "*Pages/_ValidationScriptsPartial.cshtml*" が含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="dba02-335">部分ビューの詳細については、「<xref:mvc/views/partial>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="dba02-336">ページの URL の生成</span><span class="sxs-lookup"><span data-stu-id="dba02-336">URL generation for Pages</span></span>

<span data-ttu-id="dba02-337">上に示した `Create` ページでは、`RedirectToPage` を使用します。</span><span class="sxs-lookup"><span data-stu-id="dba02-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="dba02-338">アプリには次のファイル/フォルダー構造があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="dba02-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="dba02-339">*/Pages*</span></span>

  * <span data-ttu-id="dba02-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="dba02-341">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="dba02-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="dba02-342">*/Customers*</span></span>

    * <span data-ttu-id="dba02-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="dba02-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="dba02-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-345">*Index.cshtml*</span></span>

<span data-ttu-id="dba02-346">成功すると、*Pages/Customers/Create.cshtml* ページと *Pages/Customers/Edit.cshtml* ページが *Pages/Customers/Index.cshtml* にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="dba02-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="dba02-347">文字列 `./Index` は、前のページにアクセスするために使用される相対ページ名です。</span><span class="sxs-lookup"><span data-stu-id="dba02-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="dba02-348">これは、*Pages/Customers/Index.cshtml* ページへの URI を生成するために使われます。</span><span class="sxs-lookup"><span data-stu-id="dba02-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="dba02-349">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="dba02-350">絶対ページ名 `/Index` は、*Pages/Index.cshtml* ページへの URL を生成するために使われます。</span><span class="sxs-lookup"><span data-stu-id="dba02-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="dba02-351">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="dba02-352">ページ名は、先頭の `/` を含む、ルート */Pages* フォルダーからページへのパスです (たとえば `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="dba02-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="dba02-353">先述の URL 生成サンプルでは、URL のハードコーディングに関する拡張オプションと機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="dba02-354">URL の生成は[ルーティング](xref:mvc/controllers/routing)を使用し、ターゲット パスで定義されたルート方法に従って、パラメーターの生成とエンコードができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="dba02-355">ページの URL 生成は、相対名をサポートします。</span><span class="sxs-lookup"><span data-stu-id="dba02-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="dba02-356">次の表に、*Pages/Customers/Create.cshtml* の異なる `RedirectToPage` パラメーターで選択されたインデックス ページを示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="dba02-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="dba02-357">RedirectToPage(x)</span></span>| <span data-ttu-id="dba02-358">ページ</span><span class="sxs-lookup"><span data-stu-id="dba02-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="dba02-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="dba02-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="dba02-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="dba02-360">*Pages/Index*</span></span> |
| <span data-ttu-id="dba02-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="dba02-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="dba02-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="dba02-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="dba02-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="dba02-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="dba02-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="dba02-364">*Pages/Index*</span></span> |
| <span data-ttu-id="dba02-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="dba02-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="dba02-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="dba02-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="dba02-367">`RedirectToPage("Index")`、`RedirectToPage("./Index")`、`RedirectToPage("../Index")` は *相対名* です。</span><span class="sxs-lookup"><span data-stu-id="dba02-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="dba02-368">`RedirectToPage` パラメーターは現在のページのパスと *組み合わされて*、ターゲット ページの名前を計算します。</span><span class="sxs-lookup"><span data-stu-id="dba02-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="dba02-369">相対名のリンクは、複雑な構造を持つサイトを構築する際に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="dba02-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="dba02-370">あるフォルダー内のページ間をリンクする目的で相対名を使用するとき:</span><span class="sxs-lookup"><span data-stu-id="dba02-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="dba02-371">フォルダー名を変更しても相対リンクは壊れません。</span><span class="sxs-lookup"><span data-stu-id="dba02-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="dba02-372">フォルダー名が含まれていないため、リンクは壊れません。</span><span class="sxs-lookup"><span data-stu-id="dba02-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="dba02-373">別の [[区分]](xref:mvc/controllers/areas) のページにリダイレクトするには、その区分を指定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="dba02-374">詳細については、次のトピックを参照してください。 <xref:mvc/controllers/areas> および <xref:razor-pages/razor-pages-conventions></span><span class="sxs-lookup"><span data-stu-id="dba02-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="dba02-375">ViewData 属性</span><span class="sxs-lookup"><span data-stu-id="dba02-375">ViewData attribute</span></span>

<span data-ttu-id="dba02-376">データは <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> を含むページに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="dba02-377">`[ViewData]` 属性を含むプロパティにはその値が格納され、<xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="dba02-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="dba02-378">次の例では、`AboutModel` により、`[ViewData]` 属性が `Title` プロパティに適用されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="dba02-379">[About] ページでは、モデル プロパティとして `Title` プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="dba02-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="dba02-380">レイアウトでは、タイトルは ViewData ディクショナリから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="dba02-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="dba02-381">TempData</span><span class="sxs-lookup"><span data-stu-id="dba02-381">TempData</span></span>

<span data-ttu-id="dba02-382">ASP.NET Core により <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> が公開されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="dba02-383">このプロパティは、読み取られるまでデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="dba02-383">This property stores data until it's read.</span></span> <span data-ttu-id="dba02-384"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> メソッドと <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> メソッドは、削除せずにデータを確認するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="dba02-385">`TempData` は、複数の要求に対してデータが必要な場合のリダイレクトに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="dba02-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="dba02-386">次のコードは、`TempData` を使用して `Message` の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="dba02-387">*Pages/Customers/Index.cshtml* ファイル内の次のマークアップは、`TempData` を使用して `Message` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="dba02-388">*Pages/Customers/Index.cshtml.cs* ページは、`[TempData]` 属性を `Message` プロパティに適用します。</span><span class="sxs-lookup"><span data-stu-id="dba02-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="dba02-389">詳細については、「[TempData](xref:fundamentals/app-state#tempdata)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="dba02-390">ページあたり複数のハンドラー</span><span class="sxs-lookup"><span data-stu-id="dba02-390">Multiple handlers per page</span></span>

<span data-ttu-id="dba02-391">次のページでは、`asp-page-handler` タグ ヘルパーを使用して 2 つのハンドラーにマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="dba02-392">前の例のフォームには、それぞれが `FormActionTagHelper` を使用して異なる URL に送信する 2 つの送信ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="dba02-393">`asp-page-handler` 属性は、`asp-page` のコンパニオンです。</span><span class="sxs-lookup"><span data-stu-id="dba02-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="dba02-394">`asp-page-handler` はページごとに定義されている各ハンドラー メソッドに送信する URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="dba02-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="dba02-395">サンプルは現在のページにリンクしているため、`asp-page` は指定されません。</span><span class="sxs-lookup"><span data-stu-id="dba02-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="dba02-396">ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="dba02-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="dba02-397">上記のコードは、*名前付きハンドラー メソッド* を使用しています。</span><span class="sxs-lookup"><span data-stu-id="dba02-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="dba02-398">名前付きハンドラー メソッドは、名前の `On<HTTP Verb>` の後および `Async` の前 (ある場合) のテキストを取得して作成されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="dba02-399">前の例では、ページ メソッドは OnPost **JoinList** Async と OnPost **JoinListUC** Async です。</span><span class="sxs-lookup"><span data-stu-id="dba02-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="dba02-400">*OnPost* と *Async* を削除すると、ハンドラー名は `JoinList` と `JoinListUC` になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="dba02-401">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="dba02-402">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="dba02-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="dba02-403">カスタム ルート</span><span class="sxs-lookup"><span data-stu-id="dba02-403">Custom routes</span></span>

<span data-ttu-id="dba02-404">`@page` ディレクティブを次に使用します:</span><span class="sxs-lookup"><span data-stu-id="dba02-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="dba02-405">カスタム ルートをページに指定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-405">Specify a custom route to a page.</span></span> <span data-ttu-id="dba02-406">たとえば、[バージョン情報] ページへのルートを `@page "/Some/Other/Path"` を使用して `/Some/Other/Path` に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="dba02-407">ページの既定のルートにセグメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-407">Append segments to a page's default route.</span></span> <span data-ttu-id="dba02-408">たとえば、"item" セグメントを `@page "item"` を使用してページの既定のルートに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="dba02-409">ページの既定のルートにパラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="dba02-410">たとえば、`@page "{id}"` を含むページに ID パラメーター `id` を必須とすることができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="dba02-411">パスの先頭のチルダ (`~`) によって指定されたルートの相対パスがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="dba02-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="dba02-412">たとえば、`@page "~/Some/Other/Path"` は `@page "/Some/Other/Path"` と同じです。</span><span class="sxs-lookup"><span data-stu-id="dba02-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="dba02-413">URL 内のクエリ文字列 `?handler=JoinList` が気に入らない場合は、ルートを変更して URL のパス部分にハンドラー名を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="dba02-414">`@page` ディレクティブの後に二重引用符で囲んだルート テンプレートを追加して、ルートをカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="dba02-415">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="dba02-416">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="dba02-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="dba02-417">`handler` の後の `?` は、ルート パラメーターが省略可能なことを意味します。</span><span class="sxs-lookup"><span data-stu-id="dba02-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="dba02-418">詳細な構成と設定</span><span class="sxs-lookup"><span data-stu-id="dba02-418">Advanced configuration and settings</span></span>

<span data-ttu-id="dba02-419">次のセクションの構成と設定はほとんどのアプリで必要ありません。</span><span class="sxs-lookup"><span data-stu-id="dba02-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="dba02-420">高度なオプションを構成するには、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> を構成する <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> オーバーロードを使用します。</span><span class="sxs-lookup"><span data-stu-id="dba02-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="dba02-421"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> を使用してページのルート ディレクトリを設定したり、ページのアプリケーション モデルの規則を追加したりできます。</span><span class="sxs-lookup"><span data-stu-id="dba02-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="dba02-422">規則の詳細については、「[Razor ページの承認規則](xref:security/authorization/razor-pages-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="dba02-423">ビューをプリコンパイルするには、[Razor ビューのコンパイル](xref:mvc/views/view-compilation)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dba02-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="dba02-424">Razor Pages をコンテンツのルートに指定する</span><span class="sxs-lookup"><span data-stu-id="dba02-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="dba02-425">Razor Pages のルートは既定で */Pages* ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="dba02-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="dba02-426"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> を追加して、Razor ページをアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) に置くように指定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="dba02-427">Razor Pages をカスタム ルート ディレクトリに指定する</span><span class="sxs-lookup"><span data-stu-id="dba02-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="dba02-428">(相対パスを指定して) Razor ページをアプリのカスタム ルート ディレクトリに置くように指定するには、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> を追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="dba02-429">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dba02-429">Additional resources</span></span>

* <span data-ttu-id="dba02-430">この概要に基づく、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するページをご覧ください</span><span class="sxs-lookup"><span data-stu-id="dba02-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="dba02-431">属性と Razor ページを承認する</span><span class="sxs-lookup"><span data-stu-id="dba02-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="dba02-432">サンプル コードのダウンロードまたは表示</span><span class="sxs-lookup"><span data-stu-id="dba02-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="dba02-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba02-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba02-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba02-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba02-435">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dba02-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="dba02-436">Razor ページ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="dba02-436">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dba02-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dba02-437">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dba02-438">Razor ページ プロジェクトを作成する詳細な手順については、「[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-438">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dba02-439">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dba02-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dba02-440">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="dba02-440">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="dba02-441">Visual Studio for Mac から生成された *.csproj* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="dba02-441">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dba02-442">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dba02-442">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dba02-443">コマンド ラインから `dotnet new webapp` を実行します。</span><span class="sxs-lookup"><span data-stu-id="dba02-443">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="dba02-444">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="dba02-444">Razor Pages</span></span>

<span data-ttu-id="dba02-445">Razor ページは "*Startup.cs*" で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="dba02-445">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="dba02-446">基本ページを検討します。<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="dba02-446">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="dba02-447">上記のコードは、コントローラーとビューを含んだ ASP.NET Core アプリで使われる [Razor ビュー ファイル](xref:tutorials/first-mvc-app/adding-view)によく似ています。</span><span class="sxs-lookup"><span data-stu-id="dba02-447">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="dba02-448">違いは、`@page` ディレクティブにあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-448">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="dba02-449">`@page` はファイルを MVC アクションにします。つまり、コントローラーを経由せずに要求を直接処理します。</span><span class="sxs-lookup"><span data-stu-id="dba02-449">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="dba02-450">`@page` はページ上で最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-450">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="dba02-451">`@page` はその他の Razor コンストラクトの動作に影響します。</span><span class="sxs-lookup"><span data-stu-id="dba02-451">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="dba02-452">`PageModel` クラスを使用している類似したページが、次の 2 つのファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-452">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="dba02-453">*Pages/Index2.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-453">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="dba02-454">*Pages/Index2.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="dba02-454">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="dba02-455">規則により、`PageModel` クラス ファイルは、Razor ページ ファイルと同じ名前に " *.cs*" が付加された名前になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-455">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="dba02-456">たとえば、上の Razor ページは "*Pages/Index2.cshtml*" になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-456">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="dba02-457">`PageModel` クラスを含むファイル名は、*Pages/Index2.cshtml.cs* になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-457">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="dba02-458">URL パスのページへの関連付けは、ファイル システム内のページの場所によって決定されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-458">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="dba02-459">次の表に、Razor ページ パスと一致 URL を示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-459">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="dba02-460">ファイル名とパス</span><span class="sxs-lookup"><span data-stu-id="dba02-460">File name and path</span></span>               | <span data-ttu-id="dba02-461">一致 URL</span><span class="sxs-lookup"><span data-stu-id="dba02-461">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="dba02-462">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-462">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="dba02-463">`/` または `/Index`</span><span class="sxs-lookup"><span data-stu-id="dba02-463">`/` or `/Index`</span></span> |
| <span data-ttu-id="dba02-464">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-464">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="dba02-465">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-465">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="dba02-466">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-466">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="dba02-467">`/Store` または `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="dba02-467">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="dba02-468">メモ:</span><span class="sxs-lookup"><span data-stu-id="dba02-468">Notes:</span></span>

* <span data-ttu-id="dba02-469">既定では、ランタイムが "*Pages*" フォルダー内で Razor ページ ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="dba02-469">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="dba02-470">`Index` は、URL にページが含まれない場合の既定のページになります。</span><span class="sxs-lookup"><span data-stu-id="dba02-470">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="dba02-471">基本フォームを作成する</span><span class="sxs-lookup"><span data-stu-id="dba02-471">Write a basic form</span></span>

<span data-ttu-id="dba02-472">Razor ページは、アプリの構築時に Web ブラウザーで使用される一般的なパターンを実装しやすくするために設計されています。</span><span class="sxs-lookup"><span data-stu-id="dba02-472">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="dba02-473">[モデル バインド](xref:mvc/models/model-binding)、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、および HTML ヘルパーはすべて、Razor ページ クラスで定義されたプロパティで "*機能します*"。</span><span class="sxs-lookup"><span data-stu-id="dba02-473">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="dba02-474">`Contact` モデルの基本的な "お問い合わせ" フォームを実装するページを考察します。</span><span class="sxs-lookup"><span data-stu-id="dba02-474">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="dba02-475">このドキュメントのサンプルでは、[Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) ファイルで `DbContext` が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-475">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="dba02-476">データ モデル:</span><span class="sxs-lookup"><span data-stu-id="dba02-476">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="dba02-477">db コンテキスト:</span><span class="sxs-lookup"><span data-stu-id="dba02-477">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="dba02-478">*Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-478">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="dba02-479">*Pages/Create.cshtml.cs* ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="dba02-479">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="dba02-480">規則により、`PageModel` クラスは `<PageName>Model` と呼ばれ、ページと同じ名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-480">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="dba02-481">`PageModel` クラスでは、ページの表示からロジックを分離できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-481">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="dba02-482">これは、ページに送信される要求のページ ハンドラーと、ページのレンダリングに使用されるデータを定義します。</span><span class="sxs-lookup"><span data-stu-id="dba02-482">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="dba02-483">この分離により可能になること:</span><span class="sxs-lookup"><span data-stu-id="dba02-483">This separation allows:</span></span>

* <span data-ttu-id="dba02-484">[依存関係の挿入](xref:fundamentals/dependency-injection)によるページの依存関係の管理。</span><span class="sxs-lookup"><span data-stu-id="dba02-484">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="dba02-485">ページを[単体テスト](xref:test/razor-pages-tests)します。</span><span class="sxs-lookup"><span data-stu-id="dba02-485">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="dba02-486">このページには、(ユーザーがフォームを投稿したときに) `POST` 要求で実行される `OnPostAsync` "*ハンドラー メソッド*" があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-486">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="dba02-487">任意の HTTP 動詞のハンドラー メソッドを追加できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-487">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="dba02-488">最も一般的なハンドラーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dba02-488">The most common handlers are:</span></span>

* <span data-ttu-id="dba02-489">ページに必要な状態を初期化するための `OnGet`。</span><span class="sxs-lookup"><span data-stu-id="dba02-489">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="dba02-490">[OnGet](#OnGet) サンプル。</span><span class="sxs-lookup"><span data-stu-id="dba02-490">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="dba02-491">フォームの送信を処理するための `OnPost`。</span><span class="sxs-lookup"><span data-stu-id="dba02-491">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="dba02-492">`Async` 名前付けサフィックスは省略可能ですが、非同期関数の規則でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-492">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="dba02-493">上記のコードは、Razor ページでは一般的です。</span><span class="sxs-lookup"><span data-stu-id="dba02-493">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="dba02-494">コントローラーとビューを利用する ASP.NET アプリに慣れている場合:</span><span class="sxs-lookup"><span data-stu-id="dba02-494">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="dba02-495">前の例の `OnPostAsync` コードは、一般的なコントローラー コードに似ています。</span><span class="sxs-lookup"><span data-stu-id="dba02-495">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="dba02-496">[モデル バインド](xref:mvc/models/model-binding)、[検証](xref:mvc/models/validation)、[検証](xref:mvc/models/validation) アクションの結果などのほとんどの MVC プリミティブは共有されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-496">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="dba02-497">上記の `OnPostAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="dba02-497">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="dba02-498">`OnPostAsync` の基本的な流れは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dba02-498">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="dba02-499">検証エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="dba02-499">Check for validation errors.</span></span>

* <span data-ttu-id="dba02-500">エラーがない場合は、データを保存し、リダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="dba02-500">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="dba02-501">エラーがある場合は、検証メッセージとともにページをもう一度表示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-501">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="dba02-502">クライアント側の検証は、従来の ASP.NET Core MVC アプリケーションと同じです。</span><span class="sxs-lookup"><span data-stu-id="dba02-502">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="dba02-503">多くの場合、検証エラーはクライアントで検出され、サーバーには送信されません。</span><span class="sxs-lookup"><span data-stu-id="dba02-503">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="dba02-504">データが正常に入力されると、`OnPostAsync` ハンドラー メソッドが `RedirectToPage` ヘルパー メソッドを呼び出して `RedirectToPageResult` のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="dba02-504">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="dba02-505">`RedirectToPage` は、`RedirectToAction` や `RedirectToRoute` と同じような新しいアクション結果ですが、ページ用にカスタマイズされています。</span><span class="sxs-lookup"><span data-stu-id="dba02-505">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="dba02-506">上記のサンプルでは、ルート インデックス ページ (`/Index`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="dba02-506">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="dba02-507">`RedirectToPage` については、「[ページの URL の生成](#url_gen)」セクションで詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="dba02-507">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="dba02-508">送信されたフォームに検証エラー (サーバーに渡される) があると、`OnPostAsync` ハンドラー メソッドが `Page` ヘルパー メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dba02-508">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="dba02-509">`Page` は `PageResult` のインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="dba02-509">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="dba02-510">`Page` を返すのは、コントローラーのアクションが `View` を返す方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="dba02-510">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="dba02-511">`PageResult` はハンドラー メソッドの既定の戻り値の型です。</span><span class="sxs-lookup"><span data-stu-id="dba02-511">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="dba02-512">`void` を返すハンドラー メソッドがページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="dba02-512">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="dba02-513">`Customer` プロパティは `[BindProperty]` 属性を使用してモデル バインドにオプトインします。</span><span class="sxs-lookup"><span data-stu-id="dba02-513">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="dba02-514">既定では、Razor ページはプロパティを非 `GET` 動詞とのみバインドします。</span><span class="sxs-lookup"><span data-stu-id="dba02-514">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="dba02-515">プロパティをバインドすることで、記述すべきコードの量を削減できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-515">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="dba02-516">同じプロパティを使用してバインドすることでコードを減らし、フィールド (`<input asp-for="Customer.Name">`) からレンダリングして入力を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="dba02-516">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="dba02-517">ホーム ページ (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="dba02-517">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="dba02-518">関連付けられた `PageModel` クラス (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="dba02-518">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="dba02-519">*Index.cshtml* ファイルには、各連絡先の編集リンクを作成するために次のマークアップが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-519">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="dba02-520">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)では、`asp-route-{value}` 属性を使用して編集ページへのリンクが生成されました。</span><span class="sxs-lookup"><span data-stu-id="dba02-520">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="dba02-521">リンクには、連絡先 ID とともにルート データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-521">The link contains route data with the contact ID.</span></span> <span data-ttu-id="dba02-522">たとえば、`https://localhost:5001/Edit/1` のようにします。</span><span class="sxs-lookup"><span data-stu-id="dba02-522">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="dba02-523">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-523">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="dba02-524">タグ ヘルパーは `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` によって有効になります</span><span class="sxs-lookup"><span data-stu-id="dba02-524">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="dba02-525">*Pages/Edit.cshtml* ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-525">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="dba02-526">最初の行には `@page "{id:int}"` ディレクティブが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-526">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="dba02-527">ルーティングの制約 `"{id:int}"` は、`int` ルート データを含むページへの要求を受け入れるようにページに指示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-527">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="dba02-528">ページへの要求に `int` に変換できるルート データが含まれていない場合は、ランタイムで HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-528">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="dba02-529">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-529">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="dba02-530">*Pages/Edit.cshtml.cs* ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-530">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="dba02-531">*Index.cshtml* ファイルには、各顧客の連絡先の削除ボタンを作成するマークアップも含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-531">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="dba02-532">HTML で削除ボタンがレンダリングされる場合、その `formaction` には次のパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-532">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="dba02-533">`asp-route-id` 属性によって指定された顧客の連絡先 ID。</span><span class="sxs-lookup"><span data-stu-id="dba02-533">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="dba02-534">`asp-page-handler` 属性によって指定された `handler`。</span><span class="sxs-lookup"><span data-stu-id="dba02-534">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="dba02-535">顧客の連絡先 ID `1` でレンダリングされた削除ボタンの例を示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-535">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="dba02-536">ボタンが選択されると、フォームの `POST` 要求がサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-536">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="dba02-537">慣例により、ハンドラー メソッドの名前はスキーム `OnPost[handler]Async` に従った `handler` パラメーターの値に基づいて選択されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-537">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="dba02-538">この例では `handler` が `delete` であるため、`OnPostDeleteAsync` ハンドラー メソッドを使用して `POST` 要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-538">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="dba02-539">`asp-page-handler` が `remove` などの別の値に設定されている場合、名前が `OnPostRemoveAsync` のハンドラー メソッドが選択されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-539">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="dba02-540">次のコードは、`OnPostDeleteAsync` ハンドラーを示しています。</span><span class="sxs-lookup"><span data-stu-id="dba02-540">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="dba02-541">`OnPostDeleteAsync` メソッド:</span><span class="sxs-lookup"><span data-stu-id="dba02-541">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="dba02-542">クエリ文字列から `id` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="dba02-542">Accepts the `id` from the query string.</span></span> <span data-ttu-id="dba02-543">*Index.cshtml* ページ ディレクティブにルーティング制約 `"{id:int?}"` が含まれていた場合、`id` はルート データから取得されることがあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-543">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="dba02-544">`id` のルート データは `https://localhost:5001/Customers/2` のように URI で指定されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-544">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="dba02-545">`FindAsync` を使用してデータベースから顧客の連絡先を照会します。</span><span class="sxs-lookup"><span data-stu-id="dba02-545">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="dba02-546">顧客の連絡先が見つかった場合、その連絡先は顧客の連絡先の一覧から削除されています。</span><span class="sxs-lookup"><span data-stu-id="dba02-546">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="dba02-547">データベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-547">The database is updated.</span></span>
* <span data-ttu-id="dba02-548">ルート インデックス ページ (`/Index`) にリダイレクトされるように、`RedirectToPage` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dba02-548">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="dba02-549">必要に応じてページのプロパティをマークする</span><span class="sxs-lookup"><span data-stu-id="dba02-549">Mark page properties as required</span></span>

<span data-ttu-id="dba02-550">`PageModel` 上のプロパティは、[必須](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)属性を使ってマークできます。</span><span class="sxs-lookup"><span data-stu-id="dba02-550">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="dba02-551">詳細については、[モデルの検証](xref:mvc/models/validation)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-551">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="dba02-552">OnGet ハンドラー フォールバックを使用した HEAD 要求の処理</span><span class="sxs-lookup"><span data-stu-id="dba02-552">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="dba02-553">`HEAD`HEAD 要求を使用すると、特定のリソースに対するヘッダーを取得できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-553">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="dba02-554">`GET` 要求とは異なり、`HEAD` 要求から応答本文は返されません。</span><span class="sxs-lookup"><span data-stu-id="dba02-554">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="dba02-555">通常、`HEAD` 要求に対して `OnHead` ハンドラーが作成され、呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-555">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="dba02-556">ASP.NET Core 2.1 以降では、`OnHead` ハンドラーが定義されていない場合、Razor ページは `OnGet` ハンドラーの呼び出しにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="dba02-556">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="dba02-557">この動作は、`Startup.ConfigureServices` での [SetCompatibilityVersion](xref:mvc/compatibility-version) への呼び出しによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-557">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="dba02-558">既定のテンプレートでは、ASP.NET Core 2.1 および 2.2 で `SetCompatibilityVersion` の呼び出しが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-558">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="dba02-559">`SetCompatibilityVersion` は実質的に Razor ページのオプション `AllowMappingHeadRequestsToGetHandler` を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-559">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="dba02-560">`SetCompatibilityVersion` とのすべての動作にオプトインするのではなく、明示的に *特定の* 動作にオプトインすることもできます。</span><span class="sxs-lookup"><span data-stu-id="dba02-560">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="dba02-561">次のコードでは、`OnGet` ハンドラーに `HEAD` 要求をマップできるようにすることにオプトインしています。</span><span class="sxs-lookup"><span data-stu-id="dba02-561">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="dba02-562">XSRF/CSRF および Razor ページ</span><span class="sxs-lookup"><span data-stu-id="dba02-562">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="dba02-563">[偽造防止検証](xref:security/anti-request-forgery)のためにコードを記述する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dba02-563">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="dba02-564">偽造防止トークンの生成と検証は、自動的に Razor ページに含まれます。</span><span class="sxs-lookup"><span data-stu-id="dba02-564">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="dba02-565">Razor ページでのレイアウト、パーシャル、テンプレート、およびタグ ヘルパーの使用</span><span class="sxs-lookup"><span data-stu-id="dba02-565">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="dba02-566">ページは、Razor ビュー エンジンのすべての機能で動作します。</span><span class="sxs-lookup"><span data-stu-id="dba02-566">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="dba02-567">レイアウト、パーシャル、テンプレート、タグ ヘルパー、" *_ViewStart.cshtml*"、" *_ViewImports.cshtml*" は、従来の Razor ビューと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="dba02-567">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="dba02-568">これらの機能の一部を利用してこのページをまとめてみましょう。</span><span class="sxs-lookup"><span data-stu-id="dba02-568">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="dba02-569">[レイアウト ページ](xref:mvc/views/layout)を *Pages/Shared/_Layout.cshtml* に追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-569">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="dba02-570">[レイアウト](xref:mvc/views/layout)は次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="dba02-570">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="dba02-571">(ページでレイアウトを止めない限り) 各ページのレイアウトを制御します。</span><span class="sxs-lookup"><span data-stu-id="dba02-571">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="dba02-572">JavaScript やスタイルシートなどの HTML 構造をインポートします。</span><span class="sxs-lookup"><span data-stu-id="dba02-572">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="dba02-573">詳細については、[レイアウトのページ](xref:mvc/views/layout)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-573">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="dba02-574">[Layout](xref:mvc/views/layout#specifying-a-layout) プロパティは *Pages/_ViewStart.cshtml* で設定されています。</span><span class="sxs-lookup"><span data-stu-id="dba02-574">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="dba02-575">レイアウトは、*Pages/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-575">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="dba02-576">ページは現在のページと同じフォルダーから開始して、階層的に他のビュー (レイアウト、テンプレート、パーシャル) を検索します。</span><span class="sxs-lookup"><span data-stu-id="dba02-576">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="dba02-577">"*Pages/Shared*" フォルダー内のレイアウトは、"*Pages*" フォルダー配下の任意の Razor ページから使用できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-577">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="dba02-578">レイアウト ファイルは *Pages/Shared* フォルダーに入ります。</span><span class="sxs-lookup"><span data-stu-id="dba02-578">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="dba02-579">レイアウト ファイルを *Views/Shared* フォルダー内に配置 **しない** ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dba02-579">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="dba02-580">*Views/Shared* は MVC ビュー パターンです。</span><span class="sxs-lookup"><span data-stu-id="dba02-580">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="dba02-581">Razor ページは、パス規則ではなく、フォルダー階層に依存することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="dba02-581">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="dba02-582">Razor ページからのビュー検索には、"*Pages*" フォルダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="dba02-582">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="dba02-583">MVC コントローラーで使用しているレイアウト、テンプレート、およびパーシャルと、従来の Razor ビューは "*機能します*"。</span><span class="sxs-lookup"><span data-stu-id="dba02-583">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="dba02-584">*Pages/_ViewImports.cshtml* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-584">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="dba02-585">`@namespace` はこのチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="dba02-585">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="dba02-586">`@addTagHelper` ディレクティブにより、[組み込みタグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/Index)が *Pages* フォルダー内のすべてのページにもたらされます。</span><span class="sxs-lookup"><span data-stu-id="dba02-586">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="dba02-587">ページで `@namespace` ディレクティブが明示的に使用されている場合:</span><span class="sxs-lookup"><span data-stu-id="dba02-587">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="dba02-588">ディレクティブは、ページの名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-588">The directive sets the namespace for the page.</span></span> <span data-ttu-id="dba02-589">`@model` ディレクティブには、名前空間を含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dba02-589">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="dba02-590">`@namespace` ディレクティブが *_ViewImports.cshtml* に含まれていると、指定した名前空間が `@namespace` ディレクティブをインポートするページで生成された名前空間のプレフィックスを提供します。</span><span class="sxs-lookup"><span data-stu-id="dba02-590">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="dba02-591">生成された名前空間の残りの部分 (サフィックスの部分) は、 *_ViewImports.cshtml* を含むフォルダーとページを含むフォルダー間のドットで区切られた相対パスです。</span><span class="sxs-lookup"><span data-stu-id="dba02-591">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="dba02-592">たとえば、`PageModel` クラス *Pages/Customers/Edit.cshtml.cs* は名前空間を明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-592">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="dba02-593">*Pages/_ViewImports.cshtml* ファイルは次の名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-593">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="dba02-594">"*Pages/Customers/Edit.cshtml*" Razor ページの生成された名前空間は、`PageModel` クラスと同じです。</span><span class="sxs-lookup"><span data-stu-id="dba02-594">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="dba02-595">`@namespace` は "*従来の Razor ビューでも機能します。* "</span><span class="sxs-lookup"><span data-stu-id="dba02-595">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="dba02-596">元の *Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-596">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="dba02-597">更新された *Pages/Create.cshtml* ビュー ファイル:</span><span class="sxs-lookup"><span data-stu-id="dba02-597">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="dba02-598">[Razor ページのスタート プロジェクト](#rpvs17)には、クライアント側の検証をフックする "*Pages/_ValidationScriptsPartial.cshtml*" が含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-598">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="dba02-599">部分ビューの詳細については、「<xref:mvc/views/partial>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-599">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="dba02-600">ページの URL の生成</span><span class="sxs-lookup"><span data-stu-id="dba02-600">URL generation for Pages</span></span>

<span data-ttu-id="dba02-601">上に示した `Create` ページでは、`RedirectToPage` を使用します。</span><span class="sxs-lookup"><span data-stu-id="dba02-601">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="dba02-602">アプリには次のファイル/フォルダー構造があります。</span><span class="sxs-lookup"><span data-stu-id="dba02-602">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="dba02-603">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="dba02-603">*/Pages*</span></span>

  * <span data-ttu-id="dba02-604">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-604">*Index.cshtml*</span></span>
  * <span data-ttu-id="dba02-605">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="dba02-605">*/Customers*</span></span>

    * <span data-ttu-id="dba02-606">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-606">*Create.cshtml*</span></span>
    * <span data-ttu-id="dba02-607">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-607">*Edit.cshtml*</span></span>
    * <span data-ttu-id="dba02-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="dba02-608">*Index.cshtml*</span></span>

<span data-ttu-id="dba02-609">成功すると、*Pages/Customers/Create.cshtml* ページと *Pages/Customers/Edit.cshtml* ページが *Pages/Index.cshtml* にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="dba02-609">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="dba02-610">文字列 `/Index` は前のページにアクセスするための URI の一部です。</span><span class="sxs-lookup"><span data-stu-id="dba02-610">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="dba02-611">文字列 `/Index` は、*Pages/Index.cshtml* ページへの URI を生成するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-611">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="dba02-612">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-612">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="dba02-613">ページ名は、先頭の `/` を含む、ルート */Pages* フォルダーからページへのパスです (たとえば `/Index`)。</span><span class="sxs-lookup"><span data-stu-id="dba02-613">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="dba02-614">先述の URL 生成サンプルでは、URL のハードコーディングに関する拡張オプションと機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-614">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="dba02-615">URL の生成は[ルーティング](xref:mvc/controllers/routing)を使用し、ターゲット パスで定義されたルート方法に従って、パラメーターの生成とエンコードができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-615">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="dba02-616">ページの URL 生成は、相対名をサポートします。</span><span class="sxs-lookup"><span data-stu-id="dba02-616">URL generation for pages supports relative names.</span></span> <span data-ttu-id="dba02-617">次の表に、*Pages/Customers/Create.cshtml* の異なる `RedirectToPage` パラメーターで選択されたインデックス ページを示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-617">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="dba02-618">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="dba02-618">RedirectToPage(x)</span></span>| <span data-ttu-id="dba02-619">ページ</span><span class="sxs-lookup"><span data-stu-id="dba02-619">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="dba02-620">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="dba02-620">RedirectToPage("/Index")</span></span> | <span data-ttu-id="dba02-621">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="dba02-621">*Pages/Index*</span></span> |
| <span data-ttu-id="dba02-622">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="dba02-622">RedirectToPage("./Index");</span></span> | <span data-ttu-id="dba02-623">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="dba02-623">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="dba02-624">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="dba02-624">RedirectToPage("../Index")</span></span> | <span data-ttu-id="dba02-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="dba02-625">*Pages/Index*</span></span> |
| <span data-ttu-id="dba02-626">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="dba02-626">RedirectToPage("Index")</span></span>  | <span data-ttu-id="dba02-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="dba02-627">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="dba02-628">`RedirectToPage("Index")`、`RedirectToPage("./Index")`、および `RedirectToPage("../Index")` は *相対名* です。</span><span class="sxs-lookup"><span data-stu-id="dba02-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="dba02-629">`RedirectToPage` パラメーターは現在のページのパスと *組み合わされて*、ターゲット ページの名前を計算します。</span><span class="sxs-lookup"><span data-stu-id="dba02-629">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="dba02-630">相対名のリンクは、複雑な構造を持つサイトを構築する際に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="dba02-630">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="dba02-631">相対名を使用してフォルダー内のページ間をリンクする場合、そのフォルダー名を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-631">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="dba02-632">すべてのリンクは引き続き機能します (リンクにはフォルダー名が含まれていないため)。</span><span class="sxs-lookup"><span data-stu-id="dba02-632">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="dba02-633">別の [[区分]](xref:mvc/controllers/areas) のページにリダイレクトするには、その区分を指定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-633">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="dba02-634">詳細については、「<xref:mvc/controllers/areas>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-634">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="dba02-635">ViewData 属性</span><span class="sxs-lookup"><span data-stu-id="dba02-635">ViewData attribute</span></span>

<span data-ttu-id="dba02-636">データは [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) とのページに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-636">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="dba02-637">`[ViewData]` 属性を持つコントローラーまたは Razor ページのモデルのプロパティの値は、[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) に格納してそこから読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-637">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="dba02-638">次の例では、`AboutModel` に `[ViewData]` でマークされた `Title` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dba02-638">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="dba02-639">`Title` プロパティは、[About] ページのタイトルに設定されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-639">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="dba02-640">[About] ページでは、モデル プロパティとして `Title` プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="dba02-640">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="dba02-641">レイアウトでは、タイトルは ViewData ディクショナリから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="dba02-641">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="dba02-642">TempData</span><span class="sxs-lookup"><span data-stu-id="dba02-642">TempData</span></span>

<span data-ttu-id="dba02-643">ASP.NET Core は [コントローラー](/dotnet/api/microsoft.aspnetcore.mvc.controller)上で [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="dba02-643">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="dba02-644">このプロパティは、読み取られるまでデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="dba02-644">This property stores data until it's read.</span></span> <span data-ttu-id="dba02-645">`Keep` メソッドと `Peek` メソッドは、削除せずにデータを確認するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="dba02-645">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="dba02-646">`TempData` は、複数の要求にデータが必要な場合のリダイレクトに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="dba02-646">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="dba02-647">次のコードは、`TempData` を使用して `Message` の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-647">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="dba02-648">*Pages/Customers/Index.cshtml* ファイル内の次のマークアップは、`TempData` を使用して `Message` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="dba02-648">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="dba02-649">*Pages/Customers/Index.cshtml.cs* ページは、`[TempData]` 属性を `Message` プロパティに適用します。</span><span class="sxs-lookup"><span data-stu-id="dba02-649">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="dba02-650">詳細については、「[TempData](xref:fundamentals/app-state#tempdata)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba02-650">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="dba02-651">ページあたり複数のハンドラー</span><span class="sxs-lookup"><span data-stu-id="dba02-651">Multiple handlers per page</span></span>

<span data-ttu-id="dba02-652">次のページでは、`asp-page-handler` タグ ヘルパーを使用して 2 つのハンドラーにマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-652">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="dba02-653">前の例のフォームには、それぞれが `FormActionTagHelper` を使用して異なる URL に送信する 2 つの送信ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="dba02-653">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="dba02-654">`asp-page-handler` 属性は、`asp-page` のコンパニオンです。</span><span class="sxs-lookup"><span data-stu-id="dba02-654">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="dba02-655">`asp-page-handler` はページごとに定義されている各ハンドラー メソッドに送信する URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="dba02-655">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="dba02-656">サンプルは現在のページにリンクしているため、`asp-page` は指定されません。</span><span class="sxs-lookup"><span data-stu-id="dba02-656">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="dba02-657">ページ モデル:</span><span class="sxs-lookup"><span data-stu-id="dba02-657">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="dba02-658">上記のコードは、*名前付きハンドラー メソッド* を使用しています。</span><span class="sxs-lookup"><span data-stu-id="dba02-658">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="dba02-659">名前付きハンドラー メソッドは、名前の `On<HTTP Verb>` の後および `Async` の前 (ある場合) のテキストを取得して作成されます。</span><span class="sxs-lookup"><span data-stu-id="dba02-659">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="dba02-660">前の例では、ページ メソッドは OnPost **JoinList** Async と OnPost **JoinListUC** Async です。</span><span class="sxs-lookup"><span data-stu-id="dba02-660">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="dba02-661">*OnPost* と *Async* を削除すると、ハンドラー名は `JoinList` と `JoinListUC` になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-661">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="dba02-662">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-662">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="dba02-663">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="dba02-663">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="dba02-664">カスタム ルート</span><span class="sxs-lookup"><span data-stu-id="dba02-664">Custom routes</span></span>

<span data-ttu-id="dba02-665">`@page` ディレクティブを次に使用します:</span><span class="sxs-lookup"><span data-stu-id="dba02-665">Use the `@page` directive to:</span></span>

* <span data-ttu-id="dba02-666">カスタム ルートをページに指定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-666">Specify a custom route to a page.</span></span> <span data-ttu-id="dba02-667">たとえば、[バージョン情報] ページへのルートを `@page "/Some/Other/Path"` を使用して `/Some/Other/Path` に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-667">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="dba02-668">ページの既定のルートにセグメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-668">Append segments to a page's default route.</span></span> <span data-ttu-id="dba02-669">たとえば、"item" セグメントを `@page "item"` を使用してページの既定のルートに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-669">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="dba02-670">ページの既定のルートにパラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-670">Append parameters to a page's default route.</span></span> <span data-ttu-id="dba02-671">たとえば、`@page "{id}"` を含むページに ID パラメーター `id` を必須とすることができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-671">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="dba02-672">パスの先頭のチルダ (`~`) によって指定されたルートの相対パスがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="dba02-672">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="dba02-673">たとえば、`@page "~/Some/Other/Path"` は `@page "/Some/Other/Path"` と同じです。</span><span class="sxs-lookup"><span data-stu-id="dba02-673">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="dba02-674">URL 内のクエリ文字列 `?handler=JoinList` が気に入らない場合は、ルートを変更して URL のパス部分にハンドラー名を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-674">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="dba02-675">`@page` ディレクティブの後に二重引用符で囲んだルート テンプレートを追加して、ルートをカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-675">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="dba02-676">上記のコードを使用すると、`OnPostJoinListAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinList` になります。</span><span class="sxs-lookup"><span data-stu-id="dba02-676">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="dba02-677">`OnPostJoinListUCAsync` に送信される URL パスは `https://localhost:5001/Customers/CreateFATH/JoinListUC` です。</span><span class="sxs-lookup"><span data-stu-id="dba02-677">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="dba02-678">`handler` の後の `?` は、ルート パラメーターが省略可能なことを意味します。</span><span class="sxs-lookup"><span data-stu-id="dba02-678">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="dba02-679">構成と設定</span><span class="sxs-lookup"><span data-stu-id="dba02-679">Configuration and settings</span></span>

<span data-ttu-id="dba02-680">高度なオプションを構成するには、MVC ビルダーで拡張メソッド `AddRazorPagesOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="dba02-680">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="dba02-681">現在は、`RazorPagesOptions` を使用してページのルート ディレクトリを設定したり、ページのアプリケーション モデルの規則を追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="dba02-681">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="dba02-682">将来、この方法でより多くの機能拡張を可能にしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="dba02-682">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="dba02-683">ビューをプリコンパイルするには、[Razor ビューのコンパイル](xref:mvc/views/view-compilation)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dba02-683">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="dba02-684">[サンプル コードをダウンロードまたは表示します](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample)。</span><span class="sxs-lookup"><span data-stu-id="dba02-684">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="dba02-685">この概要に基づく、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するページをご覧ください</span><span class="sxs-lookup"><span data-stu-id="dba02-685">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="dba02-686">Razor Pages をコンテンツのルートに指定する</span><span class="sxs-lookup"><span data-stu-id="dba02-686">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="dba02-687">Razor Pages のルートは既定で */Pages* ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="dba02-687">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="dba02-688">[WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) を [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) に追加して、Razor Pages をアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) に置くように指定します。</span><span class="sxs-lookup"><span data-stu-id="dba02-688">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="dba02-689">Razor Pages をカスタム ルート ディレクトリに指定する</span><span class="sxs-lookup"><span data-stu-id="dba02-689">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="dba02-690">(相対パスを指定して) Razor Pages をアプリのカスタム ルート ディレクトリに指定するには、[WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) を [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) に追加します。</span><span class="sxs-lookup"><span data-stu-id="dba02-690">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="dba02-691">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dba02-691">Additional resources</span></span>

* [<span data-ttu-id="dba02-692">属性と Razor ページを承認する</span><span class="sxs-lookup"><span data-stu-id="dba02-692">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end

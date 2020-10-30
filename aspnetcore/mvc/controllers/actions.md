---
title: ASP.NET Core MVC でコントローラーで要求を処理する
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/actions
ms.openlocfilehash: a9319e74d0213b178c2a71be69a0332270d9446c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061458"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a><span data-ttu-id="8ac22-102">ASP.NET Core MVC でコントローラーで要求を処理する</span><span class="sxs-lookup"><span data-stu-id="8ac22-102">Handle requests with controllers in ASP.NET Core MVC</span></span>

<span data-ttu-id="8ac22-103">作成者: [Steve Smith](https://ardalis.com/)、[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="8ac22-103">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="8ac22-104">コントローラー、アクション、アクションの結果は、開発者が ASP.NET Core MVC を利用してアプリをビルドするときの基本的な部分です。</span><span class="sxs-lookup"><span data-stu-id="8ac22-104">Controllers, actions, and action results are a fundamental part of how developers build apps using ASP.NET Core MVC.</span></span>

## <a name="what-is-a-controller"></a><span data-ttu-id="8ac22-105">コントローラーとは何か。</span><span class="sxs-lookup"><span data-stu-id="8ac22-105">What is a Controller?</span></span>

<span data-ttu-id="8ac22-106">コントローラーは一連のアクションを定義し、グループ化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-106">A controller is used to define and group a set of actions.</span></span> <span data-ttu-id="8ac22-107">アクション (または *アクション メソッド* ) は、要求を処理するコントローラーのメソッドです。</span><span class="sxs-lookup"><span data-stu-id="8ac22-107">An action (or *action method* ) is a method on a controller which handles requests.</span></span> <span data-ttu-id="8ac22-108">コントローラーは、同様のアクションを論理的にグループ化します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-108">Controllers logically group similar actions together.</span></span> <span data-ttu-id="8ac22-109">アクションをこのように集めることで、ルーティング、キャッシュ、承認など、一連の共通ルールをまとめて適用できます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-109">This aggregation of actions allows common sets of rules, such as routing, caching, and authorization, to be applied collectively.</span></span> <span data-ttu-id="8ac22-110">要求は[ルーティング](xref:mvc/controllers/routing)を介してアクションにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-110">Requests are mapped to actions through [routing](xref:mvc/controllers/routing).</span></span>

<span data-ttu-id="8ac22-111">コントローラー クラスは慣例で</span><span class="sxs-lookup"><span data-stu-id="8ac22-111">By convention, controller classes:</span></span>

* <span data-ttu-id="8ac22-112">プロジェクトのルートレベル *[コントローラー]* フォルダーに置かれます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-112">Reside in the project's root-level *Controllers* folder.</span></span>
* <span data-ttu-id="8ac22-113">`Microsoft.AspNetCore.Mvc.Controller` から継承します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-113">Inherit from `Microsoft.AspNetCore.Mvc.Controller`.</span></span>

<span data-ttu-id="8ac22-114">コントローラーはインスタンス化が可能なクラスであり、次の条件の少なくとも 1 つが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-114">A controller is an instantiable class in which at least one of the following conditions is true:</span></span>

* <span data-ttu-id="8ac22-115">クラス名に `Controller` というサフィックスが付く。</span><span class="sxs-lookup"><span data-stu-id="8ac22-115">The class name is suffixed with `Controller`.</span></span>
* <span data-ttu-id="8ac22-116">クラスが名前に `Controller` というサフィックスが付くクラスから継承する。</span><span class="sxs-lookup"><span data-stu-id="8ac22-116">The class inherits from a class whose name is suffixed with `Controller`.</span></span>
* <span data-ttu-id="8ac22-117">`[Controller]` 属性がクラスに適用される。</span><span class="sxs-lookup"><span data-stu-id="8ac22-117">The `[Controller]` attribute is applied to the class.</span></span>

<span data-ttu-id="8ac22-118">コントローラー クラスには、`[NonController]` 属性を関連付けないでください。</span><span class="sxs-lookup"><span data-stu-id="8ac22-118">A controller class must not have an associated `[NonController]` attribute.</span></span>

<span data-ttu-id="8ac22-119">コントローラーは [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-119">Controllers should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span> <span data-ttu-id="8ac22-120">この原則を実装するための手法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-120">There are a couple of approaches to implementing this principle.</span></span> <span data-ttu-id="8ac22-121">複数のコントローラー アクションに同じサービスが必要な場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)で依存関係を要求する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="8ac22-121">If multiple controller actions require the same service, consider using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to request those dependencies.</span></span> <span data-ttu-id="8ac22-122">サービスを必要とするアクション メソッドが 1 つだけの場合、[アクションの挿入](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)で依存関係を要求する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="8ac22-122">If the service is needed by only a single action method, consider using [Action Injection](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) to request the dependency.</span></span>

<span data-ttu-id="8ac22-123">**M** odel- **V** iew- **C** ontroller パターン内では、コントローラーは要求の初回処理とモデルのインスタンス化を担います。</span><span class="sxs-lookup"><span data-stu-id="8ac22-123">Within the **M** odel- **V** iew- **C** ontroller pattern, a controller is responsible for the initial processing of the request and instantiation of the model.</span></span> <span data-ttu-id="8ac22-124">一般的に、ビジネスの決定はモデル内で実行するべきです。</span><span class="sxs-lookup"><span data-stu-id="8ac22-124">Generally, business decisions should be performed within the model.</span></span>

<span data-ttu-id="8ac22-125">コントローラーはモデルの処理の結果を受け取り (結果があれば)、適切なビューとそれに関連付けられるビュー データを返すか、API 呼び出しの結果を返します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-125">The controller takes the result of the model's processing (if any) and returns either the proper view and its associated view data or the result of the API call.</span></span> <span data-ttu-id="8ac22-126">詳細については、「[Overview of ASP.NET Core MVC](xref:mvc/overview)」(ASP.NET Core MVC の概要) と「[ASP.NET Core MVC と Visual Studio の概要](xref:tutorials/first-mvc-app/start-mvc)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ac22-126">Learn more at [Overview of ASP.NET Core MVC](xref:mvc/overview) and [Get started with ASP.NET Core MVC and Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="8ac22-127">コントローラーは *UI レベル* の抽象化です。</span><span class="sxs-lookup"><span data-stu-id="8ac22-127">The controller is a *UI-level* abstraction.</span></span> <span data-ttu-id="8ac22-128">その役目は、要求データの有効性を確保することと返すビュー (あるいは、API の結果) を選択することです。</span><span class="sxs-lookup"><span data-stu-id="8ac22-128">Its responsibilities are to ensure request data is valid and to choose which view (or result for an API) should be returned.</span></span> <span data-ttu-id="8ac22-129">要素が十分に考慮されたアプリの場合、データ アクセスやビジネス ロジックは直接含まれません。</span><span class="sxs-lookup"><span data-stu-id="8ac22-129">In well-factored apps, it doesn't directly include data access or business logic.</span></span> <span data-ttu-id="8ac22-130">そうではなく、コントローラーはサービスにそのような役目を委任します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-130">Instead, the controller delegates to services handling these responsibilities.</span></span>

## <a name="defining-actions"></a><span data-ttu-id="8ac22-131">アクションの定義</span><span class="sxs-lookup"><span data-stu-id="8ac22-131">Defining Actions</span></span>

<span data-ttu-id="8ac22-132">`[NonAction]` 属性が設定されているものを除き、コントローラーのパブリック メソッドはアクションです。</span><span class="sxs-lookup"><span data-stu-id="8ac22-132">Public methods on a controller, except those with the `[NonAction]` attribute, are actions.</span></span> <span data-ttu-id="8ac22-133">アクションのパラメーターは要求データにバインドされ、[モデル バインド](xref:mvc/models/model-binding)により検証されます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-133">Parameters on actions are bound to request data and are validated using [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="8ac22-134">モデル検証は、モデル バインドされているすべてに実行されます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-134">Model validation occurs for everything that's model-bound.</span></span> <span data-ttu-id="8ac22-135">プロパティ値 `ModelState.IsValid` は、モデルのバインドと検証に成功したかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-135">The `ModelState.IsValid` property value indicates whether model binding and validation succeeded.</span></span>

<span data-ttu-id="8ac22-136">アクション メソッドには、要求をビジネスにマッピングするためのロジックを含めてください。</span><span class="sxs-lookup"><span data-stu-id="8ac22-136">Action methods should contain logic for mapping a request to a business concern.</span></span> <span data-ttu-id="8ac22-137">ビジネスは通常、コントローラーが[依存関係挿入](xref:mvc/controllers/dependency-injection)を経由してアクセスするサービスとして表されます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-137">Business concerns should typically be represented as services that the controller accesses through [dependency injection](xref:mvc/controllers/dependency-injection).</span></span> <span data-ttu-id="8ac22-138">アクションはビジネス アクションの結果をアプリケーションの状態にマッピングします。</span><span class="sxs-lookup"><span data-stu-id="8ac22-138">Actions then map the result of the business action to an application state.</span></span>

<span data-ttu-id="8ac22-139">アクションは何でも返すことができますが、多くの場合、応答を生成する `IActionResult` のインスタンス (あるいは、非同期メソッドの `Task<IActionResult>`) を返します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-139">Actions can return anything, but frequently return an instance of `IActionResult` (or `Task<IActionResult>` for async methods) that produces a response.</span></span> <span data-ttu-id="8ac22-140">アクション メソッドは、 *応答の種類* の選択を担います。</span><span class="sxs-lookup"><span data-stu-id="8ac22-140">The action method is responsible for choosing *what kind of response* .</span></span> <span data-ttu-id="8ac22-141">アクションの結果は *応答を行いません* 。</span><span class="sxs-lookup"><span data-stu-id="8ac22-141">The action result *does the responding* .</span></span>

### <a name="controller-helper-methods"></a><span data-ttu-id="8ac22-142">コントローラー ヘルパー メソッド</span><span class="sxs-lookup"><span data-stu-id="8ac22-142">Controller Helper Methods</span></span>

<span data-ttu-id="8ac22-143">コントローラーは通常、[コントローラー](/dotnet/api/microsoft.aspnetcore.mvc.controller)から継承します。ただし、これは必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="8ac22-143">Controllers usually inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), although this isn't required.</span></span> <span data-ttu-id="8ac22-144">`Controller` から派生することで、次の 3 つのカテゴリのヘルパー メソッドにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-144">Deriving from `Controller` provides access to three categories of helper methods:</span></span>

#### <a name="1-methods-resulting-in-an-empty-response-body"></a><span data-ttu-id="8ac22-145">1. 空の応答本文が生成されたメソッド</span><span class="sxs-lookup"><span data-stu-id="8ac22-145">1. Methods resulting in an empty response body</span></span>

<span data-ttu-id="8ac22-146">`Content-Type` HTTP 応答ヘッダーが含まれません。応答の本文に記述するコンテンツがないためです。</span><span class="sxs-lookup"><span data-stu-id="8ac22-146">No `Content-Type` HTTP response header is included, since the response body lacks content to describe.</span></span>

<span data-ttu-id="8ac22-147">このカテゴリの中に、リダイレクトと HTTP 状態コードという 2 つの種類の結果があります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-147">There are two result types within this category: Redirect and HTTP Status Code.</span></span>

* <span data-ttu-id="8ac22-148">**HTTP 状態コード**</span><span class="sxs-lookup"><span data-stu-id="8ac22-148">**HTTP Status Code**</span></span>

    <span data-ttu-id="8ac22-149">この型は HTTP 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-149">This type returns an HTTP status code.</span></span> <span data-ttu-id="8ac22-150">この種類のヘルパー メソッドには、`BadRequest`、`NotFound`、`Ok` などがあります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-150">A couple of helper methods of this type are `BadRequest`, `NotFound`, and `Ok`.</span></span> <span data-ttu-id="8ac22-151">たとえば、`return BadRequest();` の場合、実行時に 400 状態コードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-151">For example, `return BadRequest();` produces a 400 status code when executed.</span></span> <span data-ttu-id="8ac22-152">`BadRequest`、`NotFound`、`Ok` などのメソッドがオーバーロードされると、HTTP ステータス コードのレスポンダーでなくなります。コンテンツ ネゴシエーションが発生するためです。</span><span class="sxs-lookup"><span data-stu-id="8ac22-152">When methods such as `BadRequest`, `NotFound`, and `Ok` are overloaded, they no longer qualify as HTTP Status Code responders, since content negotiation is taking place.</span></span>

* <span data-ttu-id="8ac22-153">**リダイレクト**</span><span class="sxs-lookup"><span data-stu-id="8ac22-153">**Redirect**</span></span>

    <span data-ttu-id="8ac22-154">この種類では、アクションまたは宛先にリダイレクトが返されます (`Redirect`、`LocalRedirect`、`RedirectToAction`、`RedirectToRoute` を利用して)。</span><span class="sxs-lookup"><span data-stu-id="8ac22-154">This type returns a redirect to an action or destination (using `Redirect`, `LocalRedirect`, `RedirectToAction`, or `RedirectToRoute`).</span></span> <span data-ttu-id="8ac22-155">たとえば、`return RedirectToAction("Complete", new {id = 123});` は `Complete` にリダイレクトし、匿名のオブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-155">For example, `return RedirectToAction("Complete", new {id = 123});` redirects to `Complete`, passing an anonymous object.</span></span>

    <span data-ttu-id="8ac22-156">リダイレクトの結果の種類は HTTP ステータス コードの種類とは、`Location` HTTP 応答ヘッダーを追加するという点で主に異なります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-156">The Redirect result type differs from the HTTP Status Code type primarily in the addition of a `Location` HTTP response header.</span></span>

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a><span data-ttu-id="8ac22-157">2. 定義済みのコンテンツタイプを持つ空でない応答本文が生成されるメソッド</span><span class="sxs-lookup"><span data-stu-id="8ac22-157">2. Methods resulting in a non-empty response body with a predefined content type</span></span>

<span data-ttu-id="8ac22-158">このカテゴリのヘルパー メソッドの多くには `ContentType` プロパティが含まれ、`Content-Type` 応答ヘッダーを設定し、応答本文を記述できます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-158">Most helper methods in this category include a `ContentType` property, allowing you to set the `Content-Type` response header to describe the response body.</span></span>

<span data-ttu-id="8ac22-159">このカテゴリには 2 種類の結果があります。[ビュー](xref:mvc/views/overview)と[書式設定された応答](xref:web-api/advanced/formatting)です。</span><span class="sxs-lookup"><span data-stu-id="8ac22-159">There are two result types within this category: [View](xref:mvc/views/overview) and [Formatted Response](xref:web-api/advanced/formatting).</span></span>

* <span data-ttu-id="8ac22-160">**表示**</span><span class="sxs-lookup"><span data-stu-id="8ac22-160">**View**</span></span>

    <span data-ttu-id="8ac22-161">この種類では、モデルを利用して HTML をレンダリングするビューが返されます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-161">This type returns a view which uses a model to render HTML.</span></span> <span data-ttu-id="8ac22-162">たとえば、`return View(customer);` はデータ バインディングのビューにモデルを渡します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-162">For example, `return View(customer);` passes a model to the view for data-binding.</span></span>

* <span data-ttu-id="8ac22-163">**書式設定された応答**</span><span class="sxs-lookup"><span data-stu-id="8ac22-163">**Formatted Response**</span></span>

    <span data-ttu-id="8ac22-164">この種類では、JSON または同様のデータ交換形式を返し、特定の手法でオブジェクトを表します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-164">This type returns JSON or a similar data exchange format to represent an object in a specific manner.</span></span> <span data-ttu-id="8ac22-165">たとえば、`return Json(customer);` の場合、与えられたオブジェクトを JSON 形式にシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-165">For example, `return Json(customer);` serializes the provided object into JSON format.</span></span>
    
    <span data-ttu-id="8ac22-166">この種類の一般的なメソッドには他に `File`、`PhysicalFile` などがあります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-166">Other common methods of this type include `File` and `PhysicalFile`.</span></span> <span data-ttu-id="8ac22-167">たとえば、`return PhysicalFile(customerFilePath, "text/xml");` は [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult) を返します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-167">For example, `return PhysicalFile(customerFilePath, "text/xml");` returns [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).</span></span>

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a><span data-ttu-id="8ac22-168">3. クライアントとネゴシエートされるコンテンツの種類で書式設定された、空でない応答本文が生成されるメソッド</span><span class="sxs-lookup"><span data-stu-id="8ac22-168">3. Methods resulting in a non-empty response body formatted in a content type negotiated with the client</span></span>

<span data-ttu-id="8ac22-169">このカテゴリは、 **コンテンツ ネゴシエーション** として知られています。</span><span class="sxs-lookup"><span data-stu-id="8ac22-169">This category is better known as **Content Negotiation** .</span></span> <span data-ttu-id="8ac22-170">[コンテンツ ネゴシエーション](xref:web-api/advanced/formatting#content-negotiation)は、アクションが [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) 型を返すか、[IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) 実装以外の何かを返すときに適用されます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-170">[Content negotiation](xref:web-api/advanced/formatting#content-negotiation) applies whenever an action returns an [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) type or something other than an [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) implementation.</span></span> <span data-ttu-id="8ac22-171">非 `IActionResult` の実装 (`object` など) を返すアクションは、書式設定された応答も返します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-171">An action that returns a non-`IActionResult` implementation (for example, `object`) also returns a Formatted Response.</span></span>

<span data-ttu-id="8ac22-172">この種類のヘルパー メソッドには `BadRequest`、`CreatedAtRoute`、`Ok` などがあります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-172">Some helper methods of this type include `BadRequest`, `CreatedAtRoute`, and `Ok`.</span></span> <span data-ttu-id="8ac22-173">このようなメソッドの例として、それぞれ `return BadRequest(modelState);`、`return CreatedAtRoute("routename", values, newobject);`、`return Ok(value);` があります。</span><span class="sxs-lookup"><span data-stu-id="8ac22-173">Examples of these methods include `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, and `return Ok(value);`, respectively.</span></span> <span data-ttu-id="8ac22-174">`BadRequest` と `Ok` は、値が渡されたときにのみ、コンテンツ ネゴシエーションを実行します。値が渡されなければ、代わりに HTTP ステータス コードという結果の種類としてサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-174">Note that `BadRequest` and `Ok` perform content negotiation only when passed a value; without being passed a value, they instead serve as HTTP Status Code result types.</span></span> <span data-ttu-id="8ac22-175">一方、`CreatedAtRoute` メソッドは常にコンテンツ ネゴシエーションを実行します。そのあらゆるオーバーロードにおいて、値を渡すことが必要になるためです。</span><span class="sxs-lookup"><span data-stu-id="8ac22-175">The `CreatedAtRoute` method, on the other hand, always performs content negotiation since its overloads all require that a value be passed.</span></span>

### <a name="cross-cutting-concerns"></a><span data-ttu-id="8ac22-176">横断的な問題</span><span class="sxs-lookup"><span data-stu-id="8ac22-176">Cross-Cutting Concerns</span></span>

<span data-ttu-id="8ac22-177">アプリケーションは通常、ワークフローの一部を共有します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-177">Applications typically share parts of their workflow.</span></span> <span data-ttu-id="8ac22-178">たとえば、ショッピング カートにアクセスする際、認証を要求するアプリや一部のページでデータをキャッシュするアプリです。</span><span class="sxs-lookup"><span data-stu-id="8ac22-178">Examples include an app that requires authentication to access the shopping cart, or an app that caches data on some pages.</span></span> <span data-ttu-id="8ac22-179">アクション メソッドの前または後でロジックを実行するには、 *フィルター* を使用します。</span><span class="sxs-lookup"><span data-stu-id="8ac22-179">To perform logic before or after an action method, use a *filter* .</span></span> <span data-ttu-id="8ac22-180">横断的な問題に対して[フィルター](xref:mvc/controllers/filters)を使うと、重複を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-180">Using [Filters](xref:mvc/controllers/filters) on cross-cutting concerns can reduce duplication.</span></span>

<span data-ttu-id="8ac22-181">`[Authorize]` など、フィルター属性の多くは、求められる詳細度に基づき、コントローラーまたはアクション レベルで適用できます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-181">Most filter attributes, such as `[Authorize]`, can be applied at the controller or action level depending upon the desired level of granularity.</span></span>

<span data-ttu-id="8ac22-182">エラー処理と応答キャッシュは多くの場合、横断的な問題です。</span><span class="sxs-lookup"><span data-stu-id="8ac22-182">Error handling and response caching are often cross-cutting concerns:</span></span>
* [<span data-ttu-id="8ac22-183">エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="8ac22-183">Handle errors</span></span>](xref:mvc/controllers/filters#exception-filters)
* [<span data-ttu-id="8ac22-184">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="8ac22-184">Response Caching</span></span>](xref:performance/caching/response)

<span data-ttu-id="8ac22-185">横断的な問題の多くはフィルターやカスタム [ミドルウェア](xref:fundamentals/middleware/index)の利用で処理できます。</span><span class="sxs-lookup"><span data-stu-id="8ac22-185">Many cross-cutting concerns can be handled using filters or custom [middleware](xref:fundamentals/middleware/index).</span></span>

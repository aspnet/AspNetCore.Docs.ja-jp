---
title: ASP.NET Core でのモデル バインド
author: rick-anderson
description: ASP.NET Core でのモデル バインドのしくみと、その動作のカスタマイズ方法について説明します。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: a3be22134246c76b0a809ddb97b33ff97ace9a5b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057506"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="b999d-103">ASP.NET Core でのモデル バインド</span><span class="sxs-lookup"><span data-stu-id="b999d-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b999d-104">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="b999d-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="b999d-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b999d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="b999d-106">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="b999d-106">What is Model binding</span></span>

<span data-ttu-id="b999d-107">コントローラーと :::no-loc(Razor)::: ページは、HTTP 要求から取得されたデータを処理します。</span><span class="sxs-lookup"><span data-stu-id="b999d-107">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="b999d-108">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="b999d-109">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="b999d-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="b999d-110">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-110">Model binding automates this process.</span></span> <span data-ttu-id="b999d-111">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="b999d-111">The model binding system:</span></span>

* <span data-ttu-id="b999d-112">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="b999d-113">:::no-loc(Razor):::メソッドパラメーターおよびパブリックプロパティのデータをコントローラーおよびページに提供します。</span><span class="sxs-lookup"><span data-stu-id="b999d-113">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="b999d-114">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="b999d-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="b999d-115">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="b999d-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="b999d-116">例</span><span class="sxs-lookup"><span data-stu-id="b999d-116">Example</span></span>

<span data-ttu-id="b999d-117">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="b999d-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="b999d-118">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="b999d-119">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="b999d-120">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="b999d-121">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="b999d-122">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="b999d-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="b999d-123">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="b999d-124">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="b999d-125">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="b999d-126">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="b999d-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="b999d-127">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="b999d-128">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="b999d-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="b999d-129">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="b999d-130">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="b999d-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="b999d-131">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="b999d-132">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="b999d-133">対象サーバー</span><span class="sxs-lookup"><span data-stu-id="b999d-133">Targets</span></span>

<span data-ttu-id="b999d-134">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="b999d-135">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="b999d-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="b999d-136">:::no-loc(Razor):::要求がルーティングされるページハンドラーメソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="b999d-136">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="b999d-137">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="b999d-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="b999d-138">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-138">[BindProperty] attribute</span></span>

<span data-ttu-id="b999d-139">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="b999d-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="b999d-140">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-140">[BindProperties] attribute</span></span>

<span data-ttu-id="b999d-141">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="b999d-142">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="b999d-143">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="b999d-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="b999d-144">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="b999d-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="b999d-145">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="b999d-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="b999d-146">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="b999d-147">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b999d-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="b999d-148">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b999d-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="b999d-149">変換元</span><span class="sxs-lookup"><span data-stu-id="b999d-149">Sources</span></span>

<span data-ttu-id="b999d-150">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="b999d-151">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="b999d-151">Form fields</span></span>
1. <span data-ttu-id="b999d-152">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="b999d-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="b999d-153">ルート データ</span><span class="sxs-lookup"><span data-stu-id="b999d-153">Route data</span></span>
1. <span data-ttu-id="b999d-154">クエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="b999d-154">Query string parameters</span></span>
1. <span data-ttu-id="b999d-155">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="b999d-155">Uploaded files</span></span>

<span data-ttu-id="b999d-156">ターゲット パラメーターまたはプロパティごとに、前述の一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="b999d-157">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="b999d-157">There are a few exceptions:</span></span>

* <span data-ttu-id="b999d-158">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="b999d-159">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="b999d-160">既定のソースが正しくない場合は、次のいずれかの属性を使用してソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="b999d-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="b999d-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="b999d-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -ルートデータから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="b999d-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -ポストされたフォームフィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="b999d-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="b999d-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="b999d-166">これらの属性: </span><span class="sxs-lookup"><span data-stu-id="b999d-166">These attributes:</span></span>

* <span data-ttu-id="b999d-167">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="b999d-168">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b999d-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="b999d-169">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="b999d-170">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b999d-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="b999d-171">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-171">[FromBody] attribute</span></span>

<span data-ttu-id="b999d-172">`[FromBody]` 属性をパラメーターに適用すると、HTTP 要求の本文からそのプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="b999d-173">ASP.NET Core ランタイムでは、本文を読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="b999d-174">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="b999d-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="b999d-175">`[FromBody]` を複合型パラメーターに適用すると、そのプロパティに適用されているバインディング ソース属性はいずれも無視されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="b999d-176">たとえば、次の `Create` アクションでは、その `pet` パラメーターを本文から設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="b999d-177">`Pet` クラスでは、`Breed` プロパティをクエリ文字列パラメーターから設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="b999d-178">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="b999d-178">In the preceding example:</span></span>

* <span data-ttu-id="b999d-179">`[FromQuery]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="b999d-180">`Breed` プロパティは、クエリ文字列パラメーターから設定されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="b999d-181">入力フォーマッタでは本文のみが読み取られ、バインディング ソース属性は認識されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="b999d-182">本文内で適切な値が見つかった場合は、その値を使用して `Breed` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="b999d-183">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="b999d-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="b999d-184">入力フォーマッタによって要求ストリームが読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="b999d-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="b999d-185">その他のソース</span><span class="sxs-lookup"><span data-stu-id="b999d-185">Additional sources</span></span>

<span data-ttu-id="b999d-186">ソース データは、" *値プロバイダー* " によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-186">Source data is provided to the model binding system by *value providers* .</span></span> <span data-ttu-id="b999d-187">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="b999d-188">たとえば、またはセッション状態のデータが必要になる場合があり :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="b999d-188">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="b999d-189">新しいソースからデータを取得するには: </span><span class="sxs-lookup"><span data-stu-id="b999d-189">To get data from a new source:</span></span>

* <span data-ttu-id="b999d-190">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b999d-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="b999d-191">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b999d-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="b999d-192">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="b999d-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="b999d-193">サンプルアプリには、s から値を取得する [値プロバイダー](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) と [ファクトリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) の例が含まれてい :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="b999d-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="b999d-194">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="b999d-195">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="b999d-196">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b999d-196">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="b999d-197">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="b999d-197">No source for a model property</span></span>

<span data-ttu-id="b999d-198">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="b999d-199">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="b999d-200">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="b999d-201">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="b999d-202">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="b999d-203">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="b999d-204">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="b999d-205">モデルプロパティのフォームフィールドに何も見つからない場合にモデルの状態を無効にする必要がある場合は、属性を使用し [`[BindRequired]`](#bindrequired-attribute) ます。</span><span class="sxs-lookup"><span data-stu-id="b999d-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="b999d-206">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="b999d-207">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="b999d-208">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="b999d-208">Type conversion errors</span></span>

<span data-ttu-id="b999d-209">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="b999d-210">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="b999d-211">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="b999d-212">ページで、次のエラーメッセージが表示さ :::no-loc(Razor)::: れたページを再び表示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-212">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="b999d-213">クライアント側の検証では、ページフォームに送信される可能性のあるほとんどの不適切なデータをキャッチ :::no-loc(Razor)::: します。</span><span class="sxs-lookup"><span data-stu-id="b999d-213">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="b999d-214">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="b999d-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="b999d-215">サンプル アプリには、 **[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、 **[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="b999d-216">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="b999d-217">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="b999d-218">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="b999d-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="b999d-219">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="b999d-220">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="b999d-221">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b999d-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="b999d-222">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="b999d-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="b999d-223">単純型</span><span class="sxs-lookup"><span data-stu-id="b999d-223">Simple types</span></span>

<span data-ttu-id="b999d-224">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="b999d-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="b999d-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="b999d-226">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="b999d-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="b999d-227">Char</span><span class="sxs-lookup"><span data-stu-id="b999d-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="b999d-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="b999d-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="b999d-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="b999d-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="b999d-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="b999d-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="b999d-231">Double</span><span class="sxs-lookup"><span data-stu-id="b999d-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="b999d-232">Enum</span><span class="sxs-lookup"><span data-stu-id="b999d-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="b999d-233">Guid</span><span class="sxs-lookup"><span data-stu-id="b999d-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="b999d-234">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="b999d-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="b999d-235">Single</span><span class="sxs-lookup"><span data-stu-id="b999d-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="b999d-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="b999d-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="b999d-237">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="b999d-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="b999d-238">Uri</span><span class="sxs-lookup"><span data-stu-id="b999d-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="b999d-239">バージョン</span><span class="sxs-lookup"><span data-stu-id="b999d-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="b999d-240">複合型</span><span class="sxs-lookup"><span data-stu-id="b999d-240">Complex types</span></span>

<span data-ttu-id="b999d-241">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="b999d-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="b999d-242">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="b999d-243">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name* .</span></span> <span data-ttu-id="b999d-244">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="b999d-245">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="b999d-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="b999d-246">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="b999d-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="b999d-247">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="b999d-248">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="b999d-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="b999d-249">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="b999d-249">Prefix = parameter name</span></span>

<span data-ttu-id="b999d-250">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合: </span><span class="sxs-lookup"><span data-stu-id="b999d-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="b999d-251">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="b999d-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="b999d-252">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="b999d-253">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="b999d-253">Prefix = property name</span></span>

<span data-ttu-id="b999d-254">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合: </span><span class="sxs-lookup"><span data-stu-id="b999d-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="b999d-255">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="b999d-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="b999d-256">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="b999d-257">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="b999d-257">Custom prefix</span></span>

<span data-ttu-id="b999d-258">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合: </span><span class="sxs-lookup"><span data-stu-id="b999d-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="b999d-259">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="b999d-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="b999d-260">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="b999d-261">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="b999d-261">Attributes for complex type targets</span></span>

<span data-ttu-id="b999d-262">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="b999d-263">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="b999d-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="b999d-264">これらは、入力フォーマッタに影響を与え **ません** 。これは、ポストされた JSON および XML 要求本文を処理します。</span><span class="sxs-lookup"><span data-stu-id="b999d-264">They do \* **not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="b999d-265">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="b999d-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="b999d-266">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-266">[Bind] attribute</span></span>

<span data-ttu-id="b999d-267">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="b999d-268">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="b999d-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="b999d-269">`[Bind]` 入力フォーマッタには影響し _\*_ません_\*_ 。</span><span class="sxs-lookup"><span data-stu-id="b999d-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="b999d-270">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="b999d-271">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="b999d-272">`[Bind]`属性を使用して、_create \* シナリオで過剰ポストを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="b999d-273">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="b999d-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="b999d-274">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b999d-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="b999d-275">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="b999d-276">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-276">[BindRequired] attribute</span></span>

<span data-ttu-id="b999d-277">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="b999d-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="b999d-278">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="b999d-279">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="b999d-280">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="b999d-281">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-281">[BindNever] attribute</span></span>

<span data-ttu-id="b999d-282">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="b999d-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="b999d-283">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="b999d-284">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="b999d-285">コレクション</span><span class="sxs-lookup"><span data-stu-id="b999d-285">Collections</span></span>

<span data-ttu-id="b999d-286">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="b999d-287">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="b999d-288">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-288">For example:</span></span>

* <span data-ttu-id="b999d-289">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合: </span><span class="sxs-lookup"><span data-stu-id="b999d-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="b999d-290">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-290">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="b999d-291">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="b999d-292">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="b999d-293">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="b999d-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="b999d-294">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="b999d-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="b999d-295">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b999d-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="b999d-296">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="b999d-297">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="b999d-298">辞書</span><span class="sxs-lookup"><span data-stu-id="b999d-298">Dictionaries</span></span>

<span data-ttu-id="b999d-299">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="b999d-300">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="b999d-301">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-301">For example:</span></span>

* <span data-ttu-id="b999d-302">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします: </span><span class="sxs-lookup"><span data-stu-id="b999d-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="b999d-303">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="b999d-303">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="b999d-304">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="b999d-305">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="b999d-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="b999d-306">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="b999d-306">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="b999d-307">コンストラクターのバインドとレコードの種類</span><span class="sxs-lookup"><span data-stu-id="b999d-307">Constructor binding and record types</span></span>

<span data-ttu-id="b999d-308">モデルバインドでは、複合型にパラメーターなしのコンストラクターが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="b999d-308">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="b999d-309">`System.Text.Json`と `Newtonsoft.Json` ベースの入力フォーマッタはどちらも、パラメーターなしのコンストラクターを持たないクラスの逆シリアル化をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="b999d-309">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="b999d-310">C# 9 では、レコードの種類が導入されています。これは、ネットワーク上でデータを簡潔に表現するための優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="b999d-310">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="b999d-311">ASP.NET Core は、1つのコンストラクターを使用して、モデルバインドとレコードの種類の検証のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="b999d-311">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="b999d-312">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="b999d-312">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="b999d-313">レコードの種類を検証するとき、ランタイムは、プロパティではなく、パラメーターに対して明示的に検証メタデータを検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-313">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="b999d-314">モデル バインド ルート データとクエリ文字列のグローバリゼーション動作</span><span class="sxs-lookup"><span data-stu-id="b999d-314">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="b999d-315">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="b999d-315">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="b999d-316">値をインバリアント カルチャとして扱います。</span><span class="sxs-lookup"><span data-stu-id="b999d-316">Treat values as invariant culture.</span></span>
* <span data-ttu-id="b999d-317">URL はカルチャに依存しないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="b999d-317">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="b999d-318">これに対し、フォーム データからの値は、カルチャに依存した変換にかけられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-318">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="b999d-319">URL がロケール間で共有可能なように、設計上そのようになっています。</span><span class="sxs-lookup"><span data-stu-id="b999d-319">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="b999d-320">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでカルチャ依存の変換が行われるようにするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-320">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="b999d-321"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory> から継承します</span><span class="sxs-lookup"><span data-stu-id="b999d-321">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="b999d-322">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) または [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) からコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="b999d-322">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="b999d-323">値プロバイダー コンストラクターに渡された[カルチャ値](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)を [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b999d-323">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="b999d-324">MVC オプション内の既定値プロバイダー ファクトリを、新しいものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b999d-324">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="b999d-325">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="b999d-325">Special data types</span></span>

<span data-ttu-id="b999d-326">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-326">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="b999d-327">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="b999d-327">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="b999d-328">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="b999d-328">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="b999d-329">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b999d-329">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="b999d-330">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="b999d-330">CancellationToken</span></span>

<span data-ttu-id="b999d-331">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-331">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="b999d-332">FormCollection</span><span class="sxs-lookup"><span data-stu-id="b999d-332">FormCollection</span></span>

<span data-ttu-id="b999d-333">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="b999d-333">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="b999d-334">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="b999d-334">Input formatters</span></span>

<span data-ttu-id="b999d-335">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-335">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="b999d-336">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された " *入力フォーマッタ* " が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-336">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="b999d-337">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b999d-337">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="b999d-338">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-338">You can add other formatters for other content types.</span></span>

<span data-ttu-id="b999d-339">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-339">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="b999d-340">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-340">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="b999d-341">組み込みの XML 入力フォーマッタを使用するには: </span><span class="sxs-lookup"><span data-stu-id="b999d-341">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="b999d-342">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b999d-342">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="b999d-343">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b999d-343">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="b999d-344">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="b999d-344">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="b999d-345">詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-345">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="b999d-346">入力フォーマッタを使用してモデル バインドをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="b999d-346">Customize model binding with input formatters</span></span>

<span data-ttu-id="b999d-347">入力フォーマッタは、要求本文からデータを読み取るためのすべての役割を担います。</span><span class="sxs-lookup"><span data-stu-id="b999d-347">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="b999d-348">このプロセスをカスタマイズするには、入力フォーマッタによって使用される API を構成します。</span><span class="sxs-lookup"><span data-stu-id="b999d-348">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="b999d-349">このセクションでは、`ObjectId` という名前のカスタム型を理解するために、`System.Text.Json` ベースの入力フォーマッタをカスタマイズする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b999d-349">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="b999d-350">`Id` という名前のカスタム `ObjectId` プロパティが含まれている、次のモデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="b999d-350">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="b999d-351">`System.Text.Json` を使用する際のモデル バインド プロセスをカスタマイズするために、<xref:System.Text.Json.Serialization.JsonConverter%601> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b999d-351">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="b999d-352">カスタム コンバーターを使用するために、型に <xref:System.Text.Json.Serialization.JsonConverterAttribute> 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="b999d-352">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="b999d-353">次の例では、`ObjectId` 型は、`ObjectIdConverter` をそのカスタム コンバーターとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-353">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="b999d-354">詳細については、[カスタム コンバーターを記述する方法](/dotnet/standard/serialization/system-text-json-converters-how-to)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b999d-354">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="b999d-355">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="b999d-355">Exclude specified types from model binding</span></span>

<span data-ttu-id="b999d-356">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-356">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="b999d-357">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="b999d-357">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="b999d-358">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-358">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="b999d-359">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="b999d-359">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b999d-360">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-360">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="b999d-361">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="b999d-361">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b999d-362">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-362">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="b999d-363">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="b999d-363">Custom model binders</span></span>

<span data-ttu-id="b999d-364">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="b999d-364">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="b999d-365">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-365">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="b999d-366">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="b999d-366">Manual model binding</span></span> 

<span data-ttu-id="b999d-367">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-367">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="b999d-368">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-368">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="b999d-369">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-369">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="b999d-370">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-370">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="b999d-371">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-371">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="b999d-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> では、フォーム本文、クエリ文字列、およびルート データからデータを取得するために、値プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="b999d-373">`TryUpdateModelAsync` は通常、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b999d-373">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="b999d-374">:::no-loc(Razor):::過剰ポストを防ぐために、コントローラーとビューを使用してページおよび MVC アプリで使用します。</span><span class="sxs-lookup"><span data-stu-id="b999d-374">Used with :::no-loc(Razor)::: Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="b999d-375">フォーム データ、クエリ文字列、およびルート データから使用される場合を除き、Web API では使用されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-375">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="b999d-376">JSON を使用する Web API エンドポイントでは、[入力フォーマッタ](#input-formatters)を使用して要求本文がオブジェクトに逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-376">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="b999d-377">詳細については、「[TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b999d-377">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="b999d-378">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-378">[FromServices] attribute</span></span>

<span data-ttu-id="b999d-379">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="b999d-379">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="b999d-380">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="b999d-380">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="b999d-381">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-381">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b999d-382">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-382">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b999d-383">その他の資料</span><span class="sxs-lookup"><span data-stu-id="b999d-383">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b999d-384">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="b999d-384">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="b999d-385">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b999d-385">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="b999d-386">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="b999d-386">What is Model binding</span></span>

<span data-ttu-id="b999d-387">コントローラーと :::no-loc(Razor)::: ページは、HTTP 要求から取得されたデータを処理します。</span><span class="sxs-lookup"><span data-stu-id="b999d-387">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="b999d-388">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-388">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="b999d-389">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="b999d-389">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="b999d-390">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-390">Model binding automates this process.</span></span> <span data-ttu-id="b999d-391">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="b999d-391">The model binding system:</span></span>

* <span data-ttu-id="b999d-392">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-392">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="b999d-393">:::no-loc(Razor):::メソッドパラメーターおよびパブリックプロパティのデータをコントローラーおよびページに提供します。</span><span class="sxs-lookup"><span data-stu-id="b999d-393">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="b999d-394">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="b999d-394">Converts string data to .NET types.</span></span>
* <span data-ttu-id="b999d-395">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="b999d-395">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="b999d-396">例</span><span class="sxs-lookup"><span data-stu-id="b999d-396">Example</span></span>

<span data-ttu-id="b999d-397">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="b999d-397">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="b999d-398">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-398">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="b999d-399">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-399">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="b999d-400">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-400">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="b999d-401">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-401">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="b999d-402">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="b999d-402">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="b999d-403">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-403">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="b999d-404">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="b999d-404">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="b999d-405">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-405">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="b999d-406">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="b999d-406">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="b999d-407">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-407">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="b999d-408">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="b999d-408">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="b999d-409">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-409">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="b999d-410">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="b999d-410">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="b999d-411">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-411">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="b999d-412">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-412">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="b999d-413">対象サーバー</span><span class="sxs-lookup"><span data-stu-id="b999d-413">Targets</span></span>

<span data-ttu-id="b999d-414">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-414">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="b999d-415">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="b999d-415">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="b999d-416">:::no-loc(Razor):::要求がルーティングされるページハンドラーメソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="b999d-416">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="b999d-417">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="b999d-417">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="b999d-418">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-418">[BindProperty] attribute</span></span>

<span data-ttu-id="b999d-419">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="b999d-419">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="b999d-420">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-420">[BindProperties] attribute</span></span>

<span data-ttu-id="b999d-421">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-421">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="b999d-422">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-422">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="b999d-423">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="b999d-423">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="b999d-424">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="b999d-424">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="b999d-425">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="b999d-425">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="b999d-426">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-426">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="b999d-427">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b999d-427">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="b999d-428">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b999d-428">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="b999d-429">変換元</span><span class="sxs-lookup"><span data-stu-id="b999d-429">Sources</span></span>

<span data-ttu-id="b999d-430">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-430">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="b999d-431">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="b999d-431">Form fields</span></span>
1. <span data-ttu-id="b999d-432">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="b999d-432">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="b999d-433">ルート データ</span><span class="sxs-lookup"><span data-stu-id="b999d-433">Route data</span></span>
1. <span data-ttu-id="b999d-434">クエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="b999d-434">Query string parameters</span></span>
1. <span data-ttu-id="b999d-435">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="b999d-435">Uploaded files</span></span>

<span data-ttu-id="b999d-436">ターゲット パラメーターまたはプロパティごとに、前述の一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-436">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="b999d-437">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="b999d-437">There are a few exceptions:</span></span>

* <span data-ttu-id="b999d-438">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-438">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="b999d-439">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-439">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="b999d-440">既定のソースが正しくない場合は、次のいずれかの属性を使用してソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="b999d-440">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="b999d-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="b999d-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -ルートデータから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="b999d-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -ポストされたフォームフィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="b999d-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="b999d-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="b999d-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="b999d-446">これらの属性: </span><span class="sxs-lookup"><span data-stu-id="b999d-446">These attributes:</span></span>

* <span data-ttu-id="b999d-447">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-447">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="b999d-448">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b999d-448">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="b999d-449">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-449">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="b999d-450">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b999d-450">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="b999d-451">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-451">[FromBody] attribute</span></span>

<span data-ttu-id="b999d-452">`[FromBody]` 属性をパラメーターに適用すると、HTTP 要求の本文からそのプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-452">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="b999d-453">ASP.NET Core ランタイムでは、本文を読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-453">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="b999d-454">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="b999d-454">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="b999d-455">`[FromBody]` を複合型パラメーターに適用すると、そのプロパティに適用されているバインディング ソース属性はいずれも無視されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-455">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="b999d-456">たとえば、次の `Create` アクションでは、その `pet` パラメーターを本文から設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-456">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="b999d-457">`Pet` クラスでは、`Breed` プロパティをクエリ文字列パラメーターから設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-457">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="b999d-458">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="b999d-458">In the preceding example:</span></span>

* <span data-ttu-id="b999d-459">`[FromQuery]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-459">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="b999d-460">`Breed` プロパティは、クエリ文字列パラメーターから設定されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-460">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="b999d-461">入力フォーマッタでは本文のみが読み取られ、バインディング ソース属性は認識されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-461">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="b999d-462">本文内で適切な値が見つかった場合は、その値を使用して `Breed` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-462">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="b999d-463">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="b999d-463">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="b999d-464">入力フォーマッタによって要求ストリームが読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="b999d-464">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="b999d-465">その他のソース</span><span class="sxs-lookup"><span data-stu-id="b999d-465">Additional sources</span></span>

<span data-ttu-id="b999d-466">ソース データは、" *値プロバイダー* " によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-466">Source data is provided to the model binding system by *value providers* .</span></span> <span data-ttu-id="b999d-467">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-467">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="b999d-468">たとえば、またはセッション状態のデータが必要になる場合があり :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="b999d-468">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="b999d-469">新しいソースからデータを取得するには: </span><span class="sxs-lookup"><span data-stu-id="b999d-469">To get data from a new source:</span></span>

* <span data-ttu-id="b999d-470">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b999d-470">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="b999d-471">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b999d-471">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="b999d-472">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="b999d-472">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="b999d-473">サンプルアプリには、s から値を取得する [値プロバイダー](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) と [ファクトリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) の例が含まれてい :::no-loc(cookie)::: ます。</span><span class="sxs-lookup"><span data-stu-id="b999d-473">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="b999d-474">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-474">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="b999d-475">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-475">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="b999d-476">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b999d-476">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="b999d-477">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="b999d-477">No source for a model property</span></span>

<span data-ttu-id="b999d-478">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-478">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="b999d-479">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-479">The property is set to null or a default value:</span></span>

* <span data-ttu-id="b999d-480">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-480">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="b999d-481">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-481">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="b999d-482">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-482">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="b999d-483">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-483">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="b999d-484">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-484">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="b999d-485">モデルプロパティのフォームフィールドに何も見つからない場合にモデルの状態を無効にする必要がある場合は、属性を使用し [`[BindRequired]`](#bindrequired-attribute) ます。</span><span class="sxs-lookup"><span data-stu-id="b999d-485">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="b999d-486">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-486">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="b999d-487">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-487">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="b999d-488">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="b999d-488">Type conversion errors</span></span>

<span data-ttu-id="b999d-489">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-489">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="b999d-490">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-490">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="b999d-491">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-491">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="b999d-492">ページで、次のエラーメッセージが表示さ :::no-loc(Razor)::: れたページを再び表示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-492">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="b999d-493">クライアント側の検証では、ページフォームに送信される可能性のあるほとんどの不適切なデータをキャッチ :::no-loc(Razor)::: します。</span><span class="sxs-lookup"><span data-stu-id="b999d-493">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="b999d-494">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="b999d-494">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="b999d-495">サンプル アプリには、 **[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、 **[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-495">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="b999d-496">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-496">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="b999d-497">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="b999d-497">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="b999d-498">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="b999d-498">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="b999d-499">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-499">The invalid input does appear in an error message.</span></span> <span data-ttu-id="b999d-500">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-500">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="b999d-501">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b999d-501">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="b999d-502">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="b999d-502">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="b999d-503">単純型</span><span class="sxs-lookup"><span data-stu-id="b999d-503">Simple types</span></span>

<span data-ttu-id="b999d-504">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-504">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="b999d-505">Boolean</span><span class="sxs-lookup"><span data-stu-id="b999d-505">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="b999d-506">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="b999d-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="b999d-507">Char</span><span class="sxs-lookup"><span data-stu-id="b999d-507">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="b999d-508">DateTime</span><span class="sxs-lookup"><span data-stu-id="b999d-508">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="b999d-509">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="b999d-509">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="b999d-510">Decimal</span><span class="sxs-lookup"><span data-stu-id="b999d-510">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="b999d-511">Double</span><span class="sxs-lookup"><span data-stu-id="b999d-511">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="b999d-512">Enum</span><span class="sxs-lookup"><span data-stu-id="b999d-512">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="b999d-513">Guid</span><span class="sxs-lookup"><span data-stu-id="b999d-513">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="b999d-514">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="b999d-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="b999d-515">Single</span><span class="sxs-lookup"><span data-stu-id="b999d-515">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="b999d-516">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="b999d-516">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="b999d-517">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="b999d-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="b999d-518">Uri</span><span class="sxs-lookup"><span data-stu-id="b999d-518">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="b999d-519">バージョン</span><span class="sxs-lookup"><span data-stu-id="b999d-519">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="b999d-520">複合型</span><span class="sxs-lookup"><span data-stu-id="b999d-520">Complex types</span></span>

<span data-ttu-id="b999d-521">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="b999d-521">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="b999d-522">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-522">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="b999d-523">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-523">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name* .</span></span> <span data-ttu-id="b999d-524">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-524">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="b999d-525">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="b999d-525">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="b999d-526">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="b999d-526">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="b999d-527">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-527">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="b999d-528">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="b999d-528">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="b999d-529">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="b999d-529">Prefix = parameter name</span></span>

<span data-ttu-id="b999d-530">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合: </span><span class="sxs-lookup"><span data-stu-id="b999d-530">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="b999d-531">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="b999d-531">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="b999d-532">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="b999d-533">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="b999d-533">Prefix = property name</span></span>

<span data-ttu-id="b999d-534">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合: </span><span class="sxs-lookup"><span data-stu-id="b999d-534">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="b999d-535">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="b999d-535">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="b999d-536">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-536">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="b999d-537">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="b999d-537">Custom prefix</span></span>

<span data-ttu-id="b999d-538">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合: </span><span class="sxs-lookup"><span data-stu-id="b999d-538">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="b999d-539">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="b999d-539">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="b999d-540">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-540">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="b999d-541">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="b999d-541">Attributes for complex type targets</span></span>

<span data-ttu-id="b999d-542">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-542">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="b999d-543">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="b999d-543">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="b999d-544">ポストされた JSON および XML 要求本文を処理する入力フォーマッタには影響しません。</span><span class="sxs-lookup"><span data-stu-id="b999d-544">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="b999d-545">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="b999d-545">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="b999d-546">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-546">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="b999d-547">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-547">[BindRequired] attribute</span></span>

<span data-ttu-id="b999d-548">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="b999d-548">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="b999d-549">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-549">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="b999d-550">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-550">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="b999d-551">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-551">[BindNever] attribute</span></span>

<span data-ttu-id="b999d-552">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="b999d-552">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="b999d-553">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-553">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="b999d-554">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-554">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="b999d-555">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-555">[Bind] attribute</span></span>

<span data-ttu-id="b999d-556">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-556">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="b999d-557">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="b999d-557">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="b999d-558">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-558">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="b999d-559">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-559">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="b999d-560">`[Bind]` 属性を使用すれば、" *作成* " シナリオにおいて過剰ポスティングから保護することができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-560">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="b999d-561">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="b999d-561">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="b999d-562">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b999d-562">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="b999d-563">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-563">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="b999d-564">コレクション</span><span class="sxs-lookup"><span data-stu-id="b999d-564">Collections</span></span>

<span data-ttu-id="b999d-565">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-565">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="b999d-566">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-566">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="b999d-567">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-567">For example:</span></span>

* <span data-ttu-id="b999d-568">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合: </span><span class="sxs-lookup"><span data-stu-id="b999d-568">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="b999d-569">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-569">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="b999d-570">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="b999d-570">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="b999d-571">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-571">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="b999d-572">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="b999d-572">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="b999d-573">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="b999d-573">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="b999d-574">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b999d-574">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="b999d-575">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-575">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="b999d-576">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-576">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="b999d-577">辞書</span><span class="sxs-lookup"><span data-stu-id="b999d-577">Dictionaries</span></span>

<span data-ttu-id="b999d-578">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-578">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="b999d-579">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-579">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="b999d-580">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-580">For example:</span></span>

* <span data-ttu-id="b999d-581">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします: </span><span class="sxs-lookup"><span data-stu-id="b999d-581">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="b999d-582">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="b999d-582">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="b999d-583">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-583">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="b999d-584">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="b999d-584">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="b999d-585">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="b999d-585">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="b999d-586">モデル バインド ルート データとクエリ文字列のグローバリゼーション動作</span><span class="sxs-lookup"><span data-stu-id="b999d-586">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="b999d-587">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="b999d-587">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="b999d-588">値をインバリアント カルチャとして扱います。</span><span class="sxs-lookup"><span data-stu-id="b999d-588">Treat values as invariant culture.</span></span>
* <span data-ttu-id="b999d-589">URL はカルチャに依存しないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="b999d-589">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="b999d-590">これに対し、フォーム データからの値は、カルチャに依存した変換にかけられます。</span><span class="sxs-lookup"><span data-stu-id="b999d-590">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="b999d-591">URL がロケール間で共有可能なように、設計上そのようになっています。</span><span class="sxs-lookup"><span data-stu-id="b999d-591">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="b999d-592">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでカルチャ依存の変換が行われるようにするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-592">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="b999d-593"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory> から継承します</span><span class="sxs-lookup"><span data-stu-id="b999d-593">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="b999d-594">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) または [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) からコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="b999d-594">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="b999d-595">値プロバイダー コンストラクターに渡された[カルチャ値](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)を [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b999d-595">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="b999d-596">MVC オプション内の既定値プロバイダー ファクトリを、新しいものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b999d-596">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="b999d-597">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="b999d-597">Special data types</span></span>

<span data-ttu-id="b999d-598">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-598">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="b999d-599">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="b999d-599">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="b999d-600">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="b999d-600">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="b999d-601">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b999d-601">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="b999d-602">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="b999d-602">CancellationToken</span></span>

<span data-ttu-id="b999d-603">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-603">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="b999d-604">FormCollection</span><span class="sxs-lookup"><span data-stu-id="b999d-604">FormCollection</span></span>

<span data-ttu-id="b999d-605">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="b999d-605">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="b999d-606">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="b999d-606">Input formatters</span></span>

<span data-ttu-id="b999d-607">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-607">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="b999d-608">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された " *入力フォーマッタ* " が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-608">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="b999d-609">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b999d-609">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="b999d-610">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-610">You can add other formatters for other content types.</span></span>

<span data-ttu-id="b999d-611">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-611">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="b999d-612">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-612">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="b999d-613">組み込みの XML 入力フォーマッタを使用するには: </span><span class="sxs-lookup"><span data-stu-id="b999d-613">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="b999d-614">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b999d-614">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="b999d-615">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b999d-615">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="b999d-616">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="b999d-616">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="b999d-617">詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-617">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="b999d-618">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="b999d-618">Exclude specified types from model binding</span></span>

<span data-ttu-id="b999d-619">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-619">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="b999d-620">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="b999d-620">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="b999d-621">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-621">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="b999d-622">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="b999d-622">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b999d-623">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-623">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="b999d-624">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="b999d-624">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b999d-625">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b999d-625">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="b999d-626">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="b999d-626">Custom model binders</span></span>

<span data-ttu-id="b999d-627">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="b999d-627">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="b999d-628">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b999d-628">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="b999d-629">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="b999d-629">Manual model binding</span></span>

<span data-ttu-id="b999d-630">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="b999d-630">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="b999d-631">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="b999d-631">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="b999d-632">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="b999d-632">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="b999d-633">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-633">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="b999d-634">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b999d-634">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="b999d-635">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="b999d-635">[FromServices] attribute</span></span>

<span data-ttu-id="b999d-636">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="b999d-636">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="b999d-637">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="b999d-637">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="b999d-638">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="b999d-638">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b999d-639">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="b999d-639">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b999d-640">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b999d-640">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

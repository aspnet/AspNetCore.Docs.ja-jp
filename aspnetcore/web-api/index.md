---
title: ASP.NET Core を使って Web API を作成する
author: scottaddie
description: ASP.NET Core での Web API の作成の基本について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/20/2020
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
uid: web-api/index
ms.openlocfilehash: 03debcb211a8cb8f0ebd6d5e67fbc8de7b574e27
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551654"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="0bd56-103">ASP.NET Core を使って Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="0bd56-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="0bd56-104">作成者: [Scott Addie](https://github.com/scottaddie)、[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="0bd56-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="0bd56-105">ASP.NET Core では、C# を使った RESTful サービス (別名: Web API) の作成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="0bd56-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="0bd56-106">要求を処理するために、Web API ではコントローラーを使用します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="0bd56-107">Web API の "*コントローラー*" は `ControllerBase` から派生するクラスです。</span><span class="sxs-lookup"><span data-stu-id="0bd56-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="0bd56-108">この記事では、コントローラーを使って Web API 要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="0bd56-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="0bd56-110">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0bd56-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="0bd56-111">ControllerBase クラス</span><span class="sxs-lookup"><span data-stu-id="0bd56-111">ControllerBase class</span></span>

<span data-ttu-id="0bd56-112">Web API は、<xref:Microsoft.AspNetCore.Mvc.ControllerBase> から派生した 1 つ以上のコントローラー クラスで構成されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="0bd56-113">Web API のプロジェクト テンプレートでは、スターター コントローラーが提供されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="0bd56-114"><xref:Microsoft.AspNetCore.Mvc.Controller> クラスから派生させて Web API のコントローラーを作成しないでください。</span><span class="sxs-lookup"><span data-stu-id="0bd56-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="0bd56-115">`ControllerBase` から派生した `Controller` にはビューのサポートが追加されるため、これは Web API 要求ではなく Web ページを処理するためのものです。</span><span class="sxs-lookup"><span data-stu-id="0bd56-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="0bd56-116">このルールには例外があります。ビューと Web API の両方で同じコントローラーを使うことを計画している場合は、`Controller` から派生させます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="0bd56-117">`ControllerBase` クラスには、HTTP 要求の処理に役立つプロパティとメソッドが多数用意されています。</span><span class="sxs-lookup"><span data-stu-id="0bd56-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="0bd56-118">たとえば、`ControllerBase.CreatedAtAction` では状態コード 201 が返されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="0bd56-119">次に、`ControllerBase` に用意されているメソッドの例をさらにいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="0bd56-120">メソッド</span><span class="sxs-lookup"><span data-stu-id="0bd56-120">Method</span></span>   |<span data-ttu-id="0bd56-121">メモ</span><span class="sxs-lookup"><span data-stu-id="0bd56-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="0bd56-122">400 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="0bd56-123">404 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="0bd56-124">ファイルを返します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="0bd56-125">[モデル バインド](xref:mvc/models/model-binding)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="0bd56-126">[モデル検証](xref:mvc/models/validation)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="0bd56-127">使用可能なすべてのメソッドとプロパティの一覧については、「<xref:Microsoft.AspNetCore.Mvc.ControllerBase>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0bd56-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="0bd56-128">属性</span><span class="sxs-lookup"><span data-stu-id="0bd56-128">Attributes</span></span>

<span data-ttu-id="0bd56-129"><xref:Microsoft.AspNetCore.Mvc> 名前空間には、Web API のコントローラーとアクション メソッドの動作の構成に使用できる属性が用意されています。</span><span class="sxs-lookup"><span data-stu-id="0bd56-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="0bd56-130">次の例では、属性を使って、サポート対象の HTTP アクション動詞と、返却される可能性がある既知の HTTP ステータス コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="0bd56-131">次に、使用できる属性の例をさらにいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="0bd56-132">属性</span><span class="sxs-lookup"><span data-stu-id="0bd56-132">Attribute</span></span>|<span data-ttu-id="0bd56-133">メモ</span><span class="sxs-lookup"><span data-stu-id="0bd56-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="0bd56-134">コントローラーまたはアクションの URL パターンを指定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="0bd56-135">モデル バインドのために含めるプレフィックスとプロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="0bd56-136">HTTP GET アクション動詞をサポートするアクションを特定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="0bd56-137">アクションが受け取るデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="0bd56-138">アクションによって返すデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="0bd56-139">使用可能な属性を含む一覧については、<xref:Microsoft.AspNetCore.Mvc> 名前空間をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0bd56-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="0bd56-140">ApiController 属性</span><span class="sxs-lookup"><span data-stu-id="0bd56-140">ApiController attribute</span></span>

<span data-ttu-id="0bd56-141">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性をコントローラー クラスに適用して、次の厳格な、API に固有の動作を有効にできます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="0bd56-142">属性ルーティング要件</span><span class="sxs-lookup"><span data-stu-id="0bd56-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="0bd56-143">自動的な HTTP 400 応答</span><span class="sxs-lookup"><span data-stu-id="0bd56-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="0bd56-144">バインディング ソース パラメーター推論</span><span class="sxs-lookup"><span data-stu-id="0bd56-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="0bd56-145">マルチパート/フォーム データ要求の推論</span><span class="sxs-lookup"><span data-stu-id="0bd56-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="0bd56-146">エラー状態コードに関する問題の詳細</span><span class="sxs-lookup"><span data-stu-id="0bd56-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="0bd56-147">"*エラー状態コードに関する問題の詳細*" 機能には、[互換性バージョン](xref:mvc/compatibility-version) 2.2 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="0bd56-148">その他の機能では、互換性バージョン 2.1 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

* [<span data-ttu-id="0bd56-149">属性ルーティング要件</span><span class="sxs-lookup"><span data-stu-id="0bd56-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="0bd56-150">自動的な HTTP 400 応答</span><span class="sxs-lookup"><span data-stu-id="0bd56-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="0bd56-151">バインディング ソース パラメーター推論</span><span class="sxs-lookup"><span data-stu-id="0bd56-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="0bd56-152">マルチパート/フォーム データ要求の推論</span><span class="sxs-lookup"><span data-stu-id="0bd56-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="0bd56-153">これらの機能では[互換性バージョン](xref:mvc/compatibility-version) 2.1 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="0bd56-154">特定のコントローラーにおける属性</span><span class="sxs-lookup"><span data-stu-id="0bd56-154">Attribute on specific controllers</span></span>

<span data-ttu-id="0bd56-155">プロジェクト テンプレートからの次の例のように、`[ApiController]` 属性は特定のコントローラーに適用できます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="0bd56-156">複数のコントローラーにおける属性</span><span class="sxs-lookup"><span data-stu-id="0bd56-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="0bd56-157">複数のコントローラーでこの属性を使う方法の 1 つは、`[ApiController]` 属性で注釈を付けたカスタム基本コントローラー クラスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="0bd56-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="0bd56-158">次は、カスタム基底クラスとそこから派生したコントローラーを示す例です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="0bd56-159">アセンブリにおける属性</span><span class="sxs-lookup"><span data-stu-id="0bd56-159">Attribute on an assembly</span></span>

<span data-ttu-id="0bd56-160">[互換性バージョン](xref:mvc/compatibility-version)が 2.2 以降に設定されている場合は、`[ApiController]` 属性をアセンブリに適用できます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="0bd56-161">この方法での注釈では、アセンブリ内のすべてのコントローラーに Web API の動作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="0bd56-162">個別のコントローラーを除外する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="0bd56-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="0bd56-163">アセンブリ レベルの属性を `Startup` クラスを囲む名前空間宣言に適用します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="0bd56-164">属性ルーティング要件</span><span class="sxs-lookup"><span data-stu-id="0bd56-164">Attribute routing requirement</span></span>

<span data-ttu-id="0bd56-165">`[ApiController]` 属性では、属性ルーティング要件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="0bd56-166">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="0bd56-167">`Startup.Configure` の `UseEndpoints`、<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>、または <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> で定義された[規則ルート経由](xref:mvc/controllers/routing#conventional-routing)でアクションにアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="0bd56-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="0bd56-168">`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> または <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> で定義された[規則ルート](xref:mvc/controllers/routing#conventional-routing)経由でアクションにアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="0bd56-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="0bd56-169">自動的な HTTP 400 応答</span><span class="sxs-lookup"><span data-stu-id="0bd56-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="0bd56-170">`[ApiController]` 属性により、モデル検証エラーが発生すると HTTP 400 応答が自動的にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="0bd56-171">その結果、アクション メソッド内の次のコードは不要になります。</span><span class="sxs-lookup"><span data-stu-id="0bd56-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="0bd56-172">ASP.NET Core MVC では、<xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> アクション フィルターの使用により前のチェックが行われます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="0bd56-173">既定の BadRequest 応答</span><span class="sxs-lookup"><span data-stu-id="0bd56-173">Default BadRequest response</span></span>

<span data-ttu-id="0bd56-174">2\.1 の互換性バージョンを使う場合、HTTP 400 応答に対する既定の応答の種類は <xref:Microsoft.AspNetCore.Mvc.SerializableError> です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="0bd56-175">次の要求本文は、シリアル化された型の例です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0bd56-176">2\.2 以降の互換性バージョンを使う場合、HTTP 400 応答に対する既定の応答の種類は <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="0bd56-177">次の要求本文は、シリアル化された型の例です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-177">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="0bd56-178">`ValidationProblemDetails` 型:</span><span class="sxs-lookup"><span data-stu-id="0bd56-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="0bd56-179">Web API 応答でエラーを指定するための、コンピューターが判読できる形式を提供します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="0bd56-180">[RFC 7807 仕様](https://tools.ietf.org/html/rfc7807)に準拠しています。</span><span class="sxs-lookup"><span data-stu-id="0bd56-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

<span data-ttu-id="0bd56-181">自動とカスタムの応答を一貫させるには、<xref:System.Web.Http.ApiController.BadRequest%2A> ではなく <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-181">To make automatic and custom responses consistent, call the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> method instead of <xref:System.Web.Http.ApiController.BadRequest%2A>.</span></span> <span data-ttu-id="0bd56-182">`ValidationProblem` は <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> オブジェクトと、自動的な応答を返します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-182">`ValidationProblem` returns a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> object as well as the automatic response.</span></span>

### <a name="log-automatic-400-responses"></a><span data-ttu-id="0bd56-183">自動的な 400 応答を記録する</span><span class="sxs-lookup"><span data-stu-id="0bd56-183">Log automatic 400 responses</span></span>

<span data-ttu-id="0bd56-184">「[How to log automatic 400 responses on model validation error (dotnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157)」(モデル検証エラー時に自動的な 400 応答を記録する方法) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0bd56-184">See [How to log automatic 400 responses on model validation errors (dotnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="0bd56-185">自動的な 400 応答を無効にする</span><span class="sxs-lookup"><span data-stu-id="0bd56-185">Disable automatic 400 response</span></span>

<span data-ttu-id="0bd56-186">自動的な 400 の動作を無効にするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-186">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="0bd56-187">`Startup.ConfigureServices` で次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-187">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="0bd56-188">バインディング ソース パラメーター推論</span><span class="sxs-lookup"><span data-stu-id="0bd56-188">Binding source parameter inference</span></span>

<span data-ttu-id="0bd56-189">バインディング ソース属性では、アクション パラメーターの値が存在する場所が定義されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-189">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="0bd56-190">次のバインディング ソース属性が存在します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-190">The following binding source attributes exist:</span></span>

|<span data-ttu-id="0bd56-191">属性</span><span class="sxs-lookup"><span data-stu-id="0bd56-191">Attribute</span></span>|<span data-ttu-id="0bd56-192">バインド ソース</span><span class="sxs-lookup"><span data-stu-id="0bd56-192">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="0bd56-193">要求本文</span><span class="sxs-lookup"><span data-stu-id="0bd56-193">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="0bd56-194">要求本文内のフォーム データ</span><span class="sxs-lookup"><span data-stu-id="0bd56-194">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="0bd56-195">要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="0bd56-195">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="0bd56-196">要求のクエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="0bd56-196">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="0bd56-197">現在の要求からのルート データ</span><span class="sxs-lookup"><span data-stu-id="0bd56-197">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="0bd56-198">アクション パラメーターとして挿入される要求サービス</span><span class="sxs-lookup"><span data-stu-id="0bd56-198">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="0bd56-199">値に `%2f` (つまり、`/`) が含まれる可能性がある場合は、`[FromRoute]` を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="0bd56-199">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="0bd56-200">`%2f` は `/` にエスケープ解除されません。</span><span class="sxs-lookup"><span data-stu-id="0bd56-200">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="0bd56-201">値に `%2f` が含まれる可能性がある場合は、`[FromQuery]` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="0bd56-201">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="0bd56-202">`[ApiController]` 属性や `[FromQuery]` などのバインディング ソース属性がない場合は、ASP.NET Core ランタイムにより複合オブジェクト モデル バインダーの使用が試行されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-202">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="0bd56-203">複合オブジェクト モデル バインダーでは、値プロバイダーから定義された順序でデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-203">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="0bd56-204">次の例では、`discontinuedOnly` パラメーター値が要求 URL のクエリ文字列に指定されていることが `[FromQuery]` によって示されています。</span><span class="sxs-lookup"><span data-stu-id="0bd56-204">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="0bd56-205">`[ApiController]` 属性により、アクション パラメーターの既定のデータ ソースに対する推論規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-205">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="0bd56-206">これらの規則により、アクション パラメーターに属性を適用することで手動でバインディング ソースを特定する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="0bd56-206">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="0bd56-207">バインディング ソースの推論規則は、次のように動作します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-207">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="0bd56-208">`[FromBody]` は複合型パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-208">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="0bd56-209">`[FromBody]` 推論規則に対する例外は、<xref:Microsoft.AspNetCore.Http.IFormCollection> や <xref:System.Threading.CancellationToken> など、特殊な意味を持つ組み込みの複合型です。</span><span class="sxs-lookup"><span data-stu-id="0bd56-209">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="0bd56-210">バインディング ソース推論コードでは、そのような特殊な型は無視されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-210">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="0bd56-211">`[FromForm]` は <xref:Microsoft.AspNetCore.Http.IFormFile> および <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 型のアクション パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-211">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="0bd56-212">簡易型またはユーザー定義型に対しては推論されません。</span><span class="sxs-lookup"><span data-stu-id="0bd56-212">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="0bd56-213">`[FromRoute]` は、ルート テンプレート内のパラメーターと一致する任意のアクション パラメーター名に対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-213">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="0bd56-214">複数のルートがアクション パラメーターと一致する場合、ルート値はいずれも `[FromRoute]` と見なされます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-214">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="0bd56-215">`[FromQuery]` は他の任意のアクション パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-215">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="0bd56-216">FromBody 推論に関するメモ</span><span class="sxs-lookup"><span data-stu-id="0bd56-216">FromBody inference notes</span></span>

<span data-ttu-id="0bd56-217">`[FromBody]` は、`string` や `int` などの単純型に対しては推論されません。</span><span class="sxs-lookup"><span data-stu-id="0bd56-217">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="0bd56-218">そのため、その機能が必要な場合、単純型に対しては `[FromBody]` 属性を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0bd56-218">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="0bd56-219">要求本文からバインドされるパラメーターがアクションに複数ある場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-219">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="0bd56-220">たとえば、次のアクション メソッドのシグネチャはすべて例外の原因となります。</span><span class="sxs-lookup"><span data-stu-id="0bd56-220">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="0bd56-221">複合型であるため、両方に対して `[FromBody]` が推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-221">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="0bd56-222">1 つには `[FromBody]` 属性が使われ、もう 1 つは複合型なので推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-222">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="0bd56-223">両方で `[FromBody]` 属性が使われます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-223">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="0bd56-224">ASP.NET Core 2.1 では、リストや配列などのコレクション型パラメーターが誤って `[FromQuery]` と推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-224">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="0bd56-225">これらのパラメーターを要求本文からバインドする場合は、`[FromBody]` 属性を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0bd56-225">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="0bd56-226">この動作は ASP.NET Core 2.2 以降で修正されており、既定でコレクション型パラメーターが本文からバインドされることが推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-226">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="0bd56-227">推論規則を無効にする</span><span class="sxs-lookup"><span data-stu-id="0bd56-227">Disable inference rules</span></span>

<span data-ttu-id="0bd56-228">バインディング ソースの推論を無効にするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-228">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="0bd56-229">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-229">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="0bd56-230">マルチパート/フォーム データ要求の推論</span><span class="sxs-lookup"><span data-stu-id="0bd56-230">Multipart/form-data request inference</span></span>

<span data-ttu-id="0bd56-231">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 属性を使用してアクション パラメーターに注釈を付けた場合、`[ApiController]` 属性が推論規則に適用されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-231">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="0bd56-232">`multipart/form-data` 要求コンテンツ型が推論されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-232">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="0bd56-233">既定の動作を無効にするには、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-233">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="0bd56-234">エラー状態コードに関する問題の詳細</span><span class="sxs-lookup"><span data-stu-id="0bd56-234">Problem details for error status codes</span></span>

<span data-ttu-id="0bd56-235">互換性バージョンが 2.2 以上である場合、MVC によってエラー結果 (状態コードが 400 以上の結果) が <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> を含む結果に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-235">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="0bd56-236">`ProblemDetails` 型は、HTTP 応答でコンピューターが判読できるエラーの詳細を提供するための [RFC 7807 仕様](https://tools.ietf.org/html/rfc7807)に基づきます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-236">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="0bd56-237">コントローラー アクションで次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="0bd56-237">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="0bd56-238">`NotFound` メソッドにより、`ProblemDetails` 本文を使用して HTTP 404 ステータス コードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-238">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="0bd56-239">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-239">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="0bd56-240">ProblemDetails 応答を無効にする</span><span class="sxs-lookup"><span data-stu-id="0bd56-240">Disable ProblemDetails response</span></span>

<span data-ttu-id="0bd56-241"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> プロパティが `true` に設定されている場合、エラー状態コード用の `ProblemDetails` の自動作成は無効になります。</span><span class="sxs-lookup"><span data-stu-id="0bd56-241">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="0bd56-242">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-242">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="0bd56-243">[Consumes] 属性を使ってサポートされる要求のコンテンツの種類を定義する</span><span class="sxs-lookup"><span data-stu-id="0bd56-243">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="0bd56-244">既定では、アクションでは使用可能な要求のコンテンツの種類がすべてサポートされます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-244">By default, an action supports all available request content types.</span></span> <span data-ttu-id="0bd56-245">たとえば、アプリが JSON と XML の[入力フォーマッタ](xref:mvc/models/model-binding#input-formatters)を両方サポートするように構成されている場合、アクションでは、`application/json` と `application/xml` を含む複数のコンテンツの種類がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-245">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="0bd56-246">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性を使用すると、アクションでサポートされる要求のコンテンツの種類を制限できます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-246">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="0bd56-247">1 つ以上のコンテンツの種類を指定して、アクションまたはコントローラーに `[Consumes]` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-247">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="0bd56-248">上記のコードでは、`CreateProduct` アクションでコンテンツの種類 `application/xml` が指定されています。</span><span class="sxs-lookup"><span data-stu-id="0bd56-248">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="0bd56-249">このアクションにルーティングされる要求では、`Content-Type` ヘッダーに `application/xml` を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0bd56-249">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="0bd56-250">`Content-Type` ヘッダーに `application/xml` を指定していない要求では、[415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) 応答が発生します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-250">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="0bd56-251">また、`[Consumes]` 属性を使用すると、種類の制約を適用することによって、受信要求のコンテンツの種類に基づいてアクションの選択に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-251">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="0bd56-252">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0bd56-252">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="0bd56-253">上記のコードでは、`ConsumesController` は `https://localhost:5001/api/Consumes` の URL に送信された要求を処理するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="0bd56-253">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="0bd56-254">コントローラーの両方のアクション `PostJson` と `PostForm` では、同じ URL の POST 要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-254">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="0bd56-255">種類の制約を適用する `[Consumes]` 属性がなかった場合は、あいまい一致の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-255">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="0bd56-256">`[Consumes]` 属性は両方のアクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-256">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="0bd56-257">`PostJson` アクションでは、`Content-Type` ヘッダーに `application/json` を指定して送信された要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-257">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="0bd56-258">`PostForm` アクションでは、`Content-Type` ヘッダーに `application/x-www-form-urlencoded` を指定して送信された要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="0bd56-258">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="0bd56-259">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0bd56-259">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
* [<span data-ttu-id="0bd56-260">Microsoft Learn:ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="0bd56-260">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)

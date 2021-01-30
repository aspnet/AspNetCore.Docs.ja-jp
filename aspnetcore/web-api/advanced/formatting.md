---
title: ASP.NET Core Web API の応答データの書式設定
author: rick-anderson
description: ASP.NET Core Web API で応答データを書式設定する方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 5d228af00ee34e7f8ca60a5085872fdb93842367
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057500"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="8b8ec-103">ASP.NET Core Web API の応答データの書式設定</span><span class="sxs-lookup"><span data-stu-id="8b8ec-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="8b8ec-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8b8ec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8b8ec-105">ASP.NET Core MVC では、応答データの書式設定がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="8b8ec-106">応答データは、特定の形式を使用して、またはクライアントが要求した形式に応じて書式設定できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="8b8ec-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="8b8ec-108">書式固有アクションの結果</span><span class="sxs-lookup"><span data-stu-id="8b8ec-108">Format-specific Action Results</span></span>

<span data-ttu-id="8b8ec-109">アクションの結果には、<xref:Microsoft.AspNetCore.Mvc.JsonResult> や <xref:Microsoft.AspNetCore.Mvc.ContentResult> のように、特定の形式に固有となる型があります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="8b8ec-110">アクションでは、クライアントの設定に関係なく、特定の形式で書式設定された結果を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="8b8ec-111">たとえば、`JsonResult` を返すと、JSON 形式のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="8b8ec-112">`ContentResult` または文字列を返すと、プレーンテキスト形式の文字列データが返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="8b8ec-113">アクションが特定の型を返す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="8b8ec-114">ASP.NET Core によって、すべてのオブジェクトの戻り値がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="8b8ec-115">型が <xref:Microsoft.AspNetCore.Mvc.IActionResult> ではないオブジェクトを返すアクションからの結果は、適切な <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 実装を利用してシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="8b8ec-116">詳細については、「<xref:web-api/action-return-types>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="8b8ec-117">組み込みヘルパー メソッド <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> では、JSON 形式のデータが返されます。[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="8b8ec-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="8b8ec-118">サンプル ダウンロードでは作成者の一覧が返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="8b8ec-119">F12 ブラウザー開発者ツールまたは [Postman](https://www.getpostman.com/tools) と前のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="8b8ec-120">**content-type:** `application/json; charset=utf-8` を含む応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="8b8ec-121">要求ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-121">The request headers are displayed.</span></span> <span data-ttu-id="8b8ec-122">たとえば、`Accept` ヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-122">For example, the `Accept` header.</span></span> <span data-ttu-id="8b8ec-123">`Accept` ヘッダーは前のコードでは無視されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="8b8ec-124">プレーンテキストで書式設定されたデータを返すには、<xref:Microsoft.AspNetCore.Mvc.ContentResult> と <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> ヘルパーを使用します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="8b8ec-125">前のコードで、返される `Content-Type` は `text/plain` です。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="8b8ec-126">文字列を返すと、`text/plain` の `Content-Type` が送られます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="8b8ec-127">戻り値の型が複数あるアクションの場合は、`IActionResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="8b8ec-128">たとえば、実行された操作の結果に基づいて、さまざまな HTTP 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="8b8ec-129">コンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="8b8ec-129">Content negotiation</span></span>

<span data-ttu-id="8b8ec-130">クライアントにより [Accept ヘッダー](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)が指定されると、コンテンツ ネゴシエーションが発生します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="8b8ec-131">ASP.NET Core で使用される既定の形式は [JSON](https://json.org/) です。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="8b8ec-132">コンテンツ ネゴシエーションの説明を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-132">Content negotiation is:</span></span>

* <span data-ttu-id="8b8ec-133"><xref:Microsoft.AspNetCore.Mvc.ObjectResult> によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="8b8ec-134">ヘルパー メソッドから返されるステータス コード固有のアクションの結果に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="8b8ec-135">アクションの結果のヘルパー メソッドは `ObjectResult` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="8b8ec-136">モデルの型類が返されると、戻り値の型は `ObjectResult` になります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="8b8ec-137">次のアクション メソッドでは、ヘルパー メソッドの `Ok` と `NotFound` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="8b8ec-138">ASP.NET Core では、規定で `application/json`、`text/json`、`text/plain` のメディアの種類がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="8b8ec-139">[Fiddler](https://www.telerik.com/fiddler) や [Postman](https://www.getpostman.com/tools) のようなツールでは、`Accept` 要求ヘッダーを設定して戻り値の形式を指定できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="8b8ec-140">サーバーでサポートされている型が `Accept` ヘッダーに含まれている場合は、その型が返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="8b8ec-141">フォーマッタの追加方法は次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="8b8ec-142">コントローラー アクションは POCO (単純な従来の CLR オブジェクト) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="8b8ec-143">POCO が返されると、ランタイムはそのオブジェクトをラップする `ObjectResult` を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="8b8ec-144">クライアントは、書式設定されたシリアル化オブジェクトを取得します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="8b8ec-145">返されるオブジェクトが `null` の場合は、`204 No Content` という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="8b8ec-146">オブジェクトの型を返す:</span><span class="sxs-lookup"><span data-stu-id="8b8ec-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="8b8ec-147">前のコードでは、有効な作成者エイリアスの要求に対して、`200 OK` という応答と作成者のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="8b8ec-148">無効なエイリアスの要求に対しては、`204 No Content` という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="8b8ec-149">Accept ヘッダー</span><span class="sxs-lookup"><span data-stu-id="8b8ec-149">The Accept header</span></span>

<span data-ttu-id="8b8ec-150">コンテンツ "*ネゴシエーション*" は、`Accept` ヘッダーが要求に含まれるときに発生します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="8b8ec-151">要求に Accept ヘッダーが含まれるとき、ASP.NET Core は次のように処理します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="8b8ec-152">Accept ヘッダーのメディアの種類を優先順で列挙します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="8b8ec-153">指定された形式のいずれかで応答を生成できるフォーマッタを見つけようとします。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="8b8ec-154">クライアントの要求を満たすことができるフォーマッタが見つからない場合は、ASP.NET Core は次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="8b8ec-155">`406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> がに設定されている場合 `true` はを返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> is set to `true`, or -</span></span>
* <span data-ttu-id="8b8ec-156">応答を生成できる最初のフォーマッタを見つけようとします。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="8b8ec-157">要求された形式に対応するフォーマッタが構成されていない場合、オブジェクトを書式設定できる最初のフォーマッタが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="8b8ec-158">要求に `Accept` ヘッダーが表示されない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="8b8ec-159">オブジェクトを処理できる最初のフォーマッタが使用されて、応答をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="8b8ec-160">ネゴシエーションは発生しません。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="8b8ec-161">サーバーが返す形式を決定します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-161">The server is determining what format to return.</span></span>

<span data-ttu-id="8b8ec-162">Accept ヘッダーに `*/*` が含まれる場合、`RespectBrowserAcceptHeader` が <xref:Microsoft.AspNetCore.Mvc.MvcOptions> で true に設定されていない限り、ヘッダーが無視されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="8b8ec-163">ブラウザーとコンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="8b8ec-163">Browsers and content negotiation</span></span>

<span data-ttu-id="8b8ec-164">一般的な API クライアントとは異なり、Web ブラウザーは `Accept` ヘッダーを提供します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="8b8ec-165">Web ブラウザーでは、ワイルドカードを含む多くの形式が指定されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="8b8ec-166">既定では、要求がブラウザーから送信されていることをフレームワークが検出すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="8b8ec-167">`Accept` ヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="8b8ec-168">特に構成されていない限り、コンテンツは JSON で返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="8b8ec-169">このため、API を使用するときにブラウザー間でさらに一貫性の高いエクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="8b8ec-170">ブラウザーの Accept ヘッダーを優先するようにアプリを構成するには、<xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="8b8ec-171">フォーマッタの構成</span><span class="sxs-lookup"><span data-stu-id="8b8ec-171">Configure formatters</span></span>

<span data-ttu-id="8b8ec-172">追加の形式をサポートする必要があるアプリは、適切な NuGet パッケージを追加し、サポートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="8b8ec-173">入力と出力で別々のフォーマッタがあります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="8b8ec-174">入力フォーマッタは、[モデル バインド](xref:mvc/models/model-binding)によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="8b8ec-175">出力フォーマッタは、応答の書式設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="8b8ec-176">カスタム フォーマッタの作成の詳細については、[カスタム フォーマッタ](xref:web-api/advanced/custom-formatters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="8b8ec-177">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="8b8ec-177">Add XML format support</span></span>

<span data-ttu-id="8b8ec-178"><xref:System.Xml.Serialization.XmlSerializer> を使用して実装された XML フォーマッタは、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="8b8ec-179">前のコードは、`XmlSerializer` を使用して結果をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="8b8ec-180">前のコードを使用する場合、コントローラー メソッドは要求の `Accept` ヘッダーに基づいて適切な形式を返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="8b8ec-181">ベースのフォーマッタで System.Text.Jsを構成する</span><span class="sxs-lookup"><span data-stu-id="8b8ec-181">Configure System.Text.Json based formatters</span></span>

<span data-ttu-id="8b8ec-182">ベースのフォーマッタの機能は、 `System.Text.Json` を使用して構成でき <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> ます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-182">Features for the `System.Text.Json` based formatters can be configured using <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName>.</span></span> <span data-ttu-id="8b8ec-183">既定の書式設定はキャメルケースです。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-183">The default formatting is camelCase.</span></span> <span data-ttu-id="8b8ec-184">次の強調表示されたコードは、文字セットの書式を設定します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-184">The following highlighted code sets PascalCase formatting:</span></span>

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="8b8ec-185">次のアクションメソッドは、コントローラーを呼び出して、応答を作成します[。](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) <xref:Microsoft.AspNetCore.Mvc.ProblemDetails></span><span class="sxs-lookup"><span data-stu-id="8b8ec-185">The following action method calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response:</span></span>

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

<span data-ttu-id="8b8ec-186">上記のコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-186">With the preceding code:</span></span>

  * <span data-ttu-id="8b8ec-187">`https://localhost:5001/WeatherForecast/temperature` 文字を返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-187">`https://localhost:5001/WeatherForecast/temperature` returns PascalCase.</span></span>
  * <span data-ttu-id="8b8ec-188">`https://localhost:5001/WeatherForecast/error` キャメルケースを返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-188">`https://localhost:5001/WeatherForecast/error` returns camelCase.</span></span> <span data-ttu-id="8b8ec-189">エラー応答は、アプリが形式をキャメルケースに設定している場合でも、常に "常に" になります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-189">The error response is always camelCase, even when the app sets the format to PascalCase.</span></span> <span data-ttu-id="8b8ec-190">`ProblemDetails`[RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A)に従い、小文字を指定します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-190">`ProblemDetails` follows [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), which specifies lower case</span></span>

<span data-ttu-id="8b8ec-191">次のコードでは、パスワードの大文字と小文字の区別を設定し、カスタムコンバーターを追加しています。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-191">The following code sets PascalCase and adds a custom converter:</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="8b8ec-192">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-192">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="8b8ec-193">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-193">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="8b8ec-194">Newtonsoft.Json ベースの JSON 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="8b8ec-194">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="8b8ec-195">ASP.NET Core 3.0 より前、既定では、`Newtonsoft.Json` パッケージを使用して実装された JSON フォーマッタが使用されていました。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-195">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="8b8ec-196">ASP.NET Core 3.0 以降、既定の JSON フォーマッタは `System.Text.Json` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-196">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="8b8ec-197">ベースの `Newtonsoft.Json` フォーマッタと機能のサポートは、NuGet パッケージをインストールし、で構成することによって利用でき [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-197">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="8b8ec-198">前のコードでは、を呼び出すことで、 `AddNewtonsoftJson` 次の WEB API、MVC、および Pages の各機能を使用するように構成されてい Razor `Newtonsoft.Json` ます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-198">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="8b8ec-199">JSON の読み取りと書き込みを行う入力フォーマッタと出力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="8b8ec-199">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="8b8ec-200">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="8b8ec-200">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="8b8ec-201">TempData</span><span class="sxs-lookup"><span data-stu-id="8b8ec-201">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="8b8ec-202">一部の機能は `System.Text.Json` ベースのフォーマッタでうまく動作せず、`Newtonsoft.Json` ベースのフォーマッタの参照が必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-202">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="8b8ec-203">アプリが以下の場合には、`Newtonsoft.Json` ベースのフォーマッタの使用を続けます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-203">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="8b8ec-204">`Newtonsoft.Json` 属性を使用する。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-204">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="8b8ec-205">たとえば、`[JsonProperty]` または `[JsonIgnore]` です。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-205">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="8b8ec-206">シリアル化の設定をカスタマイズする。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-206">Customizes the serialization settings.</span></span>
* <span data-ttu-id="8b8ec-207">`Newtonsoft.Json` で提供される機能に依存する。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-207">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="8b8ec-208">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` を構成する。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-208">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="8b8ec-209">ASP.NET Core 3.0 より前は、`JsonResult.SerializerSettings`が `Newtonsoft.Json` に固有の `JsonSerializerSettings` のインスタンスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-209">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="8b8ec-210">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) ドキュメントを生成する。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-210">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="8b8ec-211">`Newtonsoft.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-211">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="8b8ec-212">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-212">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="8b8ec-213">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-213">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="8b8ec-214">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="8b8ec-214">Add XML format support</span></span>

<span data-ttu-id="8b8ec-215">XML の書式設定には、[Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-215">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="8b8ec-216"><xref:System.Xml.Serialization.XmlSerializer> を使用して実装された XML フォーマッタは、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-216">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="8b8ec-217">前のコードは、`XmlSerializer` を使用して結果をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-217">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="8b8ec-218">前のコードを使用する場合、コントローラー メソッドは要求の `Accept` ヘッダーに基づいて適切な形式を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-218">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="8b8ec-219">形式を指定する</span><span class="sxs-lookup"><span data-stu-id="8b8ec-219">Specify a format</span></span>

<span data-ttu-id="8b8ec-220">応答形式を制限するには、フィルターを適用し [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) ます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-220">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="8b8ec-221">ほとんどの [フィルター](xref:mvc/controllers/filters)と同様に、 `[Produces]` アクション、コントローラー、またはグローバルスコープで適用できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-221">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="8b8ec-222">上記の [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) フィルター:</span><span class="sxs-lookup"><span data-stu-id="8b8ec-222">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="8b8ec-223">コントローラー内のすべてのアクションが、JSON で書式設定された応答を返すように強制します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-223">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="8b8ec-224">他のフォーマッタが構成されていて、クライアントが別の形式を指定した場合でも、JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-224">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="8b8ec-225">詳細については、[フィルター](xref:mvc/controllers/filters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-225">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="8b8ec-226">特殊なケースのフォーマッタ</span><span class="sxs-lookup"><span data-stu-id="8b8ec-226">Special case formatters</span></span>

<span data-ttu-id="8b8ec-227">一部の特殊なケースが組み込みのフォーマッタで実装されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-227">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="8b8ec-228">既定では、戻り値の型 `string` は *text/plain* として書式設定されます (`Accept` ヘッダー経由で要求された場合は *text/html*)。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-228">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="8b8ec-229">この動作は <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-229">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="8b8ec-230">フォーマッタは `ConfigureServices` メソッドで削除します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-230">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="8b8ec-231">戻り値の型としてモデル オブジェクトをともなうアクションは、`null` を返すとき、`204 No Content` を返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-231">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="8b8ec-232">この動作は <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-232">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="8b8ec-233">次のコードでは、`StringOutputFormatter` と `HttpNoContentOutputFormatter` が削除されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-233">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="8b8ec-234">`StringOutputFormatter` がない場合は、組み込みの JSON フォーマッタによって戻り値の型 `string` が書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-234">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="8b8ec-235">組み込みの JSON フォーマッタが削除され、XML フォーマッタを使用できる場合は、XML フォーマッタによって戻り値の型 `string` が書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-235">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="8b8ec-236">それ以外の場合は、戻り値の型 `string` で `406 Not Acceptable` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-236">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="8b8ec-237">`HttpNoContentOutputFormatter` がない場合、構成されているフォーマッタを利用し、null オブジェクトが書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-237">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="8b8ec-238">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-238">For example:</span></span>

* <span data-ttu-id="8b8ec-239">JSON フォーマッタは、本文が `null` の応答を返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-239">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="8b8ec-240">XML フォーマッタは、属性 `xsi:nil="true"` が設定された空の XML 要素を返します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-240">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="8b8ec-241">応答形式の URL マッピング</span><span class="sxs-lookup"><span data-stu-id="8b8ec-241">Response format URL mappings</span></span>

<span data-ttu-id="8b8ec-242">クライアントは、URL の一部として特定の形式を要求できます。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-242">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="8b8ec-243">クエリ文字列またはパスの一部。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-243">In the query string or part of the path.</span></span>
* <span data-ttu-id="8b8ec-244">.xml または .json など形式固有のファイル拡張子の使用。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-244">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="8b8ec-245">要求パスからのマッピングは、API で使用されるルートに指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-245">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="8b8ec-246">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-246">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="8b8ec-247">前のルートを使用すると、要求された形式をオプションのファイル拡張子として指定できます。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-247">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="8b8ec-248">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)属性は、の format 値が存在するかどうかをチェック `RouteData` し、応答が作成されるときに応答形式を適切なフォーマッタにマップします。</span><span class="sxs-lookup"><span data-stu-id="8b8ec-248">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="8b8ec-249">ルート</span><span class="sxs-lookup"><span data-stu-id="8b8ec-249">Route</span></span>        |             <span data-ttu-id="8b8ec-250">フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="8b8ec-250">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="8b8ec-251">既定の出力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="8b8ec-251">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="8b8ec-252">JSON フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="8b8ec-252">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="8b8ec-253">XML フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="8b8ec-253">The XML formatter (if configured)</span></span>  |

---
title: ASP.NET Core Web API の応答データの書式設定
author: ardalis
description: ASP.NET Core Web API で応答データを書式設定する方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 89e3e51373db5f7cff974b7a8c69d06bedf856ca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052514"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="7e9d7-103">ASP.NET Core Web API の応答データの書式設定</span><span class="sxs-lookup"><span data-stu-id="7e9d7-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="7e9d7-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7e9d7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7e9d7-105">ASP.NET Core MVC では、応答データの書式設定がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="7e9d7-106">応答データは、特定の形式を使用して、またはクライアントが要求した形式に応じて書式設定できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="7e9d7-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="7e9d7-108">書式固有アクションの結果</span><span class="sxs-lookup"><span data-stu-id="7e9d7-108">Format-specific Action Results</span></span>

<span data-ttu-id="7e9d7-109">アクションの結果には、<xref:Microsoft.AspNetCore.Mvc.JsonResult> や <xref:Microsoft.AspNetCore.Mvc.ContentResult> のように、特定の形式に固有となる型があります。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="7e9d7-110">アクションでは、クライアントの設定に関係なく、特定の形式で書式設定された結果を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="7e9d7-111">たとえば、`JsonResult` を返すと、JSON 形式のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="7e9d7-112">`ContentResult` または文字列を返すと、プレーンテキスト形式の文字列データが返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="7e9d7-113">アクションが特定の型を返す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="7e9d7-114">ASP.NET Core によって、すべてのオブジェクトの戻り値がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="7e9d7-115">型が <xref:Microsoft.AspNetCore.Mvc.IActionResult> ではないオブジェクトを返すアクションからの結果は、適切な <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 実装を利用してシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="7e9d7-116">詳細については、「<xref:web-api/action-return-types>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="7e9d7-117">組み込みヘルパー メソッド <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> では、JSON 形式のデータが返されます。[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="7e9d7-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="7e9d7-118">サンプル ダウンロードでは作成者の一覧が返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="7e9d7-119">F12 ブラウザー開発者ツールまたは [Postman](https://www.getpostman.com/tools) と前のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="7e9d7-120">**content-type:** `application/json; charset=utf-8` を含む応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="7e9d7-121">要求ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-121">The request headers are displayed.</span></span> <span data-ttu-id="7e9d7-122">たとえば、`Accept` ヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-122">For example, the `Accept` header.</span></span> <span data-ttu-id="7e9d7-123">`Accept` ヘッダーは前のコードでは無視されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="7e9d7-124">プレーンテキストで書式設定されたデータを返すには、<xref:Microsoft.AspNetCore.Mvc.ContentResult> と <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> ヘルパーを使用します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="7e9d7-125">前のコードで、返される `Content-Type` は `text/plain` です。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="7e9d7-126">文字列を返すと、`text/plain` の `Content-Type` が送られます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="7e9d7-127">戻り値の型が複数あるアクションの場合は、`IActionResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="7e9d7-128">たとえば、実行された操作の結果に基づいて、さまざまな HTTP 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="7e9d7-129">コンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="7e9d7-129">Content negotiation</span></span>

<span data-ttu-id="7e9d7-130">クライアントにより [Accept ヘッダー](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)が指定されると、コンテンツ ネゴシエーションが発生します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="7e9d7-131">ASP.NET Core で使用される既定の形式は [JSON](https://json.org/) です。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="7e9d7-132">コンテンツ ネゴシエーションの説明を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-132">Content negotiation is:</span></span>

* <span data-ttu-id="7e9d7-133"><xref:Microsoft.AspNetCore.Mvc.ObjectResult> によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="7e9d7-134">ヘルパー メソッドから返されるステータス コード固有のアクションの結果に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="7e9d7-135">アクションの結果のヘルパー メソッドは `ObjectResult` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="7e9d7-136">モデルの型類が返されると、戻り値の型は `ObjectResult` になります。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="7e9d7-137">次のアクション メソッドでは、ヘルパー メソッドの `Ok` と `NotFound` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="7e9d7-138">ASP.NET Core では、規定で `application/json`、`text/json`、`text/plain` のメディアの種類がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="7e9d7-139">[Fiddler](https://www.telerik.com/fiddler) や [Postman](https://www.getpostman.com/tools) のようなツールでは、`Accept` 要求ヘッダーを設定して戻り値の形式を指定できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="7e9d7-140">サーバーでサポートされている型が `Accept` ヘッダーに含まれている場合は、その型が返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="7e9d7-141">フォーマッタの追加方法は次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="7e9d7-142">コントローラー アクションは POCO (単純な従来の CLR オブジェクト) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="7e9d7-143">POCO が返されると、ランタイムはそのオブジェクトをラップする `ObjectResult` を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="7e9d7-144">クライアントは、書式設定されたシリアル化オブジェクトを取得します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="7e9d7-145">返されるオブジェクトが `null` の場合は、`204 No Content` という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="7e9d7-146">オブジェクトの型を返す:</span><span class="sxs-lookup"><span data-stu-id="7e9d7-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="7e9d7-147">前のコードでは、有効な作成者エイリアスの要求に対して、`200 OK` という応答と作成者のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="7e9d7-148">無効なエイリアスの要求に対しては、`204 No Content` という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="7e9d7-149">Accept ヘッダー</span><span class="sxs-lookup"><span data-stu-id="7e9d7-149">The Accept header</span></span>

<span data-ttu-id="7e9d7-150">コンテンツ " *ネゴシエーション* " は、`Accept` ヘッダーが要求に含まれるときに発生します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="7e9d7-151">要求に Accept ヘッダーが含まれるとき、ASP.NET Core は次のように処理します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="7e9d7-152">Accept ヘッダーのメディアの種類を優先順で列挙します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="7e9d7-153">指定された形式のいずれかで応答を生成できるフォーマッタを見つけようとします。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="7e9d7-154">クライアントの要求を満たすことができるフォーマッタが見つからない場合は、ASP.NET Core は次のようにします。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="7e9d7-155"><xref:Microsoft.AspNetCore.Mvc.MvcOptions> が設定されていた場合は、`406 Not Acceptable` を返します。それ以外の場合は次のようにします。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="7e9d7-156">応答を生成できる最初のフォーマッタを見つけようとします。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="7e9d7-157">要求された形式に対応するフォーマッタが構成されていない場合、オブジェクトを書式設定できる最初のフォーマッタが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="7e9d7-158">要求に `Accept` ヘッダーが表示されない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="7e9d7-159">オブジェクトを処理できる最初のフォーマッタが使用されて、応答をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="7e9d7-160">ネゴシエーションは発生しません。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="7e9d7-161">サーバーが返す形式を決定します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-161">The server is determining what format to return.</span></span>

<span data-ttu-id="7e9d7-162">Accept ヘッダーに `*/*` が含まれる場合、`RespectBrowserAcceptHeader` が <xref:Microsoft.AspNetCore.Mvc.MvcOptions> で true に設定されていない限り、ヘッダーが無視されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="7e9d7-163">ブラウザーとコンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="7e9d7-163">Browsers and content negotiation</span></span>

<span data-ttu-id="7e9d7-164">一般的な API クライアントとは異なり、Web ブラウザーは `Accept` ヘッダーを提供します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="7e9d7-165">Web ブラウザーでは、ワイルドカードを含む多くの形式が指定されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="7e9d7-166">既定では、要求がブラウザーから送信されていることをフレームワークが検出すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="7e9d7-167">`Accept` ヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="7e9d7-168">特に構成されていない限り、コンテンツは JSON で返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="7e9d7-169">このため、API を使用するときにブラウザー間でさらに一貫性の高いエクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="7e9d7-170">ブラウザーの Accept ヘッダーを優先するようにアプリを構成するには、<xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="7e9d7-171">フォーマッタの構成</span><span class="sxs-lookup"><span data-stu-id="7e9d7-171">Configure formatters</span></span>

<span data-ttu-id="7e9d7-172">追加の形式をサポートする必要があるアプリは、適切な NuGet パッケージを追加し、サポートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="7e9d7-173">入力と出力で別々のフォーマッタがあります。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="7e9d7-174">入力フォーマッタは、[モデル バインド](xref:mvc/models/model-binding)によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="7e9d7-175">出力フォーマッタは、応答の書式設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="7e9d7-176">カスタム フォーマッタの作成の詳細については、[カスタム フォーマッタ](xref:web-api/advanced/custom-formatters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="7e9d7-177">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="7e9d7-177">Add XML format support</span></span>

<span data-ttu-id="7e9d7-178"><xref:System.Xml.Serialization.XmlSerializer> を使用して実装された XML フォーマッタは、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="7e9d7-179">前のコードは、`XmlSerializer` を使用して結果をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="7e9d7-180">前のコードを使用する場合、コントローラー メソッドは要求の `Accept` ヘッダーに基づいて適切な形式を返します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="7e9d7-181">System.Text.Json ベースのフォーマッタを構成する</span><span class="sxs-lookup"><span data-stu-id="7e9d7-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="7e9d7-182">`System.Text.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="7e9d7-183">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="7e9d7-184">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="7e9d7-185">Newtonsoft.Json ベースの JSON 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="7e9d7-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="7e9d7-186">ASP.NET Core 3.0 より前、既定では、`Newtonsoft.Json` パッケージを使用して実装された JSON フォーマッタが使用されていました。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="7e9d7-187">ASP.NET Core 3.0 以降、既定の JSON フォーマッタは `System.Text.Json` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="7e9d7-188">ベースの `Newtonsoft.Json` フォーマッタと機能のサポートは、NuGet パッケージをインストールし、で構成することによって利用でき [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="7e9d7-189">前のコードでは、を呼び出すことで、 `AddNewtonsoftJson` 次の WEB API、MVC、および Pages の各機能を使用するように構成されてい :::no-loc(Razor)::: `Newtonsoft.Json` ます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-189">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and :::no-loc(Razor)::: Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="7e9d7-190">JSON の読み取りと書き込みを行う入力フォーマッタと出力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="7e9d7-190">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="7e9d7-191">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="7e9d7-191">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="7e9d7-192">TempData</span><span class="sxs-lookup"><span data-stu-id="7e9d7-192">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="7e9d7-193">一部の機能は `System.Text.Json` ベースのフォーマッタでうまく動作せず、`Newtonsoft.Json` ベースのフォーマッタの参照が必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="7e9d7-194">アプリが以下の場合には、`Newtonsoft.Json` ベースのフォーマッタの使用を続けます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="7e9d7-195">`Newtonsoft.Json` 属性を使用する。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="7e9d7-196">たとえば、`[JsonProperty]` または `[JsonIgnore]` です。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="7e9d7-197">シリアル化の設定をカスタマイズする。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="7e9d7-198">`Newtonsoft.Json` で提供される機能に依存する。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="7e9d7-199">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` を構成する。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="7e9d7-200">ASP.NET Core 3.0 より前は、`JsonResult.SerializerSettings`が `Newtonsoft.Json` に固有の `JsonSerializerSettings` のインスタンスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="7e9d7-201">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) ドキュメントを生成する。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="7e9d7-202">`Newtonsoft.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="7e9d7-203">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="7e9d7-204">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-204">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="7e9d7-205">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="7e9d7-205">Add XML format support</span></span>

<span data-ttu-id="7e9d7-206">XML の書式設定には、[Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="7e9d7-207"><xref:System.Xml.Serialization.XmlSerializer> を使用して実装された XML フォーマッタは、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="7e9d7-208">前のコードは、`XmlSerializer` を使用して結果をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="7e9d7-209">前のコードを使用する場合、コントローラー メソッドは要求の `Accept` ヘッダーに基づいて適切な形式を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="7e9d7-210">形式を指定する</span><span class="sxs-lookup"><span data-stu-id="7e9d7-210">Specify a format</span></span>

<span data-ttu-id="7e9d7-211">応答形式を制限するには、フィルターを適用し [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) ます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="7e9d7-212">ほとんどの [フィルター](xref:mvc/controllers/filters)と同様に、 `[Produces]` アクション、コントローラー、またはグローバルスコープで適用できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="7e9d7-213">上記の [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) フィルター:</span><span class="sxs-lookup"><span data-stu-id="7e9d7-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="7e9d7-214">コントローラー内のすべてのアクションが、JSON で書式設定された応答を返すように強制します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="7e9d7-215">他のフォーマッタが構成されていて、クライアントが別の形式を指定した場合でも、JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="7e9d7-216">詳細については、[フィルター](xref:mvc/controllers/filters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="7e9d7-217">特殊なケースのフォーマッタ</span><span class="sxs-lookup"><span data-stu-id="7e9d7-217">Special case formatters</span></span>

<span data-ttu-id="7e9d7-218">一部の特殊なケースが組み込みのフォーマッタで実装されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="7e9d7-219">既定では、戻り値の型 `string` は *text/plain* として書式設定されます (`Accept` ヘッダー経由で要求された場合は *text/html* )。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-219">By default, `string` return types are formatted as *text/plain* ( *text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="7e9d7-220">この動作は <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="7e9d7-221">フォーマッタは `ConfigureServices` メソッドで削除します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="7e9d7-222">戻り値の型としてモデル オブジェクトをともなうアクションは、`null` を返すとき、`204 No Content` を返します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="7e9d7-223">この動作は <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="7e9d7-224">次のコードでは、`StringOutputFormatter` と `HttpNoContentOutputFormatter` が削除されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="7e9d7-225">`StringOutputFormatter` がない場合は、組み込みの JSON フォーマッタによって戻り値の型 `string` が書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="7e9d7-226">組み込みの JSON フォーマッタが削除され、XML フォーマッタを使用できる場合は、XML フォーマッタによって戻り値の型 `string` が書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="7e9d7-227">それ以外の場合は、戻り値の型 `string` で `406 Not Acceptable` が返されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="7e9d7-228">`HttpNoContentOutputFormatter` がない場合、構成されているフォーマッタを利用し、null オブジェクトが書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="7e9d7-229">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-229">For example:</span></span>

* <span data-ttu-id="7e9d7-230">JSON フォーマッタは、本文が `null` の応答を返します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="7e9d7-231">XML フォーマッタは、属性 `xsi:nil="true"` が設定された空の XML 要素を返します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="7e9d7-232">応答形式の URL マッピング</span><span class="sxs-lookup"><span data-stu-id="7e9d7-232">Response format URL mappings</span></span>

<span data-ttu-id="7e9d7-233">クライアントは、URL の一部として特定の形式を要求できます。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="7e9d7-234">クエリ文字列またはパスの一部。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="7e9d7-235">.xml または .json など形式固有のファイル拡張子の使用。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="7e9d7-236">要求パスからのマッピングは、API で使用されるルートに指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="7e9d7-237">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="7e9d7-238">前のルートを使用すると、要求された形式をオプションのファイル拡張子として指定できます。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="7e9d7-239">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)属性は、の format 値が存在するかどうかをチェック `RouteData` し、応答が作成されるときに応答形式を適切なフォーマッタにマップします。</span><span class="sxs-lookup"><span data-stu-id="7e9d7-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="7e9d7-240">ルート</span><span class="sxs-lookup"><span data-stu-id="7e9d7-240">Route</span></span>        |             <span data-ttu-id="7e9d7-241">フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="7e9d7-241">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="7e9d7-242">既定の出力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="7e9d7-242">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="7e9d7-243">JSON フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="7e9d7-243">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="7e9d7-244">XML フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="7e9d7-244">The XML formatter (if configured)</span></span>  |

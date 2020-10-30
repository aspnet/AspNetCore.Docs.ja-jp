---
title: ASP.NET Core での応答キャッシュミドルウェア
author: rick-anderson
description: ASP.NET Core で応答キャッシュ ミドルウェアを構成し、使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/middleware
ms.openlocfilehash: 3c28b6c736f07c0d0483152eeec4300a5a92224c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052111"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="897a9-103">ASP.NET Core での応答キャッシュミドルウェア</span><span class="sxs-lookup"><span data-stu-id="897a9-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="897a9-104">作成者: [John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="897a9-104">By [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="897a9-105">この記事では、ASP.NET Core アプリで応答キャッシュミドルウェアを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="897a9-105">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="897a9-106">ミドルウェアは、応答をキャッシュ可能にするタイミングを決定し、応答を格納して、キャッシュからの応答を提供します。</span><span class="sxs-lookup"><span data-stu-id="897a9-106">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="897a9-107">HTTP キャッシュと属性の概要につい [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ては、「 [応答キャッシュ](xref:performance/caching/response)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-107">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="897a9-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="897a9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="897a9-109">構成</span><span class="sxs-lookup"><span data-stu-id="897a9-109">Configuration</span></span>

<span data-ttu-id="897a9-110">応答キャッシュミドルウェアは、共有フレームワークを介して ASP.NET Core アプリで暗黙的に使用できます。</span><span class="sxs-lookup"><span data-stu-id="897a9-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="897a9-111">で `Startup.ConfigureServices` 、応答キャッシュミドルウェアをサービスコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="897a9-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="897a9-112">拡張メソッドと共にミドルウェアを使用するようにアプリを構成し <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> ます。これにより、の要求処理パイプラインにミドルウェアが追加され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <span data-ttu-id="897a9-113"><xref:Owin.CorsExtensions.UseCors%2A><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> [CORS ミドルウェア](xref:security/cors)を使用する場合は、前にを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-113"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using [CORS middleware](xref:security/cors).</span></span>

<span data-ttu-id="897a9-114">サンプルアプリでは、後続の要求でキャッシュを制御するためのヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="897a9-114">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="897a9-115">[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2): キャッシュ可能な応答を最大10秒間キャッシュします。</span><span class="sxs-lookup"><span data-stu-id="897a9-115">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="897a9-116">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): 後続の要求の [エンコーディング](https://tools.ietf.org/html/rfc7231#section-5.3.4) ヘッダーが元の要求と一致する場合にのみ、キャッシュされた応答を提供するようにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="897a9-116">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="897a9-117">前のヘッダーは応答に書き込まれず、コントローラー、アクション、またはページでオーバーライドされ :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-117">The preceding headers are not written to the response and are overridden when a controller, action, or :::no-loc(Razor)::: Page:</span></span>

* <span data-ttu-id="897a9-118">には [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-118">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="897a9-119">これは、プロパティが設定されていない場合でも適用されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-119">This applies even if a property isn't set.</span></span> <span data-ttu-id="897a9-120">たとえば、 [VaryByHeader](./response.md#vary) プロパティを省略すると、対応するヘッダーが応答から削除されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-120">For example, omitting the [VaryByHeader](./response.md#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="897a9-121">応答キャッシュミドルウェアは、200 (OK) 状態コードを生成するサーバー応答のみをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="897a9-121">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="897a9-122">[エラーページ](xref:fundamentals/error-handling)を含むその他の応答は、ミドルウェアによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-122">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="897a9-123">認証されたクライアントのコンテンツを含む応答は、ミドルウェアがそれらの応答を格納してサービスを提供しないように、キャッシュ不可能としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-123">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="897a9-124">応答がキャッシュ可能かどうかをミドルウェアが決定する方法の詳細については、「 [キャッシュの条件](#conditions-for-caching) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-124">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="897a9-125">オプション</span><span class="sxs-lookup"><span data-stu-id="897a9-125">Options</span></span>

<span data-ttu-id="897a9-126">応答キャッシュのオプションを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="897a9-126">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="897a9-127">オプション</span><span class="sxs-lookup"><span data-stu-id="897a9-127">Option</span></span> | <span data-ttu-id="897a9-128">[説明]</span><span class="sxs-lookup"><span data-stu-id="897a9-128">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="897a9-129">応答本文の最大キャッシュ可能サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="897a9-129">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="897a9-130">既定値は `64 * 1024 * 1024` (64 MB) です。</span><span class="sxs-lookup"><span data-stu-id="897a9-130">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="897a9-131">応答キャッシュミドルウェアのサイズ制限 (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="897a9-131">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="897a9-132">既定値は `100 * 1024 * 1024` (100 MB) です。</span><span class="sxs-lookup"><span data-stu-id="897a9-132">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="897a9-133">大文字と小文字が区別されるパスに応答をキャッシュするかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="897a9-133">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="897a9-134">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="897a9-134">The default value is `false`.</span></span> |

<span data-ttu-id="897a9-135">次の例では、ミドルウェアをに構成します。</span><span class="sxs-lookup"><span data-stu-id="897a9-135">The following example configures the middleware to:</span></span>

* <span data-ttu-id="897a9-136">本文のサイズが1024バイト以下の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="897a9-136">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="897a9-137">大文字と小文字を区別するパスで応答を格納します。</span><span class="sxs-lookup"><span data-stu-id="897a9-137">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="897a9-138">たとえば、 `/page1` と `/Page1` は別々に格納されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-138">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="897a9-139">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="897a9-139">VaryByQueryKeys</span></span>

<span data-ttu-id="897a9-140">MVC/web API コントローラーまたは :::no-loc(Razor)::: ページのページモデルを使用する場合、属性は、 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 応答のキャッシュに適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="897a9-140">When using MVC / web API controllers or :::no-loc(Razor)::: Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="897a9-141">ミドルウェアを厳密に必要とする属性の唯一のパラメーター `[ResponseCache]` は <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> 、実際の HTTP ヘッダーに対応していません。</span><span class="sxs-lookup"><span data-stu-id="897a9-141">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="897a9-142">詳細については、「<xref:performance/caching/response#responsecache-attribute>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-142">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="897a9-143">属性を使用しない場合は、を使用して `[ResponseCache]` 応答のキャッシュを変化させることができ `VaryByQueryKeys` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-143">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="897a9-144">を <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> HttpContext から直接使用し[ます。](xref:Microsoft.AspNetCore.Http.HttpContext.Features)</span><span class="sxs-lookup"><span data-stu-id="897a9-144">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="897a9-145">で1つの値を使用すると `*` `VaryByQueryKeys` 、すべての要求クエリパラメーターによってキャッシュが異なります。</span><span class="sxs-lookup"><span data-stu-id="897a9-145">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="897a9-146">応答キャッシュミドルウェアによって使用される HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="897a9-146">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="897a9-147">次の表は、応答のキャッシュに影響を与える HTTP ヘッダーに関する情報を示しています。</span><span class="sxs-lookup"><span data-stu-id="897a9-147">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="897a9-148">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="897a9-148">Header</span></span> | <span data-ttu-id="897a9-149">詳細</span><span class="sxs-lookup"><span data-stu-id="897a9-149">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="897a9-150">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="897a9-150">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="897a9-151">ミドルウェアは、cache ディレクティブでマークされたキャッシュ応答のみを考慮し `public` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-151">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="897a9-152">次のパラメーターを使用してキャッシュを制御します。</span><span class="sxs-lookup"><span data-stu-id="897a9-152">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="897a9-153">最長有効期間</span><span class="sxs-lookup"><span data-stu-id="897a9-153">max-age</span></span></li><li><span data-ttu-id="897a9-154">最大-古い&#8224;</span><span class="sxs-lookup"><span data-stu-id="897a9-154">max-stale&#8224;</span></span></li><li><span data-ttu-id="897a9-155">最小-新規</span><span class="sxs-lookup"><span data-stu-id="897a9-155">min-fresh</span></span></li><li><span data-ttu-id="897a9-156">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="897a9-156">must-revalidate</span></span></li><li><span data-ttu-id="897a9-157">no-cache</span><span class="sxs-lookup"><span data-stu-id="897a9-157">no-cache</span></span></li><li><span data-ttu-id="897a9-158">ストアなし</span><span class="sxs-lookup"><span data-stu-id="897a9-158">no-store</span></span></li><li><span data-ttu-id="897a9-159">-if-キャッシュ済み</span><span class="sxs-lookup"><span data-stu-id="897a9-159">only-if-cached</span></span></li><li><span data-ttu-id="897a9-160">プライベート</span><span class="sxs-lookup"><span data-stu-id="897a9-160">private</span></span></li><li><span data-ttu-id="897a9-161">public</span><span class="sxs-lookup"><span data-stu-id="897a9-161">public</span></span></li><li><span data-ttu-id="897a9-162">s-maxage</span><span class="sxs-lookup"><span data-stu-id="897a9-162">s-maxage</span></span></li><li><span data-ttu-id="897a9-163">プロキシ再検証&#8225;</span><span class="sxs-lookup"><span data-stu-id="897a9-163">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="897a9-164">&#8224;に制限が指定されていない場合 `max-stale` 、ミドルウェアは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="897a9-164">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="897a9-165">&#8225;`proxy-revalidate` はと同じ効果があり `must-revalidate` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-165">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="897a9-166">詳細については、「 [RFC 7231: Request Cache-Control ディレクティブ](https://tools.ietf.org/html/rfc7234#section-5.2.1)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-166">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="897a9-167">要求のヘッダーでは、 `Pragma: no-cache` と同じ効果が得られ `Cache-Control: no-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-167">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="897a9-168">このヘッダーは、ヘッダー内の関連するディレクティブによってオーバーライドされ `Cache-Control` ます (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="897a9-168">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="897a9-169">HTTP/1.0 との下位互換性のために考慮されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-169">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-:::no-loc(Cookie):::` | <span data-ttu-id="897a9-170">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="897a9-170">The response isn't cached if the header exists.</span></span> <span data-ttu-id="897a9-171">1つ以上のを設定する要求処理パイプライン内のすべてのミドルウェア :::no-loc(cookie)::: は、応答キャッシュミドルウェアが応答をキャッシュしないようにします (たとえば、 [ :::no-loc(cookie)::: ベースの tempdata プロバイダー](xref:fundamentals/app-state#tempdata))。</span><span class="sxs-lookup"><span data-stu-id="897a9-171">Any middleware in the request processing pipeline that sets one or more :::no-loc(cookie):::s prevents the Response Caching Middleware from caching the response (for example, the [:::no-loc(cookie):::-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="897a9-172">`Vary`ヘッダーは、キャッシュされた応答を別のヘッダーによって変更するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-172">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="897a9-173">たとえば、ヘッダーを含めることによって、エンコードによって応答をキャッシュし `Vary: Accept-Encoding` ます。ヘッダーとは別に要求の応答をキャッシュし `Accept-Encoding: gzip` `Accept-Encoding: text/plain` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-173">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="897a9-174">ヘッダー値がの応答 `*` は格納されません。</span><span class="sxs-lookup"><span data-stu-id="897a9-174">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="897a9-175">このヘッダーによって古いと見なされる応答は、他のヘッダーでオーバーライドされない限り、格納または取得されません `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="897a9-175">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="897a9-176">値がではなく、 `*` 応答のが指定された値と一致しない場合は、完全な応答がキャッシュから提供され `ETag` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-176">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="897a9-177">それ以外の場合は、304 (変更なし) の応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-177">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="897a9-178">ヘッダーが存在しない場合、キャッシュされた `If-None-Match` 応答の日付が指定した値より新しい場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-178">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="897a9-179">それ以外の場合は、 *304-変更されていない* 応答が提供されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-179">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="897a9-180">キャッシュからサービスを提供している場合、 `Date` ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="897a9-180">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="897a9-181">キャッシュからサービスを提供している場合、 `Content-Length` ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="897a9-181">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="897a9-182">`Age`元の応答で送信されたヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-182">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="897a9-183">ミドルウェアは、キャッシュされた応答を提供するときに新しい値を計算します。</span><span class="sxs-lookup"><span data-stu-id="897a9-183">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="897a9-184">キャッシュは要求 Cache-Control ディレクティブを尊重します</span><span class="sxs-lookup"><span data-stu-id="897a9-184">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="897a9-185">ミドルウェアは、 [HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)の規則を尊重します。</span><span class="sxs-lookup"><span data-stu-id="897a9-185">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="897a9-186">規則では、 `Cache-Control` クライアントによって送信された有効なヘッダーを受け入れるためにキャッシュが必要です。</span><span class="sxs-lookup"><span data-stu-id="897a9-186">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="897a9-187">この仕様では、クライアントはヘッダー値を使用して要求を行い、 `no-cache` すべての要求に対してサーバーに新しい応答を強制的に生成させることができます。</span><span class="sxs-lookup"><span data-stu-id="897a9-187">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="897a9-188">現時点では、ミドルウェアが公式のキャッシュ仕様に準拠しているため、ミドルウェアを使用する場合、このキャッシュ動作を開発者が制御することはありません。</span><span class="sxs-lookup"><span data-stu-id="897a9-188">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="897a9-189">キャッシュの動作を詳細に制御するには、ASP.NET Core の他のキャッシュ機能を調べます。</span><span class="sxs-lookup"><span data-stu-id="897a9-189">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="897a9-190">次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-190">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="897a9-191">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="897a9-191">Troubleshooting</span></span>

<span data-ttu-id="897a9-192">キャッシュの動作が想定どおりでない場合は、応答がキャッシュ可能であり、キャッシュから提供できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="897a9-192">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="897a9-193">要求の受信ヘッダーと応答の送信ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="897a9-193">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="897a9-194">デバッグに役立つように [ログ記録](xref:fundamentals/logging/index) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="897a9-194">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="897a9-195">キャッシュ動作のテストとトラブルシューティングを行う場合、ブラウザーでは、望ましくない方法でキャッシュに影響を与える要求ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="897a9-195">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="897a9-196">たとえば、ページを更新するときに、ブラウザーで `Cache-Control` ヘッダーをまたはに設定でき `no-cache` `max-age=0` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-196">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="897a9-197">次のツールでは、要求ヘッダーを明示的に設定でき、キャッシュのテストに適しています。</span><span class="sxs-lookup"><span data-stu-id="897a9-197">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="897a9-198">Fiddler</span><span class="sxs-lookup"><span data-stu-id="897a9-198">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="897a9-199">Postman</span><span class="sxs-lookup"><span data-stu-id="897a9-199">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="897a9-200">キャッシュの条件</span><span class="sxs-lookup"><span data-stu-id="897a9-200">Conditions for caching</span></span>

* <span data-ttu-id="897a9-201">要求は、200 (OK) 状態コードでサーバー応答を生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-201">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="897a9-202">要求メソッドは GET または HEAD である必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-202">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="897a9-203">では `Startup.Configure` 、応答キャッシュミドルウェアは、キャッシュを必要とするミドルウェアの前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-203">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="897a9-204">詳細については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-204">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="897a9-205">ヘッダーを指定することはでき `Authorization` ません。</span><span class="sxs-lookup"><span data-stu-id="897a9-205">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="897a9-206">`Cache-Control` ヘッダーパラメーターは有効である必要があり、応答はとマークされている必要があり `public` `private` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-206">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="897a9-207">ヘッダーが存在する場合、ヘッダーはヘッダーをオーバーライドするので、ヘッダーが存在しない場合はヘッダーが `Pragma: no-cache` 存在しない必要があり `Cache-Control` `Cache-Control` `Pragma` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-207">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="897a9-208">ヘッダーを指定することはでき `Set-:::no-loc(Cookie):::` ません。</span><span class="sxs-lookup"><span data-stu-id="897a9-208">The `Set-:::no-loc(Cookie):::` header must not be present.</span></span>
* <span data-ttu-id="897a9-209">`Vary` ヘッダーパラメーターはと同じである必要があり `*` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-209">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="897a9-210">`Content-Length`ヘッダー値 (設定されている場合) は、応答本文のサイズと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-210">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="897a9-211">は <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 使用されません。</span><span class="sxs-lookup"><span data-stu-id="897a9-211">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="897a9-212">`Expires`ヘッダー、および、およびの各 `max-age` キャッシュディレクティブで指定されているとおり、応答を古いものにすることはできません `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="897a9-212">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="897a9-213">応答バッファリングを成功させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-213">Response buffering must be successful.</span></span> <span data-ttu-id="897a9-214">応答のサイズは、構成済みまたは既定値よりも小さくする必要があり <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-214">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="897a9-215">応答の本文のサイズは、構成済みまたは既定値よりも小さくする必要があり <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-215">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="897a9-216">応答は、 [RFC 7234](https://tools.ietf.org/html/rfc7234) 仕様に従ってキャッシュ可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-216">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="897a9-217">たとえば、ディレクティブは、 `no-store` 要求または応答のヘッダーフィールドに存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="897a9-217">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="897a9-218">詳細については、 *「セクション 3:* [RFC 7234](https://tools.ietf.org/html/rfc7234) のキャッシュに応答を格納する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-218">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="897a9-219">クロスサイト要求偽造 (CSRF) 攻撃を防ぐために、セキュリティで保護されたトークンを生成するための偽造防止システムは、 `Cache-Control` `Pragma` 応答がキャッシュされないようにヘッダーとヘッダーをに設定し `no-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-219">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="897a9-220">HTML フォーム要素の偽造防止トークンを無効にする方法については、「」を参照してください <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="897a9-220">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="897a9-221">その他の資料</span><span class="sxs-lookup"><span data-stu-id="897a9-221">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="897a9-222">この記事では、ASP.NET Core アプリで応答キャッシュミドルウェアを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="897a9-222">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="897a9-223">ミドルウェアは、応答をキャッシュ可能にするタイミングを決定し、応答を格納して、キャッシュからの応答を提供します。</span><span class="sxs-lookup"><span data-stu-id="897a9-223">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="897a9-224">HTTP キャッシュと属性の概要につい [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ては、「 [応答キャッシュ](xref:performance/caching/response)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-224">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="897a9-225">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="897a9-225">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="897a9-226">構成</span><span class="sxs-lookup"><span data-stu-id="897a9-226">Configuration</span></span>

<span data-ttu-id="897a9-227">[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を使用するか、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="897a9-227">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="897a9-228">で `Startup.ConfigureServices` 、応答キャッシュミドルウェアをサービスコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="897a9-228">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="897a9-229">拡張メソッドと共にミドルウェアを使用するようにアプリを構成し <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> ます。これにより、の要求処理パイプラインにミドルウェアが追加され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-229">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="897a9-230">サンプルアプリでは、後続の要求でキャッシュを制御するためのヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="897a9-230">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="897a9-231">[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2): キャッシュ可能な応答を最大10秒間キャッシュします。</span><span class="sxs-lookup"><span data-stu-id="897a9-231">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="897a9-232">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): 後続の要求の [エンコーディング](https://tools.ietf.org/html/rfc7231#section-5.3.4) ヘッダーが元の要求と一致する場合にのみ、キャッシュされた応答を提供するようにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="897a9-232">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="897a9-233">前のヘッダーは応答に書き込まれず、コントローラー、アクション、またはページでオーバーライドされ :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-233">The preceding headers are not written to the response and are overridden when a controller, action, or :::no-loc(Razor)::: Page:</span></span>

* <span data-ttu-id="897a9-234">には [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-234">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="897a9-235">これは、プロパティが設定されていない場合でも適用されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-235">This applies even if a property isn't set.</span></span> <span data-ttu-id="897a9-236">たとえば、 [VaryByHeader](./response.md#vary) プロパティを省略すると、対応するヘッダーが応答から削除されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-236">For example, omitting the [VaryByHeader](./response.md#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="897a9-237">応答キャッシュミドルウェアは、200 (OK) 状態コードを生成するサーバー応答のみをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="897a9-237">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="897a9-238">[エラーページ](xref:fundamentals/error-handling)を含むその他の応答は、ミドルウェアによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-238">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="897a9-239">認証されたクライアントのコンテンツを含む応答は、ミドルウェアがそれらの応答を格納してサービスを提供しないように、キャッシュ不可能としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-239">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="897a9-240">応答がキャッシュ可能かどうかをミドルウェアが決定する方法の詳細については、「 [キャッシュの条件](#conditions-for-caching) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-240">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="897a9-241">オプション</span><span class="sxs-lookup"><span data-stu-id="897a9-241">Options</span></span>

<span data-ttu-id="897a9-242">応答キャッシュのオプションを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="897a9-242">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="897a9-243">オプション</span><span class="sxs-lookup"><span data-stu-id="897a9-243">Option</span></span> | <span data-ttu-id="897a9-244">[説明]</span><span class="sxs-lookup"><span data-stu-id="897a9-244">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="897a9-245">応答本文の最大キャッシュ可能サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="897a9-245">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="897a9-246">既定値は `64 * 1024 * 1024` (64 MB) です。</span><span class="sxs-lookup"><span data-stu-id="897a9-246">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="897a9-247">応答キャッシュミドルウェアのサイズ制限 (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="897a9-247">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="897a9-248">既定値は `100 * 1024 * 1024` (100 MB) です。</span><span class="sxs-lookup"><span data-stu-id="897a9-248">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="897a9-249">大文字と小文字が区別されるパスに応答をキャッシュするかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="897a9-249">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="897a9-250">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="897a9-250">The default value is `false`.</span></span> |

<span data-ttu-id="897a9-251">次の例では、ミドルウェアをに構成します。</span><span class="sxs-lookup"><span data-stu-id="897a9-251">The following example configures the middleware to:</span></span>

* <span data-ttu-id="897a9-252">本文のサイズが1024バイト以下の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="897a9-252">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="897a9-253">大文字と小文字を区別するパスで応答を格納します。</span><span class="sxs-lookup"><span data-stu-id="897a9-253">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="897a9-254">たとえば、 `/page1` と `/Page1` は別々に格納されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-254">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="897a9-255">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="897a9-255">VaryByQueryKeys</span></span>

<span data-ttu-id="897a9-256">MVC/web API コントローラーまたは :::no-loc(Razor)::: ページのページモデルを使用する場合、属性は、 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 応答のキャッシュに適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="897a9-256">When using MVC / web API controllers or :::no-loc(Razor)::: Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="897a9-257">ミドルウェアを厳密に必要とする属性の唯一のパラメーター `[ResponseCache]` は <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> 、実際の HTTP ヘッダーに対応していません。</span><span class="sxs-lookup"><span data-stu-id="897a9-257">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="897a9-258">詳細については、「<xref:performance/caching/response#responsecache-attribute>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-258">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="897a9-259">属性を使用しない場合は、を使用して `[ResponseCache]` 応答のキャッシュを変化させることができ `VaryByQueryKeys` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-259">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="897a9-260">を <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> HttpContext から直接使用し[ます。](xref:Microsoft.AspNetCore.Http.HttpContext.Features)</span><span class="sxs-lookup"><span data-stu-id="897a9-260">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="897a9-261">で1つの値を使用すると `*` `VaryByQueryKeys` 、すべての要求クエリパラメーターによってキャッシュが異なります。</span><span class="sxs-lookup"><span data-stu-id="897a9-261">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="897a9-262">応答キャッシュミドルウェアによって使用される HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="897a9-262">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="897a9-263">次の表は、応答のキャッシュに影響を与える HTTP ヘッダーに関する情報を示しています。</span><span class="sxs-lookup"><span data-stu-id="897a9-263">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="897a9-264">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="897a9-264">Header</span></span> | <span data-ttu-id="897a9-265">詳細</span><span class="sxs-lookup"><span data-stu-id="897a9-265">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="897a9-266">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="897a9-266">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="897a9-267">ミドルウェアは、cache ディレクティブでマークされたキャッシュ応答のみを考慮し `public` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-267">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="897a9-268">次のパラメーターを使用してキャッシュを制御します。</span><span class="sxs-lookup"><span data-stu-id="897a9-268">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="897a9-269">最長有効期間</span><span class="sxs-lookup"><span data-stu-id="897a9-269">max-age</span></span></li><li><span data-ttu-id="897a9-270">最大-古い&#8224;</span><span class="sxs-lookup"><span data-stu-id="897a9-270">max-stale&#8224;</span></span></li><li><span data-ttu-id="897a9-271">最小-新規</span><span class="sxs-lookup"><span data-stu-id="897a9-271">min-fresh</span></span></li><li><span data-ttu-id="897a9-272">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="897a9-272">must-revalidate</span></span></li><li><span data-ttu-id="897a9-273">no-cache</span><span class="sxs-lookup"><span data-stu-id="897a9-273">no-cache</span></span></li><li><span data-ttu-id="897a9-274">ストアなし</span><span class="sxs-lookup"><span data-stu-id="897a9-274">no-store</span></span></li><li><span data-ttu-id="897a9-275">-if-キャッシュ済み</span><span class="sxs-lookup"><span data-stu-id="897a9-275">only-if-cached</span></span></li><li><span data-ttu-id="897a9-276">プライベート</span><span class="sxs-lookup"><span data-stu-id="897a9-276">private</span></span></li><li><span data-ttu-id="897a9-277">public</span><span class="sxs-lookup"><span data-stu-id="897a9-277">public</span></span></li><li><span data-ttu-id="897a9-278">s-maxage</span><span class="sxs-lookup"><span data-stu-id="897a9-278">s-maxage</span></span></li><li><span data-ttu-id="897a9-279">プロキシ再検証&#8225;</span><span class="sxs-lookup"><span data-stu-id="897a9-279">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="897a9-280">&#8224;に制限が指定されていない場合 `max-stale` 、ミドルウェアは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="897a9-280">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="897a9-281">&#8225;`proxy-revalidate` はと同じ効果があり `must-revalidate` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-281">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="897a9-282">詳細については、「 [RFC 7231: Request Cache-Control ディレクティブ](https://tools.ietf.org/html/rfc7234#section-5.2.1)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-282">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="897a9-283">要求のヘッダーでは、 `Pragma: no-cache` と同じ効果が得られ `Cache-Control: no-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-283">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="897a9-284">このヘッダーは、ヘッダー内の関連するディレクティブによってオーバーライドされ `Cache-Control` ます (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="897a9-284">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="897a9-285">HTTP/1.0 との下位互換性のために考慮されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-285">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-:::no-loc(Cookie):::` | <span data-ttu-id="897a9-286">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="897a9-286">The response isn't cached if the header exists.</span></span> <span data-ttu-id="897a9-287">1つ以上のを設定する要求処理パイプライン内のすべてのミドルウェア :::no-loc(cookie)::: は、応答キャッシュミドルウェアが応答をキャッシュしないようにします (たとえば、 [ :::no-loc(cookie)::: ベースの tempdata プロバイダー](xref:fundamentals/app-state#tempdata))。</span><span class="sxs-lookup"><span data-stu-id="897a9-287">Any middleware in the request processing pipeline that sets one or more :::no-loc(cookie):::s prevents the Response Caching Middleware from caching the response (for example, the [:::no-loc(cookie):::-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="897a9-288">`Vary`ヘッダーは、キャッシュされた応答を別のヘッダーによって変更するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-288">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="897a9-289">たとえば、ヘッダーを含めることによって、エンコードによって応答をキャッシュし `Vary: Accept-Encoding` ます。ヘッダーとは別に要求の応答をキャッシュし `Accept-Encoding: gzip` `Accept-Encoding: text/plain` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-289">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="897a9-290">ヘッダー値がの応答 `*` は格納されません。</span><span class="sxs-lookup"><span data-stu-id="897a9-290">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="897a9-291">このヘッダーによって古いと見なされる応答は、他のヘッダーでオーバーライドされない限り、格納または取得されません `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="897a9-291">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="897a9-292">値がではなく、 `*` 応答のが指定された値と一致しない場合は、完全な応答がキャッシュから提供され `ETag` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-292">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="897a9-293">それ以外の場合は、304 (変更なし) の応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-293">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="897a9-294">ヘッダーが存在しない場合、キャッシュされた `If-None-Match` 応答の日付が指定した値より新しい場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-294">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="897a9-295">それ以外の場合は、 *304-変更されていない* 応答が提供されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-295">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="897a9-296">キャッシュからサービスを提供している場合、 `Date` ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="897a9-296">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="897a9-297">キャッシュからサービスを提供している場合、 `Content-Length` ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="897a9-297">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="897a9-298">`Age`元の応答で送信されたヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="897a9-298">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="897a9-299">ミドルウェアは、キャッシュされた応答を提供するときに新しい値を計算します。</span><span class="sxs-lookup"><span data-stu-id="897a9-299">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="897a9-300">キャッシュは要求 Cache-Control ディレクティブを尊重します</span><span class="sxs-lookup"><span data-stu-id="897a9-300">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="897a9-301">ミドルウェアは、 [HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)の規則を尊重します。</span><span class="sxs-lookup"><span data-stu-id="897a9-301">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="897a9-302">規則では、 `Cache-Control` クライアントによって送信された有効なヘッダーを受け入れるためにキャッシュが必要です。</span><span class="sxs-lookup"><span data-stu-id="897a9-302">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="897a9-303">この仕様では、クライアントはヘッダー値を使用して要求を行い、 `no-cache` すべての要求に対してサーバーに新しい応答を強制的に生成させることができます。</span><span class="sxs-lookup"><span data-stu-id="897a9-303">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="897a9-304">現時点では、ミドルウェアが公式のキャッシュ仕様に準拠しているため、ミドルウェアを使用する場合、このキャッシュ動作を開発者が制御することはありません。</span><span class="sxs-lookup"><span data-stu-id="897a9-304">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="897a9-305">キャッシュの動作を詳細に制御するには、ASP.NET Core の他のキャッシュ機能を調べます。</span><span class="sxs-lookup"><span data-stu-id="897a9-305">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="897a9-306">次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-306">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="897a9-307">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="897a9-307">Troubleshooting</span></span>

<span data-ttu-id="897a9-308">キャッシュの動作が想定どおりでない場合は、応答がキャッシュ可能であり、キャッシュから提供できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="897a9-308">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="897a9-309">要求の受信ヘッダーと応答の送信ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="897a9-309">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="897a9-310">デバッグに役立つように [ログ記録](xref:fundamentals/logging/index) を有効にします。</span><span class="sxs-lookup"><span data-stu-id="897a9-310">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="897a9-311">キャッシュ動作のテストとトラブルシューティングを行う場合、ブラウザーでは、望ましくない方法でキャッシュに影響を与える要求ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="897a9-311">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="897a9-312">たとえば、ページを更新するときに、ブラウザーで `Cache-Control` ヘッダーをまたはに設定でき `no-cache` `max-age=0` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-312">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="897a9-313">次のツールでは、要求ヘッダーを明示的に設定でき、キャッシュのテストに適しています。</span><span class="sxs-lookup"><span data-stu-id="897a9-313">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="897a9-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="897a9-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="897a9-315">Postman</span><span class="sxs-lookup"><span data-stu-id="897a9-315">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="897a9-316">キャッシュの条件</span><span class="sxs-lookup"><span data-stu-id="897a9-316">Conditions for caching</span></span>

* <span data-ttu-id="897a9-317">要求は、200 (OK) 状態コードでサーバー応答を生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-317">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="897a9-318">要求メソッドは GET または HEAD である必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-318">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="897a9-319">では `Startup.Configure` 、応答キャッシュミドルウェアは、キャッシュを必要とするミドルウェアの前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-319">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="897a9-320">詳細については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-320">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="897a9-321">ヘッダーを指定することはでき `Authorization` ません。</span><span class="sxs-lookup"><span data-stu-id="897a9-321">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="897a9-322">`Cache-Control` ヘッダーパラメーターは有効である必要があり、応答はとマークされている必要があり `public` `private` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-322">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="897a9-323">ヘッダーが存在する場合、ヘッダーはヘッダーをオーバーライドするので、ヘッダーが存在しない場合はヘッダーが `Pragma: no-cache` 存在しない必要があり `Cache-Control` `Cache-Control` `Pragma` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-323">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="897a9-324">ヘッダーを指定することはでき `Set-:::no-loc(Cookie):::` ません。</span><span class="sxs-lookup"><span data-stu-id="897a9-324">The `Set-:::no-loc(Cookie):::` header must not be present.</span></span>
* <span data-ttu-id="897a9-325">`Vary` ヘッダーパラメーターはと同じである必要があり `*` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-325">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="897a9-326">`Content-Length`ヘッダー値 (設定されている場合) は、応答本文のサイズと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-326">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="897a9-327">は <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 使用されません。</span><span class="sxs-lookup"><span data-stu-id="897a9-327">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="897a9-328">`Expires`ヘッダー、および、およびの各 `max-age` キャッシュディレクティブで指定されているとおり、応答を古いものにすることはできません `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="897a9-328">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="897a9-329">応答バッファリングを成功させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-329">Response buffering must be successful.</span></span> <span data-ttu-id="897a9-330">応答のサイズは、構成済みまたは既定値よりも小さくする必要があり <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-330">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="897a9-331">応答の本文のサイズは、構成済みまたは既定値よりも小さくする必要があり <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-331">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="897a9-332">応答は、 [RFC 7234](https://tools.ietf.org/html/rfc7234) 仕様に従ってキャッシュ可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="897a9-332">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="897a9-333">たとえば、ディレクティブは、 `no-store` 要求または応答のヘッダーフィールドに存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="897a9-333">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="897a9-334">詳細については、 *「セクション 3:* [RFC 7234](https://tools.ietf.org/html/rfc7234) のキャッシュに応答を格納する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="897a9-334">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="897a9-335">クロスサイト要求偽造 (CSRF) 攻撃を防ぐために、セキュリティで保護されたトークンを生成するための偽造防止システムは、 `Cache-Control` `Pragma` 応答がキャッシュされないようにヘッダーとヘッダーをに設定し `no-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="897a9-335">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="897a9-336">HTML フォーム要素の偽造防止トークンを無効にする方法については、「」を参照してください <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="897a9-336">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="897a9-337">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="897a9-337">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
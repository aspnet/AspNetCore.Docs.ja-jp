---
title: ASP.NET Core のエラーを処理する
author: rick-anderson
description: ASP.NET Core アプリでエラーを処理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: e65983fb1a440057283111ea5a79a79b765607b7
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751688"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="21c6a-103">ASP.NET Core のエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="21c6a-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="21c6a-104">作成者: [Kirk Larkin](https://twitter.com/serpent5)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="21c6a-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="21c6a-105">この記事では、ASP.NET Core Web アプリでエラーを処理するための一般的な手法について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="21c6a-106">Web API については、<xref:web-api/handle-errors> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="21c6a-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="21c6a-108">([ダウンロード方法](xref:index#how-to-download-a-sample)。)F12 ブラウザー開発者ツールの [ネットワーク] タブは、サンプル アプリをテストする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="21c6a-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="21c6a-109">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="21c6a-109">Developer Exception Page</span></span>

<span data-ttu-id="21c6a-110">"*開発者例外ページ*" には、要求の例外に関する詳細な情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="21c6a-111">ASP.NET Core テンプレートにより、次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="21c6a-112">前の強調表示されたコードは、アプリが[開発環境](xref:fundamentals/environments)で実行されている場合に、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="21c6a-113">このテンプレートでは、後続のミドルウェアでスローされた例外をキャッチできるように、ミドルウェア パイプラインの早い段階で <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> が配置されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="21c6a-114">前のコードでは、アプリが開発環境で実行されている場合に "\***のみ** _"、開発者例外ページが有効になります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="21c6a-115">アプリが運用環境で実行されている場合は、詳細な例外情報を公開しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="21c6a-116">環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="21c6a-117">開発者例外ページには、例外と要求に関する次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="21c6a-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="21c6a-118">_ スタック トレース</span><span class="sxs-lookup"><span data-stu-id="21c6a-118">_ Stack trace</span></span>
* <span data-ttu-id="21c6a-119">クエリ文字列のパラメーター (ある場合)</span><span class="sxs-lookup"><span data-stu-id="21c6a-119">Query string parameters if any</span></span>
* <span data-ttu-id="21c6a-120">Cookie (ある場合)</span><span class="sxs-lookup"><span data-stu-id="21c6a-120">Cookies if any</span></span>
* <span data-ttu-id="21c6a-121">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="21c6a-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="21c6a-122">例外ハンドラー ページ</span><span class="sxs-lookup"><span data-stu-id="21c6a-122">Exception handler page</span></span>

<span data-ttu-id="21c6a-123">[運用環境](xref:fundamentals/environments)のカスタム エラー処理ページを構成するには、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="21c6a-124">この例外処理ミドルウェアは、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="21c6a-124">This exception handling middleware:</span></span>

* <span data-ttu-id="21c6a-125">例外をキャッチしてログに記録します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="21c6a-126">指定されたパスを使用して、別のパイプラインで要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="21c6a-127">応答が始まっていた場合、要求は再実行されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="21c6a-128">テンプレートによって生成されたコードは、`/Error` パスを使用して要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="21c6a-129">次の例では、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> により非開発環境に例外処理ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="21c6a-130">Razor Pages アプリのテンプレートには、エラー ページ ( *.cshtml*) と <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> クラス (`ErrorModel`) が *Pages* フォルダー内に用意されています。</span><span class="sxs-lookup"><span data-stu-id="21c6a-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="21c6a-131">MVC アプリの場合、プロジェクト テンプレートには、Home コントローラーの `Error` アクション メソッドとエラー ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="21c6a-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="21c6a-132">例外処理ミドルウェアでは、"*元の*" HTTP メソッドを使用して要求が再実行されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-132">The exception handling middleware re-executes the request using the *original* HTTP method.</span></span> <span data-ttu-id="21c6a-133">エラー ハンドラーのエンドポイントが特定の HTTP メソッドのセットに制限されている場合は、それらの HTTP メソッドに対してのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-133">If an error handler endpoint is restricted to a specific set of HTTP methods, it runs only for those HTTP methods.</span></span> <span data-ttu-id="21c6a-134">たとえば、`[HttpGet]` 属性を使用する MVC コントローラーのアクションは、GET 要求に対してのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-134">For example, an MVC controller action that uses the `[HttpGet]` attribute runs only for GET requests.</span></span> <span data-ttu-id="21c6a-135">"*すべての*" 要求がカスタム エラー処理ページに到達するようにするために、それらを特定の HTTP メソッドのセットに制限しないでください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-135">To ensure that *all* requests reach the custom error handling page, don't restrict them to a specific set of HTTP methods.</span></span>

<span data-ttu-id="21c6a-136">元の HTTP メソッドに応じて例外を異なる方法で処理するには:</span><span class="sxs-lookup"><span data-stu-id="21c6a-136">To handle exceptions differently based on the original HTTP method:</span></span>

* <span data-ttu-id="21c6a-137">Razor Pages の場合は、複数のハンドラー メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-137">For Razor Pages, create multiple handler methods.</span></span> <span data-ttu-id="21c6a-138">たとえば、GET 例外を処理するために `OnGet` を使用し、POST 例外を処理するために `OnPost` を使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-138">For example, use `OnGet` to handle GET exceptions and use `OnPost` to handle POST exceptions.</span></span>
* <span data-ttu-id="21c6a-139">MVC の場合は、複数のアクションに HTTP 動詞属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-139">For MVC, apply HTTP verb attributes to multiple actions.</span></span> <span data-ttu-id="21c6a-140">たとえば、GET 例外を処理するために `[HttpGet]` を使用し、POST 例外を処理するために `[HttpPost]` を使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-140">For example, use `[HttpGet]` to handle GET exceptions and use `[HttpPost]` to handle POST exceptions.</span></span>

<span data-ttu-id="21c6a-141">認証されていないユーザーがカスタム エラー処理ページを表示できるようにするには、匿名アクセスがサポートされるようにします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-141">To allow unauthenticated users to view the custom error handling page, ensure that it supports anonymous access.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="21c6a-142">例外にアクセスする</span><span class="sxs-lookup"><span data-stu-id="21c6a-142">Access the exception</span></span>

<span data-ttu-id="21c6a-143">エラー ハンドラーで例外や元の要求パスにアクセスするには、<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="21c6a-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="21c6a-144">次のコードは、ASP.NET Core テンプレートによって生成される既定の *Pages/Error.cshtml.cs* に `ExceptionMessage` を追加します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-144">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="21c6a-145">機密性の高いエラー情報をクライアントに提供 **しないでください**。</span><span class="sxs-lookup"><span data-stu-id="21c6a-145">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="21c6a-146">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="21c6a-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="21c6a-147">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で例外をテストするには:</span><span class="sxs-lookup"><span data-stu-id="21c6a-147">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="21c6a-148">環境を運用環境に設定します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-148">Set the environment to production.</span></span>
* <span data-ttu-id="21c6a-149">*Program.cs* の `webBuilder.UseStartup<Startup>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-149">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="21c6a-150">ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-150">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="21c6a-151">例外ハンドラー ラムダ</span><span class="sxs-lookup"><span data-stu-id="21c6a-151">Exception handler lambda</span></span>

<span data-ttu-id="21c6a-152">[カスタム例外ハンドラー ページ](#exception-handler-page)の代わりになるのは、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> にラムダを提供することです。</span><span class="sxs-lookup"><span data-stu-id="21c6a-152">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="21c6a-153">ラムダを使うと、応答を返す前にエラーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-153">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="21c6a-154">次のコードは、例外処理にラムダを使用しています。</span><span class="sxs-lookup"><span data-stu-id="21c6a-154">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="21c6a-155"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> または <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> からの機密性の高いエラー情報をクライアントに提供 **しないでください**。</span><span class="sxs-lookup"><span data-stu-id="21c6a-155">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="21c6a-156">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="21c6a-156">Serving errors is a security risk.</span></span>

<span data-ttu-id="21c6a-157">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で例外処理ラムダをテストするには:</span><span class="sxs-lookup"><span data-stu-id="21c6a-157">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="21c6a-158">環境を運用環境に設定します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-158">Set the environment to production.</span></span>
* <span data-ttu-id="21c6a-159">*Program.cs* の `webBuilder.UseStartup<StartupLambda>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-159">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="21c6a-160">ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-160">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="21c6a-161">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="21c6a-161">UseStatusCodePages</span></span>

<span data-ttu-id="21c6a-162">ASP.NET Core アプリでは、既定で、"*404 - 見つかりません*" などの HTTP エラー状態コードの状態コード ページが表示されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-162">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="21c6a-163">アプリで、本文のない HTTP 400 から 599 のエラー状態コードが発生すると、状態コードと空の応答本文が返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-163">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="21c6a-164">状態コード ページを提供するには、状態コード ページ ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-164">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="21c6a-165">一般的なエラー状態コード用に既定のテキスト専用ハンドラーを有効にするには、`Startup.Configure` メソッドで <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-165">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="21c6a-166">要求処理ミドルウェアの前に `UseStatusCodePages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-166">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="21c6a-167">たとえば、静的ファイル ミドルウェアとエンドポイント ミドルウェアの前に `UseStatusCodePages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-167">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="21c6a-168">`UseStatusCodePages` を使用しない場合、エンドポイントなしで URL に移動すると、エンドポイントが見つからないことを示すブラウザー依存のエラー メッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-168">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="21c6a-169">たとえば、`Home/Privacy2` に移動します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-169">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="21c6a-170">`UseStatusCodePages` が呼び出されると、ブラウザーにより次が返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-170">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="21c6a-171">`UseStatusCodePages` は、ユーザーにとって役に立たないメッセージを返すため、通常は、運用環境では使用されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-171">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="21c6a-172">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには:</span><span class="sxs-lookup"><span data-stu-id="21c6a-172">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="21c6a-173">環境を運用環境に設定します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-173">Set the environment to production.</span></span>
* <span data-ttu-id="21c6a-174">*Program.cs* の `webBuilder.UseStartup<StartupUseStatusCodePages>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-174">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="21c6a-175">ホーム ページにあるリンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-175">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="21c6a-176">状態コード ページのミドルウェアは例外をキャッチ **しません**。</span><span class="sxs-lookup"><span data-stu-id="21c6a-176">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="21c6a-177">カスタム エラー処理ページを提供するには、[例外ハンドラー ページ](#exception-handler-page)を使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-177">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="21c6a-178">書式設定文字列での UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="21c6a-178">UseStatusCodePages with format string</span></span>

<span data-ttu-id="21c6a-179">応答の内容の種類とテキストをカスタマイズするには、内容の種類と書式文字列を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="21c6a-179">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="21c6a-180">上記のコードでは、`{0}` はエラー コードのプレースホルダーです。</span><span class="sxs-lookup"><span data-stu-id="21c6a-180">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="21c6a-181">書式指定文字列を含む `UseStatusCodePages` は、ユーザーにとって役に立たないメッセージを返すため、通常は、運用環境では使用されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-181">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="21c6a-182">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには、*Program.cs* の `webBuilder.UseStartup<StartupFormat>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-182">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="21c6a-183">ラムダでの UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="21c6a-183">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="21c6a-184">カスタム エラー処理と応答書き込みコードを指定するには、ラムダ式を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="21c6a-184">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="21c6a-185">ラムダを含む `UseStatusCodePages` は、ユーザーにとって役に立たないメッセージを返すため、通常は、運用環境では使用されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-185">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="21c6a-186">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには、*Program.cs* の `webBuilder.UseStartup<StartupStatusLambda>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-186">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="21c6a-187">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="21c6a-187">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="21c6a-188"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="21c6a-188">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="21c6a-189">クライアントに [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) 状態コードを送信します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-189">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="21c6a-190">URL テンプレートで指定されているエラー処理エンドポイントにクライアントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-190">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="21c6a-191">エラー処理エンドポイントには、通常、エラー情報が表示され、HTTP 200 が返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-191">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="21c6a-192">前のコードに示されているように、URL テンプレートには状態コード用の `{0}` プレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-192">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="21c6a-193">URL テンプレートが `~` (チルダ) で始まっている場合、`~` はアプリの `PathBase` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-193">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="21c6a-194">アプリでエンドポイントを指定するときに、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-194">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="21c6a-195">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)にある [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-195">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="21c6a-196">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-196">This method is commonly used when the app:</span></span>

* <span data-ttu-id="21c6a-197">クライアントを別のエンドポイントにリダイレクトする必要がある場合 (通常は、別のアプリがエラーを処理する場合)。</span><span class="sxs-lookup"><span data-stu-id="21c6a-197">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="21c6a-198">Web アプリの場合は、クライアントのブラウザーのアドレス バーにリダイレクトされたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-198">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="21c6a-199">元の状態コードを保持し、初回のリダイレクト応答で返してはいけない場合。</span><span class="sxs-lookup"><span data-stu-id="21c6a-199">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="21c6a-200">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには、*Program.cs* の `webBuilder.UseStartup<StartupSCredirect>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-200">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="21c6a-201">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="21c6a-201">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="21c6a-202"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="21c6a-202">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="21c6a-203">元の状態コードをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-203">Returns the original status code to the client.</span></span>
* <span data-ttu-id="21c6a-204">代替パスを使用して要求パイプラインを再実行することで、応答本文を生成します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-204">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="21c6a-205">アプリ内でエンドポイントが指定されている場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-205">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="21c6a-206">`UseRouting` の前に `UseStatusCodePagesWithReExecute` が配置されていることを確認して、要求を状態ページに再ルーティングできるようにします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-206">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="21c6a-207">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)にある [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-207">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="21c6a-208">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-208">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="21c6a-209">別のエンドポイントにリダイレクトすることなく要求を処理する。</span><span class="sxs-lookup"><span data-stu-id="21c6a-209">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="21c6a-210">Web アプリの場合は、クライアントのブラウザーのアドレス バーに、初めに要求されていたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-210">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="21c6a-211">元の状態コードを保持し、応答で返す。</span><span class="sxs-lookup"><span data-stu-id="21c6a-211">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="21c6a-212">URL とクエリ文字列のテンプレートには、状態コード用のプレースホルダー `{0}` を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-212">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="21c6a-213">URL テンプレートの先頭には、`/` を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-213">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="21c6a-214">次の例で示すように、エラーを処理するエンドポイントでは、エラーを生成した元の URL を取得できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-214">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="21c6a-215">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)にある [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-215">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="21c6a-216">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)で `UseStatusCodePages` をテストするには、*Program.cs* の `webBuilder.UseStartup<StartupSCreX>();` からコメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-216">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="21c6a-217">状態コード ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="21c6a-217">Disable status code pages</span></span>

<span data-ttu-id="21c6a-218">MVC コントローラーまたはアクション メソッドの状態コード ページを無効にするには、[[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-218">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="21c6a-219">Razor Pages ハンドラー メソッドまたは MVC コントローラーの特定の要求に対して状態コード ページを無効にするには、<xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> を使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-219">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="21c6a-220">例外処理コード</span><span class="sxs-lookup"><span data-stu-id="21c6a-220">Exception-handling code</span></span>

<span data-ttu-id="21c6a-221">例外処理ページのコードが例外をスローすることもあります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-221">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="21c6a-222">運用環境のエラー ページは十分にテストし、それ自体から例外がスローされないように特に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-222">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="21c6a-223">応答ヘッダー</span><span class="sxs-lookup"><span data-stu-id="21c6a-223">Response headers</span></span>

<span data-ttu-id="21c6a-224">応答のヘッダーが送信された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-224">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="21c6a-225">アプリで応答の状態コードを変更できません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-225">The app can't change the response's status code.</span></span>
* <span data-ttu-id="21c6a-226">すべての例外ページやハンドラーを実行できません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-226">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="21c6a-227">応答は完了している必要があります。あるいは、接続が中止となっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-227">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="21c6a-228">サーバー例外処理</span><span class="sxs-lookup"><span data-stu-id="21c6a-228">Server exception handling</span></span>

<span data-ttu-id="21c6a-229">アプリ内の例外処理ロジックに加えて、[HTTP サーバーの実装](xref:fundamentals/servers/index)でも一部の例外を処理できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-229">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="21c6a-230">応答ヘッダーの送信前にサーバーで例外がキャッチされると、サーバーによって "`500 - Internal Server Error`" 応答が応答本文なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-230">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="21c6a-231">応答ヘッダーの送信後にサーバーで例外がキャッチされた場合、サーバーは接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-231">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="21c6a-232">アプリで処理されない要求はサーバーで処理されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-232">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="21c6a-233">サーバーが要求を処理しているときに発生した例外は、すべてサーバーの例外処理によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-233">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="21c6a-234">この動作は、アプリのカスタム エラー ページ、例外処理ミドルウェア、およびフィルターから影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-234">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="21c6a-235">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="21c6a-235">Startup exception handling</span></span>

<span data-ttu-id="21c6a-236">アプリの起動中に起こる例外はホスティング層だけが処理できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-236">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="21c6a-237">[起動時のエラーをキャプチャ](xref:fundamentals/host/web-host#capture-startup-errors)したり、[詳細なエラーをキャプチャ](xref:fundamentals/host/web-host#detailed-errors)したりするように、ホストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-237">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="21c6a-238">ホスティング レイヤーでは、ホスト アドレス/ポート バインド後にエラーが発生した場合にのみ、キャプチャされた起動時エラーに対するエラー ページを表示できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-238">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="21c6a-239">バインドが失敗した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-239">If binding fails:</span></span>

* <span data-ttu-id="21c6a-240">ホスティング レイヤーにより重大な例外がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-240">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="21c6a-241">dotnet プロセスがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-241">The dotnet process crashes.</span></span>
* <span data-ttu-id="21c6a-242">HTTP サーバーが [Kestrel](xref:fundamentals/servers/kestrel) のときは、エラー ページは表示されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-242">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="21c6a-243">[IIS](/iis) (または Azure App Service) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上で実行している場合、プロセスを開始できなければ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)から "*502.5 - 処理エラー*" が返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-243">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="21c6a-244">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-244">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="21c6a-245">データベース エラー ページ</span><span class="sxs-lookup"><span data-stu-id="21c6a-245">Database error page</span></span>

<span data-ttu-id="21c6a-246">データベース開発者ページ例外フィルター `AddDatabaseDeveloperPageExceptionFilter` では、Entity Framework Core の移行を使って解決できるデータベース関連の例外がキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-246">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="21c6a-247">これらの例外が発生すると、問題が解決する可能性のあるアクションの詳細を含む HTML 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-247">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="21c6a-248">このページは、開発環境でのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="21c6a-248">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="21c6a-249">次のコードは、個々のユーザー アカウントが指定されたときに、ASP.NET Core Razor Pages テンプレートによって生成されたものです。</span><span class="sxs-lookup"><span data-stu-id="21c6a-249">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="21c6a-250">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="21c6a-250">Exception filters</span></span>

<span data-ttu-id="21c6a-251">MVC アプリでは、例外フィルターをグローバルに、またはコントローラーやアクションの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-251">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="21c6a-252">Razor Pages アプリでは、グローバルに、またはページ モデルの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-252">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="21c6a-253">このようなフィルターは、コントローラー アクションや別のフィルターの実行中に発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-253">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="21c6a-254">詳細については、「<xref:mvc/controllers/filters#exception-filters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-254">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="21c6a-255">例外フィルターは、MVC アクション内で発生する例外をトラップする場合には便利ですが、組み込みの[例外処理ミドルウェア](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` ほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-255">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="21c6a-256">選択された MVC アクションに応じて異なる方法でエラー処理を実行する必要がある場合を除き、`UseExceptionHandler` を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-256">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="21c6a-257">モデル状態エラー</span><span class="sxs-lookup"><span data-stu-id="21c6a-257">Model state errors</span></span>

<span data-ttu-id="21c6a-258">モデル状態エラーを処理する方法については、[モデル バインド](xref:mvc/models/model-binding)および[モデルの検証](xref:mvc/models/validation)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-258">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21c6a-259">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="21c6a-259">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="21c6a-260">作成者: [Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="21c6a-260">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="21c6a-261">この記事では、ASP.NET Core Web アプリでエラーを処理するための一般的な手法について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-261">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="21c6a-262">Web API については、<xref:web-api/handle-errors> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-262">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="21c6a-263">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="21c6a-264">([ダウンロード方法](xref:index#how-to-download-a-sample)。)</span><span class="sxs-lookup"><span data-stu-id="21c6a-264">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="21c6a-265">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="21c6a-265">Developer Exception Page</span></span>

<span data-ttu-id="21c6a-266">"*開発者例外ページ*" には、要求の例外に関する詳細な情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-266">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="21c6a-267">ASP.NET Core テンプレートにより、次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-267">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="21c6a-268">前のコードは、アプリが[開発環境](xref:fundamentals/environments)で実行されている場合に、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-268">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="21c6a-269">このテンプレートでは、後続のミドルウェアで例外をキャッチできるように、ミドルウェアの前に <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> が配置されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-269">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="21c6a-270">前のコードは、アプリが **開発環境** で実行されている場合にのみ、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-270">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="21c6a-271">アプリが運用環境で実行されている場合は、詳細な例外情報を公開しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-271">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="21c6a-272">環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-272">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="21c6a-273">開発者例外ページには、例外と要求に関する次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="21c6a-273">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="21c6a-274">スタック トレース</span><span class="sxs-lookup"><span data-stu-id="21c6a-274">Stack trace</span></span>
* <span data-ttu-id="21c6a-275">クエリ文字列のパラメーター (ある場合)</span><span class="sxs-lookup"><span data-stu-id="21c6a-275">Query string parameters if any</span></span>
* <span data-ttu-id="21c6a-276">Cookie (ある場合)</span><span class="sxs-lookup"><span data-stu-id="21c6a-276">Cookies if any</span></span>
* <span data-ttu-id="21c6a-277">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="21c6a-277">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="21c6a-278">例外ハンドラー ページ</span><span class="sxs-lookup"><span data-stu-id="21c6a-278">Exception handler page</span></span>

<span data-ttu-id="21c6a-279">運用環境用のカスタム エラー処理ページを構成するには、例外処理ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-279">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="21c6a-280">ミドルウェアでは次を行います。</span><span class="sxs-lookup"><span data-stu-id="21c6a-280">The middleware:</span></span>

* <span data-ttu-id="21c6a-281">例外をキャッチしてログに記録します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-281">Catches and logs exceptions.</span></span>
* <span data-ttu-id="21c6a-282">ページ用の、またはコントローラーが指定した別のパイプラインで、要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-282">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="21c6a-283">応答が始まっていた場合、要求は再実行されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-283">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="21c6a-284">テンプレートによって生成されたコードは、`/Error` への要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-284">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="21c6a-285">次の例では、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> により非開発環境に例外処理ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-285">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="21c6a-286">Razor Pages アプリのテンプレートには、エラー ページ ( *.cshtml*) と <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> クラス (`ErrorModel`) が *Pages* フォルダー内に用意されています。</span><span class="sxs-lookup"><span data-stu-id="21c6a-286">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="21c6a-287">MVC アプリの場合、プロジェクト テンプレートには、Home コントローラーのエラー アクション メソッドとエラー ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="21c6a-287">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="21c6a-288">`HttpGet` などの HTTP メソッド属性を使ってエラー ハンドラー アクション メソッドをマークしないでください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-288">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="21c6a-289">明示的な動詞を使用すると、要求がメソッドに届かないことがあります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-289">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="21c6a-290">認証されていないユーザーにエラー ビューが表示される場合は、メソッドへの匿名アクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-290">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="21c6a-291">例外にアクセスする</span><span class="sxs-lookup"><span data-stu-id="21c6a-291">Access the exception</span></span>

<span data-ttu-id="21c6a-292">エラー ハンドラー コントローラーまたはページ内で例外や元の要求パスにアクセスするには、<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="21c6a-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="21c6a-293">機密性の高いエラー情報をクライアントに提供 **しないでください**。</span><span class="sxs-lookup"><span data-stu-id="21c6a-293">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="21c6a-294">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="21c6a-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="21c6a-295">上記の例外処理ページをトリガーするには、環境を運用に設定し、例外を強制します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-295">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="21c6a-296">例外ハンドラー ラムダ</span><span class="sxs-lookup"><span data-stu-id="21c6a-296">Exception handler lambda</span></span>

<span data-ttu-id="21c6a-297">[カスタム例外ハンドラー ページ](#exception-handler-page)の代わりになるのは、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> にラムダを提供することです。</span><span class="sxs-lookup"><span data-stu-id="21c6a-297">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="21c6a-298">ラムダを使うと、応答を返す前にエラーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-298">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="21c6a-299">例外処理にラムダを使う例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-299">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="21c6a-300">上記のコードでは `await context.Response.WriteAsync(new string(' ', 512));` が追加されるため、Internet Explorer ブラウザーには IE のエラー メッセージではなく、このエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-300">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="21c6a-301">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/16144)します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-301">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="21c6a-302"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> または <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> からの機密性の高いエラー情報をクライアントに提供 **しないでください**。</span><span class="sxs-lookup"><span data-stu-id="21c6a-302">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="21c6a-303">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="21c6a-303">Serving errors is a security risk.</span></span>

<span data-ttu-id="21c6a-304">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で例外処理ラムダの結果を表示するには、`ProdEnvironment` および `ErrorHandlerLambda` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-304">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="21c6a-305">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="21c6a-305">UseStatusCodePages</span></span>

<span data-ttu-id="21c6a-306">ASP.NET Core アプリでは、既定で、"*404 - 見つかりません*" などの HTTP 状態コードの状態コード ページが表示されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-306">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="21c6a-307">アプリでは、状態コードと空の応答本文が返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-307">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="21c6a-308">状態コード ページを提供するには、状態コード ページ ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-308">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="21c6a-309">そのミドルウェアは、[Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-309">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="21c6a-310">一般的なエラー状態コード用に既定のテキスト専用ハンドラーを有効にするには、`Startup.Configure` メソッドで <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-310">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="21c6a-311">要求処理ミドルウェア (たとえば、静的ファイル ミドルウェアや MVC ミドルウェア) の前に `UseStatusCodePages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-311">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="21c6a-312">`UseStatusCodePages` を使用しない場合、エンドポイントなしで URL に移動すると、エンドポイントが見つからないことを示すブラウザー依存のエラー メッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-312">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="21c6a-313">たとえば、`Home/Privacy2` に移動します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-313">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="21c6a-314">`UseStatusCodePages` が呼び出されると、ブラウザーにより次が返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-314">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="21c6a-315">書式設定文字列での UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="21c6a-315">UseStatusCodePages with format string</span></span>

<span data-ttu-id="21c6a-316">応答の内容の種類とテキストをカスタマイズするには、内容の種類と書式文字列を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="21c6a-316">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="21c6a-317">ラムダでの UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="21c6a-317">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="21c6a-318">カスタム エラー処理と応答書き込みコードを指定するには、ラムダ式を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="21c6a-318">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="21c6a-319">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="21c6a-319">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="21c6a-320"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="21c6a-320">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="21c6a-321">クライアントに *302 - Found* 状態コードを送信します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-321">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="21c6a-322">URL テンプレートで指定された場所にクライアントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-322">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="21c6a-323">次の例で示すように、URL テンプレートには状態コード用の `{0}` プレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-323">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="21c6a-324">URL テンプレートが `~` (チルダ) で始まっている場合、`~` はアプリの `PathBase` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-324">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="21c6a-325">アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-325">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="21c6a-326">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-326">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="21c6a-327">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-327">This method is commonly used when the app:</span></span>

* <span data-ttu-id="21c6a-328">クライアントを別のエンドポイントにリダイレクトする必要がある場合 (通常は、別のアプリがエラーを処理する場合)。</span><span class="sxs-lookup"><span data-stu-id="21c6a-328">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="21c6a-329">Web アプリの場合は、クライアントのブラウザーのアドレス バーにリダイレクトされたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-329">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="21c6a-330">元の状態コードを保持し、初回のリダイレクト応答で返してはいけない場合。</span><span class="sxs-lookup"><span data-stu-id="21c6a-330">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="21c6a-331">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="21c6a-331">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="21c6a-332"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="21c6a-332">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="21c6a-333">元の状態コードをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-333">Returns the original status code to the client.</span></span>
* <span data-ttu-id="21c6a-334">代替パスを使用して要求パイプラインを再実行することで、応答本文を生成します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-334">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="21c6a-335">アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-335">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="21c6a-336">`UseRouting` の前に `UseStatusCodePagesWithReExecute` が配置されていることを確認して、要求を状態ページに再ルーティングできるようにします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-336">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="21c6a-337">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-337">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="21c6a-338">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-338">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="21c6a-339">別のエンドポイントにリダイレクトすることなく要求を処理する。</span><span class="sxs-lookup"><span data-stu-id="21c6a-339">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="21c6a-340">Web アプリの場合は、クライアントのブラウザーのアドレス バーに、初めに要求されていたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-340">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="21c6a-341">元の状態コードを保持し、応答で返す。</span><span class="sxs-lookup"><span data-stu-id="21c6a-341">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="21c6a-342">URL とクエリ文字列のテンプレートには、状態コード用のプレースホルダー (`{0}`) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-342">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="21c6a-343">URL テンプレートの先頭には、スラッシュ (`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-343">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="21c6a-344">パスでプレースホルダーを使う場合は、エンドポイント (ページまたはコントローラー) でパスのセグメントを処理できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-344">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="21c6a-345">たとえば、エラー用の Razor ページでは、`@page` ディレクティブの付いた省略可能なパスのセグメント値を受け入れる必要があります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-345">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="21c6a-346">次の例で示すように、エラーを処理するエンドポイントでは、エラーを生成した元の URL を取得できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-346">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="21c6a-347">状態コード ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="21c6a-347">Disable status code pages</span></span>

<span data-ttu-id="21c6a-348">MVC コントローラーまたはアクション メソッドの状態コード ページを無効にするには、[`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-348">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="21c6a-349">Razor Pages ハンドラー メソッドまたは MVC コントローラーの特定の要求に対して状態コード ページを無効にするには、<xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> を使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-349">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="21c6a-350">例外処理コード</span><span class="sxs-lookup"><span data-stu-id="21c6a-350">Exception-handling code</span></span>

<span data-ttu-id="21c6a-351">例外処理ページのコードは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-351">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="21c6a-352">実稼働のエラー ページは純粋に静的なコンテンツで構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-352">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="21c6a-353">応答ヘッダー</span><span class="sxs-lookup"><span data-stu-id="21c6a-353">Response headers</span></span>

<span data-ttu-id="21c6a-354">応答のヘッダーが送信された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-354">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="21c6a-355">アプリで応答の状態コードを変更できません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-355">The app can't change the response's status code.</span></span>
* <span data-ttu-id="21c6a-356">すべての例外ページやハンドラーを実行できません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-356">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="21c6a-357">応答は完了している必要があります。あるいは、接続が中止となっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-357">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="21c6a-358">サーバー例外処理</span><span class="sxs-lookup"><span data-stu-id="21c6a-358">Server exception handling</span></span>

<span data-ttu-id="21c6a-359">アプリ内の例外処理ロジックに加えて、[HTTP サーバーの実装](xref:fundamentals/servers/index)でも一部の例外を処理できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-359">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="21c6a-360">応答ヘッダーの送信前にサーバーで例外がキャッチされると、サーバーによって "*500 - 内部サーバー エラーです*" 応答が応答本文なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-360">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="21c6a-361">応答ヘッダーの送信後にサーバーで例外がキャッチされた場合、サーバーは接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-361">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="21c6a-362">アプリで処理されない要求はサーバーで処理されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-362">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="21c6a-363">サーバーが要求を処理しているときに発生した例外は、すべてサーバーの例外処理によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-363">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="21c6a-364">この動作は、アプリのカスタム エラー ページ、例外処理ミドルウェア、およびフィルターから影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-364">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="21c6a-365">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="21c6a-365">Startup exception handling</span></span>

<span data-ttu-id="21c6a-366">アプリの起動中に起こる例外はホスティング層だけが処理できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-366">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="21c6a-367">[起動時のエラーをキャプチャ](xref:fundamentals/host/web-host#capture-startup-errors)したり、[詳細なエラーをキャプチャ](xref:fundamentals/host/web-host#detailed-errors)したりするように、ホストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-367">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="21c6a-368">ホスティング レイヤーでは、ホスト アドレス/ポート バインド後にエラーが発生した場合にのみ、キャプチャされた起動時エラーに対するエラー ページを表示できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-368">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="21c6a-369">バインドが失敗した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-369">If binding fails:</span></span>

* <span data-ttu-id="21c6a-370">ホスティング レイヤーにより重大な例外がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-370">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="21c6a-371">dotnet プロセスがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-371">The dotnet process crashes.</span></span>
* <span data-ttu-id="21c6a-372">HTTP サーバーが [Kestrel](xref:fundamentals/servers/kestrel) のときは、エラー ページは表示されません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-372">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="21c6a-373">[IIS](/iis) (または Azure App Service) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上で実行している場合、プロセスを開始できなければ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)から "*502.5 - 処理エラー*" が返されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-373">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="21c6a-374">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-374">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="21c6a-375">データベース エラー ページ</span><span class="sxs-lookup"><span data-stu-id="21c6a-375">Database error page</span></span>

<span data-ttu-id="21c6a-376">データベース エラー ページ ミドルウェアでは、Entity Framework の移行を使って解決できるデータベース関連の例外がキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-376">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="21c6a-377">これらの例外が発生すると、問題が解決する可能性のあるアクションの詳細を含む HTML 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-377">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="21c6a-378">このページは、開発環境でのみ有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="21c6a-378">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="21c6a-379">ページを有効にするには、コードを `Startup.Configure` に追加します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-379">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="21c6a-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> には、[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="21c6a-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="21c6a-381">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="21c6a-381">Exception filters</span></span>

<span data-ttu-id="21c6a-382">MVC アプリでは、例外フィルターをグローバルに、またはコントローラーやアクションの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-382">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="21c6a-383">Razor Pages アプリでは、グローバルに、またはページ モデルの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="21c6a-383">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="21c6a-384">このようなフィルターはコントローラー アクションや別のフィルターの実行中に発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-384">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="21c6a-385">詳細については、「<xref:mvc/controllers/filters#exception-filters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-385">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="21c6a-386">例外フィルターは、MVC アクション内で発生した例外をトラップする場合は便利ですが、例外処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="21c6a-386">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="21c6a-387">ミドルウェアの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="21c6a-387">We recommend using the middleware.</span></span> <span data-ttu-id="21c6a-388">選択された MVC アクションに応じて異なる方法でエラー処理を実行する必要がある場合にのみ、フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="21c6a-388">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="21c6a-389">モデル状態エラー</span><span class="sxs-lookup"><span data-stu-id="21c6a-389">Model state errors</span></span>

<span data-ttu-id="21c6a-390">モデル状態エラーを処理する方法については、[モデル バインド](xref:mvc/models/model-binding)および[モデルの検証](xref:mvc/models/validation)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21c6a-390">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21c6a-391">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="21c6a-391">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

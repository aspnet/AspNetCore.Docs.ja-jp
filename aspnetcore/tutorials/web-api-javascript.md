---
title: 'チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す'
author: rick-anderson
description: JavaScript を使用して ASP.NET Core Web API を呼び出す方法について説明します。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: c32c5befe0be3b1ad4bd87649d3cc74b0296a134
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "94703710"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="72e39-103">チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="72e39-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="72e39-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="72e39-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="72e39-105">このチュートリアルでは、[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して JavaScript で ASP.NET Core Web API を呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="72e39-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="72e39-106">ASP.NET Core 2.2 の場合は、2.2 バージョンの「[JavaScript で Web API を呼び出す](xref:tutorials/first-web-api#call-the-web-api-with-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72e39-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="72e39-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="72e39-107">Prerequisites</span></span>

* <span data-ttu-id="72e39-108">[Web API の作成のチュートリアル](xref:tutorials/first-web-api)を完了していること</span><span class="sxs-lookup"><span data-stu-id="72e39-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="72e39-109">CSS、HTML、JavaScript に関する知識</span><span class="sxs-lookup"><span data-stu-id="72e39-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="72e39-110">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="72e39-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="72e39-111">このセクションでは、To Do アイテムを作成および管理するためのフォームが含まれる HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="72e39-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="72e39-112">イベント ハンドラーを、ページ上の要素にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="72e39-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="72e39-113">イベント ハンドラーによって、Web API のアクション メソッドに HTTP 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="72e39-114">Fetch API の `fetch` 関数により、各 HTTP 要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="72e39-115">`fetch` 関数からは [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) オブジェクトが返され、このオブジェクトには `Response` オブジェクトとして表された HTTP 応答が含まれます。</span><span class="sxs-lookup"><span data-stu-id="72e39-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="72e39-116">一般的なパターンでは、`Response` オブジェクトに対して `json` 関数を呼び出して、JSON 応答の本文を抽出します。</span><span class="sxs-lookup"><span data-stu-id="72e39-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="72e39-117">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="72e39-118">`fetch` の最も簡単な呼び出しでは、ルートを表す 1 つのパラメーターが受け付けられます。</span><span class="sxs-lookup"><span data-stu-id="72e39-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="72e39-119">`init` オブジェクトである 2 番目のパラメーターは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="72e39-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="72e39-120">`init` は、HTTP 要求を構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="72e39-121">[静的ファイルを提供し](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ように、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="72e39-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="72e39-122">*Startup.cs* の `Configure` メソッドでは、次の強調表示されたコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="72e39-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="72e39-123">プロジェクト ルートに *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="72e39-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="72e39-124">*wwwroot* フォルダー内に *js* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="72e39-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="72e39-125">*index.html* という名前の HTML ファイルを、*wwwroot* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="72e39-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="72e39-126">*index.html* の内容を、次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="72e39-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="72e39-127">*site.css* という名前の CSS ファイルを *wwwroot/css* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="72e39-127">Add a CSS file named *site.css* to the *wwwroot/css* folder.</span></span> <span data-ttu-id="72e39-128">*site.css* の内容を、次のスタイルに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="72e39-128">Replace the contents of *site.css* with the following styles:</span></span>

    [!code-css[](first-web-api/samples/3.0/TodoApi/wwwroot/css/site.css)]

1. <span data-ttu-id="72e39-129">*site.js* という名前の JavaScript ファイルを、*wwwroot/js* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="72e39-129">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="72e39-130">*site.js* の内容を、次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="72e39-130">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="72e39-131">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="72e39-131">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="72e39-132">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="72e39-132">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="72e39-133">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="72e39-133">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="72e39-134">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="72e39-134">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="72e39-135">以下では、Web API 要求について説明します。</span><span class="sxs-lookup"><span data-stu-id="72e39-135">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="72e39-136">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="72e39-136">Get a list of to-do items</span></span>

<span data-ttu-id="72e39-137">次のコードでは、HTTP GET 要求が *api/TodoItems* ルートに送信されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-137">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="72e39-138">Web API から正常状態コードが返されると、`_displayItems` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-138">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="72e39-139">`_displayItems` によって受け入れられた配列パラメーターの各 To Do アイテムが、**[Edit]** ボタンと **[Delete]** ボタンのあるテーブルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-139">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="72e39-140">Web API 要求が失敗した場合は、ブラウザーのコンソールにエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-140">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="72e39-141">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="72e39-141">Add a to-do item</span></span>

<span data-ttu-id="72e39-142">次のコードの内容は以下のとおりです。</span><span class="sxs-lookup"><span data-stu-id="72e39-142">In the following code:</span></span>

* <span data-ttu-id="72e39-143">`item` 変数は、To Do アイテムのオブジェクト リテラル表現を構築するために宣言されています。</span><span class="sxs-lookup"><span data-stu-id="72e39-143">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="72e39-144">フェッチ要求は、次のオプションで構成されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-144">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="72e39-145">`method`&mdash; POST HTTP アクション動詞が指定されています。</span><span class="sxs-lookup"><span data-stu-id="72e39-145">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="72e39-146">`body`&mdash; 要求本文の JSON 表現が指定されています。</span><span class="sxs-lookup"><span data-stu-id="72e39-146">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="72e39-147">JSON は、`item` に格納されているオブジェクト リテラルを [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 関数に渡すことによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-147">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="72e39-148">`headers`&mdash;`Accept` および `Content-Type` の HTTP 要求ヘッダーが指定されています。</span><span class="sxs-lookup"><span data-stu-id="72e39-148">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="72e39-149">どちらのヘッダーも `application/json` に設定され、それぞれ、受信および送信されるメディアの種類が指定されています。</span><span class="sxs-lookup"><span data-stu-id="72e39-149">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="72e39-150">HTTP POST 要求が *api/TodoItems* ルートに送信されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-150">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="72e39-151">Web API で正常状態コードが返されると、`getItems` 関数が呼び出されて、HTML テーブルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-151">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="72e39-152">Web API 要求が失敗した場合は、ブラウザーのコンソールにエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="72e39-152">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="72e39-153">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="72e39-153">Update a to-do item</span></span>

<span data-ttu-id="72e39-154">To Do アイテムの更新は追加と似ていますが、2 つの大きな違いがあります。</span><span class="sxs-lookup"><span data-stu-id="72e39-154">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="72e39-155">ルートに、更新するアイテムの一意の識別子がサフィックスとして付けられます。</span><span class="sxs-lookup"><span data-stu-id="72e39-155">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="72e39-156">たとえば、*api/TodoItems/1* のようになります。</span><span class="sxs-lookup"><span data-stu-id="72e39-156">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="72e39-157">`method` オプションで示されているように、HTTP アクション動詞は PUT です。</span><span class="sxs-lookup"><span data-stu-id="72e39-157">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="72e39-158">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="72e39-158">Delete a to-do item</span></span>

<span data-ttu-id="72e39-159">To Do アイテムを削除するには、要求の `method` オプションを `DELETE` に設定し、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="72e39-159">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="72e39-160">Web API のヘルプ ページを生成する方法については、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="72e39-160">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end

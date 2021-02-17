---
title: ASP.NET Core を使用してネイティブ モバイル アプリのバックエンド サービスを作成する
author: rick-anderson
description: ASP.NET Core MVC を使用してネイティブ モバイル アプリをサポートするバックエンド サービスを作成する方法について説明します。
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564029"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="b6962-103">ASP.NET Core を使用してネイティブ モバイル アプリのバックエンド サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="b6962-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="b6962-104">[James Montemagno](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="b6962-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="b6962-105">モバイル アプリは、ASP.NET Core バックエンド サービスと通信できます。</span><span class="sxs-lookup"><span data-stu-id="b6962-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="b6962-106">iOS シミュレーターと Android エミュレーターからローカル Web サービスに接続する手順については、「[Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)」 (iOS シミュレーターと Android エミュレーターからローカル Web サービスに接続する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6962-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="b6962-107">サンプル バックエンド サービス コードの表示またはダウンロード</span><span class="sxs-lookup"><span data-stu-id="b6962-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="b6962-108">サンプル ネイティブ モバイル アプリ</span><span class="sxs-lookup"><span data-stu-id="b6962-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="b6962-109">このチュートリアルでは、ASP.NET Core を使用してネイティブモバイルアプリをサポートするバックエンドサービスを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b6962-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="b6962-110">これは、Android、iOS、Windows 用の個別のネイティブクライアントを含む、ネイティブクライアントとして [TodoRest アプリ](/xamarin/xamarin-forms/data-cloud/consuming/rest) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b6962-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="b6962-111">リンクされているチュートリアルに従って、ネイティブ アプリを作成し (必要な無料の Xamarin ツールをインストールし)、Xamarin サンプル ソリューションをダウンロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="b6962-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="b6962-112">Xamarin サンプルには ASP.NET Core Web API サービスプロジェクトが含まれています。このプロジェクトでは、この記事の ASP.NET Core アプリが置き換えられます (クライアントが変更する必要はありません)。</span><span class="sxs-lookup"><span data-stu-id="b6962-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Android スマートフォン上で動作する To Do Rest アプリケーション](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="b6962-114">特徴</span><span class="sxs-lookup"><span data-stu-id="b6962-114">Features</span></span>

<span data-ttu-id="b6962-115">[TodoREST アプリ](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)では、To-Do 項目の一覧表示、追加、削除、および更新がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b6962-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="b6962-116">各項目には、ID、名前、メモ、完了したかどうかを示すプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="b6962-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="b6962-117">項目のメイン ビューには、上記のように各項目の名前が表示され、完了しているかどうかがチェックマークで示されます。</span><span class="sxs-lookup"><span data-stu-id="b6962-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="b6962-118">`+` アイコンをタップすると、項目の追加ダイアログが開きます。</span><span class="sxs-lookup"><span data-stu-id="b6962-118">Tapping the `+` icon opens an add item dialog:</span></span>

![項目の追加ダイアログ](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="b6962-120">メイン リスト画面の項目をタップすると、編集ダイアログが開き、項目の名前、メモ、完了の設定を変更したり、項目を削除したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="b6962-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![項目の編集ダイアログ](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="b6962-122">お使いのコンピューターで実行されている次のセクションで作成した ASP.NET Core アプリに対してテストするには、アプリの定数を更新し [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) ます。</span><span class="sxs-lookup"><span data-stu-id="b6962-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="b6962-123">Android エミュレーターはローカルコンピューター上では実行されず、ループバック IP (10.0.2.2) を使用してローカルコンピューターと通信します。</span><span class="sxs-lookup"><span data-stu-id="b6962-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="b6962-124">[DeviceInfo](/xamarin/essentials/device-information/)を活用して、正しい URL を使用するためにシステムが実行されているオペレーティングシステムを検出します。</span><span class="sxs-lookup"><span data-stu-id="b6962-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="b6962-125">プロジェクトに移動 [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) し、ファイルを開き [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) ます。</span><span class="sxs-lookup"><span data-stu-id="b6962-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="b6962-126">*Constants.cs* ファイルには、次の構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b6962-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="b6962-127">必要に応じて、web サービスを Azure などのクラウドサービスにデプロイし、を更新することができ `RestUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="b6962-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="b6962-128">ASP.NET Core プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="b6962-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="b6962-129">Visual Studio で新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="b6962-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="b6962-130">[Web API] テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6962-130">Choose the Web API template.</span></span> <span data-ttu-id="b6962-131">プロジェクトに *TodoAPI* という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="b6962-131">Name the project *TodoAPI*.</span></span>

![Web API プロジェクト テンプレートが選択されている [新しい ASP.NET Core Web アプリケーション] ダイアログ](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="b6962-133">アプリは、モバイルクライアントのクリアテキスト http トラフィックを含む、ポート5000に対して行われたすべての要求に応答する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b6962-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="b6962-134">*Startup.cs* を更新して、 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> 開発で実行しないようにします。</span><span class="sxs-lookup"><span data-stu-id="b6962-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="b6962-135">IIS Express の背後ではなく、直接アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b6962-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="b6962-136">既定では、IIS Express はローカル以外の要求を無視します。</span><span class="sxs-lookup"><span data-stu-id="b6962-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="b6962-137">コマンドプロンプトから [dotnet run](/dotnet/core/tools/dotnet-run) を実行するか、Visual Studio ツールバーの [デバッグターゲット] ドロップダウンからアプリ名プロファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="b6962-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="b6962-138">To-Do 項目を表すモデル クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b6962-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="b6962-139">必須フィールドを `[Required]` 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="b6962-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="b6962-140">API メソッドには、データを操作する何らかの方法が必要です。</span><span class="sxs-lookup"><span data-stu-id="b6962-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="b6962-141">元の Xamarin サンプルで使用されているものと同じ `ITodoRepository` インターフェイスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b6962-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="b6962-142">このサンプルの実装では、項目のプライベート コレクションを使用します。</span><span class="sxs-lookup"><span data-stu-id="b6962-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="b6962-143">*Startup.cs* で実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="b6962-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="b6962-144">コントローラーの作成</span><span class="sxs-lookup"><span data-stu-id="b6962-144">Creating the Controller</span></span>

<span data-ttu-id="b6962-145">新しいコントローラーをプロジェクト [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs)に追加します。</span><span class="sxs-lookup"><span data-stu-id="b6962-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="b6962-146">このメソッドは、から継承する必要があり <xref:Microsoft.AspNetCore.Mvc.ControllerBase> ます。</span><span class="sxs-lookup"><span data-stu-id="b6962-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="b6962-147">`api/todoitems` で始まるパスに対する要求をコントローラーが処理することを示す `Route` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="b6962-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="b6962-148">ルート内の `[controller]` トークンはコントローラーの名前に置き換えられます (`Controller` サフィックスは省略されます)。これは特にグローバル ルートの場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b6962-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="b6962-149">詳細については、[ルーティング](../fundamentals/routing.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6962-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="b6962-150">コントローラーを使用するには `ITodoRepository` が機能する必要があります。コントローラーのコンストラクターを介してこの種類のインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="b6962-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="b6962-151">実行時に、[依存関係の挿入](../fundamentals/dependency-injection.md)用のフレームワークのサポートを使用して、このインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b6962-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="b6962-152">この API は、データ ソースに対して CRUD (Create (作成)、Read (読み取り)、Update (更新)、Delete (削除)) 操作を実行する 4 つの異なる HTTP 動詞をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="b6962-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="b6962-153">この中で最も単純な操作は読み取りです。HTTP GET 要求に対応します。</span><span class="sxs-lookup"><span data-stu-id="b6962-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="b6962-154">項目の読み取り</span><span class="sxs-lookup"><span data-stu-id="b6962-154">Reading Items</span></span>

<span data-ttu-id="b6962-155">項目一覧の要求は、`List` メソッドに対する GET 要求で行われます。</span><span class="sxs-lookup"><span data-stu-id="b6962-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="b6962-156">`List` メソッドの `[HttpGet]` 属性は、このアクションが GET 要求のみを処理する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="b6962-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="b6962-157">このアクションのルートは、コントローラーで指定されたルートです。</span><span class="sxs-lookup"><span data-stu-id="b6962-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="b6962-158">ルートの一部としてアクション名を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b6962-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="b6962-159">各アクションに一意で明確なルートを持たせる必要があります。</span><span class="sxs-lookup"><span data-stu-id="b6962-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="b6962-160">ルーティング属性をコントローラー レベルとメソッド レベルの両方で適用して、特定のルートを構築することができます。</span><span class="sxs-lookup"><span data-stu-id="b6962-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="b6962-161">メソッドは、 `List` JSON としてシリアル化された 200 OK 応答コードとすべての Todo 項目を返します。</span><span class="sxs-lookup"><span data-stu-id="b6962-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="b6962-162">次のように、[Postman](https://www.getpostman.com/docs/) などのさまざまなツールを使用して、新しい API メソッドをテストできます。</span><span class="sxs-lookup"><span data-stu-id="b6962-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![todoitems の GET 要求を表示する Postman コンソールと、返された 3 つの項目の JSON を示す応答の本文](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="b6962-164">項目の作成</span><span class="sxs-lookup"><span data-stu-id="b6962-164">Creating Items</span></span>

<span data-ttu-id="b6962-165">慣例により、新しいデータ項目の作成は HTTP POST 動詞にマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="b6962-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="b6962-166">`Create` メソッドには `[HttpPost]` 属性が適用され、このメソッドは `TodoItem` インスタンスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="b6962-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="b6962-167">`item` 引数は POST の本文で渡されるため、このパラメーターは `[FromBody]` 属性を指定します。</span><span class="sxs-lookup"><span data-stu-id="b6962-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="b6962-168">メソッド内では、データ ストア内で項目が有効であることと事前に存在することが確認され、問題がなければ、リポジトリを使用して追加されます。</span><span class="sxs-lookup"><span data-stu-id="b6962-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="b6962-169">`ModelState.IsValid` の確認で[モデルの検証](../mvc/models/validation.md)が実行されます。この確認は、ユーザー入力を受け入れるすべての API メソッドで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b6962-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="b6962-170">このサンプルでは、 `enum` モバイルクライアントに渡されるエラーコードを含むを使用します。</span><span class="sxs-lookup"><span data-stu-id="b6962-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="b6962-171">Postman を使用して新しい項目の追加をテストします。このときに、要求の本文で JSON 形式の新しいオブジェクトを提供する POST 動詞を選択します。</span><span class="sxs-lookup"><span data-stu-id="b6962-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="b6962-172">また、`application/json` の `Content-Type` を指定する要求ヘッダーも追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b6962-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![POST と応答が表示される Postman コンソール](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="b6962-174">このメソッドは、新しく作成された項目を応答で返します。</span><span class="sxs-lookup"><span data-stu-id="b6962-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="b6962-175">項目の更新</span><span class="sxs-lookup"><span data-stu-id="b6962-175">Updating Items</span></span>

<span data-ttu-id="b6962-176">レコードの変更は、HTTP PUT 要求を使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="b6962-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="b6962-177">`Edit` メソッドは、この変更以外は `Create` とほとんど同じです。</span><span class="sxs-lookup"><span data-stu-id="b6962-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="b6962-178">レコードが見つからない場合、`Edit` アクションから `NotFound` (404) 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="b6962-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="b6962-179">Postman を使用してテストするには、動詞を PUT に変更します。</span><span class="sxs-lookup"><span data-stu-id="b6962-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="b6962-180">要求の本文で更新されたオブジェクト データを指定します。</span><span class="sxs-lookup"><span data-stu-id="b6962-180">Specify the updated object data in the Body of the request.</span></span>

![PUT と応答が表示される Postman コンソール](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="b6962-182">このメソッドが成功した場合は、既存の API との整合性を保つために、`NoContent` (204) 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="b6962-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="b6962-183">アイテムを削除する</span><span class="sxs-lookup"><span data-stu-id="b6962-183">Deleting Items</span></span>

<span data-ttu-id="b6962-184">レコードを削除するには、サービスに対して DELETE 要求を実行し、削除する項目の ID を渡します。</span><span class="sxs-lookup"><span data-stu-id="b6962-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="b6962-185">更新と同様に、存在しない項目に対する要求は `NotFound` 応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b6962-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="b6962-186">それ以外の成功した要求は `NoContent` (204) 応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b6962-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="b6962-187">削除機能をテストする場合、要求の本文には何も指定する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="b6962-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![DELETE と応答が表示される Postman コンソール](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="b6962-189">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="b6962-189">Prevent over-posting</span></span>

<span data-ttu-id="b6962-190">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="b6962-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="b6962-191">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="b6962-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="b6962-192">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="b6962-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="b6962-193">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b6962-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="b6962-194">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="b6962-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="b6962-195">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="b6962-195">A DTO may be used to:</span></span>

* <span data-ttu-id="b6962-196">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="b6962-196">Prevent over-posting.</span></span>
* <span data-ttu-id="b6962-197">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="b6962-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="b6962-198">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="b6962-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="b6962-199">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="b6962-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="b6962-200">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b6962-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="b6962-201">DTO アプローチの例については、「[過剰投稿の防止](xref:tutorials/first-web-api#prevent-over-posting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6962-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="b6962-202">一般的な Web API 規約</span><span class="sxs-lookup"><span data-stu-id="b6962-202">Common Web API Conventions</span></span>

<span data-ttu-id="b6962-203">アプリのバックエンド サービスを開発する場合は、横断的な懸案事項を処理するための一連の規約やポリシーが必要になります。</span><span class="sxs-lookup"><span data-stu-id="b6962-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="b6962-204">たとえば、前述のサービスでは、見つからなかった特定のレコードに対する要求は、`BadRequest` 応答ではなく `NotFound` 応答を受け取りました。</span><span class="sxs-lookup"><span data-stu-id="b6962-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="b6962-205">同様に、モデルにバインドされた種類で渡されたこのサービスに対するコマンドは、常に `ModelState.IsValid` を確認し、無効なモデルの種類の場合に `BadRequest` を返していました。</span><span class="sxs-lookup"><span data-stu-id="b6962-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="b6962-206">API の共通ポリシーを特定した場合、通常はそのポリシーを[フィルター](../mvc/controllers/filters.md)にカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="b6962-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="b6962-207">詳細については、[ASP.NET Core MVC アプリケーションで一般的な API ポリシーをカプセル化する方法](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b6962-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b6962-208">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b6962-208">Additional resources</span></span>

- [<span data-ttu-id="b6962-209">Xamarin. Forms: Web サービス認証</span><span class="sxs-lookup"><span data-stu-id="b6962-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="b6962-210">Xamarin. フォーム: RESTful Web サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="b6962-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="b6962-211">Microsoft Learn: Xamarin アプリで REST web サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="b6962-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="b6962-212">Microsoft Learn:ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="b6962-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)

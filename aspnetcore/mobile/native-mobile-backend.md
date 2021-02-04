---
title: ASP.NET Core を使用してネイティブ モバイル アプリのバックエンド サービスを作成する
author: ardalis
description: ASP.NET Core MVC を使用してネイティブ モバイル アプリをサポートするバックエンド サービスを作成する方法について説明します。
ms.author: riande
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 4e86241771e884ba9079bcdf9a09eebc6acd62c8
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530217"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="ebec3-103">ASP.NET Core を使用してネイティブ モバイル アプリのバックエンド サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="ebec3-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="ebec3-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ebec3-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ebec3-105">モバイル アプリは、ASP.NET Core バックエンド サービスと通信できます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="ebec3-106">iOS シミュレーターと Android エミュレーターからローカル Web サービスに接続する手順については、「[Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)」 (iOS シミュレーターと Android エミュレーターからローカル Web サービスに接続する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ebec3-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="ebec3-107">サンプル バックエンド サービス コードの表示またはダウンロード</span><span class="sxs-lookup"><span data-stu-id="ebec3-107">View or download sample backend services code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="ebec3-108">サンプル ネイティブ モバイル アプリ</span><span class="sxs-lookup"><span data-stu-id="ebec3-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="ebec3-109">このチュートリアルでは、ASP.NET Core MVC を使用してネイティブ モバイル アプリをサポートするバックエンド サービスを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-109">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="ebec3-110">[Xamarin Forms ToDoRest アプリ](/xamarin/xamarin-forms/data-cloud/consuming/rest)をネイティブ クライアントとして使用します。これには、Android、iOS、Windows Universal、Window Phone デバイス用に個別のネイティブ クライアントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ebec3-110">It uses the [Xamarin Forms ToDoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="ebec3-111">リンクされているチュートリアルに従って、ネイティブ アプリを作成し (必要な無料の Xamarin ツールをインストールし)、Xamarin サンプル ソリューションをダウンロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="ebec3-112">Xamarin サンプルには、ASP.NET Web API 2 サービス プロジェクトが含まれています。この記事の ASP.NET Core アプリで、このプロジェクトを置き換えます (クライアント側に変更は必要ありません)。</span><span class="sxs-lookup"><span data-stu-id="ebec3-112">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Android スマートフォン上で動作する To Do Rest アプリケーション](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="ebec3-114">特徴</span><span class="sxs-lookup"><span data-stu-id="ebec3-114">Features</span></span>

<span data-ttu-id="ebec3-115">この ToDoRest アプリは、To-Do 項目の一覧表示、追加、削除、更新をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ebec3-115">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="ebec3-116">各項目には、ID、名前、メモ、完了したかどうかを示すプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="ebec3-117">項目のメイン ビューには、上記のように各項目の名前が表示され、完了しているかどうかがチェックマークで示されます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="ebec3-118">`+` アイコンをタップすると、項目の追加ダイアログが開きます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-118">Tapping the `+` icon opens an add item dialog:</span></span>

![項目の追加ダイアログ](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="ebec3-120">メイン リスト画面の項目をタップすると、編集ダイアログが開き、項目の名前、メモ、完了の設定を変更したり、項目を削除したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![項目の編集ダイアログ](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="ebec3-122">このサンプルは、既定で、developer.xamarin.com でホストされている読み取り専用操作を許可するバックエンド サービスを使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="ebec3-122">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="ebec3-123">次のセクションで作成する ASP.NET Core アプリをコンピューター上で実行して、自分自身をテストするには、アプリの `RestUrl` 定数を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-123">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="ebec3-124">`ToDoREST` プロジェクトに移動し、*Constants.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-124">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="ebec3-125">`RestUrl` は、マシンの IP アドレスを含む URL に置き換えます (このアドレスはマシンからではなくデバイス エミュレーターから使用されるため、localhost または 127.0.0.1 には置き換えないでください)。</span><span class="sxs-lookup"><span data-stu-id="ebec3-125">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="ebec3-126">ポート番号 (5000) も含めます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-126">Include the port number as well (5000).</span></span> <span data-ttu-id="ebec3-127">このサービスがデバイスで動作することをテストするために、アクティブなファイアウォールがこのポートへのアクセスをブロックしていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-127">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="ebec3-128">ASP.NET Core プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="ebec3-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="ebec3-129">Visual Studio で新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="ebec3-130">Web API テンプレートと [No Authentication]\(認証なし\) を選択します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-130">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="ebec3-131">プロジェクトに *ToDoApi* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-131">Name the project *ToDoApi*.</span></span>

![Web API プロジェクト テンプレートが選択されている [新しい ASP.NET Core Web アプリケーション] ダイアログ](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="ebec3-133">アプリケーションは、ポート 5000 に対して行われたすべての要求に応答する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-133">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="ebec3-134">これを達成するために、`.UseUrls("http://*:5000")` を含むように *Program.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-134">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="ebec3-135">既定でローカル以外の要求を無視する IIS Express の背後ではなく、アプリケーションを直接実行するようにします。</span><span class="sxs-lookup"><span data-stu-id="ebec3-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="ebec3-136">コマンド プロンプトから [dotnet run](/dotnet/core/tools/dotnet-run) を実行するか、Visual Studio ツールバーの [デバッグ ターゲット] ドロップダウンからアプリケーション名のプロファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-136">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="ebec3-137">To-Do 項目を表すモデル クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="ebec3-138">必須フィールドを `[Required]` 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="ebec3-138">Mark required fields with the `[Required]` attribute:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="ebec3-139">API メソッドには、データを操作する何らかの方法が必要です。</span><span class="sxs-lookup"><span data-stu-id="ebec3-139">The API methods require some way to work with data.</span></span> <span data-ttu-id="ebec3-140">元の Xamarin サンプルで使用されているものと同じ `IToDoRepository` インターフェイスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-140">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="ebec3-141">このサンプルの実装では、項目のプライベート コレクションを使用します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-141">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="ebec3-142">*Startup.cs* で実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-142">Configure the implementation in *Startup.cs*:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="ebec3-143">この時点で、*ToDoItemsController* を作成する準備が整いました。</span><span class="sxs-lookup"><span data-stu-id="ebec3-143">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="ebec3-144">Web API の作成方法については、[ASP.NET Core MVC と Visual Studio での最初の Web API の構築](../tutorials/first-web-api.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ebec3-144">Learn more about creating web APIs in [Build your first Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="ebec3-145">コントローラーの作成</span><span class="sxs-lookup"><span data-stu-id="ebec3-145">Creating the Controller</span></span>

<span data-ttu-id="ebec3-146">*ToDoItemsController* プロジェクトに新しいコントローラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-146">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="ebec3-147">Microsoft.AspNetCore.Mvc.Controller から継承する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-147">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="ebec3-148">`api/todoitems` で始まるパスに対する要求をコントローラーが処理することを示す `Route` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-148">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="ebec3-149">ルート内の `[controller]` トークンはコントローラーの名前に置き換えられます (`Controller` サフィックスは省略されます)。これは特にグローバル ルートの場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-149">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="ebec3-150">詳細については、[ルーティング](../fundamentals/routing.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ebec3-150">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="ebec3-151">コントローラーを使用するには `IToDoRepository` が機能する必要があります。コントローラーのコンストラクターを介してこの種類のインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-151">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="ebec3-152">実行時に、[依存関係の挿入](../fundamentals/dependency-injection.md)用のフレームワークのサポートを使用して、このインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-152">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="ebec3-153">この API は、データ ソースに対して CRUD (Create (作成)、Read (読み取り)、Update (更新)、Delete (削除)) 操作を実行する 4 つの異なる HTTP 動詞をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ebec3-153">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="ebec3-154">この中で最も単純な操作は読み取りです。HTTP GET 要求に対応します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-154">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="ebec3-155">項目の読み取り</span><span class="sxs-lookup"><span data-stu-id="ebec3-155">Reading Items</span></span>

<span data-ttu-id="ebec3-156">項目一覧の要求は、`List` メソッドに対する GET 要求で行われます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-156">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="ebec3-157">`List` メソッドの `[HttpGet]` 属性は、このアクションが GET 要求のみを処理する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-157">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="ebec3-158">このアクションのルートは、コントローラーで指定されたルートです。</span><span class="sxs-lookup"><span data-stu-id="ebec3-158">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="ebec3-159">ルートの一部としてアクション名を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ebec3-159">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="ebec3-160">各アクションに一意で明確なルートを持たせる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-160">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="ebec3-161">ルーティング属性をコントローラー レベルとメソッド レベルの両方で適用して、特定のルートを構築することができます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-161">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="ebec3-162">`List` メソッドは、200 OK 応答コードとすべての ToDo 項目を JSON としてシリアル化して返します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-162">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="ebec3-163">次のように、[Postman](https://www.getpostman.com/docs/) などのさまざまなツールを使用して、新しい API メソッドをテストできます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-163">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![todoitems の GET 要求を表示する Postman コンソールと、返された 3 つの項目の JSON を示す応答の本文](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="ebec3-165">項目の作成</span><span class="sxs-lookup"><span data-stu-id="ebec3-165">Creating Items</span></span>

<span data-ttu-id="ebec3-166">慣例により、新しいデータ項目の作成は HTTP POST 動詞にマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-166">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="ebec3-167">`Create` メソッドには `[HttpPost]` 属性が適用され、このメソッドは `ToDoItem` インスタンスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-167">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="ebec3-168">`item` 引数は POST の本文で渡されるため、このパラメーターは `[FromBody]` 属性を指定します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-168">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="ebec3-169">メソッド内では、データ ストア内で項目が有効であることと事前に存在することが確認され、問題がなければ、リポジトリを使用して追加されます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-169">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="ebec3-170">`ModelState.IsValid` の確認で[モデルの検証](../mvc/models/validation.md)が実行されます。この確認は、ユーザー入力を受け入れるすべての API メソッドで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-170">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="ebec3-171">このサンプルでは、​​モバイル クライアントに渡されるエラー コードを含む列挙型を使用しています。</span><span class="sxs-lookup"><span data-stu-id="ebec3-171">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="ebec3-172">Postman を使用して新しい項目の追加をテストします。このときに、要求の本文で JSON 形式の新しいオブジェクトを提供する POST 動詞を選択します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-172">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="ebec3-173">また、`application/json` の `Content-Type` を指定する要求ヘッダーも追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-173">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![POST と応答が表示される Postman コンソール](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="ebec3-175">このメソッドは、新しく作成された項目を応答で返します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-175">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="ebec3-176">項目の更新</span><span class="sxs-lookup"><span data-stu-id="ebec3-176">Updating Items</span></span>

<span data-ttu-id="ebec3-177">レコードの変更は、HTTP PUT 要求を使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-177">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="ebec3-178">`Edit` メソッドは、この変更以外は `Create` とほとんど同じです。</span><span class="sxs-lookup"><span data-stu-id="ebec3-178">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="ebec3-179">レコードが見つからない場合、`Edit` アクションから `NotFound` (404) 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-179">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="ebec3-180">Postman を使用してテストするには、動詞を PUT に変更します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-180">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="ebec3-181">要求の本文で更新されたオブジェクト データを指定します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-181">Specify the updated object data in the Body of the request.</span></span>

![PUT と応答が表示される Postman コンソール](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="ebec3-183">このメソッドが成功した場合は、既存の API との整合性を保つために、`NoContent` (204) 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-183">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="ebec3-184">アイテムを削除する</span><span class="sxs-lookup"><span data-stu-id="ebec3-184">Deleting Items</span></span>

<span data-ttu-id="ebec3-185">レコードを削除するには、サービスに対して DELETE 要求を実行し、削除する項目の ID を渡します。</span><span class="sxs-lookup"><span data-stu-id="ebec3-185">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="ebec3-186">更新と同様に、存在しない項目に対する要求は `NotFound` 応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-186">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="ebec3-187">それ以外の成功した要求は `NoContent` (204) 応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-187">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="ebec3-188">削除機能をテストする場合、要求の本文には何も指定する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="ebec3-188">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![DELETE と応答が表示される Postman コンソール](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="ebec3-190">一般的な Web API 規約</span><span class="sxs-lookup"><span data-stu-id="ebec3-190">Common Web API Conventions</span></span>

<span data-ttu-id="ebec3-191">アプリのバックエンド サービスを開発する場合は、横断的な懸案事項を処理するための一連の規約やポリシーが必要になります。</span><span class="sxs-lookup"><span data-stu-id="ebec3-191">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="ebec3-192">たとえば、前述のサービスでは、見つからなかった特定のレコードに対する要求は、`BadRequest` 応答ではなく `NotFound` 応答を受け取りました。</span><span class="sxs-lookup"><span data-stu-id="ebec3-192">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="ebec3-193">同様に、モデルにバインドされた種類で渡されたこのサービスに対するコマンドは、常に `ModelState.IsValid` を確認し、無効なモデルの種類の場合に `BadRequest` を返していました。</span><span class="sxs-lookup"><span data-stu-id="ebec3-193">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="ebec3-194">API の共通ポリシーを特定した場合、通常はそのポリシーを[フィルター](../mvc/controllers/filters.md)にカプセル化できます。</span><span class="sxs-lookup"><span data-stu-id="ebec3-194">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="ebec3-195">詳細については、[ASP.NET Core MVC アプリケーションで一般的な API ポリシーをカプセル化する方法](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ebec3-195">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ebec3-196">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="ebec3-196">Additional resources</span></span>

- [<span data-ttu-id="ebec3-197">Xamarin. Forms: Web サービス認証</span><span class="sxs-lookup"><span data-stu-id="ebec3-197">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="ebec3-198">Xamarin. フォーム: RESTful Web サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="ebec3-198">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="ebec3-199">Microsoft Learn: Xamarin アプリで REST web サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="ebec3-199">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="ebec3-200">Microsoft Learn: ASP.NET Core で web API を作成する</span><span class="sxs-lookup"><span data-stu-id="ebec3-200">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)

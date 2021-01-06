---
page_type: sample
description: このチュートリアルは、MongoDB NoSQL データベースを使用して ASP.NET Core Web API を作成する方法を説明します。
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 95a2a6fcda0a4f7148183981f7dbacd06388329d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "84106521"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="6d512-102">ASP.NET Core と MongoDB で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="6d512-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="6d512-103">このチュートリアルでは、[MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL データベース上で Create、Read、Update、Delete の各操作 (CRUD) を実行するWeb API を作成します。</span><span class="sxs-lookup"><span data-stu-id="6d512-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="6d512-104">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6d512-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="6d512-105">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="6d512-105">Configure MongoDB</span></span>
* <span data-ttu-id="6d512-106">MongoDB データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="6d512-106">Create a MongoDB database</span></span>
* <span data-ttu-id="6d512-107">MongoDB のコレクションとスキーマを定義する</span><span class="sxs-lookup"><span data-stu-id="6d512-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="6d512-108">Web API から MongoDB CRUD 操作を実行する</span><span class="sxs-lookup"><span data-stu-id="6d512-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="6d512-109">JSON のシリアル化のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="6d512-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6d512-110">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6d512-110">Prerequisites</span></span>

* [<span data-ttu-id="6d512-111">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="6d512-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="6d512-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) と **ASP.NET および Web 開発** ワークロード</span><span class="sxs-lookup"><span data-stu-id="6d512-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6d512-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6d512-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="6d512-114">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="6d512-114">Configure MongoDB</span></span>

<span data-ttu-id="6d512-115">Windows を使用する場合、MongoDB は既定では *C:\\Program Files\\MongoDB* にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6d512-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="6d512-116">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* を `Path` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="6d512-117">この変更により、開発用コンピューターのどこからでも MongoDB にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="6d512-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="6d512-118">次の手順では mongo シェルを使用して、データベースを作成し、コレクションを作成し、ドキュメントを保存します。</span><span class="sxs-lookup"><span data-stu-id="6d512-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="6d512-119">mongo のシェル コマンドについて詳しくは、「[Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)」(mongo シェルの使用) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6d512-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="6d512-120">データを格納するために開発用コンピューター上のディレクトリを選択します。</span><span class="sxs-lookup"><span data-stu-id="6d512-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="6d512-121">たとえば、Windows では *C:\\BooksData* です。</span><span class="sxs-lookup"><span data-stu-id="6d512-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="6d512-122">存在しない場合はディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6d512-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="6d512-123">mongo シェルでは新しいディレクトリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="6d512-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="6d512-124">コマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="6d512-124">Open a command shell.</span></span> <span data-ttu-id="6d512-125">次のコマンドを実行して、既定のポート 27017 で MongoDB に接続します。</span><span class="sxs-lookup"><span data-stu-id="6d512-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="6d512-126">忘れずに、前の手順で選択したディレクトリで `<data_directory_path>` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6d512-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="6d512-127">別のコマンド シェル インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="6d512-127">Open another command shell instance.</span></span> <span data-ttu-id="6d512-128">次のコマンドを実行して、既定のテスト データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="6d512-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="6d512-129">コマンド シェルで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="6d512-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="6d512-130">これがまだ存在していない場合、*BookstoreDb* という名前のデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="6d512-131">データベースが存在する場合は、トランザクションのために接続されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="6d512-132">次のコマンドを使用して `Books` コレクションを作成します。</span><span class="sxs-lookup"><span data-stu-id="6d512-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="6d512-133">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="6d512-134">次のコマンドを使用して、`Books` コレクションのスキーマを定義し、2 つのドキュメントを挿入します。</span><span class="sxs-lookup"><span data-stu-id="6d512-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="6d512-135">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-135">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > <span data-ttu-id="6d512-136">この記事に示されている ID は、このサンプルを実行するときの ID とは一致していません。</span><span class="sxs-lookup"><span data-stu-id="6d512-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="6d512-137">次のコマンドを使用して、データベース内のドキュメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="6d512-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="6d512-138">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-138">The following result is displayed:</span></span>

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    <span data-ttu-id="6d512-139">スキーマによって、自動生成された型が `ObjectId` の `_id` プロパティが各ドキュメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="6d512-140">データベースの準備ができました。</span><span class="sxs-lookup"><span data-stu-id="6d512-140">The database is ready.</span></span> <span data-ttu-id="6d512-141">ASP.NET Core Web API の作成を開始できます。</span><span class="sxs-lookup"><span data-stu-id="6d512-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="6d512-142">ASP.NET Core Web API プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="6d512-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="6d512-143">**[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に移動します。</span><span class="sxs-lookup"><span data-stu-id="6d512-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="6d512-144">**[ASP.NET Core Web アプリケーション]** プロジェクトの種類を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6d512-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="6d512-145">プロジェクトに *BooksApi* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6d512-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="6d512-146">**[.NET Core]** ターゲット フレームワークと **[ASP.NET Core 3.0]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6d512-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="6d512-147">**[API]** プロジェクト テンプレートを選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6d512-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="6d512-148">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="6d512-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6d512-149">**[パッケージ マネージャー コンソール]** ウィンドウで、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="6d512-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="6d512-150">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6d512-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="6d512-151">モデルにエンティティを追加する</span><span class="sxs-lookup"><span data-stu-id="6d512-151">Add an entity model</span></span>

1. <span data-ttu-id="6d512-152">*Models* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="6d512-153">次のコードを使用して、`Book` クラスを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    <span data-ttu-id="6d512-154">上記のクラスでは、`Id` プロパティは:</span><span class="sxs-lookup"><span data-stu-id="6d512-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="6d512-155">共通言語ランタイム (CLR) オブジェクトを MongoDB コレクションにマッピングするために必須です。</span><span class="sxs-lookup"><span data-stu-id="6d512-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="6d512-156">ドキュメントの主キーとしてこのプロパティを指定するために、[`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="6d512-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="6d512-157">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 構造体ではなく `string` 型としてパラメーターを渡すことができるようにするために、[`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="6d512-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="6d512-158">Mongo によって `string` から `ObjectId` への変換が処理されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="6d512-159">`BookName` プロパティには、[`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 属性を使用して注釈が付けられます。</span><span class="sxs-lookup"><span data-stu-id="6d512-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="6d512-160">属性の値 `Name` は、MongoDB コレクションでのプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="6d512-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="6d512-161">構成モデルを追加する</span><span class="sxs-lookup"><span data-stu-id="6d512-161">Add a configuration model</span></span>

1. <span data-ttu-id="6d512-162">次のデータベース構成値を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="6d512-163">次のコードを使用して、*BookstoreDatabaseSettings.cs* ファイルを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    <span data-ttu-id="6d512-164">前述の `BookstoreDatabaseSettings` クラスは、*appsettings.json* ファイルの `BookstoreDatabaseSettings` プロパティ値を格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="6d512-165">JSON と C# のプロパティ名には、マッピング処理を簡単にするために同じ名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="6d512-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="6d512-166">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    <span data-ttu-id="6d512-167">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="6d512-167">In the preceding code:</span></span>

    * <span data-ttu-id="6d512-168">*appsettings.json* ファイルの `BookstoreDatabaseSettings` セクションがバインドされている構成インスタンスは、Dependency Injection (DI) コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="6d512-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="6d512-169">たとえば、`BookstoreDatabaseSettings` オブジェクトの `ConnectionString` プロパティには、*appsettings.json* の `BookstoreDatabaseSettings:ConnectionString` プロパティが設定されています。</span><span class="sxs-lookup"><span data-stu-id="6d512-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="6d512-170">`IBookstoreDatabaseSettings` インターフェイスは、シングルトン [サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="6d512-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6d512-171">挿入されると、インターフェイス インスタンスは `BookstoreDatabaseSettings` オブジェクトに解決されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="6d512-172">次のコードを *Startup.cs* の先頭に追加して、`BookstoreDatabaseSettings` と `IBookstoreDatabaseSettings` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="6d512-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="6d512-173">CRUD 操作のサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="6d512-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="6d512-174">*Services* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="6d512-175">次のコードを使用して、`BookService` クラスを *Services* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    <span data-ttu-id="6d512-176">前述のコードでは、`IBookstoreDatabaseSettings` インスタンスがコンストラクターの挿入によって DI から取得されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="6d512-177">この手法で、「[構成モデルを追加する](#add-a-configuration-model)」セクションで追加した *appsettings.json* 構成値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6d512-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="6d512-178">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    <span data-ttu-id="6d512-179">前述のコードでは、消費クラスへのコンストラクターの挿入をサポートする `BookService` クラスが DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="6d512-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="6d512-180">`BookService` が `MongoClient` に直接依存しているため、シングルトン サービスの有効期間が最も適切です。</span><span class="sxs-lookup"><span data-stu-id="6d512-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="6d512-181">公式の [Mongo Client 再利用ガイドライン](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)に従い、シングルトン サービスの有効期間を使用して DI に `MongoClient` を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6d512-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="6d512-182">次のコードを *Startup.cs* の先頭に追加して、`BookService` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="6d512-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="6d512-183">`BookService` クラスは次の `MongoDB.Driver` メンバーを使用して、データベースに対する CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="6d512-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="6d512-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm):データベース操作を実行するためにサーバー インスタンスを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="6d512-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="6d512-185">このクラスのコンストラクターには MongoDB 接続文字列が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="6d512-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm):操作を実行する Mongo データベースを表します。</span><span class="sxs-lookup"><span data-stu-id="6d512-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="6d512-187">このチュートリアルでは、インターフェイスで汎用の [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) メソッドを使用し、特定のコレクション内のデータへのアクセスを取得します。</span><span class="sxs-lookup"><span data-stu-id="6d512-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="6d512-188">このメソッドが呼び出された後に、CRUD 操作をこのコレクションに対して実行します。</span><span class="sxs-lookup"><span data-stu-id="6d512-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="6d512-189">`GetCollection<TDocument>(collection)` メソッドの呼び出しの内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6d512-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="6d512-190">`collection` はコレクション名です。</span><span class="sxs-lookup"><span data-stu-id="6d512-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="6d512-191">`TDocument` はコレクションに格納されている CLR オブジェクト型です。</span><span class="sxs-lookup"><span data-stu-id="6d512-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="6d512-192">`GetCollection<TDocument>(collection)` から、コレクションを表す [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="6d512-193">このチュートリアルでは、コレクションに対して次のメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6d512-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="6d512-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm):指定された検索基準と一致する 1 つのドキュメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="6d512-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="6d512-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm):指定された検索基準と一致するコレクション内のすべてのドキュメントを返します。</span><span class="sxs-lookup"><span data-stu-id="6d512-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="6d512-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm):指定されたオブジェクトを新しいドキュメントとしてコレクションに挿入します。</span><span class="sxs-lookup"><span data-stu-id="6d512-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="6d512-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm):指定された検索基準と一致する 1 つのドキュメントを、指定されたオブジェクトで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6d512-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6d512-198">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="6d512-198">Add a controller</span></span>

<span data-ttu-id="6d512-199">次のコードを使用して、`BooksController` クラスを *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

<span data-ttu-id="6d512-200">上記の Web API コントローラー:</span><span class="sxs-lookup"><span data-stu-id="6d512-200">The preceding web API controller:</span></span>

* <span data-ttu-id="6d512-201">`BookService` クラスを使用して CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="6d512-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="6d512-202">GET、POST、PUT、DELETE HTTP 要求をサポートするアクション メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6d512-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="6d512-203">`Create` アクション メソッドで <xref:System.Web.Http.ApiController.CreatedAtRoute*> を呼び出して、[HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="6d512-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="6d512-204">状態コード 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="6d512-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="6d512-205">`CreatedAtRoute` によって、応答に `Location` ヘッダーも追加されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="6d512-206">`Location` ヘッダーでは、新しく作成されたブックの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="6d512-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="6d512-207">Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="6d512-207">Test the web API</span></span>

1. <span data-ttu-id="6d512-208">アプリケーションをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="6d512-208">Build and run the app.</span></span>

1. <span data-ttu-id="6d512-209">`http://localhost:<port>/api/books` に移動して、コントローラーのパラメーターなしの `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="6d512-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="6d512-210">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-210">The following JSON response is displayed:</span></span>

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. <span data-ttu-id="6d512-211">`http://localhost:<port>/api/books/{id here}` に移動して、コントローラーのオーバーロードされた `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="6d512-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="6d512-212">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="6d512-213">JSON シリアル化オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="6d512-213">Configure JSON serialization options</span></span>

<span data-ttu-id="6d512-214">「[Web API をテストする](#test-the-web-api)」セクションで返される JSON 応答について変更すべき 2 つの詳細があります。</span><span class="sxs-lookup"><span data-stu-id="6d512-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="6d512-215">プロパティ名の既定の camel 形式は、CLR オブジェクトのプロパティ名の Pascal 形式と一致するように変更する必要があります</span><span class="sxs-lookup"><span data-stu-id="6d512-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="6d512-216">`bookName` プロパティは `Name` として返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="6d512-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="6d512-217">上記の要件を満たすには、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="6d512-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="6d512-218">JSON.NET は ASP.NET 共有フレームワークから削除されています。</span><span class="sxs-lookup"><span data-stu-id="6d512-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="6d512-219">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="6d512-219">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="6d512-220">`Startup.ConfigureServices` で、次の強調表示されたコードを `AddMvc` メソッド呼び出しにチェーンします。</span><span class="sxs-lookup"><span data-stu-id="6d512-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    <span data-ttu-id="6d512-221">上記の変更により、Web API のシリアル化された JSON 応答内のプロパティ名は、CLR のオブジェクトの種類での対応するプロパティ名と一致しています。</span><span class="sxs-lookup"><span data-stu-id="6d512-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="6d512-222">たとえば、`Book` クラスの `Author` プロパティは `Author` としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="6d512-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="6d512-223">*Models/Book.cs* では、`BookName` プロパティに次の [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 属性を使用して注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="6d512-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="6d512-224">`[JsonProperty]`属性の値 `Name` は、Web API のシリアル化された JSON 応答内のプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="6d512-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="6d512-225">次のコードを *Models/Book.cs* の先頭に追加して、`[JsonProperty]` 属性の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="6d512-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="6d512-226">「[Web API をテストする](#test-the-web-api)」セクションで定義されている手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="6d512-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="6d512-227">JSON プロパティ名の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="6d512-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6d512-228">次の手順</span><span class="sxs-lookup"><span data-stu-id="6d512-228">Next steps</span></span>

<span data-ttu-id="6d512-229">ASP.NET Core Web API の構築について詳しくは、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6d512-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="6d512-230">この記事の YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="6d512-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="6d512-231">ASP.NET Core を使って Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="6d512-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)

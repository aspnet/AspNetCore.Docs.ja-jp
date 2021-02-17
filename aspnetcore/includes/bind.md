---
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
ms.openlocfilehash: 30baab0649268f4abf0dbd6c99dfeef3f43d0054
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536303"
---
<span data-ttu-id="2cfdb-101">関連する構成値を読み取る方法としては、[オプション パターン](xref:fundamentals/configuration/options)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="2cfdb-102">たとえば、以下の構成値を読み取るには、次のようにします:</span><span class="sxs-lookup"><span data-stu-id="2cfdb-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="2cfdb-103">次の `PositionOptions` クラスを作成します:</span><span class="sxs-lookup"><span data-stu-id="2cfdb-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="2cfdb-104">オプション クラス:</span><span class="sxs-lookup"><span data-stu-id="2cfdb-104">An options class:</span></span>

* <span data-ttu-id="2cfdb-105">パラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="2cfdb-106">型のパブリックな読み取り/書き込みプロパティは、すべてバインドされます。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="2cfdb-107">フィールドはバインド ***されません***。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-107">Fields are ***not*** bound.</span></span> <span data-ttu-id="2cfdb-108">上のコード `Position` はバインドされません。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="2cfdb-109">`Position` プロパティは、クラスを構成プロバイダーにバインドするときに、アプリで文字列 `"Position"` をハードコーディングする必要をなくすために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="2cfdb-110">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-110">The following code:</span></span>

* <span data-ttu-id="2cfdb-111">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) を呼び出して、`PositionOptions` クラスを `Position` セクションにバインドします。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-111">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="2cfdb-112">`Position` 構成データを表示します。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="2cfdb-113">上のコードでは、アプリが開始された後の JSON 構成ファイルへの変更が既定で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="2cfdb-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="2cfdb-115">`ConfigurationBinder.Get<T>` は `ConfigurationBinder.Bind` を使用するよりも便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="2cfdb-116">次のコードは、`PositionOptions` クラスで `ConfigurationBinder.Get<T>` を使用する方法を示しています:</span><span class="sxs-lookup"><span data-stu-id="2cfdb-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="2cfdb-117">上のコードでは、アプリが開始された後の JSON 構成ファイルへの変更が既定で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="2cfdb-118">\***オプション パターン** _ を使用する際の別の方法として、`Position` セクションをバインドし、[依存関係挿入サービスコンテナー](xref:fundamentals/dependency-injection)に追加する方法があります。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-118">An alternative approach when using the \***options pattern** _ is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2cfdb-119">次のコードでは、`PositionOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="2cfdb-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="2cfdb-120">下記のコードは、上記のコードを使用して位置オプションを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="2cfdb-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="2cfdb-121">上のコードでは、アプリが開始された後の JSON 構成ファイルへの変更は読み取られ ***ません***。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="2cfdb-122">アプリの開始後に変更を読み取るには、[IOptionsSnapshot](xref:fundamentals/configuration/options#ios) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2cfdb-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>

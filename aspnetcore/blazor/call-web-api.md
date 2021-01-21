---
title: ASP.NET Core Blazor WebAssembly から Web API を呼び出す
author: guardrex
description: クロス オリジン リソース共有 (CORS) 要求の作成など、JSON ヘルパーを使用して Blazor WebAssembly アプリから web API を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 18afa7faa254f89ef664e0188be357a85c790523
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252436"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a><span data-ttu-id="b929d-103">ASP.NET Core Blazor から Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="b929d-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="b929d-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="b929d-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="b929d-105">このトピックの対象は、Blazor WebAssembly です。</span><span class="sxs-lookup"><span data-stu-id="b929d-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="b929d-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) アプリは、<xref:System.Net.Http.HttpClient> インスタンスを使用して Web API を呼び出します。これは通常、<xref:System.Net.Http.IHttpClientFactory> を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="b929d-107">Blazor Server に適用されるガイダンスについては、「<xref:fundamentals/http-requests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="b929d-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) アプリは、事前に構成された <xref:System.Net.Http.HttpClient> サービスを使用して Web API を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b929d-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="b929d-109">JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) オプションを含めることができる要求は、Blazor JSON ヘルパーまたは <xref:System.Net.Http.HttpRequestMessage> を使用して作成ます。</span><span class="sxs-lookup"><span data-stu-id="b929d-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="b929d-110">Blazor WebAssembly アプリの <xref:System.Net.Http.HttpClient> サービスは、発行元のサーバーに要求を戻すことに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="b929d-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="b929d-111">このトピックのガイダンスは、Blazor WebAssembly アプリにのみ関連します。</span><span class="sxs-lookup"><span data-stu-id="b929d-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="b929d-112">[サンプル コードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([ダウンロード方法](xref:index#how-to-download-a-sample)):`BlazorWebAssemblySample` アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="b929d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="b929d-113">`BlazorWebAssemblySample` サンプル アプリで次のコンポーネントを確認します。</span><span class="sxs-lookup"><span data-stu-id="b929d-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="b929d-114">Web API (`Pages/CallWebAPI.razor`) を呼び出す</span><span class="sxs-lookup"><span data-stu-id="b929d-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="b929d-115">HTTP 要求テスター (`Components/HTTPRequestTester.razor`)</span><span class="sxs-lookup"><span data-stu-id="b929d-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="b929d-116">パッケージ</span><span class="sxs-lookup"><span data-stu-id="b929d-116">Packages</span></span>

<span data-ttu-id="b929d-117">プロジェクト ファイルで [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="b929d-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="b929d-118">HttpClient サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="b929d-118">Add the HttpClient service</span></span>

<span data-ttu-id="b929d-119">`Program.Main` で、<xref:System.Net.Http.HttpClient> サービスがまだ存在しない場合は追加します。</span><span class="sxs-lookup"><span data-stu-id="b929d-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="b929d-120">HttpClient と JSON ヘルパー</span><span class="sxs-lookup"><span data-stu-id="b929d-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="b929d-121">Blazor WebAssembly アプリでは、[`HttpClient`](xref:fundamentals/http-requests) は、要求を要求元のサーバーに返すための構成済みのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="b929d-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="b929d-122">Blazor Server アプリには、既定では <xref:System.Net.Http.HttpClient> サービスが含まれません。</span><span class="sxs-lookup"><span data-stu-id="b929d-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="b929d-123">[`HttpClient` ファクトリ インフラストラクチャ](xref:fundamentals/http-requests)を使用して、アプリに <xref:System.Net.Http.HttpClient> を提供します。</span><span class="sxs-lookup"><span data-stu-id="b929d-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="b929d-124"><xref:System.Net.Http.HttpClient> と JSON のヘルパーは、サード パーティの Web API エンドポイントを呼び出すためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="b929d-125"><xref:System.Net.Http.HttpClient> は、ブラウザーの [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して実装されており、同一生成元ポリシーの適用を含め、その制限に従います。</span><span class="sxs-lookup"><span data-stu-id="b929d-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="b929d-126">クライアントのベース アドレスは、生成元のサーバーのアドレスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="b929d-127"><xref:System.Net.Http.HttpClient> インスタンスは [`@inject`](xref:mvc/views/razor#inject) ディレクティブを使用して挿入します。</span><span class="sxs-lookup"><span data-stu-id="b929d-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="b929d-128">以降の例では、Todo Web API によって、(CRUD) 操作の作成、読み取り、更新、および削除が処理されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="b929d-129">これらの例は、以下を格納する `TodoItem` クラスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="b929d-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="b929d-130">ID (`Id`、`long`):項目の一意の ID。</span><span class="sxs-lookup"><span data-stu-id="b929d-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="b929d-131">名前 (`Name`、`string`):項目の名前。</span><span class="sxs-lookup"><span data-stu-id="b929d-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="b929d-132">状態 (`IsComplete`、`bool`):Todo 項目が終了したかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="b929d-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="b929d-133">JSON のヘルパー メソッドは、要求を URI (次の例では Web API) に送信し、応答を処理します。</span><span class="sxs-lookup"><span data-stu-id="b929d-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="b929d-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>:HTTP GET 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="b929d-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="b929d-135">次のコードでは、コンポーネントによって `todoItems` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="b929d-136">`GetTodoItems` メソッドは、コンポーネントのレンダリングが終了したとき ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)) にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="b929d-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="b929d-137">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="b929d-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>:JSON でエンコードされたコンテンツを含め、HTTP POST 要求を送信し、JSON 応答本文を解析してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="b929d-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="b929d-139">次のコードでは、コンポーネントのバインドされた要素によって `newItemName` が提供されています。</span><span class="sxs-lookup"><span data-stu-id="b929d-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="b929d-140">`AddItem` メソッドは、`<button>` 要素を選択することにでトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="b929d-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="b929d-141">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="b929d-142"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="b929d-143">応答メッセージから JSON コンテンツを逆シリアル化するには、`ReadFromJsonAsync<T>` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b929d-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="b929d-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>:JSON でエンコードされたコンテンツを含め、HTTP PUT 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="b929d-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="b929d-145">次のコードでは、`Name` および `IsCompleted` の `editItem` 値が、コンポーネントのバインドされた要素によって提供されています。</span><span class="sxs-lookup"><span data-stu-id="b929d-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="b929d-146">項目の `Id` は、UI の別の部分で項目が選択され、`EditItem` が呼び出されたときに設定されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="b929d-147">`SaveItem` メソッドは、Save `<button>` 要素を選択することでトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="b929d-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="b929d-148">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="b929d-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> を呼び出すと、<xref:System.Net.Http.HttpResponseMessage> が返されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="b929d-150">応答メッセージから JSON コンテンツを逆シリアル化するには、<xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b929d-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="b929d-151"><xref:System.Net.Http> には、HTTP 要求を送信し、HTTP 応答を受信するための追加の拡張メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b929d-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="b929d-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> は、HTTP DELETE 要求を Web API に送信するために使用します。</span><span class="sxs-lookup"><span data-stu-id="b929d-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="b929d-153">次のコードでは、Delete `<button>` 要素で `DeleteItem` メソッドを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="b929d-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="b929d-154">バインドされた `<input>` 要素では、削除する項目の `id` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="b929d-155">完全な例についてはサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="b929d-156">IHttpClientFactory を使用する名前付き HttpClient</span><span class="sxs-lookup"><span data-stu-id="b929d-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="b929d-157"><xref:System.Net.Http.IHttpClientFactory> サービスおよび名前付き <xref:System.Net.Http.HttpClient> の構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b929d-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="b929d-158">プロジェクト ファイルで [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet パッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="b929d-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package in the project file.</span></span>

<span data-ttu-id="b929d-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b929d-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="b929d-160">`FetchData` コンポーネント (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="b929d-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="b929d-161">型指定された HttpClient</span><span class="sxs-lookup"><span data-stu-id="b929d-161">Typed HttpClient</span></span>

<span data-ttu-id="b929d-162">型指定された <xref:System.Net.Http.HttpClient> では、1 つ以上のアプリの <xref:System.Net.Http.HttpClient> インスタンス (既定または名前付き) を使用して、1 つ以上の Web API エンドポイントからデータを返します。</span><span class="sxs-lookup"><span data-stu-id="b929d-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="b929d-163">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="b929d-163">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

<span data-ttu-id="b929d-164">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b929d-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="b929d-165">コンポーネントによって型指定された <xref:System.Net.Http.HttpClient> が挿入され、Web API が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="b929d-166">`FetchData` コンポーネント (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="b929d-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="b929d-167">`HttpClient` および `HttpRequestMessage` と Fetch API 要求オプション</span><span class="sxs-lookup"><span data-stu-id="b929d-167">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="b929d-168">Blazor WebAssembly アプリで WebAssembly を実行するときに、[`HttpClient`](xref:fundamentals/http-requests) ([API ドキュメント](xref:System.Net.Http.HttpClient)) および <xref:System.Net.Http.HttpRequestMessage> を使用して要求をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="b929d-168">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="b929d-169">たとえば、HTTP メソッドや要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="b929d-169">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="b929d-170">次のコンポーネントでは、サーバー上の To Do List API エンドポイントに `POST` 要求を行い、応答本文を表示します。</span><span class="sxs-lookup"><span data-stu-id="b929d-170">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="b929d-171">.NET WebAssembly の <xref:System.Net.Http.HttpClient> の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-171">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="b929d-172">フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="b929d-172">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="b929d-173">HTTP フェッチ要求オプションは、次の表に示す <xref:System.Net.Http.HttpRequestMessage> 拡張メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="b929d-173">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="b929d-174">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="b929d-174">Extension method</span></span> | <span data-ttu-id="b929d-175">フェッチ要求プロパティ</span><span class="sxs-lookup"><span data-stu-id="b929d-175">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="b929d-176">より汎用的な <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 拡張メソッドを使用してその他のオプションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="b929d-176">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="b929d-177">通常、HTTP 応答は、応答コンテンツに対する同期読み取りのサポートを有効にするために Blazor WebAssembly アプリでバッファリングされます。</span><span class="sxs-lookup"><span data-stu-id="b929d-177">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="b929d-178">応答ストリーミングのサポートを有効にするには、要求に対して <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b929d-178">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="b929d-179">クロスオリジン要求に資格情報を含めるには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b929d-179">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="b929d-180">Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-180">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="handle-errors"></a><span data-ttu-id="b929d-181">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="b929d-181">Handle errors</span></span>

<span data-ttu-id="b929d-182">Web API の操作中にエラーが発生した場合は、開発者コードで処理できます。</span><span class="sxs-lookup"><span data-stu-id="b929d-182">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="b929d-183">たとえば、<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> では、`Content-Type` が `application/json` のサーバー API からの JSON 応答が想定されています。</span><span class="sxs-lookup"><span data-stu-id="b929d-183">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="b929d-184">応答が JSON 形式ではない場合、コンテンツ検証により <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b929d-184">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="b929d-185">次の例では、天気予報データ要求の URI エンドポイントのスペルが間違っています。</span><span class="sxs-lookup"><span data-stu-id="b929d-185">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="b929d-186">URI を `WeatherForecast` にする必要がありますが、呼び出しでは `WeatherForcast` と表示されています ("e" がありません)。</span><span class="sxs-lookup"><span data-stu-id="b929d-186">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="b929d-187"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> の呼び出しでは、JSON が返されることが想定されていますが、`Content-Type` が `text/html` のサーバー上の未処理の例外に対して、サーバーから HTML が返されます。</span><span class="sxs-lookup"><span data-stu-id="b929d-187">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="b929d-188">パスが見つからず、ミドルウェアから要求のページまたはビューを提供できないため、サーバー上で未処理の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="b929d-188">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="b929d-189">クライアント上の <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> では、応答コンテンツが非 JSON であると検証されると <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b929d-189">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="b929d-190">例外は `catch` ブロックでキャッチされます。ここでカスタム ロジックを使用してエラーをログに記録したり、わかりやすいエラー メッセージをユーザーに表示したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="b929d-190">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="b929d-191">前の例は、デモンストレーションを目的としています。</span><span class="sxs-lookup"><span data-stu-id="b929d-191">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="b929d-192">エンドポイントが存在しない場合や、サーバー上でハンドルされない例外が発生した場合でも、JSON を返すように Web API サーバー アプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="b929d-192">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="b929d-193">詳細については、「<xref:blazor/fundamentals/handle-errors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-193">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="b929d-194">クロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="b929d-194">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="b929d-195">Web ページでは、ブラウザーのセキュリティにより、その Web ページを提供したドメインと異なるドメインに対して要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="b929d-195">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="b929d-196">この制限は、*同一オリジン ポリシー* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b929d-196">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="b929d-197">同一オリジン ポリシーにより、悪意のあるサイトが別のサイトから機密データを読み取れないようになっています。</span><span class="sxs-lookup"><span data-stu-id="b929d-197">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b929d-198">オリジンが異なるエンドポイントへの要求をブラウザーから行うには、*エンドポイント* で [クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b929d-198">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="b929d-199">[Blazor WebAssembly サンプル アプリ (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) では、呼び出し Web API コンポーネント (`Pages/CallWebAPI.razor`) で CORS を使用する方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="b929d-199">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="b929d-200">Blazor アプリにおけるセキュリティ保護された要求での CORS の詳細については、「<xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-200">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="b929d-201">ASP.NET Core アプリでの CORS に関する一般的な情報については、「<xref:security/cors>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b929d-201">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b929d-202">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b929d-202">Additional resources</span></span>

* <span data-ttu-id="b929d-203"><xref:blazor/security/webassembly/additional-scenarios>:<xref:System.Net.Http.HttpClient> を使用してセキュリティで保護された Web API 要求を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b929d-203"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="b929d-204">Kestrel HTTPS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="b929d-204">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel/endpoints)
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="b929d-205">Kestrel HTTPS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="b929d-205">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
::: moniker-end
* [<span data-ttu-id="b929d-206">W3C におけるクロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="b929d-206">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)

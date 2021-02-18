---
title: ASP.NET Core Blazor の構成
author: guardrex
description: アプリの設定、認証、ログの構成など、Blazor アプリの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 48d78f40e9254bac182ffbc534550157664bcc5b
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106935"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="afca4-103">ASP.NET Core Blazor の構成</span><span class="sxs-lookup"><span data-stu-id="afca4-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="afca4-104">このトピックの対象は、Blazor WebAssembly です。</span><span class="sxs-lookup"><span data-stu-id="afca4-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="afca4-105">ASP.NET Core アプリの構成に関する一般的なガイダンスについては、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afca4-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="afca4-106">Blazor WebAssembly を使用すると、既定で次のアプリ設定ファイルから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afca4-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="afca4-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="afca4-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="afca4-108">`wwwroot/appsettings.{ENVIRONMENT}.json`。ここで、`{ENVIRONMENT}` プレースホルダーはアプリの[ランタイム環境](xref:fundamentals/environments)です。</span><span class="sxs-lookup"><span data-stu-id="afca4-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="afca4-109">アプリによって登録されたその他の構成プロバイダーで構成を指定することもできますが、すべてのプロバイダーやプロバイダーの機能が Blazor WebAssembly アプリに適しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="afca4-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="afca4-110">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration): クライアント シークレットのシナリオでは、プロバイダーはマネージド ID およびアプリケーション ID (クライアント ID) に対してはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="afca4-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="afca4-111">クライアント シークレットを使用したアプリケーション ID は、ASP.NET Core アプリ、特に Blazor WebAssembly アプリでは推奨されません。これは、クライアント シークレットを Azure Key Vault サービスにアクセスするためにクライアント側ではセキュリティで保護できないためです。</span><span class="sxs-lookup"><span data-stu-id="afca4-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="afca4-112">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app): このプロバイダーは Blazor WebAssembly アプリに適していません。これは、Blazor WebAssembly アプリは Azure 内のサーバー上では実行されないためです。</span><span class="sxs-lookup"><span data-stu-id="afca4-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="afca4-113">Blazor WebAssembly アプリでの構成は、ユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="afca4-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="afca4-114">**アプリのシークレット、資格情報、その他の機微なデータを Blazor WebAssembly アプリの構成に格納しないでください。**</span><span class="sxs-lookup"><span data-stu-id="afca4-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="afca4-115">構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afca4-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="afca4-116">アプリ設定の構成</span><span class="sxs-lookup"><span data-stu-id="afca4-116">App settings configuration</span></span>

<span data-ttu-id="afca4-117">アプリ設定ファイルの構成は既定で読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afca4-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="afca4-118">次の例では、UI 構成値がアプリ設定ファイルに格納され、Blazor フレームワークによって自動的に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afca4-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="afca4-119">その値はコンポーネントによって読み取られます。</span><span class="sxs-lookup"><span data-stu-id="afca4-119">The value is read by a component.</span></span>

<span data-ttu-id="afca4-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="afca4-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="afca4-121">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="afca4-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="afca4-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="afca4-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="afca4-123">その他の構成ファイルを `wwwroot` フォルダーから構成に読み取るには、<xref:System.Net.Http.HttpClient> を使用してファイルの内容を取得します。</span><span class="sxs-lookup"><span data-stu-id="afca4-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="afca4-124">次の例では、構成ファイル (`cars.json`) をアプリの構成に読み取ります。</span><span class="sxs-lookup"><span data-stu-id="afca4-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="afca4-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="afca4-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="afca4-126">`Program.cs` に <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="afca4-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="afca4-127">`Program.cs` の `Program.Main` で、クライアントを使用してファイルを読み取るように、既存の <xref:System.Net.Http.HttpClient> サービス登録を変更します。</span><span class="sxs-lookup"><span data-stu-id="afca4-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="memory-configuration-source"></a><span data-ttu-id="afca4-128">メモリ構成のソース</span><span class="sxs-lookup"><span data-stu-id="afca4-128">Memory Configuration Source</span></span>

<span data-ttu-id="afca4-129">次の例では、`Program.Main` で <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> を使用して追加の構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afca4-129">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="afca4-130">`Program.cs` に <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="afca4-130">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="afca4-131">`Program.cs` の `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="afca4-131">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="afca4-132">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="afca4-132">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="afca4-133">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="afca4-133">`Pages/MemoryConfig.razor`:</span></span>

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

<span data-ttu-id="afca4-134"><xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>で C# コード内の構成セクションを取得します。</span><span class="sxs-lookup"><span data-stu-id="afca4-134">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="afca4-135">次の例では、前の例の構成の `wheels` セクションを取得します。</span><span class="sxs-lookup"><span data-stu-id="afca4-135">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="afca4-136">認証の構成</span><span class="sxs-lookup"><span data-stu-id="afca4-136">Authentication configuration</span></span>

<span data-ttu-id="afca4-137">アプリ設定ファイルで認証構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afca4-137">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="afca4-138">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="afca4-138">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="afca4-139">`Program.Main` で <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> を使用して、Identity プロバイダーの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="afca4-139">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="afca4-140">次の例では、[OIDC プロバイダー](xref:blazor/security/webassembly/standalone-with-authentication-library)の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="afca4-140">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="afca4-141">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="afca4-141">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="afca4-142">ログの構成</span><span class="sxs-lookup"><span data-stu-id="afca4-142">Logging configuration</span></span>

<span data-ttu-id="afca4-143">アプリのプロジェクト ファイルに、[`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="afca4-143">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="afca4-144">アプリ設定ファイルで、ログ構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afca4-144">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="afca4-145">ログ構成は `Program.Main` に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afca4-145">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="afca4-146">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="afca4-146">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="afca4-147">`Program.cs` に <xref:Microsoft.Extensions.Logging?displayProperty=fullName> の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="afca4-147">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="afca4-148">`Program.cs` の `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="afca4-148">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="afca4-149">ホスト ビルダーの構成</span><span class="sxs-lookup"><span data-stu-id="afca4-149">Host builder configuration</span></span>

<span data-ttu-id="afca4-150">`Program.Main` で <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> のホスト ビルダーの構成を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="afca4-150">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="afca4-151">`Program.cs` の `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="afca4-151">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="afca4-152">キャッシュされた構成</span><span class="sxs-lookup"><span data-stu-id="afca4-152">Cached configuration</span></span>

<span data-ttu-id="afca4-153">構成ファイルは、オフラインで使用できるようにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="afca4-153">Configuration files are cached for offline use.</span></span> <span data-ttu-id="afca4-154">[プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="afca4-154">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="afca4-155">次の理由により、展開間で構成ファイルを編集しても意味がありません。</span><span class="sxs-lookup"><span data-stu-id="afca4-155">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="afca4-156">ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="afca4-156">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="afca4-157">PWA の `service-worker.js` と `service-worker-assets.js` のファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。</span><span class="sxs-lookup"><span data-stu-id="afca4-157">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="afca4-158">PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afca4-158">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

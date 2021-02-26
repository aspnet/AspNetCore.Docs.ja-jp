---
title: ASP.NET Core Blazor の環境
author: guardrex
description: Blazor WebAssembly アプリの環境を設定する方法など、Blazor の環境について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: a5ead59e467da331b585e8daefb1d7d259c7edba
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101049"
---
# <a name="aspnet-core-blazor-environments"></a><span data-ttu-id="894ea-103">ASP.NET Core Blazor の環境</span><span class="sxs-lookup"><span data-stu-id="894ea-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="894ea-104">このトピックの対象は、Blazor WebAssembly です。</span><span class="sxs-lookup"><span data-stu-id="894ea-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="894ea-105">Blazor Server アプリに使用する方法について説明している ASP.NET Core アプリの構成に関する一般的なガイダンスについては、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="894ea-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="894ea-106">アプリをローカルで実行する場合、環境は既定で開発に設定されます。</span><span class="sxs-lookup"><span data-stu-id="894ea-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="894ea-107">アプリが発行されると、環境は既定で実稼働になります。</span><span class="sxs-lookup"><span data-stu-id="894ea-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="894ea-108">ホストされている Blazor WebAssembly ソリューションのクライアント側 Blazor アプリ ( *`Client`* ) によって、環境をブラウザーに伝えるミドルウェアを介するソリューションの *`Server`* アプリからの環境が決まります。</span><span class="sxs-lookup"><span data-stu-id="894ea-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="894ea-109">*`Server`* アプリによって、ヘッダーの値として "environment" (環境) を含む `blazor-environment` という名前のヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="894ea-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="894ea-110">*`Client`* アプリによってそのヘッダーが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="894ea-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="894ea-111">ソリューションの *`Server`* アプリは ASP.NET Core アプリであるため、環境の構成方法の詳細については「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="894ea-111">The *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="894ea-112">ローカルで実行されているスタンドアロン Blazor WebAssembly アプリの場合、開発サーバーによって `blazor-environment` ヘッダーが追加され、開発環境が指定されます。</span><span class="sxs-lookup"><span data-stu-id="894ea-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="894ea-113">他のホスト環境の環境を指定するには、`blazor-environment` ヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="894ea-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="894ea-114">次の IIS の例では、発行された `web.config` ファイルにカスタム ヘッダー (`blazor-environment`) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="894ea-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="894ea-115">`web.config` ファイルは `bin/Release/{TARGET FRAMEWORK}/publish` フォルダー内にあります。プレースホルダー `{TARGET FRAMEWORK}` はターゲット フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="894ea-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="894ea-116">アプリが `publish` フォルダーに発行されたときに上書きされない IIS 用のカスタム `web.config` ファイルを使用するには、「<xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="894ea-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="894ea-117"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> を挿入し、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> プロパティを読み取ることで、コンポーネント内のアプリの環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="894ea-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="894ea-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="894ea-118">`Pages/ReadEnvironment.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

<span data-ttu-id="894ea-119">起動時に、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> によって <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> が <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> プロパティを介して公開されます。これにより、ホスト ビルダー コードで環境固有のロジックが有効になります。</span><span class="sxs-lookup"><span data-stu-id="894ea-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="894ea-120">`Program.cs` の `Program.Main` で:</span><span class="sxs-lookup"><span data-stu-id="894ea-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="894ea-121"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> を通じて提供された次の便利な拡張メソッドを使用すると、現在の環境で開発、運用、ステージング、およびカスタムの環境名を確認できます。</span><span class="sxs-lookup"><span data-stu-id="894ea-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="894ea-122">`Program.cs` の `Program.Main` で:</span><span class="sxs-lookup"><span data-stu-id="894ea-122">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="894ea-123"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> プロパティは、<xref:Microsoft.AspNetCore.Components.NavigationManager> サービスを利用できないときの起動時に使用できます。</span><span class="sxs-lookup"><span data-stu-id="894ea-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="894ea-124">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="894ea-124">Additional resources</span></span>

* <xref:fundamentals/environments>

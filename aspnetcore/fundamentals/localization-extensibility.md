---
title: ローカライズの拡張性
author: hishamco
description: ASP.NET Core アプリでローカライズ API を拡張する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
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
uid: fundamentals/localization-extensibility
ms.openlocfilehash: c41ebc0506a565666963d9b4c70ed73df389c826
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751665"
---
# <a name="localization-extensibility"></a><span data-ttu-id="df863-103">ローカライズの拡張性</span><span class="sxs-lookup"><span data-stu-id="df863-103">Localization Extensibility</span></span>

<span data-ttu-id="df863-104">作成者: [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="df863-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="df863-105">この記事の内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="df863-105">This article:</span></span>

* <span data-ttu-id="df863-106">ローカライズ API の拡張性ポイントを挙げます。</span><span class="sxs-lookup"><span data-stu-id="df863-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="df863-107">ASP.NET Core アプリのローカライズを拡張する手順を紹介します。</span><span class="sxs-lookup"><span data-stu-id="df863-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="df863-108">ローカライズ API の拡張性ポイント</span><span class="sxs-lookup"><span data-stu-id="df863-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="df863-109">ASP.NET Core ローカライズ API は拡張性を持つように作られています。</span><span class="sxs-lookup"><span data-stu-id="df863-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="df863-110">拡張性により、開発者はそのニーズに合わせてローカライズをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="df863-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="df863-111">たとえば、[OrchardCore](https://github.com/orchardCMS/OrchardCore/) には `POStringLocalizer` があります。</span><span class="sxs-lookup"><span data-stu-id="df863-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="df863-112">`POStringLocalizer` では、[Portable Object ローカライズ](xref:fundamentals/portable-object-localization)で `PO` ファイルを使用し、ローカライズ リソースを格納することが詳しく説明されます。</span><span class="sxs-lookup"><span data-stu-id="df863-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="df863-113">この記事では、ローカライズ API によって与えられる 2 つの主要な拡張性ポイントを挙げます。</span><span class="sxs-lookup"><span data-stu-id="df863-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="df863-114">ローカライズ カルチャ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="df863-114">Localization Culture Providers</span></span>

<span data-ttu-id="df863-115">ASP.NET Core ローカライズ API には、実行中の要求の現在のカルチャを決定できる既定のプロバイダーが 4 つあります。</span><span class="sxs-lookup"><span data-stu-id="df863-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="df863-116">前のプロバイダーの詳しい説明は、[ローカライズ ミドルウェア](xref:fundamentals/localization) ドキュメントにあります。</span><span class="sxs-lookup"><span data-stu-id="df863-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="df863-117">既定のプロバイダーでニーズが満たされない場合、次のいずれかの手法でカスタム プロバイダーを構築します。</span><span class="sxs-lookup"><span data-stu-id="df863-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="df863-118">CustomRequestCultureProvider を使用する</span><span class="sxs-lookup"><span data-stu-id="df863-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="df863-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> からは、単純な委任を利用して現在のローカライズ カルチャを決定するカスタム <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="df863-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="df863-120">RequestCultureProvider の新しい実装を使用する</span><span class="sxs-lookup"><span data-stu-id="df863-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="df863-121">カスタム ソースから要求カルチャ情報を決定する <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> の新しい実装を作成できます。</span><span class="sxs-lookup"><span data-stu-id="df863-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="df863-122">たとえば、構成ファイルまたはデータベースをカスタム ソースにすることができます。</span><span class="sxs-lookup"><span data-stu-id="df863-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="df863-123">次の例では、`AppSettingsRequestCultureProvider` を確認できます。これによって <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> が拡張され、 *appsettings.json* からの要求カルチャ情報が決定されます。</span><span class="sxs-lookup"><span data-stu-id="df863-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="df863-124">ローカライズ リソース</span><span class="sxs-lookup"><span data-stu-id="df863-124">Localization resources</span></span>

<span data-ttu-id="df863-125">ASP.NET Core ローカライズからは <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="df863-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="df863-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> は、`resx` を使用してローカライズ リソースを格納する <xref:Microsoft.Extensions.Localization.IStringLocalizer> の実装です。</span><span class="sxs-lookup"><span data-stu-id="df863-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="df863-127">`resx` ファイルの使用に限定されることはありません。</span><span class="sxs-lookup"><span data-stu-id="df863-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="df863-128">`IStringLocalizer` を実装することで、あらゆるデータ ソースを使用できます。</span><span class="sxs-lookup"><span data-stu-id="df863-128">By implementing `IStringLocalizer`, any data source can be used.</span></span>

<span data-ttu-id="df863-129">次のサンプル プロジェクトでは <xref:Microsoft.Extensions.Localization.IStringLocalizer> が実装されます。</span><span class="sxs-lookup"><span data-stu-id="df863-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="df863-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="df863-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="df863-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="df863-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="df863-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="df863-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)

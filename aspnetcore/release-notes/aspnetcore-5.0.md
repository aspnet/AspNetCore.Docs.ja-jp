---
title: ASP.NET Core 5.0 の新機能
author: rick-anderson
description: ASP.NET Core 5.0 の新機能について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: 5caa412773bf9c8e3bed5ebc529d48b886de6956
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253073"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="23957-103">ASP.NET Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="23957-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="23957-104">この記事では、ASP.NET Core 5.0 の最も大きな変更点について説明します。また、関連するドキュメントへのリンクも示します。</span><span class="sxs-lookup"><span data-stu-id="23957-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="23957-105">ASP.NET Core MVC と Razor の機能強化</span><span class="sxs-lookup"><span data-stu-id="23957-105">ASP.NET Core MVC and Razor improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="23957-106">UTC としての DateTime のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="23957-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="23957-107">モデル バインドにおいて、UTC 時刻文字列の `DateTime` へのバインドがサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="23957-108">要求に UTC 時刻文字列が含まれている場合は、モデル バインドによって UTC `DateTime` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="23957-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="23957-109">たとえば、次の時刻文字列は UTC `DateTime` にバインドされます: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="23957-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="23957-110">C# 9 のレコード型を使用したモデル バインドと検証</span><span class="sxs-lookup"><span data-stu-id="23957-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="23957-111">[C# 9 のレコード型](/dotnet/csharp/whats-new/csharp-9#record-types)を、MVC コントローラーまたは Razor ページのモデル バインドと共に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="23957-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a Razor Page.</span></span> <span data-ttu-id="23957-112">レコード型は、ネットワーク経由で転送されるデータをモデル化するための優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="23957-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="23957-113">たとえば、次の `PersonController` では、モデル バインドとフォーム検証で `Person` レコード型が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23957-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="23957-114">`Person/Index.cshtml` ファイルは以下の通りです。</span><span class="sxs-lookup"><span data-stu-id="23957-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="23957-115">DynamicRouteValueTransformer の機能強化</span><span class="sxs-lookup"><span data-stu-id="23957-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="23957-116">ASP.NET Core 3.1 では、カスタム エンドポイントを使用して MVC コントローラーのアクションまたは Razor ページを動的に選択するための方法として、<xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> が導入されました。</span><span class="sxs-lookup"><span data-stu-id="23957-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a Razor page.</span></span> <span data-ttu-id="23957-117">ASP.NET Core 5.0 アプリでは、`DynamicRouteValueTransformer` に状態を渡して、選択されたエンドポイントのセットをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="23957-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="23957-118">その他</span><span class="sxs-lookup"><span data-stu-id="23957-118">Miscellaneous</span></span>

* <span data-ttu-id="23957-119">[[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 属性は Razor ページ モデルのプロパティに適用できます。</span><span class="sxs-lookup"><span data-stu-id="23957-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a Razor Page model.</span></span>
* <span data-ttu-id="23957-120">本文からバインドされたパラメーターとプロパティは、既定では必須と見なされます。</span><span class="sxs-lookup"><span data-stu-id="23957-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="23957-121">Web API</span><span class="sxs-lookup"><span data-stu-id="23957-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="23957-122">OpenAPI 仕様を規定で有効化</span><span class="sxs-lookup"><span data-stu-id="23957-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="23957-123">[OpenAPI 仕様](http://spec.openapis.org/oas/v3.0.3)は、HTTP API について記述し、それらを複雑なビジネス プロセスやサード パーティと統合するための業界標準です。</span><span class="sxs-lookup"><span data-stu-id="23957-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="23957-124">OpenAPI はすべてのクラウド プロバイダーと多くの API レジストリによって幅広くサポートされています。</span><span class="sxs-lookup"><span data-stu-id="23957-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="23957-125">アプリによって Web API から OpenAPI ドキュメントを出力できれば、これらの API を使用できるさまざまな新しい機会を得られます。</span><span class="sxs-lookup"><span data-stu-id="23957-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="23957-126">オープンソース プロジェクト [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) の保守管理者との連携により、ASP.NET Core API テンプレートには [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) への NuGet の依存関係が含まれています。</span><span class="sxs-lookup"><span data-stu-id="23957-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="23957-127">Swashbuckle は、OpenAPI ドキュメントを動的に生成する、広く普及しているオープンソース NuGet パッケージです。</span><span class="sxs-lookup"><span data-stu-id="23957-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="23957-128">Swashbuckle でこれを実行するには、実行時に API コントローラーを調べて OpenAPI ドキュメントを生成するか、または Swashbuckle CLI を使用してビルド時に生成します。</span><span class="sxs-lookup"><span data-stu-id="23957-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="23957-129">ASP.NET Core 5.0 では、Web API テンプレートによって既定で OpenAPI サポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="23957-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="23957-130">OpenAPI を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="23957-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="23957-131">コマンドラインから:</span><span class="sxs-lookup"><span data-stu-id="23957-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="23957-132">Visual Studio から: **[Enable OpenAPI support]\(OpenAPI サポートの有効化\)** をオフにします。</span><span class="sxs-lookup"><span data-stu-id="23957-132">From Visual Studio: Uncheck **Enable OpenAPI support**.</span></span>

<span data-ttu-id="23957-133">Web API プロジェクト用に作成されたすべての *.csproj* ファイルには、[Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet パッケージ参照が含まれます。</span><span class="sxs-lookup"><span data-stu-id="23957-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="23957-134">テンプレートによって生成されたコードには、OpenAPI ドキュメントの生成をアクティブにするコードが `Startup.ConfigureServices` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="23957-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="23957-135">`Startup.Configure` メソッドによって、次を有効にする Swashbuckle ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="23957-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="23957-136">ドキュメント生成プロセス。</span><span class="sxs-lookup"><span data-stu-id="23957-136">Document generation process.</span></span>
* <span data-ttu-id="23957-137">開発モードでの既定の Swagger UI ページ。</span><span class="sxs-lookup"><span data-stu-id="23957-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="23957-138">運用環境への発行時に、テンプレートで生成されたコードによって API の説明が誤って公開されることはありません。</span><span class="sxs-lookup"><span data-stu-id="23957-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="23957-139">Azure API Management のインポート</span><span class="sxs-lookup"><span data-stu-id="23957-139">Azure API Management Import</span></span>

<span data-ttu-id="23957-140">ASP.NET Core API プロジェクトで OpenAPI が有効になっている場合、Visual Studio 2019 バージョン 16.8 以降の発行では、発行フローに追加の手順が自動的に提示されます。</span><span class="sxs-lookup"><span data-stu-id="23957-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="23957-141">[Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) を使用する開発者は、発行フロー中に、API を Azure API Management に自動的にインポートすることを選択できます。</span><span class="sxs-lookup"><span data-stu-id="23957-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Azure API Management のインポートと発行](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="23957-143">Web API プロジェクトの起動エクスペリエンスの向上</span><span class="sxs-lookup"><span data-stu-id="23957-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="23957-144">OpenAPI が既定で有効になっている場合、Web API 開発者のアプリ起動エクスペリエンス (F5) が大幅に改善されます。</span><span class="sxs-lookup"><span data-stu-id="23957-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="23957-145">ASP.NET Core 5.0 では、Web API テンプレートが Swagger UI ページを読み込むように事前構成されています。</span><span class="sxs-lookup"><span data-stu-id="23957-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="23957-146">Swagger UI ページには、発行された API 用に追加されるドキュメントと、1 回のクリックで API をテストする機能の両方が用意されています。</span><span class="sxs-lookup"><span data-stu-id="23957-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![swagger/index.html の表示](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a><span data-ttu-id="23957-148">パフォーマンスの向上</span><span class="sxs-lookup"><span data-stu-id="23957-148">Performance improvements</span></span>

<span data-ttu-id="23957-149">.NET 5 では、複雑な UI レンダリングと JSON シリアル化に特に焦点を当てて、Blazor WebAssembly ランタイムのパフォーマンスが大幅に改善されました。</span><span class="sxs-lookup"><span data-stu-id="23957-149">For .NET 5, we made significant improvements to Blazor WebAssembly runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="23957-150">Microsoft によるパフォーマンス テストでは、.NET 5 の Blazor WebAssembly はほとんどのシナリオで 2 倍から 3 倍高速になりました。</span><span class="sxs-lookup"><span data-stu-id="23957-150">In our performance tests, Blazor WebAssembly in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="23957-151">詳細については、[ASP.NET ブログ:.NET 5 リリース候補 1 における ASP.NET Core の更新](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="23957-152">CSS の分離</span><span class="sxs-lookup"><span data-stu-id="23957-152">CSS isolation</span></span>

<span data-ttu-id="23957-153">Blazor では、特定のコンポーネントにスコープ設定された CSS スタイルの定義がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-153">Blazor now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="23957-154">コンポーネント固有の CSS スタイルを使用すると、アプリのスタイルを把握しやすくなり、グローバル スタイルにおける意図しない副作用を回避しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="23957-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="23957-155">詳細については、 <xref:blazor/components/css-isolation> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="23957-156">新しい `InputFile` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="23957-156">New `InputFile` component</span></span>

<span data-ttu-id="23957-157">`InputFile` コンポーネントを使用すると、アップロード用にユーザーが選択した 1 つ以上のファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="23957-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="23957-158">詳細については、 <xref:blazor/file-uploads> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="23957-159">新しい `InputRadio` および `InputRadioGroup` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="23957-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="23957-160">Blazor に、ラジオ ボタン グループへのデータ バインドを簡略化する、統合された検証機能を備えた `InputRadio` および `InputRadioGroup` コンポーネントが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="23957-160">Blazor has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="23957-161">詳細については、 <xref:blazor/forms-validation> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="23957-162">コンポーネントの仮想化</span><span class="sxs-lookup"><span data-stu-id="23957-162">Component virtualization</span></span>

<span data-ttu-id="23957-163">Blazor フレームワークに組み込まれている仮想化サポートを使用して、コンポーネント レンダリングの認識されるパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="23957-163">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="23957-164">詳細については、 <xref:blazor/components/virtualization> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-164">For more information, see <xref:blazor/components/virtualization>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="23957-165">`ontoggle` イベントのサポート</span><span class="sxs-lookup"><span data-stu-id="23957-165">`ontoggle` event support</span></span>

<span data-ttu-id="23957-166">Blazor イベントで `ontoggle` DOM イベントがサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-166">Blazor events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="23957-167">詳細については、 <xref:blazor/components/event-handling#event-argument-types> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="23957-168">Blazor アプリで UI フォーカスを設定する</span><span class="sxs-lookup"><span data-stu-id="23957-168">Set UI focus in Blazor apps</span></span>

<span data-ttu-id="23957-169">要素参照で便利なメソッド `FocusAsync` を使用して、UI フォーカスをその要素に設定できます。</span><span class="sxs-lookup"><span data-stu-id="23957-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="23957-170">詳細については、 <xref:blazor/components/event-handling#focus-an-element> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="23957-171">カスタム検証クラス属性</span><span class="sxs-lookup"><span data-stu-id="23957-171">Custom validation class attributes</span></span>

<span data-ttu-id="23957-172">カスタム検証クラス名は、Bootstrap などの CSS フレームワークと統合する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="23957-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="23957-173">詳細については、 <xref:blazor/forms-validation#custom-validation-class-attributes> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="23957-174">IAsyncDisposable のサポート</span><span class="sxs-lookup"><span data-stu-id="23957-174">IAsyncDisposable support</span></span>

<span data-ttu-id="23957-175">Blazor コンポーネントで、割り当てられたリソースの非同期リリース用に <xref:System.IAsyncDisposable> インターフェイスがサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-175">Blazor components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="23957-176"> JavaScript の分離とオブジェクト参照</span><span class="sxs-lookup"><span data-stu-id="23957-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="23957-177">Blazor により、標準 [JavaScript モジュール](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)で JavaScript の分離が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="23957-177">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="23957-178">詳細については、 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="23957-179">フォーム コンポーネントでの表示名のサポート</span><span class="sxs-lookup"><span data-stu-id="23957-179">Form components support display name</span></span>

<span data-ttu-id="23957-180">次の組み込みコンポーネントでは、`DisplayName` パラメーターを使用した表示名がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="23957-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="23957-181">詳細については、「<xref:blazor/forms-validation#display-name-support>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="23957-182">キャッチオールのルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="23957-182">Catch-all route parameters</span></span>

<span data-ttu-id="23957-183">複数のフォルダー境界にまたがるパスをキャプチャするキャッチオール ルート パラメーターが、コンポーネントでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="23957-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="23957-184">詳細については、 <xref:blazor/fundamentals/routing#catch-all-route-parameters> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="23957-185">デバッグの機能強化</span><span class="sxs-lookup"><span data-stu-id="23957-185">Debugging improvements</span></span>

<span data-ttu-id="23957-186">ASP.NET Core 5.0 では Blazor WebAssembly アプリのデバッグが改善されました。</span><span class="sxs-lookup"><span data-stu-id="23957-186">Debugging Blazor WebAssembly apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="23957-187">さらに、Visual Studio for Mac でデバッグがサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="23957-188">詳細については、 <xref:blazor/debug> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="23957-189">Microsoft Identity v2.0 および MSAL v2.0</span><span class="sxs-lookup"><span data-stu-id="23957-189">Microsoft Identity v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="23957-190">Blazor セキュリティで Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) と [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) および MSAL v2.0 が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-190">Blazor security now uses Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) and [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="23957-191">詳細については、[Blazor セキュリティと Identity ノード](xref:blazor/security/index)に関するトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-191">For more information, see the topics in the [Blazor Security and Identity node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="23957-192">Blazor Server の保護されたブラウザー ストレージ</span><span class="sxs-lookup"><span data-stu-id="23957-192">Protected Browser Storage for Blazor Server</span></span>

<span data-ttu-id="23957-193">Blazor Server アプリでは、ASP.NET Core データ保護を使用して改ざんから保護されているブラウザーにアプリの状態を格納するための、組み込みサポートを使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-193">Blazor Server apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="23957-194">データは、ローカルのブラウザー ストレージまたはセッション ストレージに格納できます。</span><span class="sxs-lookup"><span data-stu-id="23957-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="23957-195">詳細については、 <xref:blazor/state-management> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="23957-196">Blazor WebAssembly のプリレンダリング</span><span class="sxs-lookup"><span data-stu-id="23957-196">Blazor WebAssembly prerendering</span></span>

<span data-ttu-id="23957-197">コンポーネントの統合がホスティング モデル全体で向上し、Blazor WebAssembly アプリがサーバーで出力をプリレンダリングできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-197">Component integration is improved across hosting models, and Blazor WebAssembly apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="23957-198">トリミングとリンクの機能強化</span><span class="sxs-lookup"><span data-stu-id="23957-198">Trimming/linking improvements</span></span>

<span data-ttu-id="23957-199">Blazor WebAssembly では、ビルド中に中間言語 (IL) のトリミングとリンクが実行され、アプリの出力アセンブリから不要な IL がトリミングされます。</span><span class="sxs-lookup"><span data-stu-id="23957-199">Blazor WebAssembly performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="23957-200">ASP.NET Core 5.0 リリースでは、Blazor WebAssembly により、追加の構成オプションを備えた改善されたトリミングが実行されます。</span><span class="sxs-lookup"><span data-stu-id="23957-200">With the release of ASP.NET Core 5.0, Blazor WebAssembly performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="23957-201">詳細については、「<xref:blazor/host-and-deploy/configure-trimmer>」と「[トリミングのオプション](/dotnet/core/deploying/trimming-options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="23957-202">ブラウザー互換性アナライザー</span><span class="sxs-lookup"><span data-stu-id="23957-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="23957-203">Blazor WebAssembly アプリは完全な .NET API 領域を対象としていますが、ブラウザー サンドボックスの制約により、すべての .NET API が WebAssembly でサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="23957-203">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="23957-204">サポートされていない API は、WebAssembly で実行すると <xref:System.PlatformNotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="23957-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="23957-205">開発者が、アプリのターゲット プラットフォームでサポートされていない API をアプリで使用すると、プラットフォーム互換性アナライザーから警告を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="23957-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="23957-206">詳細については、 <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="23957-207">アセンブリの遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="23957-207">Lazy load assemblies</span></span>

<span data-ttu-id="23957-208">Blazor WebAssembly アプリの起動時のパフォーマンスは、一部のアプリケーション アセンブリの読み込みを必要になるまで延期することで改善できます。</span><span class="sxs-lookup"><span data-stu-id="23957-208">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="23957-209">詳細については、 <xref:blazor/webassembly-lazy-load-assemblies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="23957-210">グローバリゼーション サポートの更新</span><span class="sxs-lookup"><span data-stu-id="23957-210">Updated globalization support</span></span>

<span data-ttu-id="23957-211">International Components for Unicode (ICU) に基づき Blazor WebAssembly でグローバリゼーション サポートを利用できます。</span><span class="sxs-lookup"><span data-stu-id="23957-211">Globalization support is available for Blazor WebAssembly based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="23957-212">詳細については、 <xref:blazor/globalization-localization> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="23957-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="23957-213">gRPC</span></span>

<span data-ttu-id="23957-214">[gRPC](https://grpc.io/) では、多くのパフォーマンス改善が行われています。</span><span class="sxs-lookup"><span data-stu-id="23957-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="23957-215">詳細については、「[.NET 5 での gRPC のパフォーマンスの向上](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="23957-216">gRPC の詳細については、「<xref:grpc/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## SignalR

### <a name="no-locsignalr-hub-filters"></a><span data-ttu-id="23957-217">SignalR ハブ フィルター</span><span class="sxs-lookup"><span data-stu-id="23957-217">SignalR Hub filters</span></span>

<span data-ttu-id="23957-218">SignalR ハブ フィルター (ASP.NET SignalR のハブ パイプラインと呼ばれます) は、ハブ メソッドが呼び出される前と後にコードを実行できる機能です。</span><span class="sxs-lookup"><span data-stu-id="23957-218">SignalR Hub filters, called Hub pipelines in ASP.NET SignalR, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="23957-219">ハブ メソッドが呼び出される前と後のコードの実行は、ミドルウェアが HTTP 要求の前と後にコードを実行する機能に似ています。</span><span class="sxs-lookup"><span data-stu-id="23957-219">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="23957-220">一般的な使用方法としては、ログ記録、エラー処理、引数の検証などがあります。</span><span class="sxs-lookup"><span data-stu-id="23957-220">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="23957-221">詳細については、「[ASP.NET Core SignalR のハブ フィルターを使用する](xref:signalr/hub-filters)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-221">For more information, see [Use hub filters in ASP.NET Core SignalR](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="23957-222">SignalR 並列ハブ呼び出し</span><span class="sxs-lookup"><span data-stu-id="23957-222">SignalR parallel hub invocations</span></span>

<span data-ttu-id="23957-223">ASP.NET Core SignalR では、並列ハブ呼び出しを処理できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-223">ASP.NET Core SignalR is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="23957-224">既定の動作を変更して、クライアントが一度に複数のハブ メソッドを呼び出せるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="23957-224">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="23957-225">SignalR Java クライアントへの Messagepack サポートの追加</span><span class="sxs-lookup"><span data-stu-id="23957-225">Added Messagepack support in SignalR Java client</span></span>

<span data-ttu-id="23957-226">新しいパッケージ [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack) によって、SignalR Java クライアントに MessagePack サポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="23957-226">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the SignalR Java client.</span></span> <span data-ttu-id="23957-227">MessagePack ハブ プロトコルを使用するには、接続ビルダーに `.withHubProtocol(new MessagePackHubProtocol())` を追加します。</span><span class="sxs-lookup"><span data-stu-id="23957-227">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* <span data-ttu-id="23957-228">構成を使用して再読み込み可能なエンドポイント: Kestrel では、`reloadOnChange` パラメーターが `true` の場合、[KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) に渡された構成の変更を検出し、アプリケーションを再起動することなく、既存のエンドポイントからバインド解除して、新しいエンドポイントにバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="23957-228">Reloadable endpoints via configuration: Kestrel can detect changes to configuration passed to [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="23957-229">既定では、<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> または <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> を使用する場合、Kestrel は `reloadOnChange` が有効な ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) 構成サブセクションにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="23957-229">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, Kestrel binds to the ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="23957-230">アプリでは、`KestrelServerOptions.Configure` を手動で呼び出す際に `reloadOnChange: true` を渡して、再読み込み可能なエンドポイントを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23957-230">Apps must pass `reloadOnChange: true` when calling `KestrelServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="23957-231">HTTP/2 応答ヘッダーの機能強化。</span><span class="sxs-lookup"><span data-stu-id="23957-231">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="23957-232">詳細については、次のセクションの「[パフォーマンスの向上](#performance-improvements)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-232">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="23957-233">ソケット転送における追加のエンドポイントの種類のサポート:<xref:System.Net.Sockets?displayProperty=nameWithType> に導入された新しい API に加えて、[Kestrel](xref:fundamentals/servers/kestrel) の既定のソケット転送で、既存のファイル ハンドルと Unix ドメイン ソケットの両方にバインドできるようになります。</span><span class="sxs-lookup"><span data-stu-id="23957-233">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [Kestrel](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="23957-234">既存のファイル ハンドルへのバインドのサポートによって、`libuv` 転送を必要とせずに、既存の `Systemd` 統合を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="23957-234">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="23957-235">Kestrel でのカスタム ヘッダーのデコード:アプリでは、既定の UTF-8 の代わりに、ヘッダー名に基づいて受信ヘッダーを解釈するために使用する <xref:System.Text.Encoding> を指定できます。</span><span class="sxs-lookup"><span data-stu-id="23957-235">Custom header decoding in Kestrel: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="23957-236">使用するエンコードを指定するには、</span><span class="sxs-lookup"><span data-stu-id="23957-236">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="23957-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="23957-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="23957-238">構成を使用した Kestrel エンドポイント固有のオプション</span><span class="sxs-lookup"><span data-stu-id="23957-238">Kestrel endpoint-specific options via configuration</span></span>

<span data-ttu-id="23957-239">[構成](xref:fundamentals/configuration/index)を使用して Kestrel のエンドポイント固有のオプションを構成するためのサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="23957-239">Support has been added for configuring Kestrel’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="23957-240">エンドポイント固有の構成には、次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="23957-240">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="23957-241">使用される HTTP プロトコル</span><span class="sxs-lookup"><span data-stu-id="23957-241">HTTP protocols used</span></span>
* <span data-ttu-id="23957-242">使用される TLS プロトコル</span><span class="sxs-lookup"><span data-stu-id="23957-242">TLS protocols used</span></span>
* <span data-ttu-id="23957-243">選択される証明書</span><span class="sxs-lookup"><span data-stu-id="23957-243">Certificate selected</span></span>
* <span data-ttu-id="23957-244">クライアント証明書モード</span><span class="sxs-lookup"><span data-stu-id="23957-244">Client certificate mode</span></span>

<span data-ttu-id="23957-245">構成によって、指定したサーバー名に基づいてどの証明書が選択されるかを指定できます。</span><span class="sxs-lookup"><span data-stu-id="23957-245">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="23957-246">サーバー名は、クライアントによって示される、TLS プロトコルの Server Name Indication (SNI) 拡張機能の一部です。</span><span class="sxs-lookup"><span data-stu-id="23957-246">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="23957-247">Kestrel の構成では、ホスト名のワイルドカード プレフィックスもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="23957-247">Kestrel's configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="23957-248">構成ファイルを使用してエンドポイント固有の設定を指定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="23957-248">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="23957-249">Server Name Indication (SNI) は、SSL ネゴシエーションの一部として仮想ドメインを含めるための TLS 拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="23957-249">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="23957-250">これは実質的に、仮想ドメイン名 (またはホスト名) を使用してネットワーク エンドポイントを識別できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="23957-250">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="23957-251">パフォーマンスの向上</span><span class="sxs-lookup"><span data-stu-id="23957-251">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="23957-252">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="23957-252">HTTP/2</span></span>

* <span data-ttu-id="23957-253">HTTP/2 コード パスでの割り当ての大幅な削減。</span><span class="sxs-lookup"><span data-stu-id="23957-253">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="23957-254">[Kestrel](xref:fundamentals/servers/kestrel)での HTTP/2 応答ヘッダーの [HPack 動的圧縮](https://tools.ietf.org/html/rfc7541)のサポート。</span><span class="sxs-lookup"><span data-stu-id="23957-254">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="23957-255">詳細については、「[ヘッダー テーブルのサイズ](xref:fundamentals/servers/kestrel/options#header-table-size)」と「[HPACK: HTTP/2 のサイレント機能](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-255">For more information, see [Header table size](xref:fundamentals/servers/kestrel/options#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="23957-256">HTTP/2 PING フレームの送信:HTTP/2 には、アイドル状態の接続がまだ機能していることを確認するために PING フレームを送信するメカニズムがあります。</span><span class="sxs-lookup"><span data-stu-id="23957-256">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="23957-257">実行可能な接続であることの確認は、アイドル状態であることが多いが間欠的にアクティビティが発生する、長期間存在するストリームを使用する場合に特に便利です。たとえば、gRPC ストリームなどです。</span><span class="sxs-lookup"><span data-stu-id="23957-257">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="23957-258">アプリでは、<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> に制限を設定することによって、[Kestrel](xref:fundamentals/servers/kestrel) で定期的に PING フレームを送信できます。</span><span class="sxs-lookup"><span data-stu-id="23957-258">Apps can send periodic PING frames in [Kestrel](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="23957-259">Containers</span><span class="sxs-lookup"><span data-stu-id="23957-259">Containers</span></span>

<span data-ttu-id="23957-260">.NET 5.0 より前のバージョンでは、ASP.NET Core アプリの *Dockerfile* をビルドして発行するには、.NET Core SDK と ASP.NET Core のイメージ全体をプルする必要がありました。</span><span class="sxs-lookup"><span data-stu-id="23957-260">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="23957-261">このリリースでは、SDK イメージをプルするバイトが削減され、ASP.NET Core イメージ用にプルされるバイトの大部分が削除されます。</span><span class="sxs-lookup"><span data-stu-id="23957-261">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="23957-262">詳細については、[こちらの GitHub イシューのコメント](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750)を参照してください</span><span class="sxs-lookup"><span data-stu-id="23957-262">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="23957-263">認証と承認</span><span class="sxs-lookup"><span data-stu-id="23957-263">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="23957-264">Microsoft.Identity.Web での Azure Active Directory 認証</span><span class="sxs-lookup"><span data-stu-id="23957-264">Azure Active Directory authentication with Microsoft.Identity.Web</span></span>

<span data-ttu-id="23957-265">ASP.NET Core のプロジェクト テンプレートが <xref:Microsoft.Identity.Web?displayProperty=fullName> と統合され、[Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) での認証を処理できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-265">The ASP.NET Core project templates now integrate with <xref:Microsoft.Identity.Web?displayProperty=fullName> to handle authentication with [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="23957-266">[Microsoft.Identity.Web パッケージ](https://www.nuget.org/packages/Microsoft.Identity.Web/)には次が備わっています。</span><span class="sxs-lookup"><span data-stu-id="23957-266">The [Microsoft.Identity.Web package](https://www.nuget.org/packages/Microsoft.Identity.Web/) provides:</span></span>

* <span data-ttu-id="23957-267">Azure AD を使用した認証のエクスペリエンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="23957-267">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="23957-268">[Microsoft Graph](/graph/overview) など、ユーザーに代わって Azure リソースにアクセスするための簡単な方法。</span><span class="sxs-lookup"><span data-stu-id="23957-268">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="23957-269">[Microsoft.Identity.Web のサンプル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)を参照してください。これは基本的なログインから始まり、マルチテナント、Azure API の使用、Microsoft Graph の使用、独自の API の保護に進んでいきます。</span><span class="sxs-lookup"><span data-stu-id="23957-269">See the [Microsoft.Identity.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="23957-270">`Microsoft.Identity.Web` は .NET 5 と共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="23957-270">`Microsoft.Identity.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="23957-271">エンドポイントへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="23957-271">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="23957-272">`AllowAnonymous` 拡張メソッドを使用すると、エンドポイントへの匿名アクセスを許可できます。</span><span class="sxs-lookup"><span data-stu-id="23957-272">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="23957-273">認可失敗のカスタム処理</span><span class="sxs-lookup"><span data-stu-id="23957-273">Custom handling of authorization failures</span></span>

<span data-ttu-id="23957-274">[認可](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A)[ミドルウェア](xref:fundamentals/middleware/index)によって呼び出される新しい [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) インターフェイスにより、認可失敗のカスタム処理がより簡単になりました。</span><span class="sxs-lookup"><span data-stu-id="23957-274">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="23957-275">既定の実装は変わりませんが、カスタム ハンドラーを依存関係の挿入に登録できます。これにより、認可が失敗した理由に基づくカスタム HTTP 応答が可能になります。</span><span class="sxs-lookup"><span data-stu-id="23957-275">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="23957-276">`IAuthorizationMiddlewareResultHandler` の使用方法を示す[こちらのサンプル](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-276">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="23957-277">エンドポイント ルーティングを使用する場合の認可</span><span class="sxs-lookup"><span data-stu-id="23957-277">Authorization when using endpoint routing</span></span>

<span data-ttu-id="23957-278">エンドポイント ルーティングを使用する場合の認可では、エンドポイントのインスタンスではなく `HttpContext` を受信するようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-278">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="23957-279">これにより、認可ミドルウェアで、<xref:Microsoft.AspNetCore.Http.Endpoint> クラスではアクセスできなかった `RouteData` や `HttpContext` のその他のプロパティにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="23957-279">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="23957-280">エンドポイントは、[context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A) を使用してコンテキストからフェッチできます。</span><span class="sxs-lookup"><span data-stu-id="23957-280">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="23957-281">Linux での Kerberos 認証と LDAP を使用したロールベースのアクセス制御</span><span class="sxs-lookup"><span data-stu-id="23957-281">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="23957-282">「[Kerberos 認証とロールベースのアクセス制御 (RBAC)](xref:security/authentication/windowsauth#rbac)」を参照してください</span><span class="sxs-lookup"><span data-stu-id="23957-282">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="23957-283">API の機能強化</span><span class="sxs-lookup"><span data-stu-id="23957-283">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="23957-284">HttpRequest と HttpResponse 用の JSON 拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="23957-284">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="23957-285">新しい <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> および `WriteAsJsonAsync` 拡張メソッドを使用して、`HttpRequest` および `HttpResponse` に対する JSON データの読み取りと書き込みを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="23957-285">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="23957-286">これらの拡張メソッドでは、[System.Text.Json](xref:System.Text.Json) シリアライザーを使用して JSON データが処理されます。</span><span class="sxs-lookup"><span data-stu-id="23957-286">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="23957-287">新しい `HasJsonContentType` 拡張メソッドを使用すれば、要求に JSON コンテンツ タイプがあるかどうかを確認することもできます。</span><span class="sxs-lookup"><span data-stu-id="23957-287">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="23957-288">JSON 拡張メソッドを [エンドポイント ルーティング](xref:fundamentals/routing)と組み合わせることで、"\***コードへのルーティング** _" と呼ばれるプログラミングのスタイルで JSON API を作成できます。</span><span class="sxs-lookup"><span data-stu-id="23957-288">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \***route to code** _.</span></span> <span data-ttu-id="23957-289">これは、基本的な JSON API を軽量な方法で作成したい開発者向けの新しいオプションです。</span><span class="sxs-lookup"><span data-stu-id="23957-289">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="23957-290">たとえば、少数のエンドポイントのみを持つ Web アプリでは、ASP.NET Core MVC の完全な機能ではなく、コードへのルーティングを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="23957-290">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="23957-291">新しい JSON 拡張メソッドとコードへのルーティングについて詳しくは、[こちらの .NET ショー](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23957-291">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="23957-292">System.Diagnostics.Activity</span><span class="sxs-lookup"><span data-stu-id="23957-292">System.Diagnostics.Activity</span></span>

<span data-ttu-id="23957-293"><xref:System.Diagnostics.Activity?displayProperty=fullName> の既定の形式が W3C 形式になりました。</span><span class="sxs-lookup"><span data-stu-id="23957-293">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="23957-294">これにより、ASP.NET Core での分散トレースのサポートが、既定でより多くのフレームワークと相互運用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="23957-294">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="23957-295">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="23957-295">FromBodyAttribute</span></span>

<span data-ttu-id="23957-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> で、次のパラメーターまたはプロパティを省略可能と見なすことができるオプションを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> now supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="23957-297">その他の機能強化</span><span class="sxs-lookup"><span data-stu-id="23957-297">Miscellaneous improvements</span></span>

<span data-ttu-id="23957-298">ASP.NET Core アセンブリに [Null 許容の注釈](/dotnet/csharp/nullable-references#attributes-describe-apis)を適用する作業が開始されました。</span><span class="sxs-lookup"><span data-stu-id="23957-298">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="23957-299">.NET 5 フレームワークの一般的なパブリック API サーフェイスのほとんどに注釈が付けられる予定です。</span><span class="sxs-lookup"><span data-stu-id="23957-299">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="23957-300">Startup クラスのアクティブ化を制御する</span><span class="sxs-lookup"><span data-stu-id="23957-300">Control Startup class activation</span></span>

<span data-ttu-id="23957-301">`Startup` クラスのアクティブ化を制御するためのファクトリ メソッドをアプリで提供できるようにする、新しい <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> オーバーロードが追加されました。</span><span class="sxs-lookup"><span data-stu-id="23957-301">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="23957-302">`Startup` クラスのアクティブ化の制御は、ホストと共に初期化される `Startup` に追加のパラメーターを渡す場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="23957-302">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="23957-303">dotnet watch での自動更新</span><span class="sxs-lookup"><span data-stu-id="23957-303">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="23957-304">.NET 5 では、ASP.NET Core プロジェクトで [dotnet watch](xref:tutorials/dotnet-watch) を実行すると、既定のブラウザーが起動され、コードに変更が加えられるとブラウザーが自動的に更新されます。</span><span class="sxs-lookup"><span data-stu-id="23957-304">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="23957-305">つまり、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="23957-305">This means you can:</span></span>

<span data-ttu-id="23957-306">_ テキスト エディターで ASP.NET Core プロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="23957-306">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="23957-307">`dotnet watch` を実行します。</span><span class="sxs-lookup"><span data-stu-id="23957-307">Run `dotnet watch`.</span></span>
* <span data-ttu-id="23957-308">ツールによってアプリのリビルド、再起動、再読み込みを処理しながら、コードの変更に集中します。</span><span class="sxs-lookup"><span data-stu-id="23957-308">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="23957-309">コンソール ロガー フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="23957-309">Console Logger Formatter</span></span>

<span data-ttu-id="23957-310">`Microsoft.Extensions.Logging` ライブラリのコンソール ログ プロバイダーが改善されました。</span><span class="sxs-lookup"><span data-stu-id="23957-310">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="23957-311">開発者は、カスタム `ConsoleFormatter` を実装して、コンソール出力の書式設定と色付けを完全に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="23957-311">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="23957-312">フォーマッタ API により、VT-100 エスケープ シーケンスのサブセットを実装することによって、豊富な書式設定が可能になります。</span><span class="sxs-lookup"><span data-stu-id="23957-312">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="23957-313">VT-100 は、最新のターミナルのほとんどでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="23957-313">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="23957-314">コンソール ロガーでは、サポートされていないターミナルでエスケープ シーケンスを解析できるため、開発者はすべてのターミナル用に 1 つのフォーマッタを作成できます。</span><span class="sxs-lookup"><span data-stu-id="23957-314">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="23957-315">JSON コンソール ロガー</span><span class="sxs-lookup"><span data-stu-id="23957-315">JSON Console Logger</span></span>

<span data-ttu-id="23957-316">カスタム フォーマッタのサポートに加えて、構造化された JSON ログをコンソールに出力する組み込みの JSON フォーマッタも追加されました。</span><span class="sxs-lookup"><span data-stu-id="23957-316">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="23957-317">次のコードは、既定のロガーから JSON に切り替える方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="23957-317">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="23957-318">コンソールに出力されるログ メッセージは JSON 形式になります。</span><span class="sxs-lookup"><span data-stu-id="23957-318">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```

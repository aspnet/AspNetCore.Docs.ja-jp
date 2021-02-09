---
title: ASP.NET Core のグローバリゼーションおよびローカリゼーション
author: rick-anderson
description: ASP.NET Core がコンテンツをさまざまな言語と文化にローカライズするために提供するサービスとミドルウェアについて説明します。
ms.author: riande
ms.date: 11/30/2019
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
uid: fundamentals/localization
ms.openlocfilehash: 67f245b7f4e4aa97b30c5318c73732617aea44c7
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217571"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="3b6be-103">ASP.NET Core のグローバリゼーションおよびローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="3b6be-103">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3b6be-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="3b6be-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="3b6be-105">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-105">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="3b6be-106">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="3b6be-107">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="3b6be-108">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="3b6be-109">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="3b6be-110">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="3b6be-111">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="3b6be-112">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-112">App localization involves the following:</span></span>

1. <span data-ttu-id="3b6be-113">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="3b6be-113">Make the app's content localizable</span></span>
1. <span data-ttu-id="3b6be-114">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="3b6be-114">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="3b6be-115">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="3b6be-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="3b6be-116">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3b6be-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="3b6be-117">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="3b6be-117">Make the app's content localizable</span></span>

<span data-ttu-id="3b6be-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="3b6be-119">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-119">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="3b6be-120">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-120">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="3b6be-121">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-121">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="3b6be-122">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="3b6be-123">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="3b6be-124">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-124">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="3b6be-125">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="3b6be-126">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="3b6be-127">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="3b6be-128">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="3b6be-129">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="3b6be-130">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="3b6be-131">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="3b6be-132">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="3b6be-133">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="3b6be-134">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-134">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="3b6be-135">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="3b6be-136">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="3b6be-137">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="3b6be-138">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="3b6be-139">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="3b6be-140">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="3b6be-141">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="3b6be-141">View localization</span></span>

<span data-ttu-id="3b6be-142">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="3b6be-143">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="3b6be-144">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="3b6be-145">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="3b6be-146">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="3b6be-147">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="3b6be-148">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="3b6be-149">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="3b6be-150">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="3b6be-151">Key</span><span class="sxs-lookup"><span data-stu-id="3b6be-151">Key</span></span> | <span data-ttu-id="3b6be-152">[値]</span><span class="sxs-lookup"><span data-stu-id="3b6be-152">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="3b6be-153">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-153">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="3b6be-154">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-154">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="3b6be-155">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="3b6be-156">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="3b6be-156">DataAnnotations localization</span></span>

<span data-ttu-id="3b6be-157">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="3b6be-158">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="3b6be-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="3b6be-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="3b6be-161">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="3b6be-162">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索 **しません**。</span><span class="sxs-lookup"><span data-stu-id="3b6be-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="3b6be-163">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="3b6be-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="3b6be-164">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="3b6be-165">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="3b6be-166">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="3b6be-167">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="3b6be-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="3b6be-168">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="3b6be-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="3b6be-169">ASP.NET Core では、<xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedCultures> と <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedUICultures> という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-169">ASP.NET Core allows you to specify two culture values, <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedCultures> and <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedUICultures>.</span></span> <span data-ttu-id="3b6be-170">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の <xref:System.Globalization.CultureInfo> オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-170">The <xref:System.Globalization.CultureInfo> object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="3b6be-171">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="3b6be-172">サーバーによるカルチャの取得方法の詳細については、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=nameWithType> と <xref:System.Globalization.CultureInfo.CurrentUICulture?displayProperty=nameWithType> に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-172">For more information on how the server obtains the culture, see <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.CurrentUICulture?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3b6be-173">`SupportedUICultures` は、 `.resx` ファイルからのどの翻訳文字列が <xref:System.Resources.ResourceManager> によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-173">The `SupportedUICultures` determines which translated strings (from `.resx` files) are looked up by the <xref:System.Resources.ResourceManager>.</span></span> <span data-ttu-id="3b6be-174">`ResourceManager` では、`CurrentUICulture` によって決定されるカルチャ固有の文字列が検索されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-174">The `ResourceManager` looks up culture-specific strings that are determined by `CurrentUICulture`.</span></span> <span data-ttu-id="3b6be-175">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="3b6be-176">フレームワークでは、カルチャに依存する関数を表示するときに、これらの値が検査されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-176">The framework inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="3b6be-177">現在のスレッドのカルチャが `en-US` (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` によって `Thursday, February 18, 2016` が表示されます。ただし、`CurrentCulture` が `es-ES` (スペイン語、スペイン) に設定されている場合は、出力は `jueves, 18 de febrero de 2016` になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-177">If the current thread's culture is set to `en-US` (English, United States), `DateTime.Now.ToLongDateString()` displays `Thursday, February 18, 2016`; but if `CurrentCulture` is set to `es-ES` (Spanish, Spain), the output is `jueves, 18 de febrero de 2016`.</span></span>

## <a name="resource-files"></a><span data-ttu-id="3b6be-178">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="3b6be-178">Resource files</span></span>

<span data-ttu-id="3b6be-179">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="3b6be-180">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="3b6be-181">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="3b6be-182">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="3b6be-183">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="3b6be-184">**ソリューション エクスプローラー** で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

1. <span data-ttu-id="3b6be-187">**インストールされているテンプレートの検索** ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

1. <span data-ttu-id="3b6be-189">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

   <span data-ttu-id="3b6be-191">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="3b6be-193">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="3b6be-193">Resource file naming</span></span>

<span data-ttu-id="3b6be-194">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="3b6be-195">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="3b6be-196">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-197">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="3b6be-198">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="3b6be-199">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-200">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="3b6be-201">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-202">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="3b6be-203">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-204">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="3b6be-205">リソース名</span><span class="sxs-lookup"><span data-stu-id="3b6be-205">Resource name</span></span> | <span data-ttu-id="3b6be-206">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="3b6be-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="3b6be-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="3b6be-208">ドット</span><span class="sxs-lookup"><span data-stu-id="3b6be-208">Dot</span></span>  |
| <span data-ttu-id="3b6be-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="3b6be-210">パス</span><span class="sxs-lookup"><span data-stu-id="3b6be-210">Path</span></span> |

<span data-ttu-id="3b6be-211">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="3b6be-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="3b6be-212">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="3b6be-213">Razor ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="3b6be-214">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="3b6be-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="3b6be-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="3b6be-217">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="3b6be-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="3b6be-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="3b6be-219">アセンブリのルート名前空間がアセンブリ名と異なると、<xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-219">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="3b6be-220">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="3b6be-221">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="3b6be-222">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="3b6be-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="3b6be-223">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-223">Localization does not work by default.</span></span>
* <span data-ttu-id="3b6be-224">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="3b6be-225">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="3b6be-226">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="3b6be-227">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="3b6be-228">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="3b6be-228">Culture fallback behavior</span></span>

<span data-ttu-id="3b6be-229">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="3b6be-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="3b6be-230">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="3b6be-231">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="3b6be-232">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="3b6be-233">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="3b6be-234">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="3b6be-235">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="3b6be-236">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="3b6be-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="3b6be-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="3b6be-239">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="3b6be-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="3b6be-240">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="3b6be-241">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="3b6be-242">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="3b6be-243">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="3b6be-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="3b6be-244">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="3b6be-245">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="3b6be-246">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="3b6be-247">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="3b6be-248">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="3b6be-249">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="3b6be-249">Add other cultures</span></span>

<span data-ttu-id="3b6be-250">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="3b6be-251">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="3b6be-252">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="3b6be-253">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="3b6be-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="3b6be-254">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="3b6be-254">Configure localization</span></span>

<span data-ttu-id="3b6be-255">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="3b6be-256">`AddLocalization` は、ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-256">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="3b6be-257">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="3b6be-258">`AddViewLocalization` は、ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-258">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="3b6be-259">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="3b6be-260">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="3b6be-261">`AddDataAnnotationsLocalization` は、`IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-261">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="3b6be-262">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="3b6be-262">Localization middleware</span></span>

<span data-ttu-id="3b6be-263">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="3b6be-264">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="3b6be-265">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3b6be-266">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="3b6be-267">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="3b6be-268">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="3b6be-269">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="3b6be-270">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="3b6be-271">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="3b6be-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3b6be-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="3b6be-273">いくつかのアプリでは、クエリ文字列を使用して、<https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-273">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="3b6be-274">cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="3b6be-275">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="3b6be-276">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="3b6be-277">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="3b6be-278">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="3b6be-279">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="3b6be-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3b6be-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="3b6be-281">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="3b6be-282">cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="3b6be-283">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="3b6be-284">既定の cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="3b6be-285">cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="3b6be-286">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="3b6be-287">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="3b6be-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="3b6be-288">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="3b6be-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="3b6be-289">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="3b6be-290">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="3b6be-291">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="3b6be-292">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="3b6be-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="3b6be-293">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-293">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="3b6be-294">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-294">Tap **Languages**.</span></span>

   ![インターネット オプション](localization/_static/lang.png)

1. <span data-ttu-id="3b6be-296">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-296">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="3b6be-297">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-297">Tap **Add a language**.</span></span>

1. <span data-ttu-id="3b6be-298">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-298">Add the language.</span></span>

1. <span data-ttu-id="3b6be-299">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-299">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="3b6be-300">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="3b6be-300">Use a custom provider</span></span>

<span data-ttu-id="3b6be-301">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-301">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="3b6be-302">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-302">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="3b6be-303">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-303">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="3b6be-304">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-304">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="3b6be-305">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="3b6be-305">Set the culture programmatically</span></span>

<span data-ttu-id="3b6be-306">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-306">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="3b6be-307">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="3b6be-308">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-308">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="3b6be-309">`SetLanguage` メソッドはカルチャ cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-309">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="3b6be-310">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-310">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="3b6be-311">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-311">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="3b6be-312">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="3b6be-312">Model binding route data and query strings</span></span>

<span data-ttu-id="3b6be-313">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-313">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="3b6be-314">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="3b6be-314">Globalization and localization terms</span></span>

<span data-ttu-id="3b6be-315">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-315">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="3b6be-316">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-316">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="3b6be-317">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-317">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="3b6be-318">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-318">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="3b6be-319">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-319">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="3b6be-320">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-320">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="3b6be-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="3b6be-322">以下を参照してください。<https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)></span><span class="sxs-lookup"><span data-stu-id="3b6be-322">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="3b6be-323">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-323">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="3b6be-324">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-324">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="3b6be-325">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-325">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="3b6be-326">用語:</span><span class="sxs-lookup"><span data-stu-id="3b6be-326">Terms:</span></span>

* <span data-ttu-id="3b6be-327">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="3b6be-327">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="3b6be-328">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="3b6be-328">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="3b6be-329">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-329">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="3b6be-330">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-330">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="3b6be-331">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-331">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="3b6be-332">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="3b6be-332">(for example "en", "es")</span></span>
* <span data-ttu-id="3b6be-333">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-333">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="3b6be-334">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="3b6be-334">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="3b6be-335">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-335">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="3b6be-336">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-336">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="3b6be-337">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-337">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="3b6be-338">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3b6be-338">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="3b6be-339">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-339">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="3b6be-340">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="3b6be-340">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="3b6be-341">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="3b6be-341">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="3b6be-342">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="3b6be-342">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="3b6be-343">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="3b6be-343">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3b6be-344">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="3b6be-344">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="3b6be-345">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-345">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="3b6be-346">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-346">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="3b6be-347">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-347">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="3b6be-348">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-348">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="3b6be-349">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-349">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="3b6be-350">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-350">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="3b6be-351">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-351">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="3b6be-352">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-352">App localization involves the following:</span></span>

1. <span data-ttu-id="3b6be-353">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="3b6be-353">Make the app's content localizable</span></span>
1. <span data-ttu-id="3b6be-354">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="3b6be-354">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="3b6be-355">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="3b6be-355">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="3b6be-356">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3b6be-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="3b6be-357">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="3b6be-357">Make the app's content localizable</span></span>

<span data-ttu-id="3b6be-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="3b6be-359">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-359">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="3b6be-360">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-360">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="3b6be-361">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-361">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="3b6be-362">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-362">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="3b6be-363">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-363">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="3b6be-364">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-364">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="3b6be-365">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-365">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="3b6be-366">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-366">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="3b6be-367">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-367">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="3b6be-368">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-368">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="3b6be-369">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-369">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="3b6be-370">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-370">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="3b6be-371">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-371">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="3b6be-372">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-372">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="3b6be-373">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-373">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="3b6be-374">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-374">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="3b6be-375">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-375">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="3b6be-376">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-376">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="3b6be-377">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-377">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="3b6be-378">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-378">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="3b6be-379">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-379">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="3b6be-380">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-380">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="3b6be-381">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="3b6be-381">View localization</span></span>

<span data-ttu-id="3b6be-382">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-382">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="3b6be-383">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-383">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="3b6be-384">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-384">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="3b6be-385">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-385">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="3b6be-386">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-386">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="3b6be-387">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-387">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="3b6be-388">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-388">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="3b6be-389">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-389">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="3b6be-390">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-390">A French resource file could contain the following:</span></span>

| <span data-ttu-id="3b6be-391">Key</span><span class="sxs-lookup"><span data-stu-id="3b6be-391">Key</span></span> | <span data-ttu-id="3b6be-392">[値]</span><span class="sxs-lookup"><span data-stu-id="3b6be-392">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="3b6be-393">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-393">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="3b6be-394">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-394">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="3b6be-395">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-395">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="3b6be-396">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="3b6be-396">DataAnnotations localization</span></span>

<span data-ttu-id="3b6be-397">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-397">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="3b6be-398">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-398">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="3b6be-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="3b6be-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="3b6be-401">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-401">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="3b6be-402">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索 **しません**。</span><span class="sxs-lookup"><span data-stu-id="3b6be-402">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="3b6be-403">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="3b6be-403">Using one resource string for multiple classes</span></span>

<span data-ttu-id="3b6be-404">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-404">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="3b6be-405">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-405">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="3b6be-406">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-406">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="3b6be-407">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="3b6be-407">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="3b6be-408">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="3b6be-408">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="3b6be-409">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-409">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="3b6be-410">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-410">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="3b6be-411">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-411">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="3b6be-412">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-412">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="3b6be-413">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-413">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="3b6be-414">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-414">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="3b6be-415">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-415">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="3b6be-416">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-416">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="3b6be-417">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-417">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="3b6be-418">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="3b6be-418">Resource files</span></span>

<span data-ttu-id="3b6be-419">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-419">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="3b6be-420">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-420">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="3b6be-421">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-421">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="3b6be-422">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-422">"es" is the language code for Spanish.</span></span> <span data-ttu-id="3b6be-423">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-423">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="3b6be-424">**ソリューション エクスプローラー** で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-424">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

1. <span data-ttu-id="3b6be-427">**インストールされているテンプレートの検索** ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-427">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

1. <span data-ttu-id="3b6be-429">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-429">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

   <span data-ttu-id="3b6be-431">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-431">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="3b6be-433">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="3b6be-433">Resource file naming</span></span>

<span data-ttu-id="3b6be-434">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-434">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="3b6be-435">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-435">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="3b6be-436">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-436">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-437">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-437">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="3b6be-438">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-438">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="3b6be-439">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-439">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-440">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-440">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="3b6be-441">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-441">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-442">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-442">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="3b6be-443">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-443">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-444">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-444">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="3b6be-445">リソース名</span><span class="sxs-lookup"><span data-stu-id="3b6be-445">Resource name</span></span> | <span data-ttu-id="3b6be-446">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="3b6be-446">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="3b6be-447">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-447">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="3b6be-448">ドット</span><span class="sxs-lookup"><span data-stu-id="3b6be-448">Dot</span></span>  |
| <span data-ttu-id="3b6be-449">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-449">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="3b6be-450">パス</span><span class="sxs-lookup"><span data-stu-id="3b6be-450">Path</span></span> |

<span data-ttu-id="3b6be-451">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="3b6be-451">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="3b6be-452">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-452">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="3b6be-453">Razor ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-453">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="3b6be-454">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-454">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="3b6be-455">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-455">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="3b6be-456">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-456">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="3b6be-457">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-457">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="3b6be-458">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="3b6be-458">RootNamespaceAttribute</span></span> 

<span data-ttu-id="3b6be-459">アセンブリのルート名前空間がアセンブリ名と異なると、<xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-459">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="3b6be-460">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-460">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="3b6be-461">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-461">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="3b6be-462">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="3b6be-462">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="3b6be-463">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-463">Localization does not work by default.</span></span>
* <span data-ttu-id="3b6be-464">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-464">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="3b6be-465">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-465">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="3b6be-466">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-466">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="3b6be-467">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-467">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="3b6be-468">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="3b6be-468">Culture fallback behavior</span></span>

<span data-ttu-id="3b6be-469">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="3b6be-469">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="3b6be-470">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-470">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="3b6be-471">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-471">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="3b6be-472">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-472">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="3b6be-473">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-473">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="3b6be-474">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-474">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="3b6be-475">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-475">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="3b6be-476">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-476">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="3b6be-477">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-477">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="3b6be-478">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-478">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="3b6be-479">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="3b6be-479">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="3b6be-480">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-480">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="3b6be-481">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-481">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="3b6be-482">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-482">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="3b6be-483">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="3b6be-483">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="3b6be-484">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-484">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="3b6be-485">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-485">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="3b6be-486">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-486">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="3b6be-487">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-487">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="3b6be-488">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-488">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="3b6be-489">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="3b6be-489">Add other cultures</span></span>

<span data-ttu-id="3b6be-490">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-490">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="3b6be-491">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-491">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="3b6be-492">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-492">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="3b6be-493">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="3b6be-493">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="3b6be-494">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="3b6be-494">Configure localization</span></span>

<span data-ttu-id="3b6be-495">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-495">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="3b6be-496">`AddLocalization` は、ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-496">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="3b6be-497">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-497">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="3b6be-498">`AddViewLocalization` は、ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-498">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="3b6be-499">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-499">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="3b6be-500">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-500">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="3b6be-501">`AddDataAnnotationsLocalization` は、`IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-501">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="3b6be-502">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="3b6be-502">Localization middleware</span></span>

<span data-ttu-id="3b6be-503">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-503">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="3b6be-504">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-504">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="3b6be-505">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-505">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3b6be-506">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-506">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="3b6be-507">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-507">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="3b6be-508">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-508">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="3b6be-509">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-509">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="3b6be-510">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-510">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="3b6be-511">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-511">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="3b6be-512">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3b6be-512">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="3b6be-513">いくつかのアプリでは、クエリ文字列を使用して、<https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-513">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="3b6be-514">cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-514">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="3b6be-515">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-515">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="3b6be-516">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-516">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="3b6be-517">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-517">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="3b6be-518">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-518">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="3b6be-519">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-519">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="3b6be-520">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3b6be-520">CookieRequestCultureProvider</span></span>

<span data-ttu-id="3b6be-521">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-521">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="3b6be-522">cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-522">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="3b6be-523">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-523">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="3b6be-524">既定の cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-524">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="3b6be-525">cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-525">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="3b6be-526">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-526">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="3b6be-527">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="3b6be-527">The Accept-Language HTTP header</span></span>

<span data-ttu-id="3b6be-528">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="3b6be-528">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="3b6be-529">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-529">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="3b6be-530">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-530">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="3b6be-531">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-531">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="3b6be-532">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="3b6be-532">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="3b6be-533">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-533">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="3b6be-534">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-534">Tap **Languages**.</span></span>

   ![インターネット オプション](localization/_static/lang.png)

1. <span data-ttu-id="3b6be-536">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-536">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="3b6be-537">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-537">Tap **Add a language**.</span></span>

1. <span data-ttu-id="3b6be-538">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-538">Add the language.</span></span>

1. <span data-ttu-id="3b6be-539">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-539">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="3b6be-540">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="3b6be-540">Use a custom provider</span></span>

<span data-ttu-id="3b6be-541">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-541">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="3b6be-542">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-542">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="3b6be-543">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-543">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="3b6be-544">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-544">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="3b6be-545">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="3b6be-545">Set the culture programmatically</span></span>

<span data-ttu-id="3b6be-546">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-546">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="3b6be-547">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-547">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="3b6be-548">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-548">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="3b6be-549">`SetLanguage` メソッドはカルチャ cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-549">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="3b6be-550">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-550">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="3b6be-551">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-551">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="3b6be-552">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="3b6be-552">Model binding route data and query strings</span></span>

<span data-ttu-id="3b6be-553">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-553">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="3b6be-554">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="3b6be-554">Globalization and localization terms</span></span>

<span data-ttu-id="3b6be-555">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-555">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="3b6be-556">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-556">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="3b6be-557">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-557">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="3b6be-558">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-558">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="3b6be-559">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-559">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="3b6be-560">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-560">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="3b6be-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="3b6be-562">以下を参照してください。<https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)></span><span class="sxs-lookup"><span data-stu-id="3b6be-562">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="3b6be-563">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-563">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="3b6be-564">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-564">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="3b6be-565">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-565">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="3b6be-566">用語:</span><span class="sxs-lookup"><span data-stu-id="3b6be-566">Terms:</span></span>

* <span data-ttu-id="3b6be-567">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="3b6be-567">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="3b6be-568">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="3b6be-568">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="3b6be-569">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-569">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="3b6be-570">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-570">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="3b6be-571">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-571">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="3b6be-572">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="3b6be-572">(for example "en", "es")</span></span>
* <span data-ttu-id="3b6be-573">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-573">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="3b6be-574">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="3b6be-574">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="3b6be-575">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-575">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="3b6be-576">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-576">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="3b6be-577">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-577">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="3b6be-578">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3b6be-578">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="3b6be-579">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-579">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="3b6be-580">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="3b6be-580">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="3b6be-581">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="3b6be-581">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="3b6be-582">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="3b6be-582">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="3b6be-583">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="3b6be-583">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="3b6be-584">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="3b6be-584">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="3b6be-585">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-585">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="3b6be-586">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-586">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="3b6be-587">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-587">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="3b6be-588">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-588">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="3b6be-589">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-589">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="3b6be-590">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-590">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="3b6be-591">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-591">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="3b6be-592">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-592">App localization involves the following:</span></span>

1. <span data-ttu-id="3b6be-593">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="3b6be-593">Make the app's content localizable</span></span>
1. <span data-ttu-id="3b6be-594">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="3b6be-594">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="3b6be-595">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="3b6be-595">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="3b6be-596">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3b6be-596">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="3b6be-597">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="3b6be-597">Make the app's content localizable</span></span>

<span data-ttu-id="3b6be-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="3b6be-599">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-599">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="3b6be-600">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-600">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="3b6be-601">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-601">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="3b6be-602">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-602">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="3b6be-603">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-603">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="3b6be-604">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-604">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="3b6be-605">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-605">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="3b6be-606">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-606">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="3b6be-607">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-607">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="3b6be-608">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-608">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="3b6be-609">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-609">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="3b6be-610">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-610">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="3b6be-611">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-611">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="3b6be-612">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-612">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="3b6be-613">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-613">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="3b6be-614">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-614">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="3b6be-615">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-615">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="3b6be-616">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-616">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="3b6be-617">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-617">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="3b6be-618">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-618">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="3b6be-619">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-619">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="3b6be-620">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-620">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="3b6be-621">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="3b6be-621">View localization</span></span>

<span data-ttu-id="3b6be-622">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-622">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="3b6be-623">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-623">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="3b6be-624">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-624">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="3b6be-625">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-625">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="3b6be-626">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-626">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="3b6be-627">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-627">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="3b6be-628">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-628">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="3b6be-629">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-629">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="3b6be-630">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-630">A French resource file could contain the following:</span></span>

| <span data-ttu-id="3b6be-631">Key</span><span class="sxs-lookup"><span data-stu-id="3b6be-631">Key</span></span> | <span data-ttu-id="3b6be-632">[値]</span><span class="sxs-lookup"><span data-stu-id="3b6be-632">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="3b6be-633">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-633">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="3b6be-634">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-634">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="3b6be-635">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-635">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="3b6be-636">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="3b6be-636">DataAnnotations localization</span></span>

<span data-ttu-id="3b6be-637">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-637">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="3b6be-638">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-638">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="3b6be-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="3b6be-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="3b6be-641">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-641">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="3b6be-642">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索 **しません**。</span><span class="sxs-lookup"><span data-stu-id="3b6be-642">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="3b6be-643">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="3b6be-643">Using one resource string for multiple classes</span></span>

<span data-ttu-id="3b6be-644">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-644">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="3b6be-645">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-645">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="3b6be-646">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-646">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="3b6be-647">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="3b6be-647">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="3b6be-648">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="3b6be-648">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="3b6be-649">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-649">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="3b6be-650">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-650">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="3b6be-651">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-651">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="3b6be-652">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-652">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="3b6be-653">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-653">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="3b6be-654">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-654">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="3b6be-655">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-655">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="3b6be-656">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-656">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="3b6be-657">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-657">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="3b6be-658">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="3b6be-658">Resource files</span></span>

<span data-ttu-id="3b6be-659">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-659">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="3b6be-660">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-660">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="3b6be-661">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-661">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="3b6be-662">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-662">"es" is the language code for Spanish.</span></span> <span data-ttu-id="3b6be-663">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-663">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="3b6be-664">**ソリューション エクスプローラー** で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-664">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

1. <span data-ttu-id="3b6be-667">**インストールされているテンプレートの検索** ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-667">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

1. <span data-ttu-id="3b6be-669">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-669">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

   <span data-ttu-id="3b6be-671">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-671">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="3b6be-673">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="3b6be-673">Resource file naming</span></span>

<span data-ttu-id="3b6be-674">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-674">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="3b6be-675">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-675">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="3b6be-676">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-676">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-677">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-677">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="3b6be-678">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-678">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="3b6be-679">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-679">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-680">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-680">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="3b6be-681">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-681">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-682">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-682">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="3b6be-683">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-683">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3b6be-684">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-684">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="3b6be-685">リソース名</span><span class="sxs-lookup"><span data-stu-id="3b6be-685">Resource name</span></span> | <span data-ttu-id="3b6be-686">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="3b6be-686">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="3b6be-687">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-687">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="3b6be-688">ドット</span><span class="sxs-lookup"><span data-stu-id="3b6be-688">Dot</span></span>  |
| <span data-ttu-id="3b6be-689">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-689">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="3b6be-690">パス</span><span class="sxs-lookup"><span data-stu-id="3b6be-690">Path</span></span> |

<span data-ttu-id="3b6be-691">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="3b6be-691">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="3b6be-692">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-692">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="3b6be-693">Razor ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-693">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="3b6be-694">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-694">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="3b6be-695">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-695">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="3b6be-696">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3b6be-696">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="3b6be-697">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-697">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="3b6be-698">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="3b6be-698">RootNamespaceAttribute</span></span> 

<span data-ttu-id="3b6be-699">アセンブリのルート名前空間がアセンブリ名と異なると、<xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-699">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="3b6be-700">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-700">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="3b6be-701">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-701">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="3b6be-702">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="3b6be-702">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="3b6be-703">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-703">Localization does not work by default.</span></span>
* <span data-ttu-id="3b6be-704">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-704">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="3b6be-705">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-705">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="3b6be-706">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-706">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="3b6be-707">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-707">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="3b6be-708">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="3b6be-708">Culture fallback behavior</span></span>

<span data-ttu-id="3b6be-709">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="3b6be-709">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="3b6be-710">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-710">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="3b6be-711">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-711">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="3b6be-712">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-712">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="3b6be-713">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-713">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="3b6be-714">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-714">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="3b6be-715">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-715">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="3b6be-716">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-716">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="3b6be-717">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-717">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="3b6be-718">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3b6be-718">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="3b6be-719">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="3b6be-719">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="3b6be-720">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-720">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="3b6be-721">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-721">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="3b6be-722">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-722">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="3b6be-723">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="3b6be-723">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="3b6be-724">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-724">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="3b6be-725">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-725">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="3b6be-726">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-726">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="3b6be-727">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-727">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="3b6be-728">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-728">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="3b6be-729">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="3b6be-729">Add other cultures</span></span>

<span data-ttu-id="3b6be-730">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-730">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="3b6be-731">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-731">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="3b6be-732">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-732">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="3b6be-733">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="3b6be-733">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="3b6be-734">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="3b6be-734">Configure localization</span></span>

<span data-ttu-id="3b6be-735">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-735">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="3b6be-736">`AddLocalization` は、ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-736">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="3b6be-737">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-737">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="3b6be-738">`AddViewLocalization` は、ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-738">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="3b6be-739">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-739">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="3b6be-740">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-740">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="3b6be-741">`AddDataAnnotationsLocalization` は、`IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-741">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="3b6be-742">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="3b6be-742">Localization middleware</span></span>

<span data-ttu-id="3b6be-743">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-743">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="3b6be-744">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-744">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="3b6be-745">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-745">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3b6be-746">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-746">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="3b6be-747">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-747">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="3b6be-748">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-748">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="3b6be-749">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-749">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="3b6be-750">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-750">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="3b6be-751">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-751">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="3b6be-752">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3b6be-752">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="3b6be-753">いくつかのアプリでは、クエリ文字列を使用して、<xref:System.Globalization.CultureInfo>を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-753">Some apps will use a query string to set the <xref:System.Globalization.CultureInfo>.</span></span> <span data-ttu-id="3b6be-754">cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-754">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="3b6be-755">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-755">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="3b6be-756">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-756">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="3b6be-757">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-757">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="3b6be-758">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-758">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="3b6be-759">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-759">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="3b6be-760">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3b6be-760">CookieRequestCultureProvider</span></span>

<span data-ttu-id="3b6be-761">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-761">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="3b6be-762">cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-762">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="3b6be-763">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-763">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="3b6be-764">既定の cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-764">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="3b6be-765">cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-765">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="3b6be-766">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-766">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="3b6be-767">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="3b6be-767">The Accept-Language HTTP header</span></span>

<span data-ttu-id="3b6be-768">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="3b6be-768">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="3b6be-769">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-769">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="3b6be-770">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-770">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="3b6be-771">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-771">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="3b6be-772">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="3b6be-772">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="3b6be-773">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-773">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="3b6be-774">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-774">Tap **Languages**.</span></span>

   ![インターネット オプション](localization/_static/lang.png)

1. <span data-ttu-id="3b6be-776">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-776">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="3b6be-777">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-777">Tap **Add a language**.</span></span>

1. <span data-ttu-id="3b6be-778">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-778">Add the language.</span></span>

1. <span data-ttu-id="3b6be-779">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-779">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="3b6be-780">Content-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="3b6be-780">The Content-Language HTTP header</span></span>

<span data-ttu-id="3b6be-781">[Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) エンティティ ヘッダーは、</span><span class="sxs-lookup"><span data-stu-id="3b6be-781">The [Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

* <span data-ttu-id="3b6be-782">対象ユーザー用に想定した言語を説明するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-782">Is used to describe the language(s) intended for the audience.</span></span>
* <span data-ttu-id="3b6be-783">ユーザーが、そのユーザー独自の優先言語に従って区別することを可能にします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-783">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="3b6be-784">エンティティ ヘッダーは、HTTP 要求と応答の両方で使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-784">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="3b6be-785">プロパティ `ApplyCurrentCultureToResponseHeaders` を設定することによって `Content-Language` ヘッダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-785">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="3b6be-786">`Content-Language` ヘッダーを追加すると、</span><span class="sxs-lookup"><span data-stu-id="3b6be-786">Adding the `Content-Language` header:</span></span>

* <span data-ttu-id="3b6be-787">RequestLocalizationMiddleware で `CurrentUICulture` を使って `Content-Language` を設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-787">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
* <span data-ttu-id="3b6be-788">応答ヘッダーの `Content-Language` を明示的に設定する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-788">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="3b6be-789">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="3b6be-789">Use a custom provider</span></span>

<span data-ttu-id="3b6be-790">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="3b6be-790">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="3b6be-791">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-791">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="3b6be-792">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-792">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="3b6be-793">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-793">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="3b6be-794">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="3b6be-794">Set the culture programmatically</span></span>

<span data-ttu-id="3b6be-795">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="3b6be-795">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="3b6be-796">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-796">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="3b6be-797">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-797">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="3b6be-798">`SetLanguage` メソッドはカルチャ cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-798">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="3b6be-799">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="3b6be-799">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="3b6be-800">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-800">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="3b6be-801">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="3b6be-801">Model binding route data and query strings</span></span>

<span data-ttu-id="3b6be-802">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3b6be-802">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="3b6be-803">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="3b6be-803">Globalization and localization terms</span></span>

<span data-ttu-id="3b6be-804">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-804">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="3b6be-805">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-805">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="3b6be-806">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-806">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="3b6be-807">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-807">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="3b6be-808">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-808">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="3b6be-809">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-809">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="3b6be-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="3b6be-811">以下を参照してください。<https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)></span><span class="sxs-lookup"><span data-stu-id="3b6be-811">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="3b6be-812">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="3b6be-812">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="3b6be-813">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-813">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="3b6be-814">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="3b6be-814">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="3b6be-815">用語:</span><span class="sxs-lookup"><span data-stu-id="3b6be-815">Terms:</span></span>

* <span data-ttu-id="3b6be-816">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="3b6be-816">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="3b6be-817">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="3b6be-817">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="3b6be-818">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="3b6be-818">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="3b6be-819">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="3b6be-819">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="3b6be-820">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-820">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="3b6be-821">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="3b6be-821">(for example "en", "es")</span></span>
* <span data-ttu-id="3b6be-822">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-822">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="3b6be-823">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="3b6be-823">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="3b6be-824">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="3b6be-824">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="3b6be-825">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-825">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="3b6be-826">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="3b6be-826">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="3b6be-827">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3b6be-827">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="3b6be-828">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="3b6be-828">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="3b6be-829">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="3b6be-829">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="3b6be-830">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="3b6be-830">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="3b6be-831">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="3b6be-831">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="3b6be-832">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="3b6be-832">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

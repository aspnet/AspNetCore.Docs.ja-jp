---
title: ASP.NET Core で Portable Object のローカライズを構成する
author: sebastienros
description: この記事では、Portable Object (PO) ファイルについて紹介します。また、Orchard Core フレームワークを使用する ASP.NET Core アプリケーションで PO ファイルを使用する手順について説明します。
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 175614c426c564ce91068e18035ce05311432698
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689241"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="fad3c-103">ASP.NET Core で Portable Object のローカライズを構成する</span><span class="sxs-lookup"><span data-stu-id="fad3c-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fad3c-104">作成者: [Sébastien Ros](https://github.com/sebastienros)、[Scott Addie](https://twitter.com/Scott_Addie)、[Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="fad3c-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="fad3c-105">この記事では、[Orchard Core](https://github.com/OrchardCMS/OrchardCore) フレームワークを使用した ASP.NET Core アプリケーションで Portable Object (PO) ファイルを使用する手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="fad3c-106">**注:** Orchard Core は Microsoft 製品ではありません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="fad3c-107">したがって、Microsoft はこの機能をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="fad3c-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fad3c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="fad3c-109">PO ファイルとは</span><span class="sxs-lookup"><span data-stu-id="fad3c-109">What is a PO file?</span></span>

<span data-ttu-id="fad3c-110">PO ファイルは、特定の言語の翻訳済み文字列を含むテキスト ファイルとして配布されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="fad3c-111">*.resx* ファイルの代わりに PO ファイルを使用すると、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-111">Some advantages of using PO files instead of *.resx* files include:</span></span>
- <span data-ttu-id="fad3c-112">PO ファイルは複数形化をサポートしています。 *.resx* ファイルは複数形化をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="fad3c-113">PO ファイルは *.resx* ファイルのようにコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="fad3c-114">そのため、特殊なツールやビルドの手順は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="fad3c-115">PO ファイルは、オンラインの共同編集ツールでの利用に適しています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="fad3c-116">例</span><span class="sxs-lookup"><span data-stu-id="fad3c-116">Example</span></span>

<span data-ttu-id="fad3c-117">フランス語の 2 つの文字列 (一方は複数形も含まれています) の翻訳が記載されたサンプル PO ファイルを次に示します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="fad3c-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="fad3c-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="fad3c-119">この例では、次の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="fad3c-120">`#:`:翻訳される文字列のコンテキストを示すコメント。</span><span class="sxs-lookup"><span data-stu-id="fad3c-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="fad3c-121">同じ文字列でも、使用される場所によって翻訳が変わることがあります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="fad3c-122">`msgid`:翻訳前の文字列。</span><span class="sxs-lookup"><span data-stu-id="fad3c-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="fad3c-123">`msgstr`:翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="fad3c-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="fad3c-124">複数形化をサポートする場合は、その他のエントリも定義できます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="fad3c-125">`msgid_plural`:翻訳前の文字列の複数形。</span><span class="sxs-lookup"><span data-stu-id="fad3c-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="fad3c-126">`msgstr[0]`:0 の場合の翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="fad3c-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="fad3c-127">`msgstr[N]`:N の場合の翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="fad3c-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="fad3c-128">PO ファイルの仕様については、[こちら](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fad3c-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="fad3c-129">ASP.NET Core で PO ファイルのサポートを構成する</span><span class="sxs-lookup"><span data-stu-id="fad3c-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="fad3c-130">この例は、Visual Studio 2017 プロジェクト テンプレートから生成された ASP.NET Core MVC アプリケーションに基づいています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="fad3c-131">パッケージの参照</span><span class="sxs-lookup"><span data-stu-id="fad3c-131">Referencing the package</span></span>

<span data-ttu-id="fad3c-132">`OrchardCore.Localization.Core` NuGet パッケージの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="fad3c-133">*.csproj* ファイルに次のような行が追加されました (バージョン番号は異なる可能性があります)。</span><span class="sxs-lookup"><span data-stu-id="fad3c-133">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="fad3c-134">サービスの登録</span><span class="sxs-lookup"><span data-stu-id="fad3c-134">Registering the service</span></span>

<span data-ttu-id="fad3c-135">*Startup.cs* の `ConfigureServices` メソッドに必要なサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-135">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="fad3c-136">*Startup.cs* の `Configure` メソッドに必要なミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-136">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="fad3c-137">選択した Razor ビューに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-137">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="fad3c-138">この例では *About.cshtml* を使用します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-138">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="fad3c-139">"Hello world!" というテキストを翻訳するために、`IViewLocalizer` インスタンスが挿入され、使用されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-139">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="fad3c-140">PO ファイルの作成</span><span class="sxs-lookup"><span data-stu-id="fad3c-140">Creating a PO file</span></span>

<span data-ttu-id="fad3c-141">アプリケーションのルート フォルダーに *\<culture code>.po* というファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-141">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="fad3c-142">この例では、フランス語が使用されているため、ファイル名は *fr.po* です。</span><span class="sxs-lookup"><span data-stu-id="fad3c-142">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="fad3c-143">このファイルには、翻訳する文字列とフランス語で翻訳された文字列の両方が格納されています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-143">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="fad3c-144">必要に応じて、翻訳は元の親カルチャに戻されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-144">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="fad3c-145">この例では、要求されるカルチャが `fr-FR` または `fr-CA` の場合、*fr.po* ファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-145">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="fad3c-146">アプリケーションのテスト</span><span class="sxs-lookup"><span data-stu-id="fad3c-146">Testing the application</span></span>

<span data-ttu-id="fad3c-147">アプリケーションを実行し、URL `/Home/About` に移動します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-147">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="fad3c-148">テキスト **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="fad3c-148">The text **Hello world!**</span></span> <span data-ttu-id="fad3c-149">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-149">is displayed.</span></span>

<span data-ttu-id="fad3c-150">URL `/Home/About?culture=fr-FR` に移動します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-150">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="fad3c-151">テキスト **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="fad3c-151">The text **Bonjour le monde!**</span></span> <span data-ttu-id="fad3c-152">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-152">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="fad3c-153">複数形化</span><span class="sxs-lookup"><span data-stu-id="fad3c-153">Pluralization</span></span>

<span data-ttu-id="fad3c-154">PO ファイルは複数形化フォームをサポートしています。これは、カーディナリティに基づいて同じ文字列の翻訳を変える必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="fad3c-154">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="fad3c-155">各言語で、カーディナリティに基づいて使用する文字列を選択するためのカスタム ルールが定義されているため、このタスクは複雑になります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-155">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="fad3c-156">Orchard Localization パッケージには、このような異なる複数形を自動的に呼び出す API が用意されています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-156">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="fad3c-157">複数形化 PO ファイルの作成</span><span class="sxs-lookup"><span data-stu-id="fad3c-157">Creating pluralization PO files</span></span>

<span data-ttu-id="fad3c-158">前述の *fr.po* ファイルに次の内容を追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-158">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="fad3c-159">この例の各エントリが表す内容の説明については、「[PO ファイルとは](#what-is-a-po-file)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fad3c-159">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="fad3c-160">異なる複数形化フォームを使用する言語の追加</span><span class="sxs-lookup"><span data-stu-id="fad3c-160">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="fad3c-161">前の例では、英語とフランス語の文字列が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="fad3c-161">English and French strings were used in the previous example.</span></span> <span data-ttu-id="fad3c-162">英語とフランス語の複数形化フォームは 2 つのみであり、同じフォーム ルールを共有しています。つまり、1 のカーディナリティは最初の複数形にマップされます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-162">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="fad3c-163">その他のカーディナリティは 2 番目の複数形にマップされます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-163">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="fad3c-164">すべての言語が同じルールを共有するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-164">Not all languages share the same rules.</span></span> <span data-ttu-id="fad3c-165">たとえば、チェコ語の場合は 3 つの複数形があります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-165">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="fad3c-166">`cs.po` ファイルを次のように作成し、複数形化に 3 つの異なる翻訳がどのように必要かを記述します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-166">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="fad3c-167">チェコ語のローカライズを引き受ける場合は、`ConfigureServices` メソッドでサポートされるカルチャのリストに `"cs"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-167">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="fad3c-168">複数の基数についてローカライズされた複数形の文字列をレンダリングするように、*Views/Home/About.cshtml* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-168">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="fad3c-169">**注:** 実際のシナリオでは、変数を使用してカウントを表します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-169">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="fad3c-170">ここでは、非常に特殊なケースを表すために、3 つの異なる値を使用して同じコードを繰り返しています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-170">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="fad3c-171">カルチャを切り替えると、次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-171">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="fad3c-172">`/Home/About`の場合:</span><span class="sxs-lookup"><span data-stu-id="fad3c-172">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="fad3c-173">`/Home/About?culture=fr`の場合:</span><span class="sxs-lookup"><span data-stu-id="fad3c-173">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="fad3c-174">`/Home/About?culture=cs`の場合:</span><span class="sxs-lookup"><span data-stu-id="fad3c-174">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="fad3c-175">チェコ語のカルチャの場合、3 つの翻訳が異なることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fad3c-175">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="fad3c-176">フランス語と英語のカルチャは、翻訳後の文字列のうち、後半 2 つは同じ構造を共有しています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-176">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="fad3c-177">高度なタスク</span><span class="sxs-lookup"><span data-stu-id="fad3c-177">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="fad3c-178">文字列のコンテキスト化</span><span class="sxs-lookup"><span data-stu-id="fad3c-178">Contextualizing strings</span></span>

<span data-ttu-id="fad3c-179">多くの場合、アプリケーションには複数の場所で翻訳される文字列が含まれています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-179">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="fad3c-180">同じ文字列でも、アプリ内の場所 (Razor ビューやクラス ファイル) によっては翻訳が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-180">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="fad3c-181">PO ファイルは、表現されている文字列の分類に使用できるファイル コンテキストの概念をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-181">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="fad3c-182">ファイル コンテキストを使用すると、ファイル コンテキスト (またはファイル コンテキストの欠如) に応じて文字列の翻訳を変えることができます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-182">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="fad3c-183">PO ローカライズ サービスは、文字列を翻訳するときに使用される完全クラスまたはビューの名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-183">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="fad3c-184">これは、`msgctxt` エントリに値を設定することによって行います。</span><span class="sxs-lookup"><span data-stu-id="fad3c-184">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="fad3c-185">前述の *fr.po* の例に少し追加してみましょう。</span><span class="sxs-lookup"><span data-stu-id="fad3c-185">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="fad3c-186">*Views/Home/About.cshtml* にある Razor ビューは、予約された `msgctxt` エントリの値を設定することでファイル コンテキストとして定義できます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-186">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="fad3c-187">`msgctxt` をこのように設定すると、`/Home/About?culture=fr-FR` に移動するときにテキストの翻訳が行われます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-187">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="fad3c-188">`/Home/Contact?culture=fr-FR` に移動するときに翻訳は行われません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-188">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="fad3c-189">特定のエントリが特定のファイル コンテキストと一致しない場合、Orchard Core のフォールバック メカニズムによって、コンテキストなしで適切な PO ファイルが検索されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-189">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="fad3c-190">*Views/Home/Contact.cshtml* に特定のファイル コンテキストが定義されていない場合、`/Home/Contact?culture=fr-FR` に移動すると、次のような PO ファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-190">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="fad3c-191">PO ファイルの場所の変更</span><span class="sxs-lookup"><span data-stu-id="fad3c-191">Changing the location of PO files</span></span>

<span data-ttu-id="fad3c-192">PO ファイルの既定の場所は、`ConfigureServices` で変更できます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-192">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="fad3c-193">この例では、PO ファイルは *Localization* フォルダーから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-193">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="fad3c-194">ローカライズ ファイルを検索するためのカスタム ロジックの実装</span><span class="sxs-lookup"><span data-stu-id="fad3c-194">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="fad3c-195">PO ファイルを特定するためにより複雑なロジックが必要な場合は、`OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` インターフェイスを実装してサービスとして登録することができます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-195">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="fad3c-196">これは、PO ファイルをさまざまな場所に格納できる場合や、複数のフォルダーがある 1 階層内でファイルを検出する必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="fad3c-196">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="fad3c-197">異なる既定の複数形化された言語の使用</span><span class="sxs-lookup"><span data-stu-id="fad3c-197">Using a different default pluralized language</span></span>

<span data-ttu-id="fad3c-198">このパッケージには、2 つの複数形に固有の `Plural` 拡張メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-198">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="fad3c-199">より多くの複数形が必要な言語の場合は、拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-199">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="fad3c-200">拡張メソッドを使用すると、既定の言語用にローカライズ ファイルを用意する必要はありません &mdash; 元の文字列はコード内でそのまま使用できます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-200">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="fad3c-201">翻訳の文字列配列を受け付ける、より汎用的な `Plural(int count, string[] pluralForms, params object[] arguments)` オーバーロードを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-201">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fad3c-202">作成者: [Sébastien Ros](https://github.com/sebastienros)、[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="fad3c-202">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="fad3c-203">この記事では、[Orchard Core](https://github.com/OrchardCMS/OrchardCore) フレームワークを使用した ASP.NET Core アプリケーションで Portable Object (PO) ファイルを使用する手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-203">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="fad3c-204">**注:** Orchard Core は Microsoft 製品ではありません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-204">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="fad3c-205">したがって、Microsoft はこの機能をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-205">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="fad3c-206">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fad3c-206">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="fad3c-207">PO ファイルとは</span><span class="sxs-lookup"><span data-stu-id="fad3c-207">What is a PO file?</span></span>

<span data-ttu-id="fad3c-208">PO ファイルは、特定の言語の翻訳済み文字列を含むテキスト ファイルとして配布されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-208">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="fad3c-209">*.resx* ファイルの代わりに PO ファイルを使用すると、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-209">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="fad3c-210">PO ファイルは複数形化をサポートしています。 *.resx* ファイルは複数形化をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-210">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="fad3c-211">PO ファイルは *.resx* ファイルのようにコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-211">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="fad3c-212">そのため、特殊なツールやビルドの手順は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-212">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="fad3c-213">PO ファイルは、オンラインの共同編集ツールでの利用に適しています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-213">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="fad3c-214">例</span><span class="sxs-lookup"><span data-stu-id="fad3c-214">Example</span></span>

<span data-ttu-id="fad3c-215">フランス語の 2 つの文字列 (一方は複数形も含まれています) の翻訳が記載されたサンプル PO ファイルを次に示します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-215">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="fad3c-216">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="fad3c-216">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="fad3c-217">この例では、次の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-217">This example uses the following syntax:</span></span>

- <span data-ttu-id="fad3c-218">`#:`:翻訳される文字列のコンテキストを示すコメント。</span><span class="sxs-lookup"><span data-stu-id="fad3c-218">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="fad3c-219">同じ文字列でも、使用される場所によって翻訳が変わることがあります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-219">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="fad3c-220">`msgid`:翻訳前の文字列。</span><span class="sxs-lookup"><span data-stu-id="fad3c-220">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="fad3c-221">`msgstr`:翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="fad3c-221">`msgstr`: The translated string.</span></span>

<span data-ttu-id="fad3c-222">複数形化をサポートする場合は、その他のエントリも定義できます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-222">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="fad3c-223">`msgid_plural`:翻訳前の文字列の複数形。</span><span class="sxs-lookup"><span data-stu-id="fad3c-223">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="fad3c-224">`msgstr[0]`:0 の場合の翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="fad3c-224">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="fad3c-225">`msgstr[N]`:N の場合の翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="fad3c-225">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="fad3c-226">PO ファイルの仕様については、[こちら](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fad3c-226">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="fad3c-227">ASP.NET Core で PO ファイルのサポートを構成する</span><span class="sxs-lookup"><span data-stu-id="fad3c-227">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="fad3c-228">この例は、Visual Studio 2017 プロジェクト テンプレートから生成された ASP.NET Core MVC アプリケーションに基づいています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-228">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="fad3c-229">パッケージの参照</span><span class="sxs-lookup"><span data-stu-id="fad3c-229">Referencing the package</span></span>

<span data-ttu-id="fad3c-230">`OrchardCore.Localization.Core` NuGet パッケージの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-230">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="fad3c-231">*.csproj* ファイルに次のような行が追加されました (バージョン番号は異なる可能性があります)。</span><span class="sxs-lookup"><span data-stu-id="fad3c-231">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="fad3c-232">サービスの登録</span><span class="sxs-lookup"><span data-stu-id="fad3c-232">Registering the service</span></span>

<span data-ttu-id="fad3c-233">*Startup.cs* の `ConfigureServices` メソッドに必要なサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-233">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="fad3c-234">*Startup.cs* の `Configure` メソッドに必要なミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-234">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="fad3c-235">選択した Razor ビューに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-235">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="fad3c-236">この例では *About.cshtml* を使用します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-236">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="fad3c-237">"Hello world!" というテキストを翻訳するために、`IViewLocalizer` インスタンスが挿入され、使用されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-237">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="fad3c-238">PO ファイルの作成</span><span class="sxs-lookup"><span data-stu-id="fad3c-238">Creating a PO file</span></span>

<span data-ttu-id="fad3c-239">アプリケーションのルート フォルダーに *\<culture code>.po* というファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-239">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="fad3c-240">この例では、フランス語が使用されているため、ファイル名は *fr.po* です。</span><span class="sxs-lookup"><span data-stu-id="fad3c-240">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="fad3c-241">このファイルには、翻訳する文字列とフランス語で翻訳された文字列の両方が格納されています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-241">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="fad3c-242">必要に応じて、翻訳は元の親カルチャに戻されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-242">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="fad3c-243">この例では、要求されるカルチャが `fr-FR` または `fr-CA` の場合、*fr.po* ファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-243">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="fad3c-244">アプリケーションのテスト</span><span class="sxs-lookup"><span data-stu-id="fad3c-244">Testing the application</span></span>

<span data-ttu-id="fad3c-245">アプリケーションを実行し、URL `/Home/About` に移動します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-245">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="fad3c-246">テキスト **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="fad3c-246">The text **Hello world!**</span></span> <span data-ttu-id="fad3c-247">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-247">is displayed.</span></span>

<span data-ttu-id="fad3c-248">URL `/Home/About?culture=fr-FR` に移動します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-248">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="fad3c-249">テキスト **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="fad3c-249">The text **Bonjour le monde!**</span></span> <span data-ttu-id="fad3c-250">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-250">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="fad3c-251">複数形化</span><span class="sxs-lookup"><span data-stu-id="fad3c-251">Pluralization</span></span>

<span data-ttu-id="fad3c-252">PO ファイルは複数形化フォームをサポートしています。これは、カーディナリティに基づいて同じ文字列の翻訳を変える必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="fad3c-252">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="fad3c-253">各言語で、カーディナリティに基づいて使用する文字列を選択するためのカスタム ルールが定義されているため、このタスクは複雑になります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-253">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="fad3c-254">Orchard Localization パッケージには、このような異なる複数形を自動的に呼び出す API が用意されています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-254">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="fad3c-255">複数形化 PO ファイルの作成</span><span class="sxs-lookup"><span data-stu-id="fad3c-255">Creating pluralization PO files</span></span>

<span data-ttu-id="fad3c-256">前述の *fr.po* ファイルに次の内容を追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-256">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="fad3c-257">この例の各エントリが表す内容の説明については、「[PO ファイルとは](#what-is-a-po-file)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fad3c-257">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="fad3c-258">異なる複数形化フォームを使用する言語の追加</span><span class="sxs-lookup"><span data-stu-id="fad3c-258">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="fad3c-259">前の例では、英語とフランス語の文字列が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="fad3c-259">English and French strings were used in the previous example.</span></span> <span data-ttu-id="fad3c-260">英語とフランス語の複数形化フォームは 2 つのみであり、同じフォーム ルールを共有しています。つまり、1 のカーディナリティは最初の複数形にマップされます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-260">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="fad3c-261">その他のカーディナリティは 2 番目の複数形にマップされます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-261">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="fad3c-262">すべての言語が同じルールを共有するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-262">Not all languages share the same rules.</span></span> <span data-ttu-id="fad3c-263">たとえば、チェコ語の場合は 3 つの複数形があります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-263">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="fad3c-264">`cs.po` ファイルを次のように作成し、複数形化に 3 つの異なる翻訳がどのように必要かを記述します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-264">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="fad3c-265">チェコ語のローカライズを引き受ける場合は、`ConfigureServices` メソッドでサポートされるカルチャのリストに `"cs"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-265">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="fad3c-266">複数の基数についてローカライズされた複数形の文字列をレンダリングするように、*Views/Home/About.cshtml* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-266">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="fad3c-267">**注:** 実際のシナリオでは、変数を使用してカウントを表します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-267">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="fad3c-268">ここでは、非常に特殊なケースを表すために、3 つの異なる値を使用して同じコードを繰り返しています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-268">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="fad3c-269">カルチャを切り替えると、次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-269">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="fad3c-270">`/Home/About`の場合:</span><span class="sxs-lookup"><span data-stu-id="fad3c-270">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="fad3c-271">`/Home/About?culture=fr`の場合:</span><span class="sxs-lookup"><span data-stu-id="fad3c-271">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="fad3c-272">`/Home/About?culture=cs`の場合:</span><span class="sxs-lookup"><span data-stu-id="fad3c-272">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="fad3c-273">チェコ語のカルチャの場合、3 つの翻訳が異なることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fad3c-273">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="fad3c-274">フランス語と英語のカルチャは、翻訳後の文字列のうち、後半 2 つは同じ構造を共有しています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-274">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="fad3c-275">高度なタスク</span><span class="sxs-lookup"><span data-stu-id="fad3c-275">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="fad3c-276">文字列のコンテキスト化</span><span class="sxs-lookup"><span data-stu-id="fad3c-276">Contextualizing strings</span></span>

<span data-ttu-id="fad3c-277">多くの場合、アプリケーションには複数の場所で翻訳される文字列が含まれています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-277">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="fad3c-278">同じ文字列でも、アプリ内の場所 (Razor ビューやクラス ファイル) によっては翻訳が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="fad3c-278">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="fad3c-279">PO ファイルは、表現されている文字列の分類に使用できるファイル コンテキストの概念をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-279">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="fad3c-280">ファイル コンテキストを使用すると、ファイル コンテキスト (またはファイル コンテキストの欠如) に応じて文字列の翻訳を変えることができます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-280">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="fad3c-281">PO ローカライズ サービスは、文字列を翻訳するときに使用される完全クラスまたはビューの名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-281">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="fad3c-282">これは、`msgctxt` エントリに値を設定することによって行います。</span><span class="sxs-lookup"><span data-stu-id="fad3c-282">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="fad3c-283">前述の *fr.po* の例に少し追加してみましょう。</span><span class="sxs-lookup"><span data-stu-id="fad3c-283">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="fad3c-284">*Views/Home/About.cshtml* にある Razor ビューは、予約された `msgctxt` エントリの値を設定することでファイル コンテキストとして定義できます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-284">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="fad3c-285">`msgctxt` をこのように設定すると、`/Home/About?culture=fr-FR` に移動するときにテキストの翻訳が行われます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-285">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="fad3c-286">`/Home/Contact?culture=fr-FR` に移動するときに翻訳は行われません。</span><span class="sxs-lookup"><span data-stu-id="fad3c-286">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="fad3c-287">特定のエントリが特定のファイル コンテキストと一致しない場合、Orchard Core のフォールバック メカニズムによって、コンテキストなしで適切な PO ファイルが検索されます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-287">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="fad3c-288">*Views/Home/Contact.cshtml* に特定のファイル コンテキストが定義されていない場合、`/Home/Contact?culture=fr-FR` に移動すると、次のような PO ファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-288">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="fad3c-289">PO ファイルの場所の変更</span><span class="sxs-lookup"><span data-stu-id="fad3c-289">Changing the location of PO files</span></span>

<span data-ttu-id="fad3c-290">PO ファイルの既定の場所は、`ConfigureServices` で変更できます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-290">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="fad3c-291">この例では、PO ファイルは *Localization* フォルダーから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-291">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="fad3c-292">ローカライズ ファイルを検索するためのカスタム ロジックの実装</span><span class="sxs-lookup"><span data-stu-id="fad3c-292">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="fad3c-293">PO ファイルを特定するためにより複雑なロジックが必要な場合は、`OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` インターフェイスを実装してサービスとして登録することができます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-293">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="fad3c-294">これは、PO ファイルをさまざまな場所に格納できる場合や、複数のフォルダーがある 1 階層内でファイルを検出する必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="fad3c-294">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="fad3c-295">異なる既定の複数形化された言語の使用</span><span class="sxs-lookup"><span data-stu-id="fad3c-295">Using a different default pluralized language</span></span>

<span data-ttu-id="fad3c-296">このパッケージには、2 つの複数形に固有の `Plural` 拡張メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="fad3c-296">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="fad3c-297">より多くの複数形が必要な言語の場合は、拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="fad3c-297">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="fad3c-298">拡張メソッドを使用すると、既定の言語用にローカライズ ファイルを用意する必要はありません &mdash; 元の文字列はコード内でそのまま使用できます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-298">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="fad3c-299">翻訳の文字列配列を受け付ける、より汎用的な `Plural(int count, string[] pluralForms, params object[] arguments)` オーバーロードを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="fad3c-299">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

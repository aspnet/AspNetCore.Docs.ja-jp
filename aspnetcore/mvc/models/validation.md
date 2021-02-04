---
title: ASP.NET Core MVC でのモデルの検証
author: rick-anderson
description: ASP.NET Core MVC とページでのモデルの検証について説明し Razor ます。
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: d6fa7e4524a8afdc23d4ad46354d9d8b395656a3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530191"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="73a0d-103">MVC とページ ASP.NET Core でのモデルの検証 Razor</span><span class="sxs-lookup"><span data-stu-id="73a0d-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73a0d-104">作成者: [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="73a0d-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="73a0d-105">この記事では、ASP.NET Core MVC またはページアプリでユーザー入力を検証する方法について説明し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="73a0d-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="73a0d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="73a0d-107">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="73a0d-107">Model state</span></span>

<span data-ttu-id="73a0d-108">モデルの状態では、モデル バインドとモデル検証の 2 つのサブシステムで発生したエラーが表されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="73a0d-109">[モデルバインド](model-binding.md)から発生するエラーは、通常、データ変換エラーです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="73a0d-110">たとえば、"x" は整数フィールドに入力されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="73a0d-111">モデルの検証は、モデル バインド後に行われ、データがビジネス ルールに準拠していないエラーを報告します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="73a0d-112">たとえば、1 から 5 の評価を想定したフィールドに 0 が入力されたとします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="73a0d-113">モデルバインドとモデル検証は、どちらもコントローラーアクションまたはページハンドラーメソッドの実行前に行わ Razor れます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="73a0d-114">Web アプリでは、`ModelState.IsValid` を調べて適切に対処するのはアプリの責任です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="73a0d-115">通常、Web アプリではエラー メッセージを含むページを再表示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="73a0d-116">Web API コントローラーでは、`[ApiController]` 属性が設定されている場合は、`ModelState.IsValid` を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="73a0d-117">その場合、モデルが無効な状態のときは、エラーの詳細を含む HTTP 400 応答が自動的に返されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="73a0d-118">詳細については、「[自動的な HTTP 400 応答](xref:web-api/index#automatic-http-400-responses)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="73a0d-119">検証を再実行する</span><span class="sxs-lookup"><span data-stu-id="73a0d-119">Rerun validation</span></span>

<span data-ttu-id="73a0d-120">検証は自動的に行われますが、手動で繰り返したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="73a0d-121">たとえば、プロパティの値を計算し、プロパティを計算値に設定した後で検証を再実行するような場合です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="73a0d-122">検証を再実行するには、次に示すように `TryValidateModel` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="73a0d-123">検証属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-123">Validation attributes</span></span>

<span data-ttu-id="73a0d-124">検証属性を使用して、モデルのプロパティの検証規則を指定できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="73a0d-125">サンプル アプリの次の例では、検証属性で注釈されたモデル クラスが示されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="73a0d-126">`[ClassicMovie]` 属性はカスタム検証属性であり、その他は組み込み属性です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="73a0d-127">`[ClassicMovieWithClientValidator]` は表示されません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="73a0d-128">`[ClassicMovieWithClientValidator]` は、カスタム属性を実装するもう 1 つの方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="73a0d-129">組み込みの属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-129">Built-in attributes</span></span>

<span data-ttu-id="73a0d-130">組み込み検証属性の一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="73a0d-131">`[CreditCard]`: プロパティにクレジットカード形式があることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="73a0d-132">[JQuery 検証の追加のメソッド](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js)が必要です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="73a0d-133">`[Compare]`: モデル内の2つのプロパティが一致することを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="73a0d-134">`[EmailAddress]`: プロパティが電子メール形式であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="73a0d-135">`[Phone]`: プロパティに電話番号の書式が設定されていることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="73a0d-136">`[Range]`: プロパティ値が指定した範囲内にあることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="73a0d-137">`[RegularExpression]`: プロパティ値が指定した正規表現に一致することを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="73a0d-138">`[Required]`: フィールドが null でないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="73a0d-139">この属性の動作の詳細については、「 [ `[Required]` 属性](#required-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="73a0d-140">`[StringLength]`: 文字列プロパティの値が、指定された長さの制限を超えていないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="73a0d-141">`[Url]`: プロパティに URL 形式があることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="73a0d-142">`[Remote]`: サーバーでアクションメソッドを呼び出すことによって、クライアントの入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="73a0d-143">この属性の動作の詳細については、「 [ `[Remote]` 属性](#remote-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="73a0d-144">検証属性の完全な一覧については、[System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 名前空間で確認できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="73a0d-145">エラー メッセージ</span><span class="sxs-lookup"><span data-stu-id="73a0d-145">Error messages</span></span>

<span data-ttu-id="73a0d-146">検証属性では、無効な入力に対して表示されるエラー メッセージを指定できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="73a0d-147">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="73a0d-148">内部的には、属性ではフィールド名のプレースホルダーを指定して `String.Format` が呼び出され、場合によっては追加のプレースホルダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="73a0d-149">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="73a0d-150">`Name` プロパティに適用すると、上記のコードによって作成されるエラー メッセージは "Name length must be between 6 and 8" になります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="73a0d-151">特定の属性のエラー メッセージに対して `String.Format` に渡されるパラメーターを確認するには、[DataAnnotations のソース コード](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="non-nullable-reference-types-and-required-attribute"></a><span data-ttu-id="73a0d-152">Null 非許容の参照型と [Required] 属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-152">Non-nullable reference types and [Required] attribute</span></span>

<span data-ttu-id="73a0d-153">検証システムは、属性を持っているかのように、null 非許容パラメーターまたはバインドされたプロパティを扱い `[Required]` ます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-153">The validation system treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="73a0d-154">[ `Nullable` コンテキストを有効にする](/dotnet/csharp/nullable-references#nullable-contexts)ことで、MVC は属性で属性が付けられているかのように、null 非許容のプロパティまたはパラメーターの検証を暗黙的に開始し `[Required]` ます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-154">By [enabling `Nullable` contexts](/dotnet/csharp/nullable-references#nullable-contexts), MVC implicitly starts validating non-nullable properties or parameters as if they had been attributed with the `[Required]` attribute.</span></span> <span data-ttu-id="73a0d-155">次のコードについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-155">Consider the following code:</span></span>

```csharp
public class Person
{
    public string Name { get; set; }
}
```

<span data-ttu-id="73a0d-156">アプリがでビルドされた場合 `<Nullable>enable</Nullable>` 、 `Name` JSON またはフォームの post での欠損値が検証エラーになります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-156">If the app was built with `<Nullable>enable</Nullable>`, a missing value for `Name` in a JSON or form post results in a validation error.</span></span> <span data-ttu-id="73a0d-157">Null 値を許容する参照型を使用して、プロパティに null 値または欠損値を指定できるようにし `Name` ます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-157">Use a nullable reference type to allow null or missing values to be specified for the `Name` property:</span></span>

```csharp
public class Person
{
    public string? Name { get; set; }
}
```

<span data-ttu-id="73a0d-158">.</span><span class="sxs-lookup"><span data-stu-id="73a0d-158">.</span></span> <span data-ttu-id="73a0d-159">`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> を次のように構成することで、この動作を無効にできます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-159">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="73a0d-160">サーバーでの [Required] の検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-160">[Required] validation on the server</span></span>

<span data-ttu-id="73a0d-161">サーバーでは、プロパティが null の場合、必須の値が不足しているものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-161">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="73a0d-162">null 非許容型フィールドは常に有効であり、`[Required]` 属性のエラー メッセージが表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-162">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="73a0d-163">ただし、null 非許容型プロパティに対するモデル バインドは失敗する場合があり、`The value '' is invalid` などのエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-163">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="73a0d-164">null 非許容型のサーバー側検証に対してカスタム エラー メッセージを指定するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-164">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="73a0d-165">フィールドを null 許容型にします (たとえば、`decimal` の代わりに `decimal?` を使用)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-165">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="73a0d-166">[Null \<T> ](/dotnet/csharp/programming-guide/nullable-types/)値を許容値型は、標準の null 許容型と同様に扱われます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-166">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="73a0d-167">次の例に示すように、モデル バインドで使用される既定のエラー メッセージを指定します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-167">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="73a0d-168">既定のメッセージを設定できるモデル バインド エラーに関して詳しくは、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-168">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="73a0d-169">クライアントでの [Required] の検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-169">[Required] validation on the client</span></span>

<span data-ttu-id="73a0d-170">クライアントでの null 非許容型と文字列の処理は、サーバーとは異なります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-170">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="73a0d-171">クライアント側 :</span><span class="sxs-lookup"><span data-stu-id="73a0d-171">On the client:</span></span>

* <span data-ttu-id="73a0d-172">値は、入力が行われた場合にのみ存在するものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-172">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="73a0d-173">そのため、クライアント側の検証では、null 非許容型は null 許容型と同じように処理されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-173">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="73a0d-174">文字列フィールド内の空白文字は、jQuery Validation の [required](https://jqueryvalidation.org/required-method/) メソッドでは有効な入力と見なされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-174">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="73a0d-175">サーバー側の検証では、空白文字のみが入力された場合は、必須文字列フィールドが無効であるものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-175">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="73a0d-176">前述のように、null 非許容型は `[Required]` 属性を持つものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-176">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="73a0d-177">つまり、`[Required]` 属性を適用していない場合でも、クライアント側の検証が行われます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-177">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="73a0d-178">ただし、属性を使用しない場合は、既定のエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-178">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="73a0d-179">カスタム エラー メッセージを指定するには、属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-179">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="73a0d-180">[Remote] 属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-180">[Remote] attribute</span></span>

<span data-ttu-id="73a0d-181">`[Remote]` 属性では、フィールド入力が有効かどうかを判断するためにサーバーでメソッドを呼び出す必要があるクライアント側検証が実装されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-181">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="73a0d-182">たとえば、アプリでは、ユーザー名が既に使用されているかどうかを確認することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-182">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="73a0d-183">リモート検証を実装するには:</span><span class="sxs-lookup"><span data-stu-id="73a0d-183">To implement remote validation:</span></span>

1. <span data-ttu-id="73a0d-184">JavaScript で呼び出すアクション メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-184">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="73a0d-185">JQuery Validation [リモート](https://jqueryvalidation.org/remote-method/) メソッドは JSON 応答を想定しています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-185">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="73a0d-186">`true` は、入力データが有効であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-186">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="73a0d-187">`false`、`undefined`、または `null` は、入力が有効ではないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-187">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="73a0d-188">既定のエラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-188">Display the default error message.</span></span>
   * <span data-ttu-id="73a0d-189">その他の文字列は、入力が無効であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-189">Any other string means the input is invalid.</span></span> <span data-ttu-id="73a0d-190">カスタム エラー メッセージとして文字列を表示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-190">Display the string as a custom error message.</span></span>

   <span data-ttu-id="73a0d-191">カスタム エラー メッセージを返すアクション メソッドの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-191">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="73a0d-192">次の例に示すように、モデル クラスで、検証アクション メソッドを指し示す `[Remote]` 属性を使用してプロパティに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-192">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="73a0d-193">`[Remote]` 属性は `Microsoft.AspNetCore.Mvc` 名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-193">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="73a0d-194">追加フィールド</span><span class="sxs-lookup"><span data-stu-id="73a0d-194">Additional fields</span></span>

<span data-ttu-id="73a0d-195">`[Remote]` 属性の `AdditionalFields` プロパティでは、サーバー上のデータに対してフィールドの組み合わせを検証できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-195">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="73a0d-196">たとえば、`User` モデルに `FirstName` プロパティと `LastName` プロパティがある場合、その名前のペアを使用する既存ユーザーがいないことを確認したいことがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-196">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="73a0d-197">`AdditionalFields` を使用する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-197">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="73a0d-198">`AdditionalFields` を文字列 FirstName および LastName に明示的に設定することもできますが、[nameof](/dotnet/csharp/language-reference/keywords/nameof) 演算子を使用すると、後のリファクタリングが容易になります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-198">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="73a0d-199">この検証のアクション メソッドは、`firstName` と `lastName` の両方の引数を受け入れる必要があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-199">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="73a0d-200">ユーザーが名または姓を入力すると、JavaScript はリモート呼び出しを行って、その名前のペアが取得されているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-200">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="73a0d-201">複数の追加フィールドを検証するには、それらをコンマ区切りのリストとして提供します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-201">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="73a0d-202">たとえば、`MiddleName` プロパティをモデルに追加するには、`[Remote]` 属性を次の例のように設定します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-202">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="73a0d-203">他の属性引数と同じように、`AdditionalFields` も定数式である必要があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-203">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="73a0d-204">したがって、[補間文字列](/dotnet/csharp/language-reference/keywords/interpolated-strings)を使用したり、<xref:System.String.Join*> を呼び出して `AdditionalFields` を初期化したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-204">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="73a0d-205">組み込み属性に代わる方法</span><span class="sxs-lookup"><span data-stu-id="73a0d-205">Alternatives to built-in attributes</span></span>

<span data-ttu-id="73a0d-206">組み込み属性によって提供されない検証が必要な場合は、次のようにできます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-206">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="73a0d-207">[カスタム属性を作成する](#custom-attributes)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-207">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="73a0d-208">[IValidatableObject を実装する](#ivalidatableobject)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-208">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="73a0d-209">カスタム属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-209">Custom attributes</span></span>

<span data-ttu-id="73a0d-210">組み込みの検証属性で処理されないシナリオの場合は、カスタム検証属性を作成できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-210">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="73a0d-211"><xref:System.ComponentModel.DataAnnotations.ValidationAttribute> を継承するクラスを作成し、<xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-211">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="73a0d-212">`IsValid` メソッドは、*value* という名前のオブジェクトを受け取ります。これは、検証対象の入力です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-212">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="73a0d-213">オーバーロードは `ValidationContext` オブジェクトも受け取ります。これは、モデル バインドによって作成されたモデル インスタンスなどの追加情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-213">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="73a0d-214">次の例では、*Classic* ジャンルの映画の公開日が指定した年より後ではないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-214">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="73a0d-215">`[ClassicMovie]` 属性:</span><span class="sxs-lookup"><span data-stu-id="73a0d-215">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="73a0d-216">サーバー上でのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-216">Is only run on the server.</span></span>
* <span data-ttu-id="73a0d-217">クラシック映画の場合は、公開日が検証されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-217">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="73a0d-218">前の例の `movie` 変数は、フォーム送信からのデータを格納している `Movie` オブジェクトを表します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-218">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="73a0d-219">検証に失敗すると、エラー メッセージを含む `ValidationResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-219">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="73a0d-220">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="73a0d-220">IValidatableObject</span></span>

<span data-ttu-id="73a0d-221">前の例は、`Movie` 型でのみ動作します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-221">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="73a0d-222">クラス レベルの検証に対するもう 1 つのオプションは、次の例に示すように、`IValidatableObject` をモデル クラスに実装することです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-222">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="73a0d-223">最上位ノードの検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-223">Top-level node validation</span></span>

<span data-ttu-id="73a0d-224">最上位ノードには次が含まれています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-224">Top-level nodes include:</span></span>

* <span data-ttu-id="73a0d-225">アクションのパラメーター</span><span class="sxs-lookup"><span data-stu-id="73a0d-225">Action parameters</span></span>
* <span data-ttu-id="73a0d-226">コントローラーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="73a0d-226">Controller properties</span></span>
* <span data-ttu-id="73a0d-227">ページ ハンドラーのパラメーター</span><span class="sxs-lookup"><span data-stu-id="73a0d-227">Page handler parameters</span></span>
* <span data-ttu-id="73a0d-228">ページ モデルのプロパティ</span><span class="sxs-lookup"><span data-stu-id="73a0d-228">Page model properties</span></span>

<span data-ttu-id="73a0d-229">モデルのパラメーター検証に加え、モデルが関連付けられた最上位ノードが検証されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-229">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="73a0d-230">サンプル アプリからの次の例の `VerifyPhone` メソッドでは、<xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> を使用して `phone` アクションパラメーターが検証されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-230">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="73a0d-231">最上位ノードでは、検証属性と共に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> を使用できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-231">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="73a0d-232">サンプル アプリからの次の例では、`CheckAge` メソッドによって、フォームの送信時、クエリ文字列から `age` パラメーターを関連付ける必要があることが指定されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-232">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="73a0d-233">[年齢確認] ページ (*CheckAge.cshtml*) には 2 つのフォームがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-233">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="73a0d-234">最初のフォームでは、`Age` の値 `99` がクエリ文字列パラメーター `https://localhost:5001/Users/CheckAge?Age=99` として送信されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-234">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="73a0d-235">クエリ文字列の正しく書式設定された `age` パラメーターが送信されると、フォームの有効性が確認されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-235">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="73a0d-236">[年齢確認] ページの 2 番目のフォームでは、要求本文で `Age` 値が送信され、検証は不合格となります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-236">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="73a0d-237">`age` パラメーターはクエリ文字列で渡される必要があるため、バインドが失敗します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-237">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="73a0d-238">最大エラー数</span><span class="sxs-lookup"><span data-stu-id="73a0d-238">Maximum errors</span></span>

<span data-ttu-id="73a0d-239">エラーの最大数 (既定では 200) に達すると、検証は停止します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-239">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="73a0d-240">この数は、`Startup.ConfigureServices` の次のコードを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-240">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="73a0d-241">最大再帰</span><span class="sxs-lookup"><span data-stu-id="73a0d-241">Maximum recursion</span></span>

<span data-ttu-id="73a0d-242"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> では、検証対象のモデルのオブジェクト グラフが走査されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-242"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="73a0d-243">深いモデルまたは無限に再帰するモデルでは、検証でスタック オーバーフローが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-243">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="73a0d-244">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) では、ビジターの再帰が構成されている深さを超えた場合、早い段階で検証を停止する方法が提供されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-244">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="73a0d-245">`MvcOptions.MaxValidationDepth` の既定値は 32 です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-245">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="73a0d-246">自動省略</span><span class="sxs-lookup"><span data-stu-id="73a0d-246">Automatic short-circuit</span></span>

<span data-ttu-id="73a0d-247">モデル グラフの検証が必要ない場合、検証は自動的に省略 (スキップ) されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-247">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="73a0d-248">ランタイムで検証がスキップされるオブジェクトとしては、プリミティブのコレクション (`byte[]`、`string[]`、`Dictionary<string, string>` など) や、検証コントロールを何も持たない複雑なオブジェクト グラフなどがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-248">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="73a0d-249">検証を無効にする</span><span class="sxs-lookup"><span data-stu-id="73a0d-249">Disable validation</span></span>

<span data-ttu-id="73a0d-250">検証を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="73a0d-250">To disable validation:</span></span>

1. <span data-ttu-id="73a0d-251">どのフィールドも無効としてマークしない `IObjectModelValidator` の実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-251">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="73a0d-252">依存関係挿入コンテナーで、次のコードを `Startup.ConfigureServices` に追加し、既定の `IObjectModelValidator` の実装を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-252">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="73a0d-253">その場合でも、モデル バインドから発生するモデル状態エラーが表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-253">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="73a0d-254">クライアント側の検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-254">Client-side validation</span></span>

<span data-ttu-id="73a0d-255">クライアント側検証は、フォームが有効になるまで送信を許可しません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-255">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="73a0d-256">送信ボタンをクリックすると、フォームの送信またはエラー メッセージの表示を行う JavaScript が実行されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-256">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="73a0d-257">クライアント側の検証を使用すると、フォームに入力エラーがある場合に、サーバーへの不要なラウンドトリップを回避できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-257">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="73a0d-258">*_Layout.cshtml* および *_ValidationScriptsPartial.cshtml* の次のスクリプト参照では、クライアント側の検証がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-258">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="73a0d-259">[Jquery の控えめな検証](https://github.com/aspnet/jquery-validation-unobtrusive)スクリプトは、広く使われている[jquery validation](https://jqueryvalidation.org/)プラグインを基盤とするカスタム Microsoft フロントエンドライブラリです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-259">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="73a0d-260">jQuery Unobtrusive Validation を使用しないと、同じ検証ロジックを 2 か所でコーディングする必要があります。1 つはモデル プロパティでのサーバー側検証属性で、もう 1 つはクライアント側スクリプトです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-260">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="73a0d-261">代わりに、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)および [HTML ヘルパー](xref:mvc/views/overview)では、モデル プロパティの検証属性と型メタデータを使用して、検証の必要なフォーム要素に対する HTML 5 の `data-` 属性がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-261">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="73a0d-262">jQuery の控えめな検証では、属性を解析し、その `data-` ロジックを jQuery の検証に渡します。これにより、サーバー側の検証ロジックがクライアントに効果的に "コピー" されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-262">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="73a0d-263">次に示すように、タグ ヘルパーを使用して、クライアントで検証エラーを表示できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-263">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="73a0d-264">上記のタグ ヘルパーでは、次の HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-264">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="73a0d-265">HTML 出力の `data-` 属性が、`Movie.ReleaseDate` プロパティの検証属性に対応していることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-265">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="73a0d-266">`data-val-required` 属性には、ユーザーが公開日フィールドを入力していない場合に表示されるエラー メッセージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-266">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="73a0d-267">jQuery の控えめな検証では、この値を jQuery Validation [required ()](https://jqueryvalidation.org/required-method/) メソッドに渡します。これにより、そのメッセージが付随する要素に表示され **\<span>** ます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-267">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="73a0d-268">`[DataType]` 属性によってオーバーライドされていない限り、データ型の検証はプロパティの .NET 型に基づいて行われます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-268">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="73a0d-269">ブラウザーには独自の既定のエラー メッセージがありますが、jQuery Validation Unobtrusive Validation パッケージでそれらのメッセージをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-269">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="73a0d-270">`[DataType]` 属性と `[EmailAddress]` などのサブクラスを使用して、エラー メッセージを指定できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-270">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="73a0d-271">控えめな検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-271">Unobtrusive validation</span></span>

<span data-ttu-id="73a0d-272">控えめな検証については、[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore.Docs/issues/1111)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-272">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="73a0d-273">動的なフォームに検証を追加する</span><span class="sxs-lookup"><span data-stu-id="73a0d-273">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="73a0d-274">jQuery の控えめな検証は、ページが最初に読み込まれるときに検証ロジックとパラメーターを jQuery 検証に渡します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-274">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="73a0d-275">したがって、動的に生成されるフォームでは、検証は自動的には機能しません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-275">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="73a0d-276">検証を有効にするには、作成直後に動的フォームを解析するよう、jQuery Unobtrusive Validation に指示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-276">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="73a0d-277">たとえば、次のコードでは、AJAX によって追加されるフォームでクライアント側検証が設定されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-277">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="73a0d-278">`$.validator.unobtrusive.parse()` メソッドには、その引数の 1 つで jQuery セレクターを指定します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-278">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="73a0d-279">このメソッドは、そのセレクター内のフォームの `data-` 属性を解析するよう jQuery Unobtrusive Validation に指示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-279">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="73a0d-280">これらの属性の値は、jQuery Validation プラグインに渡されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-280">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="73a0d-281">動的なコントロールに検証を追加する</span><span class="sxs-lookup"><span data-stu-id="73a0d-281">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="73a0d-282">`$.validator.unobtrusive.parse()` メソッドは、`<input>` や `<select/>` などの動的に生成される個々のコントロールではなく、フォーム全体に対して動作します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-282">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="73a0d-283">フォームを再解析するには、次の例に示すように、フォームが前に解析されたときに追加された検証データを削除します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-283">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="73a0d-284">カスタム クライアント側検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-284">Custom client-side validation</span></span>

<span data-ttu-id="73a0d-285">カスタムのクライアント側検証を行うには `data-` 、カスタム JQuery 検証アダプターを使用する HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-285">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="73a0d-286">次のサンプルのアダプター コードは、この記事で前に導入した `[ClassicMovie]` および `[ClassicMovieWithClientValidator]` 属性用に記述されたものです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-286">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="73a0d-287">アダプターの作成方法の詳細については、 [jQuery の検証](https://jqueryvalidation.org/documentation/)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-287">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="73a0d-288">特定のフィールドに対するアダプターの使用は、次のような `data-` 属性によってトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-288">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="73a0d-289">検証対象としてフィールドにフラグを設定します (`data-val="true"`)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-289">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="73a0d-290">検証規則名とエラー メッセージ テキストを示します (例: `data-val-rulename="Error message."`)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-290">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="73a0d-291">検証コントロールで必要なその他のパラメーターを提供します (例: `data-val-rulename-param1="value"`)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-291">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="73a0d-292">次の例では、サンプル アプリの `ClassicMovie` 属性に対する `data-` 属性を示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-292">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="73a0d-293">前に説明したように、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)と [HTML ヘルパー](xref:mvc/views/overview)では、検証属性からの情報を使用して `data-` 属性がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-293">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="73a0d-294">カスタム `data-` HTML 属性が作成されるようになるコードを記述するには、2 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-294">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="73a0d-295">`AttributeAdapterBase<TAttribute>` から派生するクラスと `IValidationAttributeAdapterProvider` を実装するクラスを作成し、属性とそのアダプターを DI に登録します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-295">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="73a0d-296">この方法では[単一責任の原則](https://wikipedia.org/wiki/Single_responsibility_principle)に従って、サーバー関連の検証コードとクライアント関連の検証コードは別のクラスになります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-296">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="73a0d-297">アダプターには、DI に登録されるため、必要な場合に DI 内の他のサービスがそれを使用できるという利点もあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-297">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="73a0d-298">`ValidationAttribute` クラスで `IClientModelValidator` を実装します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-298">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="73a0d-299">この方法は、属性でサーバー側の検証が何も行われず、DI からのサービスが必要ない場合に、適している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-299">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="73a0d-300">クライアント側検証用の AttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="73a0d-300">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="73a0d-301">HTML に `data-` 属性をレンダリングするこの方法は、サンプル アプリの `ClassicMovie` 属性で使用されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-301">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="73a0d-302">この方法を使用してクライアント検証を追加するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-302">To add client validation by using this method:</span></span>

1. <span data-ttu-id="73a0d-303">カスタム検証属性の属性アダプター クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-303">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="73a0d-304"><xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>からクラスを派生させます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-304">Derive the class from <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>.</span></span> <span data-ttu-id="73a0d-305">次の例で示すように、レンダリングされた出力に `data-` 属性を追加する `AddValidation` メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-305">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="73a0d-306"><xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> を実装するアダプター プロバイダー クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-306">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="73a0d-307">次の例に示すように、`GetAttributeAdapter` メソッドで、カスタム属性をアダプターのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-307">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="73a0d-308">`Startup.ConfigureServices` でアダプター プロバイダーを DI に登録します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-308">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="73a0d-309">クライアント側検証用の IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="73a0d-309">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="73a0d-310">HTML に `data-` 属性をレンダリングするこの方法は、サンプル アプリの `ClassicMovieWithClientValidator` 属性で使用されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-310">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="73a0d-311">この方法を使用してクライアント検証を追加するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-311">To add client validation by using this method:</span></span>

* <span data-ttu-id="73a0d-312">カスタム検証属性で、`IClientModelValidator` インターフェイスを実装し、`AddValidation` メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-312">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="73a0d-313">次の例で示すように、`AddValidation` メソッドで、検証用の `data-` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-313">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="73a0d-314">クライアント側検証を無効にする</span><span class="sxs-lookup"><span data-stu-id="73a0d-314">Disable client-side validation</span></span>

<span data-ttu-id="73a0d-315">次のコードは、ページ内のクライアント検証を無効にし Razor ます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-315">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="73a0d-316">クライアント側検証を無効にするその他のオプション:</span><span class="sxs-lookup"><span data-stu-id="73a0d-316">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="73a0d-317">すべての *.cshtml* ファイル内の `_ValidationScriptsPartial` への参照をコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-317">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="73a0d-318">*Pages\Shared\_ValidationScriptsPartial.cshtml* ファイルの内容を削除します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-318">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="73a0d-319">上記の方法では、クライアント側でクラスライブラリを検証することはできません ASP.NET Core Identity Razor 。</span><span class="sxs-lookup"><span data-stu-id="73a0d-319">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="73a0d-320">詳細については、「<xref:security/authentication/scaffold-identity>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-320">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73a0d-321">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="73a0d-321">Additional resources</span></span>

* [<span data-ttu-id="73a0d-322">System.ComponentModel.DataAnnotations 名前空間</span><span class="sxs-lookup"><span data-stu-id="73a0d-322">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="73a0d-323">モデルバインド</span><span class="sxs-lookup"><span data-stu-id="73a0d-323">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73a0d-324">この記事では、ASP.NET Core MVC またはページアプリでユーザー入力を検証する方法について説明し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-324">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="73a0d-325">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="73a0d-325">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="73a0d-326">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="73a0d-326">Model state</span></span>

<span data-ttu-id="73a0d-327">モデルの状態では、モデル バインドとモデル検証の 2 つのサブシステムで発生したエラーが表されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-327">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="73a0d-328">[モデル バインド](model-binding.md)で発生するエラーは、一般に、データ変換エラーです (たとえば、整数が必要なフィールドに "x" が入力された場合)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-328">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="73a0d-329">モデル検証は、モデル バインドの後で行われて、データがビジネス ルールに従っていないエラーが報告されます (たとえば、1 から 5 までのレーティングが必要なフィールドに 0 が入力された場合)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-329">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="73a0d-330">モデルバインドと検証の両方が、コントローラーアクションまたはページハンドラーメソッドの実行前に行わ Razor れます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-330">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="73a0d-331">Web アプリでは、`ModelState.IsValid` を調べて適切に対処するのはアプリの責任です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-331">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="73a0d-332">通常、Web アプリではエラー メッセージを含むページを再表示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-332">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="73a0d-333">Web API コントローラーでは、`[ApiController]` 属性が設定されている場合は、`ModelState.IsValid` を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-333">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="73a0d-334">その場合、モデルが無効な状態のときは、エラーの詳細を含む HTTP 400 応答が自動的に返されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-334">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="73a0d-335">詳細については、「[自動的な HTTP 400 応答](xref:web-api/index#automatic-http-400-responses)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-335">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="73a0d-336">検証を再実行する</span><span class="sxs-lookup"><span data-stu-id="73a0d-336">Rerun validation</span></span>

<span data-ttu-id="73a0d-337">検証は自動的に行われますが、手動で繰り返したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-337">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="73a0d-338">たとえば、プロパティの値を計算し、プロパティを計算値に設定した後で検証を再実行するような場合です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-338">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="73a0d-339">検証を再実行するには、次に示すように `TryValidateModel` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-339">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="73a0d-340">検証属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-340">Validation attributes</span></span>

<span data-ttu-id="73a0d-341">検証属性を使用して、モデルのプロパティの検証規則を指定できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-341">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="73a0d-342">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)の次の例では、検証属性で注釈されたモデル クラスが示されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-342">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="73a0d-343">`[ClassicMovie]` 属性はカスタム検証属性であり、その他は組み込み属性です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-343">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="73a0d-344">`[ClassicMovie2]` は示されていませんが、カスタム属性を実装するもう 1 つの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-344">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="73a0d-345">組み込みの属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-345">Built-in attributes</span></span>

<span data-ttu-id="73a0d-346">組み込みの検証属性には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-346">Built-in validation attributes include:</span></span>

* <span data-ttu-id="73a0d-347">`[CreditCard]`: プロパティにクレジットカード形式があることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-347">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="73a0d-348">`[Compare]`: モデル内の2つのプロパティが一致することを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-348">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="73a0d-349">たとえば、*Register.cshtml.cs* ファイルは `[Compare]` を使用して、入力された 2 つのパスワードが一致していることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-349">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="73a0d-350">[スキャフォールディング Identity ](xref:security/authentication/scaffold-identity)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-350">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="73a0d-351">`[EmailAddress]`: プロパティが電子メール形式であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-351">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="73a0d-352">`[Phone]`: プロパティに電話番号の書式が設定されていることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-352">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="73a0d-353">`[Range]`: プロパティ値が指定した範囲内にあることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-353">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="73a0d-354">`[RegularExpression]`: プロパティ値が指定した正規表現に一致することを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-354">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="73a0d-355">`[Required]`: フィールドが null でないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-355">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="73a0d-356">この属性の動作の詳細については、「 [ `[Required]` 属性](#required-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-356">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="73a0d-357">`[StringLength]`: 文字列プロパティの値が、指定された長さの制限を超えていないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-357">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="73a0d-358">`[Url]`: プロパティに URL 形式があることを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-358">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="73a0d-359">`[Remote]`: サーバーでアクションメソッドを呼び出すことによって、クライアントの入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-359">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="73a0d-360">この属性の動作の詳細については、「 [ `[Remote]` 属性](#remote-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-360">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="73a0d-361">クライアント側の検証で `[RegularExpression]` 属性を使用する場合、regex はクライアントの JavaScript で実行されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-361">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="73a0d-362">これは、[ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) 一致の動作が使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-362">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="73a0d-363">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/corefx/issues/42487)します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-363">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="73a0d-364">検証属性の完全な一覧については、[System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 名前空間で確認できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-364">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="73a0d-365">エラー メッセージ</span><span class="sxs-lookup"><span data-stu-id="73a0d-365">Error messages</span></span>

<span data-ttu-id="73a0d-366">検証属性では、無効な入力に対して表示されるエラー メッセージを指定できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-366">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="73a0d-367">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-367">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="73a0d-368">内部的には、属性ではフィールド名のプレースホルダーを指定して `String.Format` が呼び出され、場合によっては追加のプレースホルダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-368">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="73a0d-369">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-369">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="73a0d-370">`Name` プロパティに適用すると、上記のコードによって作成されるエラー メッセージは "Name length must be between 6 and 8" になります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-370">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="73a0d-371">特定の属性のエラー メッセージに対して `String.Format` に渡されるパラメーターを確認するには、[DataAnnotations のソース コード](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-371">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="73a0d-372">[Required] 属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-372">[Required] attribute</span></span>

<span data-ttu-id="73a0d-373">既定では、検証システムでは null 非許容型のパラメーターまたはプロパティは `[Required]` 属性を持つものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-373">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="73a0d-374">`decimal` や `int` などの[値の型](/dotnet/csharp/language-reference/keywords/value-types)は null 非許容型です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-374">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="73a0d-375">サーバーでの [Required] の検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-375">[Required] validation on the server</span></span>

<span data-ttu-id="73a0d-376">サーバーでは、プロパティが null の場合、必須の値が不足しているものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-376">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="73a0d-377">null 非許容型フィールドは常に有効であり、[Required] 属性のエラー メッセージが表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-377">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="73a0d-378">ただし、null 非許容型プロパティに対するモデル バインドは失敗する場合があり、`The value '' is invalid` などのエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-378">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="73a0d-379">null 非許容型のサーバー側検証に対してカスタム エラー メッセージを指定するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-379">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="73a0d-380">フィールドを null 許容型にします (たとえば、`decimal` の代わりに `decimal?` を使用)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-380">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="73a0d-381">[Null \<T> ](/dotnet/csharp/programming-guide/nullable-types/)値を許容値型は、標準の null 許容型と同様に扱われます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-381">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="73a0d-382">次の例に示すように、モデル バインドで使用される既定のエラー メッセージを指定します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-382">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="73a0d-383">既定のメッセージを設定できるモデル バインド エラーに関して詳しくは、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-383">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="73a0d-384">クライアントでの [Required] の検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-384">[Required] validation on the client</span></span>

<span data-ttu-id="73a0d-385">クライアントでの null 非許容型と文字列の処理は、サーバーとは異なります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-385">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="73a0d-386">クライアント側 :</span><span class="sxs-lookup"><span data-stu-id="73a0d-386">On the client:</span></span>

* <span data-ttu-id="73a0d-387">値は、入力が行われた場合にのみ存在するものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-387">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="73a0d-388">そのため、クライアント側の検証では、null 非許容型は null 許容型と同じように処理されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-388">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="73a0d-389">文字列フィールド内の空白文字は、jQuery Validation の [required](https://jqueryvalidation.org/required-method/) メソッドでは有効な入力と見なされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-389">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="73a0d-390">サーバー側の検証では、空白文字のみが入力された場合は、必須文字列フィールドが無効であるものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-390">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="73a0d-391">前述のように、null 非許容型は `[Required]` 属性を持つものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-391">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="73a0d-392">つまり、`[Required]` 属性を適用していない場合でも、クライアント側の検証が行われます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-392">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="73a0d-393">ただし、属性を使用しない場合は、既定のエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-393">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="73a0d-394">カスタム エラー メッセージを指定するには、属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-394">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="73a0d-395">[Remote] 属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-395">[Remote] attribute</span></span>

<span data-ttu-id="73a0d-396">`[Remote]` 属性では、フィールド入力が有効かどうかを判断するためにサーバーでメソッドを呼び出す必要があるクライアント側検証が実装されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-396">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="73a0d-397">たとえば、アプリでは、ユーザー名が既に使用されているかどうかを確認することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-397">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="73a0d-398">リモート検証を実装するには:</span><span class="sxs-lookup"><span data-stu-id="73a0d-398">To implement remote validation:</span></span>

1. <span data-ttu-id="73a0d-399">JavaScript で呼び出すアクション メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-399">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="73a0d-400">JQuery Validate の [remote](https://jqueryvalidation.org/remote-method/) メソッドでは、JSON の応答が必要です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-400">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="73a0d-401">`"true"` は、入力データが有効であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-401">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="73a0d-402">`"false"`、`undefined`、または `null` は、入力が有効ではないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-402">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="73a0d-403">既定のエラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-403">Display the default error message.</span></span>
   * <span data-ttu-id="73a0d-404">その他の文字列は、入力が無効であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-404">Any other string means the input is invalid.</span></span> <span data-ttu-id="73a0d-405">カスタム エラー メッセージとして文字列を表示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-405">Display the string as a custom error message.</span></span>

   <span data-ttu-id="73a0d-406">カスタム エラー メッセージを返すアクション メソッドの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-406">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="73a0d-407">次の例に示すように、モデル クラスで、検証アクション メソッドを指し示す `[Remote]` 属性を使用してプロパティに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-407">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="73a0d-408">`[Remote]` 属性は `Microsoft.AspNetCore.Mvc` 名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-408">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="73a0d-409">`Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` メタパッケージを使用していない場合は、[Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-409">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="73a0d-410">追加フィールド</span><span class="sxs-lookup"><span data-stu-id="73a0d-410">Additional fields</span></span>

<span data-ttu-id="73a0d-411">`[Remote]` 属性の `AdditionalFields` プロパティでは、サーバー上のデータに対してフィールドの組み合わせを検証できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-411">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="73a0d-412">たとえば、`User` モデルに `FirstName` プロパティと `LastName` プロパティがある場合、その名前のペアを使用する既存ユーザーがいないことを確認したいことがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-412">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="73a0d-413">`AdditionalFields` を使用する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-413">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="73a0d-414">`AdditionalFields` を文字列 `"FirstName"` および `"LastName"` に明示的に設定することもできますが、[nameof](/dotnet/csharp/language-reference/keywords/nameof) 演算子を使用すると、後のリファクタリングが容易になります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-414">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="73a0d-415">この検証のアクション メソッドは、名と姓の両方を引数として受け取る必要があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-415">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="73a0d-416">ユーザーが名または姓を入力すると、JavaScript はリモート呼び出しを行って、その名前のペアが取得されているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-416">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="73a0d-417">複数の追加フィールドを検証するには、それらをコンマ区切りのリストとして提供します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-417">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="73a0d-418">たとえば、`MiddleName` プロパティをモデルに追加するには、`[Remote]` 属性を次の例のように設定します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-418">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="73a0d-419">他の属性引数と同じように、`AdditionalFields` も定数式である必要があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-419">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="73a0d-420">したがって、[補間文字列](/dotnet/csharp/language-reference/keywords/interpolated-strings)を使用したり、<xref:System.String.Join*> を呼び出して `AdditionalFields` を初期化したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-420">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="73a0d-421">組み込み属性に代わる方法</span><span class="sxs-lookup"><span data-stu-id="73a0d-421">Alternatives to built-in attributes</span></span>

<span data-ttu-id="73a0d-422">組み込み属性によって提供されない検証が必要な場合は、次のようにできます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-422">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="73a0d-423">[カスタム属性を作成する](#custom-attributes)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-423">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="73a0d-424">[IValidatableObject を実装する](#ivalidatableobject)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-424">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="73a0d-425">カスタム属性</span><span class="sxs-lookup"><span data-stu-id="73a0d-425">Custom attributes</span></span>

<span data-ttu-id="73a0d-426">組み込みの検証属性で処理されないシナリオの場合は、カスタム検証属性を作成できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-426">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="73a0d-427"><xref:System.ComponentModel.DataAnnotations.ValidationAttribute> を継承するクラスを作成し、<xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-427">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="73a0d-428">`IsValid` メソッドは、*value* という名前のオブジェクトを受け取ります。これは、検証対象の入力です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-428">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="73a0d-429">オーバーロードは `ValidationContext` オブジェクトも受け取ります。これは、モデル バインドによって作成されたモデル インスタンスなどの追加情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-429">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="73a0d-430">次の例では、*Classic* ジャンルの映画の公開日が指定した年より後ではないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-430">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="73a0d-431">`[ClassicMovie2]` 属性では最初にジャンルがチェックされ、*Classic* である場合にのみ続行されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-431">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="73a0d-432">クラシックとして識別された映画については、公開日が属性のコンストラクターに渡された制限より後ではないことがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-432">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="73a0d-433">前の例の `movie` 変数は、フォーム送信からのデータを格納している `Movie` オブジェクトを表します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-433">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="73a0d-434">`IsValid` メソッドでは、日付とジャンルがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-434">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="73a0d-435">検証に成功すると、`IsValid` によって `ValidationResult.Success` コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-435">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="73a0d-436">検証に失敗すると、エラー メッセージを含む `ValidationResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-436">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="73a0d-437">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="73a0d-437">IValidatableObject</span></span>

<span data-ttu-id="73a0d-438">前の例は、`Movie` 型でのみ動作します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-438">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="73a0d-439">クラス レベルの検証に対するもう 1 つのオプションは、次の例に示すように、`IValidatableObject` をモデル クラスに実装することです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-439">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="73a0d-440">最上位ノードの検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-440">Top-level node validation</span></span>

<span data-ttu-id="73a0d-441">最上位ノードには次が含まれています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-441">Top-level nodes include:</span></span>

* <span data-ttu-id="73a0d-442">アクションのパラメーター</span><span class="sxs-lookup"><span data-stu-id="73a0d-442">Action parameters</span></span>
* <span data-ttu-id="73a0d-443">コントローラーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="73a0d-443">Controller properties</span></span>
* <span data-ttu-id="73a0d-444">ページ ハンドラーのパラメーター</span><span class="sxs-lookup"><span data-stu-id="73a0d-444">Page handler parameters</span></span>
* <span data-ttu-id="73a0d-445">ページ モデルのプロパティ</span><span class="sxs-lookup"><span data-stu-id="73a0d-445">Page model properties</span></span>

<span data-ttu-id="73a0d-446">モデルのパラメーター検証に加え、モデルが関連付けられた最上位ノードが検証されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-446">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="73a0d-447">サンプル アプリからの次の例の `VerifyPhone` メソッドでは、<xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> を使用して `phone` アクションパラメーターが検証されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-447">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="73a0d-448">最上位ノードでは、検証属性と共に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> を使用できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-448">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="73a0d-449">サンプル アプリからの次の例では、`CheckAge` メソッドによって、フォームの送信時、クエリ文字列から `age` パラメーターを関連付ける必要があることが指定されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-449">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="73a0d-450">[年齢確認] ページ (*CheckAge.cshtml*) には 2 つのフォームがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-450">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="73a0d-451">最初のフォームでは、`Age` の値 `99` がクエリ文字列 `https://localhost:5001/Users/CheckAge?Age=99` として送信されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-451">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="73a0d-452">クエリ文字列の正しく書式設定された `age` パラメーターが送信されると、フォームの有効性が確認されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-452">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="73a0d-453">[年齢確認] ページの 2 番目のフォームでは、要求本文で `Age` 値が送信され、検証は不合格となります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-453">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="73a0d-454">`age` パラメーターはクエリ文字列で渡される必要があるため、バインドが失敗します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-454">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="73a0d-455">`CompatibilityVersion.Version_2_1` 以降で実行すると、最上位ノードの検証が既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-455">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="73a0d-456">それ以外の場合、最上位ノードの検証は無効です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-456">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="73a0d-457">次に示すように、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> プロパティを設定することにより、既定のオプションをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-457">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="73a0d-458">最大エラー数</span><span class="sxs-lookup"><span data-stu-id="73a0d-458">Maximum errors</span></span>

<span data-ttu-id="73a0d-459">エラーの最大数 (既定では 200) に達すると、検証は停止します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-459">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="73a0d-460">この数は、`Startup.ConfigureServices` の次のコードを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-460">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="73a0d-461">最大再帰</span><span class="sxs-lookup"><span data-stu-id="73a0d-461">Maximum recursion</span></span>

<span data-ttu-id="73a0d-462"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> では、検証対象のモデルのオブジェクト グラフが走査されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-462"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="73a0d-463">非常に深いモデルまたは無限に再帰するモデルでは、検証でスタック オーバーフローが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-463">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="73a0d-464">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) では、ビジターの再帰が構成されている深さを超えた場合、早い段階で検証を停止する方法が提供されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-464">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="73a0d-465">`CompatibilityVersion.Version_2_2` 以降で実行したときの `MvcOptions.MaxValidationDepth` の既定値は 32 です。</span><span class="sxs-lookup"><span data-stu-id="73a0d-465">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="73a0d-466">それより前のバージョンでは、値は null であり、深さの制約がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-466">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="73a0d-467">自動省略</span><span class="sxs-lookup"><span data-stu-id="73a0d-467">Automatic short-circuit</span></span>

<span data-ttu-id="73a0d-468">モデル グラフの検証が必要ない場合、検証は自動的に省略 (スキップ) されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-468">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="73a0d-469">ランタイムで検証がスキップされるオブジェクトとしては、プリミティブのコレクション (`byte[]`、`string[]`、`Dictionary<string, string>` など) や、検証コントロールを何も持たない複雑なオブジェクト グラフなどがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-469">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="73a0d-470">検証を無効にする</span><span class="sxs-lookup"><span data-stu-id="73a0d-470">Disable validation</span></span>

<span data-ttu-id="73a0d-471">検証を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="73a0d-471">To disable validation:</span></span>

1. <span data-ttu-id="73a0d-472">どのフィールドも無効としてマークしない `IObjectModelValidator` の実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-472">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="73a0d-473">依存関係挿入コンテナーで、次のコードを `Startup.ConfigureServices` に追加し、既定の `IObjectModelValidator` の実装を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-473">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="73a0d-474">その場合でも、モデル バインドから発生するモデル状態エラーが表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-474">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="73a0d-475">クライアント側の検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-475">Client-side validation</span></span>

<span data-ttu-id="73a0d-476">クライアント側検証は、フォームが有効になるまで送信を許可しません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-476">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="73a0d-477">送信ボタンをクリックすると、フォームの送信またはエラー メッセージの表示を行う JavaScript が実行されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-477">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="73a0d-478">クライアント側の検証を使用すると、フォームに入力エラーがある場合に、サーバーへの不要なラウンドトリップを回避できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-478">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="73a0d-479">*_Layout.cshtml* および *_ValidationScriptsPartial.cshtml* の次のスクリプト参照では、クライアント側の検証がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-479">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="73a0d-480">[jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) スクリプトは、人気のある [jQuery Validate](https://jqueryvalidation.org/) プラグインを基に作成された Microsoft のカスタム フロントエンド ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-480">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="73a0d-481">jQuery Unobtrusive Validation を使用しないと、同じ検証ロジックを 2 か所でコーディングする必要があります。1 つはモデル プロパティでのサーバー側検証属性で、もう 1 つはクライアント側スクリプトです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-481">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="73a0d-482">代わりに、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)および [HTML ヘルパー](xref:mvc/views/overview)では、モデル プロパティの検証属性と型メタデータを使用して、検証の必要なフォーム要素に対する HTML 5 の `data-` 属性がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-482">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="73a0d-483">jQuery Unobtrusive Validation では、`data-` 属性が解析され、ロジックが jQuery Validate に渡されて、サーバー側検証ロジックがクライアントに実質的に "コピー" されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-483">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="73a0d-484">次に示すように、タグ ヘルパーを使用して、クライアントで検証エラーを表示できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-484">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="73a0d-485">上記のタグ ヘルパーでは、次の HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-485">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="73a0d-486">HTML 出力の `data-` 属性が、`ReleaseDate` プロパティの検証属性に対応していることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-486">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="73a0d-487">`data-val-required` 属性には、ユーザーが公開日フィールドを入力していない場合に表示されるエラー メッセージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-487">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="73a0d-488">jQuery の控えめな検証は、この値を jQuery Validate [required ()](https://jqueryvalidation.org/required-method/) メソッドに渡します。このメソッドは、付随する要素にそのメッセージを表示し **\<span>** ます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-488">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="73a0d-489">`[DataType]` 属性によってオーバーライドされていない限り、データ型の検証はプロパティの .NET 型に基づいて行われます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-489">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="73a0d-490">ブラウザーには独自の既定のエラー メッセージがありますが、jQuery Validation Unobtrusive Validation パッケージでそれらのメッセージをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-490">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="73a0d-491">`[DataType]` 属性と `[EmailAddress]` などのサブクラスを使用して、エラー メッセージを指定できます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-491">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="73a0d-492">動的なフォームに検証を追加する</span><span class="sxs-lookup"><span data-stu-id="73a0d-492">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="73a0d-493">ページが初めて読み込まれるときに、jQuery Unobtrusive Validation によって検証ロジックとパラメーターが jQuery Validate に渡されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-493">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="73a0d-494">したがって、動的に生成されるフォームでは、検証は自動的には機能しません。</span><span class="sxs-lookup"><span data-stu-id="73a0d-494">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="73a0d-495">検証を有効にするには、作成直後に動的フォームを解析するよう、jQuery Unobtrusive Validation に指示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-495">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="73a0d-496">たとえば、次のコードでは、AJAX によって追加されるフォームでクライアント側検証が設定されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-496">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="73a0d-497">`$.validator.unobtrusive.parse()` メソッドには、その引数の 1 つで jQuery セレクターを指定します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-497">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="73a0d-498">このメソッドは、そのセレクター内のフォームの `data-` 属性を解析するよう jQuery Unobtrusive Validation に指示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-498">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="73a0d-499">その後、これらの属性の値は、jQuery Validate プラグインに渡されます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-499">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="73a0d-500">動的なコントロールに検証を追加する</span><span class="sxs-lookup"><span data-stu-id="73a0d-500">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="73a0d-501">`$.validator.unobtrusive.parse()` メソッドは、`<input>` や `<select/>` などの動的に生成される個々のコントロールではなく、フォーム全体に対して動作します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-501">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="73a0d-502">フォームを再解析するには、次の例に示すように、フォームが前に解析されたときに追加された検証データを削除します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-502">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="73a0d-503">カスタム クライアント側検証</span><span class="sxs-lookup"><span data-stu-id="73a0d-503">Custom client-side validation</span></span>

<span data-ttu-id="73a0d-504">カスタム クライアント側検証は、カスタム jQuery Validate アダプターで動作する `data-` HTML 属性を生成することによって行われます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-504">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="73a0d-505">次のサンプルのアダプター コードは、この記事で前に導入した `ClassicMovie` および `ClassicMovie2` 属性用に記述されたものです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-505">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="73a0d-506">アダプターの作成方法については、[jQuery Validate のドキュメント](https://jqueryvalidation.org/documentation/)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73a0d-506">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="73a0d-507">特定のフィールドに対するアダプターの使用は、次のような `data-` 属性によってトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-507">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="73a0d-508">検証対象としてフィールドにフラグを設定します (`data-val="true"`)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-508">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="73a0d-509">検証規則名とエラー メッセージ テキストを示します (例: `data-val-rulename="Error message."`)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-509">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="73a0d-510">検証コントロールで必要なその他のパラメーターを提供します (例: `data-val-rulename-parm1="value"`)。</span><span class="sxs-lookup"><span data-stu-id="73a0d-510">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="73a0d-511">次の例では、サンプル アプリの `ClassicMovie` 属性に対する `data-` 属性を示します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-511">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="73a0d-512">前に説明したように、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)と [HTML ヘルパー](xref:mvc/views/overview)では、検証属性からの情報を使用して `data-` 属性がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-512">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="73a0d-513">カスタム `data-` HTML 属性が作成されるようになるコードを記述するには、2 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-513">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="73a0d-514">`AttributeAdapterBase<TAttribute>` から派生するクラスと `IValidationAttributeAdapterProvider` を実装するクラスを作成し、属性とそのアダプターを DI に登録します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-514">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="73a0d-515">この方法では[単一責任の原則](https://wikipedia.org/wiki/Single_responsibility_principle)に従って、サーバー関連の検証コードとクライアント関連の検証コードは別のクラスになります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-515">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="73a0d-516">アダプターには、DI に登録されるため、必要な場合に DI 内の他のサービスがそれを使用できるという利点もあります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-516">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="73a0d-517">`ValidationAttribute` クラスで `IClientModelValidator` を実装します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-517">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="73a0d-518">この方法は、属性でサーバー側の検証が何も行われず、DI からのサービスが必要ない場合に、適している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-518">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="73a0d-519">クライアント側検証用の AttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="73a0d-519">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="73a0d-520">HTML に `data-` 属性をレンダリングするこの方法は、サンプル アプリの `ClassicMovie` 属性で使用されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-520">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="73a0d-521">この方法を使用してクライアント検証を追加するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-521">To add client validation by using this method:</span></span>

1. <span data-ttu-id="73a0d-522">カスタム検証属性の属性アダプター クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-522">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="73a0d-523"><xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>からクラスを派生させます。</span><span class="sxs-lookup"><span data-stu-id="73a0d-523">Derive the class from <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>.</span></span> <span data-ttu-id="73a0d-524">次の例で示すように、レンダリングされた出力に `data-` 属性を追加する `AddValidation` メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-524">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="73a0d-525"><xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> を実装するアダプター プロバイダー クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-525">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="73a0d-526">次の例に示すように、`GetAttributeAdapter` メソッドで、カスタム属性をアダプターのコンストラクターに渡します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-526">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="73a0d-527">`Startup.ConfigureServices` でアダプター プロバイダーを DI に登録します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-527">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="73a0d-528">クライアント側検証用の IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="73a0d-528">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="73a0d-529">HTML に `data-` 属性をレンダリングするこの方法は、サンプル アプリの `ClassicMovie2` 属性で使用されています。</span><span class="sxs-lookup"><span data-stu-id="73a0d-529">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="73a0d-530">この方法を使用してクライアント検証を追加するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="73a0d-530">To add client validation by using this method:</span></span>

* <span data-ttu-id="73a0d-531">カスタム検証属性で、`IClientModelValidator` インターフェイスを実装し、`AddValidation` メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-531">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="73a0d-532">次の例で示すように、`AddValidation` メソッドで、検証用の `data-` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="73a0d-532">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="73a0d-533">クライアント側検証を無効にする</span><span class="sxs-lookup"><span data-stu-id="73a0d-533">Disable client-side validation</span></span>

<span data-ttu-id="73a0d-534">次のコードでは、MVC ビューのクライアント検証が無効になります。</span><span class="sxs-lookup"><span data-stu-id="73a0d-534">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="73a0d-535">ページ内 Razor :</span><span class="sxs-lookup"><span data-stu-id="73a0d-535">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="73a0d-536">クライアント検証を無効にするもう 1 つのオプションは、*.cshtml* ファイルで `_ValidationScriptsPartial` への参照をコメントにすることです。</span><span class="sxs-lookup"><span data-stu-id="73a0d-536">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73a0d-537">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="73a0d-537">Additional resources</span></span>

* [<span data-ttu-id="73a0d-538">System.ComponentModel.DataAnnotations 名前空間</span><span class="sxs-lookup"><span data-stu-id="73a0d-538">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="73a0d-539">モデルバインド</span><span class="sxs-lookup"><span data-stu-id="73a0d-539">Model Binding</span></span>](model-binding.md)

::: moniker-end

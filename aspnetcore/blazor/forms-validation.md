---
title: ASP.NET Core Blazor のフォームと検証
author: guardrex
description: Blazor でフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 012c8794b3d239ce93ac942000c7ec4f71d06cbf
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279998"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="ec251-103">ASP.NET Core Blazor のフォームと検証</span><span class="sxs-lookup"><span data-stu-id="ec251-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="ec251-104">フォームと検証は、Blazor で[データ注釈](xref:mvc/models/validation)を使用してサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ec251-104">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="ec251-105">次の `ExampleModel` 型は、データ注釈を使用して検証ロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="ec251-105">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="ec251-106">フォームは、<xref:Microsoft.AspNetCore.Components.Forms.EditForm> コンポーネントを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-106">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="ec251-107">次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。</span><span class="sxs-lookup"><span data-stu-id="ec251-107">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="ec251-108">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="ec251-108">In the preceding example:</span></span>

* <span data-ttu-id="ec251-109">フォームは、`ExampleModel` 型で定義されている検証を使用して、`name` フィールドのユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="ec251-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="ec251-110">モデルはコンポーネントの `@code` ブロック内に作成され、プライベート フィールド (`exampleModel`) に保持されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="ec251-111">フィールドは、`<EditForm>` 要素の `Model` 属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="ec251-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="ec251-112"><xref:Microsoft.AspNetCore.Components.Forms.InputText> コンポーネントの `@bind-Value` は次のようにバインドします。</span><span class="sxs-lookup"><span data-stu-id="ec251-112">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="ec251-113">モデル プロパティ (`exampleModel.Name`) を <xref:Microsoft.AspNetCore.Components.Forms.InputText> コンポーネントの `Value` プロパティへ。</span><span class="sxs-lookup"><span data-stu-id="ec251-113">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="ec251-114">プロパティのバインドの詳細については、<xref:blazor/components/data-binding#binding-with-component-parameters> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-114">For more information on property binding, see <xref:blazor/components/data-binding#binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="ec251-115">変更イベント デリゲートを <xref:Microsoft.AspNetCore.Components.Forms.InputText> コンポーネントの `ValueChanged` プロパティへ。</span><span class="sxs-lookup"><span data-stu-id="ec251-115">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="ec251-116"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [検証コンポーネント](#validator-components)は、データ注釈を使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="ec251-116">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="ec251-117"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントは、検証メッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="ec251-117">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="ec251-118">`HandleValidSubmit` は、フォームが正常に送信される (検証に合格する) とトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="ec251-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="ec251-119">組み込みフォームのコンポーネント</span><span class="sxs-lookup"><span data-stu-id="ec251-119">Built-in forms components</span></span>

<span data-ttu-id="ec251-120">一連の組み込みコンポーネントを、ユーザー入力の受信と検証に使用できます。</span><span class="sxs-lookup"><span data-stu-id="ec251-120">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="ec251-121">入力は、それらが変更されたときとフォームが送信されたときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="ec251-122">次の表に、使用できる入力コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="ec251-122">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="ec251-123">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ec251-123">Input component</span></span> | <span data-ttu-id="ec251-124">&hellip; とレンダリング</span><span class="sxs-lookup"><span data-stu-id="ec251-124">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="ec251-125">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ec251-125">Input component</span></span> | <span data-ttu-id="ec251-126">&hellip; とレンダリング</span><span class="sxs-lookup"><span data-stu-id="ec251-126">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="ec251-127">`InputRadio` と `InputRadioGroup` は、ASP.NET Core 5.0 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="ec251-127">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="ec251-128">詳細については、この記事のバージョン 5.0 以降を選択してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-128">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="ec251-129">すべての入力コンポーネント (<xref:Microsoft.AspNetCore.Components.Forms.EditForm> を含む) で、任意の属性がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ec251-129">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="ec251-130">コンポーネント パラメーターに一致しない属性は、レンダリングされる HTML 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-130">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="ec251-131">入力コンポーネントにより、フィールド変更時に検証するための既定の動作が提供されます。これには、フィールドの状態を反映するようにフィールドの CSS クラスを更新することが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ec251-131">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="ec251-132">一部のコンポーネントには、便利な解析ロジックが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ec251-132">Some components include useful parsing logic.</span></span> <span data-ttu-id="ec251-133">たとえば、<xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> と <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> では、解析不能な値を検証エラーとして登録することによって、解析不能な値を適切に処理します。</span><span class="sxs-lookup"><span data-stu-id="ec251-133">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="ec251-134">NULL 値を受け入れることができる型では、ターゲット フィールドの NULL 値の許容もサポートしています (`int?` など)。</span><span class="sxs-lookup"><span data-stu-id="ec251-134">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="ec251-135">次の `Starship` 型では、以前の `ExampleModel` よりも大きなプロパティとデータ注釈のセットを使用して検証ロジックを定義しています。</span><span class="sxs-lookup"><span data-stu-id="ec251-135">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="ec251-136">前の例では、データ注釈が存在しないため、`Description` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="ec251-136">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="ec251-137">次のフォームでは、`Starship` モデルで定義されている検証を使用してユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="ec251-137">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="ec251-138"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> では、変更されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components/cascading-values-and-parameters) として <xref:Microsoft.AspNetCore.Components.Forms.EditContext> を作成します。</span><span class="sxs-lookup"><span data-stu-id="ec251-138">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="ec251-139"><xref:Microsoft.AspNetCore.Components.Forms.EditContext> **または** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> の **いずれか** を <xref:Microsoft.AspNetCore.Components.Forms.EditForm> に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="ec251-139">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="ec251-140">両方とも割り当てることはサポートされておらず、**ランタイム エラー** が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-140">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="ec251-141"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> では、有効および無効のフォームを送信するための便利なイベントが提供されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-141">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="ec251-142">カスタム コードを使用して検証をトリガーし、フィールドの値をチェックするには、<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec251-142">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="ec251-143">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ec251-143">In the following example:</span></span>

* <span data-ttu-id="ec251-144">**`Submit`** ボタンが選択されると、`HandleSubmit` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-144">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="ec251-145"><xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> を呼び出して、フォームを検証します。</span><span class="sxs-lookup"><span data-stu-id="ec251-145">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="ec251-146">検証結果に応じて、追加のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-146">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="ec251-147"><xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> に割り当てられたメソッドにビジネス ロジックを配置します。</span><span class="sxs-lookup"><span data-stu-id="ec251-147">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="ec251-148">検証メッセージを <xref:Microsoft.AspNetCore.Components.Forms.EditContext> から直接クリアする Framework API は存在しません。</span><span class="sxs-lookup"><span data-stu-id="ec251-148">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="ec251-149">このため、通常、フォームの新しい <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> に検証メッセージを追加することはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="ec251-149">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="ec251-150">検証メッセージを管理するには、この記事で説明するように、[ビジネス ロジック検証コード](#business-logic-validation)で[検証コンポーネント](#validator-components)を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec251-150">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="ec251-151">表示名のサポート</span><span class="sxs-lookup"><span data-stu-id="ec251-151">Display name support</span></span>

<span data-ttu-id="ec251-152">"*このセクションは、.NET 5 リリース候補 1 (RC1) 以降の ASP.NET Core に適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="ec251-152">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="ec251-153">次の組み込みコンポーネントでは、`DisplayName` パラメーターを使用した表示名がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ec251-153">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="ec251-154">`InputDate` コンポーネントの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ec251-154">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="ec251-155">表示名 (`DisplayName`) が `birthday` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="ec251-155">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="ec251-156">コンポーネントは、`DateTime` 型として `BirthDate` プロパティにバインドされています。</span><span class="sxs-lookup"><span data-stu-id="ec251-156">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="ec251-157">ユーザーが日付の値を指定しなかった場合、次のように検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-157">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="ec251-158">検証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ec251-158">Validator components</span></span>

<span data-ttu-id="ec251-159">検証コンポーネントでは、フォームの <xref:Microsoft.AspNetCore.Components.Forms.EditContext>の <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> を管理することで、フォームの検証をサポートします。</span><span class="sxs-lookup"><span data-stu-id="ec251-159">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="ec251-160">Blazor フレームワークでは、[検証属性 (データ注釈)](xref:mvc/models/validation#validation-attributes)に基づいて検証サポートをフォームにアタッチする <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントを提供します。</span><span class="sxs-lookup"><span data-stu-id="ec251-160">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="ec251-161">カスタム検証コンポーネントを作成して、同じページの異なるフォームの検証メッセージを処理するか、フォーム処理の異なるステップ (たとえば、クライアント側の検証の後のサーバー側の検証) で同じフォームの検証メッセージを処理します。</span><span class="sxs-lookup"><span data-stu-id="ec251-161">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="ec251-162">このセクションで示す検証コンポーネントの例 (`CustomValidator`) は、この記事の次のセクションで使用します。</span><span class="sxs-lookup"><span data-stu-id="ec251-162">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="ec251-163">ビジネス ロジックの検証</span><span class="sxs-lookup"><span data-stu-id="ec251-163">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="ec251-164">サーバーの検証</span><span class="sxs-lookup"><span data-stu-id="ec251-164">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="ec251-165">多くの場合、カスタムのデータ注釈検証属性をカスタム検証コンポーネントの代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="ec251-165">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="ec251-166">フォームのモデルに適用されるカスタム属性は、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントを使用してアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-166">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="ec251-167">サーバー側の検証で使用する場合、モデルに適用されるカスタム属性はすべてサーバー上で実行可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec251-167">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="ec251-168">詳細については、「<xref:mvc/models/validation#alternatives-to-built-in-attributes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-168">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="ec251-169"><xref:Microsoft.AspNetCore.Components.ComponentBase>から検証コンポーネントを作成します。</span><span class="sxs-lookup"><span data-stu-id="ec251-169">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="ec251-170">フォームの <xref:Microsoft.AspNetCore.Components.Forms.EditContext> は、コンポーネントの[カスケード パラメーターです](xref:blazor/components/cascading-values-and-parameters)。</span><span class="sxs-lookup"><span data-stu-id="ec251-170">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="ec251-171">検証コンポーネントが初期化されると、フォーム エラーの現在の一覧を保持するために新しい <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> が作成されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-171">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="ec251-172">フォームのコンポーネント内の開発者コードで `DisplayErrors` メソッドが呼び出されると、メッセージ ストアでエラーが受け取られます。</span><span class="sxs-lookup"><span data-stu-id="ec251-172">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="ec251-173">そのエラーは、[`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2)内の `DisplayErrors` メソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-173">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="ec251-174">ディクショナリでは、キーは、1 つ以上のエラーがあるフォーム フィールドの名前です。</span><span class="sxs-lookup"><span data-stu-id="ec251-174">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="ec251-175">値は、エラー一覧です。</span><span class="sxs-lookup"><span data-stu-id="ec251-175">The value is the error list.</span></span>
* <span data-ttu-id="ec251-176">次のいずれかが発生すると、メッセージはクリアされます。</span><span class="sxs-lookup"><span data-stu-id="ec251-176">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="ec251-177"><xref:Microsoft.AspNetCore.Components.Forms.EditContext> で検証が要求され、<xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> イベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="ec251-177">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="ec251-178">すべてのエラーがクリアされます。</span><span class="sxs-lookup"><span data-stu-id="ec251-178">All of the errors are cleared.</span></span>
  * <span data-ttu-id="ec251-179">フォームのフィールドが変更され、<xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> イベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="ec251-179">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="ec251-180">そのフィールドのエラーのみがクリアされます。</span><span class="sxs-lookup"><span data-stu-id="ec251-180">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="ec251-181">開発者コードによって `ClearErrors` メソッドが呼び出された場合。</span><span class="sxs-lookup"><span data-stu-id="ec251-181">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="ec251-182">すべてのエラーがクリアされます。</span><span class="sxs-lookup"><span data-stu-id="ec251-182">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

public class CustomValidator : ComponentBase
{
    private ValidationMessageStore messageStore;

    [CascadingParameter]
    private EditContext CurrentEditContext { get; set; }

    protected override void OnInitialized()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException(
                $"{nameof(CustomValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. " +
                $"For example, you can use {nameof(CustomValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        messageStore = new ValidationMessageStore(CurrentEditContext);

        CurrentEditContext.OnValidationRequested += (s, e) => 
            messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => 
            messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
        }

        CurrentEditContext.NotifyValidationStateChanged();
    }

    public void ClearErrors()
    {
        messageStore.Clear();
        CurrentEditContext.NotifyValidationStateChanged();
    }
}
```

> [!NOTE]
> <span data-ttu-id="ec251-183">匿名のラムダ式は、<xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> と前の例の <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> に対して登録されているイベント ハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="ec251-183">Anonymous lambda expressions are registered event handlers for <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> and <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> in the preceding example.</span></span> <span data-ttu-id="ec251-184">このシナリオでは、<xref:System.IDisposable> を実装したり、イベント デリゲートの登録を解除したりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ec251-184">It isn't necessary to implement <xref:System.IDisposable> and unsubscribe the event delegates in this scenario.</span></span> <span data-ttu-id="ec251-185">詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-185">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="business-logic-validation"></a><span data-ttu-id="ec251-186">ビジネス ロジックの検証</span><span class="sxs-lookup"><span data-stu-id="ec251-186">Business logic validation</span></span>

<span data-ttu-id="ec251-187">ビジネス ロジックの検証は、ディクショナリ内のフォーム エラーを受け取る[検証コンポーネント](#validator-components)を使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-187">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="ec251-188">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ec251-188">In the following example:</span></span>

* <span data-ttu-id="ec251-189">この記事の「[検証コンポーネント](#validator-components)」セクションの`CustomValidator` コンポーネントが使用されています。</span><span class="sxs-lookup"><span data-stu-id="ec251-189">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="ec251-190">検証では、ユーザーが`Defense`船の分類 (`Classification`) を選択した場合に船の説明 (`Description`) の値を要求します。</span><span class="sxs-lookup"><span data-stu-id="ec251-190">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="ec251-191">検証メッセージは、コンポーネント内で設定されると、検証コンポーネントの <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> に追加され、<xref:Microsoft.AspNetCore.Components.Forms.EditForm> に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-191">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="ec251-192">[検証コンポーネント](#validator-components)を使用する代わりに、データ注釈検証属性を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="ec251-192">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="ec251-193">フォームのモデルに適用されるカスタム属性は、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントを使用してアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-193">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="ec251-194">サーバー側の検証で使用する場合、属性はサーバーで実行できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec251-194">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="ec251-195">詳細については、「<xref:mvc/models/validation#alternatives-to-built-in-attributes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-195">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="ec251-196">サーバーの検証</span><span class="sxs-lookup"><span data-stu-id="ec251-196">Server validation</span></span>

<span data-ttu-id="ec251-197">サーバーの検証は、サーバー[検証コンポーネント](#validator-components)を使用して実現できます。</span><span class="sxs-lookup"><span data-stu-id="ec251-197">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="ec251-198"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントを使用して、フォーム内のクライアント側の検証を処理します。</span><span class="sxs-lookup"><span data-stu-id="ec251-198">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="ec251-199">フォームによってクライアント側の検証が渡されると (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> が呼び出されると)、フォーム処理のために、<xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> がバックエンド サーバー API に送信されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-199">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="ec251-200">サーバーでモデルの検証を処理します。</span><span class="sxs-lookup"><span data-stu-id="ec251-200">Process model validation on the server.</span></span>
* <span data-ttu-id="ec251-201">サーバー API には、組み込みのフレームワーク データ注釈検証と開発者によって提供されるカスタムの検証ロジックの両方が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ec251-201">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="ec251-202">サーバーで検証が成功すると、フォームが処理され、成功の状態コード (*200 - OK*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-202">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="ec251-203">検証が失敗すると、失敗の状態コード (*400 - 無効な要求*) とフィールド検証エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-203">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="ec251-204">成功時にフォームを無効にするか、エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="ec251-204">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="ec251-205">次の例は、以下のものに基づいています。</span><span class="sxs-lookup"><span data-stu-id="ec251-205">The following example is based on:</span></span>

* <span data-ttu-id="ec251-206">[Blazorホストされているプロジェクト テンプレート](xref:blazor/hosting-models#blazor-webassembly)によって作成された、ホストされている Blazor ソリューション。</span><span class="sxs-lookup"><span data-stu-id="ec251-206">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="ec251-207">この例は、[セキュリティと Identity のドキュメント](xref:blazor/security/webassembly/index#implementation-guidance)で説明されている、安全でホストされている Blazor ソリューションのいずれかで使用できます。</span><span class="sxs-lookup"><span data-stu-id="ec251-207">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="ec251-208">前述の「[組み込みフォームのコンポーネント](#built-in-forms-components)」セクションの *Starfleet Starship Database* フォーム例。</span><span class="sxs-lookup"><span data-stu-id="ec251-208">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="ec251-209">Blazor フレームワークの <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="ec251-209">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="ec251-210">「[検証コンポーネント](#validator-components)」セクションで示した `CustomValidator` コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="ec251-210">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="ec251-211">次の例では、サーバー API で、ユーザーが `Defense` 船の分類 (`Classification`) を選択した場合に船の説明 (`Description`) の値が提供されるかどうかを検証します。</span><span class="sxs-lookup"><span data-stu-id="ec251-211">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="ec251-212">`Starship` モデルをソリューションの `Shared` プロジェクトに配置して、クライアントとサーバーの両方のアプリでモデルを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="ec251-212">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="ec251-213">モデルにはデータ注釈が必要であるため、[`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) のパッケージ参照を `Shared` プロジェクトのプロジェクト ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="ec251-213">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="ec251-214">パッケージのプレビュー版以外の最新バージョンを確認するには、[NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations)でパッケージの **バージョン履歴** を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-214">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="ec251-215">サーバー API プロジェクトでは、starship 検証要求 (`Controllers/StarshipValidation.cs`) を処理し、失敗した検証のメッセージを返すコントローラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec251-215">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="ec251-216">前の例では、プレースホルダー `{ASSEMBLY NAME}` はアプリのアセンブリ名です (たとえば `BlazorSample.Server`)。</span><span class="sxs-lookup"><span data-stu-id="ec251-216">In the preceding example, the placeholder `{ASSEMBLY NAME}` is the app's assembly name (for example, `BlazorSample.Server`).</span></span>

<span data-ttu-id="ec251-217">サーバーでモデル バインド検証エラーが発生した場合、通常、[`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) により、[既定の無効な要求応答](xref:web-api/index#default-badrequest-response)と <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> が返されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-217">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="ec251-218">*Starfleet Starship Database* フォームのすべてのフィールドが送信されず、フォームの検証が失敗した場合、次の例に示すように、応答には、検証エラー以外のデータも含まれます。</span><span class="sxs-lookup"><span data-stu-id="ec251-218">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="ec251-219">サーバー API によって、前述の既定の JSON 応答が返された場合、クライアントでは、応答を解析して、`errors` ノードの子を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="ec251-219">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="ec251-220">ただし、ファイルの解析は容易ではありません。</span><span class="sxs-lookup"><span data-stu-id="ec251-220">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="ec251-221">JSON を解析するには、フォーム検証エラーを処理するためのエラーの [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) を生成するために、<xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> を呼び出した後に追加のコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="ec251-221">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="ec251-222">サーバー API で検証エラーのみを返すのが理想的です。</span><span class="sxs-lookup"><span data-stu-id="ec251-222">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="ec251-223">サーバー API の応答を変更して、検証エラーのみが返されるようにするには、`Startup.ConfigureServices` の <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> で注釈が付けられたアクションで呼び出されるデリゲートを変更します。</span><span class="sxs-lookup"><span data-stu-id="ec251-223">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ec251-224">API エンドポイント (`/StarshipValidation`) の場合、<xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> と <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> が返されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-224">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="ec251-225">他の API エンドポイントの場合、オブジェクトの結果と新しい <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>が返され、既定の動作が保持されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-225">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="ec251-226">詳細については、「<xref:web-api/handle-errors#validation-failure-error-response>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-226">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="ec251-227">クライアント プロジェクトでは、「[検証コンポーネント](#validator-components)」セクションで示した検証コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec251-227">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="ec251-228">また、*Starfleet Starship Database* フォームが、`CustomValidator` コンポーネントを使用してサーバー検証エラーを示すように更新されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-228">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="ec251-229">サーバー API によって検証メッセージが返されると、それらは、`CustomValidator` コンポーネントの <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>に追加されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-229">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="ec251-230">エラーは、フォームの <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>で表示するために、フォームの <xref:Microsoft.AspNetCore.Components.Forms.EditContext> で使用することができます。</span><span class="sxs-lookup"><span data-stu-id="ec251-230">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="ec251-231">[検証コンポーネント](#validator-components)の代わりに、データ注釈検証属性を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="ec251-231">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="ec251-232">フォームのモデルに適用されるカスタム属性は、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントを使用してアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-232">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="ec251-233">サーバー側の検証で使用する場合、属性はサーバーで実行できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec251-233">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="ec251-234">詳細については、「<xref:mvc/models/validation#alternatives-to-built-in-attributes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-234">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="ec251-235">このセクションで説明したサーバー側の検証は、このドキュメント セットにある、Blazor WebAssemblyでホストされるどのソリューション例にも適しています。</span><span class="sxs-lookup"><span data-stu-id="ec251-235">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="ec251-236">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="ec251-236">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="ec251-237">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="ec251-237">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="ec251-238">Identity サーバー</span><span class="sxs-lookup"><span data-stu-id="ec251-238">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="ec251-239">入力イベントに基づく InputText</span><span class="sxs-lookup"><span data-stu-id="ec251-239">InputText based on the input event</span></span>

<span data-ttu-id="ec251-240">`change` イベントではなく、`input` イベントを使用するカスタム コンポーネントを作成するには、<xref:Microsoft.AspNetCore.Components.Forms.InputText> コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec251-240">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="ec251-241">次の例では、`CustomInputText` コンポーネントによってフレームワークの `InputText` コンポーネントが継承され、イベント バインディングが `oninput` イベントに設定されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-241">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets event binding to the `oninput` event.</span></span>

<span data-ttu-id="ec251-242">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="ec251-242">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input @attributes="AdditionalAttributes" class="@CssClass" 
    @bind="CurrentValueAsString" @bind:event="oninput" />
```

<span data-ttu-id="ec251-243">`CustomInputText` コンポーネントは、<xref:Microsoft.AspNetCore.Components.Forms.InputText> が使用される場所であればどこでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="ec251-243">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="ec251-244">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="ec251-244">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="ec251-245">ラジオ ボタン</span><span class="sxs-lookup"><span data-stu-id="ec251-245">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ec251-246">ラジオ ボタン グループを作成するには、`InputRadioGroup` コンポーネントと共に `InputRadio` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec251-246">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="ec251-247">次の例では、「[組み込みフォームのコンポーネント](#built-in-forms-components)」セクションで説明されている `Starship` モデルにプロパティが追加されています。</span><span class="sxs-lookup"><span data-stu-id="ec251-247">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="ec251-248">アプリに以下の `enums` を追加します。</span><span class="sxs-lookup"><span data-stu-id="ec251-248">Add the following `enums` to the app.</span></span> <span data-ttu-id="ec251-249">`enums` を保持する新しいファイルを作成するか、`Starship.cs` ファイルに `enums` を追加します。</span><span class="sxs-lookup"><span data-stu-id="ec251-249">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="ec251-250">`Starship` モデルと *Starfleet Starship Database* フォームから `enums` にアクセスできるようにします。</span><span class="sxs-lookup"><span data-stu-id="ec251-250">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="ec251-251">「[組み込みフォームのコンポーネント](#built-in-forms-components)」セクションで説明している *Starfleet Starship Database* フォームを更新します。</span><span class="sxs-lookup"><span data-stu-id="ec251-251">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="ec251-252">生成するコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec251-252">Add the components to produce:</span></span>

* <span data-ttu-id="ec251-253">船舶製造元のラジオ ボタン グループ。</span><span class="sxs-lookup"><span data-stu-id="ec251-253">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="ec251-254">船の色およびエンジン用の入れ子になったラジオ ボタン グループ。</span><span class="sxs-lookup"><span data-stu-id="ec251-254">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="ec251-255">`Name` を省略した場合、`InputRadio` コンポーネントは最新の先祖を基準にグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-255">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ec251-256">フォームでオプション ボタンを使用する場合、オプション ボタンはグループとして評価されるため、データ バインディングが他の要素と異なる方法で処理されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-256">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="ec251-257">各オプション ボタンの値は固定ですが、オプション ボタン グループの値は、選択されたオプション ボタンの値です。</span><span class="sxs-lookup"><span data-stu-id="ec251-257">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="ec251-258">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="ec251-258">The following example shows how to:</span></span>

* <span data-ttu-id="ec251-259">オプション ボタン グループのデータバインディングを処理する。</span><span class="sxs-lookup"><span data-stu-id="ec251-259">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="ec251-260">カスタム `InputRadio` コンポーネントを使用した検証をサポートする。</span><span class="sxs-lookup"><span data-stu-id="ec251-260">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="ec251-261">次の <xref:Microsoft.AspNetCore.Components.Forms.EditForm> では、前の `InputRadio` コンポーネントを使用して、ユーザーから評価を取得して検証しています。</span><span class="sxs-lookup"><span data-stu-id="ec251-261">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="ec251-262">C# オブジェクトの `null` 値への `<select>` 要素オプションのバインド</span><span class="sxs-lookup"><span data-stu-id="ec251-262">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="ec251-263">次の理由により、`<select>` 要素のオプション値を C# オブジェクトの `null` 値として表すよい方法はありません。</span><span class="sxs-lookup"><span data-stu-id="ec251-263">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="ec251-264">HTML 属性には `null` 値を設定できません。</span><span class="sxs-lookup"><span data-stu-id="ec251-264">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="ec251-265">HTML で `null` に最も近いのは、`<option>` 要素から HTML の `value` 属性を除去することです。</span><span class="sxs-lookup"><span data-stu-id="ec251-265">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="ec251-266">`value` 属性のない `<option>` を選択すると、ブラウザーではその `<option>` の要素の "*テキスト コンテンツ*" として値が扱われます。</span><span class="sxs-lookup"><span data-stu-id="ec251-266">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="ec251-267">Blazor フレームワークでは、次の処理が必要になるため、既定の動作の抑制は試みられません。</span><span class="sxs-lookup"><span data-stu-id="ec251-267">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="ec251-268">フレームワークでの特殊なケースの回避策のチェーンの作成。</span><span class="sxs-lookup"><span data-stu-id="ec251-268">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="ec251-269">現在のフレームワークの動作に対する破壊的変更。</span><span class="sxs-lookup"><span data-stu-id="ec251-269">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ec251-270">HTML で `null` に最も近いと思われるものは、"*空の文字列*" の `value` です。</span><span class="sxs-lookup"><span data-stu-id="ec251-270">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="ec251-271">Blazor フレームワークでは、`<select>` の値への双方向バインドに対する空の文字列変換として `null` が処理されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-271">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ec251-272">Blazor フレームワークでは、`<select>` の値への双方向バインドを試みるときに、`null` は空の文字列変換として自動的に処理されません。</span><span class="sxs-lookup"><span data-stu-id="ec251-272">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="ec251-273">詳細については、「[null 値への `<select>` のバインドの修正 (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-273">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="ec251-274">検証のサポート</span><span class="sxs-lookup"><span data-stu-id="ec251-274">Validation support</span></span>

<span data-ttu-id="ec251-275"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントは、データ注釈を使用した検証サポートをカスケードされた <xref:Microsoft.AspNetCore.Components.Forms.EditContext> にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="ec251-275">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="ec251-276">データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。</span><span class="sxs-lookup"><span data-stu-id="ec251-276">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="ec251-277">データ注釈と異なる検証システムを使用するには、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> をカスタム実装に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ec251-277">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="ec251-278">参照ソース [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) での検査に、ASP.NET Core 実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ec251-278">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="ec251-279">上記の参照ソースへのリンクからは、リポジトリの `master` ブランチが提供されます。このブランチは、ASP.NET Core の次回リリースのための製品単位の現行開発を表します。</span><span class="sxs-lookup"><span data-stu-id="ec251-279">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="ec251-280">別のリリースのブランチを選択するには、GitHub ブランチ セレクターを使用します (`release/3.1` など)。</span><span class="sxs-lookup"><span data-stu-id="ec251-280">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="ec251-281">Blazor は 2 種類の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec251-281">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="ec251-282">*フィールド検証* は、ユーザーがタブでフィールドを離れたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-282">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="ec251-283">フィールドの検証時に、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントによって、報告されたすべての検証結果がフィールドに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="ec251-283">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="ec251-284">*モデル検証* は、ユーザーがフォームを送信したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-284">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="ec251-285">モデルの検証時に、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントは、検証結果で報告されたメンバー名に基づいてフィールドを判断しようとします。</span><span class="sxs-lookup"><span data-stu-id="ec251-285">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="ec251-286">個々のメンバーに関連付けられていない検証結果は、フィールドではなくモデルに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="ec251-286">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="ec251-287">検証概要コンポーネントと検証メッセージ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ec251-287">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="ec251-288"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントは、すべての検証メッセージを要約します。これは[検証概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)と似ています。</span><span class="sxs-lookup"><span data-stu-id="ec251-288">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="ec251-289">`Model` パラメーターを使用して、特定のモデルの検証メッセージを出力します。</span><span class="sxs-lookup"><span data-stu-id="ec251-289">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="ec251-290"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> コンポーネントは、特定のフィールドの検証メッセージを表示します。これは、[検証メッセージ タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似ています。</span><span class="sxs-lookup"><span data-stu-id="ec251-290">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="ec251-291">`For` 属性と、モデル プロパティに名前を付けるラムダ式で、検証するフィールドを指定します。</span><span class="sxs-lookup"><span data-stu-id="ec251-291">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="ec251-292"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> コンポーネントと <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントでは、任意の属性をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ec251-292">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="ec251-293">コンポーネント パラメーターに一致しない属性は、生成された `<div>` 要素または `<ul>` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-293">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="ec251-294">アプリのスタイルシート (`wwwroot/css/app.css` または `wwwroot/css/site.css`) での検証メッセージのスタイルを制御します。</span><span class="sxs-lookup"><span data-stu-id="ec251-294">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="ec251-295">既定の `validation-message` クラスでは、検証メッセージのテキストの色が赤に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-295">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="ec251-296">カスタム検証属性</span><span class="sxs-lookup"><span data-stu-id="ec251-296">Custom validation attributes</span></span>

<span data-ttu-id="ec251-297">[カスタム検証属性](xref:mvc/models/validation#custom-attributes)を使用するときに、検証結果がフィールドに正しく関連付けられるようにするには、<xref:System.ComponentModel.DataAnnotations.ValidationResult> の作成時に検証コンテキストの <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> を渡します。</span><span class="sxs-lookup"><span data-stu-id="ec251-297">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="ec251-298"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> が `null`です。</span><span class="sxs-lookup"><span data-stu-id="ec251-298"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="ec251-299">`IsValid` メソッドでの検証に対する挿入サービスはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ec251-299">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="ec251-300">カスタム検証クラス属性</span><span class="sxs-lookup"><span data-stu-id="ec251-300">Custom validation class attributes</span></span>

<span data-ttu-id="ec251-301">カスタム検証クラス名は、[Bootstrap](https://getbootstrap.com/) などの CSS フレームワークと統合する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ec251-301">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="ec251-302">カスタム検証クラス名を指定するには、`FieldCssClassProvider` から派生したクラスを作成し、そのクラスを <xref:Microsoft.AspNetCore.Components.Forms.EditContext> インスタンスに設定します。</span><span class="sxs-lookup"><span data-stu-id="ec251-302">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="blazor-data-annotations-validation-package"></a><span data-ttu-id="ec251-303">Blazor データ注釈検証パッケージ</span><span class="sxs-lookup"><span data-stu-id="ec251-303">Blazor data annotations validation package</span></span>

<span data-ttu-id="ec251-304">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) は、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントを使用して検証エクスペリエンスのギャップを埋めるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="ec251-304">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="ec251-305">パッケージは現在、*試験段階* です。</span><span class="sxs-lookup"><span data-stu-id="ec251-305">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="ec251-306">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) パッケージには、[Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) に "*リリース候補*" の最新バージョンが含まれています。現時点では、"*試験的*" リリース候補パッケージを継続して使用します。</span><span class="sxs-lookup"><span data-stu-id="ec251-306">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="ec251-307">パッケージのアセンブリは、将来のリリースでフレームワークまたはランタイムのいずれかに移動される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec251-307">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="ec251-308">更新の詳細については、[Announcements GitHub リポジトリ](https://github.com/aspnet/Announcements)、[dotnet/aspnetcore GitHub リポジトリ](https://github.com/dotnet/aspnetcore)、またはこのトピック セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-308">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a><span data-ttu-id="ec251-309">`[CompareProperty]` 属性</span><span class="sxs-lookup"><span data-stu-id="ec251-309">`[CompareProperty]` attribute</span></span>

<span data-ttu-id="ec251-310"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> は、検証結果を特定のメンバーに関連付けないため、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントで正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="ec251-310">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="ec251-311">これにより、フィールドレベルの検証と、送信時のモデル全体が検証されたときの動作に一貫性がなくなることがあります。</span><span class="sxs-lookup"><span data-stu-id="ec251-311">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="ec251-312">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) "*試験的*" パッケージでは、これらの制限を回避する追加の検証属性 `ComparePropertyAttribute` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="ec251-312">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="ec251-313">Blazor アプリでは、`[CompareProperty]` は [`[Compare]` 属性](xref:System.ComponentModel.DataAnnotations.CompareAttribute)の直接の代わりとなるものです。</span><span class="sxs-lookup"><span data-stu-id="ec251-313">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]` attribute](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span></span>

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="ec251-314">入れ子になったモデル、コレクション型、および複合型</span><span class="sxs-lookup"><span data-stu-id="ec251-314">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="ec251-315">Blazor では、組み込みの <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> によるデータ注釈を使用したフォーム入力の検証をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ec251-315">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="ec251-316">ただし、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>で は、コレクション型または複合型のプロパティではないフォームにバインドされているモデルの最上位レベルのプロパティのみが検証されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-316">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="ec251-317">コレクション型と複合型のプロパティを含む、バインドされたモデルのオブジェクト グラフ全体を検証するには、"*試験的*" [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) パッケージによって提供される `ObjectGraphDataAnnotationsValidator` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec251-317">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="ec251-318">`[ValidateComplexType]` でモデルのプロパティに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="ec251-318">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="ec251-319">次のモデル クラスでは、`ShipDescription` クラスに、モデルがフォームにバインドされたときに検証する追加のデータ注釈が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ec251-319">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="ec251-320">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="ec251-320">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

<span data-ttu-id="ec251-321">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="ec251-321">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }

    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="ec251-322">フォームの検証に基づいて送信ボタンを有効にする</span><span class="sxs-lookup"><span data-stu-id="ec251-322">Enable the submit button based on form validation</span></span>

<span data-ttu-id="ec251-323">フォームの検証に基づいて送信ボタンを有効または無効にするには:</span><span class="sxs-lookup"><span data-stu-id="ec251-323">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="ec251-324">コンポーネントを初期化するときに、フォームの <xref:Microsoft.AspNetCore.Components.Forms.EditContext> を使用してモデルを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="ec251-324">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="ec251-325">コンテキストの <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> コールバックでフォームを検証して、送信ボタンを有効または無効にします。</span><span class="sxs-lookup"><span data-stu-id="ec251-325">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="ec251-326"><xref:System.IDisposable> を実装し、`Dispose` メソッドでイベント ハンドラーの登録を解除します。</span><span class="sxs-lookup"><span data-stu-id="ec251-326">Implement <xref:System.IDisposable> and unsubscribe the event handler in the `Dispose` method.</span></span> <span data-ttu-id="ec251-327">詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec251-327">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="ec251-328">さらに、<xref:Microsoft.AspNetCore.Components.Forms.EditContext>を使用する場合は、<xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> を <xref:Microsoft.AspNetCore.Components.Forms.EditForm>に割り当てないでください。</span><span class="sxs-lookup"><span data-stu-id="ec251-328">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="ec251-329">前の例では、次の場合に `formInvalid` を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="ec251-329">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="ec251-330">フォームには、有効な既定値が事前に読み込まれています。</span><span class="sxs-lookup"><span data-stu-id="ec251-330">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="ec251-331">フォームが読み込まれるときに、送信ボタンを有効にしたいと考えます。</span><span class="sxs-lookup"><span data-stu-id="ec251-331">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="ec251-332">上記の方法の副作用として、ユーザーがいずれかのフィールドを操作した後に、<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントに無効なフィールドが設定されます。</span><span class="sxs-lookup"><span data-stu-id="ec251-332">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="ec251-333">このシナリオは、次のいずれかの方法で対処できます。</span><span class="sxs-lookup"><span data-stu-id="ec251-333">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="ec251-334">フォームでは <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ec251-334">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="ec251-335">送信ボタンが選択された (たとえば、`HandleValidSubmit` メソッドで) ときに <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントを表示します。</span><span class="sxs-lookup"><span data-stu-id="ec251-335">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="ec251-336">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="ec251-336">Troubleshoot</span></span>

> <span data-ttu-id="ec251-337">InvalidOperationException:EditForm には、Model パラメーターまたは EditContext パラメーター (両方ではなく) が必要です。</span><span class="sxs-lookup"><span data-stu-id="ec251-337">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="ec251-338"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> に、<xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **または** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> が指定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec251-338">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="ec251-339">同じフォームに両方を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ec251-339">Don't use both for the same form.</span></span>

<span data-ttu-id="ec251-340">フォームに <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> を割り当てるときは、次の例に示すように、そのモデルの種類がインスタンス化されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec251-340">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="ec251-341">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ec251-341">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end

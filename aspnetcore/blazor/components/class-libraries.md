---
title: ASP.NET Core Razor コンポーネント クラス ライブラリ
author: guardrex
description: 外部コンポーネント ライブラリから、コンポーネントを Blazor アプリに含める方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252553"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="e3bf8-103">ASP.NET Core Razor コンポーネント クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="e3bf8-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="e3bf8-104">作成者: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="e3bf8-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="e3bf8-105">コンポーネントは、プロジェクト間で [Razor クラス ライブラリ (RCL)](xref:razor-pages/ui-class) で共有できます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="e3bf8-106">*Razor コンポーネント クラス ライブラリ* は、次から含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="e3bf8-107">ソリューションの別のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-107">Another project in the solution.</span></span>
* <span data-ttu-id="e3bf8-108">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-108">A NuGet package.</span></span>
* <span data-ttu-id="e3bf8-109">参照されている .NET ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-109">A referenced .NET library.</span></span>

<span data-ttu-id="e3bf8-110">コンポーネントが通常の .NET 型であるのと同様に、RCL によって提供されるコンポーネントは通常の .NET アセンブリです。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="e3bf8-111">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="e3bf8-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e3bf8-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e3bf8-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e3bf8-113">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-113">Create a new project.</span></span>
1. <span data-ttu-id="e3bf8-114">**[Razor クラス ライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="e3bf8-115">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-115">Select **Next**.</span></span>
1. <span data-ttu-id="e3bf8-116">**[新しい Razor クラス ライブラリを作成します]** ダイアログで **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="e3bf8-117">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="e3bf8-118">このトピックの例では、プロジェクト名 `ComponentLibrary` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="e3bf8-119">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-119">Select **Create**.</span></span>
1. <span data-ttu-id="e3bf8-120">RCL をソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="e3bf8-121">ソリューションを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-121">Right-click the solution.</span></span> <span data-ttu-id="e3bf8-122">**[追加]**  >  **[既存のプロジェクト]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="e3bf8-123">RCL のプロジェクト ファイルに移動します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="e3bf8-124">RCL のプロジェクト ファイル (`.csproj`) を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="e3bf8-125">アプリから RCL への参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-125">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="e3bf8-126">アプリ プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-126">Right-click the app project.</span></span> <span data-ttu-id="e3bf8-127">**[追加]**  >  **[参照]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="e3bf8-128">RCL プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-128">Select the RCL project.</span></span> <span data-ttu-id="e3bf8-129">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="e3bf8-130">テンプレートから RCL を生成するときに **[ページとビューのサポート]** チェック ボックスがオンになっている場合は、生成したプロジェクトのルートに、次の内容で `_Imports.razor` ファイルも追加して、Razor コンポーネントを作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="e3bf8-131">生成されたプロジェクトのルートにファイルを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e3bf8-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e3bf8-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="e3bf8-133">コマンド シェルで [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用して、 **Razor クラス ライブラリ** テンプレート (`razorclasslib`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="e3bf8-134">次の例では、`ComponentLibrary` という名前の RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="e3bf8-135">コマンドの実行時に、`ComponentLibrary` を保持するフォルダーが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="e3bf8-136">テンプレートから RCL を生成するときに、`-s|--support-pages-and-views` スイッチが使用されている場合、生成したプロジェクトのルートに、次の内容で `_Imports.razor` ファイルも追加して、Razor コンポーネントを作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="e3bf8-137">生成されたプロジェクトのルートにファイルを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="e3bf8-138">既存のプロジェクトにライブラリを追加するには、コマンド シェルで [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="e3bf8-139">次の例では、RCL がアプリに追加されています。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="e3bf8-140">ライブラリへのパスを使用して、アプリのプロジェクト フォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="e3bf8-141">ライブラリ コンポーネントの使用</span><span class="sxs-lookup"><span data-stu-id="e3bf8-141">Consume a library component</span></span>

<span data-ttu-id="e3bf8-142">別のプロジェクトのライブラリに定義されているコンポーネントを使用するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="e3bf8-143">名前空間と完全な型名を使用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="e3bf8-144">Razor の [`@using`](xref:mvc/views/razor#using) ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="e3bf8-145">個々のコンポーネントを名前で追加することができます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-145">Individual components can be added by name.</span></span>

<span data-ttu-id="e3bf8-146">次の例で、`ComponentLibrary` は `Component1` コンポーネント (`Component1.razor`) を含むコンポーネント ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="e3bf8-147">`Component1` コンポーネントは、ライブラリの作成時に RCL プロジェクト テンプレートによって自動的に追加されるサンプルのコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="e3bf8-148">`Component1` コンポーネントをその名前空間を使用して参照します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="e3bf8-149">または、[`@using`](xref:mvc/views/razor#using) ディレクティブを使用してライブラリをスコープ内に取り込み、名前空間なしでコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="e3bf8-150">必要に応じて、最上位の `_Import.razor` ファイルに `@using ComponentLibrary` ディレクティブを含めて、プロジェクト全体でライブラリのコンポーネントを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="e3bf8-151">ディレクティブを任意のレベルの `_Import.razor` ファイルに追加して、名前空間をフォルダー内の 1 つのコンポーネントまたは複数のコンポーネントに適用します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e3bf8-152">[CSS の分離](xref:blazor/components/css-isolation)を使用するライブラリ コンポーネントの場合、ライブラリを使用するアプリでライブラリの個々のコンポーネント スタイルシートを明示的にリンクする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-152">For library components that use [CSS isolation](xref:blazor/components/css-isolation), there's no need to explicitly link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="e3bf8-153">コンポーネント スタイルは、使用アプリで自動的に利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-153">The component styles are automatically made available to the consuming app.</span></span>

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

<span data-ttu-id="e3bf8-154">ライブラリの `wwwroot` フォルダーのスタイルシートから追加のライブラリ コンポーネント スタイルを提供するには、使用アプリの `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) のスタイルシートをリンクします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-154">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets in the consuming app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e3bf8-155">`Component1` の `my-component`CSS クラスを提供するには、アプリの `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) 内でライブラリのスタイルシートにリンクします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-155">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="e3bf8-156">静的アセットを含む Razor コンポーネント クラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="e3bf8-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="e3bf8-157">RCL には、静的アセットを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-157">An RCL can include static assets.</span></span> <span data-ttu-id="e3bf8-158">静的アセットは、ライブラリを使用するすべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="e3bf8-159">詳細については、「<xref:razor-pages/ui-class#create-an-rcl-with-static-assets>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="e3bf8-160">複数のホスト型 Blazor アプリにコンポーネントと静的アセットを提供する</span><span class="sxs-lookup"><span data-stu-id="e3bf8-160">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="e3bf8-161">詳細については、「<xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-161">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="e3bf8-162">Blazor WebAssembly のブラウザー互換性アナライザー</span><span class="sxs-lookup"><span data-stu-id="e3bf8-162">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="e3bf8-163">Blazor WebAssembly アプリは完全な .NET API 領域を対象としていますが、ブラウザー サンドボックスの制約により、すべての .NET API が WebAssembly でサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-163">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="e3bf8-164">サポートされていない API は、WebAssembly で実行すると <xref:System.PlatformNotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-164">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="e3bf8-165">開発者が、アプリのターゲット プラットフォームでサポートされていない API をアプリで使用すると、プラットフォーム互換性アナライザーから警告を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-165">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="e3bf8-166">Blazor WebAssembly アプリの場合、API がブラウザーでサポートされているかどうかが確認されるということです。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-166">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="e3bf8-167">互換性アナライザーの .NET フレームワーク API に注釈を付けることは、進行中のプロセスであるため、現在、すべての .NET フレームワーク API に注釈が付けられるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-167">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="e3bf8-168">Blazor WebAssembly および Razor クラス ライブラリ プロジェクトでは、`SupportedPlatform` MSBuild 項目でサポートされているプラットフォームとして `browser` を追加することで、ブラウザーの互換性チェックを "*自動的*" に有効にします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-168">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="e3bf8-169">ライブラリ開発者は、`SupportedPlatform` 項目をライブラリのプロジェクト ファイルに手動で追加して、この機能を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-169">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="e3bf8-170">ライブラリを作成する場合は、<xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> に `browser` を指定して、ブラウザーで特定の API がサポートされていないことを示します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-170">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="e3bf8-171">詳細については、「[特定のプラットフォームでサポートされていない API に注釈を付ける (dotnet/designs GitHub リポジトリ)」](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)」参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-171">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="e3bf8-172">Blazor JavaScript の分離とオブジェクト参照</span><span class="sxs-lookup"><span data-stu-id="e3bf8-172">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="e3bf8-173">Blazor により、標準 [JavaScript モジュール](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)で JavaScript の分離が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-173">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="e3bf8-174">JavaScript の分離には、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-174">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="e3bf8-175">インポートされる JavaScript によって、グローバル名前空間が汚染されなくなります。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-175">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="e3bf8-176">ライブラリおよびコンポーネントのコンシューマーは、関連する JavaScript を手動でインポートする必要がありません。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-176">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="e3bf8-177">詳細については、「<xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-177">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="e3bf8-178">ビルド、パック、NuGet への配布</span><span class="sxs-lookup"><span data-stu-id="e3bf8-178">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="e3bf8-179">コンポーネント ライブラリは標準 .NET ライブラリであるため、それらをパッケージ化して NuGet に配布することは、ライブラリをパッケージ化して NuGet に配布する場合と変わりはありません。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-179">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="e3bf8-180">パッケージ化は、コマンド シェルで [`dotnet pack`](/dotnet/core/tools/dotnet-pack) コマンドを使用して実行します。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-180">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="e3bf8-181">コマンド シェルで [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) コマンドを使用して、パッケージを NuGet にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="e3bf8-181">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e3bf8-182">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e3bf8-182">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="e3bf8-183">XML 中間言語 (IL) トリマーの構成ファイルをライブラリに追加する</span><span class="sxs-lookup"><span data-stu-id="e3bf8-183">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="e3bf8-184">Razor クラスライブラリを使用した CSS の分離のサポート</span><span class="sxs-lookup"><span data-stu-id="e3bf8-184">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="e3bf8-185">XML 中間言語 (IL) リンカーの構成ファイルをライブラリに追加する</span><span class="sxs-lookup"><span data-stu-id="e3bf8-185">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end

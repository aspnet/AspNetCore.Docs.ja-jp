---
title: ASP.NET Core Blazor 用のリンカーを構成する
author: guardrex
description: Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: c720747983da4ef6997d95d77c3f5305cfd7d3c0
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279926"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="f3eda-103">ASP.NET Core Blazor 用のリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="f3eda-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="f3eda-104">Blazor WebAssembly では、ビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンクが実行されて、アプリの出力アセンブリから不要な IL がトリミングされます。</span><span class="sxs-lookup"><span data-stu-id="f3eda-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="f3eda-105">デバッグ構成でビルドすると、リンカーは無効になります。</span><span class="sxs-lookup"><span data-stu-id="f3eda-105">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="f3eda-106">リンカーを有効にするには、アプリをリリース構成でビルドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3eda-106">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="f3eda-107">Blazor WebAssembly アプリを配置する場合は、リリースでビルドすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f3eda-107">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="f3eda-108">アプリをリンクするとサイズが最適化されますが、悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f3eda-108">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="f3eda-109">リフレクションや関連する動的機能を使用するアプリは、トリミングされたときに中断する可能性があります。リンカーがこの動的な動作を認識せず、通常は実行時にリフレクションに必要な型を特定できないためです。</span><span class="sxs-lookup"><span data-stu-id="f3eda-109">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="f3eda-110">そのようなアプリをトリミングするには、コードと、アプリが依存しているパッケージまたはフレームワークのリフレクションで必要なすべての型を、リンカーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3eda-110">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="f3eda-111">トリミングされたアプリが配置後に正しく動作するには、開発中にアプリのリリース ビルドを頻繁にテストすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="f3eda-111">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="f3eda-112">Blazor アプリのリンクは、次の MSBuild 機能を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="f3eda-112">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="f3eda-113">[MSBuild プロパティ](#control-linking-with-an-msbuild-property)を使ってリンクをグローバルに構成する。</span><span class="sxs-lookup"><span data-stu-id="f3eda-113">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="f3eda-114">[構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。</span><span class="sxs-lookup"><span data-stu-id="f3eda-114">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="f3eda-115">MSBuild プロパティを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="f3eda-115">Control linking with an MSBuild property</span></span>

<span data-ttu-id="f3eda-116">リンクは、アプリが `Release` 構成でビルドされると有効になります。</span><span class="sxs-lookup"><span data-stu-id="f3eda-116">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="f3eda-117">これを変更するには、プロジェクト ファイルで `BlazorWebAssemblyEnableLinking` の MSBuild プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="f3eda-117">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="f3eda-118">構成ファイルを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="f3eda-118">Control linking with a configuration file</span></span>

<span data-ttu-id="f3eda-119">XML の構成ファイルを用意してそのファイルをプロジェクト ファイル内で MSBuild 項目として指定することで、アセンブリごとにリンクを制御します。</span><span class="sxs-lookup"><span data-stu-id="f3eda-119">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="f3eda-120">`LinkerConfig.xml`:</span><span class="sxs-lookup"><span data-stu-id="f3eda-120">`LinkerConfig.xml`:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="f3eda-121">詳細と例については、[データ形式 (mono/linker GitHub リポジトリ)](https://github.com/mono/linker/blob/master/docs/data-formats.md) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3eda-121">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="f3eda-122">XML リンカー構成ファイルをライブラリに追加する</span><span class="sxs-lookup"><span data-stu-id="f3eda-122">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="f3eda-123">特定のライブラリ用にリンカーを構成するには、XML リンカー構成ファイルを埋め込みリソースとしてライブラリに追加します。</span><span class="sxs-lookup"><span data-stu-id="f3eda-123">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="f3eda-124">埋め込みリソースの名前は、アセンブリと同じにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3eda-124">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="f3eda-125">次の例では、`LinkerConfig.xml` ファイルが、ライブラリのアセンブリと同じ名前を持つ埋め込みリソースとして指定されています。</span><span class="sxs-lookup"><span data-stu-id="f3eda-125">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="f3eda-126">国際化用にリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="f3eda-126">Configure the linker for internationalization</span></span>

<span data-ttu-id="f3eda-127">既定では、Blazor WebAssembly アプリに対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。</span><span class="sxs-lookup"><span data-stu-id="f3eda-127">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="f3eda-128">これらのアセンブリを削除すると、アプリのサイズが最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="f3eda-128">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="f3eda-129">保持される I18N アセンブリを制御するには、プロジェクト ファイルで MSBuild のプロパティ `<BlazorWebAssemblyI18NAssemblies>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="f3eda-129">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="f3eda-130">リージョンの値</span><span class="sxs-lookup"><span data-stu-id="f3eda-130">Region Value</span></span>     | <span data-ttu-id="f3eda-131">Mono のリージョン アセンブリ</span><span class="sxs-lookup"><span data-stu-id="f3eda-131">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="f3eda-132">すべてのアセンブリが含まれます</span><span class="sxs-lookup"><span data-stu-id="f3eda-132">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="f3eda-133">`none` (既定値)</span><span class="sxs-lookup"><span data-stu-id="f3eda-133">`none` (default)</span></span> | <span data-ttu-id="f3eda-134">None</span><span class="sxs-lookup"><span data-stu-id="f3eda-134">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="f3eda-135">複数の値を区切るにはコンマを使用します (例: `mideast,west`)。</span><span class="sxs-lookup"><span data-stu-id="f3eda-135">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="f3eda-136">詳しくは、「[I18N: Pnetlib 国際化フレームワーク ライブラリ (mono/mono GitHub リポジトリ)](https://github.com/mono/mono/tree/master/mcs/class/I18N)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f3eda-136">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3eda-137">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f3eda-137">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>

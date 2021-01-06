---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: ASP.NET Core の Razor Pages を使用して、ページのコーディングに重点を置いたシナリオをより簡略化して、MVC を使用する場合よりも生産性を高める方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: d3b01889b7634dce8ef1d6a4886a9a6ac39a6473
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060873"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>ASP.NET Core Razor SDK

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>概要

[!INCLUDE[](~/includes/2.1-SDK.md)] には、`Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK) が含まれています。 Razor SDK には次の特徴があります。

::: moniker range=">= aspnetcore-3.0"

* ASP.NET Core MVC ベースまたは [Blazor](xref:blazor/index) プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ化、および発行を行うために必要です。
* Razor ( *.cshtml* または *.razor*) ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットが含まれます。

Razor SDK には、`Include` 属性が `**\*.cshtml` および `**\*.razor` glob パターンに設定された `Content` 項目が含まれています。 一致するファイルが発行されます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* ASP.NET Core MVC ベース プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ、および発行に関わる表示と操作を標準化できます。
* Razor ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットを含みます。

Razor SDK には、`Include` 属性が `**\*.cshtml` glob パターンに設定された `Content` 項目が含まれています。 一致するファイルが発行されます。

::: moniker-end

## <a name="prerequisites"></a>必須コンポーネント

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>Razor SDK を使用する

ほとんどの Web アプリでは、明示的に Razor SDK を参照する必要がありません。

::: moniker range=">= aspnetcore-3.0"

Razor SDK を使用して Razor ビューまたは Razor Pages を含むクラス ライブラリをビルドするには、Razor クラス ライブラリ (RCL) プロジェクト テンプレートを使用して開始することをお勧めします。 Blazor ( *.razor*) ファイルのビルドに使用する RCL では、少なくとも、[Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) パッケージへの参照が必要です。 RazorRazor ビューまたはページのビルドに使用する RCL ( *.cshtml* ファイル) は、少なくとも `netcoreapp3.0` 以降をターゲットにする必要があり、そのプロジェクト ファイル内には [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)への `FrameworkReference` があります。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor SDK を使用して Razor ビューまたは Razor Pages を含むクラス ライブラリを構築する方法は次のとおりです。

* `Microsoft.NET.Sdk.Razor` の代わりに `Microsoft.NET.Sdk` を使用します。

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* 一般に、Razor Pages や Razor ビューをビルドしてコンパイルするために必要な追加の依存関係を取得するために `Microsoft.AspNetCore.Mvc` へのパッケージ参照が必要とされます。 少なくとも、以下へのパッケージ参照をプロジェクトに追加する必要があります。

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design` パッケージでは、プロジェクトの Razor コンパイル タスクとターゲットが提供されます。

  前述のパッケージは、`Microsoft.AspNetCore.Mvc` に組み込まれています。 次のマークアップは、Razor SDK を使用して ASP.NET Core Razor Pages アプリ用の Razor ファイルをビルドするプロジェクト ファイルを示しています。

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` パッケージと `Microsoft.AspNetCore.Mvc.Razor.Extensions` パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。 ただし、バージョンのない `Microsoft.AspNetCore.App` パッケージのリファレンスでは、最新バージョンの `Microsoft.AspNetCore.Razor.Design` を含まないアプリへのメタパッケージが提供されます。 プロジェクトでは、ビルド時に最新の Razor 用修正プログラムが含まれるように、一貫したバージョンの `Microsoft.AspNetCore.Razor.Design` (または `Microsoft.AspNetCore.Mvc`) を参照する必要があります。 詳細については、次を参照してください。[この GitHub の問題](https://github.com/aspnet/Razor/issues/2553)します。

::: moniker-end

### <a name="properties"></a>プロパティ

Razor の SDK の動作は、プロジェクトをビルドする際に次のプロパティにより制御されます。

* `RazorCompileOnBuild`:`true` の場合、プロジェクトをビルドする際に、Razor アセンブリがコンパイルおよび生成されます。 既定値は `true` です。
* `RazorCompileOnPublish`:`true` の場合、プロジェクトを発行する際に、Razor アセンブリがコンパイルおよび生成されます。 既定値は `true` です。

Razor SDK への入力および出力は、次の表のプロパティと項目を使用して構成されます。

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ASP.NET Core 3.0 以降では、プロジェクト ファイルで `RazorCompileOnBuild` または `RazorCompileOnPublish` の MSBuild プロパティが無効になっている場合、MVC ビューまたは Razor Pages は既定では提供されません。 *.cshtml* ファイルを処理するためにアプリがランタイム コンパイルに依存して場合は、アプリに [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) パッケージへの明示的な参照を追加する必要があります。

::: moniker-end

| 項目 | 説明 |
| ----- | ----------- |
| `RazorGenerate` | コード生成への入力である項目要素 ( *.cshtml* ファイル) です。 |
| `RazorComponent` | Razor コンポーネントのコード生成で入力される項目要素 ( *.razor* ファイル) です。 |
| `RazorCompile` | Razor のコンパイル対象に入力される項目要素 ( *.cs* ファイル) です。 Razor アセンブリに追加でコンパイルするファイルを指定するには、この `ItemGroup` を使用します。 |
| `RazorTargetAssemblyAttribute` | Razor アセンブリ用の属性をコード生成するために使用する項目要素です。 次に例を示します。  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 生成される Razor アセンブリに埋め込みのリソースとして追加される項目要素です。 |

::: moniker range=">= aspnetcore-3.0"

| プロパティ | 説明 |
| -------- | ----------- |
| `RazorTargetName` | Razor によって生成されたアセンブリのファイル名 (拡張子なし)。 |
| `RazorOutputPath` | Razor 出力ディレクトリ。 |
| `RazorCompileToolset` | Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。 有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。 |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 既定値は `true` です。 `true` の場合、プロジェクトのコンテンツとして *web.config* ファイル、*json* ファイル、 *.cshtml* ファイルが含まれます。 `Microsoft.NET.Sdk.Web` を介して参照する場合、*wwwroot* 下のファイルと構成ファイルも含まれます。 |
| `EnableDefaultRazorGenerateItems` | `true` の場合、`RazorGenerate` 項目の `Content` 項目の *.cshtml* ファイルが含まれます。 |
| `GenerateRazorTargetAssemblyInfo` | `true` の場合、`RazorAssemblyAttribute` で指定された属性を含む *.cs* ファイルが生成され、そのファイルがコンパイル出力に含められます。 |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | `true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。 |
| `CopyRazorGenerateFilesToPublishDirectory` | `true` の場合、`RazorGenerate` 項目 ( *.cshtml*) ファイルが発行ディレクトリにコピーされます。 一般に、Razor ファイルがビルド時または発行時にコンパイルに含められる場合、発行済みアプリのためにそれらは必要ありません。 既定値は `false` です。 |
| `PreserveCompilationReferences` | `true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。 一般に、ビルド時または発行時に Razor のコンパイルが行われる場合、発行済みアプリのために参照アセンブリは必要ありません。 発行済みアプリで実行時のコンパイルが必要な場合は、`true` に設定します。 たとえばアプリで、実行時に *.cshtml* ファイルを変更したり、埋め込みビューを使用したりする場合は、値を `true` に設定します。 既定値は `false` です。 |
| `IncludeRazorContentInPack` | `true` の場合、Razor コンテンツ項目 ( *.cshtml* ファイル) はすべて、生成される NuGet パッケージに含めるようマーク付けされます。 既定値は `false` です。 |
| `EmbedRazorGenerateSources` | `true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate ( *.cshtml*) 項目が追加されます。 既定値は `false` です。 |
| `UseRazorBuildServer` | `true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。 既定値は、`UseSharedCompilation` の値です。 |
| `GenerateMvcApplicationPartsAssemblyAttributes` | `true` の場合、アプリケーション パーツの検出を実行するために実行時に MVC によって使用される追加の属性が、SDK によって生成されます。 |
| `DefaultWebContentItemExcludes` | Web または Razor SDK をターゲットとするプロジェクトの `Content` 項目グループから除外する必要がある項目要素の glob パターン |
| `ExcludeConfigFilesFromBuildOutput` | `true` の場合、 *.config* ファイルと *.json* ファイルがビルド出力ディレクトリにコピーされません。 |
| `AddRazorSupportForMvc` | `true` の場合、MVC ビューまたは Razor Pages を含むアプリケーションをビルドするときに必要な MVC 構成のサポートを追加するように、Razor SDK を構成します。 このプロパティは、Web SDK をターゲットとする .NET Core 3.0 以降のプロジェクトには暗黙的に設定されます |
| `RazorLangVersion` | ターゲットとする Razor 言語のバージョン。 |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| プロパティ | 説明 |
| -------- | ----------- |
| `RazorTargetName` | Razor によって生成されたアセンブリのファイル名 (拡張子なし)。 |
| `RazorOutputPath` | Razor 出力ディレクトリ。 |
| `RazorCompileToolset` | Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。 有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。 |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 既定値は `true` です。 `true` の場合、プロジェクトのコンテンツとして *web.config* ファイル、*json* ファイル、 *.cshtml* ファイルが含まれます。 `Microsoft.NET.Sdk.Web` を介して参照する場合、*wwwroot* 下のファイルと構成ファイルも含まれます。 |
| `EnableDefaultRazorGenerateItems` | `true` の場合、`RazorGenerate` 項目の `Content` 項目の *.cshtml* ファイルが含まれます。 |
| `GenerateRazorTargetAssemblyInfo` | `true` の場合、`RazorAssemblyAttribute` で指定された属性を含む *.cs* ファイルが生成され、そのファイルがコンパイル出力に含められます。 |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | `true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。 |
| `CopyRazorGenerateFilesToPublishDirectory` | `true` の場合、`RazorGenerate` 項目 ( *.cshtml*) ファイルが発行ディレクトリにコピーされます。 一般に、Razor ファイルがビルド時または発行時にコンパイルに含められる場合、発行済みアプリのためにそれらは必要ありません。 既定値は `false` です。 |
| `CopyRefAssembliesToPublishDirectory` | `true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。 一般に、ビルド時または発行時に Razor のコンパイルが行われる場合、発行済みアプリのために参照アセンブリは必要ありません。 発行済みアプリで実行時のコンパイルが必要な場合は、`true` に設定します。 たとえばアプリで、実行時に *.cshtml* ファイルを変更したり、埋め込みビューを使用したりする場合は、値を `true` に設定します。 既定値は `false` です。 |
| `IncludeRazorContentInPack` | `true` の場合、Razor コンテンツ項目 ( *.cshtml* ファイル) はすべて、生成される NuGet パッケージに含めるようマーク付けされます。 既定値は `false` です。 |
| `EmbedRazorGenerateSources` | `true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate ( *.cshtml*) 項目が追加されます。 既定値は `false` です。 |
| `UseRazorBuildServer` | `true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。 既定値は、`UseSharedCompilation` の値です。 |
| `GenerateMvcApplicationPartsAssemblyAttributes` | `true` の場合、アプリケーション パーツの検出を実行するために実行時に MVC によって使用される追加の属性が、SDK によって生成されます。 |
| `DefaultWebContentItemExcludes` | Web または Razor SDK をターゲットとするプロジェクトの `Content` 項目グループから除外する必要がある項目要素の glob パターン |
| `ExcludeConfigFilesFromBuildOutput` | `true` の場合、 *.config* ファイルと *.json* ファイルがビルド出力ディレクトリにコピーされません。 |
| `AddRazorSupportForMvc` | `true` の場合、MVC ビューまたは Razor Pages を含むアプリケーションをビルドするときに必要な MVC 構成のサポートを追加するように、Razor SDK を構成します。 このプロパティは、Web SDK をターゲットとする .NET Core 3.0 以降のプロジェクトには暗黙的に設定されます |
| `RazorLangVersion` | ターゲットとする Razor 言語のバージョン。 |

::: moniker-end

プロパティの詳細については、「[MSBuild プロパティ](/visualstudio/msbuild/msbuild-properties)」を参照してください。

### <a name="targets"></a>ターゲット

Razor SDK では、次の 2 つの主要なターゲットが定義されています。

* `RazorGenerate`:コードにより、`RazorGenerate` 項目要素から *.cs* ファイルが生成されます。 このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorGenerateDependsOn` プロパティを使用します。
* `RazorCompile`:生成された *.cs* ファイルを、Razor アセンブリにコンパイルします。 このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorCompileDependsOn` を使用します。
* `RazorComponentGenerate`:コードにより、`RazorComponent` 項目要素のための *.cs* ファイルが生成されます。 このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorComponentGenerateDependsOn` プロパティを使用します。

### <a name="runtime-compilation-of-no-locrazor-views"></a>Razor ビューの実行時のコンパイル

* 既定では、Razor SDK からは、実行時のコンパイルを実行するために必要な参照アセンブリは公開されません。 この結果、アプリケーション モデルが実行時のコンパイルに依存している場合には、コンパイルが失敗します。たとえば、アプリが公開後に埋め込まれたビューを使用したり、ビューを変更したりする場合などです。 `CopyRefAssembliesToPublishDirectory` を `true` に設定して、参照アセンブリの公開を続行します。

* Web アプリの場合は、アプリが `Microsoft.NET.Sdk.Web` SDK をターゲットにしていることを確認します。

## <a name="no-locrazor-language-version"></a>Razor 言語バージョン

`Microsoft.NET.Sdk.Web` SDK をターゲットとする場合、Razor 言語バージョンは、アプリのターゲット フレームワークのバージョンから推論されます。 `Microsoft.NET.Sdk.Razor` SDK をターゲットとするプロジェクトの場合や、推論される値とは異なる Razor 言語バージョンを必要とするまれなアプリの場合は、アプリのプロジェクト ファイルで `<RazorLangVersion>` プロパティを設定することで、バージョンを構成できます。

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razor の言語バージョンは、そのバージョンのビルド対象であったランタイムのバージョンと緊密に統合されています。 そのランタイムに向けて設計されたのではない言語バージョンをターゲットにすることはサポートされておらず、おそらくビルド エラーが発生します。

## <a name="additional-resources"></a>その他の技術情報

* [.NET Core の csproj 形式に追加されたもの](/dotnet/core/tools/csproj)
* [MSBuild プロジェクトの共通項目](/visualstudio/msbuild/common-msbuild-project-items)

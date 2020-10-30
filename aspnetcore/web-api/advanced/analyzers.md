---
title: Web API アナライザーを使用する
author: pranavkm
description: ASP.NET Core MVC Web API アナライザー パッケージについて説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057922"
---
# <a name="use-web-api-analyzers"></a>Web API アナライザーを使用する

ASP.NET Core 2.2 以降には、Web API プロジェクトでの使用を目的とした MVC アナライザー パッケージが用意されています。 アナライザーは、[Web API 規約](xref:web-api/advanced/conventions)の設定中に <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> の注釈が付けられたコントローラーで動作します。

アナライザー パッケージでは、次のようなコントローラーのアクションが通知されます。

* 宣言されていない状態コードを返す。
* 宣言されていない成功の結果を返す。
* 返されない状態コードを文書化する。
* 明示的なモデル検証チェックを含める。

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>アナライザー パッケージを参照する

ASP.NET Core 3.0 以降、アナライザーは .NET Core SDK に含まれています。 プロジェクトでアナライザーを有効にするには、`IncludeOpenAPIAnalyzers` プロパティをプロジェクト ファイルに追加します。

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>パッケージ インストール

次のいずれかの方法を使用して、[Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet パッケージをインストールします。

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**[パッケージ マネージャー コンソール]** ウィンドウから:
  * [ **View** > **その他の Windows** > **パッケージマネージャーコンソール** の表示] にアクセスします。
  * *ApiConventions.csproj* ファイルが存在するディレクトリに移動します。
  * 次のコマンドを実行します。

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [パッケージの *Packages* **Solution Pad** > **追加** ] Solution Pad の [パッケージ] フォルダーを右クリックします。
* [ **パッケージの追加** ] ウィンドウの [ **ソース** ] ドロップダウンを [nuget.org] に設定します。
* 検索ボックスに「Microsoft.AspNetCore.Mvc.Api.Analyzers」と入力します。
* 結果ウィンドウから "Microsoft.AspNetCore.Mvc.Api.Analyzers" パッケージを選択して、[ **パッケージを追加** ] をクリックします。

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**統合ターミナル** からから次のコマンドを実行します。

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

次のコマンドを実行します。

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Web API 規則のアナライザー

OpenAPI ドキュメントには、アクションによって返される可能性のある状態コードと応答の種類が含まれます。 ASP.NET Core MVC では、<xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> や <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> などの属性がアクションの文書化に使用されます。 Web API の文書化の詳細については、<xref:tutorials/web-api-help-pages-using-swagger> を参照してください。

パッケージのいずれかのアナライザーが、<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> の注釈が付けられたコントローラーを検査し、応答全体を文書化していないアクションを特定します。 次の例を確認してください。

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

前のアクションでは、HTTP 200 の成功の戻り値の型は文書化していますが、HTTP 404 のエラー状態コードは文書化していません。 アナライザーは、HTTP 404 状態コードの文書化の不備を警告として報告します。 問題を解決するためのオプションが提供されます。

![警告を報告するアナライザー](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>その他のリソース

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>

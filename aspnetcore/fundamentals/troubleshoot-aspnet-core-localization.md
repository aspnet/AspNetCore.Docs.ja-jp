---
title: ASP.NET Core のローカライズに関するトラブルシューティング
author: hishamco
description: ASP.NET Core アプリのローカライズに関する問題を診断する方法について説明します。
ms.author: riande
ms.date: 01/24/2019
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
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 995db4c8c9d0c0f1f77b1fd3665e707975406a7f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053619"
---
# <a name="troubleshoot-aspnet-core-localization"></a>ASP.NET Core のローカライズに関するトラブルシューティング

作成者: [Hisham Bin Ateya](https://github.com/hishamco)

この記事では、ASP.NET Core アプリのローカライズに関する問題を診断する方法の手順を紹介します。

## <a name="localization-configuration-issues"></a>ローカライズの構成に関する問題

**ローカライズ ミドルウェアの順序**  
ローカライズ ミドルウェアが正しい順序で設定されていないため、アプリがローカライズされない場合があります。

この問題を解決するには、確実にローカライズ ミドルウェアを登録してから、MVC ミドルウェアを登録します。 それ以外の場合は、ローカライズ ミドルウェアは適用されません。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**ローカライズ リソースのパスが見つかりません**

**RequestCultureProvider でサポートされているカルチャが一度登録したものと一致しません**  

## <a name="resource-file-naming-issues"></a>リソース ファイルの名前付けに関する問題

ASP.NET Core では、ローカライズ リソース ファイルの名前付けに対するルールとガイドラインがあらかじめ定義されています。詳細については、[こちら](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)をご覧ください。

## <a name="missing-resources"></a>リソースの不足

リソースが見つからない一般的な原因は次のとおりです。

- `resx` ファイルまたはローカライズ ツールの要求のいずれかで、ファイル名のスペルが間違っています。
- 一部の言語の `resx` でリソースが不足しており、その他の言語に存在しています。
- 引き続き問題が発生する場合は、不足しているリソースの詳細について、ローカライズのログ メッセージ (`Debug` ログ レベル) を確認してください。

_**ヒント:** `CookieRequestCultureProvider` を使用している場合、ローカライズの cookie 値内のカルチャで単一引用符が使用されないことを確認します。たとえば、`c='en-UK'|uic='en-US'` は無効な cookie 値ですが、`c=en-UK|uic=en-US` は有効です。_

## <a name="resources--class-libraries-issues"></a>リソースとクラス ライブラリに関する問題

既定で、ASP.NET Core では、クラス ライブラリで [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1) を使ってリソース ファイルを検索できる方法を提供します。

クラス ライブラリに関する一般的な問題には次のようなものがあります。
- クラス ライブラリでの `ResourceLocationAttribute` の不足によって、`ResourceManagerStringLocalizerFactory` でリソースを検出することができない。
- リソース ファイルの名前付け。 詳細については、「[リソース ファイルの名前付けに関する問題](#resource-file-naming-issues)」を参照してください。
- クラス ライブラリのルート名前空間の変更。 詳細については、「[ルート名前空間に関する問題](#root-namespace-issues)」セクションを参照してください。

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider が正しく動作しない

`RequestLocalizationOptions` クラスには、既定のプロバイダーが 3 つあります。

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) によって、ローカライズ カルチャをご利用のアプリ内で指定する方法をカスタマイズできます。 `CustomRequestCultureProvider` は、既定のプロバイダーが要件に合わないときに使用されます。

- カスタム プロバイダーが適切に動作しない一般的な理由は、これが `RequestCultureProviders` リストの最初のプロバイダーではないことです。 この問題を解決するには、次の操作を行います。

- 次のように、`RequestCultureProviders` リストの 0 の位置にカスタム プロバイダーを挿入します。

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- `AddInitialRequestCultureProvider` 拡張メソッドを使用して、初期プロバイダーとしてカスタム プロバイダーを設定します。

## <a name="root-namespace-issues"></a>ルート名前空間に関する問題

アセンブリのルート名前空間がアセンブリ名と異なる場合、既定ではローカライズは動作しません。 [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) を使用するこの問題を回避する方法は、[ここ](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)で説明されています。

> [!WARNING]
> これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。 たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。 

## <a name="resources--build-action"></a>リソースとビルド アクション

ローカライズのためにリソース ファイルを使用する場合、適切なビルド アクションが存在することが重要です。 これらは **埋め込みリソース** である必要があり、それ以外の場合、`ResourceStringLocalizer` でこれらのリソースを見つけることはできません。

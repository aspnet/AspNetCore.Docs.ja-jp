---
title: ASP.NET Core Blazor 用のトリマーを構成する
author: guardrex
description: Blazor アプリをビルドする際に中間言語 (IL) リンカー (トリマー) を制御する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055803"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>ASP.NET Core Blazor 用のトリマーを構成する

作成者: [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly では、発行された出力のサイズを縮小するために、[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) トリミングが実行されます。

アプリをトリミングするとサイズが最適化されますが、悪影響を及ぼす可能性があります。 リフレクションや関連する動的機能を使用するアプリは、トリミングされると中断する可能性があります。トリマーが、動的な動作を認識せず、通常、実行時にリフレクションで必要とされる型を特定できないからです。 そのようなアプリをトリミングするには、コードのリフレクション、およびアプリが依存しているパッケージまたはフレームワークのリフレクションで必要なすべての型を、トリマーに通知する必要があります。

トリミングされたアプリが展開後に正しく動作するには、発行された出力を、開発中に頻繁にテストすることが重要です。

.NET アプリのトリミングを無効にするには、アプリのプロジェクト ファイルで、`PublishTrimmed` MSBuild プロパティを `false` に設定します。

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
トリマーを構成するためのその他のオプションについては、[トリミングのオプション](/dotnet/core/deploying/trimming-options)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [自己完結型の展開と実行可能ファイルのトリミング](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>

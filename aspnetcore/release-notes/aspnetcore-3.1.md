---
title: ASP.NET Core 3.1 の新機能
author: rick-anderson
description: ASP.NET Core 3.1 の新機能について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: dd012a2104f574865ed577ab3c0e81dc9cc9596d
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431018"
---
# <a name="whats-new-in-aspnet-core-31"></a>ASP.NET Core 3.1 の新機能

この記事では、ASP.NET Core 3.1 の最も大きな変更点について説明します。また、関連するドキュメントへのリンクも示します。

## <a name="partial-class-support-for-no-locrazor-components"></a>Razor コンポーネントの部分クラスのサポート

Razor コンポーネントが部分クラスとして生成されるようになりました。 Razor コンポーネントのコードは、単一ファイル内のコンポーネントのすべてのコードを定義するのではなく、部分クラスとして定義された分離コード ファイルを使用して記述できます。 詳細については、「[部分クラスのサポート](xref:blazor/components/index#partial-class-support)」を参照してください。

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor コンポーネント タグ ヘルパーと最上位レベルのコンポーネントへのパラメーターの引き渡し

ASP.NET Core 3.0 がインストールされた Blazor では、コンポーネントは HTML ヘルパー (`Html.RenderComponentAsync`) を使用してページとビューにレンダリングされていました。 ASP.NET Core 3.1 では、新しいコンポーネント タグ ヘルパーを使用して、ページまたはビューからコンポーネントがレンダリングされます。

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

HTML ヘルパーは ASP.NET Core 3.1 でも引き続きサポートされていますが、コンポーネント タグ ヘルパーをお勧めします。

初回のレンダリング時に、Blazor Server アプリで最上位レベルのコンポーネントにパラメーターを渡すことができるようになりました。 以前は、最上位レベルのコンポーネントにパラメーターを渡すには、[RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static) を使用するしかありませんでした。 このリリースでは、[RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) と [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) の両方がサポートされています。 指定されたパラメーター値はすべて JSON としてシリアル化され、初回の応答に含まれます。

たとえば、インクリメント量 (`IncrementAmount`) を使用して `Counter` コンポーネントを事前レンダリングする場合は、次のようになります。

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

詳細については、[コンポーネントの Razor Pages と MVC アプリへの統合](xref:blazor/components/prerendering-and-integration)に関するページを参照してください。

## <a name="support-for-shared-queues-in-httpsys"></a>Http.sys での共有キューのサポート

[Http.sys](xref:fundamentals/servers/httpsys) では、匿名の要求キューの作成がサポートされています。 ASP.NET Core 3.1 では、既存の名前付き HTTP.sys 要求キューに作成またはアタッチする機能が追加されています。 既存の名前付き HTTP.sys 要求キューに作成またはアタッチすると、キューを所有する HTTP.sys コントローラー プロセスがリスナー プロセスから独立しているシナリオが可能になります。 このような独立によって、リスナー プロセスの再起動の間に既存の接続とエンキューされた要求を保持することができます。

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a>SameSite cookie の重大な変更

SameSite cookie の動作が、今後のブラウザーの変更を反映するように変更されました。 これは、AzureAd、OpenIdConnect、WsFederation などの認証シナリオに影響を与える可能性があります。 詳細については、「<xref:security/samesite>」を参照してください。

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a>Blazor アプリのイベントに対する既定のアクションを回避する

イベントに対する既定のアクションを回避するには、`@on{EVENT}:preventDefault` ディレクティブ属性を使用します。 次の例では、テキスト ボックスにキーの文字を表示する既定のアクションが回避されます。

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

詳細については、「[既定のアクションを禁止する](xref:blazor/components/event-handling#prevent-default-actions)」を参照してください。

## <a name="stop-event-propagation-in-no-locblazor-apps"></a>Blazor アプリでのイベント伝達を停止する

イベント伝達を停止するには、`@on{EVENT}:stopPropagation` ディレクティブ属性を使用します。 次の例では、チェック ボックスをオンにすると、子 `<div>` からのクリック イベントが親 `<div>` に伝達されなくなります。

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

詳細については、「[イベントの伝達の停止](xref:blazor/components/event-handling#stop-event-propagation)」を参照してください。

## <a name="detailed-errors-during-no-locblazor-app-development"></a>Blazor アプリの開発中の詳細なエラー

開発中に Blazor アプリが正常に機能していない場合、アプリからの詳細なエラー情報を受け取ることで、問題のトラブルシューティングと修正に役立ちます。 エラーが発生すると Blazor アプリによって画面の下部に金色のバーが表示されます。

* 開発中は、金色のバーによってブラウザー コンソールが表示され、そこで例外を確認できます。
* 実稼働環境では、金色のバーによって、エラーが発生したことがユーザーに通知され、ブラウザーの更新が推奨されます。

詳細については、「[開発中の詳細なエラー](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)」を参照してください。

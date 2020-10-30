---
title: ASP.NET Core のスクリプト タグ ヘルパー
author: rick-anderson
ms.author: riande
description: ASP.NET Core スクリプト タグ ヘルパーの属性と、HTML スクリプト タグの動作拡張時の各属性の役割を示します。
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- appsettings.json
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053502"
---
# <a name="script-tag-helper-in-aspnet-core"></a>ASP.NET Core のスクリプト タグ ヘルパー

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[スクリプト タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)によって、プライマリまたはフォール バック スクリプト ファイルへのリンクが生成されます。 通常、プライマリ スクリプト ファイルは [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN) 上にあります。

[!INCLUDE[](~/includes/cdn.md)]

スクリプト タグ ヘルパーを使用すると、スクリプト ファイルの CDN と、CDN が使用できない場合のフォールバックを指定できます。 スクリプト タグ ヘルパーによって、ローカル ホスティングの堅牢性が CDN のパフォーマンスの利点にもたらされます。

次の Razor マークアップは、 `script` フォールバックを持つ要素を示しています。

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

CDN スクリプトの読み込みを延期する場合、`<script>` 要素の [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) 属性を使用しないでください。 スクリプト タグ ヘルパーでは、[asp-fallback-test](#asp-fallback-test) 式を直ちに実行する JavaScript がレンダリングされます。 CDN スクリプトの読み込みが遅延されている場合、式は失敗します。

## <a name="commonly-used-script-tag-helper-attributes"></a>一般的に使用されるスクリプト タグ ヘルパー属性

スクリプト タグ ヘルパーのすべての属性、プロパティ、メソッドについては、[スクリプト タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)に関するページを参照してください。

### <a name="asp-fallback-test"></a>asp-fallback-test

フォールバック テストに使用するプライマリ スクリプトで定義されているスクリプト メソッド。 詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>」を参照してください。

### <a name="asp-fallback-src"></a>asp-fallback-src

プライマリ側でエラーが発生した場合にフォールバックするスクリプト タグの URL。 詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>

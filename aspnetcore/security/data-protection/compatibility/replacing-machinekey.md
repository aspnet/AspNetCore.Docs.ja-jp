---
title: ASP.NET Core の ASP.NET machineKey を置き換える
author: rick-anderson
description: ASP.NET の machineKey を置き換えて、新しいより安全なデータ保護システムを使用できるようにする方法について説明します。
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053060"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>ASP.NET Core の ASP.NET machineKey を置き換える

<a name="compatibility-replacing-machinekey"></a>

`<machineKey>`ASP.NET の要素の実装[は置き換えることが](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)できます。 これにより、ASP.NET 暗号化ルーチンのほとんどの呼び出しは、新しいデータ保護システムを含む代替のデータ保護メカニズムを通じてルーティングされます。

## <a name="package-installation"></a>パッケージ インストール

> [!NOTE]
> 新しいデータ保護システムは、.NET 4.5.1 以降を対象とする既存の ASP.NET アプリケーションにのみインストールできます。 アプリケーションが .NET 4.5 以下を対象としている場合、インストールは失敗します。

新しいデータ保護システムを既存の ASP.NET 4.5.1 + プロジェクトにインストールするには、パッケージ Microsoft.AspNetCore.DataProtection.SystemWeb にインストールします。 これにより、 [既定の構成](xref:security/data-protection/configuration/default-settings) 設定を使用してデータ保護システムがインスタンス化されます。

パッケージをインストールすると、 *Web.config* に行が挿入されます。これにより、フォーム認証、ビューステート、および ASP.NET の呼び出しなど、 [ほとんどの暗号化操作](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)に使用するように指示されます。 挿入された行は次のように読み取られます。

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> 新しいデータ保護システムがアクティブであるかどうかを確認するには、のようなフィールド `__VIEWSTATE` を調べます。次の例のように、"CfDJ8" で始まる必要があります。 "CfDJ8" は、データ保護システムによって保護されているペイロードを識別するマジック "09 F0 C9 F0" ヘッダーの base64 表現です。

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>パッケージの構成

データ保護システムは、既定のゼロセットアップ構成を使用してインスタンス化されます。 ただし、既定ではキーはローカルファイルシステムに保存されるため、ファームに配置されているアプリケーションでは機能しません。 これを解決するには、DataProtectionStartup にサブクラスを作成し、その ConfigureServices メソッドをオーバーライドする型を作成して、構成を提供します。

次に示すのは、キーが永続化される場所と保存時に暗号化する方法の両方を構成したカスタムデータ保護のスタートアップの種類の例です。 また、独自のアプリケーション名を指定することで、既定のアプリ分離ポリシーをオーバーライドします。

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> `<machineKey applicationName="my-app" ... />`SetApplicationName への明示的な呼び出しの代わりにを使用することもできます。 これは、構成するすべてのユーザーがアプリケーション名を設定していた場合に、開発者が DataProtectionStartup 派生型を作成する必要がないようにするための便利なメカニズムです。

このカスタム構成を有効にするには、[Web.config に戻り、 `<appSettings>` パッケージのインストールによって構成ファイルに追加された要素を探します。 次のマークアップが表示されます。

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

空の値に、先ほど作成した DataProtectionStartup 派生型のアセンブリ修飾名を入力します。 アプリケーションの名前が DataProtectionDemo の場合、これは次のようになります。

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

これで、新しく構成されたデータ保護システムをアプリケーション内で使用する準備ができました。

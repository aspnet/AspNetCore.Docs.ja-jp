---
title: 認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: 認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a4f3234aa4b4b02244d17615a9033db3094d3580
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024783"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する

作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)

*Azure Active Directory (AAD) および Azure Active Directory B2C (AAD B2C) については、このトピックのガイダンスに従わず、この目次ノードの AAD および AAD B2C のトピックを参照してください。*

[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ライブラリを使用する [スタンドアロン Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成するには、選択したツールのガイダンスに従ってください。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:

1. **[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor WebAssembly アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。

1. ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納するには、 **[アプリ内のストア ユーザー アカウント]** オプションを使用して、 **[個人のユーザー アカウント]** を選択します。

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

空のフォルダーに、認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成します。 ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納するには、`-au|--auth` オプションを使用して `Individual` 認証メカニズムを指定します。

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| プレースホルダー  | 例        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。

詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:

1. **[新しい Blazor WebAssembly アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。

1. ASP.NET Core の [Identity](xref:security/authentication/identity) を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。

---

## <a name="authentication-package"></a>認証パッケージ

個人のユーザー アカウントを使用するようにアプリを作成すると、そのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージのパッケージ参照を自動的に受け取ります。 このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。

## <a name="authentication-service-support"></a>認証サービスのサポート

ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。 このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

構成は `wwwroot/appsettings.json` ファイルによって提供されます。

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

スタンドアロン アプリの認証サポートは、OpenID Connect (OIDC) を使用して提供されます。 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> メソッドでは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。 アプリの構成に必要な値は、OIDC に準拠する IP から取得できます。 アプリを登録するときに値を取得します。これは通常、オンライン ポータルで実行されます。

## <a name="access-token-scopes"></a>アクセス トークン スコープ

Blazor WebAssembly テンプレートでは、`openid` と `profile` の既定のスコープが自動的に構成されます。

Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。 サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> の既定のトークン スコープにスコープを追加します。

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。

* [追加のアクセス トークンを要求する](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [送信要求にトークンを添付する](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>インポート ファイル

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>インデックス ページ

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a>アプリ コンポーネント

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay コンポーネント

`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。

* 認証されたユーザーの場合:
  * 現在のユーザー名が表示されます。
  * アプリからログアウトするためのボタンが用意されます。
* 匿名ユーザーの場合は、ログインするオプションが用意されます。

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/security/webassembly/additional-scenarios>
* [セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:host-and-deploy/proxy-load-balancer>: 次のガイダンスが含まれます。
  * 転送されたヘッダー ミドルウェアを使用した、プロキシ サーバーと内部ネットワークの間での HTTPS スキーム情報の保持。
  * 追加のシナリオとユース ケース (手動スキーム構成を含む)、正しい要求ルーティングのための要求パスの変更、Linux および IIS 以外のリバース プロキシのための要求スキームの転送。

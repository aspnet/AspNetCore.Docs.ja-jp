---
title: ASP.NET Core に HTTPS を適用する
author: rick-anderson
description: ASP.NET Core web アプリで HTTPS/TLS を要求する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: 3277fda0d1dcb5121a2172b3fc1e4869ed6f8430
ms.sourcegitcommit: fc4cce2767e34f81079510f34bd54e9d0aa86497
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592870"
---
# <a name="enforce-https-in-aspnet-core"></a>ASP.NET Core に HTTPS を適用する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

このドキュメントでは、次の方法について説明します。

* すべての要求に HTTPS が必要です。
* すべての HTTP 要求を HTTPS にリダイレクトします。

API がないと、クライアントが最初の要求で機微なデータを送信できなくなる可能性があります。

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>API プロジェクト
>
> 機密情報を受け取る Web Api では [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **を使用しないでください。** `RequireHttpsAttribute` HTTP 状態コードを使用して、HTTP から HTTPS にブラウザーをリダイレクトします。 API クライアントは、HTTP から HTTPS へのリダイレクトについて理解したり、従うことはできません。 このようなクライアントは、HTTP 経由で情報を送信する場合があります。 Web Api は次のいずれかを実行する必要があります。
>
> * HTTP でリッスンしません。
> * 状態コード 400 (Bad Request) で接続を閉じ、要求を処理しません。
>
> ## <a name="hsts-and-api-projects"></a>HSTS と API プロジェクト
>
> HSTS は一般にブラウザー専用の命令であるため、既定の API プロジェクトには [Hsts](#hsts) は含まれません。 電話やデスクトップアプリなどの他の呼び出し元は、命令に従い **ません** 。 ブラウザー内でも、HTTP 経由の API に対して認証された単一の呼び出しにより、セキュリティで保護されていないネットワークに対するリスクが生じます。 セキュリティで保護された方法は、HTTPS 経由でのみリッスンして応答するように API プロジェクトを構成することです。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>API プロジェクト
>
> 機密情報を受け取る Web Api では [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **を使用しないでください。** `RequireHttpsAttribute` HTTP 状態コードを使用して、HTTP から HTTPS にブラウザーをリダイレクトします。 API クライアントは、HTTP から HTTPS へのリダイレクトについて理解したり、従うことはできません。 このようなクライアントは、HTTP 経由で情報を送信する場合があります。 Web Api は次のいずれかを実行する必要があります。
>
> * HTTP でリッスンしません。
> * 状態コード 400 (Bad Request) で接続を閉じ、要求を処理しません。

::: moniker-end

## <a name="require-https"></a>HTTPS を必須にする

Web アプリの運用 ASP.NET Core では次のものを使用することをお勧めします。

* <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>HTTP 要求を https にリダイレクトする Https リダイレクトミドルウェア ()。
* HSTS ミドルウェア ([Usehsts](#http-strict-transport-security-protocol-hsts)) を介して、HTTP Strict Transport Security Protocol (hsts) ヘッダーをクライアントに送信します。

> [!NOTE]
> リバースプロキシ構成で展開されたアプリは、プロキシが接続セキュリティ (HTTPS) を処理できるようにします。 プロキシが HTTPS リダイレクトも処理する場合は、HTTPS リダイレクトミドルウェアを使用する必要はありません。 また、プロキシサーバーが HSTS ヘッダーの書き込みも処理する場合 ( [IIS 10.0 (1709) 以降のネイティブ HSTS のサポート](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)など)、アプリでは Hsts ミドルウェアは必要ありません。 詳細については、「 [プロジェクト作成時の HTTPS/HSTS のオプトアウト](#opt-out-of-httpshsts-on-project-creation)」を参照してください。

### <a name="usehttpsredirection"></a>UseHttpsRedirection

次のコードは、 `UseHttpsRedirection` クラス内でを呼び出し `Startup` ます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

前の強調表示されているコード:

* 既定の [HttpsRedirectionOptions statuscode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)) を使用します。
* [](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) `ASPNETCORE_HTTPS_PORT` 環境変数または[iserverHttpsRedirectionOptions 機能](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)でオーバーライドされない限り、既定の (null) を使用します。

永続的なリダイレクトではなく、一時的なリダイレクトを使用することをお勧めします。 リンク キャッシュを使用すると、開発環境で不安定な動作が発生する可能性があります。 アプリが非開発環境にあるときに永続的なリダイレクト状態コードを送信する場合は、「運用環境 [で永続的なリダイレクトを構成](#configure-permanent-redirects-in-production) する」セクションを参照してください。 [Hsts](#http-strict-transport-security-protocol-hsts)を使用して、セキュリティで保護されたリソース要求のみをアプリケーションに送信する (運用環境のみ) ことをクライアントに通知することをお勧めします。

### <a name="port-configuration"></a>ポート構成

ミドルウェアがセキュリティで保護されていない要求を HTTPS にリダイレクトするには、ポートが使用可能である必要があります。 使用可能なポートがない場合:

* HTTPS へのリダイレクトは行われません。
* ミドルウェアは、"リダイレクト用の https ポートを特定できませんでした" という警告をログに記録します。

次のいずれかの方法を使用して、HTTPS ポートを指定します。

* [HttpsRedirectionOptions](#options)を設定します。

::: moniker range=">= aspnetcore-3.0"

* `https_port`[ホスト設定](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port)を設定します。

  * ホスト構成。
  * 環境変数を設定し `ASPNETCORE_HTTPS_PORT` ます。
  * 最上位レベルのエントリをに追加し *appsettings.json* ます。

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* [ASPNETCORE_URLS 環境変数](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls)を使用して、セキュリティで保護されたスキームのポートを指定します。 環境変数によってサーバーが構成されます。 ミドルウェアは、を使用して HTTPS ポートを間接的に検出し <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ます。 リバースプロキシの展開では、この方法は使用できません。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* `https_port`[ホスト設定](xref:fundamentals/host/web-host#https-port)を設定します。

  * ホスト構成。
  * 環境変数を設定し `ASPNETCORE_HTTPS_PORT` ます。
  * 最上位レベルのエントリをに追加し *appsettings.json* ます。

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* [ASPNETCORE_URLS 環境変数](xref:fundamentals/host/web-host#server-urls)を使用して、セキュリティで保護されたスキームのポートを指定します。 環境変数によってサーバーが構成されます。 ミドルウェアは、を使用して HTTPS ポートを間接的に検出し <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ます。 リバースプロキシの展開では、この方法は使用できません。

::: moniker-end

* 開発では、 *launchsettings.js* で HTTPS URL を設定します。 IIS Express が使用されている場合は、HTTPS を有効にします。

* [Kestrel](xref:fundamentals/servers/kestrel)サーバーまたは[HTTP.sys](xref:fundamentals/servers/httpsys)サーバーの公開エッジデプロイの HTTPS URL エンドポイントを構成します。 アプリケーションで使用される **HTTPS ポートは1つ** だけです。 ミドルウェアは、を使用してポートを検出し <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ます。

> [!NOTE]
> リバースプロキシ構成でアプリを実行する場合、は <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 使用できません。 このセクションで説明する他の方法のいずれかを使用して、ポートを設定します。

### <a name="edge-deployments"></a>Edge の展開 

Kestrel または HTTP.sys が公開エッジサーバーとして使用されている場合、Kestrel または HTTP.sys が両方でリッスンするように構成されている必要があります。

* クライアントがリダイレクトされるセキュリティで保護されたポート (通常、運用環境では443、開発では 5001)。
* セキュリティで保護されていないポート (通常、運用環境では80、開発では 5000)。

セキュリティで保護されていない要求をアプリケーションが受信し、セキュリティで保護されたポートにクライアントをリダイレクトするには、セキュリティで保護されていないポートにクライアントがアクセスできる必要があります。

詳細については、「 [Kestrel エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration) 」または「」を参照してください <xref:fundamentals/servers/httpsys> 。

### <a name="deployment-scenarios"></a>デプロイメント シナリオ

クライアントとサーバーの間のファイアウォールでは、トラフィック用の通信ポートも開いている必要があります。

リバースプロキシ構成で要求が転送される場合は、HTTPS リダイレクトミドルウェアを呼び出す前に、転送された [ヘッダーミドルウェア](xref:host-and-deploy/proxy-load-balancer) を使用します。 転送されたヘッダーミドルウェアは、 `Request.Scheme` ヘッダーを使用してを更新し `X-Forwarded-Proto` ます。 ミドルウェアは、リダイレクト Uri とその他のセキュリティポリシーを正しく動作させることを許可します。 転送ヘッダーミドルウェアが使用されていない場合、バックエンドアプリは正しいスキームを受信せず、リダイレクトループで終了する可能性があります。 一般的なエンドユーザーエラーメッセージは、リダイレクトされた回数が多すぎることを示しています。

Azure App Service にデプロイする場合は、 [「チュートリアル: 既存のカスタム SSL 証明書を Azure Web Apps にバインドする](/azure/app-service/app-service-web-tutorial-custom-ssl)」のガイダンスに従ってください。

### <a name="options"></a>Options

次の強調表示されたコードは、 [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) を呼び出してミドルウェアオプションを構成します。


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


を呼び出す `AddHttpsRedirection` 必要があるのは、 `HttpsPort` またはの値を変更することだけです `RedirectStatusCode` 。

前の強調表示されているコード:

* HttpsRedirectionOptions をに設定し [ます](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) 。 <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> これは既定値です。 への割り当てには、クラスのフィールドを使用し <xref:Microsoft.AspNetCore.Http.StatusCodes> `RedirectStatusCode` ます。
* HTTPS ポートを5001に設定します。

#### <a name="configure-permanent-redirects-in-production"></a>運用環境での永続的なリダイレクトの構成

ミドルウェアは、既定ですべてのリダイレクトを使用して [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) を送信します。 アプリが非開発環境にあるときに永続的なリダイレクト状態コードを送信する場合は、非開発環境の条件付きチェックでミドルウェアオプションの構成をラップします。

::: moniker range=">= aspnetcore-3.0"

*Startup.cs* でサービスを構成する場合:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

*Startup.cs* でサービスを構成する場合:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>HTTPS リダイレクトミドルウェアの代替アプローチ

HTTPS リダイレクトミドルウェア () を使用する代わりに、 `UseHttpsRedirection` URL リライトミドルウェア () を使用することも `AddRedirectToHttps` できます。 `AddRedirectToHttps` また、リダイレクトの実行時に状態コードとポートを設定することもできます。 詳細については、「 [URL リライトミドルウェア](xref:fundamentals/url-rewriting)」を参照してください。

追加のリダイレクト規則を必要とせずに HTTPS にリダイレクトする場合は、このトピックで説明する HTTPS リダイレクトミドルウェア () を使用することをお勧めし `UseHttpsRedirection` ます。

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>HTTP Strict Transport Security Protocol (HSTS)

[Owasp](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)では、 [HTTP Strict Transport Security (hsts)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html)は、応答ヘッダーを使用して web アプリによって指定されるオプトインセキュリティ拡張機能です。 [HSTS をサポートするブラウザー](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support)がこのヘッダーを受け取ると、次のようになります。

* ブラウザーには、HTTP 経由の通信を送信できないようにするドメインの構成が格納されます。 ブラウザーは、HTTPS 経由のすべての通信を強制的に実行します。
* ブラウザーによって、ユーザーが信頼されていない証明書や無効な証明書を使用できなくなります。 ブラウザーは、ユーザーがこのような証明書を一時的に信頼できるようにするプロンプトを無効にします。

HSTS はクライアントによって適用されるため、いくつかの制限があります。

* クライアントは HSTS をサポートしている必要があります。
* HSTS で HSTS ポリシーを確立するには、少なくとも1つの HTTPS 要求が必要です。
* アプリケーションは、すべての HTTP 要求を確認し、HTTP 要求をリダイレクトまたは拒否する必要があります。

ASP.NET Core 2.1 以降では、拡張メソッドを使用して HSTS を実装し `UseHsts` ます。 次のコードは、 `UseHsts` アプリが [開発モード](xref:fundamentals/environments)でない場合にを呼び出します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts` HSTS の設定はブラウザーによって高度なキャッシュが可能であるため、開発では推奨されません。 既定では、は `UseHsts` ローカルループバックアドレスを除外します。

初めて HTTPS を実装する運用環境では、メソッドのいずれかを使用して、初期の [HstsOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) を小さい値に設定します <xref:System.TimeSpan> 。 HTTPS インフラストラクチャを HTTP に戻す必要がある場合に備えて、値を時間から1日以内に設定します。 HTTPS 構成の持続性を確認したら、HSTS の値を増やします。 `max-age` 一般的に使用される値は1年です。

コード例を次に示します。


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* ヘッダーのプリロードパラメーターを設定し `Strict-Transport-Security` ます。 プリロードは [RFC hsts 仕様](https://tools.ietf.org/html/rfc6797)の一部ではありませんが、web ブラウザーでは、新規インストール時に hsts サイトを事前に読み込むことがサポートされています。 詳細については、[https://hstspreload.org/](https://hstspreload.org/) をご覧ください。
* HSTS ポリシーをホストサブドメインに適用する [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2)を有効にします。
* `max-age`ヘッダーのパラメーターを明示的に `Strict-Transport-Security` 60 日に設定します。 設定されていない場合、既定値は30日です。 詳細については、「 [最長有効期間」ディレクティブ](https://tools.ietf.org/html/rfc6797#section-6.1.1)を参照してください。
* `example.com`除外するホストの一覧にを追加します。

`UseHsts` 次のループバックホストを除外します。

* `localhost` : IPv4 ループバックアドレス。
* `127.0.0.1` : IPv4 ループバックアドレス。
* `[::1]` : IPv6 ループバックアドレス。

## <a name="opt-out-of-httpshsts-on-project-creation"></a>プロジェクト作成時の HTTPS/HSTS のオプトアウト

接続セキュリティがネットワークの公開エッジで処理されるバックエンドサービスのシナリオによっては、各ノードで接続セキュリティを構成する必要がない場合があります。 Visual Studio のテンプレートまたは [dotnet new](/dotnet/core/tools/dotnet-new) コマンドから生成された Web アプリでは、 [HTTPS リダイレクト](#require-https) と [hsts](#http-strict-transport-security-protocol-hsts)が有効になります。 これらのシナリオを必要としないデプロイでは、テンプレートからアプリを作成するときに HTTPS/HSTS をオプトアウトできます。

HTTPS/HSTS をオプトアウトするには、次のようにします。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[ **HTTPS 用に構成** ] チェックボックスをオフにします。

::: moniker range=">= aspnetcore-3.0"

![[HTTPS 用に構成] チェックボックスがオフになっている新しい ASP.NET Core Web アプリケーション] ダイアログボックスが表示されます。](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![[HTTPS 用に構成] チェックボックスがオフになっている新しい ASP.NET Core Web アプリケーション] ダイアログボックスが表示されます。](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli) 

`--no-https` オプションを使用します。 次に例を示します。

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Windows および macOS で ASP.NET Core HTTPS 開発証明書を信頼する

.NET Core SDK には、HTTPS 開発証明書が含まれています。 証明書は、最初の実行エクスペリエンスの一部としてインストールされます。 たとえば、は `dotnet --info` 次の出力のバリエーションを生成します。

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

.NET Core SDK をインストールすると、ローカル ユーザーの証明書ストアに ASP.NET Core HTTPS 開発証明書がインストールされます。 証明書はインストールされていますが、信頼されていません。 証明書を信頼するには、1回限りの手順を実行して dotnet ツールを実行し `dev-certs` ます。

```dotnetcli
dotnet dev-certs https --trust
```

次のコマンドにより、`dev-certs` ツールに関するヘルプが表示されます。

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Docker 用の開発者証明書を設定する方法

[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore.Docs/issues/6199)を参照してください。

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a>Linux で HTTPS 証明書を信頼する

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

Linux の手順については、ディストリビューションのドキュメントを参照してください。

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Windows Subsystem for Linux からの HTTPS 証明書を信頼する

Windows Subsystem for Linux (WSL) は、HTTPS 自己署名証明書を生成します。WSL 証明書を信頼するように Windows 証明書ストアを構成するには、次のようにします。

* WSL によって生成された証明書をエクスポートするには、次のコマンドを実行します。

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* WSL ウィンドウで、次のコマンドを実行します。

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  上記のコマンドは、Linux が Windows の信頼された証明書を使用するように環境変数を設定します。

## <a name="troubleshoot-certificate-problems"></a>証明書に関する問題のトラブルシューティング

このセクションでは ASP.NET Core の HTTPS 開発証明書がインストールされ、 [信頼](#trust)されているが、証明書が信頼されていないことを示すブラウザーの警告が表示される場合に役立つ情報を提供します。 ASP.NET Core HTTPS 開発証明書は [Kestrel](xref:fundamentals/servers/kestrel)によって使用されます。

IIS Express 証明書を修復するには、 [この Stackoverflow](https://stackoverflow.com/a/20048613/502537) の問題を参照してください。

### <a name="all-platforms---certificate-not-trusted"></a>すべてのプラットフォーム-信頼されていない証明書

次のコマンドを実行します。

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

開いているすべてのブラウザーインスタンスを閉じます。 新しいブラウザーウィンドウを開いてアプリを開きます。 証明書の信頼は、ブラウザーによってキャッシュされます。

上記のコマンドは、ほとんどのブラウザーの信頼の問題を解決します。 ブラウザーがまだ証明書を信頼していない場合は、次のプラットフォーム固有の提案に従ってください。

### <a name="docker---certificate-not-trusted"></a>Docker-信頼されていない証明書

* *C:\Users \{ USER} \AppData\Roaming\ASP.NET\Https* フォルダーを削除します。
* ソリューションをクリーンアップします。 *bin* フォルダーと *obj* フォルダーを削除します。
* 開発ツールを再起動します。 たとえば、Visual Studio、Visual Studio Code、Visual Studio for Mac などです。

### <a name="windows---certificate-not-trusted"></a>Windows-信頼されていない証明書

* 証明書ストア内の証明書を確認します。 `localhost`との両方に、 `ASP.NET Core HTTPS development certificate` フレンドリ名を持つ証明書が存在する必要があります。 `Current User > Personal > Certificates``Current User > Trusted root certification authorities > Certificates`
* 個人証明書と信頼されたルート証明機関の両方から、検出されたすべての証明書を削除します。 IIS Express localhost 証明書 **は削除しないでください。**
* 次のコマンドを実行します。

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

開いているすべてのブラウザーインスタンスを閉じます。 新しいブラウザーウィンドウを開いてアプリを開きます。

### <a name="os-x---certificate-not-trusted"></a>OS X-信頼されていない証明書

* キーチェーンアクセスを開きます。
* システムキーチェーンを選択します。
* Localhost 証明書の存在を確認します。
* アイコンにシンボルが含まれていることを確認 `+` し、すべてのユーザーに対して信頼されていることを示します。
* システムキーチェーンから証明書を削除します。
* 次のコマンドを実行します。

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

開いているすべてのブラウザーインスタンスを閉じます。 新しいブラウザーウィンドウを開いてアプリを開きます。

Visual Studio での証明書の問題のトラブルシューティングについては、 [IIS Express (dotnet/AspNetCore #16892) を使用した HTTPS エラー](https://github.com/dotnet/AspNetCore/issues/16892) を参照してください。

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a>IIS Express Visual Studio で使用される SSL 証明書

IIS Express 証明書の問題を解決するには、Visual Studio インストーラーで [ **修復** ] を選択します。 詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/aspnetcore/issues/16892)します。

<a name="trust-ff"></a>

### <a name="firefox-sec_error_inadequate_key_usage-certificate-error"></a>Firefox SEC_ERROR_INADEQUATE_KEY_USAGE 証明書エラー

Firefox ブラウザーでは独自の証明書ストアが使用されるため、 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) または [kestrel](xref:fundamentals/servers/kestrel) 開発者の証明書は信頼されません。

Firefox を IIS Express または Kestrel と共に使用するには、を設定します。  `security.enterprise_roots.enabled` = `true`

1. `about:config`FireFox ブラウザーで「」と入力します。
1. リスクに同意する場合は、[リスクを受け入れる] を選択し **て続行** します。
1. [**すべて表示**] を選択
1. 一連 `security.enterprise_roots.enabled` = `true`
1. Firefox を終了して再起動する

詳細については、「 [Firefox での証明機関 (ca) の設定](https://support.mozilla.org/kb/setting-certificate-authorities-firefox)」を参照してください。

## <a name="additional-information"></a>関連情報

* <xref:host-and-deploy/proxy-load-balancer>
* [Apache: HTTPS 構成を使用した Linux での ASP.NET Core のホスト](xref:host-and-deploy/linux-apache#https-configuration)
* [Nginx: HTTPS 構成を使用した Linux でのホスト ASP.NET Core](xref:host-and-deploy/linux-nginx#https-configuration)
* [IIS で SSL を設定する方法](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [OWASP HSTS ブラウザーサポート](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)

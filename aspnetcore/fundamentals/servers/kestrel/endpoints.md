---
title: ASP.NET Core Kestrel Web サーバーのエンドポイントを構成する
author: rick-anderson
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel でエンドポイントを構成する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: f9d82409f4b31a5564c7cdfa48beb303d784e213
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057149"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="8ecc2-103">ASP.NET Core Kestrel Web サーバーのエンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="8ecc2-103">Configure endpoints for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="8ecc2-104">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-104">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="8ecc2-105">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="8ecc2-105">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="8ecc2-106">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-106">Specify URLs using the:</span></span>

* <span data-ttu-id="8ecc2-107">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-107">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="8ecc2-108">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-108">`--urls` command-line argument.</span></span>
* <span data-ttu-id="8ecc2-109">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-109">`urls` host configuration key.</span></span>
* <span data-ttu-id="8ecc2-110">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-110">`UseUrls` extension method.</span></span>

<span data-ttu-id="8ecc2-111">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-111">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="8ecc2-112">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-112">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="8ecc2-113">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-113">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="8ecc2-114">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-114">A development certificate is created:</span></span>

* <span data-ttu-id="8ecc2-115">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-115">When the [.NET SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="8ecc2-116">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-116">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="8ecc2-117">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-117">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="8ecc2-118">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-118">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8ecc2-119"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-119">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="8ecc2-120">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-120">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="8ecc2-121">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="8ecc2-121">`KestrelServerOptions` configuration:</span></span>

## <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="8ecc2-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="8ecc2-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="8ecc2-123">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-123">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="8ecc2-124">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-124">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="8ecc2-125"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-125">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configureiconfiguration"></a><span data-ttu-id="8ecc2-126">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="8ecc2-126">Configure(IConfiguration)</span></span>

<span data-ttu-id="8ecc2-127">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-127">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="8ecc2-128">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-128">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="8ecc2-129">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-129">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="8ecc2-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="8ecc2-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="8ecc2-131">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-131">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="8ecc2-132">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-132">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="8ecc2-133"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-133">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> won't have the defaults applied.</span></span>

## <a name="listenoptionsusehttps"></a><span data-ttu-id="8ecc2-134">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="8ecc2-134">ListenOptions.UseHttps</span></span>

<span data-ttu-id="8ecc2-135">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-135">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="8ecc2-136">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="8ecc2-136">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="8ecc2-137">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-137">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="8ecc2-138">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-138">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="8ecc2-139">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="8ecc2-139">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="8ecc2-140">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-140">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="8ecc2-141">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-141">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="8ecc2-142">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-142">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="8ecc2-143">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-143">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="8ecc2-144">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-144">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="8ecc2-145">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-145">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="8ecc2-146">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-146">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="8ecc2-147">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-147">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="8ecc2-148">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-148">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="8ecc2-149">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-149">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="8ecc2-150">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-150">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="8ecc2-151">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-151">Supported configurations described next:</span></span>

* <span data-ttu-id="8ecc2-152">構成なし</span><span class="sxs-lookup"><span data-stu-id="8ecc2-152">No configuration</span></span>
* <span data-ttu-id="8ecc2-153">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="8ecc2-153">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="8ecc2-154">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="8ecc2-154">Change the defaults in code</span></span>

### <a name="no-configuration"></a><span data-ttu-id="8ecc2-155">構成なし</span><span class="sxs-lookup"><span data-stu-id="8ecc2-155">No configuration</span></span>

<span data-ttu-id="8ecc2-156">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-156">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a><span data-ttu-id="8ecc2-157">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="8ecc2-157">Replace the default certificate from configuration</span></span>

<span data-ttu-id="8ecc2-158">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-158">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="8ecc2-159">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-159">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="8ecc2-160">次の *appsettings.json* の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-160">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="8ecc2-161">`AllowInvalid` を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-161">Set `AllowInvalid` to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="8ecc2-162">証明書 (この後の例では `HttpsDefaultCert`) が指定されていないすべての HTTPS エンドポイントは、`Certificates:Default` または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-162">Any HTTPS endpoint that doesn't specify a certificate (`HttpsDefaultCert` in the example that follows) falls back to the cert defined under `Certificates:Default` or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="8ecc2-163">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="8ecc2-163">Schema notes:</span></span>

* <span data-ttu-id="8ecc2-164">エンドポイント名は[大文字と小文字が区別されます](xref:fundamentals/configuration/index#configuration-keys-and-values)。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-164">Endpoints names are [case-insensitive](xref:fundamentals/configuration/index#configuration-keys-and-values).</span></span> <span data-ttu-id="8ecc2-165">たとえば、 `HTTPS` and `Https` は同等です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-165">For example, `HTTPS` and `Https` are equivalent.</span></span>
* <span data-ttu-id="8ecc2-166">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-166">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="8ecc2-167">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-167">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="8ecc2-168">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-168">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="8ecc2-169">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-169">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="8ecc2-170">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-170">The `Certificate` section is optional.</span></span> <span data-ttu-id="8ecc2-171">`Certificate` セクションを指定しないと、`Certificates:Default` で定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-171">If the `Certificate` section isn't specified, the defaults defined in `Certificates:Default` are used.</span></span> <span data-ttu-id="8ecc2-172">既定値を使用できない場合は、開発証明書が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-172">If no defaults are available, the development certificate is used.</span></span> <span data-ttu-id="8ecc2-173">既定値がなく、開発証明書も存在しない場合、サーバーにより例外がスローされ、起動に失敗します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-173">If there are no defaults and the development certificate isn't present, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="8ecc2-174">`Certificate` セクションでは複数の[証明書のソース](#certificate-sources)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-174">The `Certificate` section supports multiple [certificate sources](#certificate-sources).</span></span>
* <span data-ttu-id="8ecc2-175">ポートが競合しない限り、[構成](xref:fundamentals/configuration/index)で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-175">Any number of endpoints may be defined in [Configuration](xref:fundamentals/configuration/index) as long as they don't cause port conflicts.</span></span>

#### <a name="certificate-sources"></a><span data-ttu-id="8ecc2-176">証明書のソース</span><span class="sxs-lookup"><span data-stu-id="8ecc2-176">Certificate sources</span></span>

<span data-ttu-id="8ecc2-177">さまざまなソースから証明書を読み込むように証明書ノードを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-177">Certificate nodes can be configured to load certificates from a number of sources:</span></span>

* <span data-ttu-id="8ecc2-178">*.pfx* ファイルを読み込むための `Path` と `Password`。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-178">`Path` and `Password` to load *.pfx* files.</span></span>
* <span data-ttu-id="8ecc2-179">*.pem*/ *.crt* および *.key* ファイルを読み込むための `Path`、`KeyPath`、および `Password`。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-179">`Path`, `KeyPath` and `Password` to load *.pem*/*.crt* and *.key* files.</span></span>
* <span data-ttu-id="8ecc2-180">証明書ストアから読み込むための `Subject` と `Store`。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-180">`Subject` and `Store` to load from the certificate store.</span></span>

<span data-ttu-id="8ecc2-181">たとえば、 `Certificates:Default`   の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-181">For example, the `Certificates:Default` certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a><span data-ttu-id="8ecc2-182">ConfigurationLoader</span><span class="sxs-lookup"><span data-stu-id="8ecc2-182">ConfigurationLoader</span></span>

<span data-ttu-id="8ecc2-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="8ecc2-184">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-184">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span>

* <span data-ttu-id="8ecc2-185">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-185">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="8ecc2-186">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-186">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="8ecc2-187">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-187">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="8ecc2-188">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-188">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="8ecc2-189">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-189">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="8ecc2-190">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-190">These overloads don't use names and only consume default settings from configuration.</span></span>

### <a name="change-the-defaults-in-code"></a><span data-ttu-id="8ecc2-191">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="8ecc2-191">Change the defaults in code</span></span>

<span data-ttu-id="8ecc2-192">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-192">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="8ecc2-193">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-193">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a><span data-ttu-id="8ecc2-194">Server Name Indication を使用してエンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="8ecc2-194">Configure endpoints using Server Name Indication</span></span>

<span data-ttu-id="8ecc2-195">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-195">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="8ecc2-196">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-196">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="8ecc2-197">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-197">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="8ecc2-198">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-198">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="8ecc2-199">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-199">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span> <span data-ttu-id="8ecc2-200">次のコールバック コードは、プロジェクトの *Program.cs* ファイルの `ConfigureWebHostDefaults` メソッドの呼び出しで使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-200">The following callback code can be used in the `ConfigureWebHostDefaults` method call of a project's *Program.cs* file:</span></span>

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

<span data-ttu-id="8ecc2-201">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-201">SNI support requires:</span></span>

* <span data-ttu-id="8ecc2-202">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-202">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="8ecc2-203">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-203">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="8ecc2-204">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-204">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="8ecc2-205">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-205">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="8ecc2-206">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-206">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

## <a name="ssltls-protocols"></a><span data-ttu-id="8ecc2-207">SSL/TLS プロトコル</span><span class="sxs-lookup"><span data-stu-id="8ecc2-207">SSL/TLS Protocols</span></span>

<span data-ttu-id="8ecc2-208">SSL プロトコルは、2 つのピア (伝統的にはクライアントとサーバー) 間のトラフィックの暗号化と暗号化解除に使用されるプロトコルです。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-208">SSL Protocols are protocols used for encrypting and decrypting traffic between two peers, traditionally a client and a server.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

<span data-ttu-id="8ecc2-209">既定値 `SslProtocols.None` では、Kestrel によってオペレーティング システムの既定値が使用され、最適なプロトコルが選択されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-209">The default value, `SslProtocols.None`, causes Kestrel to use the operating system defaults to choose the best protocol.</span></span> <span data-ttu-id="8ecc2-210">プロトコルを選択する特別な理由がない限り、既定値を使用してください。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-210">Unless you have a specific reason to select a protocol, use the default.</span></span>
## <a name="connection-logging"></a><span data-ttu-id="8ecc2-211">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="8ecc2-211">Connection logging</span></span>

<span data-ttu-id="8ecc2-212">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-212">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="8ecc2-213">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-213">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="8ecc2-214">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-214">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="8ecc2-215">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-215">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span> <span data-ttu-id="8ecc2-216">これは、[接続ミドルウェア](#connection-middleware)に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-216">This is built-in [Connection Middleware](#connection-middleware).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="8ecc2-217">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="8ecc2-217">Bind to a TCP socket</span></span>

<span data-ttu-id="8ecc2-218"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="8ecc2-219">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-219">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="8ecc2-220">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-220">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a><span data-ttu-id="8ecc2-221">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="8ecc2-221">Bind to a Unix socket</span></span>

<span data-ttu-id="8ecc2-222">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-222">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="8ecc2-223">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-223">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="8ecc2-224">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-224">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="8ecc2-225">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-225">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

## <a name="port-0"></a><span data-ttu-id="8ecc2-226">ポート 0</span><span class="sxs-lookup"><span data-stu-id="8ecc2-226">Port 0</span></span>

<span data-ttu-id="8ecc2-227">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-227">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="8ecc2-228">次の例では、実行時に Kestrel によってバインドされたポートを特定する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-228">The following example shows how to determine which port Kestrel bound at runtime:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="8ecc2-229">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-229">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a><span data-ttu-id="8ecc2-230">制限事項</span><span class="sxs-lookup"><span data-stu-id="8ecc2-230">Limitations</span></span>

<span data-ttu-id="8ecc2-231">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-231">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* <span data-ttu-id="8ecc2-232">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8ecc2-232">`--urls` command-line argument</span></span>
* <span data-ttu-id="8ecc2-233">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="8ecc2-233">`urls` host configuration key</span></span>
* <span data-ttu-id="8ecc2-234">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="8ecc2-234">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="8ecc2-235">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-235">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="8ecc2-236">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-236">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="8ecc2-237">HTTPS エンドポイントの構成 (`KestrelServerOptions` 構成、またはこの記事で前に示した構成ファイルなどを使用) で既定の証明書が指定されていない場合は、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-237">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this article).</span></span>
* <span data-ttu-id="8ecc2-238">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-238">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

## <a name="iis-endpoint-configuration"></a><span data-ttu-id="8ecc2-239">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="8ecc2-239">IIS endpoint configuration</span></span>

<span data-ttu-id="8ecc2-240">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-240">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="8ecc2-241">詳細については、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-241">For more information, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="listenoptionsprotocols"></a><span data-ttu-id="8ecc2-242">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="8ecc2-242">ListenOptions.Protocols</span></span>

<span data-ttu-id="8ecc2-243">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-243">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="8ecc2-244">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-244">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="8ecc2-245">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="8ecc2-245">`HttpProtocols` enum value</span></span> | <span data-ttu-id="8ecc2-246">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="8ecc2-246">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="8ecc2-247">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-247">HTTP/1.1 only.</span></span> <span data-ttu-id="8ecc2-248">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-248">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="8ecc2-249">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-249">HTTP/2 only.</span></span> <span data-ttu-id="8ecc2-250">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-250">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="8ecc2-251">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-251">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="8ecc2-252">HTTP/2 では、クライアントが TLS [アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクで HTTP/2 を選択する必要があります。それ以外の場合、接続は既定で HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-252">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="8ecc2-253">任意のエンドポイントに対する `ListenOptions.Protocols` の既定値は `HttpProtocols.Http1AndHttp2` です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-253">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="8ecc2-254">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="8ecc2-254">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="8ecc2-255">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="8ecc2-255">TLS version 1.2 or later</span></span>
* <span data-ttu-id="8ecc2-256">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="8ecc2-256">Renegotiation disabled</span></span>
* <span data-ttu-id="8ecc2-257">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="8ecc2-257">Compression disabled</span></span>
* <span data-ttu-id="8ecc2-258">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="8ecc2-258">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="8ecc2-259">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;:224 ビット以上</span><span class="sxs-lookup"><span data-stu-id="8ecc2-259">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="8ecc2-260">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;:2048 ビット以上</span><span class="sxs-lookup"><span data-stu-id="8ecc2-260">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="8ecc2-261">暗号スイートは禁止されない</span><span class="sxs-lookup"><span data-stu-id="8ecc2-261">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="8ecc2-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-262">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="8ecc2-263">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-263">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="8ecc2-264">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-264">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="8ecc2-265">Linux では、<xref:System.Net.Security.CipherSuitesPolicy> を使って、接続ごとに TLS ハンドシェイクをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-265">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a><span data-ttu-id="8ecc2-266">接続ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="8ecc2-266">Connection Middleware</span></span>

<span data-ttu-id="8ecc2-267">カスタムの接続ミドルウェアを使用すると、必要に応じて、特定の暗号に対し、接続ごとに TLS ハンドシェイクをフィルター処理することができます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-267">Custom connection middleware can filter TLS handshakes on a per-connection basis for specific ciphers if necessary.</span></span>

<span data-ttu-id="8ecc2-268">次の例では、アプリでサポートされていないすべての暗号アルゴリズムに対して <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-268">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="8ecc2-269">または、[ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) を定義して、指定できる暗号スイートの一覧と比較します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-269">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="8ecc2-270">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 暗号アルゴリズムでは、暗号化は使用されません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-270">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="8ecc2-271">接続のフィルター処理は、<xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> のラムダを使って構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-271">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a><span data-ttu-id="8ecc2-272">HTTP プロトコルを構成から設定する</span><span class="sxs-lookup"><span data-stu-id="8ecc2-272">Set the HTTP protocol from configuration</span></span>

<span data-ttu-id="8ecc2-273">`CreateDefaultBuilder` は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-273">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="8ecc2-274">次の *appsettings.json* の例では、すべてのエンドポイントにおける既定の接続プロトコルとして HTTP/1.1 を確立しています。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-274">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="8ecc2-275">次の *appsettings.json* の例では、特定のエンドポイントにおける HTTP/1.1 接続プロトコルを確立しています。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-275">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="8ecc2-276">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-276">Protocols specified in code override values set by configuration.</span></span>

## <a name="url-prefixes"></a><span data-ttu-id="8ecc2-277">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="8ecc2-277">URL prefixes</span></span>

<span data-ttu-id="8ecc2-278">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-278">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="8ecc2-279">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-279">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="8ecc2-280">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-280">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="8ecc2-281">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="8ecc2-281">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="8ecc2-282">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-282">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="8ecc2-283">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="8ecc2-283">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="8ecc2-284">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-284">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="8ecc2-285">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="8ecc2-285">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="8ecc2-286">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-286">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="8ecc2-287">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-287">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="8ecc2-288">さまざまなホスト名を、同じポート上のさまざまな ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) かリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-288">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server.</span></span> <span data-ttu-id="8ecc2-289">リバース プロキシ サーバーの例としては、IIS、Nginx、Apache などがあります。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-289">Reverse proxy server examples include IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="8ecc2-290">リバース プロキシ構成でのホストには[ホストのフィルター処理](xref:fundamentals/servers/kestrel/host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-290">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

* <span data-ttu-id="8ecc2-291">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="8ecc2-291">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="8ecc2-292">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-292">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="8ecc2-293">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-293">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="8ecc2-294">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="8ecc2-294">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

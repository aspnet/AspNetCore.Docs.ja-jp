---
title: ASP.NET Core Blazor のコンテンツ セキュリティ ポリシーを適用する
author: guardrex
description: ASP.NET Core Blazor アプリでコンテンツ セキュリティ ポリシー (CSP) を使用して、クロスサイト スクリプティング (XSS) 攻撃から保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: f44f348947e31864f5d0d44c9caf1a3aa9c3b1d4
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280336"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="3249e-103">ASP.NET Core Blazor のコンテンツ セキュリティ ポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="3249e-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="3249e-104">[クロスサイト スクリプティング (XSS)](xref:security/cross-site-scripting) は、アプリのレンダリングされたコンテンツに攻撃者が 1 つ以上の悪意のあるクライアント側スクリプトを配置するセキュリティの脆弱性です。</span><span class="sxs-lookup"><span data-stu-id="3249e-104">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="3249e-105">コンテンツ セキュリティ ポリシー (CSP) は、ブラウザーに次の有効な内容を通知することで、XSS 攻撃から保護することができます。</span><span class="sxs-lookup"><span data-stu-id="3249e-105">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="3249e-106">スクリプト、スタイルシート、画像など、読み込まれたコンテンツのソース。</span><span class="sxs-lookup"><span data-stu-id="3249e-106">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="3249e-107">ページによって実行され、フォームの許可された URL ターゲットを指定するアクション。</span><span class="sxs-lookup"><span data-stu-id="3249e-107">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="3249e-108">読み込むことができるプラグイン。</span><span class="sxs-lookup"><span data-stu-id="3249e-108">Plugins that can be loaded.</span></span>

<span data-ttu-id="3249e-109">CSP をアプリに適用するために、開発者は 1 つ以上の `Content-Security-Policy` ヘッダーまたは `<meta>` タグに複数の CSP コンテンツ セキュリティ "*ディレクティブ*" を指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-109">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="3249e-110">ポリシーは、ページの読み込み中にブラウザーによって評価されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-110">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="3249e-111">ブラウザーによりページのソースが検査され、コンテンツ セキュリティ ディレクティブの要件を満たしているかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-111">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="3249e-112">リソースのポリシー ディレクティブが満たされていない場合、ブラウザーでリソースが読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="3249e-112">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="3249e-113">たとえば、サードパーティのスクリプトを許可しないポリシーについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3249e-113">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="3249e-114">ページの `src` 属性にサードパーティから発生した `<script>` タグが含まれている場合、ブラウザーによってスクリプトの読み込みが禁止されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-114">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="3249e-115">CSP は、Chrome、Microsoft Edge、Firefox、Opera、Safari など、最新のデスクトップおよびモバイル ブラウザーのほとんどでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3249e-115">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="3249e-116">CSP は、Blazor アプリで推奨されています。</span><span class="sxs-lookup"><span data-stu-id="3249e-116">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="3249e-117">ポリシー ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="3249e-117">Policy directives</span></span>

<span data-ttu-id="3249e-118">最小限として、Blazor アプリの次のディレクティブとソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-118">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="3249e-119">必要に応じて、ディレクティブとソースを追加してください。</span><span class="sxs-lookup"><span data-stu-id="3249e-119">Add additional directives and sources as needed.</span></span> <span data-ttu-id="3249e-120">次のディレクティブは、この記事の「[ポリシーの適用](#apply-the-policy)」セクションで使用されます。そこでは、Blazor WebAssembly と Blazor Server のセキュリティ ポリシーの例を示しています。</span><span class="sxs-lookup"><span data-stu-id="3249e-120">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="3249e-121">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): ページの `<base>` タグの URL を制限します。</span><span class="sxs-lookup"><span data-stu-id="3249e-121">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="3249e-122">`self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-122">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="3249e-123">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): HTTP と HTTPS の混合コンテンツの読み込みを禁止します。</span><span class="sxs-lookup"><span data-stu-id="3249e-123">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="3249e-124">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): ポリシーによって明示的に指定されていないソース ディレクティブのフォールバックを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-124">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="3249e-125">`self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-125">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="3249e-126">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): 画像の有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-126">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="3249e-127">`data:` の URL からの画像の読み込みを許可するには `data:` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-127">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="3249e-128">HTTPS のエンドポイントからの画像の読み込みを許可するには `https:` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-128">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="3249e-129">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): `<object>`、`<embed>`、`<applet>` タグの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-129">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="3249e-130">すべての URL ソースを禁止するには、`none` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-130">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="3249e-131">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): スクリプトの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-131">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="3249e-132">ブートストラップ スクリプトの `https://stackpath.bootstrapcdn.com/` ホスト ソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-132">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="3249e-133">`self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-133">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="3249e-134">Blazor WebAssembly アプリでは以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="3249e-134">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="3249e-135">必要なスクリプトの読み込みを許可するハッシュを指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-135">Specify hashes to permit required scripts to load.</span></span>
    * <span data-ttu-id="3249e-136">`eval()` および文字列からコードを作成するメソッドを使用するには、`unsafe-eval` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-136">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="3249e-137">Blazor Server アプリでは、必要なスクリプトの読み込みを許可するハッシュを指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-137">In a Blazor Server app, specify hashes to permit required scripts to load.</span></span>
* <span data-ttu-id="3249e-138">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): スタイルシートの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-138">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="3249e-139">ブートストラップ スタイルシートの `https://stackpath.bootstrapcdn.com/` ホスト ソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-139">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="3249e-140">`self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-140">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="3249e-141">インライン スタイルの使用を許可するには `unsafe-inline` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-141">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="3249e-142">初期要求後にクライアントとサーバーを再接続するには、Blazor Server アプリの UI にインライン宣言が必要です。</span><span class="sxs-lookup"><span data-stu-id="3249e-142">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="3249e-143">今後のリリースでは、`unsafe-inline` が不要になるように、インライン スタイルが削除される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3249e-143">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="3249e-144">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): セキュリティで保護されていない (HTTP) ソースからのコンテンツ URL を HTTPS 経由で安全に取得する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-144">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="3249e-145">上記のディレクティブは、Microsoft Internet Explorer 以外のすべてのブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3249e-145">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="3249e-146">追加のインライン スクリプトの SHA ハッシュを取得するには:</span><span class="sxs-lookup"><span data-stu-id="3249e-146">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="3249e-147">「[ポリシーの適用](#apply-the-policy)」セクションに示されている CSP を適用します。</span><span class="sxs-lookup"><span data-stu-id="3249e-147">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="3249e-148">アプリをローカルで実行しながら、ブラウザーの開発者ツールのコンソールにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="3249e-148">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="3249e-149">CSP ヘッダーまたは `meta` タグが存在する場合、ブラウザーではブロックされたスクリプトのハッシュが計算されて表示されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-149">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="3249e-150">ブラウザーによって提供されるハッシュを `script-src` ソースにコピーします。</span><span class="sxs-lookup"><span data-stu-id="3249e-150">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="3249e-151">単一引用符を使用して各ハッシュを囲みます。</span><span class="sxs-lookup"><span data-stu-id="3249e-151">Use single quotes around each hash.</span></span>

<span data-ttu-id="3249e-152">コンテンツ セキュリティ ポリシー レベル 2 のブラウザー サポート マトリックスは、「[Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)」(私は使用できますか? コンテンツ セキュリティ ポリシー レベル 2) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3249e-152">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="3249e-153">ポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="3249e-153">Apply the policy</span></span>

<span data-ttu-id="3249e-154">`<meta>` タグを使用してポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="3249e-154">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="3249e-155">`http-equiv` 属性の値は `Content-Security-Policy` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-155">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="3249e-156">`content` 属性値にディレクティブを配置します。</span><span class="sxs-lookup"><span data-stu-id="3249e-156">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="3249e-157">各ディレクティブをセミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="3249e-157">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="3249e-158">常に `meta` タグを `<head>` のコンテンツに配置します。</span><span class="sxs-lookup"><span data-stu-id="3249e-158">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="3249e-159">次のセクションに、Blazor WebAssembly と Blazor Server のポリシーの例を示します。</span><span class="sxs-lookup"><span data-stu-id="3249e-159">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="3249e-160">これらの例は、Blazor のリリースごとにこの記事でバージョン管理されています。</span><span class="sxs-lookup"><span data-stu-id="3249e-160">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="3249e-161">リリースに適したバージョンを使用するには、この Web ページの **[バージョン]** ドロップ ダウン セレクターを使用してドキュメントのバージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="3249e-161">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### Blazor WebAssembly

<span data-ttu-id="3249e-162">`wwwroot/index.html` ホスト ページの `<head>` コンテンツで、「[ポリシー ディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。</span><span class="sxs-lookup"><span data-stu-id="3249e-162">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="3249e-163">アプリで必要な場合に、さらに `script-src` と `style-src` ハッシュを追加します。</span><span class="sxs-lookup"><span data-stu-id="3249e-163">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="3249e-164">開発時には、オンライン ツールまたはブラウザー開発者ツールを使用して、ハッシュが自動的に計算されるようにします。</span><span class="sxs-lookup"><span data-stu-id="3249e-164">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="3249e-165">たとえば、次のブラウザー ツール コンソールのエラーは、必要なスクリプトに対するハッシュがポリシーでカバーされていないことを報告するものです。</span><span class="sxs-lookup"><span data-stu-id="3249e-165">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="3249e-166">次のコンテンツ セキュリティ ポリシー ディレクティブに違反しているため、インライン スクリプトの実行が拒否されました: "..."。</span><span class="sxs-lookup"><span data-stu-id="3249e-166">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="3249e-167">インライン実行を有効にするには、'unsafe-inline' キーワード、ハッシュ ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=')、または nonce ('nonce-...') のいずれかが必要です。</span><span class="sxs-lookup"><span data-stu-id="3249e-167">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="3249e-168">エラーに関連付けられている特定のスクリプトが、コンソール内でエラーの隣に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-168">The particular script associated with the error is displayed in the console next to the error.</span></span>

### Blazor Server

<span data-ttu-id="3249e-169">`Pages/_Host.cshtml` ホスト ページの `<head>` コンテンツで、「[ポリシー ディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。</span><span class="sxs-lookup"><span data-stu-id="3249e-169">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="3249e-170">アプリで必要な場合に、さらに `script-src` と `style-src` ハッシュを追加します。</span><span class="sxs-lookup"><span data-stu-id="3249e-170">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="3249e-171">開発時には、オンライン ツールまたはブラウザー開発者ツールを使用して、ハッシュが自動的に計算されるようにします。</span><span class="sxs-lookup"><span data-stu-id="3249e-171">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="3249e-172">たとえば、次のブラウザー ツール コンソールのエラーは、必要なスクリプトに対するハッシュがポリシーでカバーされていないことを報告するものです。</span><span class="sxs-lookup"><span data-stu-id="3249e-172">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="3249e-173">次のコンテンツ セキュリティ ポリシー ディレクティブに違反しているため、インライン スクリプトの実行が拒否されました: "..."。</span><span class="sxs-lookup"><span data-stu-id="3249e-173">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="3249e-174">インライン実行を有効にするには、'unsafe-inline' キーワード、ハッシュ ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=')、または nonce ('nonce-...') のいずれかが必要です。</span><span class="sxs-lookup"><span data-stu-id="3249e-174">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="3249e-175">エラーに関連付けられている特定のスクリプトが、コンソール内でエラーの隣に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-175">The particular script associated with the error is displayed in the console next to the error.</span></span>

## <a name="meta-tag-limitations"></a><span data-ttu-id="3249e-176">Meta タグに関する制限事項</span><span class="sxs-lookup"><span data-stu-id="3249e-176">Meta tag limitations</span></span>

<span data-ttu-id="3249e-177">`<meta>` タグ ポリシーでは、次のディレクティブがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3249e-177">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="3249e-178">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="3249e-178">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="3249e-179">report-to</span><span class="sxs-lookup"><span data-stu-id="3249e-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="3249e-180">report-uri</span><span class="sxs-lookup"><span data-stu-id="3249e-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="3249e-181">sandbox</span><span class="sxs-lookup"><span data-stu-id="3249e-181">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="3249e-182">上記のディレクティブをサポートするには、`Content-Security-Policy` という名前のヘッダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="3249e-182">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="3249e-183">ディレクティブ文字列は、ヘッダーの値です。</span><span class="sxs-lookup"><span data-stu-id="3249e-183">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="3249e-184">ポリシーをテストして違反レポートを受信する</span><span class="sxs-lookup"><span data-stu-id="3249e-184">Test a policy and receive violation reports</span></span>

<span data-ttu-id="3249e-185">テストは、初期ポリシーを作成するときに、サードパーティのスクリプトが誤ってブロックされていないことを確認するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3249e-185">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="3249e-186">ポリシー ディレクティブを適用せずに一定期間にわたってポリシーをテストするには、ヘッダーベースのポリシーの `<meta>` タグの `http-equiv` 属性またはヘッダー名を `Content-Security-Policy-Report-Only` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3249e-186">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="3249e-187">エラー レポートは、JSON ドキュメントとして指定された URL に送信されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-187">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="3249e-188">詳細については、[MDN Web ドキュメント:「Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3249e-188">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="3249e-189">ポリシーがアクティブになっている間に違反を報告するには、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3249e-189">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="3249e-190">report-to</span><span class="sxs-lookup"><span data-stu-id="3249e-190">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="3249e-191">report-uri</span><span class="sxs-lookup"><span data-stu-id="3249e-191">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="3249e-192">`report-uri` の使用は推奨されなくなりましたが、すべての主要なブラウザーで `report-to` がサポートされるようになるまで、両方のディレクティブを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3249e-192">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="3249e-193">`report-uri` のサポートは、ブラウザーから "*常に*" 削除される可能性があるため、`report-uri` を排他的に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="3249e-193">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="3249e-194">`report-to` が完全にサポートされたら、ポリシー内の `report-uri` のサポートを削除します。</span><span class="sxs-lookup"><span data-stu-id="3249e-194">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="3249e-195">`report-to` が導入されたかどうかを追跡する方法については、「[Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)」(私は使用できますか? report-to) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3249e-195">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="3249e-196">アプリがリリースされるたびにポリシーをテストして更新してください。</span><span class="sxs-lookup"><span data-stu-id="3249e-196">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3249e-197">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3249e-197">Troubleshoot</span></span>

* <span data-ttu-id="3249e-198">エラーは、ブラウザーの開発者ツールのコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-198">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="3249e-199">ブラウザーには次に関する情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3249e-199">Browsers provide information about:</span></span>
  * <span data-ttu-id="3249e-200">ポリシーに準拠していない要素。</span><span class="sxs-lookup"><span data-stu-id="3249e-200">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="3249e-201">ブロックされた項目を許可するようにポリシーを変更する方法。</span><span class="sxs-lookup"><span data-stu-id="3249e-201">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="3249e-202">ポリシーが完全に有効になるのは、含まれているすべてのディレクティブがクライアントのブラウザーでサポートされている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="3249e-202">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="3249e-203">現在のブラウザーのサポート マトリックスについては、「[Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy)」(私は使用できますか? Content-Security-Policy) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3249e-203">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3249e-204">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3249e-204">Additional resources</span></span>

* [<span data-ttu-id="3249e-205">MDN Web ドキュメント:Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="3249e-205">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="3249e-206">コンテンツ セキュリティ ポリシー レベル 2</span><span class="sxs-lookup"><span data-stu-id="3249e-206">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="3249e-207">Google CSP Evaluator</span><span class="sxs-lookup"><span data-stu-id="3249e-207">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)

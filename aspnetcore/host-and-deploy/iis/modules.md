---
title: ASP.NET Core での IIS モジュール
author: rick-anderson
description: ASP.NET Core アプリに対してアクティブおよび非アクティブな IIS モジュールについて、さらに IIS モジュールの管理方法についてを把握します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 47ba04f199f9b77cf6032de9f80f2410f5c69424
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057402"
---
# <a name="iis-modules-with-aspnet-core"></a><span data-ttu-id="fcaa0-103">ASP.NET Core での IIS モジュール</span><span class="sxs-lookup"><span data-stu-id="fcaa0-103">IIS modules with ASP.NET Core</span></span>

<span data-ttu-id="fcaa0-104">一部のネイティブ IIS モジュールとすべての IIS マネージド モジュールでは、ASP.NET Core アプリに対する要求を処理することはできません。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-104">Some of the native IIS modules and all of the IIS managed modules aren't able to process requests for ASP.NET Core apps.</span></span> <span data-ttu-id="fcaa0-105">多くの場合、ASP.NET Core には、IIS のネイティブ モジュールおよびマネージド モジュールによって処理されるシナリオの代替が用意されています。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-105">In many cases, ASP.NET Core offers an alternative to the scenarios addressed by IIS native and managed modules.</span></span>

## <a name="native-modules"></a><span data-ttu-id="fcaa0-106">ネイティブ モジュール</span><span class="sxs-lookup"><span data-stu-id="fcaa0-106">Native modules</span></span>

<span data-ttu-id="fcaa0-107">次の表は、ASP.NET Core アプリおよび ASP.NET Core モジュールで機能するネイティブ IIS モジュールを示します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-107">The table indicates native IIS modules that are functional with ASP.NET Core apps and the ASP.NET Core Module.</span></span>

| <span data-ttu-id="fcaa0-108">Module</span><span class="sxs-lookup"><span data-stu-id="fcaa0-108">Module</span></span> | <span data-ttu-id="fcaa0-109">ASP.NET Core アプリで機能するか</span><span class="sxs-lookup"><span data-stu-id="fcaa0-109">Functional with ASP.NET Core apps</span></span> | <span data-ttu-id="fcaa0-110">ASP.NET Core のオプション</span><span class="sxs-lookup"><span data-stu-id="fcaa0-110">ASP.NET Core Option</span></span> |
| --- | :---: | --- |
| <span data-ttu-id="fcaa0-111">**匿名認証**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-111">**Anonymous Authentication**</span></span><br>`AnonymousAuthenticationModule`                                  | <span data-ttu-id="fcaa0-112">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-112">Yes</span></span> | |
| <span data-ttu-id="fcaa0-113">**基本認証**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-113">**Basic Authentication**</span></span><br>`BasicAuthenticationModule`                                          | <span data-ttu-id="fcaa0-114">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-114">Yes</span></span> | |
| <span data-ttu-id="fcaa0-115">**クライアント証明書マッピング認証**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-115">**Client Certification Mapping Authentication**</span></span><br>`CertificateMappingAuthenticationModule`      | <span data-ttu-id="fcaa0-116">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-116">Yes</span></span> | |
| <span data-ttu-id="fcaa0-117">**CGI**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-117">**CGI**</span></span><br>`CgiModule`                                                                           | <span data-ttu-id="fcaa0-118">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-118">No</span></span>  | |
| <span data-ttu-id="fcaa0-119">**構成検証**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-119">**Configuration Validation**</span></span><br>`ConfigurationValidationModule`                                  | <span data-ttu-id="fcaa0-120">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-120">Yes</span></span> | |
| <span data-ttu-id="fcaa0-121">**HTTP エラー**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-121">**HTTP Errors**</span></span><br>`CustomErrorModule`                                                           | <span data-ttu-id="fcaa0-122">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-122">No</span></span>  | [<span data-ttu-id="fcaa0-123">状態コード ページ ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-123">Status Code Pages Middleware</span></span>](xref:fundamentals/error-handling#usestatuscodepages) |
| <span data-ttu-id="fcaa0-124">**カスタム ログ**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-124">**Custom Logging**</span></span><br>`CustomLoggingModule`                                                      | <span data-ttu-id="fcaa0-125">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-125">Yes</span></span> | |
| <span data-ttu-id="fcaa0-126">**既定のドキュメント**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-126">**Default Document**</span></span><br>`DefaultDocumentModule`                                                  | <span data-ttu-id="fcaa0-127">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-127">No</span></span>  | [<span data-ttu-id="fcaa0-128">既定のファイル ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-128">Default Files Middleware</span></span>](xref:fundamentals/static-files#serve-a-default-document) |
| <span data-ttu-id="fcaa0-129">**ダイジェスト認証**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-129">**Digest Authentication**</span></span><br>`DigestAuthenticationModule`                                        | <span data-ttu-id="fcaa0-130">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-130">Yes</span></span> | |
| <span data-ttu-id="fcaa0-131">**ディレクトリの参照**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-131">**Directory Browsing**</span></span><br>`DirectoryListingModule`                                               | <span data-ttu-id="fcaa0-132">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-132">No</span></span>  | [<span data-ttu-id="fcaa0-133">ディレクトリ参照ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-133">Directory Browsing Middleware</span></span>](xref:fundamentals/static-files#enable-directory-browsing) |
| <span data-ttu-id="fcaa0-134">**動的圧縮**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-134">**Dynamic Compression**</span></span><br>`DynamicCompressionModule`                                            | <span data-ttu-id="fcaa0-135">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-135">Yes</span></span> | [<span data-ttu-id="fcaa0-136">応答圧縮ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-136">Response Compression Middleware</span></span>](xref:performance/response-compression) |
| <span data-ttu-id="fcaa0-137">**失敗した要求のトレース**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-137">**Failed Requests Tracing**</span></span><br>`FailedRequestsTracingModule`                                     | <span data-ttu-id="fcaa0-138">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-138">Yes</span></span> | [<span data-ttu-id="fcaa0-139">ASP.NET Core のログ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-139">ASP.NET Core Logging</span></span>](xref:fundamentals/logging/index#tracesource-provider) |
| <span data-ttu-id="fcaa0-140">**ファイル キャッシュ**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-140">**File Caching**</span></span><br>`FileCacheModule`                                                            | <span data-ttu-id="fcaa0-141">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-141">No</span></span>  | [<span data-ttu-id="fcaa0-142">応答キャッシュ ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-142">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="fcaa0-143">**HTTP キャッシュ**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-143">**HTTP Caching**</span></span><br>`HttpCacheModule`                                                            | <span data-ttu-id="fcaa0-144">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-144">No</span></span>  | [<span data-ttu-id="fcaa0-145">応答キャッシュ ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-145">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="fcaa0-146">**HTTP ログ**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-146">**HTTP Logging**</span></span><br>`HttpLoggingModule`                                                          | <span data-ttu-id="fcaa0-147">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-147">Yes</span></span> | [<span data-ttu-id="fcaa0-148">ASP.NET Core のログ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-148">ASP.NET Core Logging</span></span>](xref:fundamentals/logging/index) |
| <span data-ttu-id="fcaa0-149">**HTTP リダイレクト**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-149">**HTTP Redirection**</span></span><br>`HttpRedirectionModule`                                                  | <span data-ttu-id="fcaa0-150">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-150">Yes</span></span> | [<span data-ttu-id="fcaa0-151">URL リライト ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-151">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="fcaa0-152">**HTTP トレース**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-152">**HTTP Tracing**</span></span><br>`TracingModule`                                                              | <span data-ttu-id="fcaa0-153">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-153">Yes</span></span> | |
| <span data-ttu-id="fcaa0-154">**IIS クライアント証明書マッピング認証**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-154">**IIS Client Certificate Mapping Authentication**</span></span><br>`IISCertificateMappingAuthenticationModule` | <span data-ttu-id="fcaa0-155">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-155">Yes</span></span> | |
| <span data-ttu-id="fcaa0-156">**IP およびドメインの制限**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-156">**IP and Domain Restrictions**</span></span><br>`IpRestrictionModule`                                          | <span data-ttu-id="fcaa0-157">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-157">Yes</span></span> | |
| <span data-ttu-id="fcaa0-158">**ISAPI フィルター**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-158">**ISAPI Filters**</span></span><br>`IsapiFilterModule`                                                         | <span data-ttu-id="fcaa0-159">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-159">Yes</span></span> | [<span data-ttu-id="fcaa0-160">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-160">Middleware</span></span>](xref:fundamentals/middleware/index) |
| <span data-ttu-id="fcaa0-161">**ISAPI**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-161">**ISAPI**</span></span><br>`IsapiModule`                                                                       | <span data-ttu-id="fcaa0-162">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-162">Yes</span></span> | [<span data-ttu-id="fcaa0-163">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-163">Middleware</span></span>](xref:fundamentals/middleware/index) |
| <span data-ttu-id="fcaa0-164">**プロトコル サポート**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-164">**Protocol Support**</span></span><br>`ProtocolSupportModule`                                                  | <span data-ttu-id="fcaa0-165">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-165">Yes</span></span> | |
| <span data-ttu-id="fcaa0-166">**要求のフィルタリング**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-166">**Request Filtering**</span></span><br>`RequestFilteringModule`                                                | <span data-ttu-id="fcaa0-167">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-167">Yes</span></span> | [<span data-ttu-id="fcaa0-168">URL リライト ミドルウェア`IRule`</span><span class="sxs-lookup"><span data-stu-id="fcaa0-168">URL Rewriting Middleware `IRule`</span></span>](xref:fundamentals/url-rewriting#irule-based-rule) |
| <span data-ttu-id="fcaa0-169">**要求監視**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-169">**Request Monitor**</span></span><br>`RequestMonitorModule`                                                    | <span data-ttu-id="fcaa0-170">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-170">Yes</span></span> | |
| <span data-ttu-id="fcaa0-171">**URL リライト**&#8224;</span><span class="sxs-lookup"><span data-stu-id="fcaa0-171">**URL Rewriting**&#8224;</span></span><br>`RewriteModule`                                                      | <span data-ttu-id="fcaa0-172">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-172">Yes</span></span> | [<span data-ttu-id="fcaa0-173">URL リライト ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-173">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="fcaa0-174">**サーバー側インクルード**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-174">**Server-Side Includes**</span></span><br>`ServerSideIncludeModule`                                            | <span data-ttu-id="fcaa0-175">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-175">No</span></span>  | |
| <span data-ttu-id="fcaa0-176">**静的圧縮**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-176">**Static Compression**</span></span><br>`StaticCompressionModule`                                              | <span data-ttu-id="fcaa0-177">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-177">No</span></span>  | [<span data-ttu-id="fcaa0-178">応答圧縮ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-178">Response Compression Middleware</span></span>](xref:performance/response-compression) |
| <span data-ttu-id="fcaa0-179">**静的コンテンツ**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-179">**Static Content**</span></span><br>`StaticFileModule`                                                         | <span data-ttu-id="fcaa0-180">いいえ</span><span class="sxs-lookup"><span data-stu-id="fcaa0-180">No</span></span>  | [<span data-ttu-id="fcaa0-181">静的ファイル ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-181">Static File Middleware</span></span>](xref:fundamentals/static-files) |
| <span data-ttu-id="fcaa0-182">**トークン キャッシュ**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-182">**Token Caching**</span></span><br>`TokenCacheModule`                                                          | <span data-ttu-id="fcaa0-183">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-183">Yes</span></span> | |
| <span data-ttu-id="fcaa0-184">**URI キャッシュ**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-184">**URI Caching**</span></span><br>`UriCacheModule`                                                              | <span data-ttu-id="fcaa0-185">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-185">Yes</span></span> | |
| <span data-ttu-id="fcaa0-186">**URL 認証**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-186">**URL Authorization**</span></span><br>`UrlAuthorizationModule`                                                | <span data-ttu-id="fcaa0-187">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-187">Yes</span></span> | [ASP.NET Core Identity](xref:security/authentication/identity) |
| <span data-ttu-id="fcaa0-188">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="fcaa0-188">**Windows Authentication**</span></span><br>`WindowsAuthenticationModule`                                      | <span data-ttu-id="fcaa0-189">はい</span><span class="sxs-lookup"><span data-stu-id="fcaa0-189">Yes</span></span> | |

<span data-ttu-id="fcaa0-190">&#8224; URL リライト モジュールの `isFile` および `isDirectory` 一致タイプは、[ディレクトリ構造](xref:host-and-deploy/directory-structure)の変更のため、ASP.NET Core アプリでは動作しません。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-190">&#8224;The URL Rewrite Module's `isFile` and `isDirectory` match types don't work with ASP.NET Core apps due to the changes in [directory structure](xref:host-and-deploy/directory-structure).</span></span>

## <a name="managed-modules"></a><span data-ttu-id="fcaa0-191">マネージド モジュール</span><span class="sxs-lookup"><span data-stu-id="fcaa0-191">Managed modules</span></span>

<span data-ttu-id="fcaa0-192">アプリ プールの .NET CLR バージョンが **[マネージ コードなし]** に設定されている場合、マネージド モジュールはホストされた ASP.NET Core アプリでは機能 "*しません*"。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-192">Managed modules are *not* functional with hosted ASP.NET Core apps when the app pool's .NET CLR version is set to **No Managed Code**.</span></span> <span data-ttu-id="fcaa0-193">ASP.NET Core では、いくつかのケースにおいてミドルウェアの代替機能が提供されています。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-193">ASP.NET Core offers middleware alternatives in several cases.</span></span>

| <span data-ttu-id="fcaa0-194">Module</span><span class="sxs-lookup"><span data-stu-id="fcaa0-194">Module</span></span>                  | <span data-ttu-id="fcaa0-195">ASP.NET Core のオプション</span><span class="sxs-lookup"><span data-stu-id="fcaa0-195">ASP.NET Core Option</span></span> |
| ----------------------- | ------------------- |
| <span data-ttu-id="fcaa0-196">AnonymousIdentification</span><span class="sxs-lookup"><span data-stu-id="fcaa0-196">AnonymousIdentification</span></span> | |
| <span data-ttu-id="fcaa0-197">DefaultAuthentication</span><span class="sxs-lookup"><span data-stu-id="fcaa0-197">DefaultAuthentication</span></span>   | |
| <span data-ttu-id="fcaa0-198">FileAuthorization</span><span class="sxs-lookup"><span data-stu-id="fcaa0-198">FileAuthorization</span></span>       | |
| <span data-ttu-id="fcaa0-199">FormsAuthentication</span><span class="sxs-lookup"><span data-stu-id="fcaa0-199">FormsAuthentication</span></span>     | <span data-ttu-id="fcaa0-200">[Cookie 認証ミドルウェア](xref:security/authentication/cookie)</span><span class="sxs-lookup"><span data-stu-id="fcaa0-200">[Cookie Authentication Middleware](xref:security/authentication/cookie)</span></span> |
| <span data-ttu-id="fcaa0-201">OutputCache</span><span class="sxs-lookup"><span data-stu-id="fcaa0-201">OutputCache</span></span>             | [<span data-ttu-id="fcaa0-202">応答キャッシュ ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-202">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="fcaa0-203">Profile</span><span class="sxs-lookup"><span data-stu-id="fcaa0-203">Profile</span></span>                 | |
| <span data-ttu-id="fcaa0-204">RoleManager</span><span class="sxs-lookup"><span data-stu-id="fcaa0-204">RoleManager</span></span>             | |
| <span data-ttu-id="fcaa0-205">ScriptModule-4.0</span><span class="sxs-lookup"><span data-stu-id="fcaa0-205">ScriptModule-4.0</span></span>        | |
| <span data-ttu-id="fcaa0-206">セッション</span><span class="sxs-lookup"><span data-stu-id="fcaa0-206">Session</span></span>                 | [<span data-ttu-id="fcaa0-207">セッション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-207">Session Middleware</span></span>](xref:fundamentals/app-state) |
| <span data-ttu-id="fcaa0-208">UrlAuthorization</span><span class="sxs-lookup"><span data-stu-id="fcaa0-208">UrlAuthorization</span></span>        | |
| <span data-ttu-id="fcaa0-209">UrlMappingsModule</span><span class="sxs-lookup"><span data-stu-id="fcaa0-209">UrlMappingsModule</span></span>       | [<span data-ttu-id="fcaa0-210">URL リライト ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fcaa0-210">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="fcaa0-211">UrlRoutingModule-4.0</span><span class="sxs-lookup"><span data-stu-id="fcaa0-211">UrlRoutingModule-4.0</span></span>    | [ASP.NET Core Identity](xref:security/authentication/identity) |
| <span data-ttu-id="fcaa0-212">WindowsAuthentication</span><span class="sxs-lookup"><span data-stu-id="fcaa0-212">WindowsAuthentication</span></span>   | |

## <a name="iis-manager-application-changes"></a><span data-ttu-id="fcaa0-213">IIS マネージャー アプリケーションの変更</span><span class="sxs-lookup"><span data-stu-id="fcaa0-213">IIS Manager application changes</span></span>

<span data-ttu-id="fcaa0-214">IIS マネージャーを使って設定を構成すると、アプリの *web.config* ファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-214">When using IIS Manager to configure settings, the *web.config* file of the app is changed.</span></span> <span data-ttu-id="fcaa0-215">アプリを展開し、*web.config* を含めた場合、IIS マネージャーを使って行われた変更は、展開される *web.config* ファイルによって上書きされます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-215">If deploying an app and including *web.config*, any changes made with IIS Manager are overwritten by the deployed *web.config* file.</span></span> <span data-ttu-id="fcaa0-216">サーバーの *web.config* ファイルを変更する場合は、サーバー上の更新された *web.config* ファイルをローカル プロジェクトにすぐにコピーしてください。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-216">If changes are made to the server's *web.config* file, copy the updated *web.config* file on the server to the local project immediately.</span></span>

## <a name="disabling-iis-modules"></a><span data-ttu-id="fcaa0-217">IIS モジュールの無効化</span><span class="sxs-lookup"><span data-stu-id="fcaa0-217">Disabling IIS modules</span></span>

<span data-ttu-id="fcaa0-218">アプリに対して無効にする必要がある IIS モジュールがサーバー レベルで構成されている場合、アプリの *web.config* ファイルへの追加によってモジュールを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-218">If an IIS module is configured at the server level that must be disabled for an app, an addition to the app's *web.config* file can disable the module.</span></span> <span data-ttu-id="fcaa0-219">モジュールをそのまま残し、構成の設定を使って非アクティブにするか (使用可能な場合)、アプリからモジュールを削除します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-219">Either leave the module in place and deactivate it using a configuration setting (if available) or remove the module from the app.</span></span>

### <a name="module-deactivation"></a><span data-ttu-id="fcaa0-220">モジュールの非アクティブ化</span><span class="sxs-lookup"><span data-stu-id="fcaa0-220">Module deactivation</span></span>

<span data-ttu-id="fcaa0-221">多くのモジュールには、アプリからモジュールを削除せずに無効にすることができる構成設定が用意されています。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-221">Many modules offer a configuration setting that allows them to be disabled without removing the module from the app.</span></span> <span data-ttu-id="fcaa0-222">これは、モジュールを非アクティブ化する最も簡単ですばやい方法です。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-222">This is the simplest and quickest way to deactivate a module.</span></span> <span data-ttu-id="fcaa0-223">たとえば、HTTP リダイレクト モジュールは、*web.config* の `<httpRedirect>` 要素を使って無効にできます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-223">For example, the HTTP Redirection Module can be disabled with the `<httpRedirect>` element in *web.config*:</span></span>

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="fcaa0-224">構成設定を使ってモジュールを無効にする方法について詳しくは、[IIS \<system.webServer>](/iis/configuration/system.webServer/) の "*子要素*" に関するセクションのリンクに従ってください。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-224">For more information on disabling modules with configuration settings, follow the links in the *Child Elements* section of [IIS \<system.webServer>](/iis/configuration/system.webServer/).</span></span>

### <a name="module-removal"></a><span data-ttu-id="fcaa0-225">モジュールの削除</span><span class="sxs-lookup"><span data-stu-id="fcaa0-225">Module removal</span></span>

<span data-ttu-id="fcaa0-226">*web.config* の設定を使ってモジュールを削除する場合は、最初に、モジュールのロックを解除し、*web.config* の `<modules>` セクションのロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-226">If opting to remove a module with a setting in *web.config*, unlock the module and unlock the `<modules>` section of *web.config* first:</span></span>

1. <span data-ttu-id="fcaa0-227">サーバー レベルでモジュールのロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-227">Unlock the module at the server level.</span></span> <span data-ttu-id="fcaa0-228">IIS マネージャーの **[接続]** サイド バーで IIS サーバーを選びます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-228">Select the IIS server in the IIS Manager **Connections** sidebar.</span></span> <span data-ttu-id="fcaa0-229">**[IIS]** 領域で **[モジュール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-229">Open the **Modules** in the **IIS** area.</span></span> <span data-ttu-id="fcaa0-230">一覧でモジュールを選びます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-230">Select the module in the list.</span></span> <span data-ttu-id="fcaa0-231">右側の **[アクション]** サイド バーで、 **[ロック解除]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-231">In the **Actions** sidebar on the right, select **Unlock**.</span></span> <span data-ttu-id="fcaa0-232">モジュールのアクション エントリが **[ロック]** と表示される場合、モジュールのロックは既に解除されています。必要な操作はありません。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-232">If the action entry for the module appears as **Lock**, the module is already unlocked, and no action is required.</span></span> <span data-ttu-id="fcaa0-233">後で *web.config* から削除する予定のモジュールをできるだけ多くロック解除します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-233">Unlock as many modules as you plan to remove from *web.config* later.</span></span>

2. <span data-ttu-id="fcaa0-234">*web.config* の `<modules>` セクションを指定しないでアプリを展開します。最初に IIS マネージャーでセクションをロック解除せずに、`<modules>` セクションを含む *web.config* を使ってアプリを展開した場合、セクションのロックを解除しようとすると Configuration Manager で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-234">Deploy the app without a `<modules>` section in *web.config*. If an app is deployed with a *web.config* containing the `<modules>` section without having unlocked the section first in the IIS Manager, the Configuration Manager throws an exception when attempting to unlock the section.</span></span> <span data-ttu-id="fcaa0-235">したがって、`<modules>` セクションを指定しないでアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-235">Therefore, deploy the app without a `<modules>` section.</span></span>

3. <span data-ttu-id="fcaa0-236">*web.config* の `<modules>` セクションのロックを解除します。 **[接続]** サイド バーの **[サイト]** で Web サイトを選びます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-236">Unlock the `<modules>` section of *web.config*. In the **Connections** sidebar, select the website in **Sites**.</span></span> <span data-ttu-id="fcaa0-237">**[管理]** 領域で **構成エディター** を開きます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-237">In the **Management** area, open the **Configuration Editor**.</span></span> <span data-ttu-id="fcaa0-238">ナビゲーション コントロールを使って、`system.webServer/modules` セクションを選びます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-238">Use the navigation controls to select the `system.webServer/modules` section.</span></span> <span data-ttu-id="fcaa0-239">右側の **[アクション]** サイド バーで、セクションの **[ロック解除]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-239">In the **Actions** sidebar on the right, select to **Unlock** the section.</span></span> <span data-ttu-id="fcaa0-240">モジュール セクションのアクション エントリが **[セクションのロック]** と表示される場合、モジュール セクションのロックは既に解除されています。必要な操作はありません。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-240">If the action entry for the module section appears as **Lock Section**, the module section is already unlocked, and no action is required.</span></span>

4. <span data-ttu-id="fcaa0-241">アプリのローカル *web.config* ファイルに `<modules>` セクションを追加するとき、`<remove>` 要素を指定するとアプリからモジュールが取り除かれます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-241">Add a `<modules>` section to the app's local *web.config* file with a `<remove>` element to remove the module from the app.</span></span> <span data-ttu-id="fcaa0-242">複数のモジュールを削除するには、複数の `<remove>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-242">Add multiple `<remove>` elements to remove multiple modules.</span></span> <span data-ttu-id="fcaa0-243">サーバー上で *web.config* を変更した場合は、すぐにプロジェクトのローカルな *web.config* ファイルに対して同じ変更を行ってください。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-243">If *web.config* changes are made on the server, immediately make the same changes to the project's *web.config* file locally.</span></span> <span data-ttu-id="fcaa0-244">この手法でモジュールを削除すると、サーバー上の他のアプリでのモジュールの使用に影響がありません。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-244">Removing a module using this approach doesn't affect the use of the module with other apps on the server.</span></span>

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

<span data-ttu-id="fcaa0-245">*web.config* を利用して IIS Express のモジュールを追加または削除するには、`<modules>` セクションのロックを解除するには、*applicationHost.config* を変更します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-245">In order to add or remove modules for IIS Express using *web.config*, modify *applicationHost.config* to unlock the `<modules>` section:</span></span>

1. <span data-ttu-id="fcaa0-246">*{APPLICATION ROOT}\\.vs\config\applicationhost.config* を開きます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-246">Open *{APPLICATION ROOT}\\.vs\config\applicationhost.config*.</span></span>

1. <span data-ttu-id="fcaa0-247">IIS モジュールの `<section>` 要素を見つけ、`overrideModeDefault` を `Deny` から `Allow` に変更します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-247">Locate the `<section>` element for IIS modules and change `overrideModeDefault` from `Deny` to `Allow`:</span></span>

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. <span data-ttu-id="fcaa0-248">`<location path="" overrideMode="Allow"><system.webServer><modules>` セクションを見つけます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-248">Locate the `<location path="" overrideMode="Allow"><system.webServer><modules>` section.</span></span> <span data-ttu-id="fcaa0-249">削除するモジュールに対して、`lockItem` を `true` から `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-249">For any modules that you wish to remove, set `lockItem` from `true` to `false`.</span></span> <span data-ttu-id="fcaa0-250">次の例では、CGI モジュールのロックが解除されています。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-250">In the following example, the CGI Module is unlocked:</span></span>

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. <span data-ttu-id="fcaa0-251">`<modules>` セクションと個々のモジュールのロックが解除されたら、IIS Express でアプリを実行するためのアプリの *web.config* ファイルを利用し、IIS モジュールを自由に追加したり、削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-251">After the `<modules>` section and individual modules are unlocked, you're free to add or remove IIS modules using the app's *web.config* file for running the app on IIS Express.</span></span>

<span data-ttu-id="fcaa0-252">IIS モジュールは、*Appcmd.exe* を使って削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-252">An IIS module can also be removed with *Appcmd.exe*.</span></span> <span data-ttu-id="fcaa0-253">コマンドで `MODULE_NAME` と `APPLICATION_NAME` を指定します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-253">Provide the `MODULE_NAME` and `APPLICATION_NAME` in the command:</span></span>

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

<span data-ttu-id="fcaa0-254">たとえば、Default Web Site から `DynamicCompressionModule` を削除するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-254">For example, remove the `DynamicCompressionModule` from the Default Web Site:</span></span>

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a><span data-ttu-id="fcaa0-255">最小限のモジュール構成</span><span class="sxs-lookup"><span data-stu-id="fcaa0-255">Minimum module configuration</span></span>

<span data-ttu-id="fcaa0-256">ASP.NET Core アプリを実行するために必要なモジュールは、匿名認証モジュールと ASP.NET Core モジュールだけです。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-256">The only modules required to run an ASP.NET Core app are the Anonymous Authentication Module and the ASP.NET Core Module.</span></span>

<span data-ttu-id="fcaa0-257">URI キャッシュ モジュール (`UriCacheModule`) により、IIS は URL レベルで Web サイトの構成をキャッシュできます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-257">The URI Caching Module (`UriCacheModule`) allows IIS to cache website configuration at the URL level.</span></span> <span data-ttu-id="fcaa0-258">このモジュールがない場合、同じ URL が繰り返し要求された場合でも、IIS はすべての要求で構成を読み取って解析する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-258">Without this module, IIS must read and parse configuration on every request, even when the same URL is repeatedly requested.</span></span> <span data-ttu-id="fcaa0-259">すべての要求で構成を解析すると、パフォーマンスが大幅に低下します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-259">Parsing the configuration every request results in a significant performance penalty.</span></span> <span data-ttu-id="fcaa0-260">*URI キャッシュ モジュールはホストされた ASP.NET Core アプリを実行するために厳密には必要ありませんが、すべての ASP.NET Core の展開で URI キャッシュ モジュールを有効にすることをお勧めします。*</span><span class="sxs-lookup"><span data-stu-id="fcaa0-260">*Although the URI Caching Module isn't strictly required for a hosted ASP.NET Core app to run, we recommend that the URI Caching Module be enabled for all ASP.NET Core deployments.*</span></span>

<span data-ttu-id="fcaa0-261">HTTP キャッシュ モジュール (`HttpCacheModule`) は、IIS 出力キャッシュと共に、HTTP.sys キャッシュ内のアイテムをキャッシュするためのロジックも実装します。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-261">The HTTP Caching Module (`HttpCacheModule`) implements the IIS output cache and also the logic for caching items in the HTTP.sys cache.</span></span> <span data-ttu-id="fcaa0-262">このモジュールがないと、コンテンツはカーネル モードでキャッシュされなくなり、キャッシュ プロファイルは無視されます。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-262">Without this module, content is no longer cached in kernel mode, and cache profiles are ignored.</span></span> <span data-ttu-id="fcaa0-263">HTTP キャッシュ モジュールを削除すると、通常、パフォーマンスとリソースの使用に悪影響があります。</span><span class="sxs-lookup"><span data-stu-id="fcaa0-263">Removing the HTTP Caching Module usually has adverse effects on performance and resource usage.</span></span> <span data-ttu-id="fcaa0-264">*HTTP キャッシュ モジュールはホストされた ASP.NET Core アプリを実行するために厳密には必要ありませんが、すべての ASP.NET Core の展開で HTTP キャッシュ モジュールを有効にすることをお勧めします。*</span><span class="sxs-lookup"><span data-stu-id="fcaa0-264">*Although the HTTP Caching Module isn't strictly required for a hosted ASP.NET Core app to run, we recommend that the HTTP Caching Module be enabled for all ASP.NET Core deployments.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fcaa0-265">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fcaa0-265">Additional resources</span></span>

* [<span data-ttu-id="fcaa0-266">IIS アーキテクチャの概要: IIS のモジュール</span><span class="sxs-lookup"><span data-stu-id="fcaa0-266">Introduction to IIS Architectures: Modules in IIS</span></span>](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [<span data-ttu-id="fcaa0-267">IIS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="fcaa0-267">IIS Modules Overview</span></span>](/iis/get-started/introduction-to-iis/iis-modules-overview)
* <span data-ttu-id="fcaa0-268">[IIS 7.0 のロールとモジュールのカスタマイズ](/previous-versions/tn-archive/cc627313(v=technet.10))</span><span class="sxs-lookup"><span data-stu-id="fcaa0-268">[Customizing IIS 7.0 Roles and Modules](/previous-versions/tn-archive/cc627313(v=technet.10))</span></span>
* [<span data-ttu-id="fcaa0-269">IIS \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="fcaa0-269">IIS \<system.webServer></span></span>](/iis/configuration/system.webServer/)
---
title: 外部 OAuth 認証プロバイダー
author: rick-anderson
description: ASP.NET Core アプリで動作する外部 OAuth 認証プロバイダーを検出します。
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/otherlogins
ms.openlocfilehash: 11f770b752a6654ffe73b4fe005c09711515ac74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053268"
---
# <a name="external-oauth-authentication-providers"></a><span data-ttu-id="9c78c-103">外部 OAuth 認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9c78c-103">External OAuth authentication providers</span></span>

<span data-ttu-id="9c78c-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Pranav Rastogi](https://github.com/rustd)、 [valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="9c78c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="9c78c-105">次の一覧には、ASP.NET Core アプリで動作する一般的な外部 OAuth 認証プロバイダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9c78c-105">The following list includes common external OAuth authentication providers that work with ASP.NET Core apps.</span></span> <span data-ttu-id="9c78c-106">[Contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth)によって管理されているものなど、サードパーティの NuGet パッケージは、ASP.NET Core チームによって実装された認証プロバイダーを補完するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="9c78c-106">Third-party NuGet packages, such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), can be used to complement the authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="9c78c-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([手順](https://developer.linkedin.com/docs/oauth2))</span><span class="sxs-lookup"><span data-stu-id="9c78c-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))</span></span>

* <span data-ttu-id="9c78c-108">[Instagram](https://www.instagram.com/developer/register/) ([命令](https://www.instagram.com/developer/authentication/))</span><span class="sxs-lookup"><span data-stu-id="9c78c-108">[Instagram](https://www.instagram.com/developer/register/) ([Instructions](https://www.instagram.com/developer/authentication/))</span></span>

* <span data-ttu-id="9c78c-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([命令](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span><span class="sxs-lookup"><span data-stu-id="9c78c-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span></span>

* <span data-ttu-id="9c78c-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([手順](https://developer.github.com/v3/oauth/))</span><span class="sxs-lookup"><span data-stu-id="9c78c-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Instructions](https://developer.github.com/v3/oauth/))</span></span>

* <span data-ttu-id="9c78c-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([命令](https://developer.yahoo.com/bbauth/user.html))</span><span class="sxs-lookup"><span data-stu-id="9c78c-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Instructions](https://developer.yahoo.com/bbauth/user.html))</span></span>

* <span data-ttu-id="9c78c-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([命令](https://www.tumblr.com/docs/api/v2#auth))</span><span class="sxs-lookup"><span data-stu-id="9c78c-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Instructions](https://www.tumblr.com/docs/api/v2#auth))</span></span>

* <span data-ttu-id="9c78c-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([命令](https://developers.pinterest.com/docs/api/overview/?))</span><span class="sxs-lookup"><span data-stu-id="9c78c-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Instructions](https://developers.pinterest.com/docs/api/overview/?))</span></span>

* <span data-ttu-id="9c78c-114">[Pocket](https://getpocket.com/developer/apps/new) ([手順](https://getpocket.com/developer/docs/authentication))</span><span class="sxs-lookup"><span data-stu-id="9c78c-114">[Pocket](https://getpocket.com/developer/apps/new) ([Instructions](https://getpocket.com/developer/docs/authentication))</span></span>

* <span data-ttu-id="9c78c-115">[Flickr](https://www.flickr.com/services/apps/create) ([命令](https://www.flickr.com/services/api/auth.oauth.html))</span><span class="sxs-lookup"><span data-stu-id="9c78c-115">[Flickr](https://www.flickr.com/services/apps/create) ([Instructions](https://www.flickr.com/services/api/auth.oauth.html))</span></span>

* <span data-ttu-id="9c78c-116">[Dribble](https://dribbble.com/signup) ([命令](https://developer.dribbble.com/v1/oauth/))</span><span class="sxs-lookup"><span data-stu-id="9c78c-116">[Dribble](https://dribbble.com/signup) ([Instructions](https://developer.dribbble.com/v1/oauth/))</span></span>

* <span data-ttu-id="9c78c-117">[Vimeo](https://vimeo.com/join) ([命令](https://developer.vimeo.com/api/authentication))</span><span class="sxs-lookup"><span data-stu-id="9c78c-117">[Vimeo](https://vimeo.com/join) ([Instructions](https://developer.vimeo.com/api/authentication))</span></span>

* <span data-ttu-id="9c78c-118">[Soundcloud](https://soundcloud.com/you/apps/new) ([手順](https://developers.soundcloud.com/blog/we-love-oauth-2))</span><span class="sxs-lookup"><span data-stu-id="9c78c-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))</span></span>

* <span data-ttu-id="9c78c-119">[VK](https://vk.com/apps?act=manage) ([命令](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span><span class="sxs-lookup"><span data-stu-id="9c78c-119">[VK](https://vk.com/apps?act=manage) ([Instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span></span>

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

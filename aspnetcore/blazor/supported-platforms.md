---
title: ASP.NET Core Blazor でサポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazor でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280721"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="ad7ca-103">ASP.NET Core Blazor でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="ad7ca-103">ASP.NET Core Blazor supported platforms</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ad7ca-104">Blazor WebAssembly と Blazor Server は、次の表に示すブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ad7ca-104">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="ad7ca-105">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="ad7ca-105">Browser</span></span>                          | <span data-ttu-id="ad7ca-106">バージョン</span><span class="sxs-lookup"><span data-stu-id="ad7ca-106">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="ad7ca-107">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="ad7ca-107">Apple Safari, including iOS</span></span>      | <span data-ttu-id="ad7ca-108">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-108">Current&dagger;</span></span> |
| <span data-ttu-id="ad7ca-109">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="ad7ca-109">Google Chrome, including Android</span></span> | <span data-ttu-id="ad7ca-110">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-110">Current&dagger;</span></span> |
| <span data-ttu-id="ad7ca-111">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="ad7ca-111">Microsoft Edge</span></span>                   | <span data-ttu-id="ad7ca-112">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-112">Current&dagger;</span></span> |
| <span data-ttu-id="ad7ca-113">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="ad7ca-113">Mozilla Firefox</span></span>                  | <span data-ttu-id="ad7ca-114">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-114">Current&dagger;</span></span> |  

<span data-ttu-id="ad7ca-115">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="ad7ca-115">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="ad7ca-116">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="ad7ca-116">Browser</span></span>                          | <span data-ttu-id="ad7ca-117">バージョン</span><span class="sxs-lookup"><span data-stu-id="ad7ca-117">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="ad7ca-118">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="ad7ca-118">Apple Safari, including iOS</span></span>      | <span data-ttu-id="ad7ca-119">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-119">Current&dagger;</span></span>       |
| <span data-ttu-id="ad7ca-120">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="ad7ca-120">Google Chrome, including Android</span></span> | <span data-ttu-id="ad7ca-121">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-121">Current&dagger;</span></span>       |
| <span data-ttu-id="ad7ca-122">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="ad7ca-122">Microsoft Edge</span></span>                   | <span data-ttu-id="ad7ca-123">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-123">Current&dagger;</span></span>       |
| <span data-ttu-id="ad7ca-124">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="ad7ca-124">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="ad7ca-125">サポートされていません&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-125">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="ad7ca-126">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="ad7ca-126">Mozilla Firefox</span></span>                  | <span data-ttu-id="ad7ca-127">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-127">Current&dagger;</span></span>       |  

<span data-ttu-id="ad7ca-128">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="ad7ca-128">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="ad7ca-129">&Dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="ad7ca-129">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="ad7ca-130">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="ad7ca-130">Browser</span></span>                          | <span data-ttu-id="ad7ca-131">バージョン</span><span class="sxs-lookup"><span data-stu-id="ad7ca-131">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="ad7ca-132">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="ad7ca-132">Apple Safari, including iOS</span></span>      | <span data-ttu-id="ad7ca-133">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-133">Current&dagger;</span></span> |
| <span data-ttu-id="ad7ca-134">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="ad7ca-134">Google Chrome, including Android</span></span> | <span data-ttu-id="ad7ca-135">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-135">Current&dagger;</span></span> |
| <span data-ttu-id="ad7ca-136">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="ad7ca-136">Microsoft Edge</span></span>                   | <span data-ttu-id="ad7ca-137">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-137">Current&dagger;</span></span> |
| <span data-ttu-id="ad7ca-138">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="ad7ca-138">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="ad7ca-139">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-139">11&Dagger;</span></span>      |
| <span data-ttu-id="ad7ca-140">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="ad7ca-140">Mozilla Firefox</span></span>                  | <span data-ttu-id="ad7ca-141">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="ad7ca-141">Current&dagger;</span></span> |

<span data-ttu-id="ad7ca-142">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="ad7ca-142">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="ad7ca-143">&Dagger;追加のポリフィルが必要です。</span><span class="sxs-lookup"><span data-stu-id="ad7ca-143">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="ad7ca-144">たとえば、[`Polyfill.io`](https://polyfill.io/v3/) バンドルによって Promise を追加できます</span><span class="sxs-lookup"><span data-stu-id="ad7ca-144">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ad7ca-145">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ad7ca-145">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>

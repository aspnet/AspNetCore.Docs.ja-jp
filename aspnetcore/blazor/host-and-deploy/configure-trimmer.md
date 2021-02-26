---
title: ASP.NET Core Blazor 用のトリマーを構成する
author: guardrex
description: Blazor アプリをビルドする際に中間言語 (IL) リンカー (トリマー) を制御する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975217"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="b1d1a-103">ASP.NET Core Blazor 用のトリマーを構成する</span><span class="sxs-lookup"><span data-stu-id="b1d1a-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="b1d1a-104">Blazor WebAssembly では、発行された出力のサイズを縮小するために、[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) トリミングが実行されます。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="b1d1a-105">既定では、トリミングはアプリの発行時に行われます。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="b1d1a-106">トリミングは好ましくない効果を与える場合があります。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="b1d1a-107">リフレクションを使用するアプリの場合、実行時にリフレクションに必要な型がトリマーでは判断できないことがしばしばあります。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="b1d1a-108">リフレクションを使用するアプリをトリミングするには、アプリのコードと、アプリが依存するパッケージまたはフレームワークの両方で、リフレクションに必要な型をトリマーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="b1d1a-109">実行時にアプリの動的な動作に反応することもトリマーはできません。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="b1d1a-110">展開後、トリミングされたアプリが確実に正しく機能するよう、発行された出力を開発中に頻繁にテストしてください。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="b1d1a-111">トリマーを構成するには、.NET の基礎ドキュメントの[トリミング オプション](/dotnet/core/deploying/trimming-options)に関する記事を参照してください。次の項目に関するガイダンスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="b1d1a-112">プロジェクト ファイルの `<PublishTrimmed>` プロパティを使用し、アプリ全体のトリミングを無効にします。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="b1d1a-113">使用されていない IL をトリマーによって破棄する積極度を制御します。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="b1d1a-114">特定のアセンブリのトリミングをトリマーに禁止します。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="b1d1a-115">トリミング用の "ルート" アセンブリ。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="b1d1a-116">プロジェクト ファイルで `<SuppressTrimAnalysisWarnings>` プロパティを `false` に設定することで、リフレクションされた型の警告を表示します。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="b1d1a-117">シンボル トリミングとデバッガー サポートを制御します。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="b1d1a-118">フレームワーク ライブラリ機能をトリミングするためのトリマー機能を設定します。</span><span class="sxs-lookup"><span data-stu-id="b1d1a-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1d1a-119">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b1d1a-119">Additional resources</span></span>

* [<span data-ttu-id="b1d1a-120">自己完結型の展開と実行可能ファイルのトリミング</span><span class="sxs-lookup"><span data-stu-id="b1d1a-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>

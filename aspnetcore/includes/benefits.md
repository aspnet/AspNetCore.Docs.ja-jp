---
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
ms.openlocfilehash: 5c7412028a81233a0aedacfbe451014eeca4adc1
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552097"
---
<span data-ttu-id="f75fe-101">ASP.NET Core の利点は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f75fe-101">ASP.NET Core provides the following benefits:</span></span>

* <span data-ttu-id="f75fe-102">Web UI と Web API を構築するプロセスの統一。</span><span class="sxs-lookup"><span data-stu-id="f75fe-102">A unified story for building web UI and web APIs.</span></span>
* <span data-ttu-id="f75fe-103">テストの容易性を考慮したアーキテクチャ。</span><span class="sxs-lookup"><span data-stu-id="f75fe-103">Architected for testability.</span></span>
* <span data-ttu-id="f75fe-104">[Razor Pages](xref:razor-pages/index) により、ページ コーディングに重点を置いたシナリオがより簡略化され、その生産性が高められます。</span><span class="sxs-lookup"><span data-stu-id="f75fe-104">[Razor Pages](xref:razor-pages/index) makes coding page-focused scenarios easier and more productive.</span></span>
* <span data-ttu-id="f75fe-105">[Blazor](xref:blazor/index) により、ブラウザー内で JavaScript と共に C# を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f75fe-105">[Blazor](xref:blazor/index) lets you use C# in the browser alongside JavaScript.</span></span> <span data-ttu-id="f75fe-106">すべて .NET で記述された、サーバー側とクライアント側アプリのロジックを共有します。</span><span class="sxs-lookup"><span data-stu-id="f75fe-106">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="f75fe-107">Windows、macOS、Linux 上で開発および実行できること。</span><span class="sxs-lookup"><span data-stu-id="f75fe-107">Ability to develop and run on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="f75fe-108">オープン ソースで[コミュニティ重視](https://live.asp.net/)。</span><span class="sxs-lookup"><span data-stu-id="f75fe-108">Open-source and [community-focused](https://live.asp.net/).</span></span>
* <span data-ttu-id="f75fe-109">[最新のクライアント側フレームワーク](xref:blazor/index)と開発ワークフローの統合。</span><span class="sxs-lookup"><span data-stu-id="f75fe-109">Integration of [modern, client-side frameworks](xref:blazor/index) and development workflows.</span></span>
* <span data-ttu-id="f75fe-110">[gRPC](xref:grpc/index) を使用したリモート プロシージャ コール (RPC) サービスのホストのサポート。</span><span class="sxs-lookup"><span data-stu-id="f75fe-110">Support for hosting Remote Procedure Call (RPC) services using [gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="f75fe-111">クラウド対応で環境ベースの[構成システム](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f75fe-111">A cloud-ready, environment-based [configuration system](xref:fundamentals/configuration/index).</span></span>
* <span data-ttu-id="f75fe-112">組み込まれている[依存性の注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f75fe-112">Built-in [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="f75fe-113">軽量で[高パフォーマンス](https://github.com/aspnet/benchmarks)のモジュール化された HTTP 要求パイプライン。</span><span class="sxs-lookup"><span data-stu-id="f75fe-113">A lightweight, [high-performance](https://github.com/aspnet/benchmarks), and modular HTTP request pipeline.</span></span>
* <span data-ttu-id="f75fe-114">次がホストする機能です。</span><span class="sxs-lookup"><span data-stu-id="f75fe-114">Ability to host on the following:</span></span>
  * [<span data-ttu-id="f75fe-115">Kestrel</span><span class="sxs-lookup"><span data-stu-id="f75fe-115">Kestrel</span></span>](xref:fundamentals/servers/kestrel)
  * [<span data-ttu-id="f75fe-116">IIS</span><span class="sxs-lookup"><span data-stu-id="f75fe-116">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="f75fe-117">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="f75fe-117">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys)
  * [<span data-ttu-id="f75fe-118">Nginx</span><span class="sxs-lookup"><span data-stu-id="f75fe-118">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="f75fe-119">Apache</span><span class="sxs-lookup"><span data-stu-id="f75fe-119">Apache</span></span>](xref:host-and-deploy/linux-apache)
  * [<span data-ttu-id="f75fe-120">Docker</span><span class="sxs-lookup"><span data-stu-id="f75fe-120">Docker</span></span>](xref:host-and-deploy/docker/index)
* <span data-ttu-id="f75fe-121">[side-by-side でのバージョン管理](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)。</span><span class="sxs-lookup"><span data-stu-id="f75fe-121">[Side-by-side versioning](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level).</span></span>
* <span data-ttu-id="f75fe-122">最新の Web 開発を簡単にするツール。</span><span class="sxs-lookup"><span data-stu-id="f75fe-122">Tooling that simplifies modern web development.</span></span>

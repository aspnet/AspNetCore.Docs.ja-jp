---
title: ASP.NET Core Blazor Server のホストと展開
author: guardrex
description: ASP.NET Core を使用して Blazor Server アプリをホストおよびデプロイする方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: 75682171a59a610a24364778616774c49257d2ad
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279844"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="4cf88-103">Blazor Server のホストと展開</span><span class="sxs-lookup"><span data-stu-id="4cf88-103">Host and deploy Blazor Server</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="4cf88-104">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="4cf88-104">Host configuration values</span></span>

<span data-ttu-id="4cf88-105">[Blazor Server アプリ](xref:blazor/hosting-models#blazor-server)では、[汎用ホスト構成値](xref:fundamentals/host/generic-host#host-configuration)を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-105">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="4cf88-106">配置</span><span class="sxs-lookup"><span data-stu-id="4cf88-106">Deployment</span></span>

<span data-ttu-id="4cf88-107">[Blazor Server のホスティング モデル](xref:blazor/hosting-models#blazor-server)を使用する場合、Blazor はサーバー上で ASP.NET Core アプリ内から実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-107">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="4cf88-108">UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-108">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="4cf88-109">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="4cf88-109">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="4cf88-110">Visual Studio には **Blazor Server アプリ** プロジェクト テンプレートが含まれています ([`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorserverside` テンプレート)。</span><span class="sxs-lookup"><span data-stu-id="4cf88-110">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="4cf88-111">スケーラビリティ</span><span class="sxs-lookup"><span data-stu-id="4cf88-111">Scalability</span></span>

<span data-ttu-id="4cf88-112">Blazor Server アプリで使用できるインフラストラクチャを最大限に活用できるようにデプロイを計画します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-112">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="4cf88-113">Blazor Server アプリのスケーラビリティに対処するには、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cf88-113">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="4cf88-114">Blazor Server アプリの基礎</span><span class="sxs-lookup"><span data-stu-id="4cf88-114">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="4cf88-115">展開サーバー</span><span class="sxs-lookup"><span data-stu-id="4cf88-115">Deployment server</span></span>

<span data-ttu-id="4cf88-116">1 台のサーバーのスケーラビリティ (スケールアップ) を検討する場合、アプリに使用できるメモリは、ユーザーの要求が増えたときにアプリによって使い果たされる最初のリソースになります。</span><span class="sxs-lookup"><span data-stu-id="4cf88-116">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="4cf88-117">サーバー上の使用可能なメモリは、以下の影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-117">The available memory on the server affects the:</span></span>

* <span data-ttu-id="4cf88-118">サーバーがサポートできるアクティブ回線の数。</span><span class="sxs-lookup"><span data-stu-id="4cf88-118">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="4cf88-119">クライアントでの UI の待機時間。</span><span class="sxs-lookup"><span data-stu-id="4cf88-119">UI latency on the client.</span></span>

<span data-ttu-id="4cf88-120">セキュリティで保護されたスケーラブルな Blazor サーバー アプリを構築するためのガイダンスについては、「<xref:blazor/security/server/threat-mitigation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cf88-120">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="4cf88-121">各回線では、最小限の *Hello World* スタイルのアプリに約 250 KB のメモリが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-121">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="4cf88-122">回線のサイズは、アプリのコードと各コンポーネントに関連付けられている状態の保守要件によって変わります。</span><span class="sxs-lookup"><span data-stu-id="4cf88-122">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="4cf88-123">アプリとインフラストラクチャの開発時にはリソースのニーズを測定することをお勧めしますが、展開ターゲットを計画する際に、次のベースラインを出発点にすることができます。アプリで 5,000 人の同時ユーザーをサポートすることを想定している場合は、アプリに対して少なくとも 1.3 GB のサーバー メモリ (またはユーザーあたり最大 273 KB) の予算を割り当てること検討してください。</span><span class="sxs-lookup"><span data-stu-id="4cf88-123">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="4cf88-124">SignalR 構成</span><span class="sxs-lookup"><span data-stu-id="4cf88-124">SignalR configuration</span></span>

<span data-ttu-id="4cf88-125">Blazor Server アプリでは、ブラウザーとの通信に ASP.NET Core SignalR が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-125">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="4cf88-126">[SignalR のホストとスケーリングの条件](xref:signalr/publish-to-azure-web-app)は、Blazor Server アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-126">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="4cf88-127">Blazor は、待ち時間、信頼性、および[セキュリティ](xref:signalr/security)が低いために WebSocket を SignalR トランスポートとして使用する場合に最適です。</span><span class="sxs-lookup"><span data-stu-id="4cf88-127">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="4cf88-128">WebSocket が使用できない場合や、ロング ポーリングを使用するようにアプリが明示的に構成されている場合は、SignalR によってロング ポーリングが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-128">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="4cf88-129">Azure App Service にデプロイする場合は、サービスの Azure portal 設定で WebSocket を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-129">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="4cf88-130">Azure App Service 用にアプリを構成する方法の詳細については、[SignalR の発行ガイドライン](xref:signalr/publish-to-azure-web-app)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cf88-130">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="4cf88-131">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="4cf88-131">Azure SignalR Service</span></span>

<span data-ttu-id="4cf88-132">Blazor Server アプリには [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4cf88-132">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="4cf88-133">このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="4cf88-134">さらに、SignalR サービスのグローバル リーチとハイパフォーマンスのデータ センターは、地理的条件による待機時間の短縮に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4cf88-135">[WebSocket](https://wikipedia.org/wiki/WebSocket) が無効になっている場合、Azure App Service によって、リアルタイム接続のシミュレーションが HTTP ロング ポーリングを使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-135">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="4cf88-136">HTTP ロング ポーリングは、WebSocket を有効にして、クライアントとサーバー間の接続のシミュレーションにポーリングを使用せずに実行する場合よりも著しく遅くなります。</span><span class="sxs-lookup"><span data-stu-id="4cf88-136">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="4cf88-137">Azure App Service にデプロイされた Blazor Server アプリには WebSocket を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4cf88-137">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="4cf88-138">[Azure SignalR Service](xref:signalr/scale#azure-signalr-service) では、既定で WebSocket が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-138">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="4cf88-139">アプリで Azure SignalR Service を使用しない場合は、「<xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cf88-139">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="4cf88-140">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cf88-140">For more information, see:</span></span>
>
> * [<span data-ttu-id="4cf88-141">Azure SignalR Service とは</span><span class="sxs-lookup"><span data-stu-id="4cf88-141">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="4cf88-142">Azure SignalR Service のパフォーマンス ガイド</span><span class="sxs-lookup"><span data-stu-id="4cf88-142">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="4cf88-143">構成</span><span class="sxs-lookup"><span data-stu-id="4cf88-143">Configuration</span></span>

<span data-ttu-id="4cf88-144">Azure SignalR サービス用にアプリを構成するには、そのアプリで "*スティッキー セッション*" をサポートする必要があります。それにより、クライアントは [プリレンダリングのときに同じサーバーにリダイレクトされます](xref:blazor/hosting-models#connection-to-the-server)。</span><span class="sxs-lookup"><span data-stu-id="4cf88-144">To configure an app for the Azure SignalR Service, the app must support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="4cf88-145">`ServerStickyMode` オプションまたは構成値は `Required` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-145">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="4cf88-146">通常、アプリでは次の方法のいずれか **_1 つ_** を使用して構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-146">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="4cf88-147">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4cf88-147">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="4cf88-148">構成 (次の方法のいずれか **_1 つ_** を使用):</span><span class="sxs-lookup"><span data-stu-id="4cf88-148">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="4cf88-149">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4cf88-149">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="4cf88-150">Azure portal で App Service の **[構成]**  >  **[アプリケーションの設定]** (**名前**: `Azure__SignalR__StickyServerMode`、**値**: `Required`)。</span><span class="sxs-lookup"><span data-stu-id="4cf88-150">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure__SignalR__StickyServerMode`, **Value**: `Required`).</span></span> <span data-ttu-id="4cf88-151">この方法は、[Azure SignalR サービスをプロビジョニングする](#provision-the-azure-signalr-service)場合に、アプリに自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-151">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-signalr-service"></a><span data-ttu-id="4cf88-152">Azure SignalR サービスをプロビジョニングする</span><span class="sxs-lookup"><span data-stu-id="4cf88-152">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="4cf88-153">Visual Studio でアプリに合わせて Azure SignalR サービスをプロビジョニングするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="4cf88-153">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="4cf88-154">Blazor Server アプリ用に、Visual Studio に Azure アプリ発行プロファイルを作成する。</span><span class="sxs-lookup"><span data-stu-id="4cf88-154">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="4cf88-155">プロファイルに **Azure SignalR Service** の依存関係を追加する。</span><span class="sxs-lookup"><span data-stu-id="4cf88-155">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="4cf88-156">Azure サブスクリプションに、アプリに割り当てる既存の Azure SignalR Service のインスタンスがない場合は、 **[新しい Azure SignalR Service のインスタンスを作成する]** を選択して新しいサービス インスタンスをプロビジョニングします。</span><span class="sxs-lookup"><span data-stu-id="4cf88-156">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="4cf88-157">Azure にアプリを公開します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-157">Publish the app to Azure.</span></span>

<span data-ttu-id="4cf88-158">Visual Studio で Azure SignalR サービスを自動的にプロビジョニングすると、["*スティッキー セッション*"](#configuration) が有効になり、SignalR 接続文字列が App Service の構成に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-158">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="4cf88-159">IIS</span><span class="sxs-lookup"><span data-stu-id="4cf88-159">IIS</span></span>

<span data-ttu-id="4cf88-160">IIS を使用する場合は、次を有効にします。</span><span class="sxs-lookup"><span data-stu-id="4cf88-160">When using IIS, enable:</span></span>

* <span data-ttu-id="4cf88-161">[IIS での WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis)</span><span class="sxs-lookup"><span data-stu-id="4cf88-161">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="4cf88-162">[アプリケーション要求ルーティング処理でのスティッキー セッション](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="4cf88-162">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="4cf88-163">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="4cf88-163">Kubernetes</span></span>

<span data-ttu-id="4cf88-164">次の[スティッキー セッションに対する Kubernetes 注釈](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)を使用して、イングレス定義を作成します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-164">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="4cf88-165">Nginx を使用した Linux</span><span class="sxs-lookup"><span data-stu-id="4cf88-165">Linux with Nginx</span></span>

<span data-ttu-id="4cf88-166">SignalR WebSocket が正常に機能するには、プロキシの `Upgrade` と `Connection` のヘッダーが次の値に設定されていること、および `$connection_upgrade` が次のどちらかにマップされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-166">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="4cf88-167">アップグレード ヘッダーの値 (既定)。</span><span class="sxs-lookup"><span data-stu-id="4cf88-167">The Upgrade header value by default.</span></span>
* <span data-ttu-id="4cf88-168">アップグレード ヘッダーが存在しないか、空である場合は `close`。</span><span class="sxs-lookup"><span data-stu-id="4cf88-168">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="4cf88-169">詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cf88-169">For more information, see the following articles:</span></span>

* [<span data-ttu-id="4cf88-170">WebSocket プロキシとしての NGINX</span><span class="sxs-lookup"><span data-stu-id="4cf88-170">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="4cf88-171">WebSocket プロキシ</span><span class="sxs-lookup"><span data-stu-id="4cf88-171">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="4cf88-172">Apache を使用した Linux</span><span class="sxs-lookup"><span data-stu-id="4cf88-172">Linux with Apache</span></span>

<span data-ttu-id="4cf88-173">Linux 上の Apache の背後に Blazor アプリをホストするには、HTTP および Websocket トラフィック用に `ProxyPass` を構成します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-173">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="4cf88-174">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-174">In the following example:</span></span>

* <span data-ttu-id="4cf88-175">Kestrel サーバーは、ホスト コンピューター上で実行されています。</span><span class="sxs-lookup"><span data-stu-id="4cf88-175">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="4cf88-176">このアプリは、ポート 5000 でトラフィックをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="4cf88-176">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="4cf88-177">次のモジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="4cf88-177">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="4cf88-178">WebSockets エラーをブラウザ コンソールで確認します。</span><span class="sxs-lookup"><span data-stu-id="4cf88-178">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="4cf88-179">エラーの例:</span><span class="sxs-lookup"><span data-stu-id="4cf88-179">Example errors:</span></span>

* <span data-ttu-id="4cf88-180">Firefox は ws://the-domain-name.tld/_blazor?id=XXX のサーバーに接続を確立できません。</span><span class="sxs-lookup"><span data-stu-id="4cf88-180">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="4cf88-181">エラー :'WebSockets' の転送の開始に失敗しました。エラー :転送のエラーがありました。</span><span class="sxs-lookup"><span data-stu-id="4cf88-181">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="4cf88-182">エラー :'LongPolling' の転送の開始に失敗しました。TypeError: this.transport は定義されていません</span><span class="sxs-lookup"><span data-stu-id="4cf88-182">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="4cf88-183">エラー :利用可能ないかなる転送を使用しても、サーバーに接続できませんでした。</span><span class="sxs-lookup"><span data-stu-id="4cf88-183">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="4cf88-184">WebSockets が失敗しました</span><span class="sxs-lookup"><span data-stu-id="4cf88-184">WebSockets failed</span></span>
* <span data-ttu-id="4cf88-185">エラー :接続が 'Connected' 状態ではない場合、データは送信できません。</span><span class="sxs-lookup"><span data-stu-id="4cf88-185">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="4cf88-186">詳細については、[Apache のドキュメント](https://httpd.apache.org/docs/current/mod/mod_proxy.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cf88-186">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="4cf88-187">ネットワーク待機時間の測定</span><span class="sxs-lookup"><span data-stu-id="4cf88-187">Measure network latency</span></span>

<span data-ttu-id="4cf88-188">次の例に示すように、[JS 相互運用機能](xref:blazor/call-javascript-from-dotnet)を使用してネットワーク待機時間を測定できます。</span><span class="sxs-lookup"><span data-stu-id="4cf88-188">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="4cf88-189">妥当な UI エクスペリエンスを実現するために、UI の待機時間を 250 ミリ秒以下にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4cf88-189">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>

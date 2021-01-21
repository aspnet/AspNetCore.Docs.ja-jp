---
title: Windows サービスでの ASP.NET Core のホスト
author: rick-anderson
description: Windows サービスで ASP.NET Core アプリケーションをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: 63267bf938c6d16b8a1b13940a4b3f8a02d1a1e4
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252748"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="d866c-103">Windows サービスでの ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="d866c-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d866c-104">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="d866c-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d866c-105">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="d866c-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d866c-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d866c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d866c-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d866c-107">Prerequisites</span></span>

* [<span data-ttu-id="d866c-108">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="d866c-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d866c-109">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="d866c-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="d866c-110">ワーカー サービス テンプレート</span><span class="sxs-lookup"><span data-stu-id="d866c-110">Worker Service template</span></span>

<span data-ttu-id="d866c-111">ASP.NET Core ワーカー サービス テンプレートは、実行時間が長いサービス アプリを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="d866c-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="d866c-112">テンプレートを Windows サービス アプリの基礎として使用するには:</span><span class="sxs-lookup"><span data-stu-id="d866c-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="d866c-113">.NET Core テンプレートからワーカー サービス アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="d866c-114">「[アプリの構成](#app-configuration)」セクションのガイダンスに従って、Windows サービスとして実行できるようにワーカー サービス アプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="d866c-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="d866c-115">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="d866c-115">App configuration</span></span>

<span data-ttu-id="d866c-116">アプリには、[Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)のパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="d866c-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="d866c-117">ホストのビルド時に `IHostBuilder.UseWindowsService` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="d866c-118">アプリが Windows サービスとして実行している場合、メソッドは</span><span class="sxs-lookup"><span data-stu-id="d866c-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="d866c-119">ホストの有効期間を `WindowsServiceLifetime` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="d866c-120">[コンテンツ ルート](xref:fundamentals/index#content-root)を [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) に設定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="d866c-121">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="d866c-122">イベント ログへのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d866c-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="d866c-123">アプリケーション名が既定のソース名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="d866c-124">既定のログ レベルは、ホストを構築するために `CreateDefaultBuilder` が呼び出される ASP.NET Core テンプレートに基づくアプリに対して "*警告*" 以上です。</span><span class="sxs-lookup"><span data-stu-id="d866c-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="d866c-125">既定のログ レベルを、 *appsettings.json* /*appsettings.{Environment}.json* の `Logging:EventLog:LogLevel:Default` キーまたは他の構成プロバイダーでオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d866c-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="d866c-126">管理者のみが新しいイベント ソースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="d866c-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="d866c-127">アプリケーション名を使用して、イベント ソースを作成できない場合、警告が *アプリケーション* ソースに記録され、イベント ログが無効になります。</span><span class="sxs-lookup"><span data-stu-id="d866c-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="d866c-128">*Program.cs* の `CreateHostBuilder` の場合:</span><span class="sxs-lookup"><span data-stu-id="d866c-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="d866c-129">このトピックには、次のサンプル アプリが付属しています。</span><span class="sxs-lookup"><span data-stu-id="d866c-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="d866c-130">バックグラウンド ワーカー サービスのサンプル: バックグラウンド タスク用に[ホステッド サービス](xref:fundamentals/host/hosted-services)を使用する[ワーカー サービス テンプレート](#worker-service-template)に基づく、非 Web アプリのサンプルです。</span><span class="sxs-lookup"><span data-stu-id="d866c-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="d866c-131">Web アプリ サービスのサンプル: バックグラウンド タスク用の[ホステッド サービス](xref:fundamentals/host/hosted-services)を使用する Windows サービスとして実行される Razor Pages Web アプリのサンプルです。</span><span class="sxs-lookup"><span data-stu-id="d866c-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="d866c-132">MVC のガイダンスについては、「<xref:mvc/overview>」と「<xref:migration/22-to-30>」にある記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="d866c-133">配置の種類</span><span class="sxs-lookup"><span data-stu-id="d866c-133">Deployment type</span></span>

<span data-ttu-id="d866c-134">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d866c-135">SDK</span><span class="sxs-lookup"><span data-stu-id="d866c-135">SDK</span></span>

<span data-ttu-id="d866c-136">Razor Pages または MVC フレームワークを使用する Web アプリ ベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d866c-137">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d866c-138">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="d866c-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d866c-139">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="d866c-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d866c-140">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d866c-141">[Web SDK](#sdk) を使用している場合、*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d866c-142">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d866c-143">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="d866c-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d866c-144">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="d866c-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d866c-145">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d866c-146">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="d866c-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d866c-147">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d866c-148">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d866c-149">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数形) を使用します。</span><span class="sxs-lookup"><span data-stu-id="d866c-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d866c-150">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="d866c-151">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="d866c-151">Service user account</span></span>

<span data-ttu-id="d866c-152">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="d866c-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d866c-153">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="d866c-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-154">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="d866c-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d866c-155">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d866c-156">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="d866c-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d866c-157">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d866c-158">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="d866c-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d866c-159">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d866c-160">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="d866c-160">Log on as a service rights</span></span>

<span data-ttu-id="d866c-161">サービス ユーザー アカウントに *サービスとしてログオン* 権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d866c-162">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d866c-163">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d866c-164">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d866c-165">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d866c-166">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d866c-167">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d866c-168">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-168">Select **Advanced**.</span></span> <span data-ttu-id="d866c-169">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-169">Select **Find Now**.</span></span> <span data-ttu-id="d866c-170">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-170">Select the user account from the list.</span></span> <span data-ttu-id="d866c-171">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-171">Select **OK**.</span></span> <span data-ttu-id="d866c-172">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d866c-173">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="d866c-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d866c-174">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="d866c-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d866c-175">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="d866c-175">Create a service</span></span>

<span data-ttu-id="d866c-176">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="d866c-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d866c-177">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d866c-178">`{EXE PATH}`:ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d866c-179">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="d866c-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d866c-180">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d866c-181">`{DOMAIN OR COMPUTER NAME\USER}`:サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d866c-182">`{SERVICE NAME}`:サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d866c-183">`{EXE FILE PATH}`:アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d866c-184">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="d866c-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d866c-185">`{DESCRIPTION}`:サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d866c-186">`{DISPLAY NAME}`:サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d866c-187">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="d866c-187">Start a service</span></span>

<span data-ttu-id="d866c-188">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="d866c-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-189">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="d866c-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d866c-190">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="d866c-190">Determine a service's status</span></span>

<span data-ttu-id="d866c-191">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="d866c-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-192">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d866c-193">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="d866c-193">Stop a service</span></span>

<span data-ttu-id="d866c-194">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="d866c-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d866c-195">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="d866c-195">Remove a service</span></span>

<span data-ttu-id="d866c-196">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d866c-197">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d866c-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d866c-198">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d866c-199">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d866c-200">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="d866c-200">Configure endpoints</span></span>

<span data-ttu-id="d866c-201">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d866c-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d866c-202">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d866c-203">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d866c-204">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="d866c-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d866c-205">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d866c-206">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d866c-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d866c-207">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="d866c-207">Current directory and content root</span></span>

<span data-ttu-id="d866c-208">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory%2A> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="d866c-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory%2A> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d866c-209">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d866c-210">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="d866c-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="d866c-211">ContentRootPath または ContentRootFileProvider を使用する</span><span class="sxs-lookup"><span data-stu-id="d866c-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="d866c-212">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) または <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> を使用して、アプリのリソースを見つけます。</span><span class="sxs-lookup"><span data-stu-id="d866c-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="d866c-213">アプリがサービスとして実行されると、<xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> によって <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> が [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="d866c-214">アプリの既定設定ファイルである *appsettings.json* と *appsettings.{Environment}.json* は、[ホストの構築中に CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host) を呼び出すことで、アプリのコンテンツ ルートから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d866c-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="d866c-215"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> の開発者コードによって読み込まれた他の設定ファイルについては、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>.</span></span> <span data-ttu-id="d866c-216">次の例では、*custom_settings.json* ファイルはアプリのコンテンツ ルートに存在しており、明示的にベース パスを設定しなくても読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d866c-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="d866c-217">Windows サービス アプリからは *C:\\WINDOWS\\system32* フォルダーが現在のディレクトリとして返されるため、<xref:System.IO.Directory.GetCurrentDirectory%2A> を使用してリソース パスを取得しないでください。</span><span class="sxs-lookup"><span data-stu-id="d866c-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory%2A> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d866c-218">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="d866c-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d866c-219">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d866c-220">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="d866c-220">Troubleshoot</span></span>

<span data-ttu-id="d866c-221">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d866c-222">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="d866c-222">Common errors</span></span>

* <span data-ttu-id="d866c-223">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="d866c-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d866c-224">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの **発行済み** 出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="d866c-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d866c-225">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d866c-226">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d866c-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d866c-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d866c-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d866c-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d866c-229">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d866c-230">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d866c-231">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="d866c-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d866c-232">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d866c-233">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d866c-234">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d866c-235">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d866c-236">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="d866c-236">System and Application Event Logs</span></span>

<span data-ttu-id="d866c-237">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="d866c-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d866c-238">[スタート] メニューを開き、*イベント ビューアー* を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d866c-239">**イベント ビューアー** で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d866c-240">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d866c-241">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d866c-242">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="d866c-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d866c-243">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="d866c-243">Run the app at a command prompt</span></span>

<span data-ttu-id="d866c-244">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="d866c-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d866c-245">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d866c-246">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d866c-247">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="d866c-247">Clear package caches</span></span>

<span data-ttu-id="d866c-248">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d866c-249">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d866c-250">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="d866c-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d866c-251">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d866c-252">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="d866c-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d866c-253">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d866c-254">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d866c-255">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="d866c-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d866c-256">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d866c-257">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="d866c-257">Slow or hanging app</span></span>

<span data-ttu-id="d866c-258">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d866c-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d866c-259">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="d866c-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="d866c-260">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="d866c-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d866c-261">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d866c-262">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d866c-263">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d866c-264">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d866c-265">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="d866c-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d866c-266">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d866c-267">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d866c-268">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="d866c-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d866c-269">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d866c-270">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="d866c-270">Analyze the dump</span></span>

<span data-ttu-id="d866c-271">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="d866c-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d866c-272">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d866c-273">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d866c-273">Additional resources</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="d866c-274">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel/endpoints) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="d866c-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* <span data-ttu-id="d866c-275">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="d866c-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d866c-276">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="d866c-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d866c-277">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="d866c-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d866c-278">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d866c-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d866c-279">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d866c-279">Prerequisites</span></span>

* [<span data-ttu-id="d866c-280">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="d866c-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d866c-281">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="d866c-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d866c-282">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="d866c-282">App configuration</span></span>

<span data-ttu-id="d866c-283">アプリでは、[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) と [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) へのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="d866c-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d866c-284">サービスの外部で実行しているときにテストとデバッグを行うには、アプリがサービスとして実行しているかコンソール アプリとして実行しているかを判別するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d866c-285">デバッガーがアタッチされているか、`--console` スイッチが存在するかを検査します。</span><span class="sxs-lookup"><span data-stu-id="d866c-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d866c-286">いずれかの条件が満たされる場合 (アプリがサービスとして実行していない場合)、<xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d866c-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d866c-287">条件が満たされない場合 (アプリがサービスとして実行している場合):</span><span class="sxs-lookup"><span data-stu-id="d866c-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d866c-288"><xref:System.IO.Directory.SetCurrentDirectory*> を呼び出し、アプリの発行場所のパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="d866c-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d866c-289">パスを取得するために <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出さないでください。<xref:System.IO.Directory.GetCurrentDirectory*> が呼び出されると、Windows サービス アプリは *C:\\WINDOWS\\system32* フォルダーを戻すためです。</span><span class="sxs-lookup"><span data-stu-id="d866c-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d866c-290">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d866c-291">`CreateWebHostBuilder` でアプリを構成する前に、この手順に従います。</span><span class="sxs-lookup"><span data-stu-id="d866c-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d866c-292"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> を呼び出して、アプリをサービスとして実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d866c-293">[コマンドライン構成プロバイダー](xref:fundamentals/configuration/index#command-line-configuration-provider)では、コマンドライン引数に名前と値の組が必要であるため、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> が引数を受け取る前に `--console` スイッチは引数から削除されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d866c-294">Windows イベント ログに書き込むには、EventLog プロバイダーを <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> に追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d866c-295">*appsettings.Production.json* ファイルで `Logging:LogLevel:Default` キーを使用してログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d866c-296">サンプル アプリの次の例では、アプリ内で有効期間イベントを処理するために、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の代わりに `RunAsCustomService` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="d866c-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d866c-297">詳しくは、「[イベントの開始と停止を扱う](#handle-starting-and-stopping-events)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d866c-298">配置の種類</span><span class="sxs-lookup"><span data-stu-id="d866c-298">Deployment type</span></span>

<span data-ttu-id="d866c-299">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d866c-300">SDK</span><span class="sxs-lookup"><span data-stu-id="d866c-300">SDK</span></span>

<span data-ttu-id="d866c-301">Razor Pages または MVC フレームワークを使用する Web アプリ ベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d866c-302">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d866c-303">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="d866c-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d866c-304">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="d866c-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d866c-305">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d866c-306">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) にはターゲット フレームワークが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d866c-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d866c-307">次の例では、RID が `win7-x64` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="d866c-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d866c-308">`<SelfContained>` プロパティが `false` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="d866c-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d866c-309">これらのプロパティによって、Windows 用の実行可能ファイル ( *.exe*) と共有 .NET Core フレームワークに依存するアプリを生成するよう SDK に指示します。</span><span class="sxs-lookup"><span data-stu-id="d866c-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d866c-310">*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d866c-311">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d866c-312">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="d866c-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d866c-313">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="d866c-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d866c-314">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d866c-315">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="d866c-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d866c-316">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d866c-317">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d866c-318">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数形) を使用します。</span><span class="sxs-lookup"><span data-stu-id="d866c-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d866c-319">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d866c-320">`<SelfContained>` プロパティが `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="d866c-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d866c-321">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="d866c-321">Service user account</span></span>

<span data-ttu-id="d866c-322">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="d866c-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d866c-323">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="d866c-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-324">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="d866c-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d866c-325">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d866c-326">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="d866c-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d866c-327">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d866c-328">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="d866c-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d866c-329">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d866c-330">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="d866c-330">Log on as a service rights</span></span>

<span data-ttu-id="d866c-331">サービス ユーザー アカウントに *サービスとしてログオン* 権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d866c-332">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d866c-333">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d866c-334">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d866c-335">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d866c-336">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d866c-337">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d866c-338">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-338">Select **Advanced**.</span></span> <span data-ttu-id="d866c-339">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-339">Select **Find Now**.</span></span> <span data-ttu-id="d866c-340">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-340">Select the user account from the list.</span></span> <span data-ttu-id="d866c-341">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-341">Select **OK**.</span></span> <span data-ttu-id="d866c-342">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d866c-343">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="d866c-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d866c-344">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="d866c-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d866c-345">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="d866c-345">Create a service</span></span>

<span data-ttu-id="d866c-346">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="d866c-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d866c-347">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d866c-348">`{EXE PATH}`:ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-348">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d866c-349">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="d866c-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d866c-350">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d866c-351">`{DOMAIN OR COMPUTER NAME\USER}`:サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-351">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d866c-352">`{SERVICE NAME}`:サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-352">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d866c-353">`{EXE FILE PATH}`:アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-353">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d866c-354">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="d866c-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d866c-355">`{DESCRIPTION}`:サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-355">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d866c-356">`{DISPLAY NAME}`:サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-356">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d866c-357">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="d866c-357">Start a service</span></span>

<span data-ttu-id="d866c-358">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="d866c-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-359">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="d866c-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d866c-360">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="d866c-360">Determine a service's status</span></span>

<span data-ttu-id="d866c-361">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="d866c-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-362">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d866c-363">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="d866c-363">Stop a service</span></span>

<span data-ttu-id="d866c-364">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="d866c-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d866c-365">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="d866c-365">Remove a service</span></span>

<span data-ttu-id="d866c-366">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d866c-367">イベントの開始と停止を扱う</span><span class="sxs-lookup"><span data-stu-id="d866c-367">Handle starting and stopping events</span></span>

<span data-ttu-id="d866c-368"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>、および <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> イベントを処理するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d866c-369">`OnStarting`、`OnStarted`、および `OnStopping` メソッドを使用して、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d866c-370">`CustomWebHostService` を <xref:System.ServiceProcess.ServiceBase.Run*> に渡す <xref:Microsoft.AspNetCore.Hosting.IWebHost> の拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d866c-371">`Program.Main` で、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ではなく、拡張メソッド `RunAsCustomService` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d866c-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d866c-372">`Program.Main` 内の <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の場所を確認するには、「[展開の種類](#deployment-type)」セクションに示されているコード サンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d866c-373">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d866c-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d866c-374">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d866c-375">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d866c-376">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="d866c-376">Configure endpoints</span></span>

<span data-ttu-id="d866c-377">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d866c-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d866c-378">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d866c-379">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d866c-380">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="d866c-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d866c-381">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d866c-382">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d866c-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d866c-383">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="d866c-383">Current directory and content root</span></span>

<span data-ttu-id="d866c-384">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="d866c-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d866c-385">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d866c-386">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="d866c-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d866c-387">アプリのフォルダーにコンテンツ ルート パスを設定する</span><span class="sxs-lookup"><span data-stu-id="d866c-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d866c-388"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> は、サービスの作成時に `binPath` 引数に指定されたものと同じパスです。</span><span class="sxs-lookup"><span data-stu-id="d866c-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d866c-389">`GetCurrentDirectory` を呼び出して設定ファイルへのパスを作成する代わりに、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)へのパスを指定して <xref:System.IO.Directory.SetCurrentDirectory*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d866c-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d866c-390">`Program.Main` で、サービスの実行可能ファイルがあるフォルダーへのパスを判別し、そのパスを使用してアプリのコンテンツ ルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="d866c-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d866c-391">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="d866c-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d866c-392">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d866c-393">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="d866c-393">Troubleshoot</span></span>

<span data-ttu-id="d866c-394">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d866c-395">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="d866c-395">Common errors</span></span>

* <span data-ttu-id="d866c-396">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="d866c-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d866c-397">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの **発行済み** 出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="d866c-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d866c-398">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d866c-399">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d866c-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d866c-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d866c-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d866c-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d866c-402">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d866c-403">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d866c-404">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="d866c-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d866c-405">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d866c-406">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d866c-407">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d866c-408">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d866c-409">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="d866c-409">System and Application Event Logs</span></span>

<span data-ttu-id="d866c-410">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="d866c-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d866c-411">[スタート] メニューを開き、*イベント ビューアー* を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d866c-412">**イベント ビューアー** で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d866c-413">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d866c-414">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d866c-415">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="d866c-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d866c-416">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="d866c-416">Run the app at a command prompt</span></span>

<span data-ttu-id="d866c-417">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="d866c-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d866c-418">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d866c-419">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d866c-420">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="d866c-420">Clear package caches</span></span>

<span data-ttu-id="d866c-421">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d866c-422">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d866c-423">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="d866c-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d866c-424">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d866c-425">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="d866c-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d866c-426">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d866c-427">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d866c-428">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="d866c-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d866c-429">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d866c-430">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="d866c-430">Slow or hanging app</span></span>

<span data-ttu-id="d866c-431">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d866c-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d866c-432">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="d866c-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="d866c-433">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="d866c-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d866c-434">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d866c-435">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-435">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d866c-436">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d866c-437">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d866c-438">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="d866c-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d866c-439">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d866c-440">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d866c-441">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="d866c-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d866c-442">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d866c-443">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="d866c-443">Analyze the dump</span></span>

<span data-ttu-id="d866c-444">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="d866c-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d866c-445">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d866c-446">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d866c-446">Additional resources</span></span>

* <span data-ttu-id="d866c-447">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="d866c-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d866c-448">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="d866c-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d866c-449">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="d866c-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d866c-450">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d866c-450">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d866c-451">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d866c-451">Prerequisites</span></span>

* [<span data-ttu-id="d866c-452">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="d866c-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d866c-453">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="d866c-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d866c-454">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="d866c-454">App configuration</span></span>

<span data-ttu-id="d866c-455">アプリでは、[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) と [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) へのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="d866c-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d866c-456">サービスの外部で実行しているときにテストとデバッグを行うには、アプリがサービスとして実行しているかコンソール アプリとして実行しているかを判別するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d866c-457">デバッガーがアタッチされているか、`--console` スイッチが存在するかを検査します。</span><span class="sxs-lookup"><span data-stu-id="d866c-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d866c-458">いずれかの条件が満たされる場合 (アプリがサービスとして実行していない場合)、<xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d866c-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d866c-459">条件が満たされない場合 (アプリがサービスとして実行している場合):</span><span class="sxs-lookup"><span data-stu-id="d866c-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d866c-460"><xref:System.IO.Directory.SetCurrentDirectory*> を呼び出し、アプリの発行場所のパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="d866c-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d866c-461">パスを取得するために <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出さないでください。<xref:System.IO.Directory.GetCurrentDirectory*> が呼び出されると、Windows サービス アプリは *C:\\WINDOWS\\system32* フォルダーを戻すためです。</span><span class="sxs-lookup"><span data-stu-id="d866c-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d866c-462">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d866c-463">`CreateWebHostBuilder` でアプリを構成する前に、この手順に従います。</span><span class="sxs-lookup"><span data-stu-id="d866c-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d866c-464"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> を呼び出して、アプリをサービスとして実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d866c-465">[コマンドライン構成プロバイダー](xref:fundamentals/configuration/index#command-line-configuration-provider)では、コマンドライン引数に名前と値の組が必要であるため、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> が引数を受け取る前に `--console` スイッチは引数から削除されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d866c-466">Windows イベント ログに書き込むには、EventLog プロバイダーを <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> に追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d866c-467">*appsettings.Production.json* ファイルで `Logging:LogLevel:Default` キーを使用してログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d866c-468">サンプル アプリの次の例では、アプリ内で有効期間イベントを処理するために、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の代わりに `RunAsCustomService` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="d866c-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d866c-469">詳しくは、「[イベントの開始と停止を扱う](#handle-starting-and-stopping-events)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d866c-470">配置の種類</span><span class="sxs-lookup"><span data-stu-id="d866c-470">Deployment type</span></span>

<span data-ttu-id="d866c-471">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d866c-472">SDK</span><span class="sxs-lookup"><span data-stu-id="d866c-472">SDK</span></span>

<span data-ttu-id="d866c-473">Razor Pages または MVC フレームワークを使用する Web アプリ ベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d866c-474">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d866c-475">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="d866c-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d866c-476">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="d866c-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d866c-477">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d866c-478">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) にはターゲット フレームワークが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d866c-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d866c-479">次の例では、RID が `win7-x64` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="d866c-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d866c-480">`<SelfContained>` プロパティが `false` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="d866c-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d866c-481">これらのプロパティによって、Windows 用の実行可能ファイル ( *.exe*) と共有 .NET Core フレームワークに依存するアプリを生成するよう SDK に指示します。</span><span class="sxs-lookup"><span data-stu-id="d866c-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d866c-482">`<UseAppHost>` プロパティが `true` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="d866c-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="d866c-483">このプロパティによって、サービスに FDD のアクティブ化パス (実行可能ファイル、 *.exe*) を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="d866c-484">*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d866c-485">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d866c-486">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="d866c-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d866c-487">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="d866c-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d866c-488">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d866c-489">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="d866c-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d866c-490">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d866c-491">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d866c-492">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数形) を使用します。</span><span class="sxs-lookup"><span data-stu-id="d866c-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d866c-493">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d866c-494">`<SelfContained>` プロパティが `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="d866c-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d866c-495">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="d866c-495">Service user account</span></span>

<span data-ttu-id="d866c-496">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="d866c-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d866c-497">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="d866c-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-498">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="d866c-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d866c-499">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d866c-500">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="d866c-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d866c-501">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d866c-502">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="d866c-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d866c-503">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d866c-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d866c-504">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="d866c-504">Log on as a service rights</span></span>

<span data-ttu-id="d866c-505">サービス ユーザー アカウントに *サービスとしてログオン* 権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d866c-506">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d866c-507">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d866c-508">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d866c-509">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d866c-510">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d866c-511">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d866c-512">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-512">Select **Advanced**.</span></span> <span data-ttu-id="d866c-513">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-513">Select **Find Now**.</span></span> <span data-ttu-id="d866c-514">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-514">Select the user account from the list.</span></span> <span data-ttu-id="d866c-515">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-515">Select **OK**.</span></span> <span data-ttu-id="d866c-516">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d866c-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d866c-517">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="d866c-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d866c-518">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="d866c-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d866c-519">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="d866c-519">Create a service</span></span>

<span data-ttu-id="d866c-520">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="d866c-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d866c-521">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d866c-522">`{EXE PATH}`:ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-522">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d866c-523">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="d866c-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d866c-524">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d866c-525">`{DOMAIN OR COMPUTER NAME\USER}`:サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-525">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d866c-526">`{SERVICE NAME}`:サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-526">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d866c-527">`{EXE FILE PATH}`:アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-527">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d866c-528">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="d866c-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d866c-529">`{DESCRIPTION}`:サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-529">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d866c-530">`{DISPLAY NAME}`:サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="d866c-530">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d866c-531">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="d866c-531">Start a service</span></span>

<span data-ttu-id="d866c-532">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="d866c-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-533">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="d866c-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d866c-534">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="d866c-534">Determine a service's status</span></span>

<span data-ttu-id="d866c-535">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="d866c-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d866c-536">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d866c-537">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="d866c-537">Stop a service</span></span>

<span data-ttu-id="d866c-538">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="d866c-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d866c-539">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="d866c-539">Remove a service</span></span>

<span data-ttu-id="d866c-540">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d866c-541">イベントの開始と停止を扱う</span><span class="sxs-lookup"><span data-stu-id="d866c-541">Handle starting and stopping events</span></span>

<span data-ttu-id="d866c-542"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>、および <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> イベントを処理するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d866c-543">`OnStarting`、`OnStarted`、および `OnStopping` メソッドを使用して、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d866c-544">`CustomWebHostService` を <xref:System.ServiceProcess.ServiceBase.Run*> に渡す <xref:Microsoft.AspNetCore.Hosting.IWebHost> の拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d866c-545">`Program.Main` で、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ではなく、拡張メソッド `RunAsCustomService` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d866c-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d866c-546">`Program.Main` 内の <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の場所を確認するには、「[展開の種類](#deployment-type)」セクションに示されているコード サンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d866c-547">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d866c-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d866c-548">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d866c-549">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d866c-550">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="d866c-550">Configure endpoints</span></span>

<span data-ttu-id="d866c-551">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d866c-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d866c-552">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d866c-553">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d866c-554">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="d866c-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d866c-555">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d866c-556">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d866c-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d866c-557">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="d866c-557">Current directory and content root</span></span>

<span data-ttu-id="d866c-558">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="d866c-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d866c-559">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d866c-560">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="d866c-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d866c-561">アプリのフォルダーにコンテンツ ルート パスを設定する</span><span class="sxs-lookup"><span data-stu-id="d866c-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d866c-562"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> は、サービスの作成時に `binPath` 引数に指定されたものと同じパスです。</span><span class="sxs-lookup"><span data-stu-id="d866c-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d866c-563">`GetCurrentDirectory` を呼び出して設定ファイルへのパスを作成する代わりに、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)へのパスを指定して <xref:System.IO.Directory.SetCurrentDirectory*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d866c-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d866c-564">`Program.Main` で、サービスの実行可能ファイルがあるフォルダーへのパスを判別し、そのパスを使用してアプリのコンテンツ ルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="d866c-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d866c-565">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="d866c-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d866c-566">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d866c-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d866c-567">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="d866c-567">Troubleshoot</span></span>

<span data-ttu-id="d866c-568">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d866c-569">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="d866c-569">Common errors</span></span>

* <span data-ttu-id="d866c-570">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="d866c-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d866c-571">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの **発行済み** 出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="d866c-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d866c-572">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d866c-573">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d866c-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d866c-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d866c-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d866c-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d866c-576">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d866c-577">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d866c-578">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="d866c-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d866c-579">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="d866c-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d866c-580">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d866c-581">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d866c-582">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="d866c-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d866c-583">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="d866c-583">System and Application Event Logs</span></span>

<span data-ttu-id="d866c-584">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="d866c-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d866c-585">[スタート] メニューを開き、*イベント ビューアー* を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="d866c-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d866c-586">**イベント ビューアー** で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d866c-587">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d866c-588">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="d866c-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d866c-589">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="d866c-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d866c-590">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="d866c-590">Run the app at a command prompt</span></span>

<span data-ttu-id="d866c-591">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="d866c-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d866c-592">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d866c-593">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d866c-594">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="d866c-594">Clear package caches</span></span>

<span data-ttu-id="d866c-595">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d866c-596">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d866c-597">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="d866c-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d866c-598">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d866c-599">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="d866c-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d866c-600">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="d866c-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d866c-601">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d866c-602">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="d866c-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d866c-603">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="d866c-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d866c-604">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="d866c-604">Slow or hanging app</span></span>

<span data-ttu-id="d866c-605">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d866c-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d866c-606">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="d866c-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="d866c-607">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="d866c-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d866c-608">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d866c-609">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-609">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d866c-610">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d866c-611">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="d866c-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d866c-612">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="d866c-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d866c-613">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="d866c-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d866c-614">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="d866c-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d866c-615">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="d866c-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d866c-616">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="d866c-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d866c-617">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="d866c-617">Analyze the dump</span></span>

<span data-ttu-id="d866c-618">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="d866c-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d866c-619">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d866c-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d866c-620">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d866c-620">Additional resources</span></span>

* <span data-ttu-id="d866c-621">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="d866c-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

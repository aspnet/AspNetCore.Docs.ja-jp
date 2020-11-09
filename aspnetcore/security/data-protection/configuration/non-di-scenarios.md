---
title: ASP.NET Core でのデータ保護に関する非 DI 対応シナリオ
author: rick-anderson
description: 依存関係の挿入によって提供されるサービスを使用できない、または使用したくないデータ保護シナリオをサポートする方法について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 03257596cafd9ec99f90b44d8fcb878b6747ba39
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052826"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a><span data-ttu-id="a8e96-103">ASP.NET Core でのデータ保護に関する非 DI 対応シナリオ</span><span class="sxs-lookup"><span data-stu-id="a8e96-103">Non-DI aware scenarios for Data Protection in ASP.NET Core</span></span>

<span data-ttu-id="a8e96-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a8e96-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a8e96-105">ASP.NET Core データ保護システムは通常、 [サービスコンテナーに追加](xref:security/data-protection/consumer-apis/overview) され、依存関係の注入 (DI) を介して依存コンポーネントによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="a8e96-105">The ASP.NET Core Data Protection system is normally [added to a service container](xref:security/data-protection/consumer-apis/overview) and consumed by dependent components via dependency injection (DI).</span></span> <span data-ttu-id="a8e96-106">ただし、特にシステムを既存のアプリケーションにインポートする場合は、これが実現できないか、または望ましくない場合があります。</span><span class="sxs-lookup"><span data-stu-id="a8e96-106">However, there are cases where this isn't feasible or desired, especially when importing the system into an existing app.</span></span>

<span data-ttu-id="a8e96-107">これらのシナリオをサポートするために、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) パッケージには具象型である [dataprotectionprovider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider)が用意されています。これにより、DI に依存せずにデータ保護を簡単に使用できます。</span><span class="sxs-lookup"><span data-stu-id="a8e96-107">To support these scenarios, the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) package provides a concrete type, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), which offers a simple way to use Data Protection without relying on DI.</span></span> <span data-ttu-id="a8e96-108">この `DataProtectionProvider` 型は [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider)を実装します。</span><span class="sxs-lookup"><span data-stu-id="a8e96-108">The `DataProtectionProvider` type implements [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider).</span></span> <span data-ttu-id="a8e96-109">`DataProtectionProvider`を構築するには、次のコードサンプルに示すように、プロバイダーの暗号化キーを格納する場所を示すために、 [DirectoryInfo](/dotnet/api/system.io.directoryinfo)インスタンスを提供する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a8e96-109">Constructing `DataProtectionProvider` only requires providing a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) instance to indicate where the provider's cryptographic keys should be stored, as seen in the following code sample:</span></span>

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

<span data-ttu-id="a8e96-110">既定では、 `DataProtectionProvider` 具象型は、ファイルシステムに永続化する前に未加工のキーマテリアルを暗号化しません。</span><span class="sxs-lookup"><span data-stu-id="a8e96-110">By default, the `DataProtectionProvider` concrete type doesn't encrypt raw key material before persisting it to the file system.</span></span> <span data-ttu-id="a8e96-111">これは、開発者がネットワーク共有を指し、データ保護システムが適切な保存キー暗号化メカニズムを自動的に推測できないシナリオをサポートするためです。</span><span class="sxs-lookup"><span data-stu-id="a8e96-111">This is to support scenarios where the developer points to a network share and the Data Protection system can't automatically deduce an appropriate at-rest key encryption mechanism.</span></span>

<span data-ttu-id="a8e96-112">また、 `DataProtectionProvider` 具象型では既定で [アプリが分離](xref:security/data-protection/configuration/overview#per-application-isolation) されません。</span><span class="sxs-lookup"><span data-stu-id="a8e96-112">Additionally, the `DataProtectionProvider` concrete type doesn't [isolate apps](xref:security/data-protection/configuration/overview#per-application-isolation) by default.</span></span> <span data-ttu-id="a8e96-113">同じキーディレクトリを使用するすべてのアプリは、 [目的のパラメーター](xref:security/data-protection/consumer-apis/purpose-strings) が一致する限り、ペイロードを共有できます。</span><span class="sxs-lookup"><span data-stu-id="a8e96-113">All apps using the same key directory can share payloads as long as their [purpose parameters](xref:security/data-protection/consumer-apis/purpose-strings) match.</span></span>

<span data-ttu-id="a8e96-114">[Dataprotectionprovider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)コンストラクターは、システムの動作を調整するために使用できるオプションの構成コールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="a8e96-114">The [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) constructor accepts an optional configuration callback that can be used to adjust the behaviors of the system.</span></span> <span data-ttu-id="a8e96-115">次のサンプルでは、 [Setapplicationname](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)への明示的な呼び出しを使用して分離を復元する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a8e96-115">The sample below demonstrates restoring isolation with an explicit call to [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname).</span></span> <span data-ttu-id="a8e96-116">このサンプルでは、Windows DPAPI を使用して永続化されたキーを自動的に暗号化するようにシステムを構成する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="a8e96-116">The sample also demonstrates configuring the system to automatically encrypt persisted keys using Windows DPAPI.</span></span> <span data-ttu-id="a8e96-117">ディレクトリが UNC 共有を指している場合は、関連するすべてのコンピューターで共有証明書を配布し、 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)の呼び出しで証明書ベースの暗号化を使用するようにシステムを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="a8e96-117">If the directory points to a UNC share, you may wish to distribute a shared certificate across all relevant machines and to configure the system to use certificate-based encryption with a call to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).</span></span>

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> <span data-ttu-id="a8e96-118">`DataProtectionProvider`具象型のインスタンスは、作成するとコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="a8e96-118">Instances of the `DataProtectionProvider` concrete type are expensive to create.</span></span> <span data-ttu-id="a8e96-119">アプリがこの種類の複数のインスタンスを保持していて、それらがすべて同じキーストレージディレクトリを使用している場合は、アプリのパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a8e96-119">If an app maintains multiple instances of this type and if they're all using the same key storage directory, app performance might degrade.</span></span> <span data-ttu-id="a8e96-120">型を使用する場合は `DataProtectionProvider` 、この型を1回作成し、可能な限り再利用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a8e96-120">If you use the `DataProtectionProvider` type, we recommend that you create this type once and reuse it as much as possible.</span></span> <span data-ttu-id="a8e96-121">`DataProtectionProvider`この型と、それから作成されたすべての[IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)インスタンスは、複数の呼び出し元に対してスレッドセーフです。</span><span class="sxs-lookup"><span data-stu-id="a8e96-121">The `DataProtectionProvider` type and all [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) instances created from it are thread-safe for multiple callers.</span></span>

---
title: ASP.NET Core でのデータ保護のキー管理と有効期間
author: rick-anderson
description: ASP.NET Core におけるデータ保護のキー管理と有効期間について説明します。
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 1303c5c2c993f1d20383457666aebfa2a583e938
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053008"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a><span data-ttu-id="b4029-103">ASP.NET Core でのデータ保護のキー管理と有効期間</span><span class="sxs-lookup"><span data-stu-id="b4029-103">Data Protection key management and lifetime in ASP.NET Core</span></span>

<span data-ttu-id="b4029-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b4029-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="key-management"></a><span data-ttu-id="b4029-105">キー管理</span><span class="sxs-lookup"><span data-stu-id="b4029-105">Key management</span></span>

<span data-ttu-id="b4029-106">アプリは、運用環境を検出し、キーの構成を独自に処理しようとします。</span><span class="sxs-lookup"><span data-stu-id="b4029-106">The app attempts to detect its operational environment and handle key configuration on its own.</span></span>

1. <span data-ttu-id="b4029-107">アプリが [Azure アプリ](https://azure.microsoft.com/services/app-service/)でホストされている場合、キーは *%HOME%\ASP.NET\DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="b4029-107">If the app is hosted in [Azure Apps](https://azure.microsoft.com/services/app-service/), keys are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="b4029-108">このフォルダーはネットワーク ストレージにバックアップされ、アプリをホストしているすべてのマシンで同期されています。</span><span class="sxs-lookup"><span data-stu-id="b4029-108">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span>
   * <span data-ttu-id="b4029-109">保存中のキーは保護されていません。</span><span class="sxs-lookup"><span data-stu-id="b4029-109">Keys aren't protected at rest.</span></span>
   * <span data-ttu-id="b4029-110">*Dataprotection キー* フォルダーは、単一のデプロイスロット内のアプリのすべてのインスタンスにキーリングを提供します。</span><span class="sxs-lookup"><span data-stu-id="b4029-110">The *DataProtection-Keys* folder supplies the key ring to all instances of an app in a single deployment slot.</span></span>
   * <span data-ttu-id="b4029-111">ステージングや運用などの別のデプロイ スロットでは、キー リングが共有されません。</span><span class="sxs-lookup"><span data-stu-id="b4029-111">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span> <span data-ttu-id="b4029-112">デプロイスロット間でスワップする場合 (運用環境へのステージングまたは A/B テストを使用する場合など)、データ保護を使用するすべてのアプリは、前のスロット内のキーリングを使用して格納されたデータの暗号化を解除することはできません。</span><span class="sxs-lookup"><span data-stu-id="b4029-112">When you swap between deployment slots, for example swapping Staging to Production or using A/B testing, any app using Data Protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="b4029-113">これにより、ユーザーは、 cookie データ保護を使用してを保護するので、標準 ASP.NET Core 認証を使用するアプリからログアウトされ cookie ます。</span><span class="sxs-lookup"><span data-stu-id="b4029-113">This leads to users being logged out of an app that uses the standard ASP.NET Core cookie authentication, as it uses Data Protection to protect its cookies.</span></span> <span data-ttu-id="b4029-114">スロットに依存しないキーリングが必要な場合は、Azure Blob Storage、Azure Key Vault、SQL ストア、Redis cache などの外部キーリングプロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="b4029-114">If you desire slot-independent key rings, use an external key ring provider, such as Azure Blob Storage, Azure Key Vault, a SQL store, or Redis cache.</span></span>

1. <span data-ttu-id="b4029-115">ユーザープロファイルが使用可能な場合、キーは *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="b4029-115">If the user profile is available, keys are persisted to the *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="b4029-116">オペレーティングシステムが Windows の場合、キーは DPAPI を使用して保存時に暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="b4029-116">If the operating system is Windows, the keys are encrypted at rest using DPAPI.</span></span>

   <span data-ttu-id="b4029-117">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4029-117">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="b4029-118">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="b4029-118">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="b4029-119">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b4029-119">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="b4029-120">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="b4029-120">If keys aren't stored in the user profile directory as expected:</span></span>

   1. <span data-ttu-id="b4029-121">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="b4029-121">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
   1. <span data-ttu-id="b4029-122">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4029-122">Open the *applicationHost.config* file.</span></span>
   1. <span data-ttu-id="b4029-123">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="b4029-123">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
   1. <span data-ttu-id="b4029-124">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b4029-124">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

1. <span data-ttu-id="b4029-125">アプリが IIS でホストされている場合、キーは、ワーカープロセスアカウントに対してのみ機能する特別なレジストリキーの HKLM レジストリに保存されます。</span><span class="sxs-lookup"><span data-stu-id="b4029-125">If the app is hosted in IIS, keys are persisted to the HKLM registry in a special registry key that's ACLed only to the worker process account.</span></span> <span data-ttu-id="b4029-126">キーは DPAPI を使用して保存時に暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="b4029-126">Keys are encrypted at rest using DPAPI.</span></span>

1. <span data-ttu-id="b4029-127">これらの条件のいずれも一致しない場合、キーは現在のプロセスの外部では保持されません。</span><span class="sxs-lookup"><span data-stu-id="b4029-127">If none of these conditions match, keys aren't persisted outside of the current process.</span></span> <span data-ttu-id="b4029-128">プロセスがシャットダウンされると、生成されたすべてのキーが失われます。</span><span class="sxs-lookup"><span data-stu-id="b4029-128">When the process shuts down, all generated keys are lost.</span></span>

<span data-ttu-id="b4029-129">開発者は常にフルコントロールを使用し、キーの格納方法と場所をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="b4029-129">The developer is always in full control and can override how and where keys are stored.</span></span> <span data-ttu-id="b4029-130">上記の最初の3つのオプションでは、ASP.NET の自動生成ルーチンが過去にどのように動作したかに類似した、ほとんどのアプリに適した既定値が提供され **\<machineKey>** ます。</span><span class="sxs-lookup"><span data-stu-id="b4029-130">The first three options above should provide good defaults for most apps similar to how the ASP.NET **\<machineKey>** auto-generation routines worked in the past.</span></span> <span data-ttu-id="b4029-131">最後のフォールバックオプションは、キーの永続化が必要な場合に、開発者が事前に [構成](xref:security/data-protection/configuration/overview) を指定することを必要とする唯一のシナリオですが、このフォールバックはまれな状況でのみ発生します。</span><span class="sxs-lookup"><span data-stu-id="b4029-131">The final, fallback option is the only scenario that requires the developer to specify [configuration](xref:security/data-protection/configuration/overview) upfront if they want key persistence, but this fallback only occurs in rare situations.</span></span>

<span data-ttu-id="b4029-132">Docker コンテナーでホストする場合、キーは、Docker ボリューム (共有ボリュームまたはコンテナーの有効期間を超えて保持されるホストマウントボリューム)、または [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) や [Redis](https://redis.io/)などの外部プロバイダーのフォルダーに保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4029-132">When hosting in a Docker container, keys should be persisted in a folder that's a Docker volume (a shared volume or a host-mounted volume that persists beyond the container's lifetime) or in an external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span> <span data-ttu-id="b4029-133">アプリが共有ネットワークボリュームにアクセスできない場合は、web ファームのシナリオでも外部プロバイダーが役立ちます (詳細については、「 [Persistkeystofilesystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) 」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="b4029-133">An external provider is also useful in web farm scenarios if apps can't access a shared network volume (see [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) for more information).</span></span>

> [!WARNING]
> <span data-ttu-id="b4029-134">開発者が上記の規則をオーバーライドし、特定のキーリポジトリでデータ保護システムをポイントする場合、保存時のキーの自動暗号化は無効になります。</span><span class="sxs-lookup"><span data-stu-id="b4029-134">If the developer overrides the rules outlined above and points the Data Protection system at a specific key repository, automatic encryption of keys at rest is disabled.</span></span> <span data-ttu-id="b4029-135">保存時の保護は、 [構成](xref:security/data-protection/configuration/overview)を使用して再度有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b4029-135">At-rest protection can be re-enabled via [configuration](xref:security/data-protection/configuration/overview).</span></span>

## <a name="key-lifetime"></a><span data-ttu-id="b4029-136">キーの有効期間</span><span class="sxs-lookup"><span data-stu-id="b4029-136">Key lifetime</span></span>

<span data-ttu-id="b4029-137">既定では、キーの有効期間は90日です。</span><span class="sxs-lookup"><span data-stu-id="b4029-137">Keys have a 90-day lifetime by default.</span></span> <span data-ttu-id="b4029-138">キーの有効期限が切れると、アプリは自動的に新しいキーを生成し、新しいキーをアクティブなキーとして設定します。</span><span class="sxs-lookup"><span data-stu-id="b4029-138">When a key expires, the app automatically generates a new key and sets the new key as the active key.</span></span> <span data-ttu-id="b4029-139">インベントリから削除されたキーがシステムに残っている限り、アプリで保護されているすべてのデータの暗号化を解除できます。</span><span class="sxs-lookup"><span data-stu-id="b4029-139">As long as retired keys remain on the system, your app can decrypt any data protected with them.</span></span> <span data-ttu-id="b4029-140">詳細については、「 [キー管理](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b4029-140">See [key management](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) for more information.</span></span>

## <a name="default-algorithms"></a><span data-ttu-id="b4029-141">既定のアルゴリズム</span><span class="sxs-lookup"><span data-stu-id="b4029-141">Default algorithms</span></span>

<span data-ttu-id="b4029-142">使用される既定のペイロード保護アルゴリズムは、HMACSHA256 の場合は AES-256-CBC、信頼性を確保する場合はです。</span><span class="sxs-lookup"><span data-stu-id="b4029-142">The default payload protection algorithm used is AES-256-CBC for confidentiality and HMACSHA256 for authenticity.</span></span> <span data-ttu-id="b4029-143">90日ごとに変更された512ビットのマスターキーは、ペイロードごとにこれらのアルゴリズムに使用される2つのサブキーを派生させるために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4029-143">A 512-bit master key, changed every 90 days, is used to derive the two sub-keys used for these algorithms on a per-payload basis.</span></span> <span data-ttu-id="b4029-144">詳細については、「 [サブキーの派生](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b4029-144">See [subkey derivation](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4029-145">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b4029-145">Additional resources</span></span>

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>

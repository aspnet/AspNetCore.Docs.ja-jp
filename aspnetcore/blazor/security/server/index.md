---
title: ASP.NET Core Blazor Server アプリをセキュリティで保護する
author: guardrex
description: Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5a3d3c6e06653de7f0d01565444d37013f347a5b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280314"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="936b1-103">ASP.NET Core Blazor Server アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="936b1-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="936b1-104">Blazor Server アプリは、ASP.NET Core アプリと同じ方法でセキュリティが構成されます。</span><span class="sxs-lookup"><span data-stu-id="936b1-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="936b1-105">詳細については、<xref:security/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="936b1-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="936b1-106">この概要に含まれるトピックは、Blazor Server に特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="936b1-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="936b1-107">Blazor Server プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="936b1-107">Blazor Server project template</span></span>

<span data-ttu-id="936b1-108">Blazor Server プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="936b1-108">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="936b1-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="936b1-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="936b1-110">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="936b1-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="936b1-111">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor Server アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="936b1-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="936b1-112">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="936b1-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="936b1-113">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="936b1-113">**No Authentication**</span></span>
* <span data-ttu-id="936b1-114">**個人のユーザー アカウント**: ユーザーアカウントは次のように格納できます。</span><span class="sxs-lookup"><span data-stu-id="936b1-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="936b1-115">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="936b1-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="936b1-116">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="936b1-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="936b1-117">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="936b1-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="936b1-118">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="936b1-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="936b1-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="936b1-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="936b1-120">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="936b1-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="936b1-121">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="936b1-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="936b1-122">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="936b1-122">Authentication mechanism</span></span> | <span data-ttu-id="936b1-123">説明</span><span class="sxs-lookup"><span data-stu-id="936b1-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="936b1-124">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="936b1-124">`None` (default)</span></span>         | <span data-ttu-id="936b1-125">認証なし</span><span class="sxs-lookup"><span data-stu-id="936b1-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="936b1-126">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="936b1-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="936b1-127">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="936b1-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="936b1-128">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="936b1-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="936b1-129">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="936b1-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="936b1-130">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="936b1-130">Windows Authentication</span></span> |

<span data-ttu-id="936b1-131">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="936b1-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="936b1-132">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="936b1-132">Create a folder for the project.</span></span>
* <span data-ttu-id="936b1-133">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="936b1-133">Name the project.</span></span>

<span data-ttu-id="936b1-134">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="936b1-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="936b1-135">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="936b1-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="936b1-136"><xref:blazor/tooling> に記載されている Visual Studio for Mac のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="936b1-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="936b1-137">**[新しい Blazor Server アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="936b1-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="936b1-138">ASP.NET Core Identity を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="936b1-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="936b1-139">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="936b1-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="936b1-140">コマンド シェルで次のコマンドを使用し、認証メカニズムを使って新しい Blazor Server プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="936b1-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="936b1-141">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="936b1-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="936b1-142">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="936b1-142">Authentication mechanism</span></span> | <span data-ttu-id="936b1-143">説明</span><span class="sxs-lookup"><span data-stu-id="936b1-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="936b1-144">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="936b1-144">`None` (default)</span></span>         | <span data-ttu-id="936b1-145">認証なし</span><span class="sxs-lookup"><span data-stu-id="936b1-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="936b1-146">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="936b1-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="936b1-147">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="936b1-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="936b1-148">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="936b1-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="936b1-149">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="936b1-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="936b1-150">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="936b1-150">Windows Authentication</span></span> |

<span data-ttu-id="936b1-151">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="936b1-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="936b1-152">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="936b1-152">Create a folder for the project.</span></span>
* <span data-ttu-id="936b1-153">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="936b1-153">Name the project.</span></span>

<span data-ttu-id="936b1-154">詳細情報:</span><span class="sxs-lookup"><span data-stu-id="936b1-154">For more information:</span></span>

* <span data-ttu-id="936b1-155">.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="936b1-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="936b1-156">コマンド シェルで Blazor Server テンプレート (`blazorserver`) のヘルプ コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="936b1-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="936b1-157">スキャフォールディング Identity</span><span class="sxs-lookup"><span data-stu-id="936b1-157">Scaffold Identity</span></span>

<span data-ttu-id="936b1-158">Identity を Blazor Server プロジェクトにスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="936b1-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="936b1-159">[既存の承認がありません](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="936b1-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="936b1-160">[承認があります](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="936b1-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="936b1-161">Identity Server を使用した Azure App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="936b1-161">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="936b1-162">Identity Server を使用して Azure App Service on Linux にデプロイするときに、発行者を明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="936b1-162">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="936b1-163">詳細については、「<xref:security/authentication/identity/spa#azure-app-service-on-linux>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="936b1-163">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="936b1-164">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="936b1-164">Additional resources</span></span>

* [<span data-ttu-id="936b1-165">クイック スタート:Microsoft でのサインインを ASP.NET Core Web アプリに追加する</span><span class="sxs-lookup"><span data-stu-id="936b1-165">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="936b1-166">クイック スタート:Microsoft ID プラットフォームを使用して ASP.NET Core Web API を保護する</span><span class="sxs-lookup"><span data-stu-id="936b1-166">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="936b1-167"><xref:host-and-deploy/proxy-load-balancer>: 次のガイダンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="936b1-167"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="936b1-168">転送されたヘッダー ミドルウェアを使用した、プロキシ サーバーと内部ネットワークの間での HTTPS スキーム情報の保持。</span><span class="sxs-lookup"><span data-stu-id="936b1-168">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="936b1-169">追加のシナリオとユース ケース (手動スキーム構成を含む)、正しい要求ルーティングのための要求パスの変更、Linux および IIS 以外のリバース プロキシのための要求スキームの転送。</span><span class="sxs-lookup"><span data-stu-id="936b1-169">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>

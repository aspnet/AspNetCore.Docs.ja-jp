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
ms.openlocfilehash: 5031273c3395be4365b3a6d239ebce7aaf9b66ac
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658639"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="9902a-103">ASP.NET Core Blazor Server アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="9902a-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="9902a-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9902a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9902a-105">Blazor Server アプリは、ASP.NET Core アプリと同じ方法でセキュリティが構成されます。</span><span class="sxs-lookup"><span data-stu-id="9902a-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="9902a-106">詳細については、<xref:security/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9902a-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="9902a-107">この概要に含まれるトピックは、Blazor Server に特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="9902a-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="9902a-108">Blazor Server プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="9902a-108">Blazor Server project template</span></span>

<span data-ttu-id="9902a-109">Blazor Server プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="9902a-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9902a-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9902a-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9902a-111">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9902a-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="9902a-112">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor Server アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9902a-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="9902a-113">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9902a-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="9902a-114">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="9902a-114">**No Authentication**</span></span>
* <span data-ttu-id="9902a-115">**個人のユーザー アカウント**: ユーザーアカウントは次のように格納できます。</span><span class="sxs-lookup"><span data-stu-id="9902a-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="9902a-116">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="9902a-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="9902a-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="9902a-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="9902a-118">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="9902a-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="9902a-119">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="9902a-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9902a-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9902a-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9902a-121">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9902a-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9902a-122">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="9902a-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9902a-123">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="9902a-123">Authentication mechanism</span></span> | <span data-ttu-id="9902a-124">説明</span><span class="sxs-lookup"><span data-stu-id="9902a-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9902a-125">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="9902a-125">`None` (default)</span></span>         | <span data-ttu-id="9902a-126">認証なし</span><span class="sxs-lookup"><span data-stu-id="9902a-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9902a-127">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="9902a-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9902a-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="9902a-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9902a-129">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="9902a-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9902a-130">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="9902a-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9902a-131">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="9902a-131">Windows Authentication</span></span> |

<span data-ttu-id="9902a-132">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="9902a-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9902a-133">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9902a-133">Create a folder for the project.</span></span>
* <span data-ttu-id="9902a-134">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9902a-134">Name the project.</span></span>

<span data-ttu-id="9902a-135">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9902a-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9902a-136">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9902a-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9902a-137"><xref:blazor/tooling> に記載されている Visual Studio for Mac のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9902a-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="9902a-138">**[新しい Blazor Server アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9902a-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="9902a-139">ASP.NET Core Identity を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9902a-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9902a-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9902a-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9902a-141">コマンド シェルで次のコマンドを使用し、認証メカニズムを使って新しい Blazor Server プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="9902a-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9902a-142">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="9902a-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9902a-143">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="9902a-143">Authentication mechanism</span></span> | <span data-ttu-id="9902a-144">説明</span><span class="sxs-lookup"><span data-stu-id="9902a-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="9902a-145">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="9902a-145">`None` (default)</span></span>         | <span data-ttu-id="9902a-146">認証なし</span><span class="sxs-lookup"><span data-stu-id="9902a-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="9902a-147">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="9902a-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="9902a-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="9902a-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="9902a-149">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="9902a-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="9902a-150">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="9902a-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="9902a-151">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="9902a-151">Windows Authentication</span></span> |

<span data-ttu-id="9902a-152">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="9902a-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="9902a-153">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9902a-153">Create a folder for the project.</span></span>
* <span data-ttu-id="9902a-154">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9902a-154">Name the project.</span></span>

<span data-ttu-id="9902a-155">詳細情報:</span><span class="sxs-lookup"><span data-stu-id="9902a-155">For more information:</span></span>

* <span data-ttu-id="9902a-156">.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9902a-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="9902a-157">コマンド シェルで Blazor Server テンプレート (`blazorserver`) のヘルプ コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9902a-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="9902a-158">スキャフォールディング Identity</span><span class="sxs-lookup"><span data-stu-id="9902a-158">Scaffold Identity</span></span>

<span data-ttu-id="9902a-159">Identity を Blazor Server プロジェクトにスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="9902a-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="9902a-160">[既存の承認がありません](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="9902a-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="9902a-161">[承認があります](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="9902a-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a><span data-ttu-id="9902a-162">Identity Server を使用した Azure App Service on Linux</span><span class="sxs-lookup"><span data-stu-id="9902a-162">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="9902a-163">Identity Server を使用して Azure App Service on Linux にデプロイするときに、発行者を明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="9902a-163">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="9902a-164">詳細については、「<xref:security/authentication/identity/spa#azure-app-service-on-linux>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9902a-164">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9902a-165">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9902a-165">Additional resources</span></span>

* [<span data-ttu-id="9902a-166">クイック スタート:Microsoft でのサインインを ASP.NET Core Web アプリに追加する</span><span class="sxs-lookup"><span data-stu-id="9902a-166">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="9902a-167">クイック スタート:Microsoft ID プラットフォームを使用して ASP.NET Core Web API を保護する</span><span class="sxs-lookup"><span data-stu-id="9902a-167">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="9902a-168"><xref:host-and-deploy/proxy-load-balancer>: 次のガイダンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9902a-168"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="9902a-169">転送されたヘッダー ミドルウェアを使用した、プロキシ サーバーと内部ネットワークの間での HTTPS スキーム情報の保持。</span><span class="sxs-lookup"><span data-stu-id="9902a-169">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="9902a-170">追加のシナリオとユース ケース (手動スキーム構成を含む)、正しい要求ルーティングのための要求パスの変更、Linux および IIS 以外のリバース プロキシのための要求スキームの転送。</span><span class="sxs-lookup"><span data-stu-id="9902a-170">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>

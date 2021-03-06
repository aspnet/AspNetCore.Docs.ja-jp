---
title: IdentityASP.NET Core の概要
author: rick-anderson
description: IdentityASP.NET Core アプリで使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: 4fa49f795b78b88e00bd32d04f74acd8689383b2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394474"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="53c73-104">IdentityASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="53c73-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="53c73-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53c73-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="53c73-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="53c73-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="53c73-107">は、ユーザーインターフェイス (UI) のログイン機能をサポートする API です。</span><span class="sxs-lookup"><span data-stu-id="53c73-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="53c73-108">ユーザー、パスワード、プロファイルデータ、ロール、要求、トークン、電子メールの確認などを管理します。</span><span class="sxs-lookup"><span data-stu-id="53c73-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="53c73-109">ユーザーは、に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもでき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="53c73-110">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="53c73-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="53c73-111">[ Identity ソースコード](https://github.com/dotnet/AspNetCore/tree/main/src/Identity)は GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="53c73-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="53c73-112">[スキャフォールディング Identity ](xref:security/authentication/scaffold-identity)生成されたファイルを表示して、テンプレートとの対話を確認し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="53c73-113">Identity は、通常、ユーザー名、パスワード、およびプロファイルデータを格納するために SQL Server データベースを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="53c73-114">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="53c73-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="53c73-115">このトピックでは、を使用し Identity てユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="53c73-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="53c73-116">注: このテンプレートでは、ユーザー名と電子メールはユーザーに対して同じものとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="53c73-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="53c73-117">を使用するアプリを作成する方法の詳細について Identity は、「 [次のステップ](#next)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="53c73-118">[Microsoft id プラットフォーム](/azure/active-directory/develop/) は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="53c73-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="53c73-119">Azure Active Directory (Azure AD) 開発プラットフォームの進化。</span><span class="sxs-lookup"><span data-stu-id="53c73-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="53c73-120">に関連付けら ASP.NET Core Identity れていません。</span><span class="sxs-lookup"><span data-stu-id="53c73-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="53c73-121">[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="53c73-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="53c73-122">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="53c73-122">Create a Web app with authentication</span></span>

<span data-ttu-id="53c73-123">個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="53c73-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53c73-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53c73-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="53c73-125">[**ファイル**] [新しいプロジェクト] を選択し >  > ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="53c73-126">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53c73-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="53c73-127">プロジェクトに **WebApp1** という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="53c73-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="53c73-128">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="53c73-128">Click **OK**.</span></span>
* <span data-ttu-id="53c73-129">ASP.NET Core **Web アプリケーション** を選択し、[ **認証の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="53c73-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="53c73-130">**個々のユーザーアカウント** を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="53c73-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="53c73-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="53c73-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="53c73-132">上記のコマンドは、 Razor SQLite を使用して web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="53c73-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="53c73-133">LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="53c73-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="53c73-134">生成されたプロジェクトは [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor クラスライブラリ](xref:razor-pages/ui-class)としてを提供します。</span><span class="sxs-lookup"><span data-stu-id="53c73-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="53c73-135">Identity Razor クラスライブラリは、領域と共にエンドポイントを公開し `Identity` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="53c73-136">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c73-136">For example:</span></span>

* <span data-ttu-id="53c73-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="53c73-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="53c73-138">/Identity/アカウント/ログアウト</span><span class="sxs-lookup"><span data-stu-id="53c73-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="53c73-139">/Identity/アカウント/管理</span><span class="sxs-lookup"><span data-stu-id="53c73-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="53c73-140">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="53c73-140">Apply migrations</span></span>

<span data-ttu-id="53c73-141">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="53c73-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53c73-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53c73-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53c73-143">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="53c73-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="53c73-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="53c73-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="53c73-145">SQLite を使用する場合、この手順で移行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="53c73-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="53c73-146">LocalDB の場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="53c73-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="53c73-147">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="53c73-147">Test Register and Login</span></span>

<span data-ttu-id="53c73-148">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="53c73-148">Run the app and register a user.</span></span> <span data-ttu-id="53c73-149">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、 **登録** リンクと **ログイン** リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="53c73-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="53c73-150">サービスの構成 Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-150">Configure Identity services</span></span>

<span data-ttu-id="53c73-151">サービスはに追加されて `ConfigureServices` います。</span><span class="sxs-lookup"><span data-stu-id="53c73-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="53c73-152">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="53c73-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="53c73-153">前の強調表示されたコードは、 Identity 既定のオプション値を使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="53c73-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="53c73-154">サービスは、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="53c73-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="53c73-155">Identity は、を呼び出すことによって有効になり <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="53c73-156">`UseAuthentication` 認証 [ミドルウェア](xref:fundamentals/middleware/index) を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="53c73-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="53c73-157">前のコードでは、 Identity 既定のオプション値を使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="53c73-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="53c73-158">サービスは、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="53c73-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="53c73-159">Identity は、 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="53c73-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="53c73-160">`UseAuthentication` 認証 [ミドルウェア](xref:fundamentals/middleware/index) を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="53c73-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="53c73-161">テンプレートで生成されたアプリは、 [承認](xref:security/authorization/secure-data)を使用しません。</span><span class="sxs-lookup"><span data-stu-id="53c73-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="53c73-162">`app.UseAuthorization` は、アプリが承認を追加する正しい順序で追加されるようにするために用意されています。</span><span class="sxs-lookup"><span data-stu-id="53c73-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="53c73-163">`UseRouting`、 `UseAuthentication` 、 `UseAuthorization` 、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="53c73-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="53c73-164">およびの詳細に `IdentityOptions` つい `Startup` ては、「」および「アプリケーションの起動」を参照してください <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 。 [](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="53c73-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="53c73-165">スキャフォールディング Register、Login、LogOut、および RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="53c73-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53c73-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53c73-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53c73-167">、、、およびの各ファイルを追加し `Register` `Login` `LogOut` `RegisterConfirmation` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="53c73-168">このセクションに示されているコードを生成するための [ Razor 承認手順を含むプロジェクトに、スキャフォールディング id](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) に従います。</span><span class="sxs-lookup"><span data-stu-id="53c73-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="53c73-169">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="53c73-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="53c73-170">**WebApp1** という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="53c73-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="53c73-171">それ以外の場合は、の正しい名前空間を使用し `ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="53c73-172">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="53c73-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="53c73-173">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="53c73-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="53c73-174">スキャフォールディングの詳細につい Identity ては、「 [スキャフォールディング identity to a Razor project in authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="53c73-175">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="53c73-175">Examine Register</span></span>

<span data-ttu-id="53c73-176">ユーザーがページの [ **登録** ] ボタンをクリックすると、 `Register` `RegisterModel.OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="53c73-177">ユーザーは、オブジェクトに対して [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) によって作成され `_userManager` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="53c73-178">ログイン</span><span class="sxs-lookup"><span data-stu-id="53c73-178">Log in</span></span>

<span data-ttu-id="53c73-179">ログインフォームは次の場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="53c73-180">**ログイン** リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="53c73-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="53c73-181">ユーザーがアクセスを承認されていない、 **または** システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="53c73-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="53c73-182">ログインページのフォームが送信されると、 `OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="53c73-183">`PasswordSignInAsync` は、オブジェクトで呼び出され `_signInManager` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="53c73-184">承認決定を行う方法については、「」を参照してください <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="53c73-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="53c73-185">ログアウト</span><span class="sxs-lookup"><span data-stu-id="53c73-185">Log out</span></span>

<span data-ttu-id="53c73-186">[ **ログアウト** すると、アクションが呼び出され `LogoutModel.OnPost` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="53c73-187">前のコードでは、 `return RedirectToPage();` ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コードをリダイレクトにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="53c73-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="53c73-188">[署名](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) は、に格納されているユーザーの要求をクリア cookie します。</span><span class="sxs-lookup"><span data-stu-id="53c73-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="53c73-189">Post は *Pages/Shared/_LoginPartial* に指定されています。</span><span class="sxs-lookup"><span data-stu-id="53c73-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="53c73-190">Test Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-190">Test Identity</span></span>

<span data-ttu-id="53c73-191">既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="53c73-192">テストするには Identity 、次のように追加し [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="53c73-193">サインインしている場合は、サインアウトします。アプリを実行し、[ **プライバシー** ] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="53c73-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="53c73-194">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="53c73-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="53c73-195">Tsm Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-195">Explore Identity</span></span>

<span data-ttu-id="53c73-196">詳細については、次を参照して Identity ください。</span><span class="sxs-lookup"><span data-stu-id="53c73-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="53c73-197">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="53c73-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="53c73-198">各ページのソースを確認し、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="53c73-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="53c73-199">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="53c73-199">Identity Components</span></span>

<span data-ttu-id="53c73-200">すべての Identity 依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="53c73-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="53c73-201">のプライマリパッケージ Identity は[AspNetCore です Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="53c73-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="53c73-202">このパッケージには、のインターフェイスのコアセットが含まれており、によって含まれてい ASP.NET Core Identity `Microsoft.AspNetCore.Identity.EntityFrameworkCore` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="53c73-203">移行先 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="53c73-204">既存のストアの移行に関する詳細とガイダンスについて Identity は、「[認証と Identity の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="53c73-205">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="53c73-205">Setting password strength</span></span>

<span data-ttu-id="53c73-206">パスワードの最小要件を設定するサンプルについては、「 [構成](#pw) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="53c73-207">AddDefault Identity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="53c73-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="53c73-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="53c73-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="53c73-209">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="53c73-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="53c73-210">詳細については、「 [Adddefault Identity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="53c73-211">静的アセットの発行を禁止する Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="53c73-212">静的 Identity アセット (UI 用のスタイルシートおよび JavaScript ファイル) を web ルートに発行できないようにするには Identity 、 `ResolveStaticWebAssetsInputsDependsOn` `RemoveIdentityAssets` アプリケーションのプロジェクトファイルに次のプロパティとターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="53c73-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="53c73-213">次の手順</span><span class="sxs-lookup"><span data-stu-id="53c73-213">Next Steps</span></span>

* <span data-ttu-id="53c73-214">[ASP.NET Core Identity ソース コード](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="53c73-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="53c73-215">SQLite を使用したの構成の詳細については、こちらの [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131) を参照してください Identity 。</span><span class="sxs-lookup"><span data-stu-id="53c73-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="53c73-216">Identity を構成する</span><span class="sxs-lookup"><span data-stu-id="53c73-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="53c73-217">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53c73-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="53c73-218">ASP.NET Core Identity は、ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="53c73-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="53c73-219">ユーザーは、に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもでき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="53c73-220">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="53c73-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="53c73-221">Identity SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="53c73-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="53c73-222">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="53c73-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="53c73-223">[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="53c73-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="53c73-224">このトピックでは、を使用し Identity てユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="53c73-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="53c73-225">を使用するアプリを作成する方法の詳細については Identity 、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="53c73-226">AddDefault Identity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="53c73-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="53c73-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="53c73-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="53c73-228">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="53c73-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="53c73-229">詳細については、「 [Adddefault Identity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="53c73-230">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="53c73-230">Create a Web app with authentication</span></span>

<span data-ttu-id="53c73-231">個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="53c73-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53c73-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53c73-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="53c73-233">[**ファイル**] [新しいプロジェクト] を選択し >  > ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="53c73-234">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53c73-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="53c73-235">プロジェクトに **WebApp1** という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="53c73-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="53c73-236">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="53c73-236">Click **OK**.</span></span>
* <span data-ttu-id="53c73-237">ASP.NET Core **Web アプリケーション** を選択し、[ **認証の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="53c73-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="53c73-238">**個々のユーザーアカウント** を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="53c73-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="53c73-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="53c73-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="53c73-240">生成されたプロジェクトは [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor クラスライブラリ](xref:razor-pages/ui-class)としてを提供します。</span><span class="sxs-lookup"><span data-stu-id="53c73-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="53c73-241">Identity Razor クラスライブラリは、領域と共にエンドポイントを公開し `Identity` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="53c73-242">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c73-242">For example:</span></span>

* <span data-ttu-id="53c73-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="53c73-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="53c73-244">/Identity/アカウント/ログアウト</span><span class="sxs-lookup"><span data-stu-id="53c73-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="53c73-245">/Identity/アカウント/管理</span><span class="sxs-lookup"><span data-stu-id="53c73-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="53c73-246">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="53c73-246">Apply migrations</span></span>

<span data-ttu-id="53c73-247">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="53c73-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53c73-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53c73-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53c73-249">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="53c73-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="53c73-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="53c73-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="53c73-251">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="53c73-251">Test Register and Login</span></span>

<span data-ttu-id="53c73-252">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="53c73-252">Run the app and register a user.</span></span> <span data-ttu-id="53c73-253">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、 **登録** リンクと **ログイン** リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="53c73-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="53c73-254">サービスの構成 Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-254">Configure Identity services</span></span>

<span data-ttu-id="53c73-255">サービスはに追加されて `ConfigureServices` います。</span><span class="sxs-lookup"><span data-stu-id="53c73-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="53c73-256">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="53c73-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="53c73-257">前のコードでは、 Identity 既定のオプション値を使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="53c73-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="53c73-258">サービスは、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="53c73-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="53c73-259">Identity は、 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="53c73-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="53c73-260">`UseAuthentication` 認証 [ミドルウェア](xref:fundamentals/middleware/index) を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="53c73-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="53c73-261">詳細については、「 [ Identity オプションクラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)」と「[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="53c73-262">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="53c73-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="53c73-263">このセクションに示されているコードを生成するための [ Razor 承認手順を含むプロジェクトに、スキャフォールディング id](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) に従います。</span><span class="sxs-lookup"><span data-stu-id="53c73-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53c73-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53c73-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53c73-265">Register、Login、および LogOut の各ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="53c73-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="53c73-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="53c73-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="53c73-267">**WebApp1** という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="53c73-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="53c73-268">それ以外の場合は、の正しい名前空間を使用し `ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

<span data-ttu-id="53c73-269">SQLite を使用する場合は、 `--useSqLite` 次のように指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53c73-269">When using SQLite, `--useSqLite` must be specified:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout" --useSqLite
```

<span data-ttu-id="53c73-270">SQL Express では、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="53c73-270">With SQL Express, use the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="53c73-271">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="53c73-271">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="53c73-272">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="53c73-272">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="53c73-273">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="53c73-273">Examine Register</span></span>

<span data-ttu-id="53c73-274">ユーザーが [ **登録** ] リンクをクリックすると、 `RegisterModel.OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-274">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="53c73-275">ユーザーは、オブジェクトに対して [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) によって作成され `_userManager` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-275">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="53c73-276">ユーザーが正常に作成された場合は、の呼び出しによってユーザーがログインし `_signInManager.SignInAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-276">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="53c73-277">**注:** 登録時にすぐにログインできないようにする手順については、「 [アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration) 」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53c73-277">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="53c73-278">ログイン</span><span class="sxs-lookup"><span data-stu-id="53c73-278">Log in</span></span>

<span data-ttu-id="53c73-279">ログインフォームは次の場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-279">The Login form is displayed when:</span></span>

* <span data-ttu-id="53c73-280">**ログイン** リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="53c73-280">The **Log in** link is selected.</span></span>
* <span data-ttu-id="53c73-281">ユーザーがアクセスを承認されていない、 **または** システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="53c73-281">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="53c73-282">ログインページのフォームが送信されると、 `OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-282">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="53c73-283">`PasswordSignInAsync` は、オブジェクトで呼び出され `_signInManager` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-283">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="53c73-284">承認決定を行う方法については、「」を参照してください <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="53c73-284">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="53c73-285">ログアウト</span><span class="sxs-lookup"><span data-stu-id="53c73-285">Log out</span></span>

<span data-ttu-id="53c73-286">[ **ログアウト** すると、アクションが呼び出され `LogoutModel.OnPost` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="53c73-287">[署名](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) は、に格納されているユーザーの要求をクリア cookie します。</span><span class="sxs-lookup"><span data-stu-id="53c73-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="53c73-288">Post は *Pages/Shared/_LoginPartial* に指定されています。</span><span class="sxs-lookup"><span data-stu-id="53c73-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="53c73-289">Test Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-289">Test Identity</span></span>

<span data-ttu-id="53c73-290">既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="53c73-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="53c73-291">をテストするに Identity は、[ [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) プライバシー] ページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="53c73-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="53c73-292">サインインしている場合は、サインアウトします。アプリを実行し、[ **プライバシー** ] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="53c73-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="53c73-293">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="53c73-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="53c73-294">Tsm Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-294">Explore Identity</span></span>

<span data-ttu-id="53c73-295">詳細については、次を参照して Identity ください。</span><span class="sxs-lookup"><span data-stu-id="53c73-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="53c73-296">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="53c73-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="53c73-297">各ページのソースを確認し、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="53c73-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="53c73-298">Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="53c73-298">Identity Components</span></span>

<span data-ttu-id="53c73-299">すべての Identity 依存する NuGet パッケージは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="53c73-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="53c73-300">のプライマリパッケージ Identity は[AspNetCore です Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="53c73-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="53c73-301">このパッケージには、のインターフェイスのコアセットが含まれており、によって含まれてい ASP.NET Core Identity `Microsoft.AspNetCore.Identity.EntityFrameworkCore` ます。</span><span class="sxs-lookup"><span data-stu-id="53c73-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="53c73-302">移行先 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="53c73-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="53c73-303">既存のストアの移行に関する詳細とガイダンスについて Identity は、「[認証と Identity の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="53c73-304">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="53c73-304">Setting password strength</span></span>

<span data-ttu-id="53c73-305">パスワードの最小要件を設定するサンプルについては、「 [構成](#pw) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c73-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="53c73-306">次の手順</span><span class="sxs-lookup"><span data-stu-id="53c73-306">Next Steps</span></span>

* <span data-ttu-id="53c73-307">SQLite を使用したの構成の詳細については、こちらの [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131) を参照してください Identity 。</span><span class="sxs-lookup"><span data-stu-id="53c73-307">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="53c73-308">Identity を構成する</span><span class="sxs-lookup"><span data-stu-id="53c73-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

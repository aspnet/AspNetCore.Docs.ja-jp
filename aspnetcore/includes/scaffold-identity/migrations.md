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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552968"
---
<span data-ttu-id="d23e1-101">生成された Identity データベースコードには [Entity Framework Core 移行](/ef/core/managing-schemas/migrations/)が必要です。</span><span class="sxs-lookup"><span data-stu-id="d23e1-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="d23e1-102">移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="d23e1-102">Create a migration and update the database.</span></span> <span data-ttu-id="d23e1-103">たとえば、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d23e1-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d23e1-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d23e1-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d23e1-105">Visual Studio **パッケージマネージャーコンソール** で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="d23e1-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="d23e1-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d23e1-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="d23e1-107">Identityコマンドの "Create Schema" name パラメーター `Add-Migration` は任意です。</span><span class="sxs-lookup"><span data-stu-id="d23e1-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="d23e1-108">`"CreateIdentitySchema"` 移行について説明します。</span><span class="sxs-lookup"><span data-stu-id="d23e1-108">`"CreateIdentitySchema"` describes the migration.</span></span>

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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552300"
---
### <a name="view-the-identity-database"></a><span data-ttu-id="439e9-101">データベースを表示する Identity</span><span class="sxs-lookup"><span data-stu-id="439e9-101">View the Identity database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="439e9-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="439e9-102">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="439e9-103">[ **表示** ] メニューの [ **SQL Server オブジェクトエクスプローラー** (ssox)] を選択します。</span><span class="sxs-lookup"><span data-stu-id="439e9-103">From the **View** menu, select **SQL Server Object Explorer** (SSOX).</span></span>
* <span data-ttu-id="439e9-104">**(Localdb) MSSQLLocalDB (SQL Server 13)** に移動します。</span><span class="sxs-lookup"><span data-stu-id="439e9-104">Navigate to **(localdb)MSSQLLocalDB(SQL Server 13)**.</span></span> <span data-ttu-id="439e9-105">Dbo を右クリックし **ます。AspNetUsers**  >  **View データ**:</span><span class="sxs-lookup"><span data-stu-id="439e9-105">Right-click on **dbo.AspNetUsers** > **View Data**:</span></span>

![SQL Server オブジェクトエクスプローラーの AspNetUsers テーブルのコンテキストメニュー](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[<span data-ttu-id="439e9-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="439e9-107">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="439e9-108">Sqlite データベースを管理および表示するためにダウンロードできるサードパーティ製のツールは多数あります。たとえば、 [sqlite 用の DB Browser](https://sqlitebrowser.org/)などです。</span><span class="sxs-lookup"><span data-stu-id="439e9-108">There are many third party tools you can download to manage and view a SQLite database, for example [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

---
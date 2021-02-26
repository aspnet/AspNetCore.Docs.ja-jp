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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552080"
---
> [!NOTE]
> <span data-ttu-id="4a1f6-101">このチュートリアルでは、可能な場合は Entity Framework Core の "*移行*" 機能を使います。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="4a1f6-102">移行では、データ モデルの変更に合わせてデータベース スキーマが更新されます。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="4a1f6-103">ただし、移行では EF Core プロバイダーでサポートされている種類の変更しか行うことができず、SQLite プロバイダーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="4a1f6-104">たとえば、列の追加はサポートされていますが、列の削除や変更はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="4a1f6-105">列を削除または変更するために移行を作成した場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="4a1f6-106">これらの制限により、このチュートリアルでは SQLite のスキーマの変更に対しては移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="4a1f6-107">代わりに、スキーマを変更するときは、データベースを削除して再作成します。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="4a1f6-108">SQLite の制限に対する回避策としては、テーブル内の何かが変更されたときにテーブルの再構築を実行する移行コードを、手動で作成します。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="4a1f6-109">テーブルの再構築には次の作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="4a1f6-110">新しいテーブルの作成。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-110">Creating a new table.</span></span>
>* <span data-ttu-id="4a1f6-111">古いテーブルから新しいテーブルへのデータのコピー。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-111">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="4a1f6-112">古いテーブルの削除。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-112">Dropping the old table.</span></span>
>* <span data-ttu-id="4a1f6-113">新しいテーブルの名前変更。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-113">Renaming the new table.</span></span>
>
><span data-ttu-id="4a1f6-114">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a1f6-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="4a1f6-115">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="4a1f6-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="4a1f6-116">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="4a1f6-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="4a1f6-117">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="4a1f6-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)
  * [<span data-ttu-id="4a1f6-118">SQLite ALTER TABLE ステートメント</span><span class="sxs-lookup"><span data-stu-id="4a1f6-118">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)
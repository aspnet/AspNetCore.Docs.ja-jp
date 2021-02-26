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
ms.openlocfilehash: cf20722e8c8669fb17af8db032d4064ca2be2f4c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552183"
---
> [!NOTE]
> 
> <span data-ttu-id="a8e51-101">**SQLite の制限事項**</span><span class="sxs-lookup"><span data-stu-id="a8e51-101">**SQLite limitations**</span></span>
>
> <span data-ttu-id="a8e51-102">このチュートリアルでは、可能な場合は Entity Framework Core の *移行* 機能を使います。</span><span class="sxs-lookup"><span data-stu-id="a8e51-102">This tutorial uses the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="a8e51-103">移行では、データ モデルの変更に合わせてデータベース スキーマが更新されます。</span><span class="sxs-lookup"><span data-stu-id="a8e51-103">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="a8e51-104">しかし、移行ではデータベース エンジンによってサポートされる変更の種類のみが行われ、SQLite のスキーマ変更機能は制限されます。</span><span class="sxs-lookup"><span data-stu-id="a8e51-104">However, migrations only does the kinds of changes that the database engine supports, and SQLite's schema change capabilities are limited.</span></span> <span data-ttu-id="a8e51-105">たとえば、列の追加はサポートされていますが、列の削除はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a8e51-105">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="a8e51-106">列を削除するために移行を作成した場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。</span><span class="sxs-lookup"><span data-stu-id="a8e51-106">If a migration is created to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> 
>
> <span data-ttu-id="a8e51-107">SQLite の制限に対する回避策としては、テーブル内の何かが変更されたときにテーブルの再構築を実行する移行コードを、手動で作成します。</span><span class="sxs-lookup"><span data-stu-id="a8e51-107">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="a8e51-108">このコードは移行のために `Up` メソッドと `Down` メソッドに進み、次のようなものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a8e51-108">The code would go in the `Up` and `Down` methods for a migration and would involve:</span></span>
>
> * <span data-ttu-id="a8e51-109">新しいテーブルの作成。</span><span class="sxs-lookup"><span data-stu-id="a8e51-109">Creating a new table.</span></span>
> * <span data-ttu-id="a8e51-110">古いテーブルから新しいテーブルへのデータのコピー。</span><span class="sxs-lookup"><span data-stu-id="a8e51-110">Copying data from the old table to the new table.</span></span>
> * <span data-ttu-id="a8e51-111">古いテーブルの削除。</span><span class="sxs-lookup"><span data-stu-id="a8e51-111">Dropping the old table.</span></span>
> * <span data-ttu-id="a8e51-112">新しいテーブルの名前変更。</span><span class="sxs-lookup"><span data-stu-id="a8e51-112">Renaming the new table.</span></span>
>
> <span data-ttu-id="a8e51-113">この種類のデータベース固有のコードを記述することは、このチュートリアルの範囲外です。</span><span class="sxs-lookup"><span data-stu-id="a8e51-113">Writing database-specific code of this type is outside the scope of this tutorial.</span></span> <span data-ttu-id="a8e51-114">代わりに、このチュートリアルでは、移行を適用しようとして失敗した場合に、データベースを削除して再作成します。</span><span class="sxs-lookup"><span data-stu-id="a8e51-114">Instead, this tutorial drops and re-creates the database whenever an attempt to apply a migration would fail.</span></span> <span data-ttu-id="a8e51-115">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a8e51-115">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="a8e51-116">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="a8e51-116">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="a8e51-117">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="a8e51-117">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="a8e51-118">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="a8e51-118">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="a8e51-119">SQLite ALTER TABLE ステートメント</span><span class="sxs-lookup"><span data-stu-id="a8e51-119">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)
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
ms.openlocfilehash: 6808c71b1ca43755eea4958ff9409f40e8685694
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551247"
---
<span data-ttu-id="12d91-101">このチュートリアルでは、ASP.NET Core MVC と Entity Framework Core のコントローラーとビューについて説明します。</span><span class="sxs-lookup"><span data-stu-id="12d91-101">This tutorial teaches ASP.NET Core MVC and Entity Framework Core with controllers and views.</span></span> <span data-ttu-id="12d91-102">[Razor Pages](xref:razor-pages/index) は代替プログラミング モデルです。</span><span class="sxs-lookup"><span data-stu-id="12d91-102">[Razor Pages](xref:razor-pages/index) is an alternative programming model.</span></span> <span data-ttu-id="12d91-103">新しい開発では、コントローラーやビューを使う MVC よりも Razor Pages を使うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="12d91-103">For new development, we recommend Razor Pages over MVC with controllers and views.</span></span> <span data-ttu-id="12d91-104">このチュートリアルの [Razor Pages](xref:data/ef-rp/intro) バージョンを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12d91-104">See the [Razor Pages](xref:data/ef-rp/intro) version of this tutorial.</span></span> <span data-ttu-id="12d91-105">それぞれのチュートリアルには、もう一方では説明されない内容が含まれています。</span><span class="sxs-lookup"><span data-stu-id="12d91-105">Each tutorial covers some material the other doesn't:</span></span>

<span data-ttu-id="12d91-106">この MVC のチュートリアルに含まれ、Razor Pages のチュートリアルには含まれていないこと:</span><span class="sxs-lookup"><span data-stu-id="12d91-106">Some things this MVC tutorial has that the Razor Pages tutorial doesn't:</span></span>

* <span data-ttu-id="12d91-107">データ モデルで継承を実装する</span><span class="sxs-lookup"><span data-stu-id="12d91-107">Implement inheritance in the data model</span></span>
* <span data-ttu-id="12d91-108">生 SQL クエリを実行する</span><span class="sxs-lookup"><span data-stu-id="12d91-108">Perform raw SQL queries</span></span>
* <span data-ttu-id="12d91-109">動的な LINQ を使ってコードを簡略化する</span><span class="sxs-lookup"><span data-stu-id="12d91-109">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="12d91-110">Razor Pages のチュートリアルに含まれ、このチュートリアルには含まれていないこと:</span><span class="sxs-lookup"><span data-stu-id="12d91-110">Some things the Razor Pages tutorial has that this one doesn't:</span></span>

* <span data-ttu-id="12d91-111">Select メソッドを使って関連データを読み込む</span><span class="sxs-lookup"><span data-stu-id="12d91-111">Use Select method to load related data</span></span>
* <span data-ttu-id="12d91-112">EF のベスト プラクティス。</span><span class="sxs-lookup"><span data-stu-id="12d91-112">Best practices for EF.</span></span>
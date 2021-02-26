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
ms.openlocfilehash: 3c21d2a5604c2dfc96624fb5d161537797925fef
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551459"
---
<span data-ttu-id="c95f2-101">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="c95f2-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="c95f2-102">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c95f2-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="c95f2-103">データベースで主キー用に必要となる `Id` フィールド。</span><span class="sxs-lookup"><span data-stu-id="c95f2-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="c95f2-104">`[DataType(DataType.Date)]`: [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性では、データの型 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="c95f2-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="c95f2-105">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="c95f2-105">With this attribute:</span></span>

  * <span data-ttu-id="c95f2-106">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c95f2-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="c95f2-107">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="c95f2-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="c95f2-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="c95f2-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536302"
---
<a name="csc"></a>

<span data-ttu-id="51b9c-101">サービスを登録し、オプションを構成する次の `ConfigureServices` メソッドについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="51b9c-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="51b9c-102">関連する登録グループは、サービスを登録するための拡張メソッドに移動できます。</span><span class="sxs-lookup"><span data-stu-id="51b9c-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="51b9c-103">たとえば、構成サービスは次のクラスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="51b9c-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="51b9c-104">残りのサービスは、同様のクラスに登録されます。</span><span class="sxs-lookup"><span data-stu-id="51b9c-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="51b9c-105">次の `ConfigureServices` メソッドでは、新しい拡張メソッドを使用してサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="51b9c-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="51b9c-106">**_注:_** 各 `services.Add{GROUP_NAME}` 拡張メソッドは、サービスを追加、場合によっては構成します。</span><span class="sxs-lookup"><span data-stu-id="51b9c-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="51b9c-107">たとえば、<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> ではビューを持つ MVC コントローラーで必要なサービスが追加され、<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> では Razor Pages で必要なサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="51b9c-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="51b9c-108">アプリをこの名前付け規則に従わせることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="51b9c-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="51b9c-109">拡張メソッドを <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 名前空間に配置して、サービス登録のグループをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="51b9c-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>

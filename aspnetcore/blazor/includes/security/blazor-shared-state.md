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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551424"
---
<span data-ttu-id="f10cd-101">Blazor サーバー アプリはサーバー メモリに存在します。</span><span class="sxs-lookup"><span data-stu-id="f10cd-101">Blazor server apps live in server memory.</span></span> <span data-ttu-id="f10cd-102">これは、同じプロセス内で複数のアプリがホストされていることを意味します。</span><span class="sxs-lookup"><span data-stu-id="f10cd-102">That means that there are multiple apps hosted within the same process.</span></span> <span data-ttu-id="f10cd-103">Blazor では各アプリ セッションに対して、独自の DI コンテナー スコープで回線が開始されます。</span><span class="sxs-lookup"><span data-stu-id="f10cd-103">For each app session, Blazor starts a circuit with its own DI container scope.</span></span> <span data-ttu-id="f10cd-104">つまり、スコープが指定されたサービスは、Blazor セッションごとに一意になるということです。</span><span class="sxs-lookup"><span data-stu-id="f10cd-104">That means that scoped services are unique per Blazor session.</span></span>

> [!WARNING]
> <span data-ttu-id="f10cd-105">特別な注意を払っていない限り、同じサーバーの共有状態のアプリがシングルトン サービスを使用することはお勧めできません。これにより、回線をまたいだユーザー状態のリークなど、セキュリティ上の脆弱性が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f10cd-105">We don't recommend apps on the same server share state using singleton services unless extreme care is taken, as this can introduce security vulnerabilities, such as leaking user state across circuits.</span></span>

<span data-ttu-id="f10cd-106">Blazor アプリでは、ステートフル シングルトン サービスを使用できます (特に専用に設計されている場合)。</span><span class="sxs-lookup"><span data-stu-id="f10cd-106">You can use stateful singleton services in Blazor apps if they are specifically designed for it.</span></span> <span data-ttu-id="f10cd-107">たとえば、メモリ キャッシュをシングルトンとして使用するのは問題ありません。これは、使用されるキャッシュ キーをユーザーが制御できない場合に、特定のエントリにアクセスするためにキーが必要になるためです。</span><span class="sxs-lookup"><span data-stu-id="f10cd-107">For example, it's ok to use a memory cache as a singleton because it requires a key to access a given entry, assuming users don't have control of what cache keys are used.</span></span>

<span data-ttu-id="f10cd-108">**また、セキュリティ上の理由から、Blazor アプリ内で <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> を使用することはできません。**</span><span class="sxs-lookup"><span data-stu-id="f10cd-108">**Additionally, again for security reasons, you must not use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> within Blazor apps.**</span></span> <span data-ttu-id="f10cd-109">Blazor アプリは、ASP.NET Core パイプラインのコンテキストの外部で実行されます。</span><span class="sxs-lookup"><span data-stu-id="f10cd-109">Blazor apps run outside of the context of the ASP.NET Core pipeline.</span></span> <span data-ttu-id="f10cd-110"><xref:Microsoft.AspNetCore.Http.HttpContext> は、<xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 内で使用できるとは限りません。また、Blazor アプリが開始されたコンテキストが保持されることも保証されません。</span><span class="sxs-lookup"><span data-stu-id="f10cd-110">The <xref:Microsoft.AspNetCore.Http.HttpContext> isn't guaranteed to be available within the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, nor is it guaranteed to be holding the context that started the Blazor app.</span></span>

<span data-ttu-id="f10cd-111">Blazor アプリに要求状態を渡す方法としては、アプリの初期レンダリングでルート コンポーネントのパラメーターを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f10cd-111">The recommended way to pass request state to the Blazor app is through parameters to the root component in the initial rendering of the app:</span></span>

* <span data-ttu-id="f10cd-112">Blazor アプリに渡すデータをすべて含むクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="f10cd-112">Define a class with all the data you want to pass to the Blazor app.</span></span>
* <span data-ttu-id="f10cd-113">その時点で利用可能な <xref:Microsoft.AspNetCore.Http.HttpContext> を使用して、Razor ページからそのデータを設定します。</span><span class="sxs-lookup"><span data-stu-id="f10cd-113">Populate that data from the Razor page using the <xref:Microsoft.AspNetCore.Http.HttpContext> available at that time.</span></span>
* <span data-ttu-id="f10cd-114">ルート コンポーネント (アプリ) へのパラメーターとして Blazor アプリにデータを渡します。</span><span class="sxs-lookup"><span data-stu-id="f10cd-114">Pass the data to the Blazor app as a parameter to the root component (App).</span></span>
* <span data-ttu-id="f10cd-115">アプリに渡されるデータを保持するために、ルート コンポーネントでパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="f10cd-115">Define a parameter in the root component to hold the data being passed to the app.</span></span>
* <span data-ttu-id="f10cd-116">アプリ内のユーザー固有のデータを使用します。または、アプリ全体で使用できるようにそのデータを <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 内のスコープ サービスにコピーします。</span><span class="sxs-lookup"><span data-stu-id="f10cd-116">Use the user-specific data within the app; or alternatively, copy that data into a scoped service within <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> so that it can be used across the app.</span></span>

<span data-ttu-id="f10cd-117">詳細およびプログラム例については、「<xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f10cd-117">For more information and example code, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

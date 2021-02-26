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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551236"
---
## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="919a8-101">クラス ライブラリで相互運用コードを共有する</span><span class="sxs-lookup"><span data-stu-id="919a8-101">Share interop code in a class library</span></span>

<span data-ttu-id="919a8-102">JS 相互運用コードは、クラス ライブラリに含めて NuGet パッケージ内でコードを共有することができます。</span><span class="sxs-lookup"><span data-stu-id="919a8-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="919a8-103">クラス ライブラリは、ビルドされたアセンブリでの JavaScript リソースの埋め込みを処理します。</span><span class="sxs-lookup"><span data-stu-id="919a8-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="919a8-104">JavaScript ファイルは、`wwwroot` フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="919a8-104">The JavaScript files are placed in the `wwwroot` folder.</span></span> <span data-ttu-id="919a8-105">ライブラリのビルド時にツールがリソースの埋め込みを行います。</span><span class="sxs-lookup"><span data-stu-id="919a8-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="919a8-106">ビルド済みの NuGet パッケージは、NuGet パッケージの参照と同じ方法で、アプリのプロジェクト ファイルで参照されます。</span><span class="sxs-lookup"><span data-stu-id="919a8-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="919a8-107">パッケージが復元されたら、アプリ コードを C# と同じように JavaScript に呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="919a8-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="919a8-108">詳細については、「<xref:blazor/components/class-libraries>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="919a8-108">For more information, see <xref:blazor/components/class-libraries>.</span></span>

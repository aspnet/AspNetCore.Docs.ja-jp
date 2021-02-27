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
ms.openlocfilehash: 8772f383a830936881564ca95a7f034ba08a5798
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551589"
---
<span data-ttu-id="35ab5-101">`FetchData` コンポーネントは次の方法を示します。</span><span class="sxs-lookup"><span data-stu-id="35ab5-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="35ab5-102">アクセス トークンをプロビジョニングする。</span><span class="sxs-lookup"><span data-stu-id="35ab5-102">Provision an access token.</span></span>
* <span data-ttu-id="35ab5-103">アクセス トークンを使用して、*Server* アプリで保護されたリソース API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="35ab5-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="35ab5-104">[`@attribute [Authorize]`](xref:mvc/views/razor#attribute) ディレクティブは、このコンポーネントにアクセスするためにユーザーを承認する必要があることを Blazor WebAssembly の承認システムに示します。</span><span class="sxs-lookup"><span data-stu-id="35ab5-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="35ab5-105">`Client` アプリに属性が存在しても、適切な資格情報を使用せずにサーバー上の API が呼び出されるのを防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="35ab5-105">The presence of the attribute in the `Client` app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="35ab5-106">また、`Server` アプリは、適切なエンドポイントでそれを正しく保護するために `[Authorize]` も使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="35ab5-106">The `Server` app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="35ab5-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> は、API を呼び出すために要求に追加できるアクセス トークンの要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="35ab5-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="35ab5-108">トークンがキャッシュされている場合、またはサービスがユーザーの操作なしで新しいアクセス トークンをプロビジョニングできる場合、トークン要求は成功します。</span><span class="sxs-lookup"><span data-stu-id="35ab5-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="35ab5-109">それ以外の場合、トークン要求は、[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントでキャッチされた <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> により失敗します。</span><span class="sxs-lookup"><span data-stu-id="35ab5-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="35ab5-110">要求に含める実際のトークンを取得するには、アプリが [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) を呼び出して、要求が成功したことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="35ab5-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="35ab5-111">要求が成功すると、トークン変数にアクセス トークンが設定されます。</span><span class="sxs-lookup"><span data-stu-id="35ab5-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="35ab5-112">トークンの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> プロパティは、`Authorization` 要求ヘッダーに含めるリテラル文字列を公開します。</span><span class="sxs-lookup"><span data-stu-id="35ab5-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="35ab5-113">ユーザーの操作なしでトークンをプロビジョニングできなかったために要求が失敗した場合、トークンの結果にリダイレクト URL が含まれます。</span><span class="sxs-lookup"><span data-stu-id="35ab5-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="35ab5-114">この URL に移動すると、認証が成功した後、ユーザーがログイン ページに移動してから、現在のページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="35ab5-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

---
title: HTTP REPL を使用して Web API をテストする
author: scottaddie
description: HTTP REPL .NET Core グローバル ツールを使用して、ASP.NET Core Web API を参照およびテストする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 05/20/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl
ms.openlocfilehash: efd2208044ad6392131216266afc34187d738b78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058975"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="cc6ef-103">HTTP REPL を使用して Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="cc6ef-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="cc6ef-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="cc6ef-105">HTTP Read-Eval-Print Loop (REPL) は:</span><span class="sxs-lookup"><span data-stu-id="cc6ef-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="cc6ef-106">.NET Core がサポートされているすべての場所でサポートされている、軽量なクロスプラットフォーム コマンドライン ツールです。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="cc6ef-107">ASP.NET Core Web API (および ASP.NET 以外の Core Web API) をテストし、その結果を表示する HTTP 要求を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="cc6ef-108">localhost や Azure App Service などの任意の環境でホストされている Web API をテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="cc6ef-109">次の [HTTP 動詞](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="cc6ef-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="cc6ef-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="cc6ef-111">GET</span><span class="sxs-lookup"><span data-stu-id="cc6ef-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="cc6ef-112">矢印</span><span class="sxs-lookup"><span data-stu-id="cc6ef-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="cc6ef-113">オプション</span><span class="sxs-lookup"><span data-stu-id="cc6ef-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="cc6ef-114">KB830347</span><span class="sxs-lookup"><span data-stu-id="cc6ef-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="cc6ef-115">POST</span><span class="sxs-lookup"><span data-stu-id="cc6ef-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="cc6ef-116">PUT</span><span class="sxs-lookup"><span data-stu-id="cc6ef-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="cc6ef-117">先に進むには、[サンプル ASP.NET Core Web API を表示またはダウンロードします](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc6ef-118">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="cc6ef-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="cc6ef-119">インストール</span><span class="sxs-lookup"><span data-stu-id="cc6ef-119">Installation</span></span>

<span data-ttu-id="cc6ef-120">HTTP REPL をインストールするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="cc6ef-121">[.Net Core グローバル ツール](/dotnet/core/tools/global-tools#install-a-global-tool)は、[Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet パッケージからインストールされます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="cc6ef-122">使用</span><span class="sxs-lookup"><span data-stu-id="cc6ef-122">Usage</span></span>

<span data-ttu-id="cc6ef-123">ツールのインストールが正常に完了したら、次のコマンドを実行して HTTP REPL を開始します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="cc6ef-124">使用可能な HTTP REPL コマンドを表示するには、次のコマンドのいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="cc6ef-125">次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="cc6ef-126">HTTP REPL では、コマンド補完が提供されています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="cc6ef-127"><kbd>Tab</kbd> キーを押すと、入力した文字または API エンドポイントを補完するコマンドの一覧が反復処理されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="cc6ef-128">次のセクションでは、使用可能な CLI コマンドの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="cc6ef-129">Web API に接続する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-129">Connect to the web API</span></span>

<span data-ttu-id="cc6ef-130">次のコマンドを実行して、Web API に接続します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="cc6ef-131">`<ROOT URI>` は、Web API のベース URI です。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="cc6ef-132">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="cc6ef-133">または、HTTP REPL の実行中に、次のコマンドをいつでも実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="cc6ef-134">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="cc6ef-135">Web API の Swagger ドキュメントを手動でポイントする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="cc6ef-136">上記の connect コマンドでは、自動的に Swagger ドキュメントの検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="cc6ef-137">何らかの理由でそれができない場合は、`--swagger` オプションを使用して、Web API の Swagger ドキュメントの URI を指定できます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="cc6ef-138">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="cc6ef-139">Web API 内を移動する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="cc6ef-140">使用可能なエンドポイントを表示する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-140">View available endpoints</span></span>

<span data-ttu-id="cc6ef-141">Web API アドレスの現在のパスにあるさまざまなエンドポイント (コントローラー) を一覧表示するには、`ls` コマンドまたは `dir` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="cc6ef-142">次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="cc6ef-143">前の出力では、`Fruits` と `People` の 2 つのコントローラーが使用可能であることが示されています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="cc6ef-144">どちらのコントローラーでも、パラメーターなしの HTTP GET 操作と POST 操作がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="cc6ef-145">特定のコントローラーに移動すると、詳細がわかります。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="cc6ef-146">たとえば、次のコマンドの出力は、`Fruits` コントローラーが HTTP GET、PUT、DELETE の各操作をサポートしていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="cc6ef-147">これらの各操作には、ルートで `id` パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="cc6ef-148">または、`ui` コマンドを実行して、ブラウザーで Web API の Swagger UI ページを開きます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="cc6ef-149">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="cc6ef-150">エンドポイントに移動する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-150">Navigate to an endpoint</span></span>

<span data-ttu-id="cc6ef-151">Web API の別のエンドポイントに移動するには、`cd` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="cc6ef-152">`cd` コマンドの後のパスでは大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="cc6ef-153">次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="cc6ef-154">HTTP REPL をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="cc6ef-155">HTTP REPL の既定の[色](#set-color-preferences)はカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="cc6ef-156">また、[既定のテキストエディター](#set-the-default-text-editor)を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="cc6ef-157">HTTP REPL の設定は、現在のセッションで永続化され、今後のセッションで受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="cc6ef-158">変更した設定は、次のファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="cc6ef-159">Linux</span><span class="sxs-lookup"><span data-stu-id="cc6ef-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="cc6ef-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="cc6ef-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="cc6ef-161">macOS</span><span class="sxs-lookup"><span data-stu-id="cc6ef-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="cc6ef-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="cc6ef-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="cc6ef-163">Windows</span><span class="sxs-lookup"><span data-stu-id="cc6ef-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="cc6ef-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="cc6ef-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="cc6ef-165">*.httpreplprefs* ファイルは起動時に読み込まれ、実行時の変更は監視されません。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="cc6ef-166">ファイルに対する手動の変更は、ツールを再起動した後でのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="cc6ef-167">設定を表示する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-167">View the settings</span></span>

<span data-ttu-id="cc6ef-168">使用可能な設定を表示するには、`pref get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="cc6ef-169">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="cc6ef-170">上記のコマンドでは、使用可能なキーと値のペアが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-170">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="cc6ef-171">色の設定を設定する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-171">Set color preferences</span></span>

<span data-ttu-id="cc6ef-172">応答の色付けは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="cc6ef-173">既定の HTTP REPL ツールの色分けをカスタマイズするには、変更する色に対応するキーを見つけます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="cc6ef-174">キーを検索する方法については、「[設定を表示する](#view-the-settings)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="cc6ef-175">たとえば、次のように、`colors.json` キー値を `Green` から `White` に変更します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="cc6ef-176">使用できるのは、[許可されている色](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)だけです。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="cc6ef-177">後続の HTTP 要求では、出力が新しい色で表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="cc6ef-178">特定の色キーが設定されていない場合、より汎用的なキーが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="cc6ef-179">このフォールバック動作を示すために、次の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="cc6ef-180">`colors.json.name` に値がない場合は、`colors.json.string` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="cc6ef-181">`colors.json.string` に値がない場合は、`colors.json.literal` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="cc6ef-182">`colors.json.literal` に値がない場合は、`colors.json` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="cc6ef-183">`colors.json` 値がない場合は、コマンド シェルの既定のテキストの色 (`AllowedColors.None`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="cc6ef-184">インデントのサイズを設定する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-184">Set indentation size</span></span>

<span data-ttu-id="cc6ef-185">応答インデント サイズのカスタマイズは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="cc6ef-186">既定のサイズは 2 つのスペースです。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-186">The default size is two spaces.</span></span> <span data-ttu-id="cc6ef-187">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-187">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="cc6ef-188">既定のサイズを変更するには、`formatting.json.indentSize` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="cc6ef-189">たとえば、常に 4 つのスペースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="cc6ef-190">後続の応答では、4 つのスペースの設定が優先されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-190">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="cc6ef-191">既定のテキスト エディターを設定する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-191">Set the default text editor</span></span>

<span data-ttu-id="cc6ef-192">既定では、HTTP REPL には、使用するように構成されたテキスト エディターはありません。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="cc6ef-193">HTTP 要求本文を必要とする Web API メソッドをテストするには、既定のテキスト エディターを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="cc6ef-194">HTTP REPL ツールでは、要求本文を作成する目的のためだけに構成されたテキスト エディターが起動されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="cc6ef-195">次のコマンドを実行して、優先テキストエディターを既定値として設定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="cc6ef-196">上記のコマンドで、`<EXECUTABLE>` はテキスト エディターの実行可能ファイルへの完全なパスです。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="cc6ef-197">たとえば、次のコマンドを実行して、既定のテキストエディターとして Visual Studio Code を設定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="cc6ef-198">Linux</span><span class="sxs-lookup"><span data-stu-id="cc6ef-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="cc6ef-199">macOS</span><span class="sxs-lookup"><span data-stu-id="cc6ef-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="cc6ef-200">Windows</span><span class="sxs-lookup"><span data-stu-id="cc6ef-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="cc6ef-201">特定の CLI 引数を使用して既定のテキスト エディターを起動するには、`editor.command.default.arguments` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="cc6ef-202">たとえば、Visual Studio Code が既定のテキスト エディターで、拡張機能が無効になっている新しいセッションでは HTTP REPL で常に Visual Studio Code を開きたいとします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="cc6ef-203">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="cc6ef-204">Swagger の検索パスを設定する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-204">Set the Swagger search paths</span></span>

<span data-ttu-id="cc6ef-205">HTTP REPL には既定の相対パスのセットがあり、`connect` コマンドで `--swagger` オプションが指定されていないときは、その相対パスを使用して Swagger ドキュメントが検索されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="cc6ef-206">これらの相対パスは、`connect` コマンドで指定されているルート パスおよびベース パスと組み合わされます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="cc6ef-207">既定の相対パスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-207">The default relative paths are:</span></span>

- <span data-ttu-id="cc6ef-208">*swagger.js*</span><span class="sxs-lookup"><span data-stu-id="cc6ef-208">*swagger.json*</span></span>
- <span data-ttu-id="cc6ef-209">*swagger/v1/swagger.jsオン*</span><span class="sxs-lookup"><span data-stu-id="cc6ef-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="cc6ef-210">*/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="cc6ef-210">*/swagger.json*</span></span>
- <span data-ttu-id="cc6ef-211">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="cc6ef-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="cc6ef-212">環境で別の検索パスのセットを使用するには、ユーザー設定 `swagger.searchPaths` を設定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="cc6ef-213">値としては、パイプで区切られた相対パスのリストを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="cc6ef-214">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="cc6ef-215">HTTP GET 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cc6ef-216">構文</span><span class="sxs-lookup"><span data-stu-id="cc6ef-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cc6ef-217">引数</span><span class="sxs-lookup"><span data-stu-id="cc6ef-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cc6ef-218">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cc6ef-219">オプション</span><span class="sxs-lookup"><span data-stu-id="cc6ef-219">Options</span></span>

<span data-ttu-id="cc6ef-220">`get` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="cc6ef-221">例</span><span class="sxs-lookup"><span data-stu-id="cc6ef-221">Example</span></span>

<span data-ttu-id="cc6ef-222">HTTP GET 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="cc6ef-223">それをサポートしているエンドポイントで `get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="cc6ef-224">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-224">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="cc6ef-225">`get` コマンドにパラメーターを渡すことによって、特定のレコードを取得します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="cc6ef-226">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-226">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="cc6ef-227">HTTP POST 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cc6ef-228">構文</span><span class="sxs-lookup"><span data-stu-id="cc6ef-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cc6ef-229">引数</span><span class="sxs-lookup"><span data-stu-id="cc6ef-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cc6ef-230">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cc6ef-231">オプション</span><span class="sxs-lookup"><span data-stu-id="cc6ef-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="cc6ef-232">例</span><span class="sxs-lookup"><span data-stu-id="cc6ef-232">Example</span></span>

<span data-ttu-id="cc6ef-233">HTTP POST 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="cc6ef-234">それをサポートしているエンドポイントで `post` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="cc6ef-235">前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="cc6ef-236">既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="cc6ef-237">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="cc6ef-238">既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="cc6ef-239">モデルの検証要件を満たすように JSON テンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="cc6ef-240">*.tmp* ファイルを保存して、テキスト エディターを閉じます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="cc6ef-241">コマンド シェルに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-241">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="cc6ef-242">HTTP PUT 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cc6ef-243">構文</span><span class="sxs-lookup"><span data-stu-id="cc6ef-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cc6ef-244">引数</span><span class="sxs-lookup"><span data-stu-id="cc6ef-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cc6ef-245">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cc6ef-246">オプション</span><span class="sxs-lookup"><span data-stu-id="cc6ef-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="cc6ef-247">例</span><span class="sxs-lookup"><span data-stu-id="cc6ef-247">Example</span></span>

<span data-ttu-id="cc6ef-248">HTTP PUT 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="cc6ef-249">*省略可能* : コマンドを実行し `get` て、変更前にデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-249">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="cc6ef-250">それをサポートしているエンドポイントで `put` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="cc6ef-251">前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="cc6ef-252">既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="cc6ef-253">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="cc6ef-254">既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="cc6ef-255">モデルの検証要件を満たすように JSON テンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="cc6ef-256">*.tmp* ファイルを保存して、テキスト エディターを閉じます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="cc6ef-257">コマンド シェルに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="cc6ef-258">*省略可能* : コマンドを発行し `get` て変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-258">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="cc6ef-259">たとえば、テキスト エディターで「Cherry」と入力すると、`get` は次を返します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="cc6ef-260">HTTP DELETE 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cc6ef-261">構文</span><span class="sxs-lookup"><span data-stu-id="cc6ef-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cc6ef-262">引数</span><span class="sxs-lookup"><span data-stu-id="cc6ef-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cc6ef-263">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cc6ef-264">オプション</span><span class="sxs-lookup"><span data-stu-id="cc6ef-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="cc6ef-265">例</span><span class="sxs-lookup"><span data-stu-id="cc6ef-265">Example</span></span>

<span data-ttu-id="cc6ef-266">HTTP DELETE 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="cc6ef-267">*省略可能* : コマンドを実行し `get` て、変更前にデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-267">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="cc6ef-268">それをサポートしているエンドポイントで `delete` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="cc6ef-269">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="cc6ef-270">*省略可能* : コマンドを発行し `get` て変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-270">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="cc6ef-271">この例では、`get` は次を返します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-271">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="cc6ef-272">HTTP PATCH 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cc6ef-273">構文</span><span class="sxs-lookup"><span data-stu-id="cc6ef-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cc6ef-274">引数</span><span class="sxs-lookup"><span data-stu-id="cc6ef-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cc6ef-275">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cc6ef-276">オプション</span><span class="sxs-lookup"><span data-stu-id="cc6ef-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="cc6ef-277">HTTP HEAD 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cc6ef-278">構文</span><span class="sxs-lookup"><span data-stu-id="cc6ef-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cc6ef-279">引数</span><span class="sxs-lookup"><span data-stu-id="cc6ef-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cc6ef-280">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cc6ef-281">オプション</span><span class="sxs-lookup"><span data-stu-id="cc6ef-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="cc6ef-282">HTTP OPTIONS 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cc6ef-283">構文</span><span class="sxs-lookup"><span data-stu-id="cc6ef-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cc6ef-284">引数</span><span class="sxs-lookup"><span data-stu-id="cc6ef-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cc6ef-285">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cc6ef-286">オプション</span><span class="sxs-lookup"><span data-stu-id="cc6ef-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="cc6ef-287">HTTP 要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-287">Set HTTP request headers</span></span>

<span data-ttu-id="cc6ef-288">HTTP 要求ヘッダーを設定するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="cc6ef-289">HTTP 要求でインラインを設定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="cc6ef-290">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="cc6ef-291">上記の方法では、個別の HTTP 要求ヘッダーごとに独自の `-h` オプションが必要です。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="cc6ef-292">HTTP 要求を送信する前に設定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="cc6ef-293">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="cc6ef-294">要求を送信する前にヘッダーを設定すると、コマンド シェル セッションの間はヘッダーが設定されたままになります。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="cc6ef-295">ヘッダーをクリアするには、空の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="cc6ef-296">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="cc6ef-297">セキュリティで保護されたエンドポイントをテストする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-297">Test secured endpoints</span></span>

<span data-ttu-id="cc6ef-298">HTTP REPL は、ログインしているユーザーの既定の資格情報を使用する方法と HTTP 要求ヘッダーを使用する方法の2つの方法で、セキュリティで保護されたエンドポイントのテストをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-298">The HTTP REPL supports the testing of secured endpoints in two ways: via the default credentials of the logged in user or through the use of HTTP request headers.</span></span> 

### <a name="default-credentials"></a><span data-ttu-id="cc6ef-299">既定の資格情報</span><span class="sxs-lookup"><span data-stu-id="cc6ef-299">Default credentials</span></span>

<span data-ttu-id="cc6ef-300">テスト対象の web API が IIS でホストされ、Windows 認証で保護されているシナリオについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-300">Consider a scenario in which the web API you're testing is hosted in IIS and is secured with Windows authentication.</span></span> <span data-ttu-id="cc6ef-301">ツールを実行するユーザーの資格情報が、テスト対象の HTTP エンドポイントに送信されるようにします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-301">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="cc6ef-302">ログインしているユーザーの既定の資格情報を渡すには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-302">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="cc6ef-303">次の `httpClient.useDefaultCredentials` ように設定し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-303">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="cc6ef-304">Web API に別の要求を送信する前に、ツールを終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-304">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="cc6ef-305">HTTP 要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="cc6ef-305">HTTP request headers</span></span>

<span data-ttu-id="cc6ef-306">サポートされている認証および承認スキームの例としては、基本認証、JWT ベアラー トークン、ダイジェスト認証などがあります。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-306">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="cc6ef-307">たとえば、次のコマンドを使用して、ベアラー トークンをエンドポイントに送信できます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-307">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="cc6ef-308">Azure でホストされたエンドポイントにアクセスしたり、[Azure REST API](/rest/api/azure/) を使用したりするには、ベアラー トークンが必要です。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-308">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="cc6ef-309">[Azure CLI](/cli/azure/) で自分の Azure サブスクリプションのベアラー トークンを取得するには、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-309">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="cc6ef-310">HTTP REPL では、HTTP 要求ヘッダーにベアラー トークンが設定され、Azure App Service Web Apps のリストが取得されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-310">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="cc6ef-311">Azure へのログイン:</span><span class="sxs-lookup"><span data-stu-id="cc6ef-311">Log in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="cc6ef-312">次のコマンドで、サブスクリプション ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-312">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="cc6ef-313">サブスクリプション ID をコピーし、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-313">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="cc6ef-314">次のコマンドで、ベアラー トークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-314">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="cc6ef-315">HTTP REPL を使用して、Azure REST API に接続します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-315">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="cc6ef-316">`Authorization` HTTP 要求ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-316">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="cc6ef-317">サブスクリプションに移動します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-317">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="cc6ef-318">サブスクリプションの Azure App Service Web Apps のリストを取得します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-318">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="cc6ef-319">次の応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-319">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="cc6ef-320">HTTP 要求の表示を切り替える</span><span class="sxs-lookup"><span data-stu-id="cc6ef-320">Toggle HTTP request display</span></span>

<span data-ttu-id="cc6ef-321">既定では、送信中の HTTP 要求の表示は抑制されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-321">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="cc6ef-322">コマンド シェル セッションの期間中は、対応する設定を変更できます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-322">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="cc6ef-323">要求の表示を有効にする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-323">Enable request display</span></span>

<span data-ttu-id="cc6ef-324">`echo on` コマンドを実行して、送信中の HTTP 要求を表示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-324">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="cc6ef-325">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-325">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="cc6ef-326">現在のセッションの後続の HTTP 要求では、要求ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-326">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="cc6ef-327">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-327">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="cc6ef-328">要求の表示を無効にする</span><span class="sxs-lookup"><span data-stu-id="cc6ef-328">Disable request display</span></span>

<span data-ttu-id="cc6ef-329">`echo off` コマンドを実行して、送信中の HTTP 要求の表示を抑制します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-329">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="cc6ef-330">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-330">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="cc6ef-331">[スクリプトの実行]</span><span class="sxs-lookup"><span data-stu-id="cc6ef-331">Run a script</span></span>

<span data-ttu-id="cc6ef-332">HTTP REPL コマンドの同じセットを頻繁に実行する場合は、それらをテキスト ファイルに格納することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-332">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="cc6ef-333">ファイル内のコマンドは、コマンド ラインで手動で実行した場合と同じ形式になります。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-333">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="cc6ef-334">これらのコマンドは、`run` コマンドを使用してバッチ方式で実行できます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-334">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="cc6ef-335">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-335">For example:</span></span>

1. <span data-ttu-id="cc6ef-336">改行で区切られた一連のコマンドを含むテキスト ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-336">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="cc6ef-337">例として、次のコマンドを含む *people-script.txt* ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-337">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="cc6ef-338">`run` コマンドを実行し、テキスト ファイルのパスを渡します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-338">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="cc6ef-339">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-339">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="cc6ef-340">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-340">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="cc6ef-341">出力を消去する</span><span class="sxs-lookup"><span data-stu-id="cc6ef-341">Clear the output</span></span>

<span data-ttu-id="cc6ef-342">HTTP REPL ツールによってコマンド シェルに書き込まれたすべての出力を削除するには、`clear` コマンドまたは `cls` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-342">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="cc6ef-343">例として、コマンド シェルに次の出力が含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-343">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="cc6ef-344">次のコマンドを実行して、出力を消去します。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-344">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="cc6ef-345">上記のコマンドを実行すると、コマンド シェルには次の出力のみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="cc6ef-345">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="cc6ef-346">その他の資料</span><span class="sxs-lookup"><span data-stu-id="cc6ef-346">Additional resources</span></span>

* [<span data-ttu-id="cc6ef-347">REST API 要求</span><span class="sxs-lookup"><span data-stu-id="cc6ef-347">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="cc6ef-348">HTTP REPL GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="cc6ef-348">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)

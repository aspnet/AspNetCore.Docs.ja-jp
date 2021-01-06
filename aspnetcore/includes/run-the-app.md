# <a name="visual-studio"></a>[<span data-ttu-id="44d2b-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44d2b-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44d2b-102">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="44d2b-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="44d2b-103">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="44d2b-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="44d2b-104">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="44d2b-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="44d2b-105">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="44d2b-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="44d2b-106">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="44d2b-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="44d2b-107">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="44d2b-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="44d2b-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44d2b-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="44d2b-109">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="44d2b-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="44d2b-110">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="44d2b-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="44d2b-111">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="44d2b-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="44d2b-112">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="44d2b-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="44d2b-113">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="44d2b-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="44d2b-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44d2b-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="44d2b-115">Visual Studio から **Opt-Cmd-Return** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="44d2b-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="44d2b-116">または、メニュー バーに移動して **[実行] > [デバッグなしで開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="44d2b-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="44d2b-117">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="44d2b-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

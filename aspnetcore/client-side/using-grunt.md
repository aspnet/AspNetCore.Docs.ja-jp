---
title: ASP.NET Core での Grunt の使用
author: rick-anderson
description: ASP.NET Core での Grunt の使用
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054555"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="65b6c-103">ASP.NET Core での Grunt の使用</span><span class="sxs-lookup"><span data-stu-id="65b6c-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="65b6c-104">Grunt は、スクリプトの縮小、TypeScript のコンパイル、コード品質の "lint" ツール、CSS プリプロセッサなど、クライアント開発をサポートするために必要なほとんどの反復作業を自動化する JavaScript タスク ランナーです。</span><span class="sxs-lookup"><span data-stu-id="65b6c-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="65b6c-105">Grunt は、Visual Studio で完全にサポートされています。</span><span class="sxs-lookup"><span data-stu-id="65b6c-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="65b6c-106">この例では、空の ASP.NET Core プロジェクトを開始点として使用して、クライアントのビルド プロセスをゼロから自動化する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="65b6c-107">完成した例では、ターゲットの展開ディレクトリのクリーンアップ、JavaScript ファイルの結合、コード品質の確認、JavaScript ファイル コンテンツの圧縮、および Web アプリケーションのルートへの展開を行います。</span><span class="sxs-lookup"><span data-stu-id="65b6c-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="65b6c-108">次のパッケージを使用します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-108">We will use the following packages:</span></span>

* <span data-ttu-id="65b6c-109">**grunt**:Grunt タスク ランナー パッケージ。</span><span class="sxs-lookup"><span data-stu-id="65b6c-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="65b6c-110">**grunt-contrib-clean**:ファイルまたはディレクトリを削除するプラグイン。</span><span class="sxs-lookup"><span data-stu-id="65b6c-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="65b6c-111">**grunt-contrib-jshint**:JavaScript コードの品質を確認するプラグイン。</span><span class="sxs-lookup"><span data-stu-id="65b6c-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="65b6c-112">**grunt-contrib-concat**:ファイルを 1 つのファイルに結合するプラグイン。</span><span class="sxs-lookup"><span data-stu-id="65b6c-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="65b6c-113">**grunt-contrib-uglify**:サイズを小さくするために JavaScript を縮小するプラグイン。</span><span class="sxs-lookup"><span data-stu-id="65b6c-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="65b6c-114">**grunt-contrib-watch**:ファイル アクティビティを監視するプラグイン。</span><span class="sxs-lookup"><span data-stu-id="65b6c-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="65b6c-115">アプリケーションの準備</span><span class="sxs-lookup"><span data-stu-id="65b6c-115">Preparing the application</span></span>

<span data-ttu-id="65b6c-116">最初に、空の新しい Web アプリケーションを設定し、TypeScript サンプル ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="65b6c-117">TypeScript ファイルは、既定の Visual Studio 設定を使用して JavaScript に自動的にコンパイルされ、Grunt を使用して処理する未加工の素材になります。</span><span class="sxs-lookup"><span data-stu-id="65b6c-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="65b6c-118">Visual Studio で、新しい `ASP.NET Web Application` を作成します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="65b6c-119">**[新しい ASP.NET プロジェクト]** ダイアログで、ASP.NET Core の **[空]** テンプレートを選択し、[OK] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="65b6c-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="65b6c-120">ソリューション エクスプローラーで、プロジェクトの構造を確認します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="65b6c-121">`\src` フォルダーには、空の `wwwroot` ノードと `Dependencies` ノードが含まれます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![空の Web ソリューション](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="65b6c-123">`TypeScript` という名前の新しいフォルダーをプロジェクト ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="65b6c-124">ファイルを追加する前に、Visual Studio で TypeScript ファイルに対して [保存時にコンパイル] オプションがオンになっていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="65b6c-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="65b6c-125">**[ツール]**  >  **[オプション]**  >  **[テキスト エディター]**  >  **[TypeScript]**  >  **[プロジェクト]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![TypeScript ファイルの自動コンパイルを設定するオプション](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="65b6c-127">`TypeScript` ディレクトリを右クリックし、コンテキスト メニューで **[追加] > [新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="65b6c-128">**[JavaScript ファイル]** 項目を選択し、ファイルに *Tastes.ts* という名前を指定します (\*.ts 拡張子に注意してください)。</span><span class="sxs-lookup"><span data-stu-id="65b6c-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="65b6c-129">次の TypeScript コード行をファイルにコピーします (保存すると、新しい *Tastes.js* ファイルが JavaScript ソースと共に表示されます)。</span><span class="sxs-lookup"><span data-stu-id="65b6c-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="65b6c-130">2 つ目のファイルを **TypeScript** ディレクトリに追加し、`Food.ts` という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="65b6c-131">次のコードをファイルにコピーします。</span><span class="sxs-lookup"><span data-stu-id="65b6c-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="65b6c-132">NPM の構成</span><span class="sxs-lookup"><span data-stu-id="65b6c-132">Configuring NPM</span></span>

<span data-ttu-id="65b6c-133">次に、grunt と grunt タスクをダウンロードするように NPM を構成します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="65b6c-134">ソリューション エクスプローラーでプロジェクトを右クリックし、コンテキスト メニューで **[追加] > [新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="65b6c-135">**[NPM 構成ファイル]** 項目を選択し、既定の名前 *package.json* をそのまま使用して、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="65b6c-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="65b6c-136">*package.json* ファイルで、`devDependencies` オブジェクトの中かっこの内側に「grunt」と入力します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="65b6c-137">Intellisense の一覧から [`grunt`] を選択し、Enter キーを押します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="65b6c-138">Visual Studio によって grunt パッケージ名が引用され、コロンが追加されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="65b6c-139">コロンの右側で、Intellisense の一覧の一番上にある最新の安定バージョンのパッケージを選択します (Intellisense が表示されない場合は `Ctrl-Space` キーを押します)。</span><span class="sxs-lookup"><span data-stu-id="65b6c-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="65b6c-141">NPM では、依存関係を整理するために[セマンティック バージョニング](https://semver.org/)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="65b6c-142">セマンティック バージョニングは SemVer とも呼ばれ、\<major>.\<minor>.\<patch> の番号設定によってパッケージを識別するものです。Intellisense では、いくつかの共通の選択肢だけを表示することで、セマンティック バージョニングが簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="65b6c-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="65b6c-143">Intellisense の一覧の一番上にある項目 (上の例では 0.4.5) は、最新の安定バージョンのパッケージと見なされます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="65b6c-144">キャレット (^) 記号は最新のメジャー バージョンと一致し、チルダ (~) は最新のマイナー バージョンと一致します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="65b6c-145">SemVer で提供される完全な表現のガイドとして、[NPM SemVer バージョン パーサーのリファレンス](https://www.npmjs.com/package/semver)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65b6c-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="65b6c-146">次の例に示すように、*clean*、*jshint*、*concat*、*uglify*、および *watch* に対して grunt-contrib-\* パッケージを読み込むよう、さらに依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-146">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="65b6c-147">バージョンは例と一致している必要はありません。</span><span class="sxs-lookup"><span data-stu-id="65b6c-147">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="65b6c-148">*package.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-148">Save the *package.json* file.</span></span>

<span data-ttu-id="65b6c-149">各 `devDependencies` 項目のパッケージが、各パッケージに必要なすべてのファイルと共にダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="65b6c-150">**ソリューション エクスプローラー** で **[すべてのファイルを表示]** ボタンを有効にすると、*node_modules* ディレクトリ内のパッケージ ファイルを確認できます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="65b6c-152">必要に応じて、**ソリューション エクスプローラー** で `Dependencies\NPM` を右クリックし、 **[パッケージの復元]** メニュー オプションを選択することで、依存関係を手動で復元できます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![パッケージの復元](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="65b6c-154">Grunt の構成</span><span class="sxs-lookup"><span data-stu-id="65b6c-154">Configuring Grunt</span></span>

<span data-ttu-id="65b6c-155">Grunt は、*Gruntfile.js* という名前のマニフェストを使用して構成されます。このマニフェストでは、手動で実行したり、Visual Studio のイベントに基づいて自動的に実行されるように構成したりできるタスクの定義、読み込み、および登録が行われます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="65b6c-156">プロジェクトを右クリックし、 **[追加]**  >  **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-156">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="65b6c-157">**[JavaScript ファイル]** 項目テンプレートを選択し、名前を *Gruntfile.js* に変更して、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="65b6c-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="65b6c-158">次のコードを *Gruntfile.js* に追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-158">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="65b6c-159">`initConfig` 関数によって各パッケージのオプションが設定され、モジュールの残りの部分によってタスクの読み込みと登録が行われます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="65b6c-160">次の *Gruntfile.js* の例に示すように、`initConfig` 関数の内部に `clean` タスクのオプションを追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="65b6c-161">`clean` タスクで、ディレクトリ文字列の配列が受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="65b6c-162">このタスクにより、*wwwroot/lib* からファイルが削除され、 */temp* ディレクトリ全体が削除されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="65b6c-163">`initConfig` 関数の下に、`grunt.loadNpmTasks` の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="65b6c-164">これにより、タスクが Visual Studio から実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="65b6c-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="65b6c-165">*Gruntfile.js* を保存します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-165">Save *Gruntfile.js*.</span></span> <span data-ttu-id="65b6c-166">ファイルは次のスクリーンショットのようになります。</span><span class="sxs-lookup"><span data-stu-id="65b6c-166">The file should look something like the screenshot below.</span></span>

    ![初期の Gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="65b6c-168">*[Gruntfile.js]* を右クリックし、コンテキスト メニューで **[タスク ランナー エクスプローラー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="65b6c-169">**[タスク ランナー エクスプローラー]** ウィンドウが開きます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-169">The **Task Runner Explorer** window will open.</span></span>

    ![[タスク ランナー エクスプローラー] メニュー](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="65b6c-171">**タスク ランナー エクスプローラー** の **[タスク]** の下に `clean` が表示されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![タスク ランナー エクスプローラーの [タスク] 一覧](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="65b6c-173">[clean] タスクを右クリックし、コンテキスト メニューで **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="65b6c-174">コマンド ウィンドウに、タスクの進行状況が表示されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-174">A command window displays progress of the task.</span></span>

    ![タスク ランナー エクスプローラーでの clean タスクの実行](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="65b6c-176">クリーンアップするファイルまたはディレクトリはまだありません。</span><span class="sxs-lookup"><span data-stu-id="65b6c-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="65b6c-177">必要に応じて、ソリューション エクスプローラーでこれらを手動で作成し、clean タスクをテストとして実行できます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="65b6c-178">`initConfig` 関数で、次のコードを使用して `concat` のエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="65b6c-179">`src` プロパティの配列には、結合するファイルが、結合する順序でリストされます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="65b6c-180">`dest` プロパティにより、生成された結合ファイルへのパスが割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="65b6c-181">上記のコードの `all` プロパティは、ターゲットの名前です。</span><span class="sxs-lookup"><span data-stu-id="65b6c-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="65b6c-182">ターゲットは、複数のビルド環境が許可されるいくつかの Grunt タスクで使用されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="65b6c-183">IntelliSense を使用して組み込みのターゲットを表示するか、独自のターゲットを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="65b6c-184">次のコードを使用して、`jshint` タスクを追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="65b6c-185">jshint `code-quality` ユーティリティは、*temp* ディレクトリにあるすべての JavaScript ファイルに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="65b6c-186">オプション "-W069" は、JavaScript でドット表記の代わりに角かっこ構文 (たとえば、`Tastes.Sweet` の代わりに `Tastes["Sweet"]`) を使用してプロパティが割り当てられると、jshint によって生成されるエラーです。</span><span class="sxs-lookup"><span data-stu-id="65b6c-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="65b6c-187">このオプションによって警告がオフになり、残りのプロセスを続行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="65b6c-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="65b6c-188">次のコードを使用して、`uglify` タスクを追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="65b6c-189">このタスクでは、temp ディレクトリにある *combined.js* ファイルが縮小され、標準の名前付け規則 *\<file name\>.min.js* に従って、結果ファイルが wwwroot/lib に作成されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="65b6c-190">`grunt-contrib-clean` を読み込む `grunt.loadNpmTasks` の呼び出しの下で、次のコードを使用して、jshint、concat、および uglify に対して同じ呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="65b6c-191">*Gruntfile.js* を保存します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-191">Save *Gruntfile.js*.</span></span> <span data-ttu-id="65b6c-192">ファイルは次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="65b6c-192">The file should look something like the example below.</span></span>

    ![完全な grunt ファイルの例](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="65b6c-194">**タスク ランナー エクスプローラー** の [タスク] 一覧に、`clean`、`concat`、`jshint`、および `uglify` の各タスクが含まれることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="65b6c-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="65b6c-195">各タスクを順番に実行し、**ソリューション エクスプローラー** の結果を確認します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-195">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="65b6c-196">各タスクはエラーなしに実行されるはずです。</span><span class="sxs-lookup"><span data-stu-id="65b6c-196">Each task should run without errors.</span></span>

    ![タスク ランナー エクスプローラーでの各タスクの実行](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="65b6c-198">concat タスクでは、新しい *combined.js* ファイルが作成され、temp ディレクトリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="65b6c-199">`jshint` タスクは単に実行されるだけで、出力は生成されません。</span><span class="sxs-lookup"><span data-stu-id="65b6c-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="65b6c-200">`uglify` タスクでは、新しい *combined.min.js* ファイルが作成され、*wwwroot/lib* に格納されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="65b6c-201">完了すると、ソリューションは次のスクリーンショットのようになります。</span><span class="sxs-lookup"><span data-stu-id="65b6c-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![すべてのタスク後のソリューション エクスプローラー](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="65b6c-203">各パッケージのオプションの詳細については、[https://www.npmjs.com/](https://www.npmjs.com/) にアクセスし、メイン ページの検索ボックスでパッケージ名を検索してください。</span><span class="sxs-lookup"><span data-stu-id="65b6c-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="65b6c-204">たとえば、grunt-contrib-clean パッケージを参照して、すべてのパラメーターを説明するドキュメント リンクを取得できます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="65b6c-205">すべてをまとめる</span><span class="sxs-lookup"><span data-stu-id="65b6c-205">All together now</span></span>

<span data-ttu-id="65b6c-206">Grunt `registerTask()` メソッドを使用して、一連のタスクを特定のシーケンスで実行します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="65b6c-207">たとえば、上記の手順の例を clean -> concat -> jshint -> uglify の順に実行するには、次のコードをモジュールに追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="65b6c-208">このコードは、initConfig の外部で loadNpmTasks() 呼び出しと同じレベルに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65b6c-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="65b6c-209">新しいタスクが、タスク ランナー エクスプローラーの [エイリアス タスク] の下に表示されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="65b6c-210">このタスクは、他のタスクと同様に右クリックして実行できます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="65b6c-211">`all` タスクでは、`clean`、`concat`、`jshint`、`uglify` が順番に実行されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![エイリアス grunt タスク](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="65b6c-213">変更の監視</span><span class="sxs-lookup"><span data-stu-id="65b6c-213">Watching for changes</span></span>

<span data-ttu-id="65b6c-214">`watch` タスクでは、ファイルとディレクトリが監視されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="65b6c-215">watch によって変更が検出されると、タスクが自動的にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="65b6c-216">次のコードを initConfig に追加して、TypeScript ディレクトリ内の \*.js ファイルに対する変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="65b6c-217">JavaScript ファイルが変更さると、`watch` によって `all` タスクが実行されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="65b6c-218">タスク ランナー エクスプローラーで `watch` タスクを表示するよう、`loadNpmTasks()` の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="65b6c-219">タスク ランナー エクスプローラーで [watch] タスクを右クリックし、コンテキスト メニューで [実行] を選択します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="65b6c-220">実行中の watch タスクを表示するコマンド ウィンドウに、"Waiting…" メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="65b6c-221">TypeScript ファイルの 1 つを開き、スペースを追加して、ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="65b6c-222">これにより、watch タスクがトリガーされ、他のタスクがトリガーされて順番に実行されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="65b6c-223">次のスクリーンショットは、サンプルの実行を示しています。</span><span class="sxs-lookup"><span data-stu-id="65b6c-223">The screenshot below shows a sample run.</span></span>

![実行中のタスクの出力](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="65b6c-225">Visual Studio イベントへのバインド</span><span class="sxs-lookup"><span data-stu-id="65b6c-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="65b6c-226">Visual Studio で作業するたびに手動でタスクを開始する場合を除き、 **[ビルド前]** 、 **[ビルド後]** 、 **[クリーン]** 、および **[プロジェクトを開く]** の各イベントにタスクをバインドします。</span><span class="sxs-lookup"><span data-stu-id="65b6c-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="65b6c-227">`watch` をバインドして、Visual Studio が開くたびに実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="65b6c-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="65b6c-228">タスク ランナー エクスプローラーで [watch] タスクを右クリックし、コンテキスト メニューで **[バインド]**  >  **[プロジェクトを開く]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65b6c-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![[プロジェクトを開く] にタスクをバインドする](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="65b6c-230">プロジェクトをアンロードして再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="65b6c-230">Unload and reload the project.</span></span> <span data-ttu-id="65b6c-231">プロジェクトが再度読み込まれると、watch タスクの実行が自動的に開始されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="65b6c-232">まとめ</span><span class="sxs-lookup"><span data-stu-id="65b6c-232">Summary</span></span>

<span data-ttu-id="65b6c-233">Grunt は、クライアントによってビルドされたほとんどのタスクを自動化するために使用できる強力なタスク ランナーです。</span><span class="sxs-lookup"><span data-stu-id="65b6c-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="65b6c-234">Grunt により、NPM を利用してパッケージが配信され、ツールが Visual Studio と統合されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="65b6c-235">Visual Studio のタスク ランナー エクスプローラーでは、構成ファイルへの変更が検出され、タスクの実行、実行中のタスクの表示、および Visual Studio イベントへのタスクのバインドを行うための便利なインターフェイスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="65b6c-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>

---
title: Blazor Todo リスト アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 106e1119db777074b5eae24f5d7e216e6127ca13
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238303"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="82ccf-103">Blazor Todo リスト アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="82ccf-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="82ccf-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="82ccf-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="82ccf-105">このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="82ccf-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="82ccf-107">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="82ccf-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="82ccf-108">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="82ccf-108">Modify Razor components</span></span>
> * <span data-ttu-id="82ccf-109">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="82ccf-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="82ccf-110">Blazor アプリでルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="82ccf-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="82ccf-111">このチュートリアルの最後には、動作する ToDo リスト アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="82ccf-112">前提条件</span><span class="sxs-lookup"><span data-stu-id="82ccf-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="82ccf-113">ToDo リスト Blazor アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="82ccf-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="82ccf-114">コマンド シェルで `TodoList` という名前の新しい Blazor アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="82ccf-115">上記のコマンドでは、アプリを保持するために `-o|--output` オプションを指定して、`TodoList` という名前のフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="82ccf-116">`TodoList` フォルダーは、プロジェクトの *ルート フォルダー* です。</span><span class="sxs-lookup"><span data-stu-id="82ccf-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="82ccf-117">次のコマンドを使用して、ディレクトリを `TodoList` フォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="82ccf-118">次のコマンドを使用して、新しい `Todo` Razor コンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="82ccf-119">前のコマンドの `-n|--name` オプションで、新しい Razor コンポーネントの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="82ccf-120">新しいコンポーネントは、`-o|--output` オプションを使用してプロジェクトの `Pages` フォルダーに作成されます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="82ccf-121">Razor コンポーネント ファイル名の先頭文字は、大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="82ccf-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="82ccf-122">`Pages` フォルダーを開き、`Todo` コンポーネントのファイル名の先頭が大文字 `T` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="82ccf-123">ファイル名は `Todo.razor` のはずです。</span><span class="sxs-lookup"><span data-stu-id="82ccf-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="82ccf-124">任意のファイル エディターで `Todo` コンポーネントを開き、`/todo` の相対 URL を使用して、`@page` Razor ディレクティブをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="82ccf-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="82ccf-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo0.razor?highlight=1)]

   <span data-ttu-id="82ccf-126">`Pages/Todo.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="82ccf-127">ナビゲーション バーに `Todo` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="82ccf-128">`NavMenu` コンポーネントはアプリのレイアウトで使用されます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="82ccf-129">レイアウトは、アプリ内でのコンテンツの重複を回避できるようにするコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="82ccf-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="82ccf-130">アプリによってコンポーネントの URL が読み込まれるときに、`NavLink` コンポーネントによりそのアプリの UI でキューが指定されます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="82ccf-131">`NavMenu` コンポーネントの順不同のリスト (`<ul>...</ul>`) で、次のリスト項目 (`<li>...</li>`) と、`Todo` コンポーネントの `NavLink` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="82ccf-132">`Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="82ccf-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="82ccf-133">`Shared/NavMenu.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="82ccf-134">`TodoList` フォルダーからコマンド シェルで [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) コマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="82ccf-135">アプリが実行された後、そのアプリのナビゲーション バーにある **[`Todo`]** リンクを選択して、新しい Todo ページにアクセスします。これにより、`/todo` にページが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="82ccf-136">アプリでコマンド シェルを実行したままにします。</span><span class="sxs-lookup"><span data-stu-id="82ccf-136">Leave the app running the command shell.</span></span> <span data-ttu-id="82ccf-137">ファイルが保存されるたびに、アプリが自動的に再構築されます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="82ccf-138">コンパイルおよび再起動中は、ブラウザーからアプリへの接続が一時的に失われます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="82ccf-139">接続が再確立されると、ブラウザーのページが自動的に再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="82ccf-140">Todo アイテムを表すクラスを保持するために、プロジェクト (`TodoList` フォルダー) のルートに `TodoItem.cs` ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="82ccf-141">`TodoItem` クラス用に次の C# コードを使います。</span><span class="sxs-lookup"><span data-stu-id="82ccf-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="82ccf-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="82ccf-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]
   
   > [!NOTE]
   > <span data-ttu-id="82ccf-143">Visual Studio を使用して `ToDoItem.cs` ファイルと `ToDoItem` クラスを作成する場合は、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-143">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="82ccf-144">Visual Studio によってクラス用に生成される名前空間を削除します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-144">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="82ccf-145">前のコード ブロックの **[コピー]** ボタンを使用し、Visual Studio によって生成されるファイルの内容全体を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-145">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="82ccf-146">`Todo` コンポーネントに戻り、次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-146">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="82ccf-147">Todo アイテム用のフィールドを `@code` ブロックに追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-147">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="82ccf-148">`Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-148">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="82ccf-149">各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-149">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="82ccf-150">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="82ccf-150">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="82ccf-151">アプリには、リストに Todo 項目を追加するための UI 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="82ccf-151">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="82ccf-152">順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-152">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo3.razor?highlight=12-13)]

1. <span data-ttu-id="82ccf-153">`TodoItem.cs` ファイルと更新された `Pages/Todo.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-153">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="82ccf-154">コマンド シェルでは、ファイルが保存されるとアプリが自動的に再構築されます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-154">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="82ccf-155">ブラウザーからアプリへの接続は一時的に失われ、接続が再確立されるとページが再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-155">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="82ccf-156">**`Add todo`** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーがアタッチされていないためです。</span><span class="sxs-lookup"><span data-stu-id="82ccf-156">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="82ccf-157">`Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使用して、そのメソッドをボタン用に登録します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-157">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="82ccf-158">ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-158">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="82ccf-159">新しい Todo アイテムのタイトルを取得するには、`@code` ブロックの先頭に `newTodo` 文字列フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-159">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo5.razor?highlight=3)]

   <span data-ttu-id="82ccf-160">`@bind` 属性を使用して `newTodo` をバインドするようにテキストの `<input>` 要素を変更します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-160">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="82ccf-161">指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-161">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="82ccf-162">`newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。</span><span class="sxs-lookup"><span data-stu-id="82ccf-162">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo6.razor?highlight=19-26)]

1. <span data-ttu-id="82ccf-163">`Pages/Todo.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-163">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="82ccf-164">このアプリは、コマンド シェルで自動的にリビルドされます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-164">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="82ccf-165">ブラウザーからアプリに再接続された後、ページがブラウザーに再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-165">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="82ccf-166">各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-166">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="82ccf-167">各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="82ccf-167">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="82ccf-168">`@todo.Title` を、`@bind` で `todo.Title` にバインドされた `<input>` 要素に変更します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-168">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo7.razor?highlight=4-7)]

1. <span data-ttu-id="82ccf-169">`<h3>` ヘッダーを更新し、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。</span><span class="sxs-lookup"><span data-stu-id="82ccf-169">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="82ccf-170">完成した `Todo` コンポーネント (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="82ccf-170">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="82ccf-171">`Pages/Todo.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-171">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="82ccf-172">このアプリは、コマンド シェルで自動的にリビルドされます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-172">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="82ccf-173">ブラウザーからアプリに再接続された後、ページがブラウザーに再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-173">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="82ccf-174">アイテムを追加し、アイテムを編集し、Todo アイテムに完了のマークを付けて、コンポーネントをテストします。</span><span class="sxs-lookup"><span data-stu-id="82ccf-174">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="82ccf-175">完了したら、コマンド シェルでアプリをシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="82ccf-175">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="82ccf-176">多くのコマンド シェルでは、<kbd>Ctrl</kbd>+<kbd>c</kbd> キーボード コマンドを使用してアプリを停止できます。</span><span class="sxs-lookup"><span data-stu-id="82ccf-176">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="82ccf-177">次の手順</span><span class="sxs-lookup"><span data-stu-id="82ccf-177">Next steps</span></span>

<span data-ttu-id="82ccf-178">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="82ccf-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="82ccf-179">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="82ccf-179">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="82ccf-180">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="82ccf-180">Modify Razor components</span></span>
> * <span data-ttu-id="82ccf-181">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="82ccf-181">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="82ccf-182">Blazor アプリでルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="82ccf-182">Use routing in a Blazor app</span></span>

<span data-ttu-id="82ccf-183">ASP.NET Core Blazor 用のツールについて学習します。</span><span class="sxs-lookup"><span data-stu-id="82ccf-183">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>

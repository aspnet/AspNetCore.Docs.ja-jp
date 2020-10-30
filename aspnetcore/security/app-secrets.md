---
title: ASP.NET Core での開発におけるアプリシークレットの安全な保存
author: rick-anderson
description: ASP.NET Core アプリの開発中に、機密情報をアプリシークレットとして格納および取得する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
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
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056869"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core での開発におけるアプリシークレットの安全な保存

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このドキュメントでは、開発用コンピューターでの ASP.NET Core アプリの開発時に機密データを格納および取得する方法について説明します。 パスワードやその他の機密データをソースコードに格納しないでください。 運用環境のシークレットは、開発またはテストには使用しないでください。 シークレットはアプリと一緒にデプロイしないでください。 代わりに、環境変数、Azure Key Vault などの制御された方法を使用して、運用環境でシークレットを使用できるようにする必要があります。 [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを格納し、保護することができます。

## <a name="environment-variables"></a>環境変数

環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。 環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。

**個々のユーザーアカウント** のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。 既定のデータベース接続文字列は、プロジェクトのファイル内の *appsettings.json* キーと共に含まれ `DefaultConnection` ます。 既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。 アプリの展開時に、 `DefaultConnection` キー値は環境変数の値でオーバーライドできます。 環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。

> [!WARNING]
> 環境変数は通常、暗号化されていないプレーンテキストで格納されます。 コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。 ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>シークレットマネージャー

Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。 このコンテキストでは、機密データの一部がアプリシークレットになります。 アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。 アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。 アプリシークレットがソース管理にチェックインされていません。

> [!WARNING]
> シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。 開発目的でのみ使用されます。 キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。

## <a name="how-the-secret-manager-tool-works"></a>Secret Manager ツールの動作

Secret Manager ツールは、値の格納場所や方法などの実装の詳細を抽象化します。 このツールは、実装の詳細を把握していなくても使用できます。 値は、ローカルコンピューター上のシステムで保護されたユーザープロファイルフォルダー内の JSON 構成ファイルに格納されます。

# <a name="windows"></a>[Windows](#tab/windows)

ファイルシステムのパス:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

ファイルシステムのパス:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

上記のファイルパスで、を `<user_secrets_id>` `UserSecretsId` *.csproj* ファイルで指定された値に置き換えます。

シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。 これらの実装の詳細は変更される可能性があります。 たとえば、シークレット値は暗号化されませんが、将来の可能性があります。

## <a name="enable-secret-storage"></a>シークレットストレージを有効にする

Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。

Secret Manager ツールには .NET Core SDK 3.0.100 以降のコマンドが含まれてい `init` ます。 ユーザーシークレットを使用するには、プロジェクトディレクトリで次のコマンドを実行します。

```dotnetcli
dotnet user-secrets init
```

上のコマンドは、 `UserSecretsId` `PropertyGroup` *.csproj* ファイルの内に要素を追加します。 既定では、の内部テキスト `UserSecretsId` は GUID です。 内部テキストは任意ですが、プロジェクトに固有です。

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから [ **ユーザーシークレットの管理** ] を選択します。 このジェスチャは、 `UserSecretsId` GUID が設定された要素を .csproj ファイルに追加し *ます* 。

## <a name="set-a-secret"></a>シークレットを設定する

キーとその値で構成されるアプリシークレットを定義します。 シークレットは、プロジェクトの値に関連付けられ `UserSecretsId` ます。 たとえば、 *.csproj* ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

前の例では、コロンは、がプロパティを持つオブジェクトリテラルであることを示して `Movies` い `ServiceApiKey` ます。

シークレットマネージャーツールは、他のディレクトリからも使用できます。 `--project` *.Csproj* ファイルが存在するファイルシステムパスを指定するには、オプションを使用します。 次に例を示します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio での JSON 構造のフラット化

Visual Studio の [ **ユーザーシークレットの管理** ] ジェスチャでは、テキストエディターでファイル *のsecrets.js* が開きます。 *secrets.js* の内容を、格納されるキーと値のペアに置き換えます。 次に例を示します。

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 構造体は、またはを使用した変更後にフラット化され `dotnet user-secrets remove` `dotnet user-secrets set` ます。 たとえば、を実行 `dotnet user-secrets remove "Movies:ConnectionString"` すると、 `Movies` オブジェクトリテラルが折りたたまれます。 変更後のファイルは次のようになります。

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>複数のシークレットを設定する

シークレットのバッチは、JSON をコマンドにパイプすることによって設定でき `set` ます。 次の例では、ファイルの内容の *input.js* がコマンドにパイプされてい `set` ます。

# <a name="windows"></a>[Windows](#tab/windows)

コマンドシェルを開き、次のコマンドを実行します。

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

コマンドシェルを開き、次のコマンドを実行します。

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>シークレットへのアクセス

[ASP.NET Core 構成 API](xref:fundamentals/configuration/index)は、シークレットマネージャーのシークレットへのアクセスを提供します。

ユーザーシークレットの構成ソースは、プロジェクトがを呼び出して、 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 構成済みの既定値でホストの新しいインスタンスを初期化するときに、自動的に開発モードで追加されます。 `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>がの場合、 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> を呼び出し <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> ます。

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

`CreateDefaultBuilder`が呼び出されない場合は、を呼び出してユーザーシークレットの構成ソースを明示的に追加し <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> ます。 `AddUserSecrets`次の例に示すように、アプリが開発環境で実行されている場合にのみ、を呼び出します。

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

ユーザーシークレットは API を使用して取得でき `Configuration` ます。

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>シークレットを POCO にマップする

オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

前のシークレットを POCO にマップするには、 `Configuration` API の [オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph) 機能を使用します。 次のコードは、カスタム POCO にバインド `MovieSettings` し、 `ServiceApiKey` プロパティ値にアクセスします。

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`と `Movies:ServiceApiKey` シークレットは、のそれぞれのプロパティにマップされ `MovieSettings` ます。

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>シークレットを使用した文字列の置換

プレーンテキストでのパスワードの保存は安全ではありません。 たとえば、に格納されているデータベース接続文字列には、 *appsettings.json* 指定されたユーザーのパスワードが含まれる場合があります。

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

より安全な方法は、パスワードをシークレットとして保存することです。 次に例を示します。

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`の接続文字列からキーと値のペアを削除 *appsettings.json* します。 次に例を示します。

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

シークレットの値は、オブジェクトのプロパティに設定して、接続文字列を完成させることができ <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ます。

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>シークレットを一覧表示する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets list
```

次のような出力が表示されます。

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

前の例では、キー名のコロンは *secrets.js* 内のオブジェクト階層を表しています。

## <a name="remove-a-single-secret"></a>1つのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

ファイルのアプリの *secrets.js* は、キーに関連付けられているキーと値のペアを削除するように変更されました `MoviesConnectionString` 。

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` 次のメッセージが表示されます。

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>すべてのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets clear
```

アプリのすべてのユーザーシークレットは、ファイルの *secrets.js* から削除されています。

```json
{}
```

`dotnet user-secrets list`を実行すると、次のメッセージが表示されます。

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>その他の資料

* IIS からシークレットマネージャーにアクセスする方法については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328) を参照してください。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このドキュメントでは、開発用コンピューターでの ASP.NET Core アプリの開発時に機密データを格納および取得する方法について説明します。 パスワードやその他の機密データをソースコードに格納しないでください。 運用環境のシークレットは、開発またはテストには使用しないでください。 シークレットはアプリと一緒にデプロイしないでください。 代わりに、環境変数、Azure Key Vault などの制御された方法を使用して、運用環境でシークレットを使用できるようにする必要があります。 [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを格納し、保護することができます。

## <a name="environment-variables"></a>環境変数

環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。 環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。

**個々のユーザーアカウント** のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。 既定のデータベース接続文字列は、プロジェクトのファイル内の *appsettings.json* キーと共に含まれ `DefaultConnection` ます。 既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。 アプリの展開時に、 `DefaultConnection` キー値は環境変数の値でオーバーライドできます。 環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。

> [!WARNING]
> 環境変数は通常、暗号化されていないプレーンテキストで格納されます。 コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。 ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>シークレットマネージャー

Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。 このコンテキストでは、機密データの一部がアプリシークレットになります。 アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。 アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。 アプリシークレットがソース管理にチェックインされていません。

> [!WARNING]
> シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。 開発目的でのみ使用されます。 キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。

## <a name="how-the-secret-manager-tool-works"></a>Secret Manager ツールの動作

Secret Manager ツールは、値の格納場所や方法などの実装の詳細を抽象化します。 このツールは、実装の詳細を把握していなくても使用できます。 値は、ローカルコンピューター上のシステムで保護されたユーザープロファイルフォルダー内の JSON 構成ファイルに格納されます。

# <a name="windows"></a>[Windows](#tab/windows)

ファイルシステムのパス:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

ファイルシステムのパス:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

上記のファイルパスで、を `<user_secrets_id>` `UserSecretsId` *.csproj* ファイルで指定された値に置き換えます。

シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。 これらの実装の詳細は変更される可能性があります。 たとえば、シークレット値は暗号化されませんが、将来の可能性があります。

## <a name="enable-secret-storage"></a>シークレットストレージを有効にする

Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。

ユーザーシークレットを使用するには、 `UserSecretsId` `PropertyGroup` *.csproj* ファイルの内に要素を定義します。 の内部テキスト `UserSecretsId` は任意ですが、プロジェクトに固有です。 通常、開発者はの GUID を生成 `UserSecretsId` します。

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから [ **ユーザーシークレットの管理** ] を選択します。 このジェスチャは、 `UserSecretsId` GUID が設定された要素を .csproj ファイルに追加し *ます* 。

## <a name="set-a-secret"></a>シークレットを設定する

キーとその値で構成されるアプリシークレットを定義します。 シークレットは、プロジェクトの値に関連付けられ `UserSecretsId` ます。 たとえば、 *.csproj* ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

前の例では、コロンは、がプロパティを持つオブジェクトリテラルであることを示して `Movies` い `ServiceApiKey` ます。

シークレットマネージャーツールは、他のディレクトリからも使用できます。 `--project` *.Csproj* ファイルが存在するファイルシステムパスを指定するには、オプションを使用します。 次に例を示します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio での JSON 構造のフラット化

Visual Studio の [ **ユーザーシークレットの管理** ] ジェスチャでは、テキストエディターでファイル *のsecrets.js* が開きます。 *secrets.js* の内容を、格納されるキーと値のペアに置き換えます。 次に例を示します。

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 構造体は、またはを使用した変更後にフラット化され `dotnet user-secrets remove` `dotnet user-secrets set` ます。 たとえば、を実行 `dotnet user-secrets remove "Movies:ConnectionString"` すると、 `Movies` オブジェクトリテラルが折りたたまれます。 変更後のファイルは次のようになります。

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>複数のシークレットを設定する

シークレットのバッチは、JSON をコマンドにパイプすることによって設定でき `set` ます。 次の例では、ファイルの内容の *input.js* がコマンドにパイプされてい `set` ます。

# <a name="windows"></a>[Windows](#tab/windows)

コマンドシェルを開き、次のコマンドを実行します。

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

コマンドシェルを開き、次のコマンドを実行します。

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>シークレットへのアクセス

[ASP.NET Core 構成 API](xref:fundamentals/configuration/index)は、シークレットマネージャーのシークレットへのアクセスを提供します。

プロジェクトが .NET Framework 対象である場合は、Microsoft.Extensions.Configuration をインストールし [ ます。UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージ。

ASP.NET Core 2.0 以降では、プロジェクトがを呼び出して、事前に構成され <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> た既定値でホストの新しいインスタンスを初期化すると、開発モードでユーザーシークレットの構成ソースが自動的に追加されます。 `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>がの場合、 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> を呼び出し <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> ます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

`CreateDefaultBuilder`が呼び出されない場合は、コンストラクターでを呼び出すことによって、ユーザーシークレット構成ソースを明示的に追加し <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` ます。 `AddUserSecrets`次の例に示すように、アプリが開発環境で実行されている場合にのみ、を呼び出します。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

ユーザーシークレットは API を使用して取得でき `Configuration` ます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>シークレットを POCO にマップする

オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

前のシークレットを POCO にマップするには、 `Configuration` API の [オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph) 機能を使用します。 次のコードは、カスタム POCO にバインド `MovieSettings` し、 `ServiceApiKey` プロパティ値にアクセスします。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`と `Movies:ServiceApiKey` シークレットは、のそれぞれのプロパティにマップされ `MovieSettings` ます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>シークレットを使用した文字列の置換

プレーンテキストでのパスワードの保存は安全ではありません。 たとえば、に格納されているデータベース接続文字列には、 *appsettings.json* 指定されたユーザーのパスワードが含まれる場合があります。

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

より安全な方法は、パスワードをシークレットとして保存することです。 次に例を示します。

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`の接続文字列からキーと値のペアを削除 *appsettings.json* します。 次に例を示します。

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

シークレットの値は、オブジェクトのプロパティに設定して、接続文字列を完成させることができ <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>シークレットを一覧表示する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets list
```

次のような出力が表示されます。

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

前の例では、キー名のコロンは *secrets.js* 内のオブジェクト階層を表しています。

## <a name="remove-a-single-secret"></a>1つのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

ファイルのアプリの *secrets.js* は、キーに関連付けられているキーと値のペアを削除するように変更されました `MoviesConnectionString` 。

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`を実行すると、次のメッセージが表示されます。

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>すべてのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets clear
```

アプリのすべてのユーザーシークレットは、ファイルの *secrets.js* から削除されています。

```json
{}
```

`dotnet user-secrets list`を実行すると、次のメッセージが表示されます。

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>その他の資料

* IIS からシークレットマネージャーにアクセスする方法については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328) を参照してください。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
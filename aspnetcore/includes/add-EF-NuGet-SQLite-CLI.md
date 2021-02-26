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
ms.openlocfilehash: 1a9e98a84093f89f3859454f482dfe59c8504d9b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552200"
---
次の .NET Core CLI コマンドを実行します。

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

上記のコマンドにより次が追加されます。

* [aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。
* .NET Core CLI の Entity Framework Core ツール。
* EF Core SQLite プロバイダー。これにより、EF Core パッケージが依存関係としてインストールされます。
* スキャフォールディングに必要なパッケージ: `Microsoft.VisualStudio.Web.CodeGeneration.Design` と `Microsoft.EntityFrameworkCore.SqlServer`。

アプリが環境ごとにデータベースコンテキストを構成できるようにする複数の環境構成に関するガイダンスについては、「<xref:fundamentals/environments#environment-based-startup-class-and-methods>」を参照してください。

[!INCLUDE[](~/includes/scaffoldTFM.md)]
---
title: ASP.NET Core Blazor のログ
author: guardrex
description: ログ レベルの構成や Razor コンポーネントからログ メッセージを書き込む方法など、Blazor アプリでのログ記録について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 10c96bd2d0cc64f3bd035e7079b0996eb5768595
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97666834"
---
# <a name="aspnet-core-no-locblazor-logging"></a>ASP.NET Core Blazor のログ

::: zone pivot="webassembly"

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> プロパティを使用して、Blazor WebAssembly アプリでカスタム ログを構成します。

`Program.cs` に <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> の名前空間を追加します。

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

`Program.cs` の `Program.Main` で、<xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> を使用して最小ログ記録レベルを設定し、カスタム ログ プロバイダーを追加します。

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging` プロパティは型 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> であるため、<xref:Microsoft.Extensions.Logging.ILoggingBuilder> で使用できるすべての拡張メソッドを `Logging` で使用することもできます。

ログの構成は、アプリの設定ファイルから読み込むことができます。 詳細については、「<xref:blazor/fundamentals/configuration#logging-configuration>」を参照してください。

## <a name="no-locsignalr-net-client-logging"></a>SignalR .NET クライアント ログ

<xref:Microsoft.Extensions.Logging.ILoggerProvider> を挿入して、<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> に渡されたログ プロバイダーに `WebAssemblyConsoleLogger` を追加します。 従来の <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> とは異なり、`WebAssemblyConsoleLogger` はブラウザー固有のログ API (例: `console.log`) のラッパーです。 `WebAssemblyConsoleLogger` を使用すると、ブラウザー コンテキスト内の Mono 内でログ記録できるようになります。

> [!NOTE]
> `WebAssemblyConsoleLogger` は [internal](/dotnet/csharp/language-reference/keywords/internal) であり、開発者コードで直接使用することはできません。

<xref:Microsoft.Extensions.Logging?displayProperty=fullName> の名前空間を追加し、コンポーネントに <xref:Microsoft.Extensions.Logging.ILoggerProvider> を挿入します。

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

コンポーネントの [`OnInitializedAsync` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)で、<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> を使用します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

Blazor Server に関連する一般的な ASP.NET Core のログのガイダンスについては、「<xref:fundamentals/logging/index>」を参照してください。

::: zone-end

## <a name="log-in-no-locrazor-components"></a>Razor コンポーネントでのログ

ロガーは、アプリのスタートアップ構成を尊重します。

<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> や <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> など、API の [IntelliSense](/visualstudio/ide/using-intellisense) 入力候補をサポートするには、<xref:Microsoft.Extensions.Logging> の `using` ディレクティブが必要です。

次の例は、コンポーネントで <xref:Microsoft.Extensions.Logging.ILogger> を使用したログを示しています。

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

次の例は、コンポーネントで <xref:Microsoft.Extensions.Logging.ILoggerFactory> を使用したログを示しています。

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/logging/index>

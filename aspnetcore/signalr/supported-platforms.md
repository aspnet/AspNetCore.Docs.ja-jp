---
title: ASP.NET Core SignalR でサポートされているプラットフォーム
author: bradygaster
description: ASP.NET Core SignalR でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
no-loc:
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
uid: signalr/supported-platforms
ms.openlocfilehash: 761edbbe7bab28d2340207a0adea0718b37c7ec1
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690333"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>ASP.NET Core SignalR でサポートされているプラットフォーム

## <a name="server-system-requirements"></a>サーバー システムの要件

SignalR ASP.NET Core は、ASP.NET Core がサポートするすべてのサーバープラットフォームをサポートします。

## <a name="javascript-client"></a>JavaScript クライアント

[JavaScript クライアント](xref:signalr/javascript-client)は、nodejs 8 以降のバージョンと次のブラウザーで実行されます。

| ブラウザー                          | バージョン         |
| -------------------------------- | --------------- |
| Apple Safari (iOS を含む)      | [現在]&dagger; |
| Google Chrome (Android を含む) | [現在]&dagger; |
| Microsoft Edge                   | [現在]&dagger; |
| Mozilla Firefox                  | [現在]&dagger; |

&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。

## <a name="net-client"></a>.NET クライアント

[.Net クライアント](xref:signalr/dotnet-client)は、ASP.NET Core によってサポートされる任意のプラットフォームで実行されます。 たとえば、xamarin[開発者はを SignalR 使用](https://github.com/aspnet/Announcements/issues/305)して、xamarin 8.4.0.1 以降と ios アプリを使用して、xamarin 11.14.0.4 以降を使用して android アプリをビルドできます。

サーバーで IIS が実行されている場合、Websocket トランスポートでは Windows Server 2012 以降に IIS 8.0 以降が必要です。 その他のトランスポートはすべてのプラットフォームでサポートされています。

## <a name="java-client"></a>Java クライアント

[Java クライアント](xref:signalr/java-client)は、java 8 以降のバージョンをサポートしています。

## <a name="unsupported-clients"></a>サポートされていないクライアント

次のクライアントは使用できますが、試験的または非公式です。 現時点ではサポートされておらず、そうでない場合もあります。

* [C++ クライアント](https://github.com/aspnet/SignalR-Client-Cpp)

* [Swift クライアント](https://github.com/moozzyk/SignalR-Client-Swift)

---
title: ASP.NET Core Security の概要
author: rick-anderson
description: ASP.NET Core での認証、承認、およびセキュリティの基本を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051630"
---
# <a name="overview-of-aspnet-core-security"></a>ASP.NET Core Security の概要

ASP.NET Core を使用することで、開発者はアプリのセキュリティを簡単に構成して管理することができます。 ASP.NET Core には、認証、認可、データ保護、HTTPS の適用、アプリ シークレット、XSRF/CSRF の防止、および CORS を管理するための機能が含まれています。 これらのセキュリティ機能を使用すれば、堅牢かつセキュアな ASP.NET Core アプリを構築できます。

## <a name="aspnet-core-security-features"></a>ASP.NET Core セキュリティ機能

ASP.NET Core では、組み込みの ID プロバイダーを含むアプリをセキュリティで保護するための多くのツールとライブラリが提供されますが、Facebook、Twitter、LinkedIn などのサードパーティの ID サービスを使用することもできます。 ASP.NET Core では、コードで公開せずに機密情報を格納して使用する方法である、アプリ シークレットを簡単に管理できます。

## <a name="authentication-vs-authorization"></a>認証と承認

認証とは、ユーザーが提供した資格情報をオペレーティング システム、データベース、アプリまたはリソースに格納されているものと比較するプロセスのことです。 資格情報が一致した場合、ユーザーは正常に認証され、承認プロセス中に、承認されたアクションを実行できます。 承認とは、ユーザーに許可する実行内容を決定するプロセスのことです。

また、認証はサーバー、データベース、アプリまたはリソースなどの領域に入る方法と見なすことができます。一方、承認はユーザーがその領域 (サーバー、データベース、またはアプリ) 内のいずかのオブジェクトに対して実行できるアクションです。

## <a name="common-vulnerabilities-in-software"></a>ソフトウェアの一般的な脆弱性

ASP.NET Core および EF には、アプリをセキュリティで保護し、セキュリティ違反を防止するのに役立つ機能が含まれています。 以下にリストされているリンクから、Web アプリの最も一般的なセキュリティの脆弱性を回避するための手法の詳細を示すドキュメントにアクセスできます。

* [クロスサイト スクリプティング (XSS) 攻撃](xref:security/cross-site-scripting)
* [SQL インジェクション攻撃](/ef/core/querying/raw-sql)
* [クロスサイト リクエスト フォージェリ (XSRF/CSRF) 攻撃](xref:security/anti-request-forgery)
* [オープン リダイレクト攻撃](xref:security/preventing-open-redirects)

この他にも知っておく必要がある脆弱性はあります。 詳細については、目次の **セキュリティと Identity** のセクションにある他の記事を参照してください。

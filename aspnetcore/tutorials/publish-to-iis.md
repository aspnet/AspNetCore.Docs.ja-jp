---
title: IIS に ASP.NET Core アプリを発行する
author: rick-anderson
description: IIS サーバーで ASP.NET Core アプリをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: b3c714ea8e741430df1f70b2df258f1e8f1c7ad5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060509"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>IIS に ASP.NET Core アプリを発行する

このチュートリアルでは、IIS サーバーで ASP.NET Core アプリをホストする方法を示します。

このチュートリアルでは、次の件について取り上げます。

> [!div class="checklist"]
> * .NET Core ホスティング バンドルを Windows Server にインストールします。
> * IIS サイトを IIS マネージャーで作成します。
> * ASP.NET Core アプリを展開します。

## <a name="prerequisites"></a>必須コンポーネント

* 開発用のコンピューターにインストールされている [.NET Core SDK](/dotnet/core/sdk)。
* **Web Server (IIS)** サーバー ロールで構成された Windows Server。 IIS で Web サイトをホストするようにサーバーが構成されていない場合、<xref:host-and-deploy/iis/index#iis-configuration> 記事の「 *IIS 構成* 」セクションの指示に従い、その後、このチュートリアルに戻ってください。

> [!WARNING]
> **IIS 構成と Web サイトのセキュリティには、このチュートリアルでは説明されていない概念が含まれています。** IIS で本稼働アプリをホストする前に、[Microsoft IIS ドキュメント](https://www.iis.net/)と [IIS でホストする方法に関する ASP.NET Core 記事](xref:host-and-deploy/iis/index)を参照してください。
>
> このチュートリアルで取り上げられていない IIS ホスティングの重要なシナリオ:
>
> * [ASP.NET Core データ保護のためのレジストリ ハイブの作成](xref:host-and-deploy/iis/index#data-protection)
> * [アプリ プールのアクセス制御リスト (ACL) の構成](xref:host-and-deploy/iis/index#application-pool-identity)
> * IIS 配置の概念を集中的に取り上げるために、このチュートリアルでは、IIS で HTTPS セキュリティを構成せずにアプリを展開します。 HTTPS プロトコルに対して有効にするアプリをホストする方法については、この記事の「[その他の技術情報](#additional-resources)」セクションのセキュリティ トピックをご覧ください。 ASP.NET Core アプリのホスティングに関するその他の情報は <xref:host-and-deploy/iis/index> 記事にあります。

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core ホスティング バンドルのインストール

*.NET Core ホスティング バンドル* を IIS サーバーにインストールします。 このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。 このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。

次のリンクを使用してインストーラーをダウンロードします。

[現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. IIS サーバーでインストーラーを実行します。

1. サーバーを再起動するか、コマンド シェルで `net stop was /y` に続けて `net start w3svc` を実行します。

## <a name="create-the-iis-site"></a>IIS サイトを作成する

1. IIS サーバーで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。 以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。 アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。

1. IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。 **[サイト]** フォルダーを右クリックします。 コンテキスト メニューで **[Web サイトの追加]** を選択します。

1. **[サイト名]** を指定し、 **[物理パス]** には作成したアプリの配置フォルダーを設定します。 **[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。

   > [!WARNING]
   > 最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。 最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。 これは、強力と脆弱の両方のワイルドカードに適用されます。 ワイルドカードではなく、明示的なホスト名を使用します。 全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。 詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。

1. プロセス モデル ID に適切なアクセス許可があることを確認します。

   アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を `ApplicationPoolIdentity` から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。 たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>ASP.NET Core Razor Pages アプリを作成する

<xref:getting-started> チュートリアルに従い、Razor Pages アプリを作成します。

## <a name="publish-and-deploy-the-app"></a>アプリを発行および配置する

" *アプリを発行する* " とは、サーバーでホストできるコンパイル済みのアプリを生成するということです。 " *アプリを展開する* " とは、発行済みのアプリをホスティング システムに移動するということです。 発行手順は [.NET Core SDK](/dotnet/core/sdk) で処理されます。一方で、展開手順はさまざまな手法で処理できます。 このチュートリアルでは、" *フォルダー* " 展開手法を採用しています。
 
* アプリはフォルダーに発行されます。
* フォルダーの内容が IIS サイトのフォルダーに移動されます (IIS マネージャーのサイトの **物理パス** )。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **[ソリューション エクスプローラー]** でプロジェクトを右クリックし、 **[発行]** を選択します。
1. **[発行先を選択]** ダイアログで、 **[フォルダー]** 発行オプションを選択します。
1. **フォルダーまたはファイル共有** パスを選択します。
   * ネットワーク共有として開発用のコンピューターで利用できる IIS サイトのフォルダーを作成した場合、共有へのパスを指定します。 現在のユーザーに、共有に発行するための書き込みアクセスを与える必要があります。
   * IIS サーバー上の IIS サイト フォルダーに直接展開できない場合、リムーバブル メディア上のフォルダーに発行し、IIS マネージャーでサイトの **物理パス** である、サーバー上の IIS サイト フォルダーに発行済みのアプリを物理的に移動します。 IIS マネージャーでサイトの **物理パス** である、サーバー上の IIS サイト フォルダーに `bin/Release/{TARGET FRAMEWORK}/publish` フォルダーの内容を移動します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. コマンド シェルで、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使用してリリース構成でアプリを発行します。

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. IIS マネージャーでサイトの **物理パス** である、サーバー上の IIS サイト フォルダーに `bin/Release/{TARGET FRAMEWORK}/publish` フォルダーの内容を移動します。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. **[ソリューション]** でプロジェクトを右クリックし、 **[発行]**  >  **[フォルダーに発行]** の順に選択します。
1. **[フォルダーを選択してください]** パスを設定します。
   * ネットワーク共有として開発用のコンピューターで利用できる IIS サイトのフォルダーを作成した場合、共有へのパスを指定します。 現在のユーザーに、共有に発行するための書き込みアクセスを与える必要があります。
   * IIS サーバー上の IIS サイト フォルダーに直接展開できない場合、リムーバブル メディア上のフォルダーに発行し、IIS マネージャーでサイトの **物理パス** である、サーバー上の IIS サイト フォルダーに発行済みのアプリを物理的に移動します。 IIS マネージャーでサイトの **物理パス** である、サーバー上の IIS サイト フォルダーに `bin/Release/{TARGET FRAMEWORK}/publish` フォルダーの内容を移動します。

---

## <a name="browse-the-website"></a>Web サイトを閲覧する

アプリには、最初の要求の受信後、ブラウザーでアクセスできます。 サイトの IIS マネージャーで設定したエンドポイント バインドでアプリへの要求を行います。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * .NET Core ホスティング バンドルを Windows Server にインストールします。
> * IIS サイトを IIS マネージャーで作成します。
> * ASP.NET Core アプリを展開します。

IIS で ASP.NET Core アプリをホストする方法の詳細については、IIS の概要に関する記事を参照してください。

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>その他の技術情報

### <a name="articles-in-the-aspnet-core-documentation-set"></a>ASP.NET Core ドキュメント セットの記事

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>ASP.NET Core アプリの展開に関する記事

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Visual Studio for Mac を使用してフォルダーに Web アプリを発行する](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>IIS HTTPS 構成に関する記事

* [IIS マネージャーで SSL を構成する](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [IIS で SSL を設定する方法](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>IIS と Windows Server に関する記事

* [Microsoft IIS 公式サイト](https://www.iis.net/)
* [Windows Server テクニカル コンテンツ ライブラリ](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>IIS 管理者用の展開リソース

* [IIS ドキュメント ](/iis)
* [IIS での IIS マネージャーの概要](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core アプリケーションの展開](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>


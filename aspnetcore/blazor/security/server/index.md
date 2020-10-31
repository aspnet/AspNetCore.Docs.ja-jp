---
title: ASP.NET Core Blazor Server アプリをセキュリティで保護する
author: guardrex
description: Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055478"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a>ASP.NET Core Blazor Server アプリをセキュリティで保護する

作成者: [Luke Latham](https://github.com/guardrex)

Blazor Server アプリは、ASP.NET Core アプリと同じ方法でセキュリティが構成されます。 詳細については、<xref:security/index> の記事を参照してください。 この概要に含まれるトピックは、Blazor Server に特に当てはまります。

## <a name="no-locblazor-server-project-template"></a>Blazor Server プロジェクト テンプレート

Blazor Server プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio のガイダンスに従ってください。

**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor Server アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。

ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。

* **認証なし**
* **個人のユーザー アカウント** : ユーザーアカウントは次のように格納できます。
  * ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するアプリ内。
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c)。
* **職場または学校アカウント**
* **Windows 認証**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio Code のガイダンスに従ってください。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。

| 認証メカニズム | 説明 |
| ------------------------ | ----------- |
| `None` (既定値)         | 認証なし |
| `Individual`             | ASP.NET Core Identity を使用してアプリに格納されているユーザー |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー |
| `SingleOrg`              | 単一のテナントに対する組織認証 |
| `MultiOrg`               | 複数のテナントに対する組織認証 |
| `Windows`                | Windows 認証 |

`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。

* プロジェクトのフォルダーを作成します。
* プロジェクトに名前を付けます。

詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. <xref:blazor/tooling> に記載されている Visual Studio for Mac のガイダンスに従ってください。

1. **[新しい Blazor Server アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。

1. ASP.NET Core Identity を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

コマンド シェルで次のコマンドを使用し、認証メカニズムを使って新しい Blazor Server プロジェクトを作成します。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。

| 認証メカニズム | 説明 |
| ------------------------ | ----------- |
| `None` (既定値)         | 認証なし |
| `Individual`             | ASP.NET Core Identity を使用してアプリに格納されているユーザー |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー |
| `SingleOrg`              | 単一のテナントに対する組織認証 |
| `MultiOrg`               | 複数のテナントに対する組織認証 |
| `Windows`                | Windows 認証 |

`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。

* プロジェクトのフォルダーを作成します。
* プロジェクトに名前を付けます。

詳細情報:

* .NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。
* コマンド シェルで Blazor Server テンプレート (`blazorserver`) のヘルプ コマンドを実行します。

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a>スキャフォールディング Identity

Identity を Blazor Server プロジェクトにスキャフォールディングします。

* [既存の承認がありません](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。
* [承認があります](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。

## <a name="additional-resources"></a>その他の資料

* [クイック スタート:Microsoft でのサインインを ASP.NET Core Web アプリに追加する](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [クイック スタート:Microsoft ID プラットフォームを使用して ASP.NET Core Web API を保護する](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)

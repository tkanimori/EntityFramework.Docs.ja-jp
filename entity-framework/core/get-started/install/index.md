---
title: "EF Core のインストール"
author: divega
ms.author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
ms.technology: entity-framework-core
uid: core/get-started/install/index
ms.openlocfilehash: 31b96ebd0ae282b88be98988eff6263084dc5dd5
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="installing-ef-core"></a>EF Core のインストール

## <a name="prerequisites"></a>必須コンポーネント

.NET Core 2.0 アプリケーション (.NET Core を対象とする ASP.NET Core 2.0 アプリケーションを含む) を開発するには、お使いのプラットフォームに適したバージョンの [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core) をダウンロードし、インストールする必要があります。 **Visual Studio 2017 バージョン 15.3 をインストールしている場合でも必要です。**

.NET Core 2.0 以外の .NET プラットフォーム (たとえば、.NET Framework 4.6.1 以降) で EF Core 2.0 やその他の .NET Standard 2.0 ライブラリを使用するには、.NET Standard 2.0 とその互換フレームワークを認識するバージョンの NuGet が必要になります。 次の方法で入手できます。

* Visual Studio 2017 バージョン 15.3 をインストールする
* Visual Studio 2015 を使用している場合、[NuGet クライアント バージョン 3.6.0 をダウンロードするか、このバージョンにアップグレードします](https://www.nuget.org/downloads)。

以前のバージョンの Visual Studio とその対象となる .NET Framework で作成されたプロジェクトの場合、.NET Standard 2.0 ライブラリ互換にするには追加の変更が必要になることがあります。

* プロジェクト ファイルを編集し、初期プロパティ グループに次のエントリが表示されるようにします。
  ``` xml
  <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
  ```

* テスト プロジェクトの場合、次のエントリも必要です。
  ``` xml
  <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
  ```

## <a name="getting-the-bits"></a>ビットを取得する
EF Core ランタイム ライブラリをアプリケーションに追加するための推奨方法は、NuGet から EF Core データベース プロバイダーをインストールすることです。

ランタイム ライブラリに加え、デザイン時にプロジェクトでいくつかの EF Core 関連タスクの実行を簡単にするツールをインストールできます。関連タスクには、移行の作成と適用、既存データベースに基づくモデルの作成などがあります。

> [!TIP]  
> サードパーティ データベース プロバイダーを使用するアプリケーションを更新する場合、使用するバージョンの EF Core との間で互換性があるプロバイダーの更新がないか、常に確認してください。 例: 前のバージョンのデータベース プロバイダーは EF Core ランタイムのバージョン 2.0 との間で互換性がありません。  

> [!TIP]  
> ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。 前バージョンの ASP.NET Core を対象とするアプリケーションで EF Core 2.0 を使用するには、ASP.NET Core 2.0 にアップグレードする必要があります。

<a name="cli"></a>
### <a name="cross-platform-development-using-the-net-core-command-line-interface-cli"></a>.NET Core コマンド ライン インターフェイス (CLI) を利用したクロスプラットフォーム開発

[.NET Core](https://www.microsoft.com/net/download/core) を対象とするアプリケーションを開発するには、お気に入りのテキスト エディターや、Visual Studio、Visual Studio for Mac、Visual Studio Code などの統合開発環境 (IDE) と組み合わせて [`dotnet` CLI コマンド](https://docs.microsoft.com/dotnet/core/tools/)を使用できます。

> [!IMPORTANT]  
> .NET Core を対象とするアプリケーションは特定のバージョンの Visual Studio を必要とします。たとえば、.NET Core 1.x 開発には Visual Studio 2017 が必要であり、.NET Core 2.0 開発には Visual Studio 2017 バージョン 15.3 が必要です。

クロスプラットフォーム .NET Core アプリケーションで SQL Server プロバイダーをインストールまたはアップグレードするには、アプリケーションのディレクトリに切り替え、コマンド ラインで次を実行します。

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

`-v` 修飾子を利用し、`dotnet add package` コマンドで特定のバージョンのインストールを指定できます。 例: EF Core 2.0 パッケージをインストールするには、コマンドに `-v 2.0.0` を追加します。

EF Core には、[`dotnet` CLI の追加コマンド](../../miscellaneous/cli/dotnet.md)のセットが含まれており、その最初が `dotnet ef` です。 `dotnet ef` CLI コマンドを使用するには、アプリケーションの `.csproj` ファイルに次のエントリを含める必要があります。

``` xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
</ItemGroup>
```

EF Core 向け .NET Core CLI ツールには、Microsoft.EntityFrameworkCore.Design と呼ばれている別個のパッケージも必要です。 次を利用し、プロジェクトに追加できます。

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]  
> ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。

<a name="visual-studio"></a>
### <a name="visual-studio-development"></a>Visual Studio 開発

.NET Core、.NET Framework、または EF Core でサポートされているその他のプラットフォームを対象とするさまざまなアプリケーションを Visual Studio で開発できます。

Visual Studio からアプリケーションに EF Core データベース プロバイダーをインストールする方法が 2 つあります。

#### <a name="using-nugets-package-manager-user-interfacehttpsdocsmicrosoftcomnugettoolspackage-manager-ui"></a>NuGet の[パッケージ マネージャー ユーザー インターフェイス](https://docs.microsoft.com/nuget/tools/package-manager-ui)を使用する

* メニューで **[プロジェクト]、[NuGet パッケージの管理]** の順に選択します。

* **[参照]** または **[更新]** タブをクリックします。

* `Microsoft.EntityFrameworkCore.SqlServer` パッケージと希望のバージョンを選択し、確定します。

#### <a name="using-nugets-package-manager-console-pmchttpsdocsmicrosoftcomnugettoolspackage-manager-console"></a>NuGet の[パッケージ マネージャー コンソール (PMC)](https://docs.microsoft.com/nuget/tools/package-manager-console) を使用する

* メニューで **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。

* PMC で次のコマンドを入力し、実行します。

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* `Update-Package` コマンドを代わりに使用し、既にインストールされているパッケージを新しいバージョンに更新できます。

* 特定のバージョンを指定するには、`-Version` 修飾子を使用できます。たとえば、EF Core 2.0 パッケージをインストールするには、コマンドに `-Version 2.0.0` を追加します。

#### <a name="tools"></a>ツール

Visual Studio で [PMC 内で実行される EF Core コマンド](../../miscellaneous/cli/powershell.md)の PowerShell バージョンもあります。機能は `dotnet ef` コマンドと似ています。 これらを使用するには、パッケージ マネージャー UI または PMC を利用して `Microsoft.EntityFrameworkCore.Tools` パッケージをインストールします。

> [!IMPORTANT]  
> ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。

> [!TIP]  
> Visual Studio では PMC から `dotnet ef` コマンドを使用することができますが、PowerShell バージョンの使用がはるかに便利です。
> * PMC で現在選択されているプロジェクトと自動的に連動します。ディレクトリを手動で切り替える必要がありません。  
> * コマンドの完了後、Visual Studio で、コマンドによって生成されたファイルが自動的に開きます。

> [!IMPORTANT]  
> **EF Core 2.0 の非推奨のパッケージ:** 既存のアプリケーションを EF Core 2.0 にアップグレードするとき、場合によっては、古い EF Core パッケージの一部の参照を手動で削除する必要があります。 具体的には、`Microsoft.EntityFrameworkCore.SqlServer.Design` など、データベース プロバイダーのデザイン時パッケージは EF Core 2.0 で不要になりましたが (サポートがなくなりましたが)、他のパッケージのアップグレード時に自動的に削除されることはありません。

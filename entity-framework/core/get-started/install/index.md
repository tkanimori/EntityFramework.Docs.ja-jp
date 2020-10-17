---
title: Entiy Framework Core のインストール - EF Core
description: Entity Framework Core のインストール手順
author: bricelam
ms.date: 08/06/2017
uid: core/get-started/install/index
ms.openlocfilehash: 3aae80998768d8d1bfbad7a872abc3648b792ad5
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062101"
---
# <a name="installing-entity-framework-core"></a>Entiy Framework Core のインストール

## <a name="prerequisites"></a>前提条件

* EF Core は [.NET Standard 2.0](/dotnet/standard/net-standard) ライブラリの 1 つです。 したがって、EF Core が動作するには .NET Standard 2.0 をサポートする .NET 実装が必要です。 EF Core は他の .NET Standard 2.0 ライブラリから参照することもできます。

* たとえば、.NET Core をターゲットとするアプリを EF Core を使用して開発することができます。 .NET Core アプリを構築するには、[.NET Core SDK](https://dotnet.microsoft.com/download) が必要です。 必要に応じて、[Visual Studio](https://visualstudio.microsoft.com/vs)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac)、[Visual Studio Code](https://code.visualstudio.com) などの開発環境を使用することもできます。 詳しくは、「[.NET Core の概要](/dotnet/core/get-started)」をご覧ください。

* EF Core を使用すると、Windows 上で Visual Studio を使用してアプリケーションを開発できます。 [Visual Studio](https://visualstudio.microsoft.com/vs) の最新バージョンをお勧めします。

* EF Core を他の .NET 実装 (たとえば [Xamarin](https://dotnet.microsoft.com/apps/xamarin) や .NET Native) 上で実行することができます。 ただし、実際にはそのような実装には実行時の制約があるため、開発したアプリでの EF Core の動作に影響を与える可能性があります。 詳しくは、「[EF Core でサポートされている .NET 実装](xref:core/platforms/index)」をご覧ください。

* データベース プロバイダーによっては、特定のデータベース エンジン バージョン、.NET 実装、またはオペレーティング システムが要求される場合があります。 開発するアプリケーションに適した環境をサポートする [EF Core データベース プロバイダー](xref:core/providers/index)があることを確認してください。

## <a name="get-the-entity-framework-core-runtime"></a>Entity Framework Core のランタイムを入手する

EF Core をアプリケーションに追加するには、使用するデータベース プロバイダーに対応した NuGet パッケージをインストールします。

ASP.NET Core アプリケーションを構築する場合は、インメモリや SQL Server のプロバイダーをインストールする必要はありません。 これらのプロバイダーは、現在のバージョンの ASP.NET Core に、EF Core ランタイムとともに含まれています。

NuGet パッケージをインストールまたは更新するには、.NET Core コマンド ライン インターフェイス (CLI)、Visual Studio パッケージ マネージャー ダイアログ、または Visual Studio パッケージ マネージャー コンソールを使用できます。

### <a name="net-core-cli"></a>.NET Core CLI

* 次の .NET Core CLI コマンドをオペレーティング システムのコマンド ラインで使用して EF Core SQL Server プロバイダーのインストールまたは更新を行います。

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* `-v` 修飾子を利用し、`dotnet add package` コマンドで特定のバージョンを指定できます。 たとえば、EF Core 2.2.0 のパッケージをインストールするには、コマンドの末尾に `-v 2.2.0` を追加します。

詳細については、「[.NET コマンド ライン インターフェイス (CLI) ツール](/dotnet/core/tools/)」をご覧ください。

### <a name="visual-studio-nuget-package-manager-dialog"></a>Visual Studio の NuGet パッケージ マネージャー ダイアログ

* Visual Studio のメニューから **[プロジェクト]、[NuGet パッケージの管理]** の順に選択します。

* **[参照]** または **[更新]** タブをクリックします。

* SQL Server プロバイダーをインストールまたは更新するには、`Microsoft.EntityFrameworkCore.SqlServer` パッケージを選択して確定します。

詳細については、「[NuGet パッケージ マネージャー UI](/nuget/tools/package-manager-ui)」を参照してください。

### <a name="visual-studio-nuget-package-manager-console"></a>Visual Studio の NuGet パッケージ マネージャー コンソール

* Visual Studio のメニューから **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。

* SQL Server プロバイダーをインストールするには、パッケージ マネージャー コンソールで、次のコマンドを実行します。

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* プロバイダーを更新するには、`Update-Package` コマンドを使用します。

* 特定のバージョンを指定するには、`-Version` 修飾子を使用します。 たとえば、EF Core 2.2.0 のパッケージをインストールするには、コマンドの末尾に `-Version 2.2.0` を追加します。

詳細については、「[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)」を参照してください。

## <a name="get-the-entity-framework-core-tools"></a>Entity Framework Core のツールを入手する

EF Core 関連のタスクをプロジェクト内で実行するためのツールをインストールできます。たとえば、データベース移行を作成して適用することや、EF Core モデルを既存のデータベースに基づいて作成することができます。

2 つのツールのセットを使用できます。

* [.NET Core コマンド ライン インターフェイス (CLI) ツール](xref:core/miscellaneous/cli/dotnet)は、Windows、Linux、macOS で使用できます。 これらのコマンドは `dotnet ef` で始まります。

* [パッケージ マネージャー コンソール (PMC) ツール](xref:core/miscellaneous/cli/powershell)は、Windows 上の Visual Studio の中で動作します。 これらのコマンドは `Add-Migration`、`Update-Database` のように動詞で始まります。

`dotnet ef` コマンドをパッケージ マネージャー コンソールで使用することもできますが、Visual Studio を使用しているときは、次のような理由からパッケージ マネージャー コンソールのツールを使用することをお勧めします。

* Visual Studio の PMC で現在選択されているプロジェクトと自動的に連動します。ディレクトリを手動で切り替える必要がありません。

* コマンドの完了後、Visual Studio で、コマンドによって生成されたファイルが自動的に開きます。

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a>.NET Core CLI ツールを入手する

.NET Core CLI ツールを使用するには、「[必須コンポーネント](#prerequisites)」で前述したとおり .NET Core SDK が必要です。

* `dotnet ef` は、グローバルまたはローカルのツールとしてインストールする必要があります。 ほとんどの開発者は、次のコマンドを使用して、`dotnet ef` をグローバル ツールとしてインストールする方を選びます。

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  `dotnet ef` もローカル ツールとして使用することができます。 ローカル ツールとして使用するには、[ツール マニフェスト ファイル](/dotnet/core/tools/global-tools#install-a-local-tool)を使用してツールの依存関係として宣言するプロジェクトの依存関係を復元します。

* ツールを更新するには、`dotnet tool update` コマンドを使用します。

* 最新の `Microsoft.EntityFrameworkCore.Design` パッケージをインストールしてください。

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

> [!IMPORTANT]
> ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。

### <a name="get-the-package-manager-console-tools"></a>パッケージ マネージャー コンソール ツールを入手する

EF Core 用のパッケージ マネージャー コンソール ツールを入手するには、`Microsoft.EntityFrameworkCore.Tools` パッケージをインストールします。 たとえば、Visual Studio で次のようにします。

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

ASP.NET Core アプリの場合は、このパッケージが自動的に組み込まれます。

## <a name="upgrading-to-the-latest-ef-core"></a>最新の EF Core にアップグレードする

* 新しいバージョンの EF Core がリリースされると、EF Core プロジェクトの一部であるプロバイダー (Microsoft.EntityFrameworkCore.SqlServer、Microsoft.EntityFrameworkCore.Sqlite、Microsoft.EntityFrameworkCore.InMemory など) の新しいバージョンもリリースされます。 新しいバージョンのプロバイダーにアップグレードするだけで、すべての機能強化を入手できます。

* EF Core は、SQL Server やインメモリのプロバイダーとともに現在のバージョンの ASP.NET Core に含まれています。 既存の ASP.NET Core アプリケーションを新しいバージョンの EF Core に合わせてアップグレードするには、必ず ASP.NET Core のバージョンをアップグレードします。

* サードパーティ データベース プロバイダーを使用するアプリケーションを更新する場合、使用するバージョンの EF Core との間で互換性があるプロバイダーの更新がないか、常に確認してください。 たとえば、バージョン 1.0 のデータベース プロバイダーは EF Core ランタイムのバージョン 2.0 との間で互換性がありません。

* EF Core 用のサードパーティ プロバイダーは通常、EF Core ランタイと並行してパッチ バージョンをリリースすることはありません。 サードパーティ プロバイダーを使用するアプリケーションを EF Core のパッチ バージョンに合わせてアップグレードするには、個々の EF Core ランタイム コンポーネント (たとえば Microsoft.EntityFrameworkCore や Microsoft.EntityFrameworkCore.Relational) への直接の参照を追加することが必要になる可能性があります。

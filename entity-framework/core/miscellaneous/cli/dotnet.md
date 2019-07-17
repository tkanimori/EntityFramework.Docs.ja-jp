---
title: EF Core ツール リファレンス (.NET CLI) - EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 05c5f89fc79556e72a7e629c147aa817fe7d1a6b
ms.sourcegitcommit: e90d6cfa3e96f10b8b5275430759a66a0c714ed1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286461"
---
# <a name="entity-framework-core-tools-reference---net-cli"></a>Entity Framework Core ツールのリファレンス - .NET CLI

Entity Framework Core のコマンド ライン インターフェイス (CLI) ツールでは、デザイン時開発タスクを実行します。 たとえば、作成[移行](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations)移行を適用し、既存のデータベースに基づくモデルのコードを生成します。 クロス プラットフォーム拡張機能のコマンドは、 [dotnet](/dotnet/core/tools)コマンドで、一部の[.NET Core SDK](https://www.microsoft.com/net/core)します。 これらのツールは、.NET Core プロジェクトで動作します。

Visual Studio を使用しているかどうか、お勧め、[パッケージ マネージャー コンソール ツール](powershell.md)代わりにします。
* 選択されている現在のプロジェクトに自動的に連動、**パッケージ マネージャー コンソール**せず、ディレクトリを手動で切り替えることです。
* コマンドが完了した後、コマンドによって生成されたファイルが自動的に開きます。

## <a name="installing-the-tools"></a>ツールのインストール

インストール手順は、プロジェクトの種類とバージョンによって異なります。

* EF Core 3.x
* ASP.NET Core 2.1 以降のバージョン
* EF Core 2.x
* EF Core 1.x へ

### <a name="ef-core-3x"></a>EF Core 3.x

* `dotnet ef` グローバルまたはローカルのツールとしてインストールする必要があります。 ほとんどの開発者がインストールされます`dotnet ef`として次のコマンドを使用してグローバル ツール。

  ``` console
    $ dotnet tool install --global dotnet-ef --version 3.0.0-*
  ```

  使用することも`dotnet ef`ローカル ツールとして。 これをローカルのツールとして使用するを使用してツールの依存関係として宣言するプロジェクトの依存関係を復元する[ツール マニフェスト ファイル](https://github.com/dotnet/cli/issues/10288)します。

* インストール、 [.NET Core SDK 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0))。 SDK は、Visual Studio の最新バージョンがある場合でもをインストールする必要があります。

* 最新のインストール`Microsoft.EntityFrameworkCore.Design`パッケージ。

  ``` Console
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 以降

* 現在のインストール[.NET Core SDK](https://www.microsoft.com/net/download/core)します。 SDK は、Visual Studio 2017 の最新バージョンがある場合でもインストールする必要があります。

  これは、ASP.NET Core 2.1 + ために必要なすべての`Microsoft.EntityFrameworkCore.Design`にパッケージが含まれる、 [Microsoft.AspNetCore.App メタパッケージ](/aspnet/core/fundamentals/metapackage-app)します。

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2.x (ASP.NET Core ではなく)

`dotnet ef`コマンドは、.NET Core SDK に含まれていますが、コマンドを有効にするには、インストールする必要が、`Microsoft.EntityFrameworkCore.Design`パッケージ。

* 現在のインストール[.NET Core SDK](https://www.microsoft.com/net/download/core)します。 SDK は、Visual Studio の最新バージョンがある場合でもをインストールする必要があります。

* 最新の安定したインストール`Microsoft.EntityFrameworkCore.Design`パッケージ。

  ``` Console
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1.x へ

* .NET Core SDK バージョン 2.1.200 をインストールします。 以降のバージョンは、EF Core 1.0 および 1.1 の CLI ツールで互換性がありません。

* 変更することで、SDK のバージョンを使用して、2.1.200 にアプリケーションを構成、 [global.json](/dotnet/core/tools/global-json)ファイル。 このファイルは通常、(プロジェクトの上に 1 つ) のソリューション ディレクトリにインクルードします。

* プロジェクト ファイルを編集および追加`Microsoft.EntityFrameworkCore.Tools.DotNet`として、`DotNetCliToolReference`項目。 たとえば、最新の 1.x バージョンを指定します。1.1.6 します。 このセクションの最後に、プロジェクト ファイルの例を参照してください。

* 最新の 1.x バージョンのインストール、`Microsoft.EntityFrameworkCore.Design`などをパッケージ化します。

  ```console
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 1.1.6
  ```

  追加された両方のパッケージ参照では、プロジェクト ファイルがこのようになります。

  ``` xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>netcoreapp1.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
      <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                        Version="1.1.6"
                         PrivateAssets="All" />
    </ItemGroup>
    <ItemGroup>
       <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                              Version="1.1.6" />
    </ItemGroup>
  </Project>
  ```

  パッケージ参照を`PrivateAssets="All"`このプロジェクトを参照するプロジェクトに公開されません。 この制限は、開発中にのみ使用される一般的なパッケージに特に便利です。

### <a name="verify-installation"></a>インストールの確認

EF Core CLI ツールが正しくインストールされていることを確認するには、次のコマンドを実行します。

  ``` Console
  dotnet restore
  dotnet ef
  ```

コマンドの出力は、使用中のツールのバージョンを識別します。

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="using-the-tools"></a>ツールを使用します。

ツールを使用するのには、スタートアップ プロジェクトを作成するか、環境を設定する必要があります。

### <a name="target-project-and-startup-project"></a>ターゲット プロジェクトとスタートアップ プロジェクト

コマンドを参照してください、*プロジェクト*と*スタートアップ プロジェクト*します。

* *プロジェクト*とも呼ばれますが、*ターゲット プロジェクト*コマンドが追加または、ファイルを削除であるためです。 既定では、現在のディレクトリ内のプロジェクトは、ターゲット プロジェクトです。 ターゲット プロジェクトとして使用して、別のプロジェクトを指定することができます、 <nobr> `--project` </nobr>オプション。

* *スタートアップ プロジェクト*ツールをビルドして実行されます。 ツールは、データベース接続文字列と、モデルの構成など、プロジェクトに関する情報を取得するデザイン時にアプリケーション コードを実行する必要があります。 既定では、現在のディレクトリ内のプロジェクトは、スタートアップ プロジェクトです。 使用して、別のプロジェクトをスタートアップ プロジェクトとして指定できます、 <nobr> `--startup-project` </nobr>オプション。

スタートアップ プロジェクトとターゲット プロジェクトは、同じプロジェクトでは多くの場合です。 個別のプロジェクトが、一般的なシナリオは。

* EF Core コンテキストとエンティティ クラスは、.NET Core クラス ライブラリでは。
* .NET Core コンソール アプリまたは web アプリは、クラス ライブラリを参照します。

することも[EF Core コンテキストから別のクラス ライブラリにコードを移行配置](xref:core/managing-schemas/migrations/projects)します。

### <a name="other-target-frameworks"></a>その他のターゲット フレームワーク

CLI ツールは、.NET Core プロジェクトと .NET Framework プロジェクトで動作します。 .NET Standard クラス ライブラリで、EF Core モデルを必要とするアプリケーションは、.NET Core または .NET Framework プロジェクトがあります。 たとえば、これは、Xamarin とユニバーサル Windows プラットフォーム アプリの場合は true。 このような場合、ツールのスタートアップ プロジェクトとして機能するが唯一の目的は、.NET Core コンソール アプリ プロジェクトを作成できます。 プロジェクトは、実際のコードなしでダミー プロジェクト&mdash;ターゲット ツールを提供する必要があるだけです。

必要なダミー プロジェクトはなぜですか。 前述のように、ツールは、デザイン時にアプリケーション コードを実行する必要があります。 は、.NET Core ランタイムを使用する必要があります。 EF Core モデルは、.NET Core または .NET Framework を対象とするプロジェクトでは、EF Core ツールは、プロジェクトからランタイムを借用します。 EF Core モデルが .NET Standard クラス ライブラリの場合、インストールできません。 .NET Standard は、実際の .NET 実装ではありません。一連の .NET 実装をサポートする必要がある Api の仕様です。 したがって .NET Standard はアプリケーション コードを実行する EF Core ツールのための十分です。 スタートアップ プロジェクトとして使用するように作成するダミーのプロジェクトでは、ツールが、.NET Standard クラス ライブラリを読み込むことができます、具体的なターゲット プラットフォームを提供します。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

ASP.NET Core プロジェクト用の環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**コマンドを実行する前に環境変数。

## <a name="common-options"></a>一般的なオプション

|                   | オプション                            | 説明                                                                                                                                                                                                                                                   |
|:------------------|:----------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                   | `--json`                          | JSON 出力を表示します。                                                                                                                                                                                                                                             |
| <nobr>`-c`</nobr> | `--context <DBCONTEXT>`           | 使用する `DbContext` クラス。 クラス名のみ、または名前空間を持つ完全修飾します。  このオプションを省略すると、EF Core は、コンテキスト クラスを紹介します。 複数のコンテキスト クラスがある場合は、このオプションが必要です。                                            |
| `-p`              | `--project <PROJECT>`             | ターゲット プロジェクトのプロジェクト フォルダーへの相対パス。  既定値は、現在のフォルダーです。                                                                                                                                                              |
| `-s`              | `--startup-project <PROJECT>`     | スタートアップ プロジェクトのプロジェクト フォルダーへの相対パス。 既定値は、現在のフォルダーです。                                                                                                                                                              |
|                   | `--framework <FRAMEWORK>`         | [ターゲット フレームワーク モニカー](/dotnet/standard/frameworks#supported-target-framework-versions)の[ターゲット フレームワーク](/dotnet/standard/frameworks)します。  プロジェクト ファイルは、複数のターゲット フレームワークを指定し、うち 1 つを選択するときに使用します。 |
|                   | `--configuration <CONFIGURATION>` | たとえば、ビルド構成:`Debug`または`Release`します。                                                                                                                                                                                                   |
|                   | `--runtime <IDENTIFIER>`          | パッケージを復元するターゲット ランタイムの識別子。 ランタイム ID (RID) の一覧については、[RID カタログ](/dotnet/core/rid-catalog)に関するページをご覧ください。                                                                                                      |
| `-h`              | `--help`                          | ヘルプ情報が表示されます。                                                                                                                                                                                                                                        |
| `-v`              | `--verbose`                       | 詳細な出力を表示します。                                                                                                                                                                                                                                          |
|                   | `--no-color`                      | しないと、出力が色分けして表示します。                                                                                                                                                                                                                                        |
|                   | `--prefix-output`                 | プレフィックスのレベルで出力します。                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>dotnet ef database delete

データベースを削除します。

オプション:

|                   | オプション                   | 説明                                              |
|:------------------|:-------------------------|:---------------------------------------------------------|
| <nobr>`-f`</nobr> | <nobr>`--force`</nobr>   | しないことを確認します。                                           |
|                   | <nobr>`--dry-run`</nobr> | 表示するデータベースが削除されます。 は削除しないでください。 |

## <a name="dotnet-ef-database-update"></a>dotnet ef database update

最後に移行したり、指定された移行は、データベースを更新します。

引数:

| 引数      | 説明                                                                                                                                                                                                                                                     |
|:--------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<MIGRATION>` | Target の移行。 移行は名前または ID で識別される可能性があります。 数値 0 は特殊なケースでは意味*最初の移行の前に*と、すべての移行を元に戻されます。 移行が指定されていない場合のコマンドは、既定最後の移行になります。 |

次の例では、指定された移行するデータベースを更新します。 最初の移行の名前を使用し、2 つ目は移行 ID を使用します。

```console
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate
```

## <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext info

に関する情報を取得します、`DbContext`型。

## <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext list

使用可能な`DbContext`型。

## <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext scaffold

コードを生成、`DbContext`とデータベースのエンティティ型。 エンティティ型を生成するには、このコマンドの順序は、データベース テーブルに主キーが必要です。

引数:

| 引数       | 説明                                                                                                                                                                                                             |
|:---------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<CONNECTION>` | データベースへの接続文字列。 ASP.NET Core 2.x プロジェクトの値を指定できます*名 =\<接続文字列の名前 >* します。 その場合は、名前は、プロジェクトに設定されている構成ソースから取得されます。 |
| `<PROVIDER>`   | 使用するプロバイダー。 たとえば、NuGet パッケージの名前は、この通常:`Microsoft.EntityFrameworkCore.SqlServer`します。                                                                                           |

オプション:

|                 | オプション                                   | 説明                                                                                                                                                                    |
|:----------------|:-----------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-d</nobr> | `--data-annotations`                     | 属性を使用すると、可能な限り、モデルを構成できます。 このオプションを省略した場合は、fluent API のみが使用されます。                                                                |
| `-c`            | `--context <NAME>`                       | 名前、`DbContext`クラスを生成します。                                                                                                                                 |
|                 | `--context-dir <PATH>`                   | 格納するディレクトリ、`DbContext`でクラス ファイル。 パスでは、プロジェクト ディレクトリに対して相対的です。 名前空間は、フォルダー名から派生します。                                 |
| `-f`            | `--force`                                | 既存のファイルを上書きします。                                                                                                                                                      |
| `-o`            | `--output-dir <PATH>`                    | エンティティ クラス ファイルを配置するディレクトリ。 パスでは、プロジェクト ディレクトリに対して相対的です。                                                                                       |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | エンティティ型を生成するテーブルのスキーマです。 複数のスキーマを指定するには、繰り返し`--schema`ごと。 このオプションを省略すると、すべてのスキーマが含まれます。          |
| `-t`            | `--table <TABLE_NAME>`...                | エンティティ型を生成するテーブル。 複数のテーブルを指定するには、繰り返し`-t`または`--table`ごと。 このオプションを省略すると、すべてのテーブルが含まれます。                |
|                 | `--use-database-names`                   | データベースに表示されているとおりに、テーブルおよび列名を使用します。 このオプションを省略すると、データベース名は、c# 名前表記規則をより厳密に準拠するように変更されます。 |

次の例は、すべてのスキーマとテーブルをスキャフォールディングし、新しいファイルを配置、*モデル*フォルダー。

```console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

次の例では、選択したテーブルのみをスキャフォールディングし、指定の名前を持つ別のフォルダーにコンテキストを作成します。

```console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext
```

## <a name="dotnet-ef-migrations-add"></a>dotnet ef migrations add

新しい移行を追加します。

引数:

| 引数 | 説明                |
|:---------|:---------------------------|
| `<NAME>` | 移行の名前。 |

オプション:

|                   | オプション                             | 説明                                                                                                      |
|:------------------|:-----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>`-o`</nobr> | <nobr>`--output-dir <PATH>`</nobr> | 使用するディレクトリ (およびサブ名前空間)。 パスでは、プロジェクト ディレクトリに対して相対的です。 既定値は「移行」です。 |

## <a name="dotnet-ef-migrations-list"></a>dotnet ef migrations list

使用可能な移行を一覧表示します。

## <a name="dotnet-ef-migrations-remove"></a>dotnet ef migrations delete

最後の移行 (ロールバックの移行の実行されたコードの変更) を削除します。

オプション:

|                   | オプション    | 説明                                                                     |
|:------------------|:----------|:--------------------------------------------------------------------------------|
| <nobr>`-f`</nobr> | `--force` | 移行を元に戻す (ロールバックをデータベースに適用された変更)。 |

## <a name="dotnet-ef-migrations-script"></a>dotnet ef migrations script

移行には、SQL スクリプトを生成します。

引数:

| 引数 | 説明                                                                                                                                                   |
|:---------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<FROM>` | 開始の移行。 移行は名前または ID で識別される可能性があります。 数値 0 は特殊なケースでは意味*最初の移行の前に*します。 既定値は 0 です。 |
| `<TO>`   | 終了の移行。 移行の最後の既定値します。                                                                                                         |

オプション:

|                   | オプション            | 説明                                                        |
|:------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`-o`</nobr> | `--output <FILE>` | スクリプトを記述するファイルです。                                   |
| `-i`              | `--idempotent`    | すべての移行でのデータベースで使用できるスクリプトを生成します。 |

次の例では、InitialCreate 移行用のスクリプトを作成します。

```console
dotnet ef migrations script 0 InitialCreate
```

次の例では、InitialCreate 移行後のすべての移行のスクリプトを作成します。

```console
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>その他の技術情報

* [移行](xref:core/managing-schemas/migrations/index)
* [リバース エンジニアリング](xref:core/managing-schemas/scaffolding)

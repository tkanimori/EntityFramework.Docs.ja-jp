---
title: EF Core ツールリファレンス (.NET CLI)-EF Core
description: Entity Framework Core .NET Core CLI ツールのリファレンスガイド
author: bricelam
ms.date: 10/13/2020
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 4056fb99659ee3390d16b18eca9b12cfc8a2dd03
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062517"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Entity Framework Core ツールリファレンス-.NET Core CLI

Entity Framework Core 用のコマンドラインインターフェイス (CLI) ツールは、デザイン時の開発タスクを実行します。 たとえば、既存のデータベースに基づいて、 [移行](/aspnet/core/data/ef-mvc/migrations)を作成し、移行を適用し、モデルのコードを生成します。 コマンドは、 [.NET Core SDK](https://www.microsoft.com/net/core)の一部であるクロスプラットフォーム[dotnet](/dotnet/core/tools)コマンドの拡張機能です。 これらのツールは、.NET Core プロジェクトで動作します。

Visual Studio を使用する場合は、CLI ツールの代わりに [パッケージマネージャーコンソールツール](xref:core/miscellaneous/cli/powershell) を使用することを検討してください。 パッケージマネージャーコンソールツールは、次のように自動的になります。

* **パッケージマネージャーコンソール**で選択されている現在のプロジェクトと連動します。ディレクトリを手動で切り替える必要はありません。
* コマンドの完了後にコマンドによって生成されたファイルを開きます。
* コマンド、パラメーター、プロジェクト名、コンテキストの種類、および移行名のタブ補完を提供します。

## <a name="installing-the-tools"></a>ツールのインストール

`dotnet ef` は、グローバルまたはローカルのいずれかのツールとしてインストールできます。 ほとんどの開発者 `dotnet ef` は、次のコマンドを使用してをグローバルツールとしてインストールすることをお勧めします。

```dotnetcli
dotnet tool install --global dotnet-ef
```

ローカルツールとして使用するには、 [ツールマニフェストファイル](/dotnet/core/tools/global-tools#install-a-local-tool)を使用して、ツールの依存関係として宣言するプロジェクトの依存関係を復元します。

次のコマンドを使用してツールツールを更新します。

```dotnetcli
dotnet tool update --global dotnet-ef
```

特定のプロジェクトでツールを使用するには、その前にパッケージを追加する必要があり `Microsoft.EntityFrameworkCore.Design` ます。

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### <a name="verify-installation"></a>インストールを確認する

次のコマンドを実行して EF Core CLI ツールが正しくインストールされていることを確認します。

  ```dotnetcli
  dotnet ef
  ```

コマンドからの出力は、使用されているツールのバージョンを識別します。

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="update-the-tools"></a>ツールを更新する

を使用して、 `dotnet tool update --global dotnet-ef` グローバルツールを利用可能な最新バージョンに更新します。 プロジェクトにローカルにインストールされているツールがある場合は、を使用 `dotnet tool update dotnet-ef` します。 コマンドにを追加して、特定のバージョンをインストールし `--version <VERSION>` ます。 詳細については、dotnet tool のドキュメントの [更新](/dotnet/core/tools/dotnet-tool-update) に関するセクションを参照してください。

## <a name="using-the-tools"></a>ツールの使用

ツールを使用する前に、スタートアッププロジェクトを作成するか、環境を設定することが必要になる場合があります。

### <a name="target-project-and-startup-project"></a>ターゲットプロジェクトとスタートアッププロジェクト

これらのコマンドは、 *プロジェクト* と *スタートアッププロジェクト*を参照します。

* *プロジェクト*は*ターゲットプロジェクト*とも呼ばれます。これは、コマンドによってファイルが追加または削除されるためです。 既定では、現在のディレクトリ内のプロジェクトはターゲットプロジェクトです。 オプションを使用して、別のプロジェクトをターゲットプロジェクトとして指定でき <nobr>`--project`</nobr> ます。

* *スタートアッププロジェクト*は、ツールをビルドして実行するためのものです。 このツールでは、デザイン時にアプリケーションコードを実行して、プロジェクトに関する情報 (データベース接続文字列やモデルの構成など) を取得する必要があります。 既定では、現在のディレクトリのプロジェクトはスタートアッププロジェクトです。 オプションを使用して、別のプロジェクトをスタートアッププロジェクトとして指定でき <nobr>`--startup-project`</nobr> ます。

多くの場合、スタートアッププロジェクトとターゲットプロジェクトは同じプロジェクトです。 個別のプロジェクトである一般的なシナリオは、次のような場合です。

* EF Core コンテキストとエンティティクラスは、.NET Core クラスライブラリにあります。
* .NET Core コンソールアプリまたは web アプリはクラスライブラリを参照します。

また、 [移行コードを EF Core コンテキストとは別のクラスライブラリに配置](xref:core/managing-schemas/migrations/projects)することもできます。

### <a name="other-target-frameworks"></a>その他のターゲットフレームワーク

CLI ツールは、.NET Core プロジェクトと .NET Framework プロジェクトで使用できます。 .NET Standard クラスライブラリに EF Core モデルがあるアプリには、.NET Core または .NET Framework プロジェクトがない可能性があります。 たとえば、これは Xamarin とユニバーサル Windows プラットフォームアプリに当てはまります。 このような場合は、ツールのスタートアッププロジェクトとして機能することだけを目的とした .NET Core コンソールアプリプロジェクトを作成できます。 プロジェクトは、実際のコードを持たないダミープロジェクトにすることができ &mdash; ます。これは、ツールのターゲットを指定するためにのみ必要です。

ダミープロジェクトが必要な理由 前述のように、ツールはデザイン時にアプリケーションコードを実行する必要があります。 そのためには、.NET Core ランタイムを使用する必要があります。 EF Core モデルが .NET Core または .NET Framework を対象とするプロジェクト内にある場合、EF Core ツールはプロジェクトからランタイムを借用します。 EF Core モデルが .NET Standard クラスライブラリ内にある場合は、これを行うことはできません。 .NET Standard は実際の .NET 実装ではありません。これは、.NET 実装がサポートする必要のある一連の Api の仕様です。 したがって、EF Core ツールでアプリケーションコードを実行するために .NET Standard は十分ではありません。 スタートアッププロジェクトとして使用するために作成するダミープロジェクトは、ツールが .NET Standard クラスライブラリを読み込むことができる具象ターゲットプラットフォームを提供します。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

ASP.NET Core プロジェクトの環境を指定するには、コマンドを実行する前に **ASPNETCORE_ENVIRONMENT** 環境変数を設定します。

## <a name="common-options"></a>共通オプション

| オプション                                         | Short             | 説明                                                                                                                                                                                                                                                   |
|:-----------------------------------------------|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--json`                                       |                   | JSON 出力を表示します。                                                                                                                                                                                                                                             |
| `--context <DBCONTEXT>`                        | <nobr>`-c`</nobr> | 使用する `DbContext` クラス。 クラス名のみ、または名前空間で完全修飾されています。  このオプションを省略した場合、EF Core によってコンテキストクラスが検索されます。 複数のコンテキストクラスがある場合は、このオプションが必要です。                                            |
| `--project <PROJECT>`                          | `-p`              | ターゲットプロジェクトのプロジェクトフォルダーへの相対パス。  既定値は現在のフォルダーです。                                                                                                                                                              |
| `--startup-project <PROJECT>`                  | `-s`              | スタートアッププロジェクトのプロジェクトフォルダーへの相対パス。 既定値は現在のフォルダーです。                                                                                                                                                              |
| `--framework <FRAMEWORK>`                      |                   | [ターゲットフレームワーク](/dotnet/standard/frameworks)の[ターゲットフレームワークモニカー](/dotnet/standard/frameworks#supported-target-framework-versions) 。  プロジェクトファイルで複数のターゲットフレームワークを指定し、そのうちの1つを選択する場合は、を使用します。 |
| <nobr>`--configuration <CONFIGURATION>`</nobr> |                   | ビルド構成 (たとえば、 `Debug` または) `Release` 。                                                                                                                                                                                                   |
| `--runtime <IDENTIFIER>`                       |                   | パッケージを復元する対象のランタイムの識別子。 ランタイム ID (RID) の一覧については、[RID カタログ](/dotnet/core/rid-catalog)に関するページをご覧ください。                                                                                                      |
| `--no-build`                                   |                   | プロジェクトをビルドしません。 ビルドが最新の状態である場合に使用することを目的としています。                                                                                                                                                                                    |
| `--help`                                       | `-h`              | ヘルプ情報を表示します。                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | 詳細出力を表示します。                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | 出力を色分けしません。                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | レベルのプレフィックス出力。                                                                                                                                                                                                                                     |

EF Core 5.0 以降では、追加の引数はアプリケーションに渡されます。

## <a name="dotnet-ef-database-drop"></a>dotnet ef データベースの削除

データベースを削除します。

オプション:

| オプション                   | Short             | 説明                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | 確認しないでください。                                           |
| <nobr>`--dry-run`</nobr> |                   | 削除するデータベースを表示しますが、削除はしません。 |

[一般的なオプション](#common-options)は上記のとおりです。

## <a name="dotnet-ef-database-update"></a>dotnet ef データベースの更新

最後に移行したデータベース、または指定した移行にデータベースを更新します。

引数:

| 引数                   | 説明                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | ターゲットの移行。 移行は、名前または ID で識別できます。 数値0は、 *最初の移行の前に* 特別なケースであり、すべての移行が元に戻されます。 移行が指定されていない場合、コマンドは既定で最後の移行になります。 |

オプション:

| オプション                                    | 説明                                                                                                                      |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | データベースへの接続文字列。 既定値は、またはで指定されたもの `AddDbContext` `OnConfiguring` です。 EF Core 5.0 で追加されました。 |

[一般的なオプション](#common-options)は上記のとおりです。

次の例では、指定された移行にデータベースを更新します。 最初のは移行名を使用し、2つ目のは移行 ID と指定された接続を使用します。

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext 情報

型に関する情報を取得し `DbContext` ます。

[一般的なオプション](#common-options)は上記のとおりです。

## <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext の一覧

使用可能な種類が一覧表示さ `DbContext` れます。

[一般的なオプション](#common-options)は上記のとおりです。

## <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext スキャフォールディング

`DbContext`データベースのおよびエンティティ型のコードを生成します。 このコマンドでエンティティ型を生成するには、データベーステーブルに主キーが必要です。

引数:

| 引数                    | 説明                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | データベースへの接続文字列。 ASP.NET Core 2.x プロジェクトの場合、値には*name = \<name of connection string> *を指定できます。 その場合、プロジェクト用に設定されている構成ソースから名前を取得します。 |
| `<PROVIDER>`                | 使用するプロバイダー。 通常、これは NuGet パッケージの名前です (例:) `Microsoft.EntityFrameworkCore.SqlServer` 。                                                                                           |

オプション:

| オプション                                   | Short             | 説明                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | 属性を使用してモデルを構成します (可能な場合)。 このオプションを省略した場合は、fluent API のみが使用されます。                                                                |
| `--context <NAME>`                       | `-c`              | `DbContext`生成するクラスの名前。                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | クラスファイルを格納するディレクトリ `DbContext` 。 パスは、プロジェクトディレクトリに対する相対パスです。 名前空間は、フォルダー名から派生します。                                 |
| `--context-namespace <NAMESPACE>`        |                   | 生成されたクラスに使用する名前空間 `DbContext` 。 注: はオーバーライドさ `--namespace` れます。 EF Core 5.0 で追加されました。                                                                 |
| `--force`                                | `-f`              | 既存のファイルを上書きします。                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | エンティティクラスファイルを配置するディレクトリ。 パスは、プロジェクトディレクトリに対する相対パスです。                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | 生成されたすべてのクラスに使用する名前空間。 既定値は、ルート名前空間と出力ディレクトリから生成されます。 EF Core 5.0 で追加されました。                                  |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | エンティティ型を生成するテーブルのスキーマ。 複数のスキーマを指定するには、それぞれのスキーマを繰り返し `--schema` ます。 このオプションを省略した場合、すべてのスキーマが含まれます。          |
| `--table <TABLE_NAME>`...                | `-t`              | エンティティ型を生成するテーブル。 複数のテーブルを指定するに `-t` は、 `--table` 1 つのテーブルに対してまたはを繰り返します。 このオプションを省略した場合、すべてのテーブルが含まれます。                |
| `--use-database-names`                   |                   | テーブル名と列名は、データベースに表示されるとおりに使用します。 このオプションを省略した場合、データベース名は、C# の名前のスタイル規則により厳密に準拠するように変更されます。 |
| `--no-onconfiguring`                     |                   | 生成されたクラスのメソッドの生成を抑制し `OnConfiguring` `DbContext` ます。 EF Core 5.0 で追加されました。                                                                  |
| `--no-pluralize`                         |                   | プルーラライザーは使用しないでください。 EF Core 5.0 に追加されました                                                                                                                                 |

[一般的なオプション](#common-options)は上記のとおりです。

次の例では、すべてのスキーマとテーブルをスキャフォールディングし、新しいファイルを [ *モデル* ] フォルダーに配置します。

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

次の例では、選択したテーブルのみをスキャフォールディングし、指定された名前と名前空間を持つ別のフォルダーにコンテキストを作成します。

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

## <a name="dotnet-ef-dbcontext-script"></a>dotnet ef dbcontext スクリプト

DbContext から SQL スクリプトを生成します。 すべての移行をバイパスします。 EF Core 3.0 で追加されました。

オプション:

| オプション                         | Short             | 説明                      |
| ------------------------------ | ----------------- | -------------------------------- |
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | 結果の書き込み先のファイル。 |

[一般的なオプション](#common-options)は上記のとおりです。

## <a name="dotnet-ef-migrations-add"></a>dotnet ef 移行の追加

新しい移行を追加します。

引数:

| 引数              | 説明                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | 移行の名前。 |

オプション:

| オプション                                 | Short             | 説明                                                                                                            |
|:---------------------------------------|:------------------|:-----------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | ファイルの出力に使用するディレクトリ。 パスは、ターゲットプロジェクトディレクトリに対する相対パスです。 既定値は "移行" です。   |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | 生成されたクラスに使用する名前空間。 既定値は、出力ディレクトリから生成されます。 EF Core 5.0 で追加されました。 |

[一般的なオプション](#common-options)は上記のとおりです。

## <a name="dotnet-ef-migrations-list"></a>dotnet ef 移行リスト

使用可能な移行を一覧表示します。

オプション:

| オプション                                   | 説明                                                                                                                  |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| <nobr>`--connection <CONNECTION>`</nobr> | データベースへの接続文字列。 既定値は、AddDbContext または OnConfiguring で指定されたものです。 EF Core 5.0 で追加されました。 |
| `--no-connect`                           | データベースに接続しないでください。 EF Core 5.0 で追加されました。                                                                         |

[一般的なオプション](#common-options)は上記のとおりです。

## <a name="dotnet-ef-migrations-remove"></a>dotnet ef 移行の削除

最後の移行を削除します (移行のために実行されたコード変更をロールバックします)。

オプション:

| オプション                 | Short             | 説明                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | 移行を元に戻します (データベースに適用された変更をロールバックします)。 |

[一般的なオプション](#common-options)は上記のとおりです。

## <a name="dotnet-ef-migrations-script"></a>dotnet ef 移行スクリプト

移行から SQL スクリプトを生成します。

引数:

| 引数              | 説明                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | 移行を開始しています。 移行は、名前または ID で識別できます。 数値0は、 *最初の移行の前に*特別なケースです。 既定値は 0 です。 |
| `<TO>`                | 移行を終了しています。 既定では、最後の移行になります。                                                                                                         |

オプション:

| オプション                           | Short             | 説明                                                        |
|:---------------------------------|:------------------|:-------------------------------------------------------------------|
| `--output <FILE>`                | <nobr>`-o`</nobr> | スクリプトの書き込み先のファイル。                                   |
| `--idempotent`                   | `-i`              | 任意の移行時にデータベースで使用できるスクリプトを生成します。 |
| <nobr>`--no-transactions`</nobr> |                   | SQL トランザクションステートメントを生成しません。 EF Core 5.0 で追加されました。   |

[一般的なオプション](#common-options)は上記のとおりです。

次の例では、InitialCreate 移行用のスクリプトを作成します。

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

次の例では、InitialCreate 移行後にすべての移行用のスクリプトを作成します。

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>その他の資料

* [移行](xref:core/managing-schemas/migrations/index)
* [リバースエンジニアリング](xref:core/managing-schemas/scaffolding)

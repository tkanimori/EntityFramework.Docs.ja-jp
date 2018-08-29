---
title: .NET core CLI - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: c70003fc7e2c5381a22d26baacd3d76f32489328
ms.sourcegitcommit: 0cef7d448e1e47bdb333002e2254ed42d57b45b6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43152415"
---
<a name="ef-core-net-command-line-tools"></a>EF Core .NET コマンド ライン ツール
===============================
Entity Framework Core .NET コマンド ライン ツール、クロス プラットフォーム拡張機能**dotnet**コマンドで、一部の[.NET Core SDK][2]します。

> [!TIP]
> Visual Studio を使用しているかどうか、お勧めします[に優れた PMC ツール][ 1]代わりに、統合性を提供するためです。

<a name="installing-the-tools"></a>ツールのインストール
--------------------
> [!NOTE]
> .NET Core SDK バージョン 2.1.300 新しいが含まれています**dotnet ef** EF Core 2.0 以降のバージョンと互換性のあるコマンド。 したがって最近のバージョンの .NET Core SDK と EF Core ランタイムを使用している場合、インストールは必要なく、このセクションの残りの部分を無視することができます。
>
> 一方で、 **dotnet ef**以降と .NET Core SDK バージョン 2.1.300 に格納されているツールは、EF Core のバージョン 1.0 および 1.1 と互換性がありません。 2.1.200 のバージョンをインストールする前に、.NET Core SDK 2.1.300 をあるコンピューター上で以前のバージョンの EF Core を使用するプロジェクトを使用する、または新しいインストールもする必要がありますまたは SDK の古い変更してその以前のバージョンを使用するアプリケーションを構成して、 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)ファイル。 このファイルは通常、(プロジェクトの上に 1 つ) のソリューション ディレクトリにインクルードします。 以下の installlation 命令を続行できます。

.NET Core SDK の以前のバージョンには、次の手順を使用して EF Core .NET コマンド ライン ツールをインストールできます。

1. プロジェクト ファイルを編集し、Microsoft.EntityFrameworkCore.Tools.DotNet を DotNetCliToolReference 項目 (下記参照) として追加
2. 次のコマンドを実行します。

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore

作成されたプロジェクトは、次のようになります。

``` xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                      Version="2.0.0"
                      PrivateAssets="All" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                            Version="2.0.0" />
  </ItemGroup>
</Project>
```

> [!NOTE]
> パッケージ参照を`PrivateAssets="All"`開発時にのみ使用される一般的なパッケージに特に便利ですが、このプロジェクトを参照するプロジェクトに公開されていないことを意味します。

右のすべてのプロセスが、コマンド プロンプトで次のコマンドを正常に実行できる必要があります。

``` Console
dotnet ef
```

<a name="using-the-tools"></a>ツールを使用します。
---------------
コマンドを呼び出すたびにある 2 つのプロジェクト関係します。

ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。 ターゲット プロジェクトが現在のディレクトリでプロジェクトの既定値を使用して変更することが、 <nobr> **`--project`** </nobr>オプション。

スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。 またに既定値は、現在のディレクトリでプロジェクトを使用して変更できる、 **`--startup-project`** オプション。

> [!NOTE]
> たとえば、次のようなりますを持つ別のプロジェクトにインストールされている EF Core web アプリケーションのデータベースの更新: `dotnet ef database update --project {project-path}` (から、web アプリのディレクトリ)

一般的なオプション:

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | `--json`                           | JSON 出力を表示します。           |
| -c | `--context <DBCONTEXT>`           | 使用する DbContext します。       |
| -p | `--project <PROJECT>`             | 使用するプロジェクトです。         |
| -s | `--startup-project <PROJECT>`     | 使用するスタートアップ プロジェクトです。 |
|    | `--framework <FRAMEWORK>`         | ターゲット フレームワーク。       |
|    | `--configuration <CONFIGURATION>` | 使用する構成。   |
|    | `--runtime <IDENTIFIER>`          | 使用するランタイム。         |
| -h | `--help`                           | ヘルプ情報が表示されます。      |
| -v | `--verbose`                        | 詳細な出力を表示します。        |
|    | `--no-color`                       | しないと、出力が色分けして表示します。      |
|    | `--prefix-output`                  | プレフィックスのレベルで出力します。   |


> [!TIP]
> ASP.NET Core 環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**実行する前に環境変数。

<a name="commands"></a>コマンド
--------

### <a name="dotnet-ef-database-drop"></a>dotnet ef データベースの削除

データベースを削除します。

オプション:

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| -f | `--force`   | しないことを確認します。                                           |
|    | `--dry-run` | 表示するデータベースが削除されます。 は削除しないでください。 |

### <a name="dotnet-ef-database-update"></a>dotnet ef データベースの更新

指定された移行するには、データベースを更新します。

引数:

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| `<MIGRATION>` | Target の移行。 0 の場合、すべての移行は戻されます。 移行の最後の既定値します。 |

### <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext の情報

DbContext 型に関する情報を取得します。

### <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext の一覧

使用可能な DbContext 型を一覧表示します。

### <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext スキャフォールディング

データベースの DbContext とエンティティ型をスキャフォールディングします。

引数:

|               |                                                                             |
|:--------------|:----------------------------------------------------------------------------|
| `<CONNECTION>` | データベースへの接続文字列。                                      |
| `<PROVIDER>`   | 使用するプロバイダー。 (たとえば、Microsoft.EntityFrameworkCore.SqlServer) |

オプション:

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <nobr>-d</nobr> | `--data-annotations`                      | 属性を使用すると、可能な限り、モデルを構成できます。 省略した場合、fluent API のみが使用されます。 |
| -c              | `--context <NAME>`                       | DbContext の名前。                                                                       |
|                 | `--context-dir <PATH>`                   | DbContext のファイルを配置するディレクトリ。 パスでは、プロジェクト ディレクトリに対して相対的です。             |
| -f              | `--force`                                 | 既存のファイルを上書きします。                                                                        |
| -o              | `--output-dir <PATH>`                    | ファイルを配置するディレクトリ。 パスでは、プロジェクト ディレクトリに対して相対的です。                      |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | エンティティ型を生成するテーブルのスキーマです。                                              |
| -t              | `--table <TABLE_NAME>`...                | エンティティ型を生成するテーブル。                                                         |
|                 | `--use-database-names`                    | データベースから直接テーブルおよび列名を使用します。                                           |

### <a name="dotnet-ef-migrations-add"></a>dotnet ef migrations を追加します。

新しい移行を追加します。

引数:

|         |                            |
|:--------|:---------------------------|
| `<NAME>` | 移行の名前。 |

オプション:

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>-o</nobr> | <nobr> `--output-dir <PATH>` </nobr> | 使用するディレクトリ (およびサブ名前空間)。 パスでは、プロジェクト ディレクトリに対して相対的です。 既定値は「移行」です。 |

### <a name="dotnet-ef-migrations-list"></a>dotnet ef migrations 一覧

使用可能な移行を一覧表示します。

### <a name="dotnet-ef-migrations-remove"></a>dotnet ef migrations を削除します。

最後の移行を削除します。

オプション:

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| -f | `--force` | データベースに適用されている場合は、移行を戻します。 |

### <a name="dotnet-ef-migrations-script"></a>dotnet ef migrations スクリプト

移行には、SQL スクリプトを生成します。

引数:

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| `<FROM>` | 開始の移行。 既定値は 0 (最初のデータベース) です。 |
| `<TO>`   | 終了の移行。 移行の最後の既定値します。         |

オプション:

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| -o | `--output <FILE>` | 結果を書き込むファイル。                                   |
| -i | `--idempotent`     | すべての移行でのデータベースで使用できるスクリプトを生成します。 |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core

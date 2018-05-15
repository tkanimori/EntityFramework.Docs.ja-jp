---
title: '.NET core CLI: EF コア'
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: d053d53bd50d2e7d16223c5b4e4009c9bb2298bb
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
<a name="ef-core-net-command-line-tools"></a>EF コア .NET コマンド ライン ツール
===============================
Entity Framework Core .NET コマンド ライン ツールは、クロス プラットフォーム拡張機能**dotnet**コマンドでは、一部の[.NET Core SDK][2]です。

> [!TIP]
> Visual Studio を使用するかどうか、ことをお勧め[PMC ツール][ 1]より統合されたエクスペリエンスを提供するために代わりにします。

<a name="installing-the-tools"></a>ツールのインストール
--------------------
> [!NOTE]
> .NET Core SDK バージョン 2.1.300 おり、新しい**dotnet ef** EF コア 2.0 およびそれ以降のバージョンと互換性のあるコマンド。 したがって最新バージョンの .NET Core SDK と EF Core ランタイムを使用している場合をインストールする必要はありませんし、このセクションの残りの部分を無視することができます。
>
> その一方で、 **dotnet ef**ツール .NET Core SDK バージョン 2.1.300 に含まれていると、それ以降は EF コア バージョン 1.0 および 1.1 と互換性がありません。 .NET Core SDK 2.1.300 をあるコンピューター上で以前のバージョンの EF コアを使用するプロジェクトで作業することができます以降がインストールされて、前にバージョン 2.1.200 もインストールする必要がありますまたは SDK の古い変更してその古いバージョンを使用するアプリケーションを構成して、 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)ファイル。 このファイルは、(プロジェクトの上に 1 つ) のソリューション ディレクトリに通常含まれます。 下の installlation 命令で開始できます。

以前のバージョンの .NET Core SDK では、次の手順を使用して EF コア .NET コマンド ライン ツールをインストールすることができます。

1. プロジェクト ファイルを編集し、DotNetCliToolReference 項目 (下記参照) として Microsoft.EntityFrameworkCore.Tools.DotNet を追加
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
> パッケージの参照を`PrivateAssets="All"`開発時にのみ使用される通常パッケージに特に便利ですが、このプロジェクトを参照しているプロジェクトに公開されていないことを意味します。

すべての権利を行った場合は、コマンド プロンプトで次のコマンドを正常に実行することができます。

``` Console
dotnet ef
```

<a name="using-the-tools"></a>ツールを使用します。
---------------
コマンドを呼び出すときに 2 つのプロジェクト。

ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。 ターゲット プロジェクトが、現在のディレクトリで、プロジェクトの既定値を使用して変更することが、 <nobr> **--プロジェクト**</nobr>オプション。

スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。 また、現在のディレクトリで、プロジェクトの既定値を使用して変更することができます、 **--スタートアップ プロジェクト**オプション。

> [!NOTE]
> たとえば、次のようなりますを別のプロジェクトにインストールされている EF コアを持つ web アプリケーションのデータベースを更新します`dotnet ef database update --project {project-path}`(からの web アプリのディレクトリ)。

一般的なオプション:

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | --json                           | JSON の出力を表示します。           |
| -c | --context \<DBCONTEXT>           | 使用する DbContext です。       |
| -p | --project \<PROJECT>             | 使用するプロジェクトです。         |
| -s | --startup-project \<PROJECT>     | 使用するスタートアップ プロジェクトです。 |
|    | --framework \<FRAMEWORK >         | ターゲット フレームワーク。       |
|    | --構成\<構成 > | 使用する構成。   |
|    | --ランタイム\<識別子 >          | 使用するランタイム。         |
| -h | -ヘルプ                           | ヘルプ情報を表示します。      |
| -v | -詳細                        | 詳細出力を表示します。        |
|    | --no-color                       | しないと、出力が色分けして表示します。      |
|    | --prefix-output                  | 出力レベルをプレフィックスします。   |


> [!TIP]
> ASP.NET Core 環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**実行する前に環境変数。

<a name="commands"></a>コマンド
--------

### <a name="dotnet-ef-database-drop"></a>dotnet ef データベースの削除

データベースを削除します。

オプション:

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| -f | --強制   | しないことを確認します。                                           |
|    | --予行 | データベースは削除しますが、それを削除しないを表示します。 |

### <a name="dotnet-ef-database-update"></a>dotnet ef データベースの更新

指定された移行するには、データベースを更新します。

引数:

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| \<移行 &GT; | Target の移行。 0 の場合、すべての移行は元に戻せません。 既定値は最後に移行します。 |

### <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext 情報

DbContext 型に関する情報を取得します。

### <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext 一覧

使用可能な DbContext 型を一覧表示します。

### <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext scaffold

スキャフォールディング データベースの DbContext とエンティティの型。

引数:

|               |                                                                     |
|:--------------|:--------------------------------------------------------------------|
| \<接続 &GT; | データベースへの接続文字列。                              |
| \<プロバイダー &GT;   | 使用するプロバイダー。 (例: Microsoft.EntityFrameworkCore.SqlServer) |

オプション:

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <nobr>-d</nobr> | -データ注釈                      | 属性を使用すると、(可能な場合)、モデルを構成できます。 省略すると、fluent API のみが使用されます。 |
| -c              | --context \<NAME>                       | DbContext の名前。                                                                       |
|                 | --コンテキスト dir\<パス >                   | DbContext ファイルを配置するディレクトリ。 パスでは、プロジェクト ディレクトリに対して相対的です。             |
| -f              | --強制                                 | 既存のファイルを上書きします。                                                                        |
| -o              | --output-dir \<PATH>                    | ファイルを置くディレクトリです。 パスでは、プロジェクト ディレクトリに対して相対的です。                      |
|                 | <nobr>-スキーマ\<SCHEMA_NAME >.</nobr> | エンティティ型を生成するテーブルのスキーマです。                                              |
| -t              | -テーブル\<TABLE_NAME >.                | エンティティ型を生成するテーブル。                                                         |
|                 | --use-database-names                    | データベースから直接テーブルおよび列名を使用します。                                           |

### <a name="dotnet-ef-migrations-add"></a>dotnet ef 移行を追加します。

新しい移行を追加します。

引数:

|         |                            |
|:--------|:---------------------------|
| \<NAME> | 移行の名前。 |

オプション:

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>-o</nobr> | <nobr>--output-dir \<PATH></nobr> | ディレクトリ (およびサブ名前空間) を使用します。 パスでは、プロジェクト ディレクトリに対して相対的です。 既定値は「移行」です。 |

### <a name="dotnet-ef-migrations-list"></a>dotnet ef 移行リスト

使用可能な移行を一覧表示します。

### <a name="dotnet-ef-migrations-remove"></a>dotnet ef 移行を削除します。

前回の移行を削除します。

オプション:

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| -f | --強制 | それがデータベースに適用された場合は、移行を戻します。 |

### <a name="dotnet-ef-migrations-script"></a>dotnet ef 移行スクリプト

移行からの SQL スクリプトを生成します。

引数:

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| \<FROM> | 開始の移行。 既定値は 0 (最初のデータベース)。 |
| \<TO>   | 終了の移行。 既定値は最後に移行します。         |

オプション:

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| -o | --出力\<ファイル > | 結果を書き込むファイルです。                                   |
| -i | --idempotent     | すべての移行でのデータベースで使用できるスクリプトを生成します。 |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core

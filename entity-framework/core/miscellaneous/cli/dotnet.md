---
title: ".NET core CLI: EF コア"
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 26b5fb326d20575ed2f3c6955c699e0c3757bf57
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
<a name="ef-core-net-command-line-tools"></a>EF コア .NET コマンド ライン ツール
===============================
Entity Framework Core .NET コマンド ライン ツールは、クロス プラットフォーム拡張機能**dotnet**コマンドでは、一部の[.NET Core SDK][2]です。

> [!TIP]
> Visual Studio を使用するかどうか、ことをお勧め[PMC ツール][ 1]より統合されたエクスペリエンスを提供するために代わりにします。

<a name="installing-the-tools"></a>ツールのインストール
--------------------
次の手順を使用して EF コア .NET コマンド ライン ツールをインストールします。

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

ターゲット プロジェクトがすべてのファイルが追加された (または場合によっては削除) します。 ターゲット プロジェクトが、現在のディレクトリで、プロジェクトの既定値を使用して変更することが、 <nobr> **--プロジェクト**</nobr>オプション。

スタートアップ プロジェクトは、プロジェクトのコードを実行すると、ツールでエミュレートです。 また、現在のディレクトリで、プロジェクトの既定値を使用して変更することができます、 **--スタートアップ プロジェクト**オプション。

一般的なオプション:

|    |                                  |                             |
| -- | -------------------------------- | --------------------------- |
|    | -json                           | JSON の出力を表示します。           |
| -c | --コンテキスト\<DBCONTEXT >           | 使用する DbContext です。       |
| -P | --プロジェクト\<プロジェクト >             | 使用するプロジェクトです。         |
| -s | -スタートアップ プロジェクト\<プロジェクト >     | 使用するスタートアップ プロジェクトです。 |
|    | --framework \<FRAMEWORK >         | ターゲット フレームワーク。       |
|    | --構成\<構成 > | 使用する構成。   |
|    | --ランタイム\<識別子 >          | 使用するランタイム。         |
| -h | -ヘルプ                           | ヘルプ情報を表示します。      |
| -v | -詳細                        | 詳細出力を表示します。        |
|    | --色なし                       | しないと、出力が色分けして表示します。      |
|    | --プレフィックス出力                  | 出力レベルをプレフィックスします。   |


> [!TIP]
> ASP.NET Core 環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**実行する前に環境変数。

<a name="commands"></a>コマンド
--------

### <a name="dotnet-ef-database-drop"></a>dotnet ef データベースの削除

データベースを削除します。

オプション:

|    |           |                                                          |
| -- | --------- | -------------------------------------------------------- |
| -f | --強制   | しないことを確認します。                                           |
|    | --予行 | データベースは削除しますが、それを削除しないを表示します。 |

### <a name="dotnet-ef-database-update"></a>dotnet ef データベースの更新

指定された移行するには、データベースを更新します。

引数:

|              |                                                                                              |
| ------------ | ---------------------------------------------------------------------------------------------|
| \<移行 > | Target の移行。 0 の場合、すべての移行は元に戻せません。 既定値は最後に移行します。 |

### <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext 情報

DbContext 型に関する情報を取得します。

### <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext 一覧

使用可能な DbContext 型を一覧表示します。

### <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext scaffold

スキャフォールディング データベースの DbContext とエンティティの型。

引数:

|               |                                                                     |
| ------------- | ------------------------------------------------------------------- |
| \<接続 > | データベースへの接続文字列。                              |
| \<プロバイダー >   | 使用するプロバイダー。 (例: Microsoft.EntityFrameworkCore.SqlServer) |

オプション:

|                 |                                         |                                                          |
| --------------- | --------------------------------------- | -------------------------------------------------------- |
| <nobr>-d</nobr> |       -データ注釈                | 属性を使用すると、(可能な場合)、モデルを構成できます。 省略すると、fluent API のみが使用されます。 |
|       -c        |       --コンテキスト\<名 >                 | DbContext の名前。                               |
|       -f        |       --強制                           | 既存のファイルを上書きします。                                |
|       -o        |       --出力 dir\<パス >              | ファイルを置くディレクトリです。 パスでは、プロジェクト ディレクトリに対して相対的です。 |
|                 | <nobr>-スキーマ\<SCHEMA_NAME >.</nobr> | エンティティ型を生成するテーブルのスキーマです。      |
|       -t        |       -テーブル\<TABLE_NAME >.          | エンティティ型を生成するテーブル。                 |
|                 |       --データベース名を使用して              | データベースから直接テーブルおよび列名を使用します。   |

### <a name="dotnet-ef-migrations-add"></a>dotnet ef 移行を追加します。

新しい移行を追加します。

引数:

|         |                            |
| ------- | -------------------------- |
| \<名 > | 移行の名前。 |

オプション:

|                 |                                   |                                                                |
| --------------- |---------------------------------- | -------------------------------------------------------------- |
| <nobr>-o</nobr> | <nobr>--出力 dir\<パス ></nobr> | ディレクトリ (およびサブ名前空間) を使用します。 パスでは、プロジェクト ディレクトリに対して相対的です。 既定値は「移行」です。 |

### <a name="dotnet-ef-migrations-list"></a>dotnet ef 移行リスト

使用可能な移行を一覧表示します。

### <a name="dotnet-ef-migrations-remove"></a>dotnet ef 移行を削除します。

前回の移行を削除します。

オプション:

|    |         |                                                                       |
| -- | ------- | --------------------------------------------------------------------- |
| -f | --強制 | 移行をデータベースに適用されているかどうかをチェックしないでください。 |

### <a name="dotnet-ef-migrations-script"></a>dotnet ef 移行スクリプト

移行からの SQL スクリプトを生成します。

引数:

|         |                                                               |
| ------- | ------------------------------------------------------------- |
| \<> | 開始の移行。 既定値は 0 (最初のデータベース)。 |
| \<>   | 終了の移行。 既定値は最後に移行します。         |

オプション:

|    |                  |                                                                    |
| -- | ---------------- | ------------------------------------------------------------------ |
| -o | --出力\<ファイル > | 結果を書き込むファイルです。                                   |
| -i | べき等--     | すべての移行でのデータベースで使用できるスクリプトを生成します。 |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core

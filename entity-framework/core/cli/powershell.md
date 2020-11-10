---
title: EF Core ツールリファレンス (パッケージマネージャーコンソール)-EF Core
description: Entity Framework Core Visual Studio パッケージマネージャーコンソールのリファレンスガイド
author: bricelam
ms.date: 10/27/2020
uid: core/cli/powershell
ms.openlocfilehash: 4a1ab889fc1117b67252ace51fd3df4797b6c8d3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431314"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Entity Framework Core ツールリファレンス-Visual Studio のパッケージマネージャーコンソール

Entity Framework Core 用のパッケージマネージャーコンソール (PMC) ツールは、デザイン時の開発タスクを実行します。 たとえば、既存のデータベースに基づいて、 [移行](/aspnet/core/data/ef-mvc/migrations)を作成し、移行を適用し、モデルのコードを生成します。 コマンドは、Visual Studio 内で [パッケージマネージャーコンソール](/nuget/tools/package-manager-console)を使用して実行されます。 これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。

Visual Studio を使用していない場合は、代わりに [EF Core コマンドラインツール](xref:core/cli/dotnet) を使用することをお勧めします。 .NET Core CLI ツールはクロスプラットフォームで、コマンドプロンプト内で実行されます。

## <a name="installing-the-tools"></a>ツールのインストール

パッケージマネージャーコンソールで次のコマンドを実行して、パッケージマネージャーコンソールツールをインストールし **ます。**

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

**パッケージマネージャーコンソール** で次のコマンドを実行して、ツールを更新します。

```powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>インストール環境を確認する

次のコマンドを実行して、ツールがインストールされていることを確認します。

```powershell
Get-Help about_EntityFrameworkCore
```

出力は次のようになります (使用しているツールのバージョンはわかりません)。

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a>ツールの使用

ツールを使用する前に:

* ターゲットプロジェクトとスタートアッププロジェクトの違いを理解します。
* .NET Standard クラスライブラリでツールを使用する方法について説明します。
* ASP.NET Core プロジェクトの場合は、環境を設定します。

### <a name="target-and-startup-project"></a>ターゲットプロジェクトとスタートアッププロジェクト

これらのコマンドは、 *プロジェクト* と *スタートアッププロジェクト* を参照します。

* *プロジェクト* は *ターゲットプロジェクト* とも呼ばれます。これは、コマンドによってファイルが追加または削除されるためです。 既定では、 **パッケージマネージャーコンソール** で選択された **既定のプロジェクト** はターゲットプロジェクトです。 オプションを使用して、別のプロジェクトをターゲットプロジェクトとして指定でき <nobr>`--project`</nobr> ます。

* *スタートアッププロジェクト* は、ツールをビルドして実行するためのものです。 このツールでは、デザイン時にアプリケーションコードを実行して、プロジェクトに関する情報 (データベース接続文字列やモデルの構成など) を取得する必要があります。 既定では、 **ソリューションエクスプローラー** の **スタートアッププロジェクト** はスタートアッププロジェクトです。 オプションを使用して、別のプロジェクトをスタートアッププロジェクトとして指定でき <nobr>`--startup-project`</nobr> ます。

多くの場合、スタートアッププロジェクトとターゲットプロジェクトは同じプロジェクトです。 個別のプロジェクトである一般的なシナリオは、次のような場合です。

* EF Core コンテキストとエンティティクラスは、.NET Core クラスライブラリにあります。
* .NET Core コンソールアプリまたは web アプリはクラスライブラリを参照します。

また、 [移行コードを EF Core コンテキストとは別のクラスライブラリに配置](xref:core/managing-schemas/migrations/projects)することもできます。

### <a name="other-target-frameworks"></a>その他のターゲットフレームワーク

パッケージマネージャーコンソールツールは、.NET Core または .NET Framework プロジェクトで使用できます。 .NET Standard クラスライブラリに EF Core モデルがあるアプリには、.NET Core または .NET Framework プロジェクトがない可能性があります。 たとえば、これは Xamarin とユニバーサル Windows プラットフォームアプリに当てはまります。 このような場合は、ツールのスタートアッププロジェクトとして機能することだけを目的とした .NET Core または .NET Framework コンソールアプリプロジェクトを作成できます。 プロジェクトは、実際のコードを持たないダミープロジェクトにすることができ &mdash; ます。これは、ツールのターゲットを指定するためにのみ必要です。

ダミープロジェクトが必要な理由 前述のように、ツールはデザイン時にアプリケーションコードを実行する必要があります。 そのためには、.NET Core または .NET Framework ランタイムを使用する必要があります。 EF Core モデルが .NET Core または .NET Framework を対象とするプロジェクト内にある場合、EF Core ツールはプロジェクトからランタイムを借用します。 EF Core モデルが .NET Standard クラスライブラリ内にある場合は、これを行うことはできません。 .NET Standard は実際の .NET 実装ではありません。これは、.NET 実装がサポートする必要のある一連の Api の仕様です。 したがって、EF Core ツールでアプリケーションコードを実行するために .NET Standard は十分ではありません。 スタートアッププロジェクトとして使用するために作成するダミープロジェクトは、ツールが .NET Standard クラスライブラリを読み込むことができる具象ターゲットプラットフォームを提供します。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

ASP.NET Core プロジェクトの [環境](/aspnet/core/fundamentals/environments) を指定するには、コマンドを実行する前に **env: ASPNETCORE_ENVIRONMENT** を設定します。

EF Core 5.0 以降では、追加の引数をプログラムに渡すこともできます。 CreateHostBuilder を使用すると、コマンドラインで環境を指定できます。

```powershell
Update-Database -Args '--environment Production'
```

## <a name="common-parameters"></a>共通パラメーター

次の表に、すべての EF Core コマンドに共通のパラメーターを示します。

| パラメーター                 | 説明                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -コンテキスト \<String>        | 使用する `DbContext` クラス。 クラス名のみ、または名前空間で完全修飾されています。  このパラメーターを省略した場合、EF Core によってコンテキストクラスが検索されます。 複数のコンテキストクラスがある場合は、このパラメーターが必要です。 |
| -Project \<String>        | ターゲットプロジェクト。 このパラメーターを省略すると、 **パッケージマネージャーコンソール** の **既定のプロジェクト** がターゲットプロジェクトとして使用されます。                                                                             |
| <nobr>-Startupproject</nobr>\<String> | スタートアッププロジェクト。 このパラメーターを省略すると、 **ソリューションのプロパティ** の **スタートアッププロジェクト** がターゲットプロジェクトとして使用されます。                                                                                 |
| -Args \<String>           | アプリケーションに渡される引数。 EF Core 5.0 で追加されました。                                                                                                                                                           |
| -Verbose                  | 詳細出力を表示します。                                                                                                                                                                                                 |

コマンドに関するヘルプ情報を表示するには、PowerShell のコマンドを使用し `Get-Help` ます。

> [!TIP]
> Context、Project、および StartupProject の各パラメーターでは、タブの展開がサポートされています。

## <a name="add-migration"></a>Add-Migration

新しい移行を追加します。

パラメーター:

| パラメーター                         | 説明                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name \<String><nobr>       | 移行の名前。 これは位置指定パラメーターであり、必須です。                                              |
| <nobr>-OutputDir \<String></nobr> | ファイルの出力に使用するディレクトリ。 パスは、ターゲットプロジェクトディレクトリに対する相対パスです。 既定値は "移行" です。 |
| <nobr>-名前空間 \<String></nobr> | 生成されたクラスに使用する名前空間。 既定値は、出力ディレクトリから生成されます。 EF Core 5.0 で追加されました。  |

[共通パラメーター](#common-parameters)は上記のとおりです。

## <a name="drop-database"></a>Drop-Database

データベースを削除します。

パラメーター:

| パラメーター | 説明                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | 削除するデータベースを表示しますが、削除はしません。 |

[共通パラメーター](#common-parameters)は上記のとおりです。

## <a name="get-dbcontext"></a>Get-DbContext

使用可能な種類に関する情報を一覧表示して取得し `DbContext` ます。

[共通パラメーター](#common-parameters)は上記のとおりです。

## <a name="get-migration"></a>Get-Migration

使用可能な移行を一覧表示します。 EF Core 5.0 で追加されました。

パラメーター:

| パラメーター                          | 説明                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <nobr>-接続 \<String></nobr> | データベースへの接続文字列。 既定値は、AddDbContext または OnConfiguring で指定されたものです。 |
| -NoConnect                         | データベースに接続しないでください。                                                                         |

[共通パラメーター](#common-parameters)は上記のとおりです。

## <a name="remove-migration"></a>Remove-Migration

最後の移行を削除します (移行のために実行されたコード変更をロールバックします)。

パラメーター:

| パラメーター | 説明                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | 移行を元に戻します (データベースに適用された変更をロールバックします)。 |

[共通パラメーター](#common-parameters)は上記のとおりです。

## <a name="scaffold-dbcontext"></a>Scaffold-DbContext

`DbContext`データベースのおよびエンティティ型のコードを生成します。 で `Scaffold-DbContext` エンティティ型を生成するには、データベーステーブルに主キーが必要です。

パラメーター:

| パラメーター                          | 説明                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-接続 \<String></nobr> | データベースへの接続文字列。 ASP.NET Core 2.x プロジェクトの場合、値には *name = \<name of connection string>* を指定できます。 その場合、プロジェクト用に設定されている構成ソースから名前を取得します。 これは位置指定パラメーターであり、必須です。 |
| <nobr>-プロバイダー \<String></nobr>   | 使用するプロバイダー。 通常、これは NuGet パッケージの名前です (例:) `Microsoft.EntityFrameworkCore.SqlServer` 。 これは位置指定パラメーターであり、必須です。                                                                                           |
| -OutputDir \<String>               | ファイルを格納するディレクトリ。 パスは、プロジェクトディレクトリに対する相対パスです。                                                                                                                                                                                             |
| -ContextDir \<String>              | ファイルを格納するディレクトリ `DbContext` 。 パスは、プロジェクトディレクトリに対する相対パスです。                                                                                                                                                               |
| -名前空間 \<String>               | 生成されたすべてのクラスに使用する名前空間。 既定値は、ルート名前空間と出力ディレクトリから生成されます。 EF Core 5.0 で追加されました。                                                                                                                           |
| -ContextNamespace \<String>        | 生成されたクラスに使用する名前空間 `DbContext` 。 注: はオーバーライドさ `-Namespace` れます。 EF Core 5.0 で追加されました。                                                                                                                                                           |
| -コンテキスト \<String>                 | `DbContext`生成するクラスの名前。                                                                                                                                                                                                                          |
| -スキーマ \<String[]>               | エンティティ型を生成するテーブルのスキーマ。 このパラメーターを省略すると、すべてのスキーマが含まれます。                                                                                                                                                             |
| -テーブル \<String[]>                | エンティティ型を生成するテーブル。 このパラメーターを省略すると、すべてのテーブルが含まれます。                                                                                                                                                                         |
| -DataAnnotations                   | 属性を使用してモデルを構成します (可能な場合)。 このパラメーターを省略した場合は、fluent API のみが使用されます。                                                                                                                                                      |
| -UseDatabaseNames 場合                  | テーブル名と列名は、データベースに表示されるとおりに使用します。 このパラメーターを省略した場合、データベース名は、C# の名前のスタイル規則により厳密に準拠するように変更されます。                                                                                       |
| -Force                             | 既存のファイルを上書きします。                                                                                                                                                                                                                                               |
| -NoOnConfiguring                   | 生成しません `DbContext.OnConfiguring` 。 EF Core 5.0 で追加されました。                                                                                                                                                                                                         |
| -NoPluralize                       | プルーラライザーは使用しないでください。 EF Core 5.0 で追加されました。                                                                                                                                                                                                                         |

[共通パラメーター](#common-parameters)は上記のとおりです。

例:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

選択されたテーブルのみをスキャフォールディングし、指定された名前と名前空間を持つ別のフォルダーにコンテキストを作成する例を次に示します。

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

次の例では、 [共有マネージャーツール](/aspnet/core/security/app-secrets#secret-manager)を使用して、プロジェクトの構成から接続文字列を読み取ります。

```powershell
Scaffold-DbContext "Name=ConnectionStrings.Blogging" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="script-dbcontext"></a>Script-DbContext

DbContext から SQL スクリプトを生成します。 すべての移行をバイパスします。 EF Core 3.0 で追加されました。

パラメーター:

| パラメーター                      | 説明                      |
| ------------------------------ | -------------------------------- |
| <nobr>-出力 \<String></nobr> | 結果の書き込み先のファイル。 |

[共通パラメーター](#common-parameters)は上記のとおりです。

## <a name="script-migration"></a>Script-Migration

選択した移行のすべての変更を、選択した別の移行に適用する SQL スクリプトを生成します。

パラメーター:

| パラメーター                    | 説明                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-From*\<String>            | 移行を開始しています。 移行は、名前または ID で識別できます。 数値0は、 *最初の移行の前に* 特別なケースです。 既定値は 0 です。                                                              |
| *-To*\<String>              | 移行を終了しています。 既定では、最後の移行になります。                                                                                                                                                                      |
| -べき等                  | 任意の移行時にデータベースで使用できるスクリプトを生成します。                                                                                                                                                         |
| <nobr>-NoTransactions</nobr> | SQL トランザクションステートメントを生成しません。 EF Core 5.0 で追加されました。                                                                                                                                                           |
| -出力 \<String>            | 結果の書き込み先のファイル。 このパラメーターを省略すると、 */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* のように、アプリのランタイムファイルが作成されたときと同じフォルダーに生成された名前でファイルが作成されます。 |

[共通パラメーター](#common-parameters)は上記のとおりです。

> [!TIP]
> To、From、および Output パラメーターでは、タブの拡張がサポートされています。

次の例では、移行名を使用して、(移行のないデータベースから) InitialCreate 移行用のスクリプトを作成します。

```powershell
Script-Migration 0 InitialCreate
```

次の例では、移行 ID を使用して、InitialCreate 移行後のすべての移行用のスクリプトを作成します。

```powershell
Script-Migration 20180904195021_InitialCreate
```

## <a name="update-database"></a>Update-Database

最後に移行したデータベース、または指定した移行にデータベースを更新します。

| パラメーター                           | 説明                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-移行*\<String></nobr> | ターゲットの移行。 移行は、名前または ID で識別できます。 数値0は、 *最初の移行の前に* 特別なケースであり、すべての移行が元に戻されます。 移行が指定されていない場合、コマンドは既定で最後の移行になります。 |
| <nobr>-接続 \<String></nobr>  | データベースへの接続文字列。 既定値は、またはで指定されたもの `AddDbContext` `OnConfiguring` です。 EF Core 5.0 で追加されました。                                                                                                                                |

[共通パラメーター](#common-parameters)は上記のとおりです。

> [!TIP]
> 移行パラメーターでは、タブの拡張がサポートされています。

次の例では、すべての移行を元に戻します。

```powershell
Update-Database 0
```

次の例では、指定された移行にデータベースを更新します。 最初のは移行名を使用し、2つ目のは移行 ID と指定された接続を使用します。

```powershell
Update-Database InitialCreate
Update-Database 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a>その他のリソース

* [移行](xref:core/managing-schemas/migrations/index)
* [リバースエンジニアリング](xref:core/managing-schemas/scaffolding)

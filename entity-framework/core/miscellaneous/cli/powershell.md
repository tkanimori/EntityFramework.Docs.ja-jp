---
title: EF Core ツール リファレンス (パッケージ マネージャー コンソール) - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: db4d89b6a0babe01bccbeadc51381a309ad8ca0f
ms.sourcegitcommit: c568d33214fc25c76e02c8529a29da7a356b37b4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2018
ms.locfileid: "47459562"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Entity Framework Core ツールのリファレンス - Visual Studio でパッケージ マネージャー コンソール

Entity Framework Core のパッケージ マネージャー コンソール (PMC) ツールでは、デザイン時開発タスクを実行します。 たとえば、作成[移行](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations)移行を適用し、既存のデータベースに基づくモデルのコードを生成します。 コマンドは、Visual Studio を使用して内部で実行、[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)します。 これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。

Visual Studio を使用していないかどうか、お勧め、 [EF Core コマンド ライン ツール](dotnet.md)代わりにします。 CLI ツールは、クロス プラットフォームと、コマンド プロンプト内で実行されます。

## <a name="installing-the-tools"></a>ツールのインストール

インストールして、ツールを更新する手順は、ASP.NET Core 2.1 以降と以前のバージョンまたはその他のプロジェクトの種類によって異なります。

### <a name="aspnet-core-version-21-and-later"></a>ASP.NET Core 2.1 以降のバージョン

ユーザーがためにで、ツールは、ASP.NET Core 2.1 以降のプロジェクトに自動的に含め、`Microsoft.EntityFrameworkCore.Tools`にパッケージが含まれる、 [Microsoft.AspNetCore.App メタパッケージ](/aspnet/core/fundamentals/metapackage-app)します。

そのため、ツールをインストールすることが何もする必要はありませんを行うこと。
* 新しいプロジェクトのツールを使用する前にパッケージを復元します。
* 新しいバージョンにツールを更新するパッケージをインストールします。

ツールの最新バージョンを取得していることを確認するには、次の手順を実行することお勧めします。

* 編集、 *.csproj*ファイルを開き、最新バージョンを指定する行を追加、 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)パッケージ。 たとえば、 *.csproj*ファイルを含めることができます、`ItemGroup`次のようにします。

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

次の例のようなメッセージが表示された場合は、ツールを更新します。

> EF Core ツールのバージョン '2.1.1-rtm-30846' は、'2.1.3-rtm-32065' ランタイムのより古いです。 最新の機能とバグ修正するためのツールを更新します。

ツールの更新。
* 最新の .NET Core SDK をインストールします。
* Visual Studio を最新バージョンに更新します。
* 編集、 *.csproj*ファイルの最新のツール パッケージにパッケージ参照が含まれるように前述のようにします。

### <a name="other-versions-and-project-types"></a>他のバージョンおよびプロジェクトの種類

次のコマンドを実行して、パッケージ マネージャー コンソールのツールをインストール**パッケージ マネージャー コンソール**:

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

次のコマンドを実行してツールを更新する**パッケージ マネージャー コンソール**します。

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>インストールを確認します。

このコマンドを実行して、ツールがインストールされていることを確認します。

``` powershell
Get-Help about_EntityFrameworkCore
```

出力は、(、しないことがわかるツールを使用しているのバージョン) を次のようになります。

```console

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

## <a name="using-the-tools"></a>ツールを使用します。

前に、ツールを使用します。
* ターゲットとスタートアップ プロジェクトの違いを理解します。
* .NET Standard クラス ライブラリで、ツールを使用する方法について説明します。
* ASP.NET Core プロジェクトでは、環境を設定します。

### <a name="target-and-startup-project"></a>ターゲットとスタートアップ プロジェクト

コマンドを参照してください、*プロジェクト*と*スタートアップ プロジェクト*します。

* *プロジェクト*とも呼ばれますが、*ターゲット プロジェクト*コマンドが追加または、ファイルを削除であるためです。 既定では、**既定のプロジェクト**で選択した**パッケージ マネージャー コンソール**ターゲット プロジェクトです。 ターゲット プロジェクトとして使用して、別のプロジェクトを指定することができます、 <nobr> `--project` </nobr>オプション。

* *スタートアップ プロジェクト*ツールをビルドして実行されます。 ツールは、データベース接続文字列と、モデルの構成など、プロジェクトに関する情報を取得するデザイン時にアプリケーション コードを実行する必要があります。 既定では、**スタートアップ プロジェクト**で**ソリューション エクスプ ローラー**はスタートアップ プロジェクトです。 使用して、別のプロジェクトをスタートアップ プロジェクトとして指定できます、 <nobr> `--startup-project` </nobr>オプション。

スタートアップ プロジェクトとターゲット プロジェクトは、同じプロジェクトでは多くの場合です。 個別のプロジェクトが、一般的なシナリオは。

* EF Core コンテキストとエンティティ クラスは、.NET Core クラス ライブラリでは。
* .NET Core コンソール アプリまたは web アプリは、クラス ライブラリを参照します。

することも[EF Core コンテキストから別のクラス ライブラリにコードを移行配置](xref:core/managing-schemas/migrations/projects)します。

### <a name="other-target-frameworks"></a>その他のターゲット フレームワーク

パッケージ マネージャー コンソール ツールは、.NET Core または .NET Framework プロジェクトで動作します。 .NET Standard クラス ライブラリで、EF Core モデルを必要とするアプリケーションは、.NET Core または .NET Framework プロジェクトがあります。 たとえば、これは、Xamarin とユニバーサル Windows プラットフォーム アプリの場合は true。 このような場合は、のみを目的が、ツールのスタートアップ プロジェクトとして機能するには、.NET Core または .NET Framework コンソール アプリケーション プロジェクトを作成できます。 プロジェクトは、実際のコードなしでダミー プロジェクト&mdash;ターゲット ツールを提供する必要があるだけです。

必要なダミー プロジェクトはなぜですか。 前述のように、ツールは、デザイン時にアプリケーション コードを実行する必要があります。 そのためには、.NET Core または .NET Framework のランタイムを使用して、必要があります。 EF Core モデルは、.NET Core または .NET Framework を対象とするプロジェクトでは、EF Core ツールは、プロジェクトからランタイムを借用します。 EF Core モデルが .NET Standard クラス ライブラリの場合、インストールできません。 .NET Standard は、実際の .NET 実装ではありません。一連の .NET 実装をサポートする必要がある Api の仕様です。 したがって .NET Standard はアプリケーション コードを実行する EF Core ツールのための十分です。 スタートアップ プロジェクトとして使用するように作成するダミーのプロジェクトでは、ツールが、.NET Standard クラス ライブラリを読み込むことができます、具体的なターゲット プラットフォームを提供します。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

ASP.NET Core プロジェクト用の環境を指定するには、次のように設定します。 **env:ASPNETCORE_ENVIRONMENT**コマンドを実行する前にします。

## <a name="common-parameters"></a>共通パラメーター

次の表は、EF Core のコマンドのすべてに共通するパラメーターを示しています。

| パラメーター                 | 説明                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Context \<String>        | `DbContext`クラスを使用します。 クラス名のみ、または名前空間を持つ完全修飾します。  このパラメーターを省略した場合、EF Core は、コンテキスト クラスを検索します。 複数のコンテキスト クラスがある場合は、このパラメーターが必要です。 |
| -Project \<String>        | ターゲットのプロジェクトです。 このパラメーターを省略した場合、**既定のプロジェクト**の**パッケージ マネージャー コンソール**がターゲット プロジェクトとして使用されます。                                                                             |
| -StartupProject \<String> | スタートアップ プロジェクトです。 このパラメーターを省略した場合、**スタートアップ プロジェクト**で**ソリューションのプロパティ**ターゲット プロジェクトとして使用されます。                                                                                 |
| -Verbose                  | 詳細な出力を表示します。                                                                                                                                                                                                 |

コマンドに関するヘルプ情報を表示するには、PowerShell を使用して`Get-Help`コマンド。

> [!TIP]
> コンテキスト、プロジェクト、およびスタートアップ プロジェクトのパラメーターでは、タブ拡張をサポートします。

## <a name="add-migration"></a>追加の移行

新しい移行を追加します。

パラメーター:

| パラメーター                         | 説明                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name\<文字列 ><nobr>       | 移行の名前。 位置指定パラメーターは、これが必要です。                                              |
| <nobr>-OutputDir\<文字列 ></nobr> | 使用するディレクトリ (およびサブ名前空間)。 パスでは、ターゲット プロジェクトのディレクトリに対して相対的です。 既定値は「移行」です。 |

## <a name="drop-database"></a>データベースの削除

データベースを削除します。

パラメーター:

| パラメーター | 説明                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | 表示するデータベースが削除されます。 は削除しないでください。 |

## <a name="get-dbcontext"></a>Get-DbContext

使用可能な`DbContext`型。

## <a name="remove-migration"></a>Remove-Migration

最後の移行 (ロールバックの移行の実行されたコードの変更) を削除します。

パラメーター:

| パラメーター | 説明                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | 移行を元に戻す (ロールバックをデータベースに適用された変更)。 |

## <a name="scaffold-dbcontext"></a>Scaffold-dbcontext

コードを生成、`DbContext`とデータベースのエンティティ型。

パラメーター:

| パラメーター                          | 説明                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>の接続\<文字列 ></nobr> | データベースへの接続文字列。 ASP.NET Core 2.x プロジェクトの値を指定できます*名 =\<接続文字列の名前 >* します。 その場合は、名前は、プロジェクトに設定されている構成ソースから取得されます。 位置指定パラメーターは、これが必要です。 |
| <nobr>プロバイダー\<文字列 ></nobr>   | 使用するプロバイダー。 たとえば、NuGet パッケージの名前は、この通常:`Microsoft.EntityFrameworkCore.SqlServer`します。 位置指定パラメーターは、これが必要です。                                                                                           |
| -OutputDir\<文字列 >               | ファイルを配置するディレクトリ。 パスでは、プロジェクト ディレクトリに対して相対的です。                                                                                                                                                                                             |
| -ContextDir\<文字列 >              | 格納するディレクトリ、`DbContext`ファイルします。 パスでは、プロジェクト ディレクトリに対して相対的です。                                                                                                                                                                              |
| -Context \<String>                 | 名前、`DbContext`クラスを生成します。                                                                                                                                                                                                                          |
| -Schemas \<String[]>               | エンティティ型を生成するテーブルのスキーマです。 このパラメーターを省略すると、すべてのスキーマが含まれます。                                                                                                                                                             |
| -Tables \<String[]>                | エンティティ型を生成するテーブル。 このパラメーターを省略すると、すべてのテーブルが含まれます。                                                                                                                                                                         |
| -DataAnnotations                   | 属性を使用すると、可能な限り、モデルを構成できます。 このパラメーターを省略した場合は、fluent API のみが使用されます。                                                                                                                                                      |
| -UseDatabaseNames                  | データベースに表示されているとおりに、テーブルおよび列名を使用します。 このパラメーターを省略すると、データベース名は、c# 名前表記規則をより厳密に準拠するように変更されます。                                                                                       |
| -Force                             | 既存のファイルを上書きします。                                                                                                                                                                                                                                               |

例:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

選択したテーブルのみをスキャフォールディングし、指定した名前の別のフォルダーにコンテキストを作成する例:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext
```

## <a name="script-migration"></a>スクリプトの移行

すべての変更を 1 つの選択された移行から別の選択された移行に適用される SQL スクリプトを生成します。

パラメーター:

| パラメーター                | 説明                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-から*\<文字列 >        | 開始の移行。 移行は名前または ID で識別される可能性があります。 数値 0 は特殊なケースでは意味*最初の移行の前に*します。 既定値は 0 です。                                                              |
| *-* \<文字列 >          | 終了の移行。 移行の最後の既定値します。                                                                                                                                                                      |
| <nobr>-べき等であります。</nobr> | すべての移行でのデータベースで使用できるスクリプトを生成します。                                                                                                                                                         |
| -出力\<文字列 >        | 結果を書き込むファイル。 たとえば、アプリのランタイム ファイルが作成されると、同じフォルダーに生成された名前でファイルを作成するこのパラメーターを省略すると、: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* します。 |

> [!TIP]
> および出力パラメーターは、タブ拡張をサポートします。

次の例では、移行の名前を使用して、InitialCreate 移行用のスクリプトを作成します。

```powershell
Script-Migration -To InitialCreate
```

次の例では、移行 ID を使用して、InitialCreate 移行後のすべての移行スクリプトを作成します

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>データベースの更新

最後に移行したり、指定された移行は、データベースを更新します。

| パラメーター                           | 説明                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-移行*\<文字列 ></nobr> | Target の移行。 移行は名前または ID で識別される可能性があります。 数値 0 は特殊なケースでは意味*最初の移行の前に*と、すべての移行を元に戻されます。 移行が指定されていない場合のコマンドは、既定最後の移行になります。 |

> [!TIP]
> 移行のパラメーターは、タブ拡張をサポートします。

次の例では、すべての移行を元に戻します。

```powershell
Update-Database -Migration 0
```

次の例では、指定された移行するデータベースを更新します。 最初の移行の名前を使用し、2 つ目は移行 ID を使用します。

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate
```

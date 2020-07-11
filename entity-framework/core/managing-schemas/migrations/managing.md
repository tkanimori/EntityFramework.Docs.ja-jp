---
title: 移行の管理-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: e52d3680360a1e83e05f04650c735c5a67680094
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238730"
---
# <a name="managing-migrations"></a>移行の管理

モデルが変更されると、通常の開発の一環として移行が追加および削除され、移行ファイルがプロジェクトのソース管理にチェックインされます。 移行を管理するには、まず[EF Core コマンドラインツール](xref:core/miscellaneous/cli/index)をインストールする必要があります。

> [!TIP]
> `DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。

## <a name="add-a-migration"></a>移行を追加する

モデルが変更されたら、その変更の移行を追加できます。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

移行名は、バージョン管理システムのコミット メッセージのように使用できます。 たとえば、変更がエンティティの新しいプロパティである場合、 *Addブログ*のような名前を選択でき `CreatedTimestamp` `Blog` ます。

**[移行]** ディレクトリの下で 3 つのファイルがプロジェクトに追加されます。

* **XXXXXXXXXXXXXX_AddCreatedTimestamp**、メインの移行ファイルです。 (`Up` で) 移行を適用し、(`Down` で) それを元に戻すために必要な操作が含まれます。
* **XXXXXXXXXXXXXX_AddCreatedTimestamp**、移行メタデータファイルです。 EF によって使用される情報が含まれます。
* **MyContextModelSnapshot.cs**--現在のモデルのスナップショット。 次の移行を追加するときの変更内容の決定に使用されます。

変更の進行がわかるように、ファイル名のタイムスタンプは時系列順で維持されます。

### <a name="namespaces"></a>名前空間

移行ファイルは自由に移動し、手動で名前空間を変更できます。 新しい移行は前回の移行の兄弟として作成されます。 または、次のように、生成時に名前空間を指定することもできます。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a>移行コードをカスタマイズする

EF Core 通常は正確な移行を作成しますが、コードを常に確認し、必要な変更に対応していることを確認してください。場合によっては、これを行う必要もあります。

### <a name="column-renames"></a>列名の変更

移行のカスタマイズが必要な例の1つは、プロパティの名前を変更する場合です。 たとえば、プロパティの名前をからに変更すると `Name` `FullName` 、EF Core によって次の移行が生成されます。

```c#
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

EF Core は、通常、列を削除し、新しい列 (2 つの異なる変更) を作成し、列の名前を変更する必要があるかどうかを知ることができません。 上記の移行がそのように適用されている場合、顧客名はすべて失われます。 列の名前を変更するには、上記の生成された移行を次のように置き換えます。

```c#
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> 移行のスキャフォールディング手順では、(列の削除など) データが失われる場合に、警告が出ます。 その警告が表示されたら、移行コードが正しいことを特に確認してください。

### <a name="adding-raw-sql"></a>生の SQL の追加

列の名前の変更は、組み込みの API を使用して行うことができますが、多くの場合、可能ではありません。 たとえば、既存の `FirstName` `LastColumn` プロパティとプロパティを1つの新しいプロパティに置き換えることができ `FullName` ます。 EF Core によって生成される移行は、次のようになります。

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

以前と同様に、望ましくないデータ損失が発生します。 古い列からデータを転送するには、次のように移行を再配置し、生の SQL 操作を導入します。

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a>未加工の SQL を使用した任意の変更

生の SQL を使用すると、EF Core 認識されないデータベースオブジェクトを管理することもできます。 これを行うには、モデルを変更せずに移行を追加します。空の移行が生成されます。その後、生の SQL 操作を設定できます。

たとえば、次の移行では、SQL Server のストアドプロシージャが作成されます。

```c#
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

これを使用すると、次のようなデータベースのあらゆる側面を管理できます。

* ストアド プロシージャ
* フルテキスト検索
* 関数
* トリガー
* Views

ほとんどの場合、移行を適用すると、EF Core によって各移行が独自のトランザクションで自動的にラップされます。 残念ながら、一部のデータベースでは、一部の移行操作をトランザクション内で実行することはできません。このような場合は、に渡すことによってトランザクションをオプトアウトすることができ `suppressTransaction: true` `migrationBuilder.Sql` ます。

`DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。

## <a name="remove-a-migration"></a>移行を削除する

移行の追加後、適用する前に EF Core モデルの追加変更が必要なことに気付く場合があります。 最後の移行を削除するには、このコマンドを使用します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Remove-Migration
```

***

移行の削除後、追加のモデル変更を行い、もう一度追加できます。

> [!WARNING]
> 実稼働データベースに既に適用されている移行は削除しないように注意してください。 そうしないと、それを元に戻すことができなくなり、その後の移行によって想定される内容が壊れる可能性があります。

## <a name="listing-migrations"></a>移行の一覧表示

次のように、すべての既存の移行を一覧表示できます。

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a>すべての移行をリセットしています

極端なケースでは、すべての移行を削除してからやり直すことが必要になる場合があります。 これは、**移行**フォルダーを削除して、データベースを削除することで簡単に行うことができます。この時点で、新しい初期移行を作成できます。これには、現在のスキーマ全体が含まれます。

すべての移行をリセットし、データを失うことなく1つの移行を作成することもできます。 これは "スカッシュ" と呼ばれることもあり、手動での作業が必要です。

* **マイグレーション**フォルダーの削除
* 新しい移行を作成し、その移行用の SQL スクリプトを生成する
* データベースで、移行履歴テーブルからすべての行を削除します。
* 1つの行を移行履歴に挿入して、最初の移行が既に適用されていることを記録します。これは、テーブルが既に存在しているためです。 Insert SEQL は、上記で生成された SQL スクリプトの最後の操作です。

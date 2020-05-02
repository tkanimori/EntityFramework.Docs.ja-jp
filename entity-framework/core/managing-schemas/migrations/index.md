---
title: 移行 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 99bb420d95cb86443b63ba05ce9e6b4ab838eff9
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538450"
---
# <a name="migrations"></a>移行

データ モデルは開発中に変更され、データベースと同期しなくなります。 データベースを削除して、モデルと一致する新しいものを EF に作成させることもできますが、この手順ではデータが失われてしまいます。 EF Core の移行機能では、データベースの既存のデータを維持しながら、アプリケーションのデータ モデルとデータベース スキーマを同期した状態で、データベース スキーマを増分的に更新することができます。

移行には、次のタスクの助けとなるコマンドライン ツールと API が含まれています。

* [移行を作成します](#create-a-migration)。 データベースを更新できるモデルの変更のセットと同期できるコードを生成します。
* [データベースを更新します](#update-the-database)。 データベース スキーマを更新し、保留中となって移行を適用します。
* [移行コードをカスタマイズします](#customize-migration-code)。 生成されたコードをときどき変更したり補完したりする必要があります。
* [移行を削除します](#remove-a-migration)。 生成されたコードを削除します。
* [移行を元に戻します](#revert-a-migration)。 データベースの変更をやり直します。
* [SQL スクリプトを生成します](#generate-sql-scripts)。 実稼働データベースを更新したり、移行コードをトラブルシューティングしたりするためにスクリプトが必要な場合があります。
* [実行時に移行を適用します](#apply-migrations-at-runtime)。 デザイン時の更新やスクリプトの実行が最適なオプションでない場合、`Migrate()` メソッドを呼び出します。

> [!TIP]
> `DbContext` がスタートアップ プロジェクトとは異なるアセンブリに含まれている場合、ターゲットとスタートアップ プロジェクトは[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell#target-and-startup-project)または [.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)のいずれかに明示的に指定できます。

## <a name="install-the-tools"></a>ツールのインストール

[コマンドライン ツール](xref:core/miscellaneous/cli/index)をインストールします。

* Visual Studio では、[パッケージ マネージャー コンソール ツール](xref:core/miscellaneous/cli/powershell)をお勧めします。
* その他の開発環境では、[.NET Core CLI ツール](xref:core/miscellaneous/cli/dotnet)を選択します。

## <a name="create-a-migration"></a>移行を作成する

[最初のモデルを定義](xref:core/modeling/index)したら、次にデータベースを作成します。 最初の移行を追加するには、次のコマンドを実行します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

**[移行]** ディレクトリの下で 3 つのファイルがプロジェクトに追加されます。

* **XXXXXXXXXXXXXX_InitialCreate.cs**--メインの移行ファイル。 (`Up()` で) 移行を適用し、(`Down()` で) それを元に戻すために必要な操作が含まれます。
* **XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--移行メタデータ ファイル。 EF によって使用される情報が含まれます。
* **MyContextModelSnapshot.cs**--現在のモデルのスナップショット。 次の移行を追加するときの変更内容の決定に使用されます。

変更の進行がわかるように、ファイル名のタイムスタンプは時系列順で維持されます。

> [!TIP]
> 移行ファイルは自由に移動し、手動で名前空間を変更できます。 新しい移行は前回の移行の兄弟として作成されます。
> 
> また、`-Namespace` (パッケージ マネージャー コンソール) または `--namespace` (.NET Core CLI) を使用して、生成時に名前空間を指定することもできます。
> ### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)
> 
> ```dotnetcli
> dotnet ef migrations add InitialCreate --namespace Your.Namespace
> ```
> 
> ### <a name="visual-studio"></a>[Visual Studio](#tab/vs)
> 
> ``` powershell
> Add-Migration InitialCreate -Namespace Your.Namespace
> ```

## <a name="update-the-database"></a>データベースを更新する

次に、移行をデータベースに適用し、スキーマを作成します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

## <a name="customize-migration-code"></a>移行コードをカスタマイズする

EF Core モデルの変更後、データベース スキーマは同期していない状態になります。それを最新の状態にするには、別の移行を追加します。 移行名は、バージョン管理システムのコミット メッセージのように使用できます。 たとえば、変更するのがレビュー用の新しいエンティティ クラスである場合、*AddProductReviews* などの名前を選択します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddProductReviews
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddProductReviews
```

***

移行が (それのためにコードが生成され) スキャフォールディングされたら、コードが正しいか確認し、それを正しく適用するために必要な任意の操作を追加、削除または変更します。

たとえば、移行には次の操作が含まれることがあります。

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

これらの操作はデータベース スキーマに互換性を与えますが、既存の顧客名を保持しません。 改善するには、次のように書き直します。

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

> [!TIP]
> 移行のスキャフォールディング手順では、(列の削除など) データが失われる場合に、警告が出ます。 その警告が表示されたら、移行コードが正しいことを特に確認してください。

適切なコマンドを利用し、データベースに移行を適用します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

### <a name="empty-migrations"></a>空の移行

モデル変更を行わずに移行を追加すると便利な場合があります。 この場合、新しい移行を追加すると、空のクラスのコード ファイルが作成されます。 EF Core モデルに直接関連しない操作を実行するようにこの移行をカスタマイズできます。 この方法で管理すると便利なものは次のとおりです。

* フルテキスト検索
* 関数
* ストアド プロシージャ
* トリガー
* Views

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

## <a name="revert-a-migration"></a>移行を元に戻す

移行をデータベースに既に適用しているが、元に戻す必要がある場合、同じコマンドを使用して移行を適用できますが、ロールバックする移行の名前を指定します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update LastGoodMigration
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database LastGoodMigration
```

***

## <a name="generate-sql-scripts"></a>SQL スクリプトを生成する

移行をデバッグするか、それを実稼働データベースに展開するとき、SQL スクリプトを生成すると便利です。 このスクリプトはさらに見直して正しいかどうかを確認し、実稼働データベースのニーズに合わせて調整できます。 このスクリプトは、展開テクノロジとの連動でも利用できます。 基本コマンドは次のとおりです。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>基本的な使用方法
```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>From を使用する (暗黙的な指定に対して)
これにより、この移行から最新の移行までの SQL スクリプトが生成されます。
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a>From および To を使用する
これにより、`from` 移行から、指定された `to` までの SQL スクリプトが生成されます。
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。 *データ損失の可能性のあるシナリオには注意してください。*

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>基本的な使用方法
``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>From を使用する (暗黙的な指定に対して)
これにより、この移行から最新の移行までの SQL スクリプトが生成されます。
```powershell
Script-Migration 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a>From および To を使用する
これにより、`from` 移行から、指定された `to` までの SQL スクリプトが生成されます。
```powershell
Script-Migration 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
ロールバック スクリプトを生成するために、`to` より新しい `from` を使用することができます。 *データ損失の可能性のあるシナリオには注意してください。*

***

このコマンドにはいくつかのオプションがあります。

**from** 移行は、スクリプトの実行前にデータベースに適用される最後の移行にする必要があります。 移行が適用されていない場合、`0` を指定します (これは既定です)。

**to** 移行は、スクリプトの実行後にデータベースに適用される最後の移行です。 これは既定でプロジェクトの最後の移行になります。

**idempotent** スクリプトをオプションで生成できます。 このスクリプトは、データベースにまだ適用されていない移行のみを適用します。 これは、データベースに適用された最後の移行が正確にわからない場合、あるいは複数のデータベースに展開するとき、いずれも移行が異なる可能性がある場合に便利です。

## <a name="apply-migrations-at-runtime"></a>実行時に移行を適用する

起動中または最初の実行中、実行時に移行を適用するアプリがあります。 `Migrate()` メソッドを使用してこれを行います。

このメソッドは、より高度なシナリオで利用される `IMigrator` サービスの上でビルドされます。 アクセスするには `myDbContext.GetInfrastructure().GetService<IMigrator>()` を利用します。

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * この方法は万人向けではありません。 ローカル データベースを利用するアプリには最適ですが、ほとんどのアプリケーションでは、SQL スクリプトの生成など、より堅牢な展開戦略が必要になります。
> * `Migrate()` の前に `EnsureCreated()` を呼び出さないでください。 `EnsureCreated()` は移行をバイパスしてスキーマを作成し、`Migrate()` が失敗します。

## <a name="next-steps"></a>次の手順

詳細については、「<xref:core/miscellaneous/cli/index>」を参照してください。

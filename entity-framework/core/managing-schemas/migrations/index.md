---
title: 移行 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: dd164125c053497af94773011127853ad10d27a6
ms.sourcegitcommit: 72e59e6af86b568653e1b29727529dfd7f65d312
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34754510"
---
<a name="migrations"></a>移行
==========
移行とはデータベースに対するスキーマ変更の適用を増分的に増やす方法であり、データベースの既存データを維持しながら、EF Core モデルと同期します。

<a name="creating-the-database"></a>データベースを作成する
---------------------
[最初のモデルを定義][1]したら、次にデータベースを作成します。 これを行うには、初期移行を追加します。
[EF Core ツール][2]をインストールし、適切なコマンドを実行します。

``` powershell
Add-Migration InitialCreate
```
``` Console
dotnet ef migrations add InitialCreate
```

**[移行]** ディレクトリの下で 3 つのファイルがプロジェクトに追加されます。

* **00000000000000_InitialCreate.cs**--メインの移行ファイル。 (`Up()` で) 移行を適用し、(`Down()` で) それを元に戻すために必要な操作が含まれます。
* **00000000000000_InitialCreate.Designer.cs**--移行メタデータ ファイル。 EF によって使用される情報が含まれます。
* **MyContextModelSnapshot.cs**--現在のモデルのスナップショット。 次の移行を追加するときの変更内容の決定に使用されます。

変更の進行がわかるように、ファイル名のタイムスタンプは時系列順で維持されます。

> [!TIP]
> 移行ファイルは自由に移動したり、その名前空間を変更したりできます。 新しい移行は前回の移行の兄弟として作成されます。

次に、移行をデータベースに適用し、スキーマを作成します。

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="adding-another-migration"></a>別の移行を追加する
------------------------
EF Core モデルの変更後、データベース スキーマは同期していない状態になります。それを最新の状態にするには、別の移行を追加します。 移行名は、バージョン管理システムのコミット メッセージのように使用できます。 たとえば、製品の顧客レビューを変更して保存した場合、*AddProductReviews* のようなものを選択できます。

``` powershell
Add-Migration AddProductReviews
```
``` Console
dotnet ef migrations add AddProductReviews
```

移行のスキャフォールディング後、それが正確であるか確認し、追加の操作が必要であればそれを追加し、正しく適用します。 たとえば、移行には次の操作が含まれることがあります。

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
> データ損失 (列の脱落など) を引き起こす可能性のある操作がスキャフォールディングされるときは、新しい移行を追加すると警告が出ます。 このような移行の場合は特に正しいかどうかを確認してください。

適切なコマンドを利用し、データベースに移行を適用します。

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="removing-a-migration"></a>移行を削除する
--------------------
移行の追加後、適用する前に EF Core モデルの追加変更が必要なことに気付く場合があります。
最後の移行を削除するには、このコマンドを使用します。

``` powershell
Remove-Migration
```
``` Console
dotnet ef migrations remove
```

削除後、追加のモデル変更を行い、もう一度追加できます。

<a name="reverting-a-migration"></a>移行を元に戻す
---------------------
移行をデータベースに既に適用しているが、元に戻す必要がある場合、同じコマンドを使用して移行を適用できますが、ロールバックする移行の名前を指定します。

``` powershell
Update-Database LastGoodMigration
```
``` Console
dotnet ef database update LastGoodMigration
```

<a name="empty-migrations"></a>空の移行
----------------
モデル変更を行わずに移行を追加すると便利な場合があります。 その場合、新しい移行を追加すると空の移行が作成されます。 EF Core モデルに直接関連しない操作を実行するようにこの移行をカスタマイズできます。
この方法で管理すると便利なものは次のとおりです。

* フルテキスト検索
* 関数
* ストアド プロシージャ
* トリガー
* Views
* その他

<a name="generating-a-sql-script"></a>SQL スクリプトを生成する
-----------------------
移行をデバッグするか、それを実稼働データベースに展開するとき、SQL スクリプトを生成すると便利です。 このスクリプトはさらに見直して正しいかどうかを確認し、実稼働データベースのニーズに合わせて調整できます。 このスクリプトは、展開テクノロジとの連動でも利用できます。 基本コマンドは次のとおりです。

``` powershell
Script-Migration
```
``` Console
dotnet ef migrations script
```

このコマンドにはいくつかのオプションがあります。

**from** 移行は、スクリプトの実行前にデータベースに適用される最後の移行にする必要があります。 移行が適用されていない場合、`0` を指定します (これは既定です)。

**to** 移行は、スクリプトの実行後にデータベースに適用される最後の移行です。 これは既定でプロジェクトの最後の移行になります。

**idempotent** スクリプトをオプションで生成できます。 このスクリプトは、データベースにまだ適用されていない移行のみを適用します。 これは、データベースに適用された最後の移行が正確にわからない場合、あるいは複数のデータベースに展開するとき、いずれも移行が異なる可能性がある場合に便利です。

<a name="applying-migrations-at-runtime"></a>実行時に移行を適用する
------------------------------
起動中または最初の実行中、実行時に移行を適用するアプリがあります。 `Migrate()` メソッドを使用してこれを行います。

この方法は万人向けではないため、注意が必要です。 ローカル データベースを利用するアプリには最適ですが、ほとんどのアプリケーションでは、SQL スクリプトの生成など、より堅牢な展開戦略が必要になります。

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
> `Migrate()` の前に `EnsureCreated()` を呼び出さないでください。 `EnsureCreated()` は移行をバイパスしてスキーマを作成し、`Migrate()` が失敗します。

> [!NOTE]
> このメソッドは、より高度なシナリオで利用される `IMigrator` サービスの上でビルドされます。 アクセスするには `DbContext.GetService<IMigrator>()` を利用します。


  [1]: ../../modeling/index.md
  [2]: ../../miscellaneous/cli/index.md

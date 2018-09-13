---
title: 複数のプロバイダー - EF Core での移行
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
ms.openlocfilehash: 75c055221609679db3f00016b9cb44c6c8c6e473
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488778"
---
<a name="migrations-with-multiple-providers"></a>複数のプロバイダーを使用した移行
==================================
[EF Core ツール][ 1]のみ active プロバイダーへの移行をスキャフォールディングします。 場合によっては、ただし、可能性がある、DbContext で 1 つ以上のプロバイダー (Microsoft SQL Server、SQLite など) を使用します。 移行でこれを処理するために 2 つの方法はあります。 2 つのセットを維持する移行の両方で機能プロバイダー--またはセットを 1 つにマージごとに 1 つのことができます。

<a name="two-migration-sets"></a>2 つの移行セット
------------------
最初の方法では、各モデルの変更のための 2 つの移行を生成します。

移行セットごとにはこれを行う方法の 1 つ[別のアセンブリに][ 2] 2 つの移行を追加する間、アクティブなプロバイダー (および移行アセンブリ) を手動で切り替えるとします。

別の方法をツールで作業が容易では、DbContext から派生し、アクティブなプロバイダーをオーバーライドする新しい型を作成します。 この種類はデザイン時に使用を追加または移行を適用したときにします。

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> 各移行セットは、独自の DbContext 型を使用するための個別移行アセンブリを使用してこの方法は必要ありません。

新しい移行を追加する場合は、コンテキストの種類を指定します。

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```
``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> 最後の 1 つの兄弟として作成されているので、次の移行の出力ディレクトリを指定する必要はありません。

<a name="one-migration-set"></a>1 つの移行セット
-----------------
移行の 2 つのセットがない場合は、手動で両方のプロバイダーに適用できる単一セットに結合することができます。

注釈は、プロバイダーを理解しない任意のコメントを無視するために共存できます。 たとえば、Microsoft SQL Server と SQLite の両方で動作する主キー列は、これのようになります。

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

操作は、1 つのプロバイダーにのみ適用できます (またはプロバイダーの間で異なるにいる) 場合は、使用、`ActiveProvider`プロパティをプロバイダーがアクティブかを確認します。

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```


  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md

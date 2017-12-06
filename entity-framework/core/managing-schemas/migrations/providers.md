---
title: "複数のプロバイダーの EF コアと移行"
author: bricelam
ms.author: bricelam
ms.date: 11/8/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 6b278a5ae270b6a84269dffd72eeca609b168cdd
ms.sourcegitcommit: 3b6159db8a6c0653f13c7b528367b4e69ac3d51e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
<a name="migrations-with-multiple-providers"></a>複数のプロバイダーと移行
==================================
[EF コア ツール][ 1]アクティブなプロバイダーへの移行をスキャフォールディングのみです。 場合によっては、ただし、場合がある、DbContext で (たとえば Microsoft SQL Server や SQLite) の複数のプロバイダーを使用します。 これに対処する移行で 2 つの方法ができます。 2 つのセットを保持する移行の両方で作業プロバイダー--またはセットを 1 つにマージごとに 1 つのことができます。

<a name="two-migration-sets"></a>2 つの移行セット
------------------
最初の方法では、各モデルの変更の 2 つの移行を生成します。

これは 1 つの方法として各移行のセットを格納する[別のアセンブリに][ 2] 2 つの移行を追加する間、アクティブなプロバイダーと移行アセンブリを手動で切り替えるとします。

ツールでの作業を容易になりますが、新しい型を作成するもう 1 つの方法は、DbContext から派生して、アクティブなプロバイダーをオーバーライドします。 この型はデザイン時使用の追加または移行を適用するときに時間します。

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> 各移行セットは、独自の DbContext 型を使用するため、このアプローチは独立した移行アセンブリを使用する必要はありません。

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

<a name="one-migration-set"></a>1 つの移行のセット
-----------------
移行の 2 つのセットを持つしない場合に、手動で一緒に両方のプロバイダーに適用できる 1 つのセットにします。

注釈は、プロバイダーが理解していないすべての注釈を無視するために共存できます。 たとえば、Microsoft SQL Server と SQLite の両方で動作する主キー列は、次のようになります。

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

操作は、1 つのプロバイダーにのみ適用できます (またはプロバイダー間で異なる方法である) を使用して、`ActiveProvider`プロパティをプロバイダーがアクティブに指示します。

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```


  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md

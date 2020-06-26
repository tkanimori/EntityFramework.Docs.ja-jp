---
title: 複数のプロバイダーを使用した移行-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: 388829992687e626648889a47130cc61df1c0d1f
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370621"
---
# <a name="migrations-with-multiple-providers"></a>複数のプロバイダーを使用した移行

[EF Core ツール][1]は、アクティブなプロバイダーの移行のみをスキャフォールディングします。 ただし、場合によっては、DbContext を使用して、複数のプロバイダー (Microsoft SQL Server や SQLite など) を使用することもできます。 移行でこれを処理する方法は2つあります。 2つの移行セット (プロバイダーごとに1つ) を維持することも、両方で動作する1つのセットに統合することもできます。

## <a name="two-migration-sets"></a>2つの移行セット

最初の方法では、モデルの変更ごとに2つの移行を生成します。

これを行う1つの方法は、各移行セットを[個別のアセンブリに][2]配置し、2つの移行を追加する間に、アクティブなプロバイダー (および移行アセンブリ) を手動で切り替えることです。

ツールを簡単に操作できるもう1つの方法は、DbContext から派生した新しい型を作成し、アクティブなプロバイダーをオーバーライドすることです。 この型は、移行を追加または適用するときに、デザイン時に使用されます。

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> 各移行セットは独自の DbContext 型を使用するため、この方法では個別の移行アセンブリを使用する必要はありません。

新しい移行を追加する場合は、コンテキストの種類を指定します。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> 後続の移行では、最後のディレクトリの兄弟として作成されるため、出力ディレクトリを指定する必要はありません。

## <a name="one-migration-set"></a>1つの移行セット

2つの移行セットを作成したくない場合は、両方のプロバイダーに適用できる1つのセットに手動で組み合わせることができます。

認識できない注釈がプロバイダーによって無視されるため、注釈を共存させることができます。 たとえば、Microsoft SQL Server と SQLite の両方で動作する主キー列は、次のようになります。

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

操作を1つのプロバイダーに対してのみ適用できる場合、またはプロバイダー間で異なる場合は、プロパティを使用して、 `ActiveProvider` どのプロバイダーがアクティブであるかを確認します。

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md

---
title: カスタムの移行操作 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 510d585534b4809179c905ee5b77cab4209a2b8f
ms.sourcegitcommit: 902257be9c63c427dc793750a2b827d6feb8e38c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39614286"
---
<a name="custom-migrations-operations"></a>カスタムの移行操作
============================
MigrationBuilder API では、移行中、さまざまな種類の操作を実行できます。 が網羅したなります。 ただし、API は、独自の操作を定義できる拡張可能なもです。 API を拡張する 2 つの方法があります: を使用して、`Sql()`メソッド、またはカスタムを定義して`MigrationOperation`オブジェクト。

を示すためには、それぞれのアプローチを使用してデータベース ユーザーを作成する操作の実装を見てみましょう。 移行では、次のコードを記述を有効にします。

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

<a name="using-migrationbuildersql"></a>MigrationBuilder.Sql() を使用します。
----------------------------
呼び出す拡張メソッドを定義するカスタム操作を実装する最も簡単な方法は、`MigrationBuilder.Sql()`します。
適切な Transact SQL を生成する例を示します。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

複数のデータベース プロバイダーをサポートするために、移行する場合は、使用できます、`MigrationBuilder.ActiveProvider`プロパティ。 Microsoft SQL Server、PostgreSQL の両方をサポートしている例を示します。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    switch (migrationBuilder.ActiveProvider)
    {
        case "Npgsql.EntityFrameworkCore.PostgreSQL":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD '{password}';");

        case "Microsoft.EntityFrameworkCore.SqlServer":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD = '{password}';");
    }

    return migrationBuilder;
}
```

このアプローチのみはすべてのプロバイダーがわかっている場合、カスタム操作が適用されます。

<a name="using-a-migrationoperation"></a>MigrationOperation を使用します。
---------------------------
SQL からカスタムの操作を分離するために定義できます独自`MigrationOperation`それを表します。 生成する適切な SQL を判断できるように、操作がプロバイダーに渡されます。

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

この方法は、だけ、拡張メソッドはいずれかのこれらの操作を追加する必要があります。`MigrationBuilder.Operations`します。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    migrationBuilder.Operations.Add(
        new CreateUserOperation
        {
            Name = name,
            Password = password
        });

    return migrationBuilder;
}
```

このアプローチには、この操作で SQL を生成する方法を理解するには、各プロバイダーが必要があります、`IMigrationsSqlGenerator`サービス。 新しい操作を処理するために、SQL Server のジェネレーターをオーバーライドする例を示します。

``` csharp
class MyMigrationsSqlGenerator : SqlServerMigrationsSqlGenerator
{
    public MyMigrationsSqlGenerator(
        MigrationsSqlGeneratorDependencies dependencies,
        IMigrationsAnnotationProvider migrationsAnnotations)
        : base(dependencies, migrationsAnnotations)
    {
    }

    protected override void Generate(
        MigrationOperation operation,
        IModel model,
        MigrationCommandListBuilder builder)
    {
        if (operation is CreateUserOperation createUserOperation)
        {
            Generate(createUserOperation, builder);
        }
        else
        {
            base.Generate(operation, model, builder);
        }
    }

    private void Generate(
        CreateUserOperation operation,
        MigrationCommandListBuilder builder)
    {
        var sqlHelper = Dependencies.SqlGenerationHelper;
        var stringMapping = Dependencies.TypeMapper.GetMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

1 つずつ更新では、既定の移行 sql ジェネレーターのサービスを置き換えます。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```

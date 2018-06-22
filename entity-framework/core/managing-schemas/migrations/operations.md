---
title: カスタムの移行操作 - EF コア
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 84d80175e719c950844b13688e1a4992614f25d8
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34163143"
---
<a name="custom-migrations-operations"></a>カスタムの移行操作
============================
MigrationBuilder API では、移行中にさまざまな種類の操作を実行することができますが、排他的なかけ離れていること。 ただし、また API は、拡張を独自の操作を定義できるようにします。 API を拡張する 2 つの方法があります: を使用して、`Sql()`メソッド、またはカスタムを定義して`MigrationOperation`オブジェクト。

理解するには、それぞれのアプローチを使用してデータベース ユーザーを作成する操作の実装を見てみましょう。 この移行で、次のコードを記述できるようにします。

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

<a name="using-migrationbuildersql"></a>MigrationBuilder.Sql() を使用します。
----------------------------
呼び出す拡張メソッドを定義するカスタム操作を実装する最も簡単な方法は、`MigrationBuilder.Sql()`です。
適切な Transact SQL を生成する例を次に示します。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

複数のデータベース プロバイダーをサポートするために、移行する場合を使えば、`MigrationBuilder.ActiveProvider`プロパティです。 Microsoft SQL Server と PostgreSQL の両方をサポートする例を次に示します。

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
}
```

このアプローチだけはすべてのプロバイダーがわかっている場合、カスタムの操作が適用されます。

<a name="using-a-migrationoperation"></a>使用して、MigrationOperation
---------------------------
SQL からカスタムの操作を切り離すことを定義できます。 自分`MigrationOperation`それを表します。 操作は、ことを確認し、適切な SQL を生成するために、プロバイダーに渡されます。

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

この方法は、だけ、拡張メソッドはこれらの操作を 1 つ追加する必要があります。`MigrationBuilder.Operations`です。

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

この方法には、SQL では、この操作を生成する方法を理解するには、各プロバイダーが必要があります、`IMigrationsSqlGenerator`サービス。 新しい操作を処理する SQL Server のジェネレーターをオーバーライドする例を次に示します。

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

1 つずつ更新で、既定の移行 sql ジェネレーターのサービスを置き換えます。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```

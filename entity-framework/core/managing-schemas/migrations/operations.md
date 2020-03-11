---
title: カスタム移行操作-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: bd2bfdc24977a47eaf7a6756a88b758b563d818a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414327"
---
# <a name="custom-migrations-operations"></a>カスタム移行操作

MigrationBuilder API を使用すると、移行中にさまざまな種類の操作を実行できますが、これは包括的なものではありません。 ただし、API を拡張して、独自の操作を定義することもできます。 API を拡張するには、`Sql()` メソッドを使用する方法と、カスタム `MigrationOperation` オブジェクトを定義する方法の2つの方法があります。

例として、各方法を使用してデータベースユーザーを作成する操作を実装する方法を見てみましょう。 この移行では、次のコードを記述できるようにします。

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>MigrationBuilder () の使用

カスタム操作を実装する最も簡単な方法は、`MigrationBuilder.Sql()`を呼び出す拡張メソッドを定義することです。 次に、適切な Transact-sql を生成する例を示します。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

移行で複数のデータベースプロバイダーをサポートする必要がある場合は、`MigrationBuilder.ActiveProvider` プロパティを使用できます。 Microsoft SQL Server と PostgreSQL の両方をサポートする例を次に示します。

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

この方法は、カスタム操作が適用されるすべてのプロバイダーがわかっている場合にのみ機能します。

## <a name="using-a-migrationoperation"></a>MigrationOperation を使用する

SQL からカスタム操作を分離するには、独自の `MigrationOperation` を定義してそれを表すことができます。 次に、操作がプロバイダーに渡され、生成する適切な SQL を決定できるようになります。

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

この方法では、拡張メソッドは、これらの操作のいずれかを `MigrationBuilder.Operations`に追加するだけで済みます。

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

この方法では、各プロバイダーが `IMigrationsSqlGenerator` サービスでこの操作の SQL を生成する方法を知る必要があります。 新しい操作を処理するために SQL Server のジェネレーターをオーバーライドする例を次に示します。

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
        var stringMapping = Dependencies.TypeMappingSource.FindMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(operation.Name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(operation.Password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

既定の移行 sql ジェネレーターサービスを更新されたものに置き換えます。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```

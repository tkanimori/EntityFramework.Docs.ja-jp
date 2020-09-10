---
title: カスタム移行操作-EF Core
description: Entity Framework Core を使用したデータベーススキーマ管理のためのカスタムおよび生の SQL 移行の管理
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 708894d8d567a4644be3a4ace98cc837465710e0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617950"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="d7319-103">カスタム移行操作</span><span class="sxs-lookup"><span data-stu-id="d7319-103">Custom Migrations Operations</span></span>

<span data-ttu-id="d7319-104">MigrationBuilder API を使用すると、移行中にさまざまな種類の操作を実行できますが、これは包括的なものではありません。</span><span class="sxs-lookup"><span data-stu-id="d7319-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="d7319-105">ただし、API を拡張して、独自の操作を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="d7319-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="d7319-106">API を拡張するには、メソッドを使用する方法と、 `Sql()` カスタムオブジェクトを定義する方法の2つの方法があります。 `MigrationOperation`</span><span class="sxs-lookup"><span data-stu-id="d7319-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="d7319-107">例として、各方法を使用してデータベースユーザーを作成する操作を実装する方法を見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="d7319-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="d7319-108">この移行では、次のコードを記述できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d7319-108">In our migrations, we want to enable writing the following code:</span></span>

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="d7319-109">MigrationBuilder () の使用</span><span class="sxs-lookup"><span data-stu-id="d7319-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="d7319-110">カスタム操作を実装する最も簡単な方法は、を呼び出す拡張メソッドを定義することです `MigrationBuilder.Sql()` 。</span><span class="sxs-lookup"><span data-stu-id="d7319-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="d7319-111">次に、適切な Transact-sql を生成する例を示します。</span><span class="sxs-lookup"><span data-stu-id="d7319-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

<span data-ttu-id="d7319-112">移行で複数のデータベースプロバイダーをサポートする必要がある場合は、プロパティを使用でき `MigrationBuilder.ActiveProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="d7319-112">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="d7319-113">Microsoft SQL Server と PostgreSQL の両方をサポートする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d7319-113">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

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

<span data-ttu-id="d7319-114">この方法は、カスタム操作が適用されるすべてのプロバイダーがわかっている場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="d7319-114">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="d7319-115">MigrationOperation を使用する</span><span class="sxs-lookup"><span data-stu-id="d7319-115">Using a MigrationOperation</span></span>

<span data-ttu-id="d7319-116">SQL からカスタム操作を分離するには、独自のを定義してそれを表すことができ `MigrationOperation` ます。</span><span class="sxs-lookup"><span data-stu-id="d7319-116">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="d7319-117">次に、操作がプロバイダーに渡され、生成する適切な SQL を決定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d7319-117">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

<span data-ttu-id="d7319-118">この方法では、拡張メソッドでこれらの操作のいずれかをに追加するだけで済み `MigrationBuilder.Operations` ます。</span><span class="sxs-lookup"><span data-stu-id="d7319-118">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

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

<span data-ttu-id="d7319-119">この方法では、各プロバイダーがサービスでこの操作の SQL を生成する方法を知る必要があり `IMigrationsSqlGenerator` ます。</span><span class="sxs-lookup"><span data-stu-id="d7319-119">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="d7319-120">新しい操作を処理するために SQL Server のジェネレーターをオーバーライドする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d7319-120">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

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

<span data-ttu-id="d7319-121">既定の移行 sql ジェネレーターサービスを更新されたものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d7319-121">Replace the default migrations sql generator service with the updated one.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```

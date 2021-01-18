---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 64b72ba2a6f752b9d71ea9b64ab08f4cf92ef03d
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129279"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="f1758-103">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="f1758-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="f1758-104">以下のリストには、EF Core 5.0 の主な新機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="f1758-104">The following list includes the major new features in EF Core 5.0.</span></span> <span data-ttu-id="f1758-105">リリースの問題の完全なリストについては、[問題の追跡ツール](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1758-105">For the full list of issues in the release, see our [issue tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span></span>

<span data-ttu-id="f1758-106">メジャー リリースである EF Core 5.0 には、いくつかの[破壊的変更](xref:core/what-is-new/ef-core-5.0/breaking-changes)も含まれています。これは、既存のアプリケーションに悪影響を及ぼす可能性がある API の強化または動作変更です。</span><span class="sxs-lookup"><span data-stu-id="f1758-106">As a major release, EF Core 5.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), which are API improvements or behavioral changes that may have negative impact on existing applications.</span></span>

## <a name="many-to-many"></a><span data-ttu-id="f1758-107">多対多</span><span class="sxs-lookup"><span data-stu-id="f1758-107">Many-to-many</span></span>

<span data-ttu-id="f1758-108">結合テーブルを明示的にマッピングしなくても、EF Core 5.0 によって多対多のリレーションシップがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f1758-108">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="f1758-109">たとえば、次のようなエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="f1758-109">For example, consider these entity types:</span></span>

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Tag> Tags { get; set; }
}

public class Tag
{
    public int Id { get; set; }
    public string Text { get; set; }
    public ICollection<Post> Posts { get; set; }
}
```

<span data-ttu-id="f1758-110">EF Core 5.0 では、規約により、多対多として認識され、データベース内に `PostTag` 結合テーブルが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-110">EF Core 5.0 recognizes this as a many-to-many relationship by convention, and automatically creates a `PostTag` join table in the database.</span></span> <span data-ttu-id="f1758-111">明示的に結合テーブルを参照せずに、データのクエリや更新を実行できるため、コードを大幅に簡素化することができます。</span><span class="sxs-lookup"><span data-stu-id="f1758-111">Data can be queried and updated without explicitly referencing the join table, considerably simplifying code.</span></span> <span data-ttu-id="f1758-112">この場合でも、必要に応じて、結合テーブルをカスタマイズし、明示的にクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-112">The join table can still be customized and queried explicitly if needed.</span></span>

<span data-ttu-id="f1758-113">詳細については、[多対多に関する完全なドキュメントを参照してください](xref:core/modeling/relationships#many-to-many)。</span><span class="sxs-lookup"><span data-stu-id="f1758-113">For further information, [see the full documentation on many-to-many](xref:core/modeling/relationships#many-to-many).</span></span>

## <a name="split-queries"></a><span data-ttu-id="f1758-114">分割クエリ</span><span class="sxs-lookup"><span data-stu-id="f1758-114">Split queries</span></span>

<span data-ttu-id="f1758-115">EF Core 3.0 以降、EF Core では、LINQ クエリごとに 1 つの SQL クエリが常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-115">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="f1758-116">これにより、使用中のトランザクション モードの制約内で返されるデータの整合性が確保されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-116">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="f1758-117">ただし、クエリで `Include` またはプロジェクションを使用して複数の関連コレクションを戻すと、この処理が非常に遅くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1758-117">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="f1758-118">EF Core 5.0 では、関連するコレクションを含む単一の LINQ クエリを複数の SQL クエリに分割できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-118">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="f1758-119">これにより、パフォーマンスを大幅に向上させることができますが、2 つのクエリ間でデータが変更された場合に返される結果に不整合が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1758-119">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="f1758-120">シリアル化可能な、またはスナップショットのトランザクションを使用すると、これを軽減し、分割されたクエリとの整合性を維持できますが、他のパフォーマンス コストと動作の違いが生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1758-120">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

<span data-ttu-id="f1758-121">たとえば、`Include` を使用して 2 つのレベルの関連コレクションを取得するクエリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="f1758-121">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

<span data-ttu-id="f1758-122">既定では、SQLite プロバイダーの使用時に、EF Core によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-122">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

<span data-ttu-id="f1758-123">分割クエリを使用すると、代わりに次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-123">With split queries, the following SQL is generated instead:</span></span>

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

<span data-ttu-id="f1758-124">分割クエリを有効にするには、新しい `AsSplitQuery` 演算子を LINQ クエリ内の任意の場所に、またはグローバルにモデルの `OnConfiguring` に配置します。</span><span class="sxs-lookup"><span data-stu-id="f1758-124">Split queries can be enabled by placing the new `AsSplitQuery` operator anywhere in your LINQ query, or globally in your model's `OnConfiguring`.</span></span> <span data-ttu-id="f1758-125">詳細については、[分割クエリに関する完全なドキュメントを参照してください](xref:core/querying/single-split-queries)。</span><span class="sxs-lookup"><span data-stu-id="f1758-125">For further information, [see the full documentation on split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="simple-logging-and-improved-diagnostics"></a><span data-ttu-id="f1758-126">シンプルなログと強化された診断</span><span class="sxs-lookup"><span data-stu-id="f1758-126">Simple logging and improved diagnostics</span></span>

<span data-ttu-id="f1758-127">EF Core 5.0 では、新しい `LogTo` メソッドを使用してログを簡単に設定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-127">EF Core 5.0 introduces a simple way to set up logging via the new `LogTo` method.</span></span> <span data-ttu-id="f1758-128">次の例では、EF Core によって生成されたすべての SQL を含め、ログ メッセージがコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="f1758-128">The following will cause logging messages to be written to the console, including all SQL generated by EF Core:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

<span data-ttu-id="f1758-129">さらに、任意の LINQ クエリで `ToQueryString` を呼び出して、クエリで実行する SQL を取得できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-129">In addition, it is now possible to call `ToQueryString` on any LINQ query, retrieving the SQL that the query would execute:</span></span>

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

<span data-ttu-id="f1758-130">最後に、内部の詳細ビューを提供する拡張 `DebugView` プロパティに合わせてさまざまな EF Core の型を使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-130">Finally, various EF Core types have been fitted with an enhanced `DebugView` property which provides a detailed view into the internals.</span></span> <span data-ttu-id="f1758-131">たとえば、<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> を調べて、特定の時点でどのエンティティが追跡されるかを正確に把握することができます。</span><span class="sxs-lookup"><span data-stu-id="f1758-131">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> can be consulted to see exactly which entities are being tracked in a given moment.</span></span>

<span data-ttu-id="f1758-132">詳細については、[ログとインターセプトに関するドキュメントを参照してください](xref:core/logging-events-diagnostics/index)。</span><span class="sxs-lookup"><span data-stu-id="f1758-132">For further information, [see the documentation on logging and interception](xref:core/logging-events-diagnostics/index).</span></span>

## <a name="filtered-include"></a><span data-ttu-id="f1758-133">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="f1758-133">Filtered include</span></span>

<span data-ttu-id="f1758-134">`Include` メソッドで、インクルードされるエンティティのフィルター処理がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-134">The `Include` method now supports filtering of the entities included:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="f1758-135">このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-135">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="f1758-136">詳細については、[分割クエリに関する完全なドキュメントを参照してください](xref:core/querying/related-data/eager#filtered-include)。</span><span class="sxs-lookup"><span data-stu-id="f1758-136">For further information, [see the full documentation on split queries](xref:core/querying/related-data/eager#filtered-include).</span></span>

## <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="f1758-137">Table-Per-Type (TPT) のマッピング</span><span class="sxs-lookup"><span data-stu-id="f1758-137">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="f1758-138">EF Core の既定では、.NET 型の継承階層が 1 つのデータベース テーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="f1758-138">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="f1758-139">これは、Table-Per-Hierarchy (TPH) のマッピングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f1758-139">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="f1758-140">EF Core 5.0 を使用すると、継承階層の各 .NET 型を別のデータベース テーブルにマップすることもできます。これは Table-Per-Type (TPT) のマッピングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f1758-140">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="f1758-141">たとえば、次のようにマップされた階層を持つこのモデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="f1758-141">For example, consider this model with a mapped hierarchy:</span></span>

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Cat : Animal
{
    public string EducationLevel { get; set; }
}

public class Dog : Animal
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="f1758-142">TPT を使用すると、階層内の型ごとにデータベース テーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-142">With TPT, a database table is created for each type in the hierarchy:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);
```

<span data-ttu-id="f1758-143">詳細については、[TPT に関する完全なドキュメントを参照してください](xref:core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="f1758-143">For further information, [see the full documentation on TPT](xref:core/modeling/inheritance).</span></span>

## <a name="flexible-entity-mapping"></a><span data-ttu-id="f1758-144">柔軟なエンティティ マッピング</span><span class="sxs-lookup"><span data-stu-id="f1758-144">Flexible entity mapping</span></span>

<span data-ttu-id="f1758-145">エンティティ型は通常、その型に対してクエリを実行するときに、EF Core によってテーブルまたはビューの内容がプルバックされるようなテーブルまたはビューにマップされます。</span><span class="sxs-lookup"><span data-stu-id="f1758-145">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="f1758-146">EF Core 5.0 では、マッピング オプションが追加され、エンティティを SQL クエリ ("クエリ定義"と呼ばれる)、またはテーブル値関数 (TVF) にマップできます。</span><span class="sxs-lookup"><span data-stu-id="f1758-146">EF Core 5.0 adds additional mapping options, where an entity can be mapped to a SQL query (called a "defining query"), or to a table-valued function (TVF):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");

    modelBuilder.Entity<Blog>().ToFunction("BlogsReturningFunction");
}
```

<span data-ttu-id="f1758-147">さらに、テーブル値関数を DbSet ではなく .NET メソッドにマップしてパラメーターを渡すことができます。このマッピングは、<xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> で設定できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-147">Table-valued functions can also be mapped to a .NET method rather than to a DbSet, allowing parameters to be passed; the mapping can be set up with <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A>.</span></span>

<span data-ttu-id="f1758-148">最後に、クエリ時はエンティティをビュー (または関数や定義クエリ) にマップし、更新時にはテーブルにマップできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-148">Finally, it is now possible to map an entity to a view when querying (or to a function or defining query), but to a table when updating:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a><span data-ttu-id="f1758-149">共有型のエンティティ型とプロパティ バッグ</span><span class="sxs-lookup"><span data-stu-id="f1758-149">Shared-type entity types and property bags</span></span>

<span data-ttu-id="f1758-150">EF Core 5.0 では、同じ CLR 型を複数の異なるエンティティ型にマップできます。このような型は、共有型のエンティティ型と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="f1758-150">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types; such types are known as shared-type entity types.</span></span> <span data-ttu-id="f1758-151">この機能では、任意の CLR 型を使用できますが、.NET `Dictionary` により、"プロパティ バッグ" と呼ばれる特に魅力的なユースケースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-151">While any CLR type can be used with this feature, .NET `Dictionary` offers a particularly compelling use-case which we call "property bags":</span></span>

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<decimal>("Price");
        });
    }
}
```

<span data-ttu-id="f1758-152">これらのエンティティは、専用の CLR 型を使用して、通常のエンティティ型と同様にクエリおよび更新できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-152">These entities can then be queried and updated just like normal entity types with their own, dedicated CLR type.</span></span> <span data-ttu-id="f1758-153">詳細については、[プロパティ バッグ](xref:core/modeling/shadow-properties)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1758-153">More information can be found in the documentation on [property bags](xref:core/modeling/shadow-properties).</span></span>

## <a name="required-11-dependents"></a><span data-ttu-id="f1758-154">必要な一対一の依存</span><span class="sxs-lookup"><span data-stu-id="f1758-154">Required 1:1 dependents</span></span>

<span data-ttu-id="f1758-155">EF Core 3.1 では、一対一のリレーションシップの依存側は常に省略可能と見なされていました。</span><span class="sxs-lookup"><span data-stu-id="f1758-155">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="f1758-156">これは、所有エンティティを使用する場合に最も顕著でした。モデルで必須として構成されている場合でも、所有エンティティのすべての列がデータベースで Null 許容として作成されるためです。</span><span class="sxs-lookup"><span data-stu-id="f1758-156">This was most apparent when using owned entities, as all the owned entity's column were created as nullable in the database, even if they were configured as required in the model.</span></span>

<span data-ttu-id="f1758-157">EF Core 5.0 では、所有エンティティへのナビゲーションを必須の依存として構成できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-157">In EF Core 5.0, a navigation to an owned entity can be configured as as a required dependent.</span></span> <span data-ttu-id="f1758-158">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f1758-158">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();
    });
}
```

## <a name="dbcontextfactory"></a><span data-ttu-id="f1758-159">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="f1758-159">DbContextFactory</span></span>

<span data-ttu-id="f1758-160">EF Core 5.0 では、アプリケーションの依存関係挿入 (D.I.) コンテナーに DbContext インスタンスを作成するためのファクトリを登録する `AddDbContextFactory` と `AddPooledDbContextFactory` が導入されています。これは、アプリケーション コードでコンテキスト インスタンスを手動で作成と破棄を行う必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="f1758-160">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container; this can be useful when application code needs to create and dispose context instances manually.</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="f1758-161">現在、`IDbContextFactory<TContext>` で ASP.NET Core コントローラーなどのアプリケーション サービスを挿入し、それを使用してコンテキスト インスタンスをインスタンス化できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-161">At this point, application services such as ASP.NET Core controllers can then be injected with `IDbContextFactory<TContext>`, and use it to instantiate context instances:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
        => _contextFactory = contextFactory;

    public void DoSomeThing()
    {
        using (var context = _contextFactory.CreateDbContext())
        {
            // ...
        }
    }
}
```

<span data-ttu-id="f1758-162">詳細については、[DbContextFactory に関する完全なドキュメントを参照してください](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor)。</span><span class="sxs-lookup"><span data-stu-id="f1758-162">For further information, [see the full documentation on DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span></span>

## <a name="sqlite-table-rebuilds"></a><span data-ttu-id="f1758-163">SQLite テーブルの再構築</span><span class="sxs-lookup"><span data-stu-id="f1758-163">SQLite table rebuilds</span></span>

<span data-ttu-id="f1758-164">SQLite では、他のデータベースと比べて、スキーマ操作機能がより制限されます。たとえば、既存のテーブルから列を削除することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f1758-164">Compared to other databases, SQLite is relatively limited in its schema manipulation capabilities; for example, dropping a column from an existing table is not supported.</span></span> <span data-ttu-id="f1758-165">EF Core 5.0 では、自動的に新しいテーブルを作成し、データを古いテーブルからコピーし、古いテーブルを削除して、新しいテーブルを削除して名前を変更することにより、これらの制限を回避します。</span><span class="sxs-lookup"><span data-stu-id="f1758-165">EF Core 5.0 works around these limitations by automatically creating a new table, copying the data from the old table, dropping the old table and renaming the new one.</span></span> <span data-ttu-id="f1758-166">これにより、テーブルが "再構築" され、以前はサポートされていなかった移行操作を安全に適用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f1758-166">This "rebuilds" the table, and allows previously unsupported migration operations to be safely applied.</span></span>

<span data-ttu-id="f1758-167">テーブルの再構築によってサポートされるようになった移行操作の詳細については、[このドキュメント ページを参照してください](xref:core/providers/sqlite/limitations#migrations-limitations)。</span><span class="sxs-lookup"><span data-stu-id="f1758-167">For details on which migration operations are now supported via table rebuilds, [see this documentation page](xref:core/providers/sqlite/limitations#migrations-limitations).</span></span>

## <a name="database-collations"></a><span data-ttu-id="f1758-168">データベースの照合順序</span><span class="sxs-lookup"><span data-stu-id="f1758-168">Database collations</span></span>

<span data-ttu-id="f1758-169">EF Core 5.0 では、データベース、列、またはクエリのレベルでテキスト照合順序を指定するためのサポートが導入されています。</span><span class="sxs-lookup"><span data-stu-id="f1758-169">EF Core 5.0 introduces support for specifying text collations at the database, column or query level.</span></span> <span data-ttu-id="f1758-170">これにより、大文字と小文字の区別やその他のテキストの側面を柔軟に構成でき、かつクエリのパフォーマンスを低下させることもありません。</span><span class="sxs-lookup"><span data-stu-id="f1758-170">This allows case sensitivity and other textual aspects to be configured in a way that is both flexible and does not compromise query performance.</span></span>

<span data-ttu-id="f1758-171">たとえば、次の例では、SQL Server で大文字と小文字を区別するように `Name` 列を構成し、それに応じて、その列で作成されたインデックスが機能します。</span><span class="sxs-lookup"><span data-stu-id="f1758-171">For example, the following will configure the `Name` column to be case-sensitive on SQL Server, and any indexes created on the column will function accordingly:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

<span data-ttu-id="f1758-172">詳細については、[照合順序と大文字と小文字の区別に関する完全なドキュメントを参照してください](xref:core/miscellaneous/collations-and-case-sensitivity)。</span><span class="sxs-lookup"><span data-stu-id="f1758-172">For further information, [see the full documentation on collations and case sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

## <a name="event-counters"></a><span data-ttu-id="f1758-173">イベント カウンター</span><span class="sxs-lookup"><span data-stu-id="f1758-173">Event counters</span></span>

<span data-ttu-id="f1758-174">EF Core 5.0 では、アプリケーションのパフォーマンスを追跡し、さまざまな異常を特定するために使用できる[イベント カウンター](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0)が公開されています。</span><span class="sxs-lookup"><span data-stu-id="f1758-174">EF Core 5.0 exposes [event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) which can be used to track your application's performance and spot various anomalies.</span></span> <span data-ttu-id="f1758-175">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ツールを使用して EF を実行するプロセスにアタッチするだけです。</span><span class="sxs-lookup"><span data-stu-id="f1758-175">Simply attach to a process running EF with the [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) tool:</span></span>

```console
> dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                     1,755
    Queries (Total)                                            98,402
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             1
```

<span data-ttu-id="f1758-176">詳細については、[イベント カウンターに関する完全なドキュメントを参照してください](xref:core/logging-events-diagnostics/event-counters)。</span><span class="sxs-lookup"><span data-stu-id="f1758-176">For further information, [see the full documentation on event counters](xref:core/logging-events-diagnostics/event-counters).</span></span>

## <a name="other-features"></a><span data-ttu-id="f1758-177">その他の機能</span><span class="sxs-lookup"><span data-stu-id="f1758-177">Other features</span></span>

### <a name="model-building"></a><span data-ttu-id="f1758-178">モデル構築</span><span class="sxs-lookup"><span data-stu-id="f1758-178">Model building</span></span>

* <span data-ttu-id="f1758-179">[値の比較演算子](xref:core/modeling/value-comparers)をより簡単に構築するためのモデル構築 API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="f1758-179">Model building APIs have been introduced for easier configuration of [value comparers](xref:core/modeling/value-comparers).</span></span>
* <span data-ttu-id="f1758-180">計算列を [*stored* または *virtual*](xref:core/modeling/generated-properties#computed-columns) として構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-180">Computed columns can now be configured as [*stored* or *virtual*](xref:core/modeling/generated-properties#computed-columns).</span></span>
* <span data-ttu-id="f1758-181">精度とスケールを [Fluent API を使用して](xref:core/modeling/entity-properties#precision-and-scale)構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-181">Precision and scale can now be configured [via the Fluent API](xref:core/modeling/entity-properties#precision-and-scale).</span></span>
* <span data-ttu-id="f1758-182">[ナビゲーション プロパティ](xref:core/modeling/relationships#configuring-navigation-properties)用の新しいモデル構築 API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="f1758-182">New model building APIs have been introduced for [navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>
* <span data-ttu-id="f1758-183">プロパティと同様に、フィールド用の新しいモデル構築 API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="f1758-183">New model building APIs have been introduced for fields, similar to properties.</span></span>
* <span data-ttu-id="f1758-184">.NET の [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) 型と [IPAddress](/dotnet/api/system.net.ipaddress) 型をデータベース文字列の列にマップできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-184">The .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) and [IPAddress](/dotnet/api/system.net.ipaddress) types can now be mapped to database string columns.</span></span>
* <span data-ttu-id="f1758-185">[新しい `[BackingField]` 属性](xref:core/modeling/backing-field)を使用してバッキング フィールドを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-185">A backing field can now be configured via [the new `[BackingField]` attribute](xref:core/modeling/backing-field).</span></span>
* <span data-ttu-id="f1758-186">Null 許容のバッキング フィールドを使用できるようになりました。これにより、CLR 既定値が適切な sentinel 値ではない場合にストア生成の既定値のサポートが向上しました (新しい `bool`)。</span><span class="sxs-lookup"><span data-stu-id="f1758-186">Nullable backing fields are now allowed, providing better support for store-generated defaults where the CLR default isn't a good sentinel value (notable `bool`).</span></span>
* <span data-ttu-id="f1758-187">Fluent API を使用する代わりに、エンティティ型に対して新しい `[Index]` 属性を使用してインデックスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="f1758-187">A new `[Index]` attribute can be used on an entity type to specify an index, instead of using the Fluent API.</span></span>
* <span data-ttu-id="f1758-188">新しい `[Keyless]` 属性を使用して、エンティティ型を [キーなしとして](xref:core/modeling/keyless-entity-types)構成できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-188">A new `[Keyless]` attribute can be used to configure an entity type [as having no key](xref:core/modeling/keyless-entity-types).</span></span>
* <span data-ttu-id="f1758-189">既定により、[EF Core では識別子が "*完全*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration)" と見なされるようになりました。つまり、モデル内のアプリケーションによって構成されていない識別子の値は表示されないことが想定されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-189">By default, [EF Core now regards discriminators as *complete*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), meaning that it expects to never see discriminator values not configured by the application in the model.</span></span> <span data-ttu-id="f1758-190">これにより、パフォーマンスが多少向上し、識別子列に不明な値が含まれている場合は無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="f1758-190">This allows for some performance improvements, and can be disabled if your discriminator column might hold unknown values.</span></span>

### <a name="query"></a><span data-ttu-id="f1758-191">クエリ</span><span class="sxs-lookup"><span data-stu-id="f1758-191">Query</span></span>

* <span data-ttu-id="f1758-192">クエリ変換エラーの例外に、失敗の理由についてより明確な説明が含まれるようになり、問題を特定するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="f1758-192">Query translation failure exceptions now contain more explicit reasons about the reasons for the failure, to help pinpoint the issue.</span></span>
* <span data-ttu-id="f1758-193">追跡なしのクエリで、[ID の解決](xref:core/querying/tracking#identity-resolution)を実行できるようになりました。これにより、同じデータベース オブジェクトについて複数のエンティティ インスタンスが返されるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-193">No-tracking queries can now perform [identity resolution](xref:core/querying/tracking#identity-resolution), avoiding multiple entity instances being returned for the same database object.</span></span>
* <span data-ttu-id="f1758-194">条件付き集計で GroupBy のサポートが追加されました (例: `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`)。</span><span class="sxs-lookup"><span data-stu-id="f1758-194">Added support for GroupBy with conditional aggregates (e.g. `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).</span></span>
* <span data-ttu-id="f1758-195">集計前に Distinct 演算子をグループ要素に変換するためのサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="f1758-195">Added support for translating the Distinct operator over group elements before aggregate.</span></span>
* <span data-ttu-id="f1758-196">[`Reverse`](/dotnet/api/system.linq.queryable.reverse) の変換。</span><span class="sxs-lookup"><span data-stu-id="f1758-196">Translation of [`Reverse`](/dotnet/api/system.linq.queryable.reverse).</span></span>
* <span data-ttu-id="f1758-197">SQL Server の `DateTime` の変換が改善されました (例: [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A)、[`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A))。</span><span class="sxs-lookup"><span data-stu-id="f1758-197">Improved translation around `DateTime` for SQL Server (e.g. [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A), [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).</span></span>
* <span data-ttu-id="f1758-198">バイト配列での新しいメソッドの変換 (例: [`Contains`](/dotnet/api/system.linq.enumerable.contains)、[`Length`](/dotnet/api/system.array.length)、[`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal))。</span><span class="sxs-lookup"><span data-stu-id="f1758-198">Translation of new methods on byte arrays (e.g. [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length), [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).</span></span>
* <span data-ttu-id="f1758-199">いくつかの追加のビット処理演算子 (2 の補数など) の変換。</span><span class="sxs-lookup"><span data-stu-id="f1758-199">Translation of some additional bitwise operators, such as two's complement.</span></span>
* <span data-ttu-id="f1758-200">文字列に対する `FirstOrDefault` の変換。</span><span class="sxs-lookup"><span data-stu-id="f1758-200">Translation of `FirstOrDefault` over strings.</span></span>
* <span data-ttu-id="f1758-201">null セマンティクスに関するクエリ変換が改善され、その結果、クエリの厳密さと効率が向上しました。</span><span class="sxs-lookup"><span data-stu-id="f1758-201">Improved query translation around null semantics, resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="f1758-202">null 値の反映を制御するためにユーザーマップ関数に注釈を付けられるようになりました。この場合も、その結果、クエリの厳密さと効率が向上しました。</span><span class="sxs-lookup"><span data-stu-id="f1758-202">User-mapped functions can now be annotated to control null propagation, again resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="f1758-203">CASE ブロックを含む SQL は、大幅に簡潔になりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-203">SQL containing CASE blocks is now considerably more concise.</span></span>
* <span data-ttu-id="f1758-204">新しい [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) メソッドを使用してクエリで SQL Server [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) 関数を呼び出すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-204">The SQL Server [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) function can now be called in queries via the new [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) method.</span></span>
* <span data-ttu-id="f1758-205">`EnableDetailedErrors` を使用すると、[例外にさらに詳細が追加されます](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions)。</span><span class="sxs-lookup"><span data-stu-id="f1758-205">`EnableDetailedErrors` adds [additional details to exceptions](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span></span>

### <a name="saving"></a><span data-ttu-id="f1758-206">Saving</span><span class="sxs-lookup"><span data-stu-id="f1758-206">Saving</span></span>

* <span data-ttu-id="f1758-207">SaveChanges [インターセプト](xref:core/logging-events-diagnostics/interceptors#savechanges-interception)と[イベント](xref:core/logging-events-diagnostics/events)。</span><span class="sxs-lookup"><span data-stu-id="f1758-207">SaveChanges [interception](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) and [events](xref:core/logging-events-diagnostics/events).</span></span>
* <span data-ttu-id="f1758-208">[トランザクション セーブポイント](xref:core/saving/transactions#savepoints)を制御するための API が導入されました。</span><span class="sxs-lookup"><span data-stu-id="f1758-208">APIs have been introduced for controlling [transaction savepoints](xref:core/saving/transactions#savepoints).</span></span> <span data-ttu-id="f1758-209">さらに、EF Core では、`SaveChanges` が呼び出され、トランザクションが既に進行中であるときにセーブポイントを自動的に作成し、障害が発生した場合にそれをロールバックします。</span><span class="sxs-lookup"><span data-stu-id="f1758-209">In addition, EF Core will automatically create a savepoint when `SaveChanges` is called and a transaction is already in progress, and roll back to it in case of failure.</span></span>
* <span data-ttu-id="f1758-210">トランザクション ID をアプリケーションによって明示的に設定できます。これにより、ログやその他の場所でトランザクション イベントの関連付けがより簡単になります。</span><span class="sxs-lookup"><span data-stu-id="f1758-210">A transaction ID can be explicitly set by the application, allowing for easier correlation of transaction events in logging and elsewhere.</span></span>
* <span data-ttu-id="f1758-211">バッチ処理のパフォーマンス分析に基づいて、SQL Server の既定の最大バッチ サイズが 42 に変更されました。</span><span class="sxs-lookup"><span data-stu-id="f1758-211">The default maximum batch size for SQL Server has been changed to 42 based on an analysis of batching performance.</span></span>

### <a name="migrations-and-scaffolding"></a><span data-ttu-id="f1758-212">移行とスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="f1758-212">Migrations and scaffolding</span></span>

* <span data-ttu-id="f1758-213">テーブルを[移行から除外](xref:core/modeling/entity-types#excluding-from-migrations)できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-213">Tables can now be [excluded from migrations](xref:core/modeling/entity-types#excluding-from-migrations).</span></span>
* <span data-ttu-id="f1758-214">新しい [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) コマンドを使用すると、データベースにまだ適用されていない移行が表示されるようになりました (Package Management コンソールでは、[`Get-Migration`](xref:core/cli/powershell#get-migration) が同じ機能を備えています)。</span><span class="sxs-lookup"><span data-stu-id="f1758-214">A new [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) command now shows which migrations have not yet been applied to the database ([`Get-Migration`](xref:core/cli/powershell#get-migration) does the same in the Package Management Console).</span></span>
* <span data-ttu-id="f1758-215">移行アプリケーションが失敗したときの処理を改善するために、必要に応じて、移行スクリプトにトランザクション ステートメントが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-215">Migrations scripts now contain transaction statements where appropriate to improve handling cases where migration application fails.</span></span>
* <span data-ttu-id="f1758-216">マップされていない基底クラスの列が、マップされたエンティティ型の他の列の後に並べ替えられるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-216">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="f1758-217">これは、新しく作成されたテーブルにのみ影響し、既存のテーブルの列順は変更されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f1758-217">Note this only impacts newly created tables; the column order for existing tables remains unchanged.</span></span>
* <span data-ttu-id="f1758-218">移行生成で、生成される移行がべき等であるかどうか、および出力が即座に実行されるか、またはスクリプトとして生成されるかどうかを認識できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-218">Migration generation can now be aware if the migration being generated is idempotent, and whether the output will be executed immediately or generated as a script.</span></span>
* <span data-ttu-id="f1758-219">[移行](xref:core/managing-schemas/migrations/index#namespaces)および[スキャフォールディング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)で名前空間を指定するための新しいコマンドライン パラメーターが追加されました。</span><span class="sxs-lookup"><span data-stu-id="f1758-219">New command-line parameters have been added for specifying namespaces in [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [scaffolding](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span></span>
* <span data-ttu-id="f1758-220">[dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) コマンドで、接続文字列を指定するための新しい `--connection` パラメーターが受け取られるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-220">The [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) command now accepts a new `--connection` parameter for specifying the connection string.</span></span>
* <span data-ttu-id="f1758-221">既存のデータベースのスキャフォールディングで、テーブル名が単数化されるようになりました。これにより、`People` および `Addresses` という名前のテーブルは、`Person` および `Address` という名前のエンティティ型にスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="f1758-221">Scaffolding existing databases now singularizes table names, so tables named `People` and `Addresses` will be scaffolded to entity types called `Person` and `Address`.</span></span> <span data-ttu-id="f1758-222">[元のデータベース名はそのまま保持されます](xref:core/managing-schemas/scaffolding#preserving-names)。</span><span class="sxs-lookup"><span data-stu-id="f1758-222">[Original database names can still be preserved](xref:core/managing-schemas/scaffolding#preserving-names).</span></span>
* <span data-ttu-id="f1758-223">モデルをスキャフォールディングする場合、新しい [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) オプションを使用して、`OnModelConfiguring` を除外するように EF Core に指示することができます。</span><span class="sxs-lookup"><span data-stu-id="f1758-223">The new [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) option can instruct EF Core to exclude `OnModelConfiguring` when scaffolding a model.</span></span>

### <a name="cosmos"></a><span data-ttu-id="f1758-224">Cosmos</span><span class="sxs-lookup"><span data-stu-id="f1758-224">Cosmos</span></span>

* <span data-ttu-id="f1758-225">[Cosmos 接続の設定](xref:core/providers/cosmos/index#cosmos-options) が拡張されました。</span><span class="sxs-lookup"><span data-stu-id="f1758-225">[Cosmos connection settings](xref:core/providers/cosmos/index#cosmos-options) have been expanded.</span></span>
* <span data-ttu-id="f1758-226">オプティミスティック同時実行制御が、[ETag を使用して Cosmos でサポートされる](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags)ようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-226">Optimistic concurrency is now [supported on Cosmos via the use of ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span></span>
* <span data-ttu-id="f1758-227">新しい `WithPartitionKey` メソッドを使用することにより、Cosmos [パーティション キー](xref:core/providers/cosmos/index#partition-keys)をモデルとクエリの両方に含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f1758-227">The new `WithPartitionKey` method allows the Cosmos [partition key](xref:core/providers/cosmos/index#partition-keys) to be included both in the model and in queries.</span></span>
* <span data-ttu-id="f1758-228">文字列メソッド [`Contains`](/dotnet/api/system.string.contains)、[`StartsWith`](/dotnet/api/system.string.startswith)、[`EndsWith`](/dotnet/api/system.string.endswith) が Cosmos 用に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-228">The string methods [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) and [`EndsWith`](/dotnet/api/system.string.endswith) are now translated for Cosmos.</span></span>
* <span data-ttu-id="f1758-229">Cosmos で、C# `is` 演算子が変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-229">The C# `is` operator is now translated on Cosmos.</span></span>

### <a name="sqlite"></a><span data-ttu-id="f1758-230">Sqlite</span><span class="sxs-lookup"><span data-stu-id="f1758-230">Sqlite</span></span>

* <span data-ttu-id="f1758-231">計算列がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-231">Computed columns are now supported.</span></span>
* <span data-ttu-id="f1758-232">GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-232">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="f1758-233">SqliteConnection の初期化がゆっくりになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-233">Initialization of SqliteConnection is now lazy.</span></span>

### <a name="other"></a><span data-ttu-id="f1758-234">その他</span><span class="sxs-lookup"><span data-stu-id="f1758-234">Other</span></span>

* <span data-ttu-id="f1758-235">[INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) および [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) を自動的に実装する変更追跡プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-235">Change-tracking proxies can be generated that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="f1758-236">これにより、`SaveChanges` が呼び出されたときに変更をスキャンしない、変更追跡の代替方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="f1758-236">This provides an alternative approach to change-tracking that doesn't scan for changes when `SaveChanges` is called.</span></span>
* <span data-ttu-id="f1758-237">既に初期化された DbContext で <xref:System.Data.Common.DbConnection> または接続文字列を変更できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-237">A <xref:System.Data.Common.DbConnection> or connection string can now be changed on an already-initialized DbContext.</span></span>
* <span data-ttu-id="f1758-238">新しい <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) メソッドを使用すると、追跡されたすべてのエンティティの DbContext がクリアされます。</span><span class="sxs-lookup"><span data-stu-id="f1758-238">The new <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) method clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="f1758-239">これは、作業単位ごとに有効期間が短い新しいコンテキスト インスタンスを作成するベスト プラクティスを使用している場合には通常不要です。</span><span class="sxs-lookup"><span data-stu-id="f1758-239">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="f1758-240">ただし、DbContext インスタンスの状態をリセットする必要がある場合、この新しい `Clear()` メソッドを使用すると、すべてのエンティティを一括でデタッチするよりも、効率と堅牢性が向上します。</span><span class="sxs-lookup"><span data-stu-id="f1758-240">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more efficient and robust than mass-detaching all entities.</span></span>
* <span data-ttu-id="f1758-241">EF Core コマンド ライン ツールによって、環境変数 `ASPNETCORE_ENVIRONMENT` "_および_" `DOTNET_ENVIRONMENT` が "Development" に自動的に構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-241">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="f1758-242">これにより、汎用ホストを使用する場合のエクスペリエンスと、開発時の ASP.NET Core のエクスペリエンスが一致するようになります。</span><span class="sxs-lookup"><span data-stu-id="f1758-242">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span>
* <span data-ttu-id="f1758-243">カスタム コマンドライン引数を <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> に渡すことができます。これにより、アプリケーションで、コンテキストの作成および初期化方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-243">Custom command-line arguments can be flowed into <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601>, allowing applications to control how the context is created and initialized.</span></span>
* <span data-ttu-id="f1758-244">インデックス FILL FACTOR を [SQL Server で構成](xref:core/providers/sql-server/indexes#fill-factor)できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="f1758-244">The index fill factor can now be [configured on SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span></span>
* <span data-ttu-id="f1758-245">新しい <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> プロパティを使用して、リレーショナル プロバイダーを使用する場合と非リレーション プロバイダー (InMemory など) を使用する場合を区別できます。</span><span class="sxs-lookup"><span data-stu-id="f1758-245">The new <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> property can be used to distinguish when using a relational provider and a non-relation provider (such as InMemory).</span></span>

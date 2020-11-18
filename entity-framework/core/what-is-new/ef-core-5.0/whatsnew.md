---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 3efa883cdfac1ecd412112ef06c7763f1a7e12f1
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429248"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="d75e9-103">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="d75e9-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="d75e9-104">EF Core 5.0 に予定されていたすべての機能が揃いました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-104">All features planned for EF Core 5.0 have now been completed.</span></span> <span data-ttu-id="d75e9-105">このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-105">This page contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="d75e9-106">このページには [EF Core 5.0 のプラン](xref:core/what-is-new/ef-core-5.0/plan)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="d75e9-107">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="rc1"></a><span data-ttu-id="d75e9-108">RC1</span><span class="sxs-lookup"><span data-stu-id="d75e9-108">RC1</span></span>

### <a name="many-to-many"></a><span data-ttu-id="d75e9-109">多対多</span><span class="sxs-lookup"><span data-stu-id="d75e9-109">Many-to-many</span></span>

<span data-ttu-id="d75e9-110">結合テーブルを明示的にマッピングしなくても、EF Core 5.0 によって多対多のリレーションシップがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-110">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="d75e9-111">たとえば、次のようなエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-111">For example, consider these entity types:</span></span>

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

<span data-ttu-id="d75e9-112">`Post` に `Tags` のコレクションが含まれており、`Tag` に `Posts` のコレクションが含まれていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-112">Notice that `Post` contains a collection of `Tags`, and `Tag` contains a collection of `Posts`.</span></span> <span data-ttu-id="d75e9-113">EF Core 5.0 では、規則に従って多対多のリレーションシップとしてこれが認識されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-113">EF Core 5.0 recognizes this as a many-to-many relationship by convention.</span></span> <span data-ttu-id="d75e9-114">これは、`OnModelCreating` にコードが必要ないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-114">This means no code is required in `OnModelCreating`:</span></span>

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }
}
```

<span data-ttu-id="d75e9-115">データベースの作成に移行 (または `EnsureCreated`) を使用すると、EF Core によって結合テーブルが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-115">When Migrations (or `EnsureCreated`) are used to create the database, EF Core will automatically create the join table.</span></span> <span data-ttu-id="d75e9-116">たとえば、このモデルの SQL Server では、EF Core によって次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-116">For example, on SQL Server for this model, EF Core generates:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id])
);

CREATE TABLE [Tag] (
    [Id] int NOT NULL IDENTITY,
    [Text] nvarchar(max) NULL,
    CONSTRAINT [PK_Tag] PRIMARY KEY ([Id])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] int NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([Id]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tag_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tag] ([Id]) ON DELETE CASCADE
);

CREATE INDEX [IX_PostTag_TagsId] ON [PostTag] ([TagsId]);
```

<span data-ttu-id="d75e9-117">`Tag` エンティティと `Post` エンティティを作成して関連付けると、結合テーブルの更新が自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-117">Creating and associating `Tag` and `Post` entities results in join table updates happening automatically.</span></span> <span data-ttu-id="d75e9-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-118">For example:</span></span>

```csharp
var beginnerTag = new Tag {Text = "Beginner"};
var advancedTag = new Tag {Text = "Advanced"};
var efCoreTag = new Tag {Text = "EF Core"};

context.AddRange(
    new Post {Name = "EF Core 101", Tags = new List<Tag> {beginnerTag, efCoreTag}},
    new Post {Name = "Writing an EF database provider", Tags = new List<Tag> {advancedTag, efCoreTag}},
    new Post {Name = "Savepoints in EF Core", Tags = new List<Tag> {beginnerTag, efCoreTag}});

context.SaveChanges();
```

<span data-ttu-id="d75e9-119">Post と Tag を挿入すると、EF によって結合テーブルに行が自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-119">After inserting the Posts and Tags, EF will then automatically create rows in the join table.</span></span> <span data-ttu-id="d75e9-120">たとえば、SQL Server の場合は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-120">For example, on SQL Server:</span></span>

```sql
SET NOCOUNT ON;
INSERT INTO [PostTag] ([PostsId], [TagsId])
VALUES (@p6, @p7),
(@p8, @p9),
(@p10, @p11),
(@p12, @p13),
(@p14, @p15),
(@p16, @p17);
```

<span data-ttu-id="d75e9-121">クエリの場合、Include およびその他のクエリ操作は、他のリレーションシップと同様に機能します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-121">For queries, Include and other query operations work just like for any other relationship.</span></span> <span data-ttu-id="d75e9-122">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-122">For example:</span></span>

```csharp
foreach (var post in context.Posts.Include(e => e.Tags))
{
    Console.Write($"Post \"{post.Name}\" has tags");

    foreach (var tag in post.Tags)
    {
        Console.Write($" '{tag.Text}'");
    }
}
```

<span data-ttu-id="d75e9-123">生成された SQL により、結合テーブルが自動的に使用されて、関連するすべての Tag が返されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-123">The SQL generated uses the join table automatically to bring back all related Tags:</span></span>

```sql
SELECT [p].[Id], [p].[Name], [t0].[PostsId], [t0].[TagsId], [t0].[Id], [t0].[Text]
FROM [Posts] AS [p]
LEFT JOIN (
    SELECT [p0].[PostsId], [p0].[TagsId], [t].[Id], [t].[Text]
    FROM [PostTag] AS [p0]
    INNER JOIN [Tag] AS [t] ON [p0].[TagsId] = [t].[Id]
) AS [t0] ON [p].[Id] = [t0].[PostsId]
ORDER BY [p].[Id], [t0].[PostsId], [t0].[TagsId], [t0].[Id]
```

<span data-ttu-id="d75e9-124">EF6 とは異なり、EF Core では結合テーブルの完全なカスタマイズが可能です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-124">Unlike EF6, EF Core allows full customization of the join table.</span></span> <span data-ttu-id="d75e9-125">たとえば、次のコードは、結合エンティティへのナビゲーションも持つ多対多のリレーションシップを構成します。結合エンティティにはペイロード プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-125">For example, the code below configures a many-to-many relationship that also has navigations to the join entity, and in which the join entity contains a payload property:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Post>()
        .HasMany(p => p.Tags)
        .WithMany(p => p.Posts)
        .UsingEntity<PostTag>(
            j => j
                .HasOne(pt => pt.Tag)
                .WithMany()
                .HasForeignKey(pt => pt.TagId),
            j => j
                .HasOne(pt => pt.Post)
                .WithMany()
                .HasForeignKey(pt => pt.PostId),
            j =>
            {
                j.Property(pt => pt.PublicationDate).HasDefaultValueSql("CURRENT_TIMESTAMP");
                j.HasKey(t => new { t.PostId, t.TagId });
            });
}
```

> [!NOTE]
> <span data-ttu-id="d75e9-126">データベースから多対多リレーションシップのスキャフォールディングを行うためのサポートは、まだ追加されていません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-126">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="d75e9-127">[問題の追跡](https://github.com/dotnet/efcore/issues/22475)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-127">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

### <a name="map-entity-types-to-queries"></a><span data-ttu-id="d75e9-128">エンティティ型をクエリにマップする</span><span class="sxs-lookup"><span data-stu-id="d75e9-128">Map entity types to queries</span></span>

<span data-ttu-id="d75e9-129">エンティティ型は通常、その型に対してクエリを実行するときに、EF Core によってテーブルまたはビューの内容がプルバックされるようなテーブルまたはビューにマップされます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-129">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="d75e9-130">EF Core 5.0 では、エンティティ型を "クエリの定義" にマップできます</span><span class="sxs-lookup"><span data-stu-id="d75e9-130">EF Core 5.0 allows an entity type to mapped to a "defining query".</span></span> <span data-ttu-id="d75e9-131">(これは以前のバージョンでは部分的にサポートされていましたが、大幅に改善され、EF Core 5.0 では構文が異なります)。</span><span class="sxs-lookup"><span data-stu-id="d75e9-131">(This was partially supported in previous versions, but is much improved and has different syntax in EF Core 5.0.)</span></span>

<span data-ttu-id="d75e9-132">たとえば、2 つのテーブルについて考えてみます。1 つには最新の投稿が含まれ、もう 1 つには従来の投稿が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-132">For example, consider two tables; one with modern posts; the other with legacy posts.</span></span> <span data-ttu-id="d75e9-133">最新の投稿テーブルにはいくつかの列が追加されていますが、このアプリケーションの目的のために、最新の投稿と従来の投稿の両方を組み合わせて、必要なすべてのプロパティを持つエンティティ型にマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-133">The modern posts table has some additional columns, but for the purpose of our application we want both modern and legacy posts to be combined and mapped to an entity type with all necessary properties:</span></span>

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

<span data-ttu-id="d75e9-134">EF Core 5.0 では、`ToSqlQuery` を使用して、このエンティティ型を、両方のテーブルから行をプルして結合するクエリにマップできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-134">In EF Core 5.0, `ToSqlQuery` can be used to map this entity type to a query that pulls and combines rows from both tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

<span data-ttu-id="d75e9-135">`legacy_posts` テーブルには `Category` 列がないことに注目してください。そのため、従来のすべての投稿に対して既定値を合成します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-135">Notice that the `legacy_posts` table does not have a `Category` column, so we instead synthesize a default value for all legacy posts.</span></span>

<span data-ttu-id="d75e9-136">こうすると、このエンティティ型が、LINQ クエリの通常の方法で使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-136">This entity type can then be used in the normal way for LINQ queries.</span></span> <span data-ttu-id="d75e9-137">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-137">For example.</span></span> <span data-ttu-id="d75e9-138">LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="d75e9-138">the LINQ query:</span></span>

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

<span data-ttu-id="d75e9-139">SQLite で次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-139">Generates the following SQL on SQLite:</span></span>

```sql
SELECT "p"."Id", "p"."BlogId", "p"."Category", "p"."Name"
FROM (
    SELECT Id, Name, Category, BlogId FROM posts
    UNION ALL
    SELECT Id, Name, "Legacy", BlogId from legacy_posts
) AS "p"
INNER JOIN "Blogs" AS "b" ON "p"."BlogId" = "b"."Id"
WHERE ('Unicorn' = '') OR (instr("b"."Name", 'Unicorn') > 0)
```

<span data-ttu-id="d75e9-140">エンティティ型用に構成されたクエリが、完全な LINQ クエリの作成の開始として使用されていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-140">Notice that the query configured for the entity type is used as a starting for composing the full LINQ query.</span></span>

### <a name="event-counters"></a><span data-ttu-id="d75e9-141">イベント カウンター</span><span class="sxs-lookup"><span data-stu-id="d75e9-141">Event counters</span></span>

<span data-ttu-id="d75e9-142">[.NET イベント カウンター](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/)は、アプリケーションからパフォーマンス メトリックを効率的に公開するための手段です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-142">[.NET event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) are a way to efficiently expose performance metrics from an application.</span></span> <span data-ttu-id="d75e9-143">EF Core 5.0 には、`Microsoft.EntityFrameworkCore` カテゴリの下にイベント カウンターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-143">EF Core 5.0 includes event counters under the `Microsoft.EntityFrameworkCore` category.</span></span> <span data-ttu-id="d75e9-144">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-144">For example:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

<span data-ttu-id="d75e9-145">これは、プロセス 49496 の EF Core イベントの収集を開始するように dotnet カウンターに指示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-145">This tells dotnet counters to start collecting EF Core events for process 49496.</span></span> <span data-ttu-id="d75e9-146">これにより、コンソールに次のような出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-146">This generates output like this in the console:</span></span>

```console
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

### <a name="property-bags"></a><span data-ttu-id="d75e9-147">プロパティ バッグ</span><span class="sxs-lookup"><span data-stu-id="d75e9-147">Property bags</span></span>

<span data-ttu-id="d75e9-148">EF Core 5.0 を使用すると、同じ CLR 型を複数の異なるエンティティ型にマップできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-148">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types.</span></span> <span data-ttu-id="d75e9-149">このような型は、共有型のエンティティ型と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-149">Such types are known as shared-type entity types.</span></span> <span data-ttu-id="d75e9-150">この機能と (Preview 1 に含まれていた) インデクサー プロパティを組み合わせることで、プロパティ バッグをエンティティ型として使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-150">This feature combined with indexer properties (included in preview 1) allows property bags to be used as entity type.</span></span>

<span data-ttu-id="d75e9-151">たとえば、次の DbContext は、BCL 型 `Dictionary<string, object>` を製品とカテゴリの両方の共有型のエンティティ型として構成します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-151">For example, the DbContext below configures the BCL type `Dictionary<string, object>` as a shared-type entity type for both products and categories.</span></span>

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");
    public DbSet<Dictionary<string, object>> Categories => Set<Dictionary<string, object>>("Category");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Category", b =>
        {
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
        });

        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<decimal>("Price");
            b.IndexerProperty<int?>("CategoryId");

            b.HasOne("Category", null).WithMany();
        });
    }
}
```

<span data-ttu-id="d75e9-152">ディクショナリ オブジェクト ("プロパティ バッグ") をエンティティ インスタンスとしてコンテキストに追加し、保存できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-152">Dictionary objects ("property bags") can now be added to the context as entity instances and saved.</span></span> <span data-ttu-id="d75e9-153">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-153">For example:</span></span>

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

<span data-ttu-id="d75e9-154">これらのエンティティは、その後、通常の方法でクエリおよび更新できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-154">These entities can then be queried and updated in the normal way:</span></span>

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a><span data-ttu-id="d75e9-155">SaveChanges インターセプトとイベント</span><span class="sxs-lookup"><span data-stu-id="d75e9-155">SaveChanges interception and events</span></span>

<span data-ttu-id="d75e9-156">EF Core 5.0 には、SaveChanges が呼び出されたときにトリガーされる、.NET イベントと EF Core インターセプターの両方が導入されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-156">EF Core 5.0 introduces both .NET events and an EF Core interceptor triggered when SaveChanges is called.</span></span>

<span data-ttu-id="d75e9-157">イベントの使用は簡単です。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-157">The events are simple to use; for example:</span></span>

```csharp
context.SavingChanges += (sender, args) =>
{
    Console.WriteLine($"Saving changes for {((DbContext)sender).Database.GetConnectionString()}");
};

context.SavedChanges += (sender, args) =>
{
    Console.WriteLine($"Saved {args.EntitiesSavedCount} changes for {((DbContext)sender).Database.GetConnectionString()}");
};
```

<span data-ttu-id="d75e9-158">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-158">Notice that:</span></span>

* <span data-ttu-id="d75e9-159">イベントの送信元は `DbContext` インスタンス</span><span class="sxs-lookup"><span data-stu-id="d75e9-159">The event sender is the `DbContext` instance</span></span>
* <span data-ttu-id="d75e9-160">`SavedChanges` イベントの引数には、データベースに保存されたエンティティの数が含まれている</span><span class="sxs-lookup"><span data-stu-id="d75e9-160">The args for the `SavedChanges` event contains the number of entities saved to the database</span></span>

<span data-ttu-id="d75e9-161">インターセプターは `ISaveChangesInterceptor` で定義されますが、すべてのメソッドの実装を回避するために、`SaveChangesInterceptor` から継承すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-161">The interceptor is defined by `ISaveChangesInterceptor`, but it is often convienient to inherit from `SaveChangesInterceptor` to avoid implementing every method.</span></span> <span data-ttu-id="d75e9-162">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-162">For example:</span></span>

```csharp
public class MySaveChangesInterceptor : SaveChangesInterceptor
{
    public override InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        Console.WriteLine($"Saving changes for {eventData.Context.Database.GetConnectionString()}");

        return result;
    }

    public override ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        Console.WriteLine($"Saving changes asynchronously for {eventData.Context.Database.GetConnectionString()}");

        return new ValueTask<InterceptionResult<int>>(result);
    }
}
```

<span data-ttu-id="d75e9-163">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-163">Notice that:</span></span>

* <span data-ttu-id="d75e9-164">インターセプターには同期と非同期の両方のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-164">The interceptor has both sync and async methods.</span></span> <span data-ttu-id="d75e9-165">これは、監査サーバーへの書き込みなど、非同期 I/O を実行する必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-165">This can be useful if you need to perform async I/O, such as writing to an audit server.</span></span>
* <span data-ttu-id="d75e9-166">インターセプターでは、すべてのインターセプターに共通の `InterceptionResult` メカニズムを使用して SaveChanges をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-166">The interceptor allows SaveChanges to be skipped using the `InterceptionResult` mechanism common to all interceptors.</span></span>

<span data-ttu-id="d75e9-167">インターセプターの欠点は、構築時に DbContext に登録する必要があることです。</span><span class="sxs-lookup"><span data-stu-id="d75e9-167">The downside of interceptors is that they must be registered on the DbContext when it is being constructed.</span></span> <span data-ttu-id="d75e9-168">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-168">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

<span data-ttu-id="d75e9-169">これに対し、イベントは、いつでも DbContext インスタンスに登録できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-169">In contrast, the events can be registered on the DbContext instance at any time.</span></span>

### <a name="exclude-tables-from-migrations"></a><span data-ttu-id="d75e9-170">移行からテーブルを除外する</span><span class="sxs-lookup"><span data-stu-id="d75e9-170">Exclude tables from migrations</span></span>

<span data-ttu-id="d75e9-171">1 つのエンティティ型を複数の DbContexts にマップすると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-171">It is sometimes useful to have a single entity type mapped in multiple DbContexts.</span></span> <span data-ttu-id="d75e9-172">これは、[境界付けられたコンテキスト](https://www.martinfowler.com/bliki/BoundedContext.html)を使用する場合に特に当てはまります。境界付けられたコンテキストでは、それぞれ異なる DbContext 型を持つことが一般的です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-172">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different DbContext type for each bounded context.</span></span>

<span data-ttu-id="d75e9-173">たとえば、`User` 型は承認コンテキストとレポート コンテキストの両方で必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-173">For example, a `User` type may be needed by both an authorization context and a reporting context.</span></span> <span data-ttu-id="d75e9-174">`User` 型に変更が加えられると、両方の DbContexts の移行でデータベースの更新が試行されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-174">If a change is made to the `User` type, then migrations for both DbContexts will attempt to update the database.</span></span> <span data-ttu-id="d75e9-175">これを回避するには、いずれかのコンテキストのモデルを、その移行からテーブルを除外するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-175">To prevent this, the model for one of the contexts can be configured to exclude the table from its migrations.</span></span>

<span data-ttu-id="d75e9-176">次のコードでは、`AuthorizationContext` は `Users` テーブルに対する変更の移行を生成しますが、`ReportingContext` は生成しないため、移行の競合を防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-176">In the code below, the `AuthorizationContext` will generate migrations for changes to the `Users` table, but the `ReportingContext` will not, preventing the migrations from clashing.</span></span>

```csharp
public class AuthorizationContext : DbContext
{
    public DbSet<User> Users { get; set; }
}

public class ReportingContext : DbContext
{
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>().ToTable("Users", t => t.ExcludeFromMigrations());
    }
}
```

### <a name="required-11-dependents"></a><span data-ttu-id="d75e9-177">必要な一対一の依存</span><span class="sxs-lookup"><span data-stu-id="d75e9-177">Required 1:1 dependents</span></span>

<span data-ttu-id="d75e9-178">EF Core 3.1 では、一対一のリレーションシップの依存側は常に省略可能と見なされていました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-178">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="d75e9-179">これは、所有エンティティを使用する場合に最も顕著でした。</span><span class="sxs-lookup"><span data-stu-id="d75e9-179">This was most apparent when using owned entities.</span></span> <span data-ttu-id="d75e9-180">たとえば、次のモデルと構成について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-180">For example, consider the following model and configuration:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }

    public Address HomeAddress { get; set; }
    public Address WorkAddress { get; set; }
}

public class Address
{
    public string Line1 { get; set; }
    public string Line2 { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string Country { get; set; }
    public string Postcode { get; set; }
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

<span data-ttu-id="d75e9-181">この結果、移行によって SQLite 用に次のテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-181">This results in Migrations creating the following table for SQLite:</span></span>

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NULL,
    "HomeAddress_Region" TEXT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

<span data-ttu-id="d75e9-182">一部の `HomeAddress` プロパティは必要に応じて構成されていますが、すべての列が Null 許容であることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-182">Notice that all the columns are nullable, even though some of the `HomeAddress` properties have been configured as required.</span></span> <span data-ttu-id="d75e9-183">また、`Person` に対してクエリを実行するときに、自宅または勤務先のアドレスのすべての列が null 値の場合、EF Core では、`HomeAddress` プロパティと `WorkAddress` プロパティの両方またはいずれかが null 値のままになり、`Address` の空のインスタンスは設定されません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-183">Also, when querying for a `Person`, if all the columns for either the home or work address are null, then EF Core will leave the `HomeAddress` and/or `WorkAddress` properties as null, rather than setting an empty instance of `Address`.</span></span>

<span data-ttu-id="d75e9-184">EF Core 5.0 では、`HomeAddress` ナビゲーションを必要な依存として構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-184">In EF Core 5.0, the `HomeAddress` navigation can now be configured as as a required dependent.</span></span> <span data-ttu-id="d75e9-185">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-185">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

<span data-ttu-id="d75e9-186">移行によって作成されたテーブルには、必要な依存の必須プロパティに null 非許容の列が含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-186">The table created by Migrations will now included non-nullable columns for the required properties of the required dependent:</span></span>

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NOT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NOT NULL,
    "HomeAddress_Region" TEXT NOT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NOT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

<span data-ttu-id="d75e9-187">また、null 値の必要な依存を持つ所有者を保存しようとすると、EF Core によって例外がスローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-187">In addition, EF Core will now throw an exception if an attempt is made to save an owner which has a null required dependent.</span></span> <span data-ttu-id="d75e9-188">この例では、null 値の `HomeAddress` で `Person` を保存しようとすると、EF Core によってスローされます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-188">In this example, EF Core will throw when attempting to save a `Person` with a null `HomeAddress`.</span></span>

<span data-ttu-id="d75e9-189">必要な依存のすべての列に null 値が含まれている場合でも、最終的には、EF Core によって必要な依存のインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-189">Finally, EF Core will still create an instance of a required dependent even when all the columns for the required dependent have null values.</span></span>

### <a name="options-for-migration-generation"></a><span data-ttu-id="d75e9-190">移行生成のオプション</span><span class="sxs-lookup"><span data-stu-id="d75e9-190">Options for migration generation</span></span>

<span data-ttu-id="d75e9-191">EF Core 5.0 によって、さまざまな目的のために移行の生成をより細かく制御できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-191">EF Core 5.0 introduces greater control over generation of migrations for different purposes.</span></span> <span data-ttu-id="d75e9-192">メソッドによって実行できるタスクを次に示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-192">This includes the ability to:</span></span>

* <span data-ttu-id="d75e9-193">スクリプトまたは即時実行に対して移行が生成されているかどうかを把握する</span><span class="sxs-lookup"><span data-stu-id="d75e9-193">Know if the migration is being generated for a script or for immediate execution</span></span>
* <span data-ttu-id="d75e9-194">べき等スクリプトが生成されているかどうかを把握する</span><span class="sxs-lookup"><span data-stu-id="d75e9-194">Know if an idempotent script is being generated</span></span>
* <span data-ttu-id="d75e9-195">スクリプトでトランザクション ステートメントを除外する必要があるかどうかを把握する (以下の「_トランザクションを使用したスクリプトの移行_」を参照)</span><span class="sxs-lookup"><span data-stu-id="d75e9-195">Know if the script should exclude transaction statements (See _Migrations scripts with transactions_ below.)</span></span>

<span data-ttu-id="d75e9-196">この動作は `MigrationsSqlGenerationOptions` 列挙型によって指定され、`IMigrator.GenerateScript` に渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-196">This behavior is specified by an the `MigrationsSqlGenerationOptions` enum, which can now be passed to `IMigrator.GenerateScript`.</span></span>

<span data-ttu-id="d75e9-197">また、この作業には、必要に応じて SQL Server で `EXEC` を呼び出すことによる、より優れたべき等スクリプトの生成も含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-197">Also included in this work is better generation of idempotent scripts with calls to `EXEC` on SQL Server when needed.</span></span> <span data-ttu-id="d75e9-198">これにより、PostgreSQL など、他のデータベース プロバイダーによって生成されたスクリプトにも同様の機能強化が可能になります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-198">This work also enables similar improvements to the scripts generated by other database providers, including PostgreSQL.</span></span>

### <a name="migrations-scripts-with-transactions"></a><span data-ttu-id="d75e9-199">トランザクションを使用したスクリプトの移行</span><span class="sxs-lookup"><span data-stu-id="d75e9-199">Migrations scripts with transactions</span></span>

<span data-ttu-id="d75e9-200">移行から生成された SQL スクリプトには、移行に必要なトランザクションを開始およびコミットするステートメントが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-200">SQL scripts generated from migrations now contain statements to begin and commit transactions as appropriate for the migration.</span></span> <span data-ttu-id="d75e9-201">たとえば、次の移行スクリプトは 2 つの移行から生成されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-201">For example, the migration script below was generated from two migrations.</span></span> <span data-ttu-id="d75e9-202">各移行がトランザクション内で適用されるようになったことに注目してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-202">Notice that each migration is now applied inside a transaction.</span></span>

```sql
BEGIN TRANSACTION;
GO

CREATE TABLE [Groups] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Groups] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Members] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    [GroupId] int NULL,
    CONSTRAINT [PK_Members] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Members_Groups_GroupId] FOREIGN KEY ([GroupId]) REFERENCES [Groups] ([Id]) ON DELETE NO ACTION
);
GO

CREATE INDEX [IX_Members_GroupId] ON [Members] ([GroupId]);
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910194835_One', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
GO

BEGIN TRANSACTION;
GO

EXEC sp_rename N'[Groups].[Name]', N'GroupName', N'COLUMN';
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910195234_Two', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
```

<span data-ttu-id="d75e9-203">前のセクションで説明したように、トランザクションを別の方法で処理する必要がある場合は、このトランザクションの使用を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-203">As mentioned in the previous section, this use of transactions can be disabled if transactions need to be handled differently.</span></span>

### <a name="see-pending-migrations"></a><span data-ttu-id="d75e9-204">保留中の移行を表示する</span><span class="sxs-lookup"><span data-stu-id="d75e9-204">See pending migrations</span></span>

<span data-ttu-id="d75e9-205">この機能は、[@Psypher9](https://github.com/Psypher9) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-205">This feature was contributed from the community by [@Psypher9](https://github.com/Psypher9).</span></span> <span data-ttu-id="d75e9-206">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-206">Many thanks for the contribution!</span></span>

<span data-ttu-id="d75e9-207">`dotnet ef migrations list` コマンドを使用すると、データベースにまだ適用されていない移行が表示されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-207">The `dotnet ef migrations list` command now shows which migrations have not yet been applied to the database.</span></span> <span data-ttu-id="d75e9-208">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-208">For example:</span></span>

```console
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

<span data-ttu-id="d75e9-209">さらに、同じ機能を備えたパッケージ マネージャー コンソール用の `Get-Migration` コマンドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-209">In addition, there is now a `Get-Migration` command for the Package Manager Console with the same functionality.</span></span>

### <a name="modelbuilder-api-for-value-comparers"></a><span data-ttu-id="d75e9-210">値の比較演算子用の ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="d75e9-210">ModelBuilder API for value comparers</span></span>

<span data-ttu-id="d75e9-211">カスタムの可変型の EF Core プロパティには、プロパティの変更を正しく検出するために[値の比較演算子が必要](xref:core/modeling/value-comparers)です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-211">EF Core properties for custom mutable types [require a value comparer](xref:core/modeling/value-comparers) for property changes to be detected correctly.</span></span> <span data-ttu-id="d75e9-212">これは、型の値変換の構成の一部として指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-212">This can now be specified as part of configuring the value conversion for the type.</span></span> <span data-ttu-id="d75e9-213">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-213">For example:</span></span>

```csharp
modelBuilder
    .Entity<EntityType>()
    .Property(e => e.MyProperty)
    .HasConversion(
        v => JsonSerializer.Serialize(v, null),
        v => JsonSerializer.Deserialize<List<int>>(v, null),
        new ValueComparer<List<int>>(
            (c1, c2) => c1.SequenceEqual(c2),
            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
            c => c.ToList()));
```

### <a name="entityentry-trygetvalue-methods"></a><span data-ttu-id="d75e9-214">EntityEntry TryGetValue メソッド</span><span class="sxs-lookup"><span data-stu-id="d75e9-214">EntityEntry TryGetValue methods</span></span>

<span data-ttu-id="d75e9-215">この機能は、[@m4ss1m0g](https://github.com/m4ss1m0g) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-215">This feature was contributed from the community by [@m4ss1m0g](https://github.com/m4ss1m0g).</span></span> <span data-ttu-id="d75e9-216">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-216">Many thanks for the contribution!</span></span>

<span data-ttu-id="d75e9-217">`TryGetValue` メソッドが `EntityEntry.CurrentValues` と `EntityEntry.OriginalValues` に追加されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-217">A `TryGetValue` method has been added to `EntityEntry.CurrentValues` and `EntityEntry.OriginalValues`.</span></span> <span data-ttu-id="d75e9-218">これにより、プロパティが EF モデルでマップされているかどうかを最初に確認することなく、プロパティの値を要求することができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-218">This allows the value of a property to be requested without first checking if the property is mapped in the EF model.</span></span> <span data-ttu-id="d75e9-219">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-219">For example:</span></span>

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a><span data-ttu-id="d75e9-220">SQL Server の既定の最大バッチ サイズ</span><span class="sxs-lookup"><span data-stu-id="d75e9-220">Default max batch size for SQL Server</span></span>

<span data-ttu-id="d75e9-221">EF Core 5.0 から、SQL Server の SaveChanges の既定の最大バッチ サイズが 42 になりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-221">Starting with EF Core 5.0, the default maximum batch size for SaveChanges on SQL Server is now 42.</span></span> <span data-ttu-id="d75e9-222">よく知られているように、これは生命、宇宙、そして万物についての究極の疑問の答えでもあります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-222">As is well known, this is also the answer to the Ultimate Question of Life, the Universe, and Everything.</span></span> <span data-ttu-id="d75e9-223">しかし、この値は、[バッチ処理のパフォーマンスの分析](https://github.com/dotnet/efcore/issues/9270)を通じて得られたものであるため、おそらく偶然でしょう。</span><span class="sxs-lookup"><span data-stu-id="d75e9-223">However, this is probably a coincidence, since the value was obtained through [analysis of batching performance](https://github.com/dotnet/efcore/issues/9270).</span></span> <span data-ttu-id="d75e9-224">究極の疑問の形式を発見したとは思っていませんが、SQL Server がなぜそのように機能するのかを理解するために地球が作られたというのは、多少もっともらしく思われます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-224">We do not believe that we have discovered a form of the Ultimate Question, although it does seem somewhat plausible that the Earth was created to understand why SQL Server works the way it does.</span></span>

### <a name="default-environment-to-development"></a><span data-ttu-id="d75e9-225">開発の既定の環境</span><span class="sxs-lookup"><span data-stu-id="d75e9-225">Default environment to Development</span></span>

<span data-ttu-id="d75e9-226">EF Core コマンド ライン ツールによって、環境変数 `ASPNETCORE_ENVIRONMENT` "_および_" `DOTNET_ENVIRONMENT` が "Development" に自動的に構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-226">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="d75e9-227">これにより、汎用ホストを使用する場合のエクスペリエンスと、開発時の ASP.NET Core のエクスペリエンスが一致するようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-227">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span> <span data-ttu-id="d75e9-228">[#19903](https://github.com/dotnet/efcore/issues/19903) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-228">See [#19903](https://github.com/dotnet/efcore/issues/19903).</span></span>

### <a name="better-migrations-column-ordering"></a><span data-ttu-id="d75e9-229">移行列の順序付けの改善</span><span class="sxs-lookup"><span data-stu-id="d75e9-229">Better migrations column ordering</span></span>

<span data-ttu-id="d75e9-230">マップされていない基底クラスの列が、マップされたエンティティ型の他の列の後に並べ替えられるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-230">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="d75e9-231">これは、新しく作成されたテーブルにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-231">Note this only impacts newly created tables.</span></span> <span data-ttu-id="d75e9-232">既存のテーブルの列の順序は変更されません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-232">The column order for existing tables remains unchanged.</span></span> <span data-ttu-id="d75e9-233">[#11314](https://github.com/dotnet/efcore/issues/11314) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-233">See [#11314](https://github.com/dotnet/efcore/issues/11314).</span></span>

### <a name="query-improvements"></a><span data-ttu-id="d75e9-234">クエリの機能強化</span><span class="sxs-lookup"><span data-stu-id="d75e9-234">Query improvements</span></span>

<span data-ttu-id="d75e9-235">EF Core 5.0 RC1 には、クエリ変換の機能強化がいくつか追加されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-235">EF Core 5.0 RC1 contains some additional query translation improvements:</span></span>

* <span data-ttu-id="d75e9-236">Cosmos での `is` の翻訳-- [#16391](https://github.com/dotnet/efcore/issues/16391) を参照</span><span class="sxs-lookup"><span data-stu-id="d75e9-236">Translation of `is` on Cosmos--see [#16391](https://github.com/dotnet/efcore/issues/16391)</span></span>
* <span data-ttu-id="d75e9-237">null 値の反映を制御するためにユーザーマップ関数に注釈を付けられるようになりました-- [#19609](https://github.com/dotnet/efcore/issues/19609) を参照</span><span class="sxs-lookup"><span data-stu-id="d75e9-237">User-mapped functions can now be annotated to control null propagation--see [#19609](https://github.com/dotnet/efcore/issues/19609)</span></span>
* <span data-ttu-id="d75e9-238">条件付き集計による GroupBy の翻訳のサポート-- [#11711](https://github.com/dotnet/efcore/issues/11711) を参照</span><span class="sxs-lookup"><span data-stu-id="d75e9-238">Support for translation of GroupBy with conditional aggregates--see [#11711](https://github.com/dotnet/efcore/issues/11711)</span></span>
* <span data-ttu-id="d75e9-239">集計前の group 要素に対する Distinct 演算子の翻訳-- [#17376](https://github.com/dotnet/efcore/issues/17376) を参照</span><span class="sxs-lookup"><span data-stu-id="d75e9-239">Translation of Distinct operator over group element before aggregate--see [#17376](https://github.com/dotnet/efcore/issues/17376)</span></span>

### <a name="model-building-for-fields"></a><span data-ttu-id="d75e9-240">フィールドのモデル構築</span><span class="sxs-lookup"><span data-stu-id="d75e9-240">Model building for fields</span></span>

<span data-ttu-id="d75e9-241">最後に RC1 で、EF Core により ModelBuilder でフィールドとプロパティに対して、ラムダ メソッドを使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-241">Finally for RC1, EF Core now allows use of the lambda methods in the ModelBuilder for fields as well as properties.</span></span> <span data-ttu-id="d75e9-242">たとえば、あなたが何らかの理由でプロパティを嫌っていて、パブリック フィールドを使用することにした場合、これらのフィールドをラムダ ビルダーを使用してマッピングできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-242">For example, if you are averse to properties for some reason and decide to use public fields, then these fields can now be mapped using the lambda builders:</span></span>

```csharp
public class Post
{
    public int Id;
    public string Name;
    public string Category;
    public int BlogId;
    public Blog Blog;
}

public class Blog
{
    public int Id;
    public string Name;
    public ICollection<Post> Posts;
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
    });

    modelBuilder.Entity<Post>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
        b.Property(e => e.Category);
        b.Property(e => e.BlogId);
        b.HasOne(e => e.Blog).WithMany(e => e.Posts);
    });
}
```

<span data-ttu-id="d75e9-243">可能になったとはいえ、これを行うことは推奨されていません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-243">While this is now possible, we are certainly not recommending that you do this.</span></span> <span data-ttu-id="d75e9-244">また、これによって EF Core にフィールド マッピング機能が追加されることはなく、常に文字列メソッドを必要とする代わりにラムダ メソッドを使用できるようになるだけだということにご注意ください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-244">Also, note that this does not add any additional field mapping capabilities to EF Core, it only allows the lambda methods to be used instead of always requiring the string methods.</span></span> <span data-ttu-id="d75e9-245">フィールドが公開されることはめったにないので、これはあまり役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-245">This is seldom useful since fields are rarely public.</span></span>

## <a name="preview-8"></a><span data-ttu-id="d75e9-246">Preview 8</span><span class="sxs-lookup"><span data-stu-id="d75e9-246">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="d75e9-247">Table-Per-Type (TPT) のマッピング</span><span class="sxs-lookup"><span data-stu-id="d75e9-247">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="d75e9-248">EF Core の既定では、.NET 型の継承階層が 1 つのデータベース テーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-248">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="d75e9-249">これは、Table-Per-Hierarchy (TPH) のマッピングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-249">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="d75e9-250">EF Core 5.0 を使用すると、継承階層の各 .NET 型を別のデータベース テーブルにマップすることもできます。これは Table-Per-Type (TPT) のマッピングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-250">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="d75e9-251">たとえば、次のようにマップされた階層を持つこのモデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="d75e9-251">For example, consider this model with a mapped hierarchy:</span></span>

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EducationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="d75e9-252">EF Core の既定では、これが 1 つのテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-252">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="d75e9-253">一方、各エンティティ型を別のテーブルにマップすると、代わりに型ごとに 1 つのテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-253">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

<span data-ttu-id="d75e9-254">前述の外部キー制約の作成は、プレビュー 8 のコードを分岐した後に追加されたことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-254">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="d75e9-255">エンティティ型を別のテーブルにマップするには、マッピング属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-255">Entity types can be mapped to different tables using mapping attributes:</span></span>

```csharp
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="d75e9-256">または `ModelBuilder` 構成を使用します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-256">Or using `ModelBuilder` configuration:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="d75e9-257">ドキュメントは、イシュー [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-257">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="d75e9-258">移行:SQLite テーブルを再構築する</span><span class="sxs-lookup"><span data-stu-id="d75e9-258">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="d75e9-259">他のデータベースと比較すると、SQLite のスキーマ操作機能は比較的限られています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-259">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="d75e9-260">たとえば、既存のテーブルから列を削除するには、テーブル全体を削除して再作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-260">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="d75e9-261">EF Core 5.0 の移行では、必要なスキーマ変更のためにテーブルの自動再構築がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-261">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="d75e9-262">たとえば、エンティティ型 `Unicorn` に対して作成された `Unicorns` テーブルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="d75e9-262">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```csharp
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

<span data-ttu-id="d75e9-263">次に、ユニコーンの年齢を格納することは非常に失礼だと考えられることがわかったので、そのプロパティを削除し、新しい移行を追加して、データベースを更新しましょう。</span><span class="sxs-lookup"><span data-stu-id="d75e9-263">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="d75e9-264">EF Core 3.1 を使用している場合は列を削除できないため、この更新は失敗します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-264">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="d75e9-265">EF Core 5.0 ではそうではなく、移行によってテーブルが再構築されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-265">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

<span data-ttu-id="d75e9-266">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-266">Notice that:</span></span>

* <span data-ttu-id="d75e9-267">新しいテーブルに必要なスキーマを使用して一時テーブルが作成されます</span><span class="sxs-lookup"><span data-stu-id="d75e9-267">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="d75e9-268">データは現在のテーブルから一時テーブルにコピーされます</span><span class="sxs-lookup"><span data-stu-id="d75e9-268">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="d75e9-269">外部キーの適用はオフに切り替えられます</span><span class="sxs-lookup"><span data-stu-id="d75e9-269">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="d75e9-270">現在のテーブルはドロップされます</span><span class="sxs-lookup"><span data-stu-id="d75e9-270">The current table is dropped</span></span>
* <span data-ttu-id="d75e9-271">一時テーブルは新しいテーブルとして名前が変更されます</span><span class="sxs-lookup"><span data-stu-id="d75e9-271">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="d75e9-272">ドキュメントは、イシュー [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-272">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="d75e9-273">テーブル値関数</span><span class="sxs-lookup"><span data-stu-id="d75e9-273">Table-valued functions</span></span>

<span data-ttu-id="d75e9-274">この機能は、[@pmiddleton](https://github.com/pmiddleton) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-274">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="d75e9-275">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-275">Many thanks for the contribution!</span></span>

<span data-ttu-id="d75e9-276">EF Core 5.0 には、.NET メソッドをテーブル値関数 (TVF) にマップするための最上級のサポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-276">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="d75e9-277">これらの関数を LINQ クエリで使用することができます。また、この関数の結果に対する追加の構成も SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-277">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="d75e9-278">たとえば、SQL Server データベースで定義されている次の TVF について考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="d75e9-278">For example, consider this TVF defined in a SQL Server database:</span></span>

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

<span data-ttu-id="d75e9-279">EF Core モデルでは、この TVF を使用するために 2 つのエンティティ型が必要です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-279">The EF Core model requires two entity types to use this TVF:</span></span>

* <span data-ttu-id="d75e9-280">通常の方法で Employees テーブルにマップする `Employee` 型</span><span class="sxs-lookup"><span data-stu-id="d75e9-280">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="d75e9-281">TVF から返されるシェイプと一致する `Report` 型</span><span class="sxs-lookup"><span data-stu-id="d75e9-281">A `Report` type that matches the shape returned by the TVF</span></span>

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```csharp
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="d75e9-282">これらの型は、EF Core モデルに含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-282">These types must be included in the EF Core model:</span></span>

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="d75e9-283">`Report` には主キーがないため、そのように構成する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-283">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="d75e9-284">最後に、.NET メソッドをデータベースの TVF にマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-284">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="d75e9-285">このメソッドは、新しい `FromExpression` メソッドを使用して DbContext に対して定義できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-285">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="d75e9-286">このメソッドには、上記で定義された TVF に一致するパラメーターと戻り値の型が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-286">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="d75e9-287">次に、メソッドは OnModelCreating の EF Core モデルに追加されます</span><span class="sxs-lookup"><span data-stu-id="d75e9-287">The method is then added to the EF Core model in OnModelCreating:</span></span>

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="d75e9-288">(ここでラムダを使用すると、`MethodInfo` を EF Core に簡単に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-288">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="d75e9-289">メソッドに渡された引数は無視されます)。</span><span class="sxs-lookup"><span data-stu-id="d75e9-289">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="d75e9-290">これで、`GetReports` を呼び出して結果を構成するクエリを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-290">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="d75e9-291">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-291">For example:</span></span>

```csharp
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="d75e9-292">SQL Server では、これは次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-292">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="d75e9-293">SQL では `Employees` テーブルがルートとされ、`GetReports` が呼び出され、関数の結果に WHERE 句が追加されることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-293">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="d75e9-294">柔軟なクエリと更新マップ</span><span class="sxs-lookup"><span data-stu-id="d75e9-294">Flexible query/update mapping</span></span>

<span data-ttu-id="d75e9-295">EF Core 5.0 では、同じエンティティ型を異なるデータベース オブジェクトにマップできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-295">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="d75e9-296">これらのオブジェクトは、テーブル、ビュー、または関数です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-296">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="d75e9-297">たとえば、エンティティ型はデータベース ビューとデータベース テーブルの両方にマップできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-297">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="d75e9-298">既定では、EF Core はビューからクエリを実行し、更新をテーブルに送信します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-298">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="d75e9-299">たとえば、次のコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-299">For example, executing the following code:</span></span>

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="d75e9-300">ビューに対してクエリを実行し、テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-300">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="d75e9-301">コンテキスト全体の分割クエリ構成</span><span class="sxs-lookup"><span data-stu-id="d75e9-301">Context-wide split-query configuration</span></span>

<span data-ttu-id="d75e9-302">分割クエリ (以下を参照してください) は、DbContext によって実行されるクエリの既定値として構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-302">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="d75e9-303">この構成はリレーショナル プロバイダーの場合にのみ使用できるため、`UseProvider` 構成の一部として指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-303">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="d75e9-304">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-304">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="d75e9-305">ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-305">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="d75e9-306">PhysicalAddress のマッピング</span><span class="sxs-lookup"><span data-stu-id="d75e9-306">PhysicalAddress mapping</span></span>

<span data-ttu-id="d75e9-307">この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-307">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="d75e9-308">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-308">Many thanks for the contribution!</span></span>

<span data-ttu-id="d75e9-309">この構成はリレーショナル プロバイダーでのみ使用できるた標準の .NET [PhysicalAddress クラス](/dotnet/api/system.net.networkinformation.physicaladdress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-309">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="d75e9-310">詳細については、以下の `IPAddress` の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-310">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="d75e9-311">Preview 7</span><span class="sxs-lookup"><span data-stu-id="d75e9-311">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="d75e9-312">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="d75e9-312">DbContextFactory</span></span>

<span data-ttu-id="d75e9-313">EF Core 5.0 には、アプリケーションの依存関係挿入 (D.I.) コンテナーに DbContext インスタンスを作成するファクトリを登録する `AddDbContextFactory` と `AddPooledDbContextFactory` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-313">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="d75e9-314">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-314">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="d75e9-315">これにより、ASP.NET Core コントローラーなどのアプリケーション サービスが、サービス コンストラクターの `IDbContextFactory<TContext>` に依存できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-315">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="d75e9-316">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-316">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="d75e9-317">これにより、DbContext インスタンスを必要に応じて作成および使用できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-317">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="d75e9-318">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-318">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="d75e9-319">なお、この方法で作成された DbContext インスタンスは、アプリケーションのサービス プロバイダーには管理 "_されず_"、従ってアプリケーションによって破棄される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-319">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="d75e9-320">この分離は、`IDbContextFactory` の使用が推奨される Blazor アプリケーションで非常に便利ですが、他のシナリオでも役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-320">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="d75e9-321">DbContext インスタンスは、`AddPooledDbContextFactory` を呼び出すことによってプールできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-321">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="d75e9-322">このプールは、`AddDbContextPool` の場合と同じように動作しますが、同じ制限もあります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-322">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="d75e9-323">ドキュメントは、イシュー [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-323">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="d75e9-324">DbContext の状態のリセット</span><span class="sxs-lookup"><span data-stu-id="d75e9-324">Reset DbContext state</span></span>

<span data-ttu-id="d75e9-325">EF Core 5.0 には、追跡対象のすべてのエンティティの DbContext をクリアする `ChangeTracker.Clear()` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-325">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="d75e9-326">これは、作業単位ごとに有効期間が短い新しいコンテキスト インスタンスを作成するベスト プラクティスを使用している場合には通常不要です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-326">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="d75e9-327">ただし、DbContext インスタンスの状態をリセットする必要がある場合、この新しい `Clear()` メソッドを使用すると、すべてのエンティティを一括でデタッチするよりも、パフォーマンスと堅牢性が向上します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-327">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="d75e9-328">ドキュメントは、イシュー [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-328">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="d75e9-329">ストアで生成された既定値に新しいパターン</span><span class="sxs-lookup"><span data-stu-id="d75e9-329">New pattern for store-generated defaults</span></span>

<span data-ttu-id="d75e9-330">EF Core では、既定の制約値がある列に、値を明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-330">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="d75e9-331">EF Core では、このセンチネルとして、プロパティ型に CLR の既定値を使用しています。つまり、その値が CLR の既定でない場合はそれが挿入され、それ以外の場合は、データベースの既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-331">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="d75e9-332">これは、既定の CLR が適切なセンチネルではない型 (特に、`bool` のプロパティ) で問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-332">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="d75e9-333">EF Core 5.0 で、次のようなケースで、バッキング フィールドに null 値が許容されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-333">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="d75e9-334">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-334">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="d75e9-335">なお、null 値はバッキング フィールドでは許容されますが、公開されているプロパティでは許容されません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-335">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="d75e9-336">これにより、エンティティ型の公開サーフェスに影響を与えることなく、センチネル値を `null` にできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-336">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="d75e9-337">この場合、`IsValid` が設定されない場合、バッキング フィールドは null のままであるため、データベースの既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-337">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="d75e9-338">`true` または `false` のいずれかが設定される場合、この値はデータベースに明示的に保存されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-338">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="d75e9-339">ドキュメントは、イシュー [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-339">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="d75e9-340">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="d75e9-340">Cosmos partition keys</span></span>

<span data-ttu-id="d75e9-341">EF Core では、EF モデルに Cosmos パーティション キーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-341">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="d75e9-342">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-342">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="d75e9-343">Preview 7 以降では、一部のクエリのパフォーマンスの向上のために、パーティション キーがエンティティ型の PK に含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-343">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="d75e9-344">ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-344">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="d75e9-345">Cosmos の構成</span><span class="sxs-lookup"><span data-stu-id="d75e9-345">Cosmos configuration</span></span>

<span data-ttu-id="d75e9-346">EF Core 5.0 では、Cosmos と Cosmos への接続の構成が改善されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-346">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="d75e9-347">以前は、EF Core を Cosmos データベースに接続する場合に、エンドポイントとキーを明示的に指定する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-347">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="d75e9-348">EF Core 5.0 では、代わりに接続文字列を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-348">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="d75e9-349">また、EF Core 5.0 では、WebProxy インスタンスを明示的に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-349">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="d75e9-350">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-350">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="d75e9-351">その他、多くのタイムアウト値、制限なども構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-351">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="d75e9-352">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-352">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="d75e9-353">最後に、一般により互換性がある、`ConnectionMode.Gateway` が既定の接続モードになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-353">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="d75e9-354">ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-354">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="d75e9-355">DbContext のスキャフォールディングの単数化</span><span class="sxs-lookup"><span data-stu-id="d75e9-355">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="d75e9-356">以前は EF Core で、既存のデータベースから DbContext をスキャフォールディングする場合、データベース内のテーブル名と一致するエンティティ型名が作成されていました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-356">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="d75e9-357">たとえば、`People` テーブルと `Addresses` テーブルには、`People` と `Addresses` という名前のエンティティ型が生成されていました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-357">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="d75e9-358">この動作は、以前のリリースでは、複数形化サービスの登録によって構成できました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-358">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="d75e9-359">EF Core 5.0 では、既定の複数形化サービスとして [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) パッケージが使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-359">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="d75e9-360">つまり、`People` テーブルと `Addresses` テーブルは、`Person` と `Address` という名前のエンティティ型にリバース エンジニアリングされます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-360">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="d75e9-361">セーブポイント</span><span class="sxs-lookup"><span data-stu-id="d75e9-361">Savepoints</span></span>

<span data-ttu-id="d75e9-362">EF Core では、複数の操作を実行するトランザクションをより細かく制御する、[セーブポイント](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks)がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-362">EF Core now supports [savepoints](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="d75e9-363">セーブポイントは、手動で作成、解放、ロールバックすることができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-363">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="d75e9-364">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-364">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="d75e9-365">また、`SaveChanges` の実行に失敗すると、EF Core では最後のセーブポイントにロールバックされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-365">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="d75e9-366">これにより、トランザクション全体を再実行しなくても、SaveChanges を再実行できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-366">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="d75e9-367">ドキュメントは、イシュー [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-367">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="d75e9-368">Preview 6</span><span class="sxs-lookup"><span data-stu-id="d75e9-368">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="d75e9-369">関連するコレクションのクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="d75e9-369">Split queries for related collections</span></span>

<span data-ttu-id="d75e9-370">EF Core 3.0 以降、EF Core では、LINQ クエリごとに 1 つの SQL クエリが常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-370">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="d75e9-371">これにより、使用中のトランザクション モードの制約内で返されるデータの整合性が確保されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-371">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="d75e9-372">ただし、クエリで `Include` またはプロジェクションを使用して複数の関連コレクションを戻すと、この処理が非常に遅くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-372">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="d75e9-373">EF Core 5.0 では、関連するコレクションを含む単一の LINQ クエリを複数の SQL クエリに分割できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-373">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="d75e9-374">これにより、パフォーマンスを大幅に向上させることができますが、2 つのクエリ間でデータが変更された場合に返される結果に不整合が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-374">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="d75e9-375">シリアル化可能な、またはスナップショットのトランザクションを使用すると、これを軽減し、分割されたクエリとの整合性を維持できますが、他のパフォーマンス コストと動作の違いが生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-375">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="d75e9-376">インクルードを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="d75e9-376">Split queries with Include</span></span>

<span data-ttu-id="d75e9-377">たとえば、`Include` を使用して 2 つのレベルの関連コレクションを取得するクエリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-377">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="d75e9-378">既定では、SQLite プロバイダーの使用時に、EF Core によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-378">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="d75e9-379">新しい `AsSplitQuery` API を使用して、この動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-379">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="d75e9-380">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-380">For example:</span></span>

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="d75e9-381">AsSplitQuery は、すべてのリレーショナル データベース プロバイダーで使用でき、AsNoTracking と同様に、クエリ内の任意の場所で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-381">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="d75e9-382">EF Core によって、次の 3 つの SQL クエリが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-382">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="d75e9-383">クエリ ルートに対するすべての操作がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-383">All operations on the query root are supported.</span></span> <span data-ttu-id="d75e9-384">これには、OrderBy/Skip/Take、結合操作、FirstOrDefault、および類似した単一結果選択操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-384">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="d75e9-385">OrderBy/Skip/Take でフィルター処理されたインクルードは、Preview 6 ではサポートされませんが、デイリー ビルドで使用でき、Preview 7 には含まれます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-385">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="d75e9-386">コレクション プロジェクションを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="d75e9-386">Split queries with collection projections</span></span>

<span data-ttu-id="d75e9-387">`AsSplitQuery` は、コレクションがプロジェクションに読み込まれるときにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-387">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="d75e9-388">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-388">For example:</span></span>

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="d75e9-389">この LINQ クエリでは、SQLite プロバイダーを使用するときに、次の 2 つの SQL クエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-389">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="d75e9-390">サポートされるのはコレクションの具体化のみであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-390">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="d75e9-391">上記のケースの `e.Albums` 後のコンポジションでは、分割クエリは発生しません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-391">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="d75e9-392">この領域の機能強化は、[#21234](https://github.com/dotnet/efcore/issues/21234) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-392">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="d75e9-393">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="d75e9-393">IndexAttribute</span></span>

<span data-ttu-id="d75e9-394">新しい IndexAttribute をエンティティ型に配置して、1 つの列のインデックスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-394">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="d75e9-395">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-395">For example:</span></span>

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="d75e9-396">SQL Server の場合、移行によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-396">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="d75e9-397">IndexAttribute を使用して、複数の列にまたがるインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-397">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="d75e9-398">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-398">For example:</span></span>

```csharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="d75e9-399">SQL Server の場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-399">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="d75e9-400">ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-400">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="d75e9-401">クエリ変換の例外の改善</span><span class="sxs-lookup"><span data-stu-id="d75e9-401">Improved query translation exceptions</span></span>

<span data-ttu-id="d75e9-402">クエリ変換が失敗したときに生成される例外メッセージの改善を続けています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-402">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="d75e9-403">たとえば、次のクエリでは、マップされていないプロパティ `IsSigned` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-403">For example, this query uses the unmapped property `IsSigned`:</span></span>

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="d75e9-404">EF Core は、`IsSigned` がマップされていないために変換が失敗したことを示す次の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="d75e9-404">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
   .Where(a => a.IsSigned)' could not be translated. Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed. Possibly the specified member is not mapped. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

<span data-ttu-id="d75e9-405">同様に、カルチャに依存するセマンティクスを使用して文字列の比較を変換しようとしたときに、より適切な例外メッセージが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-405">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="d75e9-406">たとえば、次のクエリは `StringComparison.CurrentCulture` を使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="d75e9-406">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="d75e9-407">EF Core では、次の例外がスローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-407">EF Core will now throw the following exception:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
     .Where(a => a.Name.Equals(
         value: "The Unicorns",
         comparisonType: CurrentCulture))' could not be translated. Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported. See <https://go.microsoft.com/fwlink/?linkid=2129535> for more information. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

### <a name="specify-transaction-id"></a><span data-ttu-id="d75e9-408">トランザクション ID を指定する</span><span class="sxs-lookup"><span data-stu-id="d75e9-408">Specify transaction ID</span></span>

<span data-ttu-id="d75e9-409">この機能は、[@Marusyk](https://github.com/Marusyk) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-409">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="d75e9-410">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-410">Many thanks for the contribution!</span></span>

<span data-ttu-id="d75e9-411">EF Core では、呼び出し全体のトランザクションの相関関係に対するトランザクション ID が公開されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-411">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="d75e9-412">この ID は通常、トランザクションの開始時に EF Core によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-412">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="d75e9-413">アプリケーションがトランザクションを代わりに開始する場合、この機能により、アプリケーションが、使用されるすべての場所で正しく関連付けられるように、トランザクション ID を明示的に設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-413">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="d75e9-414">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-414">For example:</span></span>

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="d75e9-415">IPAddress マッピング</span><span class="sxs-lookup"><span data-stu-id="d75e9-415">IPAddress mapping</span></span>

<span data-ttu-id="d75e9-416">この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-416">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="d75e9-417">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-417">Many thanks for the contribution!</span></span>

<span data-ttu-id="d75e9-418">標準の .NET [IPAddress クラス](/dotnet/api/system.net.ipaddress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-418">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="d75e9-419">たとえば、次のエンティティ型をマップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-419">For example, consider mapping this entity type:</span></span>

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="d75e9-420">SQL Server では、移行によって次のテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-420">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="d75e9-421">これで、通常の方法でエンティティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-421">Entities can then be added in the normal way:</span></span>

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="d75e9-422">結果として生成される SQL では、正規化された IPv4 または IPv6 のアドレスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-422">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="d75e9-423">スキャフォールディング時の OnConfiguring を除外する</span><span class="sxs-lookup"><span data-stu-id="d75e9-423">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="d75e9-424">DbContext が既存のデータベースからスキャフォールディングされると、EF Core の既定では、接続文字列を使用して OnConfiguring オーバーロードが作成され、コンテキストがすぐに使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-424">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="d75e9-425">ただし、これは、OnConfiguring を持つ部分クラスが既に存在する場合や、コンテキストを他の方法で構成する場合には役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-425">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="d75e9-426">これに対処するため、スキャフォールディング コマンドに、OnConfiguring の生成を省略するように指示できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-426">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="d75e9-427">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-427">For example:</span></span>

```console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="d75e9-428">または、パッケージ マネージャー コンソールで:</span><span class="sxs-lookup"><span data-stu-id="d75e9-428">Or in the Package Manager Console:</span></span>

```console
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="d75e9-429">[名前付き接続文字列と、ユーザー シークレットのようなセキュリティで保護されたストレージ](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d75e9-429">Note that we recommend using [a named connection string and secure storage like User Secrets](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="d75e9-430">文字列の FirstOrDefault の変換</span><span class="sxs-lookup"><span data-stu-id="d75e9-430">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="d75e9-431">この機能は、[@dvoreckyaa](https://github.com/dvoreckyaa) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-431">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="d75e9-432">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-432">Many thanks for the contribution!</span></span>

<span data-ttu-id="d75e9-433">FirstOrDefault と文字列内の文字に対する類似の演算子が変換されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-433">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="d75e9-434">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="d75e9-434">For example, this LINQ query:</span></span>

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="d75e9-435">は、SQL Server を使用すると、次の SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-435">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="d75e9-436">CASE ブロックの簡略化</span><span class="sxs-lookup"><span data-stu-id="d75e9-436">Simplify case blocks</span></span>

<span data-ttu-id="d75e9-437">EF Core は、CASE ブロックを使用してより良いクエリを生成するようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-437">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="d75e9-438">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="d75e9-438">For example, this LINQ query:</span></span>

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="d75e9-439">は、SQL Server では、正式には次のように変換されていました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-439">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

<span data-ttu-id="d75e9-440">しかし、今後は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-440">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="d75e9-441">Preview 5</span><span class="sxs-lookup"><span data-stu-id="d75e9-441">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="d75e9-442">データベースの照合順序</span><span class="sxs-lookup"><span data-stu-id="d75e9-442">Database collations</span></span>

<span data-ttu-id="d75e9-443">データベースにおける既定の照合順序を EF モデルで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-443">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="d75e9-444">これは、データベースの作成時に照合順序を設定するために生成された移行にフローします。</span><span class="sxs-lookup"><span data-stu-id="d75e9-444">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="d75e9-445">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-445">For example:</span></span>

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="d75e9-446">次に、移行では、SQL Server 上にデータベースを作成するために次のものが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-446">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="d75e9-447">特定のデータベース列に使用する照合順序を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-447">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="d75e9-448">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-448">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="d75e9-449">移行を使用しないものについては、DbContext をスキャフォールディングする際に、照合順序がデータベースからリバースエンジニアリングされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-449">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="d75e9-450">最後に、`EF.Functions.Collate()` では、さまざまな照合順序を使用したアドホック クエリが可能です。</span><span class="sxs-lookup"><span data-stu-id="d75e9-450">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="d75e9-451">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-451">For example:</span></span>

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="d75e9-452">これにより、SQL Server に対して次のクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-452">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="d75e9-453">アドホック照合順序はデータベースのパフォーマンスに悪影響を及ぼす可能性があるため、注意して使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-453">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="d75e9-454">ドキュメントは、イシュー [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-454">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="d75e9-455">引数を IDesignTimeDbContextFactory にフローする</span><span class="sxs-lookup"><span data-stu-id="d75e9-455">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="d75e9-456">引数は、コマンド ラインから [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1)の `CreateDbContext` メソッドにフローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-456">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span></span> <span data-ttu-id="d75e9-457">たとえば、これが開発ビルドであることを示すために、カスタム引数 (`dev` など) をコマンド ラインで渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-457">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```console
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="d75e9-458">この引数は次にファクトリにフローされ、そこではコンテキストの作成方法および初期化方法を制御するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-458">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="d75e9-459">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-459">For example:</span></span>

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="d75e9-460">ドキュメントは、イシュー [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-460">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="d75e9-461">識別子の解決を使用した追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="d75e9-461">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="d75e9-462">識別子の解決を実行するように追跡なしのクエリ構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-462">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="d75e9-463">たとえば、次のクエリでは、各ブログの主キーが同じである場合でも、投稿ごとに新しいブログ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-463">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="d75e9-464">ただし、通常は速度が少し低下し、常により多くのメモリを使用することになりますが、1 つのブログ インスタンスだけを確実に作成するように、このクエリを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-464">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="d75e9-465">これは、追跡なしのクエリに対してのみ有効です。すべての追跡クエリでは既にこの動作が行われているからです。</span><span class="sxs-lookup"><span data-stu-id="d75e9-465">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="d75e9-466">また、API レビューに従って、`PerformIdentityResolution` 構文も変更されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-466">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="d75e9-467">[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-467">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="d75e9-468">ドキュメントは、イシュー [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-468">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="d75e9-469">格納された (保存された) 計算列</span><span class="sxs-lookup"><span data-stu-id="d75e9-469">Stored (persisted) computed columns</span></span>

<span data-ttu-id="d75e9-470">ほとんどのデータベースでは、計算後に計算列の値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-470">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="d75e9-471">これによってディスク領域が消費されますが、計算列の計算は、その値が取得されるたびではなく、更新時に 1 回だけ行われます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-471">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="d75e9-472">また、これにより、一部のデータベースについて列のインデックスを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-472">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="d75e9-473">EF Core 5.0 では、計算列を格納済みとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-473">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="d75e9-474">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-474">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="d75e9-475">SQLite の計算列</span><span class="sxs-lookup"><span data-stu-id="d75e9-475">SQLite computed columns</span></span>

<span data-ttu-id="d75e9-476">EF Core では、SQLite データベースの計算列がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-476">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="d75e9-477">Preview 4</span><span class="sxs-lookup"><span data-stu-id="d75e9-477">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="d75e9-478">モデルでデータベースの有効桁数または小数点以下桁数を構成する</span><span class="sxs-lookup"><span data-stu-id="d75e9-478">Configure database precision/scale in model</span></span>

<span data-ttu-id="d75e9-479">モデル ビルダーを使用して、プロパティの有効桁数と小数点以下桁数を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-479">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="d75e9-480">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-480">For example:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="d75e9-481">有効桁数と小数点以下桁数は、"decimal(16,4)" のように、完全なデータベースの種類を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-481">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="d75e9-482">ドキュメントは、イシュー [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-482">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="d75e9-483">SQL Server インデックスの指定 FILL FACTOR を指定する</span><span class="sxs-lookup"><span data-stu-id="d75e9-483">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="d75e9-484">SQL Server でインデックスを作成するときに、FILL FACTOR を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-484">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="d75e9-485">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-485">For example:</span></span>

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="d75e9-486">Preview 3</span><span class="sxs-lookup"><span data-stu-id="d75e9-486">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="d75e9-487">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="d75e9-487">Filtered Include</span></span>

<span data-ttu-id="d75e9-488">Include メソッドでは、インクルードされるエンティティのフィルター処理がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-488">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="d75e9-489">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-489">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="d75e9-490">このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-490">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="d75e9-491">Skip と Take を使用して、インクルードされるエンティティの数を減らすこともできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-491">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="d75e9-492">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-492">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```

<span data-ttu-id="d75e9-493">このクエリでは、ブログ 1 件あたり最大 5 件の投稿が含まれるブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-493">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="d75e9-494">詳細については、[インクルードに関するドキュメント](xref:core/querying/related-data#filtered-include)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-494">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="d75e9-495">ナビゲーション プロパティの新しい ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="d75e9-495">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="d75e9-496">ナビゲーション プロパティは、主に[リレーションシップを定義する](xref:core/modeling/relationships)ときに構成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-496">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="d75e9-497">ただし、ナビゲーション プロパティに追加の構成が必要な場合は、新しい `Navigation` メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-497">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="d75e9-498">たとえば、フィールドが規約によって見つからない場合にナビゲーションにバッキング フィールドを設定するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-498">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="d75e9-499">`Navigation` API は、リレーションシップの構成に代わるものではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-499">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="d75e9-500">代わりに、既に検出または定義されたリレーションシップで、ナビゲーション プロパティの追加構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-500">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="d75e9-501">[ナビゲーション プロパティの構成に関するドキュメント](xref:core/modeling/relationships#configuring-navigation-properties)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-501">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="d75e9-502">名前空間と接続文字列の新しいコマンドライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="d75e9-502">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="d75e9-503">移行とスキャフォールディングでは、コマンド ラインで名前空間を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-503">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="d75e9-504">たとえば、別の名前空間にコンテキスト クラスとモデル クラスを配置するデータベースをリバース エンジニアリングするには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-504">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="d75e9-505">詳細については、「[移行](xref:core/managing-schemas/migrations/index#namespaces)」と「[リバース エンジニアリング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)」のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-505">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="d75e9-506">また、接続文字列を `database-update` コマンドに渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-506">Also, a connection string can now be passed to the `database-update` command:</span></span>

```dotnetcli
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="d75e9-507">詳細については、[ツールのドキュメント](xref:core/cli/dotnet#dotnet-ef-database-update)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d75e9-507">See the [Tools documentation](xref:core/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="d75e9-508">VS パッケージ マネージャー コンソールで使用される PowerShell コマンドにも、同等のパラメーターが追加されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-508">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="d75e9-509">EnableDetailedErrors が返された</span><span class="sxs-lookup"><span data-stu-id="d75e9-509">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="d75e9-510">パフォーマンス上の理由から、EF では、データベースから値を読み取るときに追加の null チェックは行われません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-510">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="d75e9-511">これにより、予期しない null が検出された場合に、根本原因を突き止めることが困難な例外が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-511">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="d75e9-512">`EnableDetailedErrors` を使用すると、クエリに null チェックがさらに追加されます。パフォーマンスのオーバーヘッドが小さいため、これらのエラーでは、根本原因まで簡単に追跡できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-512">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="d75e9-513">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-513">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="d75e9-514">ドキュメントは、イシュー [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-514">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="d75e9-515">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="d75e9-515">Cosmos partition keys</span></span>

<span data-ttu-id="d75e9-516">指定されたクエリに使用するパーティション キーをクエリで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-516">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="d75e9-517">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-517">For example:</span></span>

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="d75e9-518">ドキュメントは、イシュー [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-518">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="d75e9-519">SQL Server DATALENGTH 関数のサポート</span><span class="sxs-lookup"><span data-stu-id="d75e9-519">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="d75e9-520">これには、新しい `EF.Functions.DataLength` メソッドを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-520">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="d75e9-521">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-521">For example:</span></span>

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="d75e9-522">Preview 2</span><span class="sxs-lookup"><span data-stu-id="d75e9-522">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="d75e9-523">C# 属性を利用し、プロパティ バッキング フィールドを指定する</span><span class="sxs-lookup"><span data-stu-id="d75e9-523">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="d75e9-524">C# 属性を利用し、プロパティのバッキング フィールドを指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-524">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="d75e9-525">この属性を利用することで、EF Core では、バッキング フィールドが自動的に検出できないときでさえ、通常のようにバッキング フィールドとの間で読み書きできます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-525">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="d75e9-526">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-526">For example:</span></span>

```csharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="d75e9-527">ドキュメントは、イシュー [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-527">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="d75e9-528">完全な識別子マッピング</span><span class="sxs-lookup"><span data-stu-id="d75e9-528">Complete discriminator mapping</span></span>

<span data-ttu-id="d75e9-529">EF Core では、[継承階層の TPH マッピング](xref:core/modeling/inheritance)のために識別子列が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-529">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](xref:core/modeling/inheritance).</span></span> <span data-ttu-id="d75e9-530">識別子に利用できるすべての値が EF Core で認識されている限り、パフォーマンスを一部改善できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-530">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="d75e9-531">EF Core 5.0 にはこのような機能強化が実装されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-531">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="d75e9-532">たとえば、EF Core の前バージョンでは、ある階層内のすべての型を返すクエリにこの SQL が常に生成されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-532">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="d75e9-533">EF Core 5.0 では現在、完全な識別子マッピングが構成されたとき、次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-533">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="d75e9-534">プレビュー 3 以降、これが既定の動作になります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-534">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="d75e9-535">Microsoft.Data.Sqlite のパフォーマンス向上</span><span class="sxs-lookup"><span data-stu-id="d75e9-535">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="d75e9-536">SQLIte でパフォーマンスが 2 点改善されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-536">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="d75e9-537">GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-537">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="d75e9-538">SqliteConnection の初期化がゆっくりになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-538">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="d75e9-539">これらの機能強化は ADO.NET Microsoft.Data.Sqlite プロバイダーにあり、そのため、EF Core の外でもパフォーマンスが改善されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-539">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="d75e9-540">Preview 1</span><span class="sxs-lookup"><span data-stu-id="d75e9-540">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="d75e9-541">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="d75e9-541">Simple logging</span></span>

<span data-ttu-id="d75e9-542">この機能により、EF6 の `Database.Log` に似た機能が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-542">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="d75e9-543">つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-543">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="d75e9-544">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-544">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="d75e9-545">追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-545">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="d75e9-546">生成された SQL の取得方法がシンプルに</span><span class="sxs-lookup"><span data-stu-id="d75e9-546">Simple way to get generated SQL</span></span>

<span data-ttu-id="d75e9-547">EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-547">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="d75e9-548">暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-548">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="d75e9-549">追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-549">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="d75e9-550">エンティティにキーがないことを示すために C# 属性を使用</span><span class="sxs-lookup"><span data-stu-id="d75e9-550">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="d75e9-551">新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-551">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="d75e9-552">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-552">For example:</span></span>

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="d75e9-553">ドキュメントは、イシュー [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-553">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="d75e9-554">初期化された DbContext で接続または接続文字列が変更可能に</span><span class="sxs-lookup"><span data-stu-id="d75e9-554">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="d75e9-555">接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-555">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="d75e9-556">また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-556">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="d75e9-557">この機能により、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-557">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="d75e9-558">ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-558">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="d75e9-559">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="d75e9-559">Change-tracking proxies</span></span>

<span data-ttu-id="d75e9-560">EF Core で、[INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) および [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) を自動的に実装するランタイム プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-560">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="d75e9-561">これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="d75e9-561">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="d75e9-562">ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません</span><span class="sxs-lookup"><span data-stu-id="d75e9-562">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="d75e9-563">ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-563">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="d75e9-564">強化されたデバッグ ビュー</span><span class="sxs-lookup"><span data-stu-id="d75e9-564">Enhanced debug views</span></span>

<span data-ttu-id="d75e9-565">デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-565">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="d75e9-566">モデルのデバッグ ビューは少し前に実装されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-566">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="d75e9-567">EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-567">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="d75e9-568">暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-568">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="d75e9-569">追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-569">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="d75e9-570">データベースの null セマンティクスの処理の向上</span><span class="sxs-lookup"><span data-stu-id="d75e9-570">Improved handling of database null semantics</span></span>

<span data-ttu-id="d75e9-571">リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。</span><span class="sxs-lookup"><span data-stu-id="d75e9-571">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="d75e9-572">C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-572">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="d75e9-573">EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-573">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="d75e9-574">EF Core 5.0 では、この変換の効率が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-574">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="d75e9-575">ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-575">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="d75e9-576">インデクサーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="d75e9-576">Indexer properties</span></span>

<span data-ttu-id="d75e9-577">EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-577">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="d75e9-578">これらのプロパティにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-578">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="d75e9-579">ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-579">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="d75e9-580">列挙型マッピングの CHECK 制約の生成</span><span class="sxs-lookup"><span data-stu-id="d75e9-580">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="d75e9-581">EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-581">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="d75e9-582">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-582">For example:</span></span>

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="d75e9-583">ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-583">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="d75e9-584">IsRelational</span><span class="sxs-lookup"><span data-stu-id="d75e9-584">IsRelational</span></span>

<span data-ttu-id="d75e9-585">既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-585">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="d75e9-586">このメソッドは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-586">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="d75e9-587">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-587">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="d75e9-588">ドキュメントは、イシュー [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-588">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="d75e9-589">ETag を使用した Cosmos オプティミスティック同時実行制御</span><span class="sxs-lookup"><span data-stu-id="d75e9-589">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="d75e9-590">Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-590">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="d75e9-591">OnModelCreating のモデル ビルダーを使用して ETag を構成します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-591">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="d75e9-592">SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](xref:core/saving/concurrency)、たとえば再試行を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-592">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](xref:core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="d75e9-593">ドキュメントは、イシュー [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-593">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="d75e9-594">より多くの DateTime コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d75e9-594">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="d75e9-595">新しい DateTime のコンストラクトが含まれるクエリを変換できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-595">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="d75e9-596">さらに、次の SQL Server 関数がマップされました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-596">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="d75e9-597">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="d75e9-597">DateDiffWeek</span></span>
* <span data-ttu-id="d75e9-598">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="d75e9-598">DateFromParts</span></span>

<span data-ttu-id="d75e9-599">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-599">For example:</span></span>

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="d75e9-600">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-600">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="d75e9-601">より多くのバイト配列コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d75e9-601">Query translations for more byte array constructs</span></span>

<span data-ttu-id="d75e9-602">byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-602">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="d75e9-603">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d75e9-603">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="d75e9-604">追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-604">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="d75e9-605">Reverse のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d75e9-605">Query translation for Reverse</span></span>

<span data-ttu-id="d75e9-606">`Reverse` を使用したクエリが変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-606">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="d75e9-607">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d75e9-607">For example:</span></span>

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="d75e9-608">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-608">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="d75e9-609">ビット処理演算子のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d75e9-609">Query translation for bitwise operators</span></span>

<span data-ttu-id="d75e9-610">ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-610">Queries using bitwise operators are now translated in more cases For example:</span></span>

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="d75e9-611">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-611">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="d75e9-612">Cosmos の文字列のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="d75e9-612">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="d75e9-613">文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d75e9-613">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="d75e9-614">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d75e9-614">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
